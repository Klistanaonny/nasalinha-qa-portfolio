# Casos de teste

Onde: Interface (navegador), API (Insomnia) ou Servidor. Tipo: Positivo (fluxo
normal) ou Negativo (dado inválido ou ação proibida). Status: Pass, Fail ou
N/E (não executado).

Interface executada na exploração (`00-exploracao.md`, 06/11/2026). API
executada no Insomnia (`02-execucao-api.md`, 07 e 08/11/2026).

## 1. Autenticação (JWT)

| ID | Req. | Onde e tipo | Cenário | Passos | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|---|---|---|
| CT-AUT-01 | RF-01 | Interface, Positivo | Cadastro de usuário | 1. Abrir o site 2. Criar Conta 3. Preencher nome, e-mail e senha 4. Enviar 5. Abrir a inbox do Mailtrap | Conta PENDENTE e e-mail com código de verificação | Conta criada com login automático. E-mail de boas-vindas, sem código | Fail | BUG-AUTH-02 |
| CT-AUT-02 | RF-01 | API, Positivo | Cadastro pela API | 1. POST /api/auth/register com nome, e-mail e senha novos 2. Ler a resposta | 201, conta PENDENTE, sem acesso liberado | 201 com role MEMBER, accessToken e refreshToken. Sem campo de status da conta | Fail | BUG-AUTH-01 (relacionado) |
| CT-AUT-03 | RF-02 | Interface, Negativo | Login sem verificar o e-mail | 1. Cadastrar usuário novo 2. Sem verificar, tentar entrar | Bloqueio com "Por favor, verifique sua conta para continuar" | Login liberado, sem mensagem | Fail | BUG-AUTH-01 |
| CT-AUT-04 | RF-02 | API, Negativo | Login de conta não verificada | 1. Cadastrar teste3@example.com 2. POST /api/auth/login com esse e-mail e senha | 401 ou 403, mensagem de verificação, sem token | 200 "Login realizado com sucesso" com tokens | Fail | BUG-AUTH-01 |
| CT-AUT-05 | RF-03 | Interface, Negativo | Trainee em áreas de gestão | 1. Entrar como pedro@example.com 2. Procurar Temporadas e Moderação | Sem acesso | Sem acesso à moderação (exploração, linhas 7 e 8) | Pass | |
| CT-AUT-06 | RF-03 | API, Negativo | Trainee criando temporada | 1. Login do Pedro 2. POST /api/seasons com o token e Body {} | 403 | 403 "Sem permissão para acessar este recurso" | Pass | |
| CT-AUT-07 | RNF-01 | Interface, Positivo | Login com a senha cadastrada | 1. Entrar com usuário cadastrado e senha correta | Login funciona (senha comparada com o hash) | Funcionou com Admin, Pedro e usuários novos. O hash em si não foi verificado | Pass | |
| CT-AUT-08 | RNF-01 | API, Negativo | Senha não exposta e guardada com hash | 1. Ler respostas de login e cadastro 2. Conferir a senha no banco | Senha nunca retorna e no banco está com hash | Respostas lidas não trazem senha. Banco não inspecionado | N/E | |

## 2. Check-in por Foto

| ID | Req. | Onde e tipo | Cenário | Passos | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|---|---|---|
| CT-CHK-01 | RF-04 | Interface, Negativo | Mesma foto enviada de novo | 1. Como Pedro, fazer check-in com a foto A 2. Como Maria, fazer check-in com a mesma foto A | Segundo envio recusado | Aceito (exploração, linha 13) | Fail | BUG-CHK-03 |
| CT-CHK-02 | RF-04 | API, Positivo | Check-in com foto válida | 1. POST /api/checkins com a foto (Multipart) | 201 e check-in ligado à temporada ativa | Não executado | N/E | |
| CT-CHK-03 | RF-05 | Interface, Positivo | Status inicial do check-in | 1. Fazer um check-in 2. Olhar o status | PENDENTE | APROVADO, já com 10 pontos | Fail | BUG-CHK-01 |
| CT-CHK-04 | RF-05 | API, Negativo | Usuário comum muda o status | 1. Login como Pedro 2. Tentar alterar o status de um check-in | 403 | Pela interface não há opção (linha 16). Pela API não executado | N/E | |
| CT-CHK-05 | RF-06 | Interface, Negativo | Mexer no check-in de outro usuário | 1. Entrar como Maria 2. Tentar ver, editar ou apagar o check-in do Pedro | Negado | Sem opção na tela (linha 15) | Pass | |
| CT-CHK-06 | RF-06 | API, Negativo | Ler e editar check-in alheio | 1. Login do Pedro 2. GET /api/checkins 3. GET /api/checkins/(ID da Maria) 4. PUT no mesmo ID | Lista só os do Pedro. Leitura e edição negadas | Lista com 5 check-ins de outros. Leitura por ID: 200 com dados da Maria. Edição: 403 | Fail | BUG-CHK-02 |
| CT-CHK-07 | RNF-02 | Interface, Positivo | Foto válida processada | 1. Fazer check-in com foto JPG 2. Ver o check-in | Foto no Cloudinary e exibida | Foto exibida, URL do Cloudinary na API | Pass | |
| CT-CHK-08 | RNF-02 | Servidor, Positivo | Arquivos temporários removidos | 1. Fazer um check-in 2. Listar a pasta de uploads do servidor | Nenhum arquivo temporário sobrando | Não executado | N/E | |

## 3. Sistema de Pontos

| ID | Req. | Onde e tipo | Cenário | Passos | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|---|---|---|
| CT-PTS-01 | RF-07 | Interface, Positivo | Pontos somados ao check-in | 1. Anotar o ranking 2. Fazer check-in 3. Abrir o ranking | Pontos somados e ranking atualizado | Pontos sobem a cada check-in (linha 18) | Pass | |
| CT-PTS-02 | RF-07 | API, Positivo | Ranking pela API | 1. GET /api/rankings | Saldo e contagem por usuário | 200 com totalPoints e checkInsCount (40, 30, 20, 0) | Pass | |
| CT-PTS-03 | RF-08 | Interface, Positivo | Pontos subtraídos ao apagar | 1. Entrar como Admin 2. Anotar o ranking 3. Apagar um check-in 4. Abrir o ranking | Pontos subtraídos | Pontuação inalterada (linha 19) | Fail | BUG-PTS-01 |
| CT-PTS-04 | RF-08 | API, Positivo | Apagar e conferir o ranking | 1. GET /api/rankings 2. DELETE /api/checkins/(ID) como Admin 3. GET /api/checkins/(ID) 4. GET /api/rankings | Usuário de 20 para 10 pontos e de 2 para 1 check-in | DELETE 200 e GET do check-in apagado 404, mas ranking igual (20 pontos e 2 check-ins) | Fail | BUG-PTS-01 |
| CT-PTS-05 | RF-09 | Interface, Positivo | Ordem do ranking | 1. Abrir o ranking | Decrescente por pontos | Decrescente (linha 20). Pela API: 40, 30, 20, 0 | Pass | |
| CT-PTS-06 | RF-09 | Interface, Negativo | Dois check-ins em menos de 24 horas | 1. Entrar como Pedro 2. Fazer um check-in 3. Fazer outro em seguida | Segundo recusado | Segundo aceito e pontos somados (linha 12). Versão pela API não executada | Fail | BUG-PTS-02 |

## 4. Casos de regressão (cenários simulados)

Status: simulados e descritos, não executados.

### CT-AUT-R: se o BUG-AUTH-01 for corrigido (login bloqueado até verificar)

Repetir:
- Login válido de Admin, Lucas, Maria e Pedro. Os usuários do seed precisam continuar entrando.
- Login com senha errada (401).
- Login de conta não verificada (401 ou 403, com a mensagem do RF-02).
- Cadastro válido (201) e com e-mail repetido (409).
- Rota protegida sem token e com token vencido (401).
- Trainee em /api/seasons (403) e Admin (200).
- Refresh token.
- Um check-in feito logo após o login, já que depende da autenticação.

### CT-CHK-R: se o BUG-CHK-01 for corrigido (check-in nasce PENDENTE)

Repetir:
- Criar check-in com foto válida (201, status PENDENTE).
- Admin aprova (APROVADO, pontos entram) e Admin rejeita (REJEITADO, sem pontos).
- Usuário comum tentando mudar o status (403).
- Ranking somando só check-ins aprovados.
- Maria sem acesso aos check-ins do Pedro (CT-CHK-05 e CT-CHK-06).
- Foto repetida e limite de um check-in por dia.
- Apagar check-in e conferir o ranking (RF-08).

### CT-PTS-R: se o BUG-PTS-01 for corrigido (apagar subtrai pontos)

Repetir:
- Check-in somando pontos e ranking atualizado (CT-PTS-01 e CT-PTS-02).
- Apagar check-in: pontos e contagem diminuem.
- Apagar o único check-in de um usuário: saldo zera, sem ficar negativo.
- Ordem decrescente e desempate por checkInsCount.
- Apagar um check-in pendente ou rejeitado: não subtrai pontos que não entraram.
- Limite de 24 horas entre check-ins.
- Trainee tentando apagar check-in alheio (negado).

## 5. Casos ad-hoc (fora do planejamento inicial)

| ID | Cenário | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|
| CT-ADHOC-01 | GET /api-docs no navegador | Página Swagger (README do backend) | 404 "Rota não encontrada" | Fail | BUG-DOC-01 |
| CT-ADHOC-02 | Cadastro pela API com e-mail repetido | 409 | 409 "Este registro já existe" | Pass | |
| CT-ADHOC-03 | PUT com token vencido | 401 | 401 "Token inválido ou expirado" | Pass | |
| CT-ADHOC-04 | Escolher arquivo que não é imagem no upload | Só imagens | O seletor só lista imagens (filtro do navegador). Servidor não testado pela API | Pass | |
