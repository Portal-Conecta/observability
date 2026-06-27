# Serviços Observados

Este documento registra os serviços previstos para a observabilidade local do Portal Conecta.

## Nomes oficiais

| Serviço | Nome oficial | Nome atual no código | Status inicial |
| --- | --- | --- | --- |
| Hub Core | `hub` | `hub` | Referência funcional inicial |
| API Gateway | `api-gateway` | `api-gateway` | Preparado parcialmente |
| Checklist | `checklist` | `conecta.checklist` | Pendente de padronização |
| Mapa de Sala | `seat-map` | `seat-map` | Preparado parcialmente |
| Comunicados | `comunicados` | `comunicados` | Preparado parcialmente |

## Portas locais esperadas

| Serviço | Porta local | Observação |
| --- | --- | --- |
| `hub` | `8080` | Execução direta por Maven/IDE |
| `hub` | `8081` | Execução via `docker-compose.all.yml` ou `docker-compose.gateway-core.yml` |
| `api-gateway` | `8090` | Execução via compose geral/gateway-core |
| `checklist` | `8082` | Execução via compose geral |
| `seat-map` | `8083` | Execução via compose geral |
| `comunicados` | `8084` | Execução via compose geral |

## Contrato mínimo esperado

Para entrar na stack local, cada serviço deve:

- expor `/actuator/health`;
- expor `/actuator/info`;
- expor `/actuator/prometheus`;
- emitir logs estruturados em JSON no stdout;
- incluir `service` com o nome oficial do serviço;
- incluir `environment`;
- propagar `X-Correlation-Id` quando aplicável;
- iniciar normalmente mesmo quando o coletor de telemetria estiver desligado.

## Próximos passos

A stack local já sobe com Grafana, Loki, Prometheus, Tempo e Alloy sem exigir que os serviços do Portal Conecta estejam rodando.

O `hub` será o primeiro target funcional da coleta. Os demais serviços devem ser ativados no Alloy e nos dashboards somente depois de completarem o contrato mínimo de observabilidade.
