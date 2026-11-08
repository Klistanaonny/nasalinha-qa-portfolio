# BUG-CHK-03: Sistema aceita a mesma foto em check-ins repetidos

- **Área:** Check-in por Foto
- **Requisito violado:** RF-04 (restringir o envio da mesma foto repetidas vezes)
- **Caso de teste:** CT-CHK-01
- **Localização:** Backend (validação do upload)
- **Ambiente:** localhost, Docker Compose, NaSalinha (Node.js + PostgreSQL)
- **Data:** 06/11/2026
- **Severidade:** Maior (regra de negócio não aplicada)
- **Status:** Aberto

## Passos para reproduzir

1. Em http://localhost:3000, entrar com um usuário (no print, a conta naonny).
2. Fazer um check-in com a foto A.
3. Fazer outro check-in com a mesma foto A.

## Resultado esperado

O segundo envio é recusado, porque o sistema deve restringir o envio da mesma
foto repetidas vezes.

## Resultado atual

O check-in com a foto repetida é aceito normalmente e aparece na lista junto
com o primeiro.

## Consequência

Um usuário pode reaproveitar a mesma imagem para pontuar, sem comprovar uma
nova atividade. Isso enfraquece a validação de mídia e distorce o ranking.

## Análise técnica

Pelo comportamento observado, o upload é aceito sem comparar a imagem com os
envios anteriores. A correção deve rejeitar o arquivo repetido, por exemplo
comparando o hash da imagem com o das fotos já enviadas, e devolver uma
mensagem de erro clara.

## Observações

- O mesmo comportamento apareceu na exploração (06/11, linha 13), com usuários
  diferentes: a foto A enviada por um usuário foi aceita de novo no check-in de
  outro.
- Nesse mesmo teste, os dois check-ins foram feitos com um minuto de diferença,
  o que também viola o intervalo mínimo de 24 horas (BUG-PTS-02).

## Evidências

- ![Check-ins com a mesma foto aceitos](../evidencias/CHK-02-foto-repetida.png)
