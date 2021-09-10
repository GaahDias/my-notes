# Agents :man_in_tuxedo:

## What is an Agent?

Como Refs e Atoms, Agents nos dão acesso compartilhado a um estado mutável. Onde Refs trabalham de forma coordenada, e síncrona, Agents nos dão a possibilidade de trabalhar de forma independente, e assíncrona. 

Agentes são ligados a uma única localização de armazenamento durante seu ciclo de vida, e apenas permitem uma mutação dessa localização (para um novo state) como resultado de uma ação. Ações são funções que são aplicadas de forma assíncrona para um state de um Agent, sendo que o valor retornado pela função se torna o novo estado do Agent.



## Functions

As principais funções que usamos ao trabalhar com Agents são:

* **send**: Envia uma ação para um Agent. `(send a f & args)`. Subsequentemente, em uma pool de threads, o estado do Agente será definido com: `(apply action-f state-of-agent args)`.

* **send-off**: Parecido com o `send`, porém envia uma ação potencialmente bloqueadora para um Agent. `(send-off a f & args)`.

* **restart-agent**: Quando acontece uma falha com um Agent, essa função muda o estado para um novo, e então retira a falha do cache do Agent, para permitir sends novamente. `(restart-agent a new-state & options)`.

  Se a opção `:clear-actions` for declarada como true, qualquer ação que estava na fila do Agent, por conta da falha, será descartada. Caso contrário, essas ações continuaram após o restart.

  Se houver algum Watcher vinculado com o Agent, o mesmo **não** será notificado do novo estado atribuido pela função `restart-agent`.
  
* **shutdown-agents**: Inicia o desligamento da pool de threads que estava gerenciando o sistema de Agents. Ações rodando serão completadas, porém novas ações não serão aceitas. `(shutdown-agents)`.

* **set-validator!**: Adiciona um validador para o estado do Agente. Antes de um novo estado ser atribuído ao Agent, o mesmo passará pelo validador. `(set-validator reference validator-fn)`.

* **add-watch**: Adiciona um watcher para o Agente. Caso o estado do Agent mude, o watcher será acionado. `(add-watch reference key fn)`.

* **agent-error**: Retorna a exceção recebida durante uma ação assíncrona. Retorna `nil` caso não haja nenhuma exceção. `(agent-error a)`.

* **set-error-handler!**: Define uma função que será chamada caso alguma exceção seja levantada no Agent. A `handler-fn` será chamada com dois argumentos: o Agent, e a exceção. `(set-error-handler! a handler-fn)`.

* **set-error-mode!**: Define o error-mode de um Agent. Podemos definir esse modo como `:fail`, ou `:continue`. Caso uma ação do Agent falhe e levante uma exceção, ou não passe da função validadora, um error-handler pode ser chamado.

  Após isso, caso o error-mode esteja definido como `:continue`, o Agent continuará como se a ação que causou o erro, ou o próprio erro não tivesse acontecido.

  Caso esteja como `fail`, o Agent se tornará falho, e não estará mais aberto a funções como `send` ou `send-off`. Qualquer ação que estiver na fila do Agente será mantida até que o Agent seja reiniciado com `restart-agent`. Deref também continuará funcionando, e retornará o antigo estado (anterior ao erro) do Agente.



## Actions

Os despachos de ação dos Agents tomam a forma `(send agent fn args)`. `send` e `send-off` sempre retornam imediatamente. Em algum momento, em outra thread, o seguinte acontecerá:

1. A função `fn` fornecida será aplicada para o estado do Agente com os `args` (isso é, se passado algum argumento).
2. O valor retornado pela função será passado por um validador ( `set-validator!`), se algum tiver sido definido.
3. Se a validação for bem sucedida, ou se não existir alguma validação, o valor retornado da função passará a ser o novo estado do Agent.
4. Se algum watcher (`add-watch`) tiver sido definido, o mesmo será chamado.
5. Se durante a execução da função algum outro despacho for feito, o mesmo esperará até o estado do Agent estar completamente atualizado.

Se algum exceção for lançada por uma função de ação, nenhum despacho aninhado ocorrerá, e a exceção será armazenada no cache do próprio Agent. Se algum Agent possui erros no seu cache, qualquer interação subsequente com o mesmo irá resultar em uma exceção. Erros de Agente podem ser examinados com `agent-error` e o reinicio do Agente com `restart-agent`.

As ações de todos os Agentes são intercaladas entre threads, em uma pool de threads. A algum momento, no máximo uma ação para cada Agent está sendo executada. Das funções para alterar o estado de um Agent: `send` deve ser utilizado para ações que são limitadas pela CPU, enquanto `send-off` é mais apropriado para ações que podem bloquear no I/O.

Agents são integrados com STM - qualquer despacho feito em uma transação é retido até que commite, e é descartado caso se for repetido, ou cancelado.

Note também que o uso de Agents começa uma pool no background de não-daemons threads que vão impedir o desligamento da JVM. Use `shutdown-agents` para fechar essas threads e permitir o desligamento. 



## Example

Este exemplo é uma implementação do teste send-a-message-around-a-ring. Uma cadeia de m Agentes é criada e, em seguida, uma sequência de n ações é despachada para o topo da cadeia e retransmitida através dela.

```clojure
(defn relay [x i]
    (when (:next x)
        (send (:next x) relay i))
    (when (and (zero? i) (:report-queue x))
        (.put (:report-queue x) i))
    x)

(defn run [m n]
    (let [q (new java.util.concurrent.SynchronousQueue)
          hd (reduce (fn [next _] (agent {:next next}))
                     (agent {:report-queue q}) (range (dec m)))]
        (doseq [i (reverse (range n))]
            (send hd relay i))
        (.take q)))

; 1 million message sends:
(time (run 1000 1000))
; "Elapsed time: 2959.254 msecs"
```

