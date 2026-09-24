# Comstart

Comstart é uma plataforma que tem o objetivo de auxiliar os Microempreendedores Individuais a divulgarem seus produtos e serviços, podendo criar anúncios para atrair interesse por outras pessoas e esse projeto pode ser acessado por clientes e artistas.

*Como instalar e rodar*

Ferramentas necessárias:

XAMPP (para php e MySQL);

Git (para clonar o repositório).

Através do botão “code”, copie a URL do repositório (HTTPS), em seguida, abra o terminal de comando (ou git bash) e execute: “git clone” junto com a URL copiada.

Entre no diretório usando o comando “cd” com o nome da pasta.

Crie o banco rodando o arquivo ‘database/schema.sql’ no MySQL via phpMyAdmin;

Caso seu usuário ou senha forem diferentes de ‘root’ mude-as no seguinte arquivo: ‘config/database.php’;

Mova a pasta ‘Comstart’ para a pasta ‘htdocs’ do XAMPP;

Acesse: ‘http://localhost/Comstart’.


## CRUD USUARIOS

Foi implementado o CRUD, ou seja, o cadastro, exclusão, edição e consulta de usuários (clientes/artistas). Além disso, realiza a conexão com o Banco de Dados para que o CRUD funcione corretamente com os dados dos usuários. 

*“api/usuarios.php”*

	Busca de usuários;
	
	Cadastro de usuários;
	
	Atualizar usuários;
	
	Suspensão de usuários;
	
	Exclusão de usuários.

## LOGIN;

### *“auth/login.php”*

Foi acrescentado o acesso e bloqueio de conta do usuário. Se o e-mail ou a senha estiverem incorretos, aparecerá uma mensagem de erro e o usuário poderá tentar logar novamente apenas 5 vezes, caso ultrapasse disso, sua conta ficará bloqueada por 15 minutos.

Limite de 5 tentativas por login e 15 minutos de tempo bloqueado;

Processamento dos dados de e-mail e senha enviados pelo usuário;

Não aceita campos de e-mail e senha vazios.

Impede que o usuário acesse a conta com: 

	* E-mail e senha incorretos;
	
	* Conta inativa;
	
	* Conta suspensa;
	
	* Conta bloqueada, exibe uma mensagem afirmando que a conta está bloqueada por excesso de tentativas.
		
	* O usuário conseguirá logar se preencher o e-mail e senha corretamente.

### *“pages/redefinir-senha.php”*

(???)


## DASHBOARD;

### *“private/dashboard”*

Exibição do painel de acordo com cada tipo de usuário:

	Clientes (Card mostrando os pedidos que estão abertos);
	
	Artistas (Comissões pendentes e vagas disponíveis);
	
	Administrador (Quantidade de usuários cadastrados e denúncias abertas);


## PERMISSÕES;

Foi realizada a permissão de somente o administrador adicionar, remover uma categoria e a permissão para o artista publicar sua obra no seu portfólio.

### *“pages/categorias/index.php”*

Somente o administrador consegue publicar uma nova categoria;

### *“pages/portfolio/index.php”*

Artista acrescenta uma obra no seu portfólio;

### *“api/portfolio.php”*

Verifica se a obra realmente existe;
Logo em seguida, caso a obra exista e o artista não for o mesmo usuário autenticado, impedirá que um artista altere a arte de outro.

### *“api/mensagens.php”*

Não permite que o usuário mande mensagem para ele mesmo e somente será possível enviar a mensagem se conter um texto ou um anexo.

## ORÇAMENTOS;

Orçamento entre o artista e o cliente, controlando desde o pedido inicial até a entrega final.


### *“pages/dashboard/client/orcamentos.php”*

Essa página permite que o cliente envie e verifique seus pedidos de orçamento para os artistas, descrevendo o que deseja.

### *“pages/dashboard/creator/orcamentos.php”*

Já nessa página, no painel do criador, ele tem acesso aos orçamentos recebidos, seu portfólio e suas comissões, podendo aceitar ou recusar pedidos enviados por contratadores.

### *“api/orcamentos.php”*

**Restrição de dois orçamentos idênticos**

Verifica se há um orçamento duplicado antes de solicitar um novo.


## ENCOMENDAS/CATALOGO;

Armazenamento de encomendas realizadas no site e o status de disponibilidade no Banco de Dados MySQL.

### “public/catalogo.html”

Estrutura da página do catálogo de artistas contendo quadro de encomendas (com link para o feed) e filtros avançados (ordenar por mais recentes, menor preço e maior preço).

### “api/catalogo.php”

Consulta ao Banco de Dados, buscando registro no banco de dados utilizando filtros por tags via método GET.

### “api/encomendas.php”

Verifica se uma encomenda já possui um orçamento aceito antes mesmo de editar, caso já tenha um, a mensagem irá mostrar que não foi possível concluir a ação.


## PORTFOLIO/CATEGORIAS;

### “api/portifolio.php”

(???)

### “api/categorias.php”

(???)


## CHAT DE MENSAGENS E REDES SOCIAIS;

### “api/mensagens.php”

* **Lista de conversas ordenadas por mais recente;**
  

* **Histórico de conversas;**
  

* **Envio de mensagens;**
 
### “pages/chat.php”

Tela Front-End do chat de mensagens, permitindo selecionar uma conversa para enviar uma mensagem e procurar pelo nome do usuário.


### “api/redes_sociais.php”

* **Lista de redes sociais;**
 
* **Vínculo;**

* **Atualização de link;**

* **Remoção do vínculo;**

* **Validação das redes sociais do usuário;**


### “pages/perfil.php”

Tela Front-End do perfil com formulário adicionando uma rede social além de recomendações feitas por usuários no perfil de um artista e publicação de uma nova comissão na galeria.


## BANCO DE DADOS;

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
