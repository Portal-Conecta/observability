# Portal Conecta Observability

Repositório de observabilidade local do Portal Conecta.

Esta base prepara a estrutura e a stack local mínima com Grafana, Loki, Prometheus, Tempo e Alloy. A stack sobe sem depender dos backends do Portal Conecta e pode coletar métricas, logs e traces dos serviços quando os targets estiverem acessíveis.

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

Os serviços observados usam nomes oficiais estáveis para filtros de Prometheus, Loki e Tempo:

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
6. Evoluir dashboards por domínio quando existirem métricas de negócio além das métricas genéricas do Actuator/Micrometer.

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

O Alloy publica as portas OTLP locais `4317` e `4318`, encaminha traces para o Tempo e envia métricas dos targets configurados para o Prometheus via remote write.

Os dashboards provisionados são:

| Dashboard | Foco |
| --- | --- |
| Portal Conecta - Visao Geral | Saúde, tráfego HTTP, erros, latência, logs e traces por serviço |
| Portal Conecta - Runtime JVM / Prometheus | CPU, memória JVM, threads, GC, uptime, HTTP e saúde do scrape |

Se a porta `3000` já estiver ocupada, altere `GRAFANA_PORT` no `.env` local antes de subir a stack.

## Validar traces localmente

Suba a stack completa pelo compose da raiz do workspace, para que `core`, `api-gateway`, Alloy e Tempo compartilhem a mesma rede Docker:

```powershell
docker compose --profile observability up -d
```

Confira se Tempo e Alloy estao prontos:

```powershell
curl.exe -s http://localhost:3200/ready
curl.exe -s http://localhost:12345/-/ready
```

Execute um fluxo pelo gateway:

```powershell
curl.exe --% -i -X POST http://localhost:8081/auth/login -H "Content-Type: application/json" --data "{\"email\":\"admin@portal.test\",\"password\":\"123456\"}"
```

Aguarde alguns segundos para o batch/export e consulte o Tempo:

```powershell
curl.exe -s "http://localhost:3200/api/search?tags=service.name%3Dapi-gateway&limit=20"
curl.exe -s "http://localhost:3200/api/search?tags=service.name%3Dhub&limit=20"
```

As duas consultas devem retornar pelo menos um trace quando o request passar do gateway para o Hub Core. Se vier vazio, verifique primeiro os logs do Alloy para confirmar recebimento/exportacao OTLP:

```powershell
docker compose --profile observability logs --tail 100 alloy
docker compose --profile observability logs --tail 100 tempo
```
