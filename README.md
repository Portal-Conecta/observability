# Portal Conecta Observability

Repositório de observabilidade local do Portal Conecta.

Esta base prepara a estrutura e a stack local mínima com Grafana, Loki, Prometheus, Tempo e Alloy. A stack sobe sem depender dos backends do Portal Conecta e ainda não configura coleta real dos serviços.

## Objetivo

Centralizar os arquivos de observabilidade da plataforma em um repositório dedicado, com estrutura previsível para:

- configurar a stack local de observabilidade;
- coletar logs, métricas e traces dos serviços;
- provisionar datasources e dashboards no Grafana;
- documentar o contrato para conectar novos serviços.

## Componentes planejados

| Componente | Papel |
| --- | --- |
| Grafana | Visualização de métricas, logs e traces |
| Loki | Armazenamento e consulta de logs |
| Prometheus | Armazenamento e consulta de métricas |
| Tempo | Armazenamento e consulta de traces |
| Alloy | Coleta e roteamento de telemetria local |

## Estrutura

```text
.
|- .env.example
|- .gitignore
|- README.md
|- docker-compose.yml
|- alloy
|- grafana
|  |- provisioning
|  |  |- datasources
|  |  |- dashboards
|  |- dashboards
|- loki
|- prometheus
|- tempo
|- docs
|  |- servicos-observados.md
```

## Serviços observáveis

O Hub Core (`hub`) é o primeiro serviço de referência. Os demais serviços devem seguir o mesmo contrato de observabilidade quando completarem a instrumentação necessária:

- `hub`
- `api-gateway`
- `checklist`
- `seat-map`
- `comunicados`

Consulte `docs/servicos-observados.md` para nomes oficiais, portas locais e status inicial de cada serviço.

## Evolução planejada

1. Criar a fundação do repositório com diretórios, variáveis e documentação base.
2. Adicionar containers locais de Grafana, Loki, Prometheus, Tempo e Alloy.
3. Configurar Alloy para coletar telemetria do Hub Core.
4. Provisionar datasources e dashboard inicial do Hub Core no Grafana.
5. Conectar os demais serviços conforme eles implementarem o contrato de observabilidade.

## Stack local

Execute:

```bash
docker compose config
docker compose up -d
docker compose ps
```

Verifique os logs principais:

```bash
docker compose logs grafana
docker compose logs loki
docker compose logs prometheus
docker compose logs tempo
docker compose logs alloy
```

URLs locais:

| Serviço | URL |
| --- | --- |
| Grafana | http://localhost:3000 |
| Loki | http://localhost:3100/ready |
| Prometheus | http://localhost:9090/-/ready |
| Tempo | http://localhost:3200/ready |
| Alloy | http://localhost:12345 |

O Alloy publica as portas OTLP locais `4317` e `4318` e encaminha traces para o Tempo pela rede Docker. A coleta real de logs, métricas do Hub Core e dashboards será configurada nas próximas issues.

Se a porta `3000` já estiver ocupada, altere `GRAFANA_PORT` no `.env` local antes de subir a stack.
