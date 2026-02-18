---
{"dg-publish":true,"permalink":"/modelagem-do-sistema/","created":"2026-02-02T15:23:09.702-03:00","updated":"2026-02-18T15:18:52.782-03:00"}
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

![Pasted image 20260218150237.png](/img/user/Pasted%20image%2020260218150237.png)

## EXTRACT
Coleta dos dados via API da meta, a partir do OAuth por login com Facebook. As entidades coletadas são:
- Dados de perfil: username, bio, foto, seguidores, seguindo
- Posts: legenda, tipo, data, contagem de likes, comentários, impressões e outros insights se disponíveis.

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
- Indexação:
	- indexar por profile_id, create_at, para facilitar consultas.
