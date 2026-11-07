# BUG-AUTH-01: Login liberado para conta que não verificou o e-mail

- **Área:** Autenticação (JWT)
- **Requisito violado:** RF-02 (Restrição de Login)
- **Caso de teste:** CT-AUT-03 (interface) e CT-AUT-04 (API)
- **Localização:** Backend / API (`POST /api/auth/login`)
- **Ambiente:** localhost, Docker Compose, NaSalinha (Node.js + PostgreSQL)
- **Data:** 07/11/2026
- **Severidade:** Maior (erro de lógica em regra de acesso)

## Passos para reproduzir (API)

1. Enviar `POST http://localhost:5001/api/auth/register` com um e-mail novo
   (ex.: teste3@example.com) e uma senha.
2. Não abrir o e-mail de verificação nem confirmar o código.
3. Enviar `POST http://localhost:5001/api/auth/login` com o mesmo e-mail e senha.

## Passos para reproduzir (interface)

1. Em http://localhost:3000, cadastrar um usuário novo.
2. Sem verificar o e-mail, tentar entrar com ele.

## Resultado esperado

Login bloqueado, com a mensagem "Por favor, verifique sua conta para continuar"
e nenhum token na resposta (status 401 ou 403).

## Resultado atual

- API: status 200 OK com a mensagem "Login realizado com sucesso" e com
  `accessToken` e `refreshToken` na resposta.
- Interface: o usuário entra no sistema normalmente, sem verificar a conta.
- Exemplo: o check-in 33307ed3-b831-4036-bd75-046ef9794858, criado pelo
  testador pela interface, tem `status: APPROVED`, `points: 10` e
  `createdAt` igual a `updatedAt`.

## Análise de causa raiz (hipótese)

O endpoint de login parece não consultar o status da conta (PENDENTE) antes de
emitir os tokens. Hipótese a confirmar no código do controller/service de
autenticação, que não foi lido nesta auditoria.

## Observações

- O e-mail recebido após o cadastro é uma mensagem de boas-vindas e não contém
  código de verificação (registrado em BUG-AUTH-02).
- Relacionado: o cadastro (`POST /api/auth/register`) também já devolve tokens
  (será registrado como bug separado).

## Evidências

- ![Login sem verificar na interface](../evidencias/AUTH-01-login-sem-verificar.png)
- ![Login direto após cadastro](../evidencias/AUTH-02-login-por-email-direto.png)
- ![E-mail de verificação no Mailtrap](../evidencias/AUTH-03-email-de-confirmacao.png)
- ![Resposta da API com tokens](../evidencias/BUG-AUTH-01-api-login-sem-verificar.png)
