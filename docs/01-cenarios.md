# Cenários de teste (planejamento)

Planejamento feito antes da execução, a partir do documento de requisitos (11
requisitos nas 3 áreas principais). Cada requisito tem 2 casos: um de interface
e um de API (no RNF-02, de interface e de servidor). Positivo = fluxo normal.
Negativo = dado errado ou ação proibida.

Os passos, os resultados e os bugs de cada caso estão em
`03-casos-de-teste.md`. A coluna "Executado em" indica onde o caso foi rodado:
`00` é a exploração pela interface e `02` é a execução da API.

## Autenticação (JWT)

| ID | Req. | Onde | Tipo | Cenário | Resultado esperado | Executado em |
|---|---|---|---|---|---|---|
| CT-AUT-01 | RF-01 | Interface | Positivo | Cadastrar com nome, e-mail e senha válidos | Conta criada e e-mail com código chega ao Mailtrap | 00, linhas 2 a 5 |
| CT-AUT-02 | RF-01 | API | Positivo | POST de cadastro com dados válidos | Status 201 e conta com status PENDENTE | 02, linha 4 |
| CT-AUT-03 | RF-02 | Interface | Negativo | Entrar sem verificar o e-mail | Login bloqueado com "Por favor, verifique sua conta para continuar" | 00, linha 4 |
| CT-AUT-04 | RF-02 | API | Negativo | POST de login com conta não verificada | Status 401 ou 403, mensagem acima e nenhum token | 02, linha 5 |
| CT-AUT-05 | RF-03 | Interface | Negativo | Trainee tenta acessar Temporadas e Moderação | Acesso negado | 00, linhas 7 e 8 |
| CT-AUT-06 | RF-03 | API | Negativo | Trainee chama /api/seasons com o próprio token | Status 403 | 02, linha 15 |
| CT-AUT-07 | RNF-01 | Interface | Positivo | Entrar com a senha cadastrada | Login funciona (o sistema compara a senha com o hash) | 00, linhas 4, 6 e 7 |
| CT-AUT-08 | RNF-01 | API | Negativo | Ver as respostas da API e o banco | Senha nunca vem em texto, e no banco está como hash | Não executado |

## Check-in por Foto

| ID | Req. | Onde | Tipo | Cenário | Resultado esperado | Executado em |
|---|---|---|---|---|---|---|
| CT-CHK-01 | RF-04 | Interface | Negativo | Enviar a mesma foto duas vezes | Segunda foto rejeitada | 00, linha 13 |
| CT-CHK-02 | RF-04 | API | Positivo | POST de check-in com foto válida | Status 201, ligado à temporada ativa | Não executado |
| CT-CHK-03 | RF-05 | Interface | Positivo | Fazer um check-in novo | Status inicial PENDENTE | 00, linha 11 |
| CT-CHK-04 | RF-05 | API | Negativo | Usuário comum tenta mudar o status de um check-in | Status 403, só Admin altera | 00, linha 16 (interface). API não executada |
| CT-CHK-05 | RF-06 | Interface | Negativo | Usuário tenta ver ou mexer no check-in de outro usuário | Acesso negado | 00, linha 15 |
| CT-CHK-06 | RF-06 | API | Negativo | Pedro lê e edita check-in da Maria pelo ID | Status 403 ou 404 | 02, linhas 8 a 10 |
| CT-CHK-07 | RNF-02 | Interface | Positivo | Enviar foto válida | Foto processada e exibida | 00, linha 10 |
| CT-CHK-08 | RNF-02 | Servidor | Positivo | Olhar a pasta de uploads do servidor depois do envio | Nenhum arquivo temporário sobrando | Não executado |

## Sistema de Pontos

| ID | Req. | Onde | Tipo | Cenário | Resultado esperado | Executado em |
|---|---|---|---|---|---|---|
| CT-PTS-01 | RF-07 | Interface | Positivo | Fazer check-in e ver o ranking | Pontos somados e ranking atualizado | 00, linha 18 |
| CT-PTS-02 | RF-07 | API | Positivo | GET /api/rankings depois do check-in | Saldo atualizado | 02, linha 11 |
| CT-PTS-03 | RF-08 | Interface | Positivo | Apagar um check-in e ver o ranking | Pontos subtraídos do saldo | 00, linha 19 |
| CT-PTS-04 | RF-08 | API | Positivo | DELETE de check-in e depois GET /api/rankings | Saldo reduzido | 02, linhas 12 a 14 |
| CT-PTS-05 | RF-09 | Interface | Positivo | Olhar a ordem do ranking | Ordem decrescente de pontos | 00, linha 20 (e 02, linha 11 pela API) |
| CT-PTS-06 | RF-09 | Interface e API | Negativo | Dois check-ins do mesmo usuário em menos de 24 horas | Segundo recusado | 00, linha 12 (interface). API não executada (02, linha 17) |

## Cobertura dos requisitos

| Requisito | Descrição | Casos |
|---|---|---|
| RF-01 | Cadastro e verificação | CT-AUT-01, CT-AUT-02 |
| RF-02 | Restrição de login | CT-AUT-03, CT-AUT-04 |
| RF-03 | Controle de níveis (Admin e Trainee) | CT-AUT-05, CT-AUT-06 |
| RNF-01 | Senha com hash | CT-AUT-07, CT-AUT-08 |
| RF-04 | Registro de check-in com foto | CT-CHK-01, CT-CHK-02 |
| RF-05 | Fluxo de status do check-in | CT-CHK-03, CT-CHK-04 |
| RF-06 | Restrição de propriedade | CT-CHK-05, CT-CHK-06 |
| RNF-02 | Integridade de arquivos | CT-CHK-07, CT-CHK-08 |
| RF-07 | Atribuição de pontos | CT-PTS-01, CT-PTS-02 |
| RF-08 | Consistência de saldo | CT-PTS-03, CT-PTS-04 |
| RF-09 | Regras de ranking | CT-PTS-05, CT-PTS-06 |

## Regressão e casos ad-hoc

- Um cenário de regressão por área, descrito na seção 4 de
  `03-casos-de-teste.md`: CT-AUT-R (BUG-AUTH-01), CT-CHK-R (BUG-CHK-01) e
  CT-PTS-R (BUG-PTS-01).
- Casos ad-hoc (CT-ADHOC-01 a CT-ADHOC-04), criados durante a execução e
  registrados na seção 5 de `03-casos-de-teste.md`.
