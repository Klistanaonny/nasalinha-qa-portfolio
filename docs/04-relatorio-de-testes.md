# Relatório de testes final: NaSalinha

Data: 08/11/2026. Ambiente: localhost, Docker Compose (Node.js, PostgreSQL,
React). Ferramentas: navegador, Insomnia e terminal do Docker.

## 1. Resumo

Foram planejados 22 casos de teste nas 3 áreas principais (Autenticação,
Check-in e Pontos) e criados 4 casos ad-hoc durante a execução. Dos 26 casos,
22 foram executados, com 10 Pass e 12 Fail. Foram registrados 8 bug reports: 7
de severidade Maior e 1 Menor. Nenhum bug trava o sistema (Crítico).

## 2. Métricas de execução

| Área | Planejados | Executados | Pass | Fail | Não executados |
|---|---|---|---|---|---|
| Autenticação (JWT) | 8 | 7 | 3 | 4 | 1 |
| Check-in por Foto | 8 | 5 | 1 | 4 | 3 |
| Sistema de Pontos | 6 | 6 | 3 | 3 | 0 |
| **Total das áreas principais** | **22** | **18** | **7** | **11** | **4** |
| Casos ad-hoc | 4 | 4 | 3 | 1 | 0 |
| **Total geral** | **26** | **22** | **10** | **12** | **4** |

- Taxa de execução: 18 de 22 nas áreas principais (81,8%) e 22 de 26 no total (84,6%).
- Dos 22 casos executados, 10 passaram (45,5%) e 12 falharam (54,5%).
- Nas áreas principais, dos 18 casos executados, 7 passaram e 11 falharam.
- Regressão: 3 cenários simulados e descritos (CT-AUT-R, CT-CHK-R e CT-PTS-R),
  que não entram nas contagens acima.

## 3. Distribuição de severidade

| Severidade | Critério | Quantidade | Bugs |
|---|---|---|---|
| Crítico | Trava o sistema | 0 | |
| Maior | Causa erro de lógica | 7 | BUG-AUTH-01, BUG-AUTH-02, BUG-CHK-01, BUG-CHK-02, BUG-CHK-03, BUG-PTS-01, BUG-PTS-02 |
| Menor | Erro visual ou de documentação | 1 | BUG-DOC-01 |

Total: 8 bugs.

## 4. Bugs encontrados

| Bug | Problema | Área | Requisito | Severidade | Casos de teste |
|---|---|---|---|---|---|
| BUG-AUTH-01 | Login liberado para conta que não verificou o e-mail | Autenticação | RF-02 | Maior | CT-AUT-02, CT-AUT-03, CT-AUT-04 |
| BUG-AUTH-02 | E-mail pós-cadastro sem código de verificação | Autenticação | RF-01 | Maior | CT-AUT-01 |
| BUG-CHK-01 | Check-in nasce aprovado e já com pontos | Check-in | RF-05 | Maior | CT-CHK-03 |
| BUG-CHK-02 | Usuário comum vê check-ins de outros usuários | Check-in | RF-06 | Maior | CT-CHK-05, CT-CHK-06 |
| BUG-CHK-03 | Mesma foto aceita em check-ins repetidos | Check-in | RF-04 | Maior | CT-CHK-01 |
| BUG-PTS-01 | Apagar check-in não subtrai pontos do ranking | Pontos | RF-08 | Maior | CT-PTS-03, CT-PTS-04 |
| BUG-PTS-02 | Dois check-ins seguidos do mesmo usuário aceitos | Pontos | RF-09 e RF-04 | Maior | CT-PTS-06 |
| BUG-DOC-01 | Swagger indicado no README do backend não existe | Documentação (extra) | Diverge do README | Menor | CT-ADHOC-01 |

## 5. Resultado por requisito

| Requisito | Casos | Resultado |
|---|---|---|
| RF-01 Cadastro e verificação | CT-AUT-01, CT-AUT-02 | Fail nos dois |
| RF-02 Restrição de login | CT-AUT-03, CT-AUT-04 | Fail nos dois |
| RF-03 Controle de níveis | CT-AUT-05, CT-AUT-06 | Pass nos dois |
| RNF-01 Senha com hash | CT-AUT-07, CT-AUT-08 | Um Pass e um não executado |
| RF-04 Registro de check-in | CT-CHK-01, CT-CHK-02 | Um Fail e um não executado |
| RF-05 Fluxo de status | CT-CHK-03, CT-CHK-04 | Um Fail e um não executado |
| RF-06 Restrição de propriedade | CT-CHK-05, CT-CHK-06 | Fail nos dois |
| RNF-02 Integridade de arquivos | CT-CHK-07, CT-CHK-08 | Um Pass e um não executado |
| RF-07 Atribuição de pontos | CT-PTS-01, CT-PTS-02 | Pass nos dois |
| RF-08 Consistência de saldo | CT-PTS-03, CT-PTS-04 | Fail nos dois |
| RF-09 Regras de ranking | CT-PTS-05, CT-PTS-06 | Um Pass e um Fail |

## 6. Casos não executados

| Caso | O que não foi feito | Cobertura alternativa |
|---|---|---|
| CT-AUT-08 | Conferir no banco que a senha está com hash | As respostas de login e cadastro lidas não trazem a senha |
| CT-CHK-02 | POST de check-in com foto (multipart) pela API | Check-ins com foto foram feitos pela interface (CT-CHK-07) |
| CT-CHK-04 | Usuário comum mudar o status de um check-in pela API | Pela interface não há opção para alterar o status (`00-exploracao.md`, linha 16) |
| CT-CHK-08 | Inspecionar a pasta de uploads do servidor | Nenhuma |

Há ainda a variante de API do CT-PTS-06, não executada. O comportamento foi
comprovado pela interface (`00-exploracao.md`, linha 12).

## 7. Escopo e limitações

- Abordagem de caixa-preta: os resultados vêm do comportamento observado na
  interface e na API. O código-fonte não foi lido, e as sugestões de correção
  nos bug reports descrevem o comportamento esperado.
- Testes de interface feitos em 06 e 07/11 e de API em 07 e 08/11, com os
  dados do seed e contas criadas no teste.
- Divergências entre o README e o ambiente que não viraram bug estão em
  `02-execucao-api.md`, como a validade do token e a temporada ativa com data
  vencida.

## 8. Conclusão e recomendações

Mais da metade dos casos executados falhou (12 de 22). Dos 11 requisitos, 7
tiveram falha (RF-01, RF-02, RF-04, RF-05, RF-06, RF-08 e RF-09), 2 passaram em
todos os casos (RF-03 e RF-07) e 2 tiveram um caso aprovado e outro não
executado (RNF-01 e RNF-02). As falhas se concentram em regras de negócio:
acesso sem verificação de conta, check-ins de outros usuários visíveis,
status inicial incorreto e pontuação manipulável (sem limite de 24 horas, com
foto repetida e sem subtrair pontos ao apagar). Na avaliação desta auditoria, o
sistema não está pronto para produção.

Pontos que funcionam bem: bloqueio de Trainee em rotas de gestão pela API
(403), edição de check-in alheio bloqueada (403), cadastro com e-mail repetido
recusado (409), token vencido recusado (401) e ordem decrescente do ranking.

Ordem sugerida de correção:
1. Controle de acesso: BUG-AUTH-01 e BUG-CHK-02.
2. Integridade da pontuação: BUG-PTS-01, BUG-PTS-02 e BUG-CHK-03.
3. Fluxo de status do check-in: BUG-CHK-01.
4. Verificação de e-mail: BUG-AUTH-02.
5. Documentação da API: BUG-DOC-01.
