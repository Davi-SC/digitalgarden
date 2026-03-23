---
{"dg-publish":true,"permalink":"/modelagem-do-sistema/","created":"2026-02-02T15:23:09.702-03:00","updated":"2026-03-23T12:58:30.693-03:00"}
---

A modelagem do método inclui a coleta e organização dos dados com o Airflow utilizando a API oficial do Meta, o backend que pré-processa os dados e contém as regras de negócios do método e o frontend(**se possível**) que exibe os dashboards e as métricas calculadas pelo método.

Meta API → Airflow → Banco de dados → Backend → Banco de dados → Frontend

Definições iniciais:
- **Requisitos Funcionais e não funcionais:** Coletar dados históricos(ou snapshots diários), calcular ER ponderado e outras métricas compostas, classificar audiência, gerar dashboards
- **Limitações técnicas**: 
	- Instagram Graph API permite acesso a dados históricos?
- **Diagrama de arquitetura**: Arquitetura em camadas
	- [Diagrama:](https://drive.google.com/file/d/173fn-RNzSVpR6tAIuejWDC6zlJXfjrwM/view?usp=drive_link) ![Pasted image 20260203153658.png](/img/user/images/Pasted%20image%2020260203153658.png)
- **Tecnologias**:
	- Backend em Python com FastAPI
	- MongoDB para banco de dados
	- Frontend com React
	- Coleta de dados automatizada com AirFlow
#### Os detalhes de Modelagem e Desenvolvimento de cada camada estão descritos nas seguintes páginas:

* [[Coleta de Dados\|Coleta de Dados]]
* [[Armazenamento\|Armazenamento]]
* [[Visualização\|Visualização]]


# ETL - Extract, Transform, Load

![Pasted image 20260218150237.png](/img/user/images/Pasted%20image%2020260218150237.png)

## EXTRACT
Coleta dos dados via API da meta, a partir do OAuth por login com Facebook. As entidades coletadas são:
- Dados de perfil: username, bio, foto, seguidores, seguindo
- Posts: legenda, tipo, data, contagem de likes, comentários, impressões e outros insights se disponíveis.
* [[Coleta de Dados\|Coleta de Dados]]
## TRANSFORM
Esta etapa deverá incluir tanto o pré-processamento quanto o cálculo de indicadores, que serão usadas na análise estatística.
#### Limpeza
- Normalizar datas
- Tratar campos ausentes
- Remover duplicatas
#### Padronização do schema
- Editar o json da api para uma estrutura consistente
#### Cálculo de métricas
- Engajamento por post
- Engajamento médio por período
- Distribuição de tipos de conteudo e desempenho relativo
#### Agregação para análise
- Séries temporais: número de posts por dia, evolução de seguidores, engajamento diário/mensal
- Métricas específicas do problema(estudo de caso): comparação entre cursos/campi do ifma

## LOAD
MongoDB para persistência dos dados, já que casa bem com o formato JSON que estamos adotando.
- Modelo de dados:
	- Coleção **profiles**: dados estáveis do perfil
	- Coleção **posts**: um documento por post
	- Coleção **métricas_daily**: análises por dia/perfil
	- Coleção **auth_tokens**: armazena os tokens de oauth, campos como profile_id, username, long-lived-token, token_type, expires_in, expires_at, auth_method(para saber o que usar no refresh), etc
- Indexação:
	- indexar por profile_id, create_at, para facilitar consultas.


CheckList Desenvolvimento

# Checklist — Backend ETL Instagram Analytics

  

## 0. Fundação — Infraestrutura base

- [x] Reorganizar estrutura de pastas (`etl/`, `dags/`, `services/`, `repositories/`) 

- [x] Expandir mongo_repository.py com todas as collections novas 

- [x] Criar todos os índices compostos (unique, parciais) no create_indexes

- [x] Criar models Pydantic para cada collection (validação de entrada/saída)

- [x] Configurar variáveis de ambiente para Airflow + Mongo + IG AP

  

---

  

## 1. Extract — Serviços de coleta da API

  

### 1.1 profile_service

- [x] Buscar dados estáticos do perfil via `/me` (Graph API) ✅ 2026-03-20

- [x] Upsert em `accounts` com campos: `profile_id`, `username`, `biography`, `website`, `account_type` ✅ 2026-03-20

- [x] Tratar token inválido / expirado com fallback de log ✅ 2026-03-20

  

### 1.2 media_discovery_service

- [x] Buscar lista de mídias via `/me/media` ✅ 2026-03-20

- [x] Filtrar posts já existentes (evitar re-inserção por `post_id`) ✅ 2026-03-20

- [x] Extrair `hashtags` do `caption` na descoberta (regex) ✅ 2026-03-20

- [x] Inserir em `posts` (write-once, nunca atualizar) ✅ 2026-03-20

  

### 1.3 snapshot_service

- [x] Coletar contadores do perfil (`followers_count`, `follows_count`, `media_count`) 

- [x] Upsert em `account_snapshots` com índice `{ profile_id, date }` unique 

- [x] Coletar contadores por post (`like_count`, `comments_count`) 

- [x] Buscar `followers_at_date` do `account_snapshots` do mesmo dia

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
  

---

  

## 2. Transform — Serviços de processamento

  

### 2.1 engagement_service

- [ ] Ler `post_snapshots` e `posts` do dia/período

- [ ] Calcular `er_simple` = (likes + comments) / followers

- [ ] Calcular `er_weighted` = (likes×1 + comments×2) / followers  ← Arman & Sidik (2019)

- [ ] Calcular `er_reach` = total_interactions / reach

- [ ] Calcular `velocity_likes_24h` = delta de likes em 24h

- [ ] Calcular `velocity_comments_24h` = delta de comentários em 24h

- [ ] Calcular `loyalty_rate` (reach recorrente / total)

- [ ] Calcular `days_since_published`

- [ ] Upsert em `engagement_metrics` com `{ post_id, date }` unique

  

### 2.2 sentiment_service

- [ ] Ler comentários sem `sentiment_score` da collection `comments`

- [ ] Aplicar modelo de NLP (ex: `transformers` BERT-pt ou `VADER` adaptado)

- [ ] Atualizar documento do comentário com campo `sentiment_score` e `sentiment_label`

  

### 2.3 qualification_service

- [ ] Ler `engagement_metrics` + `account_insights` do período

- [ ] Segmentar audiência por score composto (ER + crescimento + sentimento)

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

- [ ] `dag_engagement_processing.py` → aciona `engagement_service` após `dag_daily_snapshot`

- [ ] `dag_sentiment_processing.py` → aciona `sentiment_service` após `dag_comments`

- [ ] `dag_qualification.py` → aciona `qualification_service` semanalmente

  

### Configuração Airflow

- [ ] Definir `AIRFLOW_HOME` e inicializar DB do Airflow (SQLite dev / Postgres prod)

- [ ] Criar `airflow.cfg` adaptado ao projeto

- [ ] Criar `docker-compose.yml` com Airflow + MongoDB (dev local)

- [ ] Configurar Airflow Connections: `mongo_default`, `ig_api`

  

---

  

## 4. API Layer (FastAPI)

  

- [ ] Endpoint `GET /data/snapshots/{profile_id}` — retorna série temporal de perfil

- [ ] Endpoint `GET /data/posts/{profile_id}` — retorna posts com último snapshot

- [ ] Endpoint `GET /data/engagement/{post_id}` — retorna `engagement_metrics`

- [ ] Endpoint `GET /data/insights/account/{profile_id}` — retorna `account_insights`

- [ ] Proteger endpoints com token/session do OAuth já existente

  

---

  

## 5. Testes & Validação

  

- [ ] Testes unitários dos cálculos de ER (`engagement_service`)

- [ ] Testes de integração dos inserts/upserts no Mongo (coleção de teste isolada)

- [ ] Teste de idempotência: rodar DAG duas vezes no mesmo dia → sem duplicatas

- [ ] Validar índices únicos ativos no Atlas

- [ ] Teste fim-a-fim com conta real (perfil de teste IG Business)