---
{"dg-publish":true,"permalink":"/proposta-de-iqa-incide-de-qualidade-de-audiencia/","created":"2026-03-25T14:18:15.682-03:00","updated":"2026-03-26T12:00:46.305-03:00"}
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

~~**``IQA = Σer_wighted_reach x loyalty_rate``**~~


Sugestão de melhoria da IA:

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

