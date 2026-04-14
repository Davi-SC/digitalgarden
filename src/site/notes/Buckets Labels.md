---
{"dg-publish":true,"permalink":"/buckets-labels/","created":"2026-04-14T15:25:31.633-03:00","updated":"2026-04-14T17:08:38.723-03:00"}
---

Esta página descreve a pesquisa sobre os buckets labels, que poderão ser utilizados para validar se a nossa proposta (er_weighted_reach) diferencia tão bem os labels quanto as outras taxas de engajamento. 
## 1. Percentis
Qualificar os posts a partir dos percentis de alguma métrica. Como reach por exemplo:
- <P25                = LOW
- >P25 e <P75    = MEDIUM
- >P75 e < P90   = HIGH
- >P90                 = VIRAL

Fontes:  Buscar artigos
## 2. Padrão da Industria
A industria comumente utiliza ER Post (total interactions / total followers * 100) para definir os labels, sendo:
- <1%        = LOW
- 1%-3.5% = MEDIUM
- 3.5%-6% = HIGH
- >6%       = VIRAL
Fontes: 
- https://popularpays.com/blog/understanding-social-media-engagement-key-metrics
- https://blog.hootsuite.com/calculate-engagement-rate/
- https://creatorflow.so/blog/instagram-engagement-rate/
- https://www.amplifyinfluencer.com/blog/influencer-engagement-rate

### 2.1 Ajuste pelo numero de seguidores
Algumas fontes ajustam o valor de LOW, MID, HIGH e VIRAL a partir do numero de seguidores. Não acho que cabe a nossa pesquisa nesse exato momento pois o dataset que utilizamos não tem dados de perfil, como numero de seguidores. 
Mas vale pontuar caso essa realidade mude.

# 3. Viralidade mista
No cenário ideal, com dados completos, poderíamos desenvolver um índice de viralidade misto, que considere todos os pontos acima de forma conjunta e para que um post fosse classificado como "VIRAL" utilizaríamos uma análise da velocidade em que o alcance cresceu e o volume de interações pelo tempo, não apenas baseado em taxa de engajamento estático.

# 4. Segmentação por ML
Utilizar algum algoritmo de ML para segmentação em buckets, como o Kmeans.


# Plano de validação
- Buckets da industria + teste ANOVA
- Percentil Buckets + teste ANOVA
- Kmeans + Analise de Separabilidade dos clusters + teste ANOVA
- Visualizações
