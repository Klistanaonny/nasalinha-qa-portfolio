# Relatório de testes final: NaSalinha

Data: 08/11/2026. Ambiente: localhost, Docker Compose (Node.js, PostgreSQL,
React). Ferramentas: navegador, Insomnia e terminal do Docker.

## 1. Resumo

Foram planejados 22 casos de teste nas 3 áreas principais (Autenticação,
Check-in e Pontos) e 4 casos ad-hoc, criados durante a execução. Foram
executados 22 dos 26 casos. Foram registrados 7 bug reports, 6 de severidade
Maior e 1 de severidade Menor. Nenhum bug trava o sistema (Crítico).

## 2. Métricas de execução

| Área | Planejados | Executados | Pass | Fail | Não executados |
|---|---|---|---|---|---|
| Autenticação (JWT) | 8 | 7 | 3 | 4 | 1 |
| Check-in por Foto | 8 | 5 | 2 | 3 | 3 |
| Sistema de Pontos | 6 | 6 | 3 | 3 | 0 |
| **Total das áreas principais** | **22** | **18** | **8** | **10** | **4** |
| Casos ad-hoc | 4 | 4 | 3 | 1 | 0 |
| **Total geral** | **26** | **22** | **11** | **11** | **4** |

- Taxa de execução: 18 de 22 nas áreas principais (81,8%) e 22 de 26 no total (84,6%).
- Dos 22 casos executados, 11 passaram (50,0%) e 11 falharam (50,0%).
- Casos de regressão: 3 cenários simulados e descritos (CT-AUT-R, CT-CHK-R e
  CT-PTS-R). Não entram nas contagens acima.

## 3. Distribuição de severidade

| Severidade | Critério | Quantidade | Bugs |
|---|---|---|---|
| Crítico | Trava o sistema | 0 | |
| Maior | Causa erro de lógica | 6 | BUG-AUTH-01, BUG-AUTH-02, BUG-CHK-01, BUG-CHK-02, BUG-CHK-03, BUG-PTS-01, BUG-PTS-02 |
| Menor | Erro visual ou de documentação | 1 | BUG-DOC-01 |

Total: 7 bugs.

(Conferir: a linha Maior lista os bugs de severidade Maior. Se a contagem
mudar depois da revisão de severidade, atualizar as quantidades.)

## 4. Bugs por área

| Área | Bugs |
|---|---|
| Autenticação (JWT) | BUG-AUTH-01 (login liberado sem verificar o e-mail), BUG-AUTH-02 (e-mail sem código de verificação) |
| Check-in por Foto | BUG-CHK-01 (check-in nasce aprovado), BUG-CHK-02 (usuário vê check-ins de outros), BUG-CHK-03 (foto repetida aceita) |
| Sistema de Pontos | BUG-PTS-01 (apagar check-in não subtrai pontos), BUG-PTS-02 (dois check-ins seguidos aceitos) |
| Documentação (extra) | BUG-DOC-01 (Swagger indicado no README não existe) |

## 5. Casos não executados

| Caso | Motivo |
|---|---|
| CT-AUT-08 | O banco de dados não foi inspecionado para confirmar o hash da senha |
| CT-CHK-02 | O envio de check-in com foto (multipart) não foi feito pela API |
| CT-CHK-04 | A mudança de status por usuário comum não foi testada pela API |
| CT-CHK-08 | A pasta de uploads do servidor não foi inspecionada |

## 6. Limitações

- Testes de interface feitos em 06/11 e de API em 07 e 08/11, com dados do seed.
- O código-fonte não foi lido. As causas dos bugs são hipóteses.
- A versão da API do CT-PTS-06 (check-ins seguidos) não foi executada. O
  comportamento foi comprovado pela interface.
- Observações que não viraram bug estão em `02-execucao-api.md`, como a
  diferença entre README e `.env` e a temporada ativa com data vencida.

## 7. Conclusão

Metade dos casos executados falhou e as falhas se concentram em regras de
negócio: acesso sem verificação de conta, check-ins de outros usuários
visíveis pela API, status inicial incorreto e pontuação manipulável (sem limite
de 24 horas, com foto repetida e sem subtrair pontos ao apagar). Na avaliação
desta auditoria, o sistema não está pronto para produção. Os pontos que
funcionam bem são o bloqueio de Trainee em rotas de gestão pela API (403), o
tratamento de token vencido (401) e a ordem decrescente do ranking.
