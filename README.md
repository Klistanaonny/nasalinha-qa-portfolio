# Auditoria de Qualidade do Sistema NaSalinha

**Autora:** Klistanaonny Amábille Diniz Lucchesi
**Função:** Trainee de Quality Assurance (QA) da Comp Júnior, 2026/2
**Data de entrega:** 08/11/2026

Portfólio de Qualidade desenvolvido como projeto final da trilha de Quality Assurance da Comp Júnior. O repositório reúne o planejamento, a execução e o relatório de uma auditoria de qualidade realizada sobre o sistema NaSalinha, com foco funcional, de API e de regressão.

## Sumário

1. [Visão geral](#1-visão-geral)
2. [Resumo dos resultados](#2-resumo-dos-resultados)
3. [Escopo e requisitos testados](#3-escopo-e-requisitos-testados)
4. [Estratégia e abordagem de testes](#4-estratégia-e-abordagem-de-testes)
5. [Ferramentas utilizadas](#5-ferramentas-utilizadas)
6. [Estrutura do repositório](#6-estrutura-do-repositório)
7. [Como reproduzir o ambiente](#7-como-reproduzir-o-ambiente)
8. [Casos de teste e regressão](#8-casos-de-teste-e-regressão)
9. [Métricas de execução](#9-métricas-de-execução)
10. [Bugs encontrados](#10-bugs-encontrados)
11. [Rastreabilidade por requisito](#11-rastreabilidade-por-requisito)
12. [Casos não executados e limitações](#12-casos-não-executados-e-limitações)
13. [Observações adicionais](#13-observações-adicionais)
14. [Desafios de configuração do ambiente](#14-desafios-de-configuração-do-ambiente)
15. [Conclusão](#15-conclusão)

## 1. Visão geral

O NaSalinha é um sistema de check-in gamificado da Comp Júnior: os usuários registram presença por meio de uma foto, acumulam pontos em temporadas e aparecem em um ranking. O sistema tem três níveis de acesso (Admin, Membro e Trainee) e foi disponibilizado com inconsistências propositais para treinamento de QA.

- **Sistema testado:** [nasalinha-qa-challenge](https://github.com/Gustavohmmagalhaes/nasalinha-qa-challenge)
- **Tecnologias do sistema:** Node.js, Express, Prisma ORM, PostgreSQL, React 18 e TailwindCSS. Serviços externos: Cloudinary (imagens) e Nodemailer com Mailtrap (e-mails).
- **Referência de comportamento esperado:** Documento de Requisitos do projeto NaSalinha, com 11 requisitos em 3 módulos.
- **Opção de documentação adotada:** Opção B (documental), com os artefatos em Markdown na pasta `docs/`.

## 2. Resumo dos resultados

| Indicador | Resultado |
|---|---|
| Requisitos cobertos | 11 (9 funcionais e 2 não funcionais) |
| Casos de teste planejados | 22 nas áreas principais, mais 4 ad-hoc, totalizando 26 |
| Casos executados | 22 de 26 (84,6%) |
| Resultado dos casos executados | 10 Pass (45,5%) e 12 Fail (54,5%) |
| Casos não executados | 4 |
| Bugs registrados | 8 (0 Crítico, 7 Maior e 1 Menor) |
| Cenários de regressão | 3 (um por área), simulados e descritos |

**Avaliação geral:** na avaliação desta auditoria, o sistema não está pronto para produção. As falhas se concentram em regras de negócio (veja a [Conclusão](#15-conclusão)).

## 3. Escopo e requisitos testados

A auditoria cobriu as três áreas principais definidas no desafio.

| Área | Requisitos |
|---|---|
| Autenticação e acesso (JWT) | RF-01 Cadastro e verificação, RF-02 Restrição de login, RF-03 Controle de níveis (RBAC), RNF-01 Hash de senhas |
| Check-in por foto | RF-04 Registro de atividade, RF-05 Fluxo de status, RF-06 Restrição de propriedade, RNF-02 Integridade de arquivos |
| Pontuação e ranking | RF-07 Atribuição de pontos, RF-08 Consistência de saldo, RF-09 Regras de ranking |

## 4. Estratégia e abordagem de testes

A abordagem foi de **caixa-preta**: os resultados vêm do comportamento observado na interface e na API, sem leitura do código-fonte.

1. Montagem do ambiente local com Docker e navegação exploratória, para entender o fluxo do usuário.
2. Definição dos cenários a partir do documento de requisitos, com dois casos por requisito: um de interface e um de API (no RNF-02, de interface e de servidor).
3. Execução da interface no navegador e da API no Insomnia, comparando cada resultado com o requisito.
4. Registro de cada falha em um bug report individual, com passos, resultado esperado, resultado atual, severidade e evidências. Cada bug trata de um único problema.
5. Registro de cenários fora do planejamento como casos ad-hoc, mantendo a rastreabilidade.
6. Descrição de um cenário de regressão por área, indicando os testes a repetir caso o bug principal fosse corrigido.
7. Consolidação das métricas no relatório final.

**Tipos de teste:**

| Tipo | Aplicação |
|---|---|
| Funcional | Comportamento na interface comparado aos requisitos |
| Validação de API | Endpoints, status codes, corpo das respostas e permissões |
| Regressão | Cenários simulados, descritos para cada área |

**Severidade:**

| Nível | Critério |
|---|---|
| Crítico | Trava o sistema |
| Maior | Causa erro de lógica |
| Menor | Erro visual ou de documentação |

**Convenção de identificadores:**

| Prefixo | Significado |
|---|---|
| `CT-AUT`, `CT-CHK`, `CT-PTS` | Casos de teste de Autenticação, Check-in e Pontos |
| `CT-xxx-R` | Cenário de regressão da área |
| `CT-ADHOC` | Caso ad-hoc, criado durante a execução |
| `BUG-AUTH`, `BUG-CHK`, `BUG-PTS`, `BUG-DOC` | Bug reports por área (DOC é a documentação) |

## 5. Ferramentas utilizadas

| Ferramenta | Uso |
|---|---|
| Docker e Docker Compose | Ambiente local (banco de dados, backend e frontend) |
| Navegador (DevTools) | Testes de interface e captura de evidências |
| Insomnia | Testes de API: autenticação, permissões e regras de negócio |
| Mailtrap (Email Sandbox) | Caixa de e-mails de teste |
| Cloudinary | Armazenamento das fotos dos check-ins |
| Git e GitHub | Versionamento e entrega do portfólio |
| Visual Studio Code | Escrita da documentação em Markdown |

## 6. Estrutura do repositório

```
nasalinha-qa-portfolio/
├── README.md                      Este documento
├── api-collection/
│   ├── nasalinha-insomnia.json    Coleção exportada do Insomnia (JSON)
│   └── nasalinha-insomnia.yaml    A mesma coleção, em YAML
└── docs/
    ├── 00-exploracao.md           Exploração inicial do sistema pela interface
    ├── 01-cenarios.md             Planejamento dos cenários e cobertura dos requisitos
    ├── 02-execucao-api.md         Execução dos testes de API no Insomnia
    ├── 03-casos-de-teste.md       Casos detalhados, regressão e ad-hoc
    ├── 04-relatorio-de-testes.md  Relatório final com métricas e severidade
    ├── bugs/                      Um bug report por arquivo
    └── evidencias/                Prints e capturas dos testes
```

**Ordem de leitura sugerida:** `00-exploracao.md`, `01-cenarios.md`, `03-casos-de-teste.md`, `02-execucao-api.md`, a pasta `bugs/` e, por fim, `04-relatorio-de-testes.md`.

| Arquivo | Conteúdo |
|---|---|
| [`docs/00-exploracao.md`](docs/00-exploracao.md) | 21 passos executados pela interface, cada um ligado a casos e bugs |
| [`docs/01-cenarios.md`](docs/01-cenarios.md) | 22 cenários planejados e tabela de cobertura dos 11 requisitos |
| [`docs/02-execucao-api.md`](docs/02-execucao-api.md) | 17 requisições executadas no Insomnia, com resultado e evidência |
| [`docs/03-casos-de-teste.md`](docs/03-casos-de-teste.md) | Casos com passos, resultado esperado e obtido, regressão e ad-hoc |
| [`docs/04-relatorio-de-testes.md`](docs/04-relatorio-de-testes.md) | Métricas, severidade, resultado por requisito e recomendações |
| [`docs/bugs/`](docs/bugs/) | Os 8 bug reports |
| [`docs/evidencias/`](docs/evidencias/) | Evidências usadas nos bug reports |
| [`api-collection/`](api-collection/) | Coleção do Insomnia com as requisições executadas |

## 7. Como reproduzir o ambiente

**Pré-requisitos:** Git, Docker Desktop, contas gratuitas no [Mailtrap](https://mailtrap.io/) e no [Cloudinary](https://cloudinary.com/) e o Insomnia (ou outro cliente de API).

1. Clonar o sistema testado:
   ```bash
   git clone https://github.com/Gustavohmmagalhaes/nasalinha-qa-challenge.git
   cd nasalinha-qa-challenge
   ```
2. Criar o arquivo de configuração a partir do modelo:
   ```bash
   cd backend
   copy .env.example .env      # em Linux ou Mac: cp .env.example .env
   ```
3. Preencher no `.env` as credenciais do Cloudinary (`CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY` e `CLOUDINARY_API_SECRET`) e as do Mailtrap (`EMAIL_HOST`, `EMAIL_PORT`, `EMAIL_USER` e `EMAIL_PASSWORD`). O arquivo `.env` contém dados sensíveis e **não** faz parte deste repositório.
4. Subir os serviços na raiz do projeto:
   ```bash
   docker compose up --build
   ```
5. Em outro terminal, criar as tabelas e os dados iniciais:
   ```bash
   docker compose exec backend npx prisma migrate dev --name init
   docker compose exec backend npx prisma db seed
   ```
6. Acessar o sistema:

| Serviço | Endereço |
|---|---|
| Interface | http://localhost:3000 |
| API | http://localhost:5001/api |
| Verificação da API | http://localhost:5001/health |

**Usuários de teste (criados pelo seed, senha `senha123`):**

| Perfil | E-mail |
|---|---|
| Admin | admin@nasalinha.com |
| Membro | lucas@example.com e maria@example.com |
| Trainee | pedro@example.com |

**Importar a coleção da API:** no Insomnia, importar o arquivo `nasalinha-insomnia.json` (ou o `.yaml`) da pasta `api-collection/`. Os tokens de acesso expiram em 1 hora. Para obter um novo, executar o login e copiar o campo `accessToken` para a aba Auth (Bearer Token). Se o limite de requisições (100 a cada 15 minutos) for atingido, usar `docker compose restart backend`.

## 8. Casos de teste e regressão

**Casos planejados** (detalhes em [`03-casos-de-teste.md`](docs/03-casos-de-teste.md)):

| Área | Casos | Interface | API | Servidor | Interface e API |
|---|---|---|---|---|---|
| Autenticação (JWT) | 8 | 4 | 4 | 0 | 0 |
| Check-in por foto | 8 | 4 | 3 | 1 | 0 |
| Sistema de pontos | 6 | 3 | 2 | 0 | 1 |
| **Total** | **22** | **11** | **9** | **1** | **1** |

O caso de "Interface e API" é o CT-PTS-06 (dois check-ins em menos de 24 horas), que prevê a verificação nas duas camadas.

**Casos ad-hoc** (criados durante a execução):

| Caso | Cenário | Resultado |
|---|---|---|
| CT-ADHOC-01 | Acesso a `/api-docs` (Swagger indicado no README) | Fail (BUG-DOC-01) |
| CT-ADHOC-02 | Cadastro com e-mail repetido | Pass (409) |
| CT-ADHOC-03 | Requisição com token vencido | Pass (401) |
| CT-ADHOC-04 | Envio de arquivo que não é imagem pelo seletor | Pass |

**Regressão:** cenários simulados, descritos e não executados, pois dependem da correção dos bugs.

| Cenário | Gatilho | Testes a repetir (resumo) |
|---|---|---|
| CT-AUT-R | Correção do BUG-AUTH-01 | Login válido dos quatro usuários, conta não verificada, cadastro, permissões de Trainee e Admin, token vencido, refresh token e check-in após login |
| CT-CHK-R | Correção do BUG-CHK-01 | Criação com status PENDENTE, aprovação e rejeição pelo Admin, permissões, pontos só para check-ins aprovados, foto repetida, limite diário e exclusão |
| CT-PTS-R | Correção do BUG-PTS-01 | Soma de pontos, exclusão com subtração, saldo zerado sem ficar negativo, ordem do ranking, desempate, check-in pendente ou rejeitado e limite de 24 horas |

## 9. Métricas de execução

| Área | Planejados | Executados | Pass | Fail | Não executados |
|---|---|---|---|---|---|
| Autenticação (JWT) | 8 | 7 | 3 | 4 | 1 |
| Check-in por foto | 8 | 5 | 1 | 4 | 3 |
| Sistema de pontos | 6 | 6 | 3 | 3 | 0 |
| **Total das áreas principais** | **22** | **18** | **7** | **11** | **4** |
| Casos ad-hoc | 4 | 4 | 3 | 1 | 0 |
| **Total geral** | **26** | **22** | **10** | **12** | **4** |

**Distribuição de severidade dos bugs:**

| Severidade | Quantidade |
|---|---|
| Crítico | 0 |
| Maior | 7 |
| Menor | 1 |
| **Total** | **8** |

## 10. Bugs encontrados

| Bug | Problema | Área | Requisito | Severidade |
|---|---|---|---|---|
| [BUG-AUTH-01](docs/bugs/BUG-AUTH-01.md) | Login liberado para conta que não verificou o e-mail | Autenticação | RF-02 | Maior |
| [BUG-AUTH-02](docs/bugs/BUG-AUTH-02.md) | E-mail pós-cadastro sem código de verificação | Autenticação | RF-01 | Maior |
| [BUG-CHK-01](docs/bugs/BUG-CHK-01.md) | Check-in nasce aprovado e já com pontos | Check-in | RF-05 | Maior |
| [BUG-CHK-02](docs/bugs/BUG-CHK-02.md) | Usuário comum lê check-ins de outros usuários | Check-in | RF-06 | Maior |
| [BUG-CHK-03](docs/bugs/BUG-CHK-03.md) | Mesma foto aceita em check-ins repetidos | Check-in | RF-04 | Maior |
| [BUG-PTS-01](docs/bugs/BUG-PTS-01.md) | Apagar check-in não subtrai pontos do ranking | Pontos | RF-08 | Maior |
| [BUG-PTS-02](docs/bugs/BUG-PTS-02.md) | Dois check-ins seguidos do mesmo usuário são aceitos | Pontos | RF-09 e RF-04 | Maior |
| [BUG-DOC-01](docs/bugs/BUG-DOC-01.md) | Swagger indicado no README do backend não existe | Documentação (extra) | Diverge do README | Menor |

Cada bug report segue o modelo da trilha: título, localização, ambiente, passos para reproduzir, resultado esperado, resultado atual, análise técnica, severidade e evidências.

## 11. Rastreabilidade por requisito

| Requisito | Casos | Resultado | Bug |
|---|---|---|---|
| RF-01 Cadastro e verificação | CT-AUT-01, CT-AUT-02 | Fail nos dois | BUG-AUTH-02 e BUG-AUTH-01 |
| RF-02 Restrição de login | CT-AUT-03, CT-AUT-04 | Fail nos dois | BUG-AUTH-01 |
| RF-03 Controle de níveis | CT-AUT-05, CT-AUT-06 | Pass nos dois | |
| RNF-01 Senha com hash | CT-AUT-07, CT-AUT-08 | Um Pass e um não executado | |
| RF-04 Registro de check-in | CT-CHK-01, CT-CHK-02 | Um Fail e um não executado | BUG-CHK-03 |
| RF-05 Fluxo de status | CT-CHK-03, CT-CHK-04 | Um Fail e um não executado | BUG-CHK-01 |
| RF-06 Restrição de propriedade | CT-CHK-05, CT-CHK-06 | Fail nos dois | BUG-CHK-02 |
| RNF-02 Integridade de arquivos | CT-CHK-07, CT-CHK-08 | Um Pass e um não executado | |
| RF-07 Atribuição de pontos | CT-PTS-01, CT-PTS-02 | Pass nos dois | |
| RF-08 Consistência de saldo | CT-PTS-03, CT-PTS-04 | Fail nos dois | BUG-PTS-01 |
| RF-09 Regras de ranking | CT-PTS-05, CT-PTS-06 | Um Pass e um Fail | BUG-PTS-02 |

## 12. Casos não executados e limitações

| Caso | O que não foi feito | Cobertura alternativa |
|---|---|---|
| CT-AUT-08 | Conferir no banco que a senha está com hash | As respostas de login e cadastro lidas não trazem a senha |
| CT-CHK-02 | POST de check-in com foto (multipart) pela API | Check-ins com foto foram feitos pela interface (CT-CHK-07) |
| CT-CHK-04 | Usuário comum tentar mudar o status de um check-in pela API | Pela interface não há opção para alterar o status |
| CT-CHK-08 | Inspecionar a pasta de uploads do servidor | Nenhuma |

A variante de API do CT-PTS-06 (dois check-ins seguidos) também não foi executada. O comportamento foi comprovado pela interface.

**Limitações:**

- Abordagem de caixa-preta: o código-fonte não foi lido.
- Testes de interface em 06 e 07/11/2026 e de API em 07 e 08/11/2026, com os dados do seed e contas criadas durante os testes.
- Os cenários de regressão foram descritos e não executados, pois dependem de correções dos bugs.

## 13. Observações adicionais

Divergências entre a documentação e o ambiente que não violam um requisito do documento e, por isso, não foram registradas como bugs:

- O README do backend informa token com validade de 7 dias, mas o ambiente usa 1 hora.
- O README do backend cita `EMAIL_PASS` com Gmail, enquanto o modelo `.env.example` usa `EMAIL_PASSWORD` com host da Hostinger. O README da raiz manda usar o Mailtrap.
- O README do backend afirma que o ranking é atualizado na aprovação do check-in, mas os pontos entram na criação (consequência do BUG-CHK-01).
- A "Temporada 2025.1" aparece como ativa na API, com data final em 30/06/2025, e a tela de ranking a exibe como encerrada. Check-ins continuam sendo aceitos.

## 14. Desafios de configuração do ambiente

| Problema | Causa | Solução |
|---|---|---|
| A instalação do Docker Desktop falhou | Recurso "Virtual Machine Platform" do Windows desativado | Habilitado pelo PowerShell como administrador (`dism`), seguido de reinício |
| O Docker informava virtualização desativada | Virtualização (SVM Mode) desligada na BIOS | Opção habilitada na BIOS |
| O sistema não subia sem configuração | O `.env` vem sem as credenciais de Cloudinary e Mailtrap | Contas próprias criadas e credenciais preenchidas |
| Erros 401 durante os testes de API | Token de acesso com validade de 1 hora | Novo login e novo token a cada sessão de testes |

## 15. Conclusão

Dos 22 casos executados, 12 falharam. Dos 11 requisitos, 7 tiveram falha (RF-01, RF-02, RF-04, RF-05, RF-06, RF-08 e RF-09), 2 passaram em todos os casos (RF-03 e RF-07) e 2 tiveram um caso aprovado e outro não executado (RNF-01 e RNF-02).

As falhas se concentram em regras de negócio: acesso sem verificação de conta, check-ins de outros usuários visíveis, status inicial incorreto e pontuação manipulável (sem limite de 24 horas, com foto repetida e sem subtração de pontos ao apagar).

**Pontos que funcionam bem:** bloqueio de Trainee em rotas de gestão pela API (403), edição de check-in alheio bloqueada (403), cadastro com e-mail repetido recusado (409), token vencido recusado (401) e ordem decrescente do ranking.

**Ordem sugerida de correção:**

1. Controle de acesso: BUG-AUTH-01 e BUG-CHK-02.
2. Integridade da pontuação: BUG-PTS-01, BUG-PTS-02 e BUG-CHK-03.
3. Fluxo de status do check-in: BUG-CHK-01.
4. Verificação de e-mail: BUG-AUTH-02.
5. Documentação da API: BUG-DOC-01.
