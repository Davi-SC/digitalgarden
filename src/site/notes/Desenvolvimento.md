---
{"dg-publish":true,"permalink":"/desenvolvimento/","created":"2026-02-12T11:18:37.528-03:00","updated":"2026-03-23T17:36:22.141-03:00"}
---

# Checklist — Backend ETL Instagram Analytics

  

## 0. Fundação — Infraestrutura base

- [x] Reorganizar estrutura de pastas (`etl/`, `dags/`, `services/`, `repositories/`)

- [x] Expandir [mongo_repository.py](file:///c:/Users/cruzd/OneDrive/%C3%81rea%20de%20Trabalho/PIBIC_TCC/projeto/backend-ig-analysis/app/repositories/mongo_repository.py) com todas as collections novas

- [x] Criar todos os índices compostos (unique, parciais) no [create_indexes()](file:///c:/Users/cruzd/OneDrive/%C3%81rea%20de%20Trabalho/PIBIC_TCC/projeto/backend-ig-analysis/app/repositories/mongo_repository.py#34-47)

- [x] Criar models Pydantic para cada collection (validação de entrada/saída)

- [x] Configurar variáveis de ambiente ([.env](file:///c:/Users/cruzd/OneDrive/%C3%81rea%20de%20Trabalho/PIBIC_TCC/projeto/backend-ig-analysis/.env)) para Airflow + Mongo + IG API

  

---

  

## 1. Extract — Serviços de coleta da API

  

### 1.1 profile_service

- [x] Buscar dados estáticos do perfil via `/me` (Graph API)

- [x] Upsert em `accounts` com campos: `profile_id`, `username`, `biography`, `website`, `account_type`

- [x] Tratar token inválido / expirado com fallback de log

  

### 1.2 media_discovery_service

- [x] Buscar lista de mídias via `/me/media`

- [x] Filtrar posts já existentes (evitar re-inserção por `post_id`)

- [x] Extrair `hashtags` do `caption` na descoberta (regex)

- [x] Inserir em `posts` (write-once, nunca atualizar)

  

### 1.3 snapshot_service

- [x] Coletar contadores do perfil (`followers_count`, `follows_count`, `media_count`)

- [x] Upsert em `account_snapshots` com índice `{ profile_id, date }` unique

- [x] Coletar contadores por post (`like_count`, `comments_count`)

- [x] Buscar `followers_at_date` do `account_snapshots` do mesmo dia (desnormalização)

- [x] Upsert em `post_snapshots` com índice `{ post_id, date }` unique

  

### 1.4 comments_service

- [x] Buscar comentários por post via `/media/{id}/comments`

- [x] Buscar replies aninhadas via `/comment/{id}/replies`

- [x] Inserir em `comments` (imutável após inserção — sem upsert no texto)

- [x] Embeber replies como array dentro do documento do comentário

  

### 1.5 insights_service — Posts

- [x] Buscar métricas de post via `/media/{id}/insights`

- [x] Campos: `reach`, `saved`, `shares`, `total_interactions`, `views`, `ig_reels_avg_watch_time`

- [x] Inserir em `post_insights` (append, não upsert — séries temporais acumuladas)

  

### 1.6 insights_service — Account (semanal)

- [x] Buscar métricas de conta via `/me/insights` com período 7 dias

- [x] Campos: `reach`, `profile_views`, `total_interactions`, `audience_country/city/age/gender`

- [x] Inserir em `account_insights` com `period_since` / `period_until`

## 2. Transform — Serviços de processamento

  

### 2.1 engagement_service

- [x] Ler `post_snapshots` e `posts` do dia/período

- [x] Calcular `er_simple` = (likes + comments) / followers

- [x] Calcular `er_weighted_post` = (shares×1.42 + likes×1.25 + comments×1.0 + saves×1.0) / followers

- [x] Calcular `er_reach_weighted_post` = (shares×1.42 + likes×1.25 + comments×1.0 + saves×1.0) / reach

- [x] Calcular `er_reach` = total_interactions / reach  ← taxa de engajamento por alcance

- [x] Calcular `er_followers` = total_interactions / followers_at_date  ← por seguidores

- [x] Calcular `relative_reach` = reach / followers_at_date  ← alcance relativo

- [x] Calcular `amplification_rate` = shares / reach  ← e-WOM / advocacia (Li et al.)

- [x] Calcular `velocity_likes_24h` = delta de likes em 24h

- [x] Calcular `velocity_comments_24h` = delta de comentários em 24h

- [x] Calcular `loyalty_rate` = page_interaction_rate / virality_rate  ← Sanches & Ramos (2025)

  - `page_interaction_rate` = unique_accounts_engaged / reach

  - `virality_rate` = total_interactions / views

- [x] Calcular `er_views` = total_interactions / views  ← Padrão Hootsuite/Dashthis (views substitui impressions)

- [x] Calcular `days_since_published`

- [x] Upsert em `engagement_metrics` com `{ post_id, date }` unique

  

### 2.2 growth_service 

- [x] Ler `profile_snapshots` consecutivos do mesmo perfil 

- [x] Calcular variação diária/semanal de `followers_count` (crescimento absoluto e %)

- [x] Calcular variação de `media_count` (frequência de publicação)

- [x] Salvar em `engagement_metrics` (por date) ou campo novo em `profile_snapshots`

  

### 2.3 sentiment_service


- [ ] **Definir qual modelo NLP iremos utilizar -> pysentimiento, vander, ou outro**

- [ ] Ler comentários sem `sentiment_score` da collection `comments` (índice sparse já existe)

- [ ] Aplicar modelo NLP 

- [ ] Atualizar documento do comentário com `sentiment_score` (float) e `sentiment_label` (pos/neg/neu)

- [ ] Agregar sentimento médio por post em `engagement_metrics` ou `post_insights`

  

### 2.4 video_metrics_service  

- [x] Ler `post_insights` com `media_type = VIDEO` e campos `ig_reels_avg_watch_time`, `ig_reels_video_view_total_time`, `views`

- [x] Calcular `watch_time_per_view` = `ig_reels_video_view_total_time / views` (proxy do avg, cross-check) 

- [x] Calcular ranking relativo de retenção entre Reels do mesmo perfil (normalizado 0–1) 

- [x] Nota: a duração do vídeo não é retornada pela Graph API — usar comparação relativa entre posts 

- [x] Salvar resultado na `engagement_metrics` do post (campo `reel_retention_score`) 

  

### 2.5 qualification_service


- [ ] **Definir como calcular o [[Score composto\|Score composto]]**

- [ ] Ler `engagement_metrics` + `profile_insights` do período

- [ ] Segmentar audiência por score composto

- [ ] Inserir/atualizar em `audience_profiles`

  

---

  

## 3. Airflow — DAGs

  

### Extract DAGs

- [ ] `dag_daily_snapshot.py` → aciona `snapshot_service` + `profile_service` todo dia às 03:00

- [ ] `dag_media_discovery.py` → aciona `media_discovery_service` todo dia às 02:00

- [ ] `dag_comments.py` → aciona `comments_service` após `dag_daily_snapshot`

- [ ] `dag_post_insights.py` → aciona `insights_service` (posts) todo dia

- [ ] `dag_account_insights.py` → aciona `insights_service` (account) semanalmente (domingo)

  

### Transform DAGs

- [ ] `dag_engagement_processing.py` → aciona `engagement_service` + `growth_service` após `dag_daily_snapshot`

- [ ] `dag_sentiment_processing.py` → aciona `sentiment_service` após `dag_comments`

- [ ] `dag_video_metrics.py` → aciona `video_metrics_service` após `dag_post_insights`

- [ ] `dag_qualification.py` → aciona `qualification_service` semanalmente

  

### Configuração Airflow

- [ ] Definir `AIRFLOW_HOME` e inicializar DB do Airflow (SQLite dev / Postgres prod)

- [ ] Criar `airflow.cfg` adaptado ao projeto

- [ ] Criar `docker-compose.yml` com Airflow + MongoDB (dev local)

- [ ] Configurar Airflow Connections: `mongo_default`, `ig_api`

  

---

  

## 4. API Layer (FastAPI)

  

### Endpoints de dados base

- [ ] Endpoint `GET /data/snapshots/{profile_id}` — série temporal de perfil

- [ ] Endpoint `GET /data/posts/{profile_id}` — posts com último snapshot

- [ ] Endpoint `GET /data/engagement/{post_id}` — `engagement_metrics` de um post

- [ ] Endpoint `GET /data/insights/account/{profile_id}` — `profile_insights`

- [ ] Proteger endpoints com token/session do OAuth já existente

  

### Endpoints analíticos  ← NOVOS (query-only, sem coleta adicional)

- [ ] Endpoint `GET /analytics/top-posts?metric=reach&limit=10` — ranking de posts por métrica

- [ ] Endpoint `GET /analytics/best-hours` — agrupa `posts.published_at` por hora do dia e calcula média de engajamento (melhor horário de publicação)

- [ ] Endpoint `GET /analytics/by-format` — engajamento médio agrupado por `media_type` (IMAGE × CAROUSEL × VIDEO) via aggregation pipeline

  

---

  

## 5. Testes & Validação

  

- [ ] Testes unitários dos cálculos de ER (`engagement_service`)

- [ ] Testes de integração dos inserts/upserts no Mongo (coleção de teste isolada)

- [ ] Teste de idempotência: rodar DAG duas vezes no mesmo dia → sem duplicatas

- [ ] Validar índices únicos ativos no Atlas

- [ ] Teste fim-a-fim com conta real (perfil de teste IG Business)