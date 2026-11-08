# Execução dos testes de API (Insomnia)

Datas: 07/11/2026 e 08/11/2026. Ambiente: localhost, Docker Compose.
Base da API: http://localhost:5001. Ferramenta: Insomnia.

Usuários usados: Admin (admin@nasalinha.com), Pedro (pedro@example.com,
Trainee) e a conta teste3@example.com, criada no teste de cadastro.

Resultado: Pass (passou), Fail (falhou) ou N/E (não executado). Os bugs estão
em `bugs/` e as evidências em `evidencias/`.

| # | Caso | Requisição no Insomnia | Esperado | Obtido | Resultado | Bug | Evidência |
|---|---|---|---|---|---|---|---|
| 1 | Smoke | GET /health | 200 | 200 OK, "NaSalinha API is running" | Pass | | |
| 2 | Smoke | POST /api/auth/login (Admin) | 200 com tokens | 200, role ADMIN, com tokens | Pass | | |
| 3 | CT-ADHOC-02 | POST /api/auth/register (e-mail já usado) | 409 | 409 "Este registro já existe" (campo email) | Pass | | |
| 4 | CT-AUT-02 | POST /api/auth/register (teste3@example.com) | 201, conta PENDENTE, sem acesso | 201, role MEMBER, devolve accessToken e refreshToken, sem campo de status da conta | Fail | BUG-AUTH-01 (relacionado) | |
| 5 | CT-AUT-04 | POST /api/auth/login (teste3, sem verificar) | 401 ou 403 com a mensagem de verificação e sem token | 200 "Login realizado com sucesso" com tokens | Fail | BUG-AUTH-01 | BUG-AUTH-01-api-login-sem-verificar.png |
| 6 | Smoke | POST /api/auth/login (Pedro) | 200 com tokens | 200, role TRAINEE | Pass | | |
| 7 | CT-ADHOC-03 | PUT /api/checkins/:id com token vencido | 401 | 401 "Token inválido ou expirado" | Pass | | |
| 8 | CT-CHK-06 | GET /api/checkins (token do Pedro) | Só os check-ins do Pedro | 200 com 5 check-ins de outros usuários | Fail | BUG-CHK-02 | API-04-listar-checkins-pedro.png e API-04-listar-checkins-pedro-fim.png |
| 9 | CT-CHK-06 | GET /api/checkins/:id (check-in da Maria, como Pedro) | 403 ou 404 | 200 com os dados completos | Fail | BUG-CHK-02 | API-05-ver-checkin-da-maria.png |
| 10 | CT-CHK-06 | PUT /api/checkins/:id (check-in da Maria, como Pedro) | 403 ou 404 | 403 | Pass | | API-06-editar-checkin-alheio-403.png |
| 11 | CT-PTS-02 e CT-PTS-05 | GET /api/rankings | Ordem decrescente de pontos | 40, 30, 20, 0 (decrescente) | Pass | | PTS-03-api-ranking-antes-1.png e PTS-03-api-ranking-antes-2.png |
| 12 | CT-PTS-04 | DELETE /api/checkins/:id (Admin) | 200 ou 204 | 200 "Check-in deletado com sucesso" | Pass | | API-07-apagar-checkin-admin.png |
| 13 | CT-PTS-04 | GET /api/rankings depois do DELETE | naonny de 20 para 10 pontos e de 2 para 1 check-in | Igual ao de antes: 20 pontos e 2 check-ins | Fail | BUG-PTS-01 | PTS-03-api-ranking-depois.png e PTS-03-api-ranking-depois-2.png |
| 14 | CT-PTS-04 | GET /api/checkins/:id do check-in apagado | 404 | 404 "Check-in não encontrado" | Pass | | API-08-checkin-apagado-404.png |
| 15 | CT-AUT-06 | POST /api/seasons (token do Pedro, Body {}) | 403 | 403 "Sem permissão para acessar este recurso" | Pass | | |
| 16 | CT-ADHOC-01 | GET /api-docs (navegador) | Página Swagger | 404 "Rota não encontrada" | Fail | BUG-DOC-01 | DOC-01-api-docs-404.png |
| 17 | CT-PTS-06 | POST /api/checkins duas vezes seguidas | Segundo recusado (RF-09) | Não executado pela API. Comprovado pela interface (`00-exploracao.md`, linha 12) | N/E | BUG-PTS-02 | |

## Casos de API não executados

| Caso | O que não foi feito |
|---|---|
| CT-AUT-08 | Conferir no banco que a senha está com hash. As respostas de login e cadastro lidas não trazem a senha |
| CT-CHK-02 | POST /api/checkins com foto (multipart) |
| CT-CHK-04 | Usuário comum tentar mudar o status de um check-in pela API |
| CT-PTS-06 | POST /api/checkins duas vezes seguidas pela API. O comportamento foi comprovado pela interface |

## Observações adicionais (sem requisito violado)

- O README do backend diz que o token vale 7 dias, mas o `.env` usa 1 hora. Na
  prática, o token de acesso expira em 1 hora.
- O README do backend usa `EMAIL_PASS` com Gmail, enquanto o modelo
  `.env.example` usa `EMAIL_PASSWORD` com host da Hostinger. O README da raiz
  manda usar o Mailtrap.
- O README do backend diz que o ranking é atualizado ao aprovar check-ins, mas
  os pontos entram na criação (o check-in já nasce aprovado, BUG-CHK-01).
- A "Temporada 2025.1" está ativa (isActive true, 10 pontos por check-in), mas a
  data final é 30/06/2025 e a data atual é novembro de 2026. Check-ins estão
  sendo aceitos fora do período da temporada (GET /api/seasons).
- O backend limita as requisições a 100 a cada 15 minutos. Ao passar do limite,
  `docker compose restart backend` zera o contador.
