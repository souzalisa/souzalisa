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
\\
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


