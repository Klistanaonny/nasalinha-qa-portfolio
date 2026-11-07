# Cenários de teste

Positivo = fluxo normal. Negativo = dado errado ou ação proibida.

## Autenticação (JWT)

| ID | Req. | Onde | Tipo | Cenário | Resultado esperado | Já testei? |
|---|---|---|---|---|---|---|
| CT-AUT-01 | RF-01 | Interface | Positivo | Cadastrar com nome, e-mail e senha válidos | Conta criada e e-mail com código chega ao Mailtrap | Sim (linhas 2-3) |
| CT-AUT-02 | RF-01 | API | Positivo | POST de cadastro com dados válidos | Status 201 e conta com status PENDENTE | Não |
| CT-AUT-03 | RF-02 | Interface | Negativo | Entrar sem verificar o e-mail | Login bloqueado com "Por favor, verifique sua conta para continuar" | Sim (linha 4) |
| CT-AUT-04 | RF-02 | API | Negativo | POST de login com conta não verificada | Status 401 ou 403, mensagem acima e nenhum token | Não |
| CT-AUT-05 | RF-03 | Interface | Negativo | Trainee tenta acessar Temporadas e Moderação | Acesso negado | Sim (linhas 7-8) |
| CT-AUT-06 | RF-03 | API | Negativo | Trainee chama /api/seasons com o próprio token | Status 403 | Não |
| CT-AUT-07 | RNF-01 | Interface | Positivo | Entrar com a senha cadastrada | Login funciona (o sistema compara a senha com o hash) | Sim (linhas 2 e 4) |
| CT-AUT-08 | RNF-01 | API | Negativo | Ver as respostas da API e o banco | Senha nunca vem em texto, e no banco está como hash | Não |

## Check-in por Foto

| ID | Req. | Onde | Tipo | Cenário | Resultado esperado | Já testei? |
|---|---|---|---|---|---|---|
| CT-CHK-01 | RF-04 | Interface | Negativo | Enviar a mesma foto duas vezes | Segunda foto rejeitada | Sim (linha 13) |
| CT-CHK-02 | RF-04 | API | Positivo | POST de check-in com foto válida | Status 201, ligado à temporada ativa | Não |
| CT-CHK-03 | RF-05 | Interface | Positivo | Fazer um check-in novo | Status inicial PENDENTE | Sim (linha 11) |
| CT-CHK-04 | RF-05 | API | Negativo | Usuário comum tenta mudar o status de um check-in | Status 403, só Admin altera | Não |
| CT-CHK-05 | RF-06 | Interface | Negativo | Maria tenta ver ou mexer no check-in do Pedro | Acesso negado | Sim (linha 15) |
| CT-CHK-06 | RF-06 | API | Negativo | Maria edita ou apaga o check-in do Pedro pelo ID | Status 403 ou 404 | Não |
| CT-CHK-07 | RNF-02 | Interface | Positivo | Enviar foto válida | Foto processada e exibida | Sim (linha 10) |
| CT-CHK-08 | RNF-02 | Servidor | Positivo | Olhar a pasta de uploads do servidor depois do envio | Nenhum arquivo temporário sobrando | Não |

## Sistema de Pontos

| ID | Req. | Onde | Tipo | Cenário | Resultado esperado | Já testei? |
|---|---|---|---|---|---|---|
| CT-PTS-01 | RF-07 | Interface | Positivo | Fazer check-in e ver o ranking | Pontos somados e ranking atualizado | Sim (linha 18) |
| CT-PTS-02 | RF-07 | API | Positivo | GET /api/rankings depois do check-in | Saldo atualizado | Não |
| CT-PTS-03 | RF-08 | Interface | Positivo | Apagar um check-in e ver o ranking | Pontos subtraídos do saldo | Sim (linha 19) |
| CT-PTS-04 | RF-08 | API | Positivo | DELETE de check-in e depois GET /api/rankings | Saldo reduzido | Não |
| CT-PTS-05 | RF-09 | Interface | Positivo | Olhar a ordem do ranking | Ordem decrescente de pontos | Sim (linha 20) |
| CT-PTS-06 | RF-09 | API | Negativo | Dois check-ins do mesmo usuário em menos de 24h | Segundo recusado | Visto na tela (linha 12), falta pela API |

## A fazer depois

- 1 caso de regressão por área: CT-AUT-R, CT-CHK-R e CT-PTS-R
- Casos ad-hoc (CT-ADHOC-xx) para bugs que aparecerem fora do planejado
