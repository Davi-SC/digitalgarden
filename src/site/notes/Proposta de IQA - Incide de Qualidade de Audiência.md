---
{"dg-publish":true,"permalink":"/proposta-de-iqa-incide-de-qualidade-de-audiencia/","created":"2026-03-25T14:18:15.682-03:00","updated":"2026-04-14T17:16:51.833-03:00"}
---

Como eu pensei:

Para posts: Taxa de engajamento ponderada.

er_weighted_reach = (shares×1.5 + likes×1.0 + comments×2.0 + saves×2.0) / reach

 - Arman2019 propõem explicitamente comments com peso 2 e likes com peso 1 em sua proposta para ER ponderada (ER = commentsx2 + likes / follows).
 - Milne-Ives2024 sustentam que interações comportamentais(shares e saves) exigem que os usuários aplicam mais esforço cognitivo e mental.
 - Mariani et al(citados por Arman2019) propõem que as métricas de engajamento devem ser calculadas aplicando pesos percentuais exatos baseados na qualidade da interação: 50% para comentários, 30% para compartilhamentos e 20% para curtidas.
 - Saves indica intenção de revisitar. Com peso maior pela dimensão cognitiva de Milne-Ives2024, cognitivo-intencional. Comment também entra na dimensão cognitiva, cognitivo-verbal.
 - Mayol2023 define a taxa de engajamento como as interações/alcance(reach); Pencarelli2019 conceituam que o alcance representa o tamanho real da comunidade; Além disso, plataformas de mercado, como o mlabs, também sustentam que o alcance é um indicador mais forte do que o numero de seguidores; Por expressar o tamanho real da comunidade, o alcance é o denominador da nossa taxa de engajamento.
 - er_weighted_reach seria a variável alvo dos modelos de AM que testaríamos.


Para perfil

**``IQA = Σer_wighted_reach x loyalty_rate``**


Sugestão de melhoria da IQA:

**``IQA_Engajamento = Σ(er_weighted_reach × weight_i) / Σ(weight_i)``**

**``IQA = 0.75 × IQA_engagement + 0.25 × loyalty_rate_normalizado``**

- Peso de 75% para o eixo de engajamento pois reflete a centralidade do engajamento comportamental. Trunfio2021 identificam a dimensão de Contribuição como o componente de maior valor preditivo no modelo COBRA.
- Peso de 25% para taxa de lealdade, é uma dimensão complementar de fidelidade da audiência. A dimensão capturada por loyalty_rate não é coletada pelo engajamento post a post.
``
Onde:

**``weight_i = 1 / (1 + days_since_published_i / 90)``**
	-> post recente (<90 dias): weight = 1.0
	-> post com 90 dias:    weight = 0.5
	-> post com 180 dias:   weight = 0.33
	
- Posts recentes refletem o estado real da audiência

**``loyalty_rate = taxa de interação da página / taxa de viralidade``**
	-> ``Taxa de interação da pagina = contas com interação(engaged_accounts)/contas alcançadas(reach) x 100``
	-> ``Taxa de viralidade = interações(total_interactions)/impressões(views)``

- Exatamente como foi proposto por Sanches2025, so precisamos substituir impressões por views, pois a API depreciou Impressions na versão 22, mas as duas significam o mesmo.


Pela limitação dos datasets é preciso adaptar algumas coisas.

er_weighted pode ser um conceito geral e ai adaptamos ele para cada rede social com base nas referencias. 

Por exemplo: er_weighted = Σ(interação x peso) / tamanho da audiência

Para o instagram teriamos: er_weighted = (shares×1.5 + likes×1.0 + comments×2.0 + saves×2.0) / reach
E ai para as outras redes poderiamos adaptar com as métricas específicas da rede(no twitter/X por exemplo incluiriamos o retweet, provavelmente com o mesmo peso do shares para o instagram, e adaptariamos o reach para a medida de tamanho de audiência da rede).

Adaptando a abordagem assim podemos utilizar os datasets a seguir:

### Para Instagram
er_weighted_instagram = (shares×1.5 + likes×1.0 + comments×2.0 + saves×2.0) / reach
Datasets: [IG Post Analytics](https://www.kaggle.com/datasets/srisyra02/instagram-post-analytics-dataset) e [IG Analytics Dataset](https://www.kaggle.com/datasets/kundanbedmutha/instagram-analytics-dataset) Ambos contem todas as métricas necessárias, e ambos com 30k posts(sinteticos e tem que verificar se um não é a copia do outro)

### Para Tiktok
er_weighted_tiktok = (shares × 1.5 + likes × 1.0 + comments × 2.0 + downloads × 2.0) / views 
Datasets: [Tiktok User Engagement Data](https://www.kaggle.com/datasets/yakhyojon/tiktok) e [Tiktok 2025 Dataset](https://www.kaggle.com/datasets/haziqhalifi/tiktok-2025-dataset), porém , o segundo não tem *downloads*

### Para Twitter/X
er_weighted_twitter = (retweets × 1.5 + likes × 1.0 + replies × 2.0 + quotes × 1.8 ) / views
Datasets:  [Twitter Dataset February 2024](https://www.kaggle.com/datasets/fastcurious/twitter-dataset-february-2024)e [Twitter New Dataset 2024 March Data](https://www.kaggle.com/datasets/fastcurious/twitter-new-dataset-2024-march-data) contem dados completos de twites extraidos por web-scrapping

# Validação do Método:
Pesquisei algumas formas de validação para o nosso método proposto, cada uma delas é detalhada nas páginas linkadas abaixo
- [[Buckets Labels\|Buckets Labels]]
- [[Validação preditiva\|Validação preditiva]]
- [[Sensibilidade dos pesos\|Sensibilidade dos pesos]]: Validar se os pesos escolhidos estão bons , testando varias combinações e fazendo teste ANOVA com de cada combinação com os Buckets Labels.
- [[PCA\|PCA]]: Fazer a analise de componentes principais entre as métricas de engajamento básicas e verificar a correlação da componente principal com er_weighted_reach
- 