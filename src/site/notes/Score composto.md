---
{"dg-publish":true,"permalink":"/score-composto/","created":"2026-03-23T17:35:34.083-03:00","updated":"2026-03-23T23:32:45.549-03:00"}
---

### Abordagem 1: Score por Zonas de Referência (Mais Simples e Direta)

Nesta abordagem, definimos metas "ideais" de mercado para cada métrica e damos uma nota até 100 para cada pilar, aplicando pesos ao final.

1. **Pilar de Engajamento (Peso: 40%)**
    - Usamos o `er_reach` ou `er_views` do perfil na semana.
    - _Benchmark:_ Um engajamento > 5% é excelente (nota 100). Se for 2.5%, nota 50.
2. **Pilar de Aquisição/Crescimento (Peso: 30%)**
    - Usamos o `followers_growth_7d_pct` (do growth_service).
    - _Benchmark:_ Crescer 1% na semana é ótimo (nota 100). Se cresceu 0.5%, nota 50. Se perdeu seguidores, nota 0.
3. **Pilar de Retenção/Fidelidade (Peso: 30%)**
    - Usamos a `loyalty_rate` média dos posts da semana.
    - _Benchmark:_ Se a taxa for > 20% (ou o parâmetro de Sanches), nota 100.

**Cálculo Final:** `Perfil_Score = (Nota_Engajamento × 0.40) + (Nota_Crescimento × 0.30) + (Nota_Fidelidade × 0.30)`

---

### Abordagem 2: Score Relativo / Percentil

Se o objetivo é preparar o terreno para Machine Learning (como mencionado na documentação do projeto), não usamos "chutes" de mercado. Em vez disso, **comparamos os perfis ou as semanas entre si** usando um _Min-Max Scaler_ (exatamente o que fizemos no video_metrics_service).

Neste cenário, se o banco tiver dados de 3 meses:

1. Achamos a "melhor semana da história do perfil" em Engajamento. Essa ganha peso 1.0.
2. Achamos a "melhor semana" em Crescimento. Ganha peso 1.0.
3. Achamos a "melhor semana" em Fidelidade. Ganha peso 1.0.

A semana atual é uma fração dessas melhores semanas.

- Se o **Score Composto final for > 0.8**, o perfil entra na segmentação: `[A] Audiência de Alta Performance`.
- Se for **0.5 a 0.8**: `[B] Audiência Estável`.
- Se o Crescimento for alto (0.9) mas a Fidelidade for baixa (0.2): `[C] Audiência Viral/Fria` (chegou muita gente, mas ninguém fica).

### E onde entra o Sentimento?

Lembra do `sentiment_service` (2.3) que pulamos? Se/quando o implementarmos, ele adicionaria o 4º pilar: **Pilar de Qualidade (Peso: 20%)**. Afinal, um post pode ter 1.000 comentários (alto Engajamento) xingando a marca (péssimo Sentimento). O Sentimento atua como um "corretor" do score de engajamento bruto.