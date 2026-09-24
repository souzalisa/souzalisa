# CRUD Categorias e Portfólio

Essa pasta contém a permissão de somente o administrador adicionar uma categoria e a permissão para o artista publicar sua obra no seu portfólio.

## “pages/categorias/index.php”

SOMENTE ADMIN CONSEGUE ACRESCENTAR UMA CATEGORIA


    <div class="cabecalho-pagina">

    		<h1>Categorias</h1>

    		<?php if (($_SESSION['usuario']['tipo'] ?? '') === 'admin'): ?>

       	 		<button class="botao botao--primario" id="btn-nova-categoria">+Nova categoria</button>

    		<?php endif; ?>
    </div>


## “pages/portfolio/index.php”

 ARTISTA ACRESCENTA UMA OBRA NO SEU PERFIL


    <div class="cabecalho-pagina">

    		<h1>Portfólio</h1>

    		<?php if (($_SESSION['usuario']['tipo'] ?? '') === 'artista'): ?>

        			<button class="botao botao--primario" id="btn-nova-obra">+ Adicionar obra</button>

    		<?php endif; ?>
        
    </div>

# Orçamento e status do trabalho

Nessa pasta consta o orçamento entre o artista e o contratador, controlando desde o pedido inicial até a entrega final.


## “pages/dashboard/client/orcamentos.php”

Essa página permite que o cliente envie e verifique seus pedidos de orçamento para os artistas, descrevendo o que deseja.

## “pages/dashboard/creator/orcamentos.php”

Já nessa página, no painel do criador, ele tem acesso aos orçamentos recebidos, seu portfólio e suas comissões, podendo aceitar ou recusar pedidos enviados por contratadores.


# Comunicação e presença

## “api/mensagens.php”

* **Lista de conversas ordenadas por mais recente;**
  

* **Histórico de conversas;**
  

* **Envio de mensagens;**
 
## “pages/chat.php”

Tela Front-End do chat de mensagens, sem lógica de php, apenas testes.



**Exemplo (selecionando uma conversa para enviar uma mensagem):**


            <section class="chat-painel">

                        <div class="chat-painel__cabecalho" id="cabecalho-conversa">

                                    <span class="estado-vazio">Selecione uma conversa ou busque alguém para começar.</span>

                        </div>

                        <div class="chat-mensagens" id="lista-mensagens"></div>

                        <form class="chat-form" id="form-enviar-mensagem">

                                    <input type="text" id="campo-mensagem" placeholder="Escreva uma mensagem..." maxlength="1000" disabled>

                                    <button type="submit" class="botao botao--primario" id="btn-enviar-mensagem" disabled>Enviar</button>

                        </form>

            </section>


## “api/redes_sociais.php”

* **Lista de redes sociais;**
 
* **Vínculo;**

* **Atualização de link;**

* **Remoção do vínculo;**

* **Validação das redes sociais do usuário;**


## “pages/perfil.php”

Tela Front-End do perfil do usuário com formulário adicionando uma rede social.


**Exemplo:**

            <form id="form-rede" class="perfil-form-rede">

                        <select id="rede-plataforma">

                                    <?php foreach ($rotulosPlataforma as $valor => $rotulo): ?>

                                                <option value="<?= $valor ?>"><?= $rotulo ?></option>

                                    <?php endforeach; ?>

                        </select>

                        <input type="url" id="rede-link" placeholder="https://..." required>

                        <button type="submit" class="botao botao--primario">Adicionar</button>

            </form>           


# Quadro encomendas

Esse arquivo realiza o armazenamento de encomendas realizadas no site e o status de disponibilidade no Banco de Dados MySQL.


#### “api/catalogo.php”


Consulta ao Banco de Dados, buscando registro na tabela encomendas utilizando filtros via método GET.


    if (!empty($_GET['categoria'])) {
    
    $categoria = $_GET['categoria'];
    
    $sql = $sql . " AND categoria = '$categoria'";
    
    }


# Estrutura do Banco de Dados

### -- Tabela categorias


Armazena o nome e a descrição de uma categoria publicada por um admin.
    

    
    CREATE TABLE IF NOT EXISTS categorias (

    id INT AUTO_INCREMENT PRIMARY KEY,

    nome VARCHAR(100) NOT NULL,

    descricao TEXT NULL

    ) ENGINE=InnoDB;



### -- Tabela portfólio

Armazena os dados do artista e sua obra publicada em seu perfil


    
    CREATE TABLE IF NOT EXISTS portfolio_itens (

    id INT AUTO_INCREMENT PRIMARY KEY,

    artista INT NOT NULL,

    categoria INT NOT NULL,

    titulo VARCHAR(150) NOT NULL,

    arquivo VARCHAR(255) NOT NULL,


    
    
### -- Tipo de mídia

Guarda o tipo de conteúdo publicado no portfólio do artista



    tipo_midia ENUM('imagem', 'video', 'audio') NOT NULL,

    criado_em DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_portfolio_artista FOREIGN KEY (artista) REFERENCES usuarios(id),

    CONSTRAINT fk_portfolio_categoria FOREIGN KEY (categoria) REFERENCES categorias(id)

    ) ENGINE=InnoDB;

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


### -- Tabela encomendas

Mostra o cliente que pediu, o que ele pediu e o prazo para o criador finalizar e entregar o produto final para o cliente:

    
    
    CREATE TABLE IF NOT EXISTS encomendas (

    id INT AUTO_INCREMENT PRIMARY KEY,

    contratador VARCHAR(150) NOT NULL,

    categoria VARCHAR(100) NOT NULL,

    titulo VARCHAR(150) NOT NULL,

    descricao TEXT NOT NULL,

    prazo DATE NOT NULL,

    
    

### -- Status e Integridade

Status da encomenda que só é permitido nessas condições (aberto, em andamento, concluído e cancelado), valor em dinheiro e estilo que o cliente deseja:

    
    
     status ENUM('aberto', 'em_andamento', 'concluido', 'cancelado') NOT NULL DEFAULT 'aberto',

    preco_medio DECIMAL(10,2) DEFAULT NULL,

    estilo VARCHAR(100) DEFAULT NULL,





