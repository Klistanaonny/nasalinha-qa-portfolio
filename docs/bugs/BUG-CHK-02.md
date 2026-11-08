# BUG-CHK-02: Usuário comum lê check-ins de outros usuários

- **Área:** Check-in por Foto
- **Requisito violado:** RF-06 (Restrição de Propriedade)
- **Casos de teste:** CT-CHK-05 (interface, sem falha aparente) e CT-CHK-06 (API)
- **Localização:** Backend / API (`GET /api/checkins` e `GET /api/checkins/:id`)
- **Ambiente:** localhost, Docker Compose, NaSalinha (Node.js + PostgreSQL)
- **Data:** 07/11/2026
- **Severidade:** Maior (falha no controle de acesso; expõe dados de outros usuários)

## Passos para reproduzir

1. Enviar `POST http://localhost:5001/api/auth/login` com pedro@example.com
   (perfil TRAINEE) e copiar o `accessToken`.
2. Enviar `GET http://localhost:5001/api/checkins` com esse token
   (Auth > Bearer Token).
3. Enviar `GET http://localhost:5001/api/checkins/69d3c236-ab62-4957-86e4-977fba721d37`
   com o mesmo token (ID de um check-in da Maria Santos).

## Resultado esperado

- Passo 2: apenas os check-ins do próprio Pedro (ou lista vazia).
- Passo 3: acesso negado (status 403 ou 404).

## Resultado atual

- Passo 2: status 200 com 5 check-ins, todos de outros usuários e nenhum do
  Pedro: 2 de "Maria Santos", 2 de "Lucas Silva" e 1 de "naonny". Cada item
  expõe e-mail e ID do dono.
- Passo 3: status 200 com os dados completos do check-in da Maria (foto,
  pontos, status, e-mail e ID dela).

## Observações

- Na interface o bloqueio parecia funcionar (exploração, linha 15). O problema
  aparece na API, o que indica que a restrição existe só na tela.
- Edição e exclusão de check-ins de outros usuários serão testadas à parte.

## Análise de causa raiz (hipótese)

Os endpoints de leitura parecem não comparar o dono do check-in com o ID do
usuário autenticado. Hipótese a confirmar no código do controller, que não foi
lido nesta auditoria.

## Evidências

- ![Lista de check-ins como Pedro](../evidencias/API-04-listar-checkins-pedro.png)
- ![Check-in da Maria lido pelo Pedro](../evidencias/API-05-ver-checkin-da-maria.png)
