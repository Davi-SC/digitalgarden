---
{"dg-publish":true,"permalink":"/armazenamento/","created":"2026-02-11T19:38:39.222-03:00","updated":"2026-02-12T15:14:36.634-03:00"}
---


Pensei no armazenamento em camadas, já que teremos:
- Dados do software, como contas, perfis, etc.
- Dados brutos do Instagram, em JSON.
- Dados analíticos, que são as métricas, insights e modelos derivados dos dados brutos coletados anteriormente

Pensei em utilizar um banco **NoSQL(MongoDB)** já que é ideial para JSON nativo. Um modelo de coleções que estive pensando inclue:
- Coleção Contas (documentos estruturados):
	``` json
	  {"id": ObjectId,
	  "email":"exemplo@email.com",
	  "perfis_gerenciados":["@perfil_oficial","@franquia1","@franquia2"],
	  "data_criacao":ISODate,
	  }
	```
  - Coleção Perfis:
	``` json
	  {"id": ObjectId,
	  "username":"@perfil_oficial",
	  "dados_perfil":{JSON completo do perfil},
	  "conta_id":ref_to_contas,
	  }
	``` 
-  Coleção Posts:
	``` json
	  {"id": ObjectId,
	  "perfil_username":"@perfil_oficial",
	  "dados_post":{JSON completo dos posts},
	  "metricas_calculadas":{dados analíticos},
	  }
	``` 
- Coleção Interações:
	``` json
	  {"id": ObjectId,
	  "post_id":string (ref),
	  "tipo":string (comment),
	  "metricas_calculadas":object {...},
	  }
	``` 
Para futura escala poderíamos utilizar uma modelagem Híbrida, como SQL para os dados estruturados e NoSQL para os arquivos JSON.