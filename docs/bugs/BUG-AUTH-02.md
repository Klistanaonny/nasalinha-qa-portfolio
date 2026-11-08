# BUG-AUTH-02: E-mail pós-cadastro não contém código de verificação

- **Área:** Autenticação (JWT)
- **Requisito violado:** RF-01 (Cadastro e Verificação)
- **Caso de teste:** CT-AUT-01
- **Localização:** Backend (envio de e-mail)
- **Ambiente:** localhost, Docker Compose, Mailtrap (Sandbox)
- **Data:** 07/11/2026
- **Severidade:** Maior (fluxo de ativação da conta não implementado)
- **Status:** Aberto

## Passos para reproduzir

1. Em http://localhost:3000, cadastrar um usuário novo.
2. Abrir a caixa de entrada do Mailtrap.
3. Abrir o e-mail recebido após o cadastro.

## Resultado esperado

E-mail com um código de verificação único, para o usuário ativar a conta
(status inicial PENDENTE).

## Resultado atual

O e-mail recebido é uma mensagem de boas-vindas ("Sua conta foi criada com
sucesso") com um botão "Fazer Login". Não há nenhum código de verificação.

## Consequência

Não há como ativar a conta, porque o usuário nunca recebe um código para
confirmar. Sem esse fluxo, o sistema libera o acesso sem verificação
(BUG-AUTH-01).

## Análise técnica

Pelo comportamento observado, o e-mail enviado no cadastro é o de boas-vindas e
o código de verificação não é gerado nem incluído na mensagem. A correção deve
gerar um código único no cadastro, enviá-lo por e-mail e manter a conta como
PENDENTE até a confirmação.

## Observações

- Não existe, na interface, campo para digitar um código de verificação.
- O envio de e-mails está funcionando: a mensagem chega à caixa do Mailtrap.

## Evidências

- ![E-mail recebido após o cadastro (sem código de verificação)](../evidencias/AUTH-03-email-de-confirmacao.png)
