---
{"dg-publish":true,"permalink":"/coleta-de-dados/","created":"2026-02-04T14:29:26.943-03:00","updated":"2026-03-23T15:05:21.530-03:00"}
---

### Meta API
##### Configuração da API com login empresarial no Instagram(Instagram API with Instagram Login):
- [X] Configuração de webhooks
- [x] Configuração de login (Paginas de redirecionamento, exclusão de dados, termos de privacidade e de uso)
- [x] Concluir análise do app (pode ser pulado se desenvolve apenas para suas próprias empresas no Instagram e não pretende criar soluções para clientes) 
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
	- [x] Como esse app usará o instagram_business_basic? ✅ 2026-03-09
	- [x] Como esse app usará o instagram_business_manage_messages? ✅ 2026-03-09
	- [x] Como esse app usará o instagram_business_content_publish? ✅ 2026-03-09
	- [x] Como esse app usará o instagram_business_manage_insights? ✅ 2026-03-09
	- [x] Como esse app usará o instagram_business_manage_comments? ✅ 2026-03-09
	- [ ] Como esse app usará o Human Agent? --- Não precisa
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

##  Métricas da  taxonomia, que fazem sentido para o EC

### Altamente recomendadas — disponíveis na API e com valor analítico claro

|Métrica|Grupo na planilha|Por quê vale a pena|
|---|---|---|
|**Salvamentos**|Engajamento — Interações de maior valor|Mede intenção real de revisitar o conteúdo. Mais "pesado" que um like|
|**Taxa de engajamento por alcance** (`interações / alcance`)|Índices agregados de engajamento|Normaliza o engajamento, permite comparar posts de tamanhos diferentes|
|**Crescimento de seguidores** (variação no período)|Qualidade do público|Mostra tendência: a conta está crescendo ou estagnando?|
|**Impressões vs. Alcance** (→ Frequência de exposição)|Eficiência de distribuição|`impressões / alcance` = quantas vezes a mesma pessoa viu. Acima de ~3 pode indicar saturação|
|**Views de Reels / Watch Time**|Exposição bruta|Reels têm peso enorme no algoritmo; watch time indica retenção de atenção|
|**Taxa de conclusão do vídeo**|Exposição bruta|% que assistiu até o fim — indica qualidade do conteúdo em vídeo|
|**Cliques no link da bio**|Interesse real e ações concretas|Ação de alta intenção, bem além do like|
|**Informações demográficas** (idade, gênero, localização)|Estrutura da audiência|Disponível via API; fundamental para entender _quem_ é a audiência|
|**Horário/dia de publicação** (vs. engajamento)|Cadência e contexto|Cruzar hora do post com taxa de engajamento revela os melhores horários|

---

### Interessantes — requerem cálculo/cruzamento, não são campos diretos da API

|Métrica|Como obter|
|---|---|
|**Alcance relativo** (`alcance / seguidores`)|Calcula a partir dos campos que já coleta|
|**Taxa de engajamento por seguidores**|`(likes + comentários + salvamentos) / seguidores`|
|**Engajamento por formato** (imagem × carrossel × reel)|Agrupa os posts por `media_type` e calcula a média|
|**Top posts** (por alcance ou engajamento)|Ranking dos posts coletados|
|**Cadência de publicação** (posts por semana/mês)|Conta os timestamps dos posts|

---

### Fora do escopo — não disponíveis via API Instagram Graph básica

| Métrica                                       | Motivo                                                       |
| --------------------------------------------- | ------------------------------------------------------------ |
| Sentimento de comentários (positivo/negativo) | A API não classifica — precisaria de NLP externo             |
| Share of Voice                                | Requer dados de concorrentes — não há acesso via API         |
| NPS, CSAT, Taxa de churn                      | São métricas de produto/CRM, não de redes sociais            |
| Tráfego social                                | É dado do Google Analytics, não do Instagram                 |
| Análise de DMs                                | A API de Mensagens tem permissões separadas e mais restritas |
