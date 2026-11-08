# BUG-DOC-01: Documentação Swagger indicada no README não está disponível

- **Área:** Documentação da API (bug extra, fora das 3 áreas principais)
- **Requisito violado:** nenhum do documento de requisitos. Diverge do README do backend
- **Caso de teste:** CT-ADHOC-01
- **Localização:** Backend (rota /api-docs)
- **Ambiente:** localhost, Docker Compose, NaSalinha (Node.js + PostgreSQL)
- **Data:** 07/11/2026
- **Severidade:** Menor (documentação indisponível, sem impacto nas regras de negócio)

## Passos para reproduzir

1. Com o sistema ligado, abrir http://localhost:5001/api-docs no navegador.

## Resultado esperado

Página do Swagger UI com a lista de endpoints, como informa o README do backend
("A documentação interativa Swagger está disponível em
http://localhost:5001/api-docs").

## Resultado atual

Resposta JSON: {"success":false,"message":"Rota não encontrada"}.

## Consequência

Quem testa ou integra com a API fica sem a referência oficial dos endpoints e
dos campos de cada requisição (por exemplo, o nome do campo de upload da foto).

## Análise de causa raiz (hipótese)

A rota do Swagger não está registrada no servidor, ou foi removida, embora as
bibliotecas swagger-jsdoc e swagger-ui-express estejam listadas no README.
Hipótese a confirmar no código do servidor, que não foi lido nesta auditoria.

## Evidências

- ![Resposta 404 em /api-docs](../evidencias/DOC-01-api-docs-404.png)
