# BUG-PTS-02: Sistema aceita dois check-ins seguidos do mesmo usuário

- **Área:** Sistema de Pontos
- **Requisito violado:** RF-09 (intervalo mínimo de 24 horas) e RF-04 (apenas um check-in por dia)
- **Caso de teste:** CT-PTS-06
- **Localização:** Backend (regra de criação de check-in)
- **Ambiente:** localhost, Docker Compose, NaSalinha (Node.js + PostgreSQL)
- **Data:** 07/11/2026
- **Severidade:** Maior (erro de lógica em regra de negócio)
- **Status:** Aberto

## Passos para reproduzir

1. Em http://localhost:3000, entrar com um usuário comum (no print, a conta
   naonny).
2. Fazer um check-in com uma foto.
3. Sem esperar, fazer um segundo check-in.
4. Abrir a tela inicial e o ranking e conferir os check-ins e os pontos.

## Resultado esperado

O segundo check-in é recusado, porque o mesmo usuário só pode fazer um check-in
por dia, com intervalo mínimo de 24 horas entre eles.

## Resultado atual

O segundo check-in é aceito, com a mensagem "Check-in realizado com sucesso!".
Na tela inicial, a conta naonny aparece com dois check-ins feitos às 02:13 e
às 02:14 de 07/11, ambos aprovados e com +10 pontos cada, somando 20 pontos.

## Consequência

Um usuário pode repetir check-ins sem limite e acumular pontos, o que
distorce o ranking e a competição da temporada.

## Análise técnica

Pelo comportamento observado, o endpoint de criação de check-in grava um novo
registro sem consultar o último check-in do usuário. A correção deve recusar o
check-in quando o usuário já tiver feito um nos últimos 24 horas ou no mesmo dia.

## Observações

- O mesmo comportamento foi verificado na exploração, com o usuário Pedro: o
  segundo check-in feito logo em seguida foi aceito (linha 12).

## Evidências

- ![Dois check-ins da mesma conta com um minuto de diferença, ambos aprovados](../evidencias/CHK-02-foto-repetida.png)
