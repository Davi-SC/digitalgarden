---
{"dg-publish":true,"permalink":"/coleta-de-dados/","created":"2026-02-04T14:29:26.943-03:00","updated":"2026-02-24T16:10:13.375-03:00"}
---

### Meta API
##### Configuração da API com login empresarial no Instagram(Instagram API with Instagram Login):
- [X] Configuração de webhooks
- [x] Configuração de login (Paginas de redirecionamento, exclusão de dados, termos de privacidade e de uso)
- [ ] Concluir análise do app (pode ser pulado se desenvolve apenas para suas próprias empresas no Instagram e não pretende criar soluções para clientes)
- [ ] 


**Sobre pular a análise do app**: como nosso app vai servir para 'multiple businesses' (perfis não vinculados a minha conta), precisamos de Advanced Access nas funcionalidades almejadas (instagram_business_basic, instagram_business_insights, instagram_business_manage_comments). Dessa forma, não posso pular a análise do app(App Review). A não ser que :
 - O IFMA(ou outra conta alvo dos estudos) me adiciona-se como administrados no Business Manage da conta
 - Todas as contas do IFMA estiverem linkadas a um único Facebook Page/ Business Manager, onde eu possa gerenciar

**[FONTE](https://developers.facebook.com/docs/instagram-platform/app-review)**
##### Análise do APP: [mais detalhes](https://developers.facebook.com/apps/859402103106794/app-review/submissions/?submission_id=899469852433352&business_id=4153443828315892)
- [ ] Conectar portfólio empresarial (Comprar domínio para site da empresa -   
socialdatalab.dev)
- [x] Configurações do APP 
- [ ] Uso permitido
	- [ ] Como esse app usará o instagram_business_basic?
	- [ ] Como esse app usará o instagram_business_manage_messages?
	- [ ] Como esse app usará o instagram_business_content_publish?
	- [ ] Como esse app usará o instagram_business_manage_insights?
	- [ ] Como esse app usará o instagram_business_manage_comments?
	- [ ] Como esse app usará o Human Agent?
Para a revisão de permissões da meta api vamos desenvolver uma UI simples para demonstrar como usaremos as permissões, sem backend por enquanto.
1. [x] Botão de login OAuth usa o facebook login SDK
2. [x] Seção de perfil básico (Intagram_business_basic) 
3. [x] Seção insights de engajamento (instagram_business_manage_insights) 
4. [x] Seção comentários (instagram_business_manage_comments) 
5. [ ] Estado desconectado
6. [x] Estados de loading e erro

- [ ] Tratamento de dados
- [ ] Instruções da análise (possivelmente onde precisa da UI e dos vídeos)

##### Configuração da API com login empresarial no Facebook(Instagram API with Facebook Login)

### Desenvolver UI antes do App Review:

- App backend puro pode passar mas exige justificativas detalhadas e corre risco de múltiplas rejeições ([exemplo de rejeições](https://www.reddit.com/r/webdev/comments/1bsoeb3/why_is_it_so_damn_hard_to_get_approved_for_meta/))
- UI mínima e vídeo explicativo do uso do app ajudam a conseguir autorização. Não precisa ser pública inicialmente.
- [x] UI mínima
- [x] Hospedagem Feita
- [ ] 


# Próximos passos

- [ ] **Implementar OAuth Flow** 
    - Auth.py com endpoints:
        - [x] /auth/login: Gera URL de autorização (login com facebook). ✅ 2026-02-24
        - [ ] Será necessário o de login com instagram também, para aqueles que tem conta no instagram mas não são vinculadas ao facebook?
        - [x] /auth/callback: Recebe code, troca por access_token (short-lived → long-lived). ✅ 2026-02-24
        - [x] Testes em Notebook ✅ 2026-02-24
        - [ ] 
    - Criar auth_service.py para:
        - Construir URL: https://www.facebook.com/v20.0/dialog/oauth?client_id=...&redirect_uri=...&scope=instagram_basic,instagram_manage_insights,...&response_type=code
        - Troca por token: POST para https://graph.facebook.com/v20.0/oauth/access_token
        - Armazene token (em Mongo ou env para testes).
        - [ ] 
    - Escopos mínimos iniciais: instagram_basic, pages_show_list, instagram_manage_insights (para métricas).
    - Teste: Rode localmente, autorize uma conta sua (Business/Creator), pegue o token.
- **Implemente Coleta Inicial (Extract) (2-4 dias)**
    - Crie meta_repository.py com funções como:
        - get_profile(user_id, access_token) → GET /<user_id>?fields=username,biography,followers_count,follows_count,profile_picture_url,media_count
        - get_media(user_id, access_token, since/until) → GET /<user_id>/media?fields=id,caption,media_type,media_url,like_count,comments_count,impressions,reach,timestamp&limit=50
        - get_insights(media_id, access_token) → GET /<media_id>/insights?metric=impressions,reach,engagement,likes,comments,saves,plays
    - Use requests com headers Authorization: Bearer {token}.
    - Salve responses raw em JSON temporário para análise (ex.: print(json.dumps(response.json(), indent=2))).
    - Foque em 1-2 perfis IFMA (os que você gerencia ou tem autorização).
- **Analise Retorno e Planeje DB + Métricas (1-2 dias)**
    - Liste campos reais retornados (ex.: followers_count, biography, like_count, impressions deprecated em alguns casos → use views ou reach em 2025+).
    - Defina schema Mongo:
        - profiles: { profile_id, username, bio, followers_count, follows_count, profile_pic, last_updated }
        - posts: { post_id, profile_id, caption, media_type, timestamp, likes, comments, impressions/reach, saves, plays, insights_raw }
        - metrics_daily: { profile_id, date, total_posts, avg_engagement, growth_followers, ... } (agregações)
    - Métricas iniciais: ER = (likes + comments + saves + shares) / reach ou followers; distribuição por media_type (IMAGE vs CAROUSEL vs REELS).
- **Implemente Transform + Load (próxima semana)**
    - etl_service.py: chain extract → transform (limpeza datas, remoção duplicatas, cálculos) → load via mongo_repository.py.
    - Use Pandas para transformações (ex.: df.groupby('media_type')['engagement'].mean()).
- **Integre Webhook com ETL**
    - No webhook_service.py, ao receber evento (ex.: novo post), chame etl_service.partial_update(profile_id).
- **Deploy e Testes**
    - Atualize Render com novo código.
    - Monitore rate limits da API (200 calls/hora típicos).
