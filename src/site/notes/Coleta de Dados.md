---
{"dg-publish":true,"permalink":"/coleta-de-dados/","created":"2026-02-04T14:29:26.943-03:00","updated":"2026-02-18T15:08:47.421-03:00"}
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


## Coleta histórica
Como plataformas como o mlabs conseguem os dados históricos(crescimento de número de seguidores, etc) ? Estudar se existe um endpoint na api para dados históricos.
### Airflow
