---
{"dg-publish":true,"permalink":"/proposta-de-iqa-incide-de-qualidade-de-audiencia/","created":"2026-03-25T14:18:15.682-03:00","updated":"2026-03-25T18:35:22.570-03:00"}
---



Quero repensar essa análise de audiência de forma mais simples.
Primeiro, teremos um índice de qualidade de audiência(IQA) sim. Invés de cacular com base em um post_score complexo com base em Engajamento + LoyaltyProxy + SentimentModifier, quero alcom mais simples com base apenas em er_wighted_reach(ainda vou decidir os pesos com base na literatura)

Como eu pensei:

Para posts:

er_weighted_reach = (shares×1.42 + likes×1.25 + comments×2.0 + saves×1.5) / reach

 - Já vou ter que justificar essa formula, inclusive cada um dos valores de pesos e até o denominador reach

 - er_weighted_reach seria o novo "post_score", e serviria inclusive como variável alvo dos modelos de AM que testaríamos


Para perfil

IQA_Engajamento = Σ(post_score_i × weight_i) / Σ(weight_i)
IQA = (0.75 × AQS_engagement + 0.25 × loyalty_rate_normalizado)× growth_modifier

onde:

weight_i = 1 / (1 + days_since_published_i / 90)
	-> post publicado hoje: weight = 1.0
	-> post com 90 dias:    weight = 0.5
	-> post com 180 dias:   weight = 0.33

growth_modifier = 0.85 + 0.15 × growth_rate_normalizado
	-> varia de 0.85 (crescimento negativo) a 1.0 (crescimento máximo)

loyalty_rate = taxa de interação da página/taxa de viralidade
	-> Taxa de interação da pagina = contas com interação(engaged_accounts)/contas alcançadas(reach) x 100
	-> taxa de viralidade = interações(total_interactions)/impressões(views)


O que acha dessa minha proposta simplificada? Visto tudo que terei que justificar e o pouco tempo que tenho para fazer tudo(desde as coletas de dados até o treinamento/validação de AM e validação dessa classificação de audiência).