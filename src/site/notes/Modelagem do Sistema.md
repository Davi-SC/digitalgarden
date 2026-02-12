---
{"dg-publish":true,"permalink":"/modelagem-do-sistema/","created":"2026-02-02T15:23:09.702-03:00","updated":"2026-02-12T15:13:58.276-03:00"}
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