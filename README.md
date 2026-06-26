# Portal Conecta Observability

Repositório de observabilidade local do Portal Conecta.

Esta base prepara a estrutura para receber Grafana, Loki, Prometheus, Tempo e Alloy nas próximas issues. A fundação não sobe containers, não configura coleta e não altera nenhum backend.

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

## Validação da fundação

Execute:

```bash
docker compose config
```

A saída esperada deve conter o projeto `portal-conecta-observability`, a rede de observabilidade e os volumes nomeados. Nesta etapa, o Compose não deve definir containers.

Como ainda não existem serviços usando a rede e os volumes, algumas versões do Docker Compose podem omitir esses blocos na saída normalizada. Nesse caso, valide que `services: {}` aparece na saída e que `docker-compose.yml` declara a rede e os volumes esperados.
