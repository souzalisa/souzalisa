#Comstart

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


 ## CRUD USUÁRIOS

Foi implementado o CRUD, ou seja, o cadastro, exclusão, edição e consulta de usuários (clientes/artistas). Além disso, realiza a conexão com o Banco de Dados para que o CRUD funcione corretamente com os dados dos usuários. 

*“api/usuarios.php”*

*Busca de usuários*
*Cadastro de usuários*
*Atualizar usuários*
*Suspensão de usuários*
*Exclusão de usuários*

## DASHBOARD

*“private/dashboard”*

Painel exibido para cada tipo de usuário:


Clientes: 

    <?php if ($tipoUsuario === 'contratador'): ?>
        <div class="card">
            <div class="card-label">Pedidos em aberto</div>
            <div class="card-valor"><?= (int) $valores['pedidos'] ?></div>
        </div>
        <div class="card">
            <div class="card-label">Orçamentos pendentes</div>
            <div class="card-valor"><?= (int) $valores['pendentes'] ?></div>
        </div>
    <?php endif; ?>


Artistas:

    <?php if ($tipoUsuario === 'artista'): ?>
        <div class="card">
            <div class="card-label">Comissões em andamento</div>
            <div class="card-valor"><?= (int) $valores['andamento'] ?></div>
        </div>
        <div class="card">
            <div class="card-label">Vagas disponíveis</div>
            <div class="card-valor"><?= (int) $valores['vagas'] ?></div>
        </div>
    <?php endif; ?>


Administrador: 

    <?php if ($tipoUsuario === 'admin'): ?>
        <div class="card">
            <div class="card-label">Usuários cadastrados</div>
            <div class="card-valor"><?= (int) $valores['usuarios'] ?></div>
        </div>
        <div class="card">
            <div class="card-label">Denúncias abertas</div>
            <div class="card-valor">0</div>
        </div>
    <?php endif; ?>

## FEATURE-LOGIN

*“auth/login.php”*


Limite para tentativas de login e tempo bloqueado:

    define('MAX_TENTATIVAS_LOGIN', 5);
    define('MINUTOS_BLOQUEIO_TEMPORARIO', 15);


Campos de e-mail e senha:

    $email = trim($input['email'] ?? '');
    $senha = trim($input['senha'] ?? '');


Caso haja erros ao preencher os campos:

      if (!$usuario) {
        		http_response_code(401);
        		echo json_encode(['erro' => 'E-mail ou senha incorretos.']);
       		 exit;
    	}


Se a conta não estiver no status “ativo”: 

    if ($usuario['status'] === 'inativo') {
        		http_response_code(403);
        		echo json_encode(['erro' => 'Esta conta está inativa. Fale com o suporte para reativá-la.']);
        		exit;
   	 }

    if ($usuario['status'] === 'suspenso') {
       		 http_response_code(403);
        		echo json_encode(['erro' => 'Conta suspensa. Fale com o suporte.']);
        		exit;
  	  }


Autenticação bem-sucedida: 

    session_regenerate_id(true);
      $_SESSION['id_usuario']   = $usuario['id'];
      $_SESSION['nome']         = $usuario['nome'];
      $_SESSION['email']        = $usuario['email'];
      $_SESSION['tipo_usuario'] = $usuario['tipo_usuario'];

    echo json_encode(['sucesso' => true, 'mensagem' => 'Autenticado com sucesso.']);


## REDEFINIÇÃO DE SENHA

*“pages/redefinir-senha.php”*


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

## Front-End

#### “public/catalogo.html”


Estrutura da página do catálogo de artistas contendo quadro de encomendas (com link para o feed) e filtros avançados (ordenar por mais recentes, menor preço e maior preço).


## Back-End

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


# Acesso e bloqueio de conta

## Comstart-sprint3 “auth/login.php”


Foi acrescentado o acesso e bloqueio de conta do usuário. Se o e-mail ou a senha estiverem incorretos, aparecerá uma mensagem de erro e o usuário poderá tentar logar novamente apenas 5 vezes, caso ultrapasse disso, sua conta ficará bloqueada por 15 minutos.


**Tentativas de Login**


    define('MAX_TENTATIVAS_LOGIN', 5);
    define('MINUTOS_BLOQUEIO_TEMPORARIO', 15);


**Validação de E-mail e Senha**


Não aceita campos de e-mail e senha vazios.


	$input = json_decode(file_get_contents('php://input'), true) ?? [];
    $email = trim($input['email'] ?? '');
    $senha = trim($input['senha'] ?? '');

    if ($email === '' || $senha === '') {
    		http_response_code(400);
   		 echo json_encode(['erro' => 'Preencha todos os campos.']);
    		exit;
    }


**Busca de usuários no Banco de Dados**


    $pdo = getConnection();
   	$stmt = $pdo->prepare(
       		 'SELECT id, nome, email, senha_hash, tipo_usuario, status, tentativas_login, bloqueado_ate
         		FROM usuarios WHERE email = ? LIMIT 1'
    	);
    	$stmt->execute([$email]);
    	$usuario = $stmt->fetch();

**Usuário não encontrado**


    if (!$usuario) {
        		http_response_code(401);
        		echo json_encode(['erro' => 'E-mail ou senha incorretos.']);
        		exit;
    	}


**Inatividade**

Não será possível logar se a conta estiver inativa ou suspensa


    if ($usuario['status'] === 'inativo') {
        		http_response_code(403);
        		echo json_encode(['erro' => 'Esta conta está inativa. Fale com o suporte para reativá-la.']);
        		exit;
   	 }

    	if ($usuario['status'] === 'suspenso') {
        		http_response_code(403);
        		echo json_encode(['erro' => 'Conta suspensa. Fale com o suporte.']);
        		exit;
    	}


**Bloqueio**

Exibe uma mensagem afirmando que a conta está bloqueada por excesso de tentativas.

    if ($usuario['bloqueado_ate'] !== null && strtotime($usuario['bloqueado_ate']) > time()) {
        		$minutosRestantes = (int) ceil((strtotime($usuario['bloqueado_ate']) - time()) / 60);
        		http_response_code(423);
        		echo json_encode([
            		'erro' => "Conta bloqueada temporariamente por excesso de tentativas. Tente novamente em {$minutosRestantes} minuto(s)."
       		 ]);
        		exit;
    	}




**Senha correta**

O usuário consegue logar se colocar a senha certa

    $stmtReset = $pdo->prepare(
        		'UPDATE usuarios SET tentativas_login = 0, bloqueado_ate = NULL WHERE id = :id'
   	 );
   	 $stmtReset->execute(['id' => $usuario['id']]);

    	session_regenerate_id(true);
    	$_SESSION['id_usuario']   = $usuario['id'];
    	$_SESSION['nome']         = $usuario['nome'];
    	$_SESSION['email']        = $usuario['email'];
    	$_SESSION['tipo_usuario'] = $usuario['tipo_usuario'];

    	echo json_encode(['sucesso' => true, 'mensagem' => 'Autenticado com sucesso.']);



# Integridade do Orçamento/Encomenda


## “api/orcamentos.php”

**Restrição de dois orçamentos idênticos**

Verifica se há um orçamento duplicado antes de solicitar um novo

    $sqlDuplicado = 'SELECT id FROM orcamentos
                     WHERE contratador_id = :contratador_id
                       AND artista_id = :artista_id
                       AND status_orcamento = "pendente"
                       AND (
                           LOWER(TRIM(escopo)) = LOWER(TRIM(:escopo))
                           OR (:encomenda_id IS NOT NULL AND encomenda_id = :encomenda_id_check)
                       )
                     LIMIT 1';
    $stmtDuplicado = $pdo->prepare($sqlDuplicado);
    $stmtDuplicado->execute([
        'contratador_id'     => $usuarioLogado['id_usuario'],
        'artista_id'         => $artistaId,
        'escopo'             => $escopo,
        'encomenda_id'       => $encomendaId,
        'encomenda_id_check' => $encomendaId,
    ]);


## “api/encomendas.php”

Verifica se uma encomenda já possui um orçamento aceito antes mesmo de editar, caso já tenha um, a mensagem irá mostrar que não foi possível concluir a ação.

$stmtOrc = $pdo->prepare(
        'SELECT id FROM orcamentos WHERE encomenda_id = :encomenda_id AND status_orcamento = "aceito" LIMIT 1'
);
$stmtOrc->execute(['encomenda_id' => $id]);
if ($stmtOrc->fetch()) {
        respostaJson([
       	 'sucesso' => false,
       	 'mensagem' => 'Não é possível alterar ou cancelar esta encomenda, pois ela já possui um orçamento aceito vinculado.'
        ], 409);
}


# Permissões Cruzadas

## “api/categorias.php”

Para excluir uma categoria, somente o administrador tem a permissão:

    function tratarDelete(PDO $pdo): void
      {
    		exigirTipo('admin');

   	 	if (!isset($_GET['id'])) {
        			respostaJson(['sucesso' => false, 'mensagem' => 'Informe o id da categoria na URL.'], 400);
    		}

   		 $id = (int) $_GET['id'];


Caso haja algum vínculo de portfólio ou encomendas na categoria, a exclusão é bloqueada retornando uma mensagem:

      if ($totalObras > 0 || $totalEncomendas > 0) {
        		respostaJson([
            		'sucesso' => false,
           		'mensagem' => 'Não é possível excluir esta categoria: existem '
            		    	. $totalObras . ' obra(s) de portfólio e ' . $totalEncomendas
                			. ' encomenda(s) vinculadas a ela.',
        		], 409);
    	}

Se não houver vínculo, a exclusão é realizada com sucesso:

      $stmt = $pdo->prepare('DELETE FROM categorias WHERE id = :id');
    	$stmt->execute(['id' => $id]);

    	respostaJson(['sucesso' => true, 'mensagem' => 'Categoria excluída com sucesso.']);
    }


## “api/portfolio.php”

Verifica se a obra realmente existe:


    function buscarItemDoArtista(PDO $pdo, int $id, int $artistaId): array
      {
   		 $stmt = $pdo->prepare('SELECT * FROM portfolio_itens WHERE id = :id');
 		   $stmt->execute(['id' => $id]);
   		 $item = $stmt->fetch();

    		if (!$item) {
        		respostaJson(['sucesso' => false, 'mensagem' => 'Obra não encontrada.'], 404);
   		 }



Logo em seguida, caso a obra exista e o artista não for o mesmo usuário autenticado, impedirá que altere a arte de outro:



        if ((int) $item['artista'] !== $artistaId) {
        			respostaJson(['sucesso' => false, 'mensagem' => 'Essa obra não pertence a você.'], 403);
    		}

   		 return $item;
      }



## “api/redes_sociais.php”

Caso a rede social não exista ou se não for do mesmo usuário, também retornará a mensagem:

      if (!$rede) {
        		respostaJson(['sucesso' => false, 'mensagem' => 'Rede social não encontrada.'], 404);
    	}

   	 if ((int) $rede['usuario_id'] !== $usuarioId) {
       		 respostaJson(['sucesso' => false, 'mensagem' => 'Essa rede social não pertence a você.'], 403);
    	}



## “api/mensagens.php”

Não permite que o usuário mande mensagem para ele mesmo e só será possível enviar a mensagem se conter um texto ou um anexo:

    if ($destinatarioId === $usuarioId) {
        		respostaJson(['sucesso' => false, 'mensagem' => 'Não é possível enviar mensagem para si mesmo.'], 422);
   	 }

    	if ($conteudo === '' && $anexo === '') {
        		respostaJson(['sucesso' => false, 'mensagem' => 'A mensagem precisa ter um texto ou um anexo.'], 422);
   	 }



# Fiscal de Vagas do Artista

## “api/orcamentos.php”


Regra: artista define um número máximo de comissões simultâneas; o sistema bloqueia aceitar um novo orçamento se ele já estiver no limite (nem deixa nem o botão aparecer no front, nem aceita a requisição no back) Onde entra: api/orcamentos.php (ação "responder"), tabela usuarios ou uma nova coluna/tabela de capacidade 



