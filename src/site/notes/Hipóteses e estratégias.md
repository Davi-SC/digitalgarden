---
{"dg-publish":true,"permalink":"/hipoteses-e-estrategias/","created":"2026-03-23T17:35:34.083-03:00","updated":"2026-03-26T15:20:08.523-03:00"}
---

## H1 - Métricas compostas >>> simples

Métricas compostas de engajamento, que combinam diferentes tipos de interação com pesos diferenciados e incorporam modificadores adicionais, fornecem uma classificação mais informativa da qualidade da audiência em nível de perfil do que métricas simples baseadas apenas em contagens isoladas ou taxas unitárias.

- O Índice de Qualidade de Audiência proposto classifica melhor perfis do que métricas simples.

##### Comparação 1: Ranking Simples vs IQA
Ranking simples : ordenar perfis por followers_count ou like_count médio
Ranking IQA : ordenar perfis pelo IQA

Se um perfil com muitos seguidores aparece baixo no IQA pois tem sentimento negativo alto e Loyalty Rate baixo, essa divergência é a evidência empírica de H3
##### Comparação 2:  poder discriminativo
Se a variância entre perfis é maior no IQA então ele é mais discriminativo/informativo

##### IQA Proposto: 

IQA_engagement = Σ(post_score_i × weight_i) / Σ(weight_i)
IQA =(0.75 × AQS_engagement + 0.25 × loyalty_rate_normalizado) × growth_modifier


---
## H2 - Conteúdo, Formato e AM

O formato das postagens(imagens, carrossel, video) e o tom do conteúdo(categorias derivadas de análise de texto das legendas) apresentam associação significativa com o nível de engajamento das postagens, e modelos de aprendizado de máquina baseados nessas variáveis conseguem prever o score de engajamento das postagens (post_score) com desempenho superior a modelos de referência baseados apenas em variáveis simples.
- O formato e o tom do conetudo apresentam relação significativa com as métricas de engajamento
- Modelos de AM que utilizam o formato e o tom do conteúdo são melhores para o engajamento das postagens

**OBS**: além de formato e tom, pode ser interessante incluir outros dados de posts como hashtags, número de hashtags, horário de postagem e tamanho da legenda.

### Estratégia para comprovar
1. Variáveis:
	1. Post_score ou alguma alternativa mais simples
	2. Formato - categórica : imagem, carrossel, vídeo
	3. Tom do conteúdo: 
	4. Número de hashtags
	5. Tamanho das legendas
	6. Hora da postagem
	7. Dia da semana de publicação
2. Target do modelo
	1. er_reach, er_wighted_reach ou post_score(indice composto por vários ERs)
3. Como classificar o tom do conteúdo:
	1. **Regras + dicionário**: classificador baseado em palavras-chave
		Categorias = { 
			"institucional": ["processo seletivo", "edital", "ifma", "campus", "resultado"], 
			"informativo": ["dica", "saiba", "como fazer", "aprenda", "curiosidade"],
		}
	2. Zero-shot com modelo transformer: classificador zero-shot-classification com labels definidas(institucional, informativo, interativo, promocional)
		Mais pesado computacionalmente e pode não caber no prazo.

O modelo de AM: se for prever a métrica de engajamento escolhida (er_reach) é um problema de regressão, mas se for prever categoria de performance(alto, médio, baixo) é uma classificação. Qual escolher e porquê?

Se tiver poucos dados, acredito que a classificação seja mais robusta que regressão.

Taha2025 aponta XGboost como de alta precisão para classificação de perfis. Sendo recomendado para detecção de bots e detecção de toxicidade.

---
## H3 - Sentimento modera engajamento
O sentimento predominante nos comentários de uma postagem modera sua qualidade de engajamento - postagens com métricas de interação altas mas sentimento negativo representam engajamento de menor qualidade do que postagens com interação equivalente e sentimento positivo.
Análise de correlação entre sentiment_score e post_score. Exemplos empíricos da hipótese acontecem quando er_reach é alto mas post_score cai por causa do sentiment_modifier

