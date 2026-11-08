# Casos de teste

Contém os 22 casos planejados para as 3 áreas principais (`01-cenarios.md`), os
3 cenários de regressão e os 4 casos ad-hoc criados durante a execução.

Onde: Interface (navegador), API (Insomnia) ou Servidor. Tipo: Positivo (fluxo
normal) ou Negativo (dado inválido ou ação proibida). Status: Pass, Fail ou
N/E (não executado).

Os resultados vêm de `00-exploracao.md` (interface, 06 e 07/11/2026) e de
`02-execucao-api.md` (API, 07 e 08/11/2026). Os bugs estão em `bugs/` e as
evidências em `evidencias/`.

## 1. Autenticação (JWT)

| ID | Req. | Onde e tipo | Cenário | Passos | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|---|---|---|
| CT-AUT-01 | RF-01 | Interface, Positivo | Cadastro de usuário | 1. Abrir o site 2. Criar Conta 3. Preencher nome, e-mail e senha 4. Enviar 5. Abrir a inbox do Mailtrap | Conta PENDENTE e e-mail com código de verificação | Conta criada com login automático. E-mail de boas-vindas, sem código (00, linhas 2 a 5) | Fail | BUG-AUTH-02 |
| CT-AUT-02 | RF-01 | API, Positivo | Cadastro pela API | 1. POST /api/auth/register com nome, e-mail e senha novos 2. Ler a resposta | 201, conta PENDENTE, sem acesso liberado | 201 com role MEMBER, accessToken e refreshToken. Sem campo de status da conta (02, linha 4) | Fail | BUG-AUTH-01 (relacionado) |
| CT-AUT-03 | RF-02 | Interface, Negativo | Login sem verificar o e-mail | 1. Cadastrar usuário novo 2. Sem verificar, tentar entrar | Bloqueio com "Por favor, verifique sua conta para continuar" | Login liberado, sem mensagem (00, linha 4) | Fail | BUG-AUTH-01 |
| CT-AUT-04 | RF-02 | API, Negativo | Login de conta não verificada | 1. Cadastrar teste3@example.com 2. POST /api/auth/login com esse e-mail e senha | 401 ou 403, mensagem de verificação, sem token | 200 "Login realizado com sucesso" com tokens (02, linha 5) | Fail | BUG-AUTH-01 |
| CT-AUT-05 | RF-03 | Interface, Negativo | Trainee em áreas de gestão | 1. Entrar como pedro@example.com 2. Procurar Temporadas e Moderação | Sem acesso | Sem acesso à moderação (00, linhas 7 e 8) | Pass | |
| CT-AUT-06 | RF-03 | API, Negativo | Trainee criando temporada | 1. Login do Pedro 2. POST /api/seasons com o token e Body {} | 403 | 403 "Sem permissão para acessar este recurso" (02, linha 15) | Pass | |
| CT-AUT-07 | RNF-01 | Interface, Positivo | Login com a senha cadastrada | 1. Entrar com usuário cadastrado e senha correta | Login funciona (senha comparada com o hash) | Funcionou com Admin, Pedro e contas novas (00, linhas 4, 6 e 7). O hash é conferido no CT-AUT-08 | Pass | |
| CT-AUT-08 | RNF-01 | API, Negativo | Senha não exposta e guardada com hash | 1. Ler respostas de login e cadastro 2. Conferir a senha no banco | Senha nunca retorna e no banco está com hash | Respostas lidas não trazem senha. Banco não inspecionado | N/E | |

## 2. Check-in por Foto

| ID | Req. | Onde e tipo | Cenário | Passos | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|---|---|---|
| CT-CHK-01 | RF-04 | Interface, Negativo | Mesma foto enviada de novo | 1. Fazer check-in com a foto A 2. Fazer outro check-in com a mesma foto A | Segundo envio recusado | Aceito, com usuários diferentes (00, linha 13) e na mesma conta (print CHK-02) | Fail | BUG-CHK-03 |
| CT-CHK-02 | RF-04 | API, Positivo | Check-in com foto válida | 1. POST /api/checkins com a foto (Multipart) | 201 e check-in ligado à temporada ativa | Não executado | N/E | |
| CT-CHK-03 | RF-05 | Interface, Positivo | Status inicial do check-in | 1. Fazer um check-in 2. Olhar o status | PENDENTE | APROVADO, já com 10 pontos (00, linhas 11 e 17) | Fail | BUG-CHK-01 |
| CT-CHK-04 | RF-05 | API, Negativo | Usuário comum muda o status | 1. Login como Pedro 2. Tentar alterar o status de um check-in | 403 | Interface: sem opção para alterar (00, linha 16). API: não executado | N/E | |
| CT-CHK-05 | RF-06 | Interface, Negativo | Ver ou mexer no check-in de outro usuário | 1. Entrar com um usuário comum 2. Tentar ver, editar ou apagar check-ins de outros usuários | Negado | Sem opção para editar ou apagar. A tela inicial lista check-ins de outros usuários em "Atividade Recente" (00, linha 15, e print CHK-02) | Fail | BUG-CHK-02 |
| CT-CHK-06 | RF-06 | API, Negativo | Ler e editar check-in alheio | 1. Login do Pedro 2. GET /api/checkins 3. GET /api/checkins/(ID da Maria) 4. PUT no mesmo ID | Lista só os do Pedro. Leitura e edição negadas | Lista com 5 check-ins de outros. Leitura por ID: 200 com dados da Maria. Edição: 403 (02, linhas 8 a 10) | Fail | BUG-CHK-02 |
| CT-CHK-07 | RNF-02 | Interface, Positivo | Foto válida processada | 1. Fazer check-in com foto JPG 2. Ver o check-in | Foto no Cloudinary e exibida | Foto exibida, URL do Cloudinary na API (00, linha 10) | Pass | |
| CT-CHK-08 | RNF-02 | Servidor, Positivo | Arquivos temporários removidos | 1. Fazer um check-in 2. Listar a pasta de uploads do servidor | Nenhum arquivo temporário sobrando | Não executado | N/E | |

## 3. Sistema de Pontos

| ID | Req. | Onde e tipo | Cenário | Passos | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|---|---|---|
| CT-PTS-01 | RF-07 | Interface, Positivo | Pontos somados ao check-in | 1. Anotar o ranking 2. Fazer check-in 3. Abrir o ranking | Pontos somados e ranking atualizado | Pontos sobem a cada check-in (00, linha 18) | Pass | |
| CT-PTS-02 | RF-07 | API, Positivo | Ranking pela API | 1. GET /api/rankings | Saldo e contagem por usuário | 200 com totalPoints e checkInsCount (40, 30, 20, 0) (02, linha 11) | Pass | |
| CT-PTS-03 | RF-08 | Interface, Positivo | Pontos subtraídos ao apagar | 1. Entrar como Admin 2. Anotar o ranking 3. Apagar um check-in 4. Abrir o ranking | Pontos subtraídos | Pontuação inalterada (00, linha 19) | Fail | BUG-PTS-01 |
| CT-PTS-04 | RF-08 | API, Positivo | Apagar e conferir o ranking | 1. GET /api/rankings 2. DELETE /api/checkins/(ID) como Admin 3. GET /api/checkins/(ID) 4. GET /api/rankings | naonny de 20 para 10 pontos e de 2 para 1 check-in | DELETE 200 e GET do check-in apagado 404, mas ranking igual: 20 pontos e 2 check-ins (02, linhas 12 a 14) | Fail | BUG-PTS-01 |
| CT-PTS-05 | RF-09 | Interface, Positivo | Ordem do ranking | 1. Abrir o ranking | Decrescente por pontos | Decrescente (00, linha 20). Pela API: 40, 30, 20, 0 (02, linha 11) | Pass | |
| CT-PTS-06 | RF-09 | Interface e API, Negativo | Dois check-ins em menos de 24 horas | 1. Entrar como usuário comum 2. Fazer um check-in 3. Fazer outro em seguida (na API, POST /api/checkins duas vezes) | Segundo recusado | Interface: segundo aceito, com dois check-ins a um minuto de distância e pontos somados (00, linha 12, e print CHK-02). API: não executado (02, linha 17) | Fail | BUG-PTS-02 |

## 4. Casos de regressão (cenários simulados)

Cada cenário descreve os testes que seriam repetidos se o bug indicado fosse
corrigido, para garantir que a correção não causou efeitos colaterais.

### CT-AUT-R: se o BUG-AUTH-01 for corrigido (login bloqueado até verificar)

Repetir:
- CT-AUT-03 e CT-AUT-04: login de conta não verificada bloqueado (401 ou 403, com a mensagem do RF-02 e sem token).
- CT-AUT-02: cadastro pela API (201), sem acesso liberado enquanto a conta está PENDENTE.
- CT-AUT-07: login válido de Admin, Lucas, Maria e Pedro. Os usuários do seed precisam continuar entrando.
- Login com senha errada (401).
- CT-ADHOC-02: cadastro com e-mail repetido (409).
- CT-ADHOC-03: rota protegida com token vencido (401) e sem token (401).
- CT-AUT-05 e CT-AUT-06: Trainee bloqueado em /api/seasons (403) e Admin com acesso (200).
- Refresh token.
- CT-CHK-03: um check-in feito logo após o login, já que depende da autenticação.

### CT-CHK-R: se o BUG-CHK-01 for corrigido (check-in nasce PENDENTE)

Repetir:
- CT-CHK-03 e CT-CHK-02: criar check-in com foto válida (201, status PENDENTE).
- Admin aprova (APROVADO, pontos entram) e Admin rejeita (REJEITADO, sem pontos).
- CT-CHK-04: usuário comum tentando mudar o status (403).
- CT-PTS-01 e CT-PTS-02: ranking somando só check-ins aprovados.
- CT-CHK-05 e CT-CHK-06: usuário sem acesso aos check-ins de outros.
- CT-CHK-01 e CT-PTS-06: foto repetida e limite de um check-in por dia.
- CT-PTS-03 e CT-PTS-04: apagar check-in e conferir o ranking (RF-08).
- CT-CHK-07: upload de foto válida.

### CT-PTS-R: se o BUG-PTS-01 for corrigido (apagar subtrai pontos)

Repetir:
- CT-PTS-01 e CT-PTS-02: check-in somando pontos e ranking atualizado.
- CT-PTS-03 e CT-PTS-04: apagar check-in, com pontos e contagem diminuindo.
- Apagar o único check-in de um usuário: saldo zera, sem ficar negativo.
- CT-PTS-05: ordem decrescente e desempate por checkInsCount.
- Apagar um check-in pendente ou rejeitado: não subtrai pontos que não entraram.
- CT-PTS-06: limite de 24 horas entre check-ins.
- CT-CHK-05 e CT-CHK-06: Trainee tentando apagar check-in alheio (negado).

## 5. Casos ad-hoc (fora do planejamento inicial)

| ID | Onde | Cenário | Esperado | Obtido | Status | Bug |
|---|---|---|---|---|---|---|
| CT-ADHOC-01 | Navegador | GET /api-docs | Página Swagger (README do backend) | 404 "Rota não encontrada" (02, linha 16) | Fail | BUG-DOC-01 |
| CT-ADHOC-02 | API | Cadastro com e-mail repetido | 409 | 409 "Este registro já existe" (02, linha 3) | Pass | |
| CT-ADHOC-03 | API | PUT com token vencido | 401 | 401 "Token inválido ou expirado" (02, linha 7) | Pass | |
| CT-ADHOC-04 | Interface | Escolher arquivo que não é imagem no upload | Só imagens | O seletor só lista imagens (filtro do navegador). Servidor não testado pela API (00, linha 21) | Pass | |
