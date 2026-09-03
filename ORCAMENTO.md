## Orçamento_e_status_do_trabalho

Nessa pasta consta o orçamento entre o artista e o contratador, controlando desde o pedido inicial até a entrega final.

## Estrutura do Banco de Dados


### -- Tabela orçamentos

Gerencia o processo de serviço entre compradores e artistas. Armazenando o id de quem compra e quem produz, texto explicando o que será feito, prazo para entrega e valor sugerido.

    
    
    CREATE TABLE IF NOT EXISTS orcamentos (

    id                INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,

    contratador_id    INT UNSIGNED NOT NULL,

    artista_id        INT UNSIGNED NOT NULL,

    escopo            TEXT NOT NULL,

    prazo_solicitado  DATE NULL,

    valor_proposto    DECIMAL(10,2) NULL,


### -- Status de proposta e serviço

A Situação da proposta só é aceita se estiver em uma dessas condições: pendente, aceito ou recusado. 


Assim como a situação do serviço que deve estar: nao_iniciado, em_andamento, em_revisao ou concluido.


    status_orcamento  ENUM('pendente', 'aceito', 'recusado') NOT NULL DEFAULT 'pendente',

    status_trabalho   ENUM('nao_iniciado', 'em_andamento', 'em_revisao', 'concluido')
                       	    NOT NULL DEFAULT 'nao_iniciado',



### -- Tabela trabalho_status_historico

Armazena o histórico de alterações de status do orçamento, exibindo a data que foi alterada.

    
    CREATE TABLE IF NOT EXISTS trabalho_status_historico (

    id            INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,

    orcamento_id  INT UNSIGNED NOT NULL,

    status        ENUM('nao_iniciado', 'em_andamento', 'em_revisao', 'concluido') NOT NULL,

    alterado_em   DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,



    
    
### -- Clientes e criadores

<br>

**Orçamento_e_status_do_trabalho/pages/dashboard/client/orçamentos.php**

Essa página permite que o cliente envie e verifique seus pedidos de orçamento para os artistas, descrevendo o que deseja.

<br>

**Orçamento_e_status_do_trabalho/pages/dashboard/creator/orçamentos.php**

Já essa página, no painel do criador, ele tem acesso aos orçamentos recebidos, seu portfólio e suas comissões, podendo aceitar ou recusar pedidos enviados por contratadores.
