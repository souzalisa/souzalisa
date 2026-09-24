## comunicacao-e-presenca


Foi realizado dentro dessa pasta, a criação de chat de mensagem e o vínculo da rede social do usuário.



## Chat de mensagens


**comunicacao-e-presenca/api/mensagens.php**

<br>

* **Lista de conversas ordenadas por mais recente;**
  

* **Histórico de conversas;**
  

* **Envio de mensagens;**

<br>

**comunicacao-e-presenca/pages/chat.php**

<br>

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



## Redes sociais



**comunicacao-e-presenca/api/redes_sociais.php**

<br>

* **Lista de redes sociais;**
 
* **Vínculo;**

* **Atualização de link;**

* **Remoção do vínculo;**

* **Validação das redes sociais do usuário;**

<br>

**comunicacao-e-presenca/pages/perfil.php**

<br>

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




