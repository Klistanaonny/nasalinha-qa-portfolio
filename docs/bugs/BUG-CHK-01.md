# BUG-CHK-01: Check-in nasce com status APROVADO e já com pontos

- **Área:** Check-in por Foto
- **Requisito violado:** RF-05 (Fluxo de Status do Check-in)
- **Caso de teste:** CT-CHK-03 (interface)
- **Localização:** Backend (criação do check-in)
- **Ambiente:** localhost, Docker Compose, NaSalinha (Node.js + PostgreSQL)
- **Data:** 07/11/2026
- **Severidade:** Maior (erro de lógica no fluxo de status)
- **Status:** Aberto

## Passos para reproduzir

1. Entrar como Pedro (pedro@example.com).
2. Fazer um check-in com uma foto.
3. Olhar o status do check-in na interface.
4. Na API, enviar `GET http://localhost:5001/api/checkins` (ou
   `GET /api/checkins/:id`) com o token do usuário que fez o check-in.

## Resultado esperado

Status inicial PENDENTE. Só o Administrador altera para Aprovado ou Rejeitado.

## Resultado atual

- Interface: o check-in aparece como "Aprovado".
- API: o check-in 33307ed3-b831-4036-bd75-046ef9794858, criado pelo testador
  pela interface, retorna `"status": "APPROVED"` e `"points": 10`, com
  `createdAt` igual a `updatedAt` (ninguém alterou o status depois).

## Consequência

Como todo check-in já nasce aprovado, o Administrador não tem o que aprovar ou
rejeitar e a etapa de moderação deixa de ter efeito. Os pontos entram no
ranking sem nenhuma validação.

## Análise técnica

Pelo comportamento observado, o check-in é gravado com status APPROVED e com os
pontos já atribuídos no momento da criação. A correção deve gravar PENDING na
criação e atribuir os pontos somente depois da aprovação pelo Administrador,
como descreve o README do backend.

## Observações

- Na exploração (linha 17), como Administrador, não foi possível mudar o status
  dos check-ins porque todos já estavam aprovados.
- O check-in 33307ed3-b831-4036-bd75-046ef9794858 foi apagado depois, no teste
  CT-PTS-04. Por isso a consulta a esse ID hoje retorna 404.

## Evidências

- ![Check-in aprovado na interface](../evidencias/CHK-01-status-aprovado.png)
- ![Resposta da API](../evidencias/API-04-listar-checkins-pedro.png)
