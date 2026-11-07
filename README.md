# Auditoria de Qualidade: NaSalinha

Portfólio de QA da trilha de Quality Assurance da Comp Júnior.
Responsável: Klistanaonny Amábille Diniz Lucchesi

## Sobre o projeto

O NaSalinha é um sistema de check-in gamificado: usuários fazem check-in
com foto, ganham pontos e aparecem em um ranking. Este repositório reúne
a auditoria de qualidade feita sobre ele: planejamento de testes,
execução, bugs encontrados e relatório final.

Projeto testado: https://github.com/Gustavohmmagalhaes/nasalinha-qa-challenge

## Áreas testadas

1. Autenticação JWT: cadastro, verificação de e-mail, login e níveis de acesso
2. Check-in por Foto: upload, status e restrições de propriedade
3. Sistema de Pontos: atribuição, saldo e ranking

## Ferramentas

- Docker e Docker Compose: ambiente local
- Navegador (DevTools): testes de interface
- Insomnia: testes de API
- Mailtrap: caixa de e-mails de teste
- Cloudinary: armazenamento das fotos
- GitHub: documentação

## Estratégia de testes

1. Partir do documento de requisitos (11 requisitos nas 3 áreas).
2. Explorar o sistema para entender o fluxo do usuário.
3. Escrever casos de teste positivos e negativos, de interface e de API.
4. Executar os casos e registrar o resultado (Pass/Fail).
5. Documentar cada falha em um bug report, com evidência.
6. Descrever um cenário de regressão para cada área.
7. Consolidar as métricas no relatório final.

## Tipos de testes

- Funcional (interface)
- Validação de API (status codes e regras de negócio)
- Regressão

## Estrutura do repositório

- `docs/`: exploração, casos de teste e relatório final
- `docs/bugs/`: um arquivo por bug reportado
- `docs/evidencias/`: prints e logs
- `api-collection/`: coleção exportada do Insomnia

## Como rodar o projeto testado

1. Instalar Docker e Git
2. Clonar o repositório do projeto
3. Copiar `backend/.env.example` para `backend/.env` e preencher as chaves
   do Cloudinary e do Mailtrap
4. Na raiz do projeto: `docker compose up --build`
5. Criar tabelas e dados iniciais:
   - `docker compose exec backend npx prisma migrate dev --name init`
   - `docker compose exec backend npx prisma db seed`
6. Acessar http://localhost:3000

## Status

Em andamento.
