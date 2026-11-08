# Exploração do sistema (06 e 07/11/2026)

Primeira navegação pelo NaSalinha (http://localhost:3000), feita para entender o
fluxo do usuário antes de escrever os casos de teste. Cada passo foi comparado
com o documento de requisitos. Ambiente: localhost, Docker Compose.

Usuários usados: Admin, Lucas, Maria e Pedro (usuários do seed), a conta do
testador (naonny) e uma conta nova criada no teste (teste1@example.com).

Resultado: "Conforme" quando o sistema se comportou como o requisito descreve e
"Diferente" quando não. A coluna Caso / Bug liga cada passo a
`03-casos-de-teste.md` e à pasta `bugs/`.

| # | O que fazer | Esperado (requisitos) | O que observei | Resultado | Caso / Bug |
|---|---|---|---|---|---|
| 1 | Abrir http://localhost:3000 | A página abre e mostra login e cadastro | O site abriu normalmente | Conforme | Linha de base |
| 2 | Cadastrar um usuário novo (teste1@example.com, com nome e senha) | RF-01: cadastro feito, conta PENDENTE e código de verificação enviado ao e-mail | Cadastro concluído e login feito automaticamente em seguida | Diferente | CT-AUT-01, BUG-AUTH-01 |
| 3 | Abrir a caixa de entrada do Mailtrap | RF-01: e-mail com um código de verificação | Chegou um e-mail de boas-vindas, com o botão "Fazer Login" e sem nenhum código | Diferente | CT-AUT-01, BUG-AUTH-02 |
| 4 | Tentar entrar com teste1@example.com sem verificar a conta | RF-02: login bloqueado com a mensagem "Por favor, verifique sua conta para continuar" | O login foi liberado, sem verificação | Diferente | CT-AUT-03, BUG-AUTH-01 |
| 5 | Procurar um campo ou tela para digitar o código de verificação | RF-01 e RF-02: conta ativada pelo código e login liberado | Não existe campo para digitar código | Diferente | CT-AUT-01, BUG-AUTH-02 |
| 6 | Entrar como Admin e anotar os itens do menu e das telas | RF-03: Admin acessa Temporadas e Moderação | Há opções extras: excluir e desativar usuários, gerenciar temporadas e excluir check-ins | Conforme | CT-AUT-05 |
| 7 | Sair e entrar como Pedro (Trainee) | RF-03: Trainee sem acesso a Temporadas e Moderação | Entrou normalmente como Pedro. O acesso às áreas de gestão está na linha 8 | Conforme | CT-AUT-05 |
| 8 | Como Pedro, tentar chegar às telas de Temporadas e Moderação | RF-03: acesso negado | Sem acesso à moderação | Conforme | CT-AUT-05 |
| 9 | Abrir o ranking antes de qualquer check-in e anotar nomes e pontos | RF-09: ranking em ordem decrescente de pontos | Com a conta do Lucas: Lucas Silva (20 pontos) em primeiro, Maria Santos (1 ponto) em segundo e Pedro Costa (0 pontos) em último | Conforme | Linha de base |
| 10 | Como Pedro, fazer check-in com a foto A | RF-04: check-in registrado na temporada ativa, com a foto | Check-in com foto realizado com sucesso | Conforme | CT-CHK-07 |
| 11 | Olhar o status desse check-in | RF-05: status PENDENTE | O check-in aparece como Aprovado | Diferente | CT-CHK-03, BUG-CHK-01 |
| 12 | Como Pedro, fazer um segundo check-in logo em seguida | RF-04 e RF-09: bloqueado (um por dia, intervalo mínimo de 24 horas) | O segundo check-in foi aceito. Reproduzido depois na conta naonny: dois check-ins com um minuto de diferença, ambos aprovados (print CHK-02) | Diferente | CT-PTS-06, BUG-PTS-02 |
| 13 | Como Maria, fazer check-in com a mesma foto A | RF-04: foto repetida recusada | O sistema aceitou a foto repetida. O mesmo ocorreu na conta naonny (print CHK-02) | Diferente | CT-CHK-01, BUG-CHK-03 |
| 14 | Como Maria, fazer check-in com a foto B | RF-04: check-in aceito | Check-in aceito | Conforme | Controle do passo 13 |
| 15 | Tentar ver, editar ou apagar o check-in de outro usuário | RF-06: negado, cada usuário vê e altera só os próprios | Não há opção para editar nem apagar, mas a tela inicial lista check-ins de outros usuários em "Atividade Recente" (visto na conta naonny, print CHK-02) | Diferente | CT-CHK-05, BUG-CHK-02 |
| 16 | Como Pedro, tentar mudar o status do próprio check-in | RF-05: negado, só o Admin altera | Não há opção para alterar o status | Conforme | CT-CHK-04 (parte da interface) |
| 17 | Como Admin, mudar o status do check-in do Pedro para Aprovado | RF-05: o status muda para Aprovado | Não foi possível mudar o status, porque todos os check-ins já estão aprovados | Diferente | CT-CHK-03, BUG-CHK-01 |
| 18 | Abrir o ranking de novo e ver os pontos do Pedro | RF-07: pontos somados e ranking atualizado | Os pontos sobem a cada check-in realizado. Eles entram na criação do check-in, que já nasce aprovado | Conforme | CT-PTS-01 |
| 19 | Como Admin, apagar check-ins (do Pedro, da Maria e do Lucas) e abrir o ranking | RF-08: pontos subtraídos do saldo | Os check-ins foram apagados, mas a pontuação do ranking não diminuiu | Diferente | CT-PTS-03, BUG-PTS-01 |
| 20 | Conferir a ordem do ranking | RF-09: ordem decrescente de pontos | Ordem decrescente | Conforme | CT-PTS-05 |
| 21 | Extra: tentar enviar no check-in um arquivo que não é imagem (PDF ou .txt) | Sem requisito: registrar o comportamento | O seletor de arquivos só mostra imagens, então não é possível escolher um PDF | Conforme (sem requisito) | CT-ADHOC-04 |

## Resumo

Dos 21 passos, 11 foram conformes e 10 diferentes do esperado. As diferenças
deram origem a 7 bug reports:

| Bug | Passos |
|---|---|
| BUG-AUTH-01 | 2 e 4 |
| BUG-AUTH-02 | 3 e 5 |
| BUG-CHK-01 | 11 e 17 |
| BUG-CHK-02 | 15 |
| BUG-CHK-03 | 13 |
| BUG-PTS-01 | 19 |
| BUG-PTS-02 | 12 |

O BUG-DOC-01 surgiu fora da exploração, na execução da API
(`02-execucao-api.md`).

## Observações adicionais

- Os pontos entram no momento do check-in (linha 18), enquanto o README do
  backend diz que o ranking é atualizado quando o check-in é aprovado.
- Os resultados dos testes de API que complementam esta exploração estão em
  `02-execucao-api.md`.
