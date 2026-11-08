# Execução dos testes de API (Insomnia)

Data: 07/11/2026. Ambiente: localhost, Docker Compose.
Resultado: Pass (passou), Fail (falhou) ou N/E (não executado).

| # | Caso | Requisição no Insomnia | Esperado | Obtido | Resultado | Bug |
|---|---|---|---|---|---|---|
| 1 | Smoke | GET /health | 200 | 200 OK, "NaSalinha API is running" | Pass | |
| 2 | Smoke | POST /api/auth/login (Admin) | 200 com tokens | 200, role ADMIN, com tokens | Pass | |
| 3 | CT-ADHOC-02 | POST /api/auth/register (e-mail já usado) | 409 | 409 "Este registro já existe" (campo email) | Pass | |
| 4 | CT-AUT-02 | POST /api/auth/register (teste3@example.com) | 201, conta PENDENTE, sem acesso | 201, role MEMBER, devolve accessToken e refreshToken, sem campo de status da conta | Fail | BUG-AUTH-01 (relacionado) |
| 5 | CT-AUT-04 | POST /api/auth/login (teste3, sem verificar) | 401/403 com a mensagem de verificação e sem token | 200 "Login realizado com sucesso" com tokens | Fail | BUG-AUTH-01 |
| 6 | Smoke | POST /api/auth/login (Pedro) | 200 com tokens | 200, role TRAINEE | Pass | |
| 7 | CT-ADHOC-03 | PUT /api/checkins/:id com token vencido | 401 | 401 "Token inválido ou expirado" | Pass | |
| 8 | CT-CHK-06 | GET /api/checkins (token do Pedro) | só os check-ins do Pedro | 200 com 5 check-ins de outros usuários | Fail | BUG-CHK-02 |
| 9 | CT-CHK-06 | GET /api/checkins/:id (check-in da Maria, como Pedro) | 403 ou 404 | 200 com os dados completos | Fail | BUG-CHK-02 |
| 10 | CT-CHK-06 | PUT /api/checkins/:id (check-in da Maria, como Pedro) | 403 ou 404 | 403 | Pass | |
| 11 | CT-PTS-05 (via API) | GET /api/rankings | ordem decrescente de pontos | 40, 30, 20, 0 (decrescente) | Pass | |
| 12 | CT-PTS-04 | DELETE /api/checkins/:id (Admin) | 200 ou 204 | 200 "Check-in deletado com sucesso" | Pass | |
| 13 | CT-PTS-04 | GET /api/rankings depois do DELETE | naonny com 10 pontos e 1 check-in | igual ao de antes: 20 pontos e 2 check-ins | Fail | BUG-PTS-01 |
| 14 | CT-PTS-04 | GET /api/checkins/:id do check-in apagado | 404 | (preencher) | (preencher) | |
| 15 | CT-AUT-06 | POST /api/seasons (token do Pedro, Body {}) | 403 | (preencher) | (preencher) | |
| 16 | CT-ADHOC-01 | GET /api-docs (navegador) | página Swagger | 404 "Rota não encontrada" | Fail | BUG-DOC-01 |
| 17 | CT-PTS-06 | POST /api/checkins duas vezes seguidas | segundo recusado (RF-09) | Não executado pela API. Comprovado pela interface | N/E | BUG-PTS-02 |

## Casos de API planejados e ainda não executados

- CT-AUT-08: senha nunca retorna em texto e fica como hash no banco
- CT-CHK-02: POST de check-in com foto válida pela API
- CT-CHK-04: usuário comum tentando mudar o status de um check-in pela API
- CT-PTS-06: dois check-ins seguidos pela API (linha 17)

## Observações soltas (ainda não são bugs)

- O README do backend diz que o token vale 7 dias, mas o `.env` usa 1 hora.
- O README do backend usa `EMAIL_PASS` com Gmail, enquanto o `.env` usa
  `EMAIL_PASSWORD` com host da Hostinger no modelo. O README da raiz manda usar o Mailtrap.
- O README do backend diz que o ranking é atualizado ao aprovar check-ins,
  mas os pontos entram na criação (check-in já nasce aprovado).
- O README cita o arquivo `postman_NaSalinha.json`. Conferir se existe no projeto.
- Check-ins ligados à "Temporada 2025.1", com a data atual em novembro de 2026.
  Conferir na tela de Temporadas (como Admin) se ela é a ativa.
- Rate limiting de 100 requisições a cada 15 minutos. Se passar do limite:
  `docker compose restart backend`.
- Token de acesso vale 1 hora.
