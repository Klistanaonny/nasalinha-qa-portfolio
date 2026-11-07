# Exploração do sistema (06/11/2026)

| # | O que fazer | Esperado (segundo o documento) | O que eu vi | Resultado |
|---|---|---|---|---|
| 1 | Abrir http://localhost:3000 | A página abre e mostra login/cadastro | o site abriu normalmente | rodou normal|
| 2 | Cadastrar usuário novo: e-mail teste1@example.com, qualquer nome e senha | RF-01: cadastro feito, conta nasce com status PENDENTE, sistema envia código de verificação ao e-mail |cadastrou com sucesso e após o cadastro o login foi realizado com sucesso, e código enviado com sucesso pelo mailtrap | rodou corretamente |
| 3 | Abrir o Mailtrap (mailtrap.io, sua inbox) | RF-01: chegou e-mail com um código de verificação | chegou corretamente | rodou corretamente|
| 4 | Tentar entrar com teste1@example.com sem verificar | RF-02: login bloqueado com a mensagem "Por favor, verifique sua conta para continuar" |consegui entrar sem verificar pelo e-mail |verificação não ocorreu como devido |
| 5 | Se existir campo ou tela para o código, digitar o código do e-mail e tentar entrar de novo | RF-01/RF-02: conta ativada e login liberado | não há campo para código |
| 6 | Entrar como Admin e anotar os itens do menu e das telas | RF-03: Admin acessa Temporadas e Moderação |como admin notei mais possibilidades, como excluir usuário, desativar usuário, mais opções em gerenciamento de temporadas, posso excluir check-ins | posso, como admin, ter mais acesso à plataforma|
| 7 | Sair e entrar como Pedro (Trainee) e anotar o menu | RF-03: sem acesso a Temporadas e Moderação |entrei como pedro e entrou normalmente |rodou corretamente |
| 8 | Como Pedro, tentar chegar às telas de Temporadas ou Moderação. Se não existirem no menu, anote isso e siga em frente (a API a gente testa depois no Insomnia) | RF-03: acesso negado | não tenho acesso as moderações | rodou normalmente|
| 9 | Abrir o ranking e anotar como está antes de qualquer check-in (nomes e pontos) | Ranking exibido em ordem decrescente de pontos | na conta do lucas, há o eu mesmo, lucas silva com 20 pontos em primeiro lugar, maria santos com 1 pontos em segundo, e pedro costa em ultimo lugar com zero pontos.|abriu rank normalmente |
| 10 | Como Pedro, fazer check-in com a foto A | RF-04: check-in registrado na temporada ativa e a foto aparece | chekin com foto realizada com sucesso| agiu como esperado|
| 11 | Olhar o status desse check-in | RF-05: status PENDENTE | nao está pendente, está aprovado.|acredito que era pra estar aprovado mesmo. |
| 12 | Como Pedro, tentar um segundo check-in logo em seguida | RF-04/RF-09: bloqueado (só 1 por dia, intervalo mínimo de 24h) |fiz chekin e adicionou. |não corresponde ao esperado. |
| 13 | Como Maria, fazer check-in com a MESMA foto A | RF-04: sistema restringe foto repetida |mandei o chekin com a foto A e o sistema aceita |não ocorre como o esperado |
| 14 | Como Maria, fazer check-in com a foto B | RF-04: check-in aceito |mandei o chekin com a foto B e o sistema aceitou novamente. |ocorreu como esperado |
| 15 | Como Maria, tentar ver, editar ou apagar o check-in do Pedro (se a tela permitir) | RF-06: negado, só vê e mexe nos próprios | não consigo modificar|ocorreu como esperado |
| 16 | Como Pedro, tentar mudar o status do próprio check-in (se existir a opção) | RF-05: negado, só Admin altera | não consegui alterar, não houve opções|ocorreu como esperado |
| 17 | Como Admin, mudar o status do check-in do Pedro para Aprovado | RF-05: status muda para Aprovado | não consegui mudar o status, e todos já estão aprovados|não ocorreu como esperado |
| 18 | Abrir o ranking de novo e ver os pontos do Pedro (anote se os pontos entraram ao fazer o check-in ou só ao aprovar; o documento não diz) | RF-07: pontos somados e ranking atualizado | sim os pontos sobem de acordo com os chekins realizados|ocorreu como esperado |
| 19 | Apagar o check-in do Pedro (se houver botão, talvez só como Admin) e abrir o ranking | RF-08: pontos subtraídos do saldo |apaguei não só do pedro mas também da maria e do lucar e a pontuação não subtraiu do ranking |não ocorreu como esperado |
| 20 | Conferir se o ranking está em ordem decrescente de pontos | RF-09: ordem decrescente | sim está|ocorreu como esperado |
| 21 | EXTRA (opcional): tentar enviar no check-in um arquivo que não é foto (PDF ou .txt) | Não consta nos requisitos, só anote o que acontecer |ao tentar subir arquivo como pdf, não é possível pois não aparecem outra opções que não sejam fotos nas pastas do computador. |ocorreu como esperado |
