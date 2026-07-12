# Serviços Observados

Este documento registra os serviços previstos para a observabilidade local do Portal Conecta.

## Nomes oficiais

| Serviço | Nome oficial | Nome atual no código | Status inicial |
| --- | --- | --- | --- |
| Hub Core | `hub` | `hub` | Referência funcional inicial |
| API Gateway | `api-gateway` | `api-gateway` | Preparado parcialmente |
| Checklist | `checklist` | `checklist` | Integrado à observabilidade local |
| Mapa de Sala | `seat-map` | `seat-map` | Integrado à observabilidade local |
| Comunicados | `comunicados` | `comunicados` | Integrado à observabilidade local |

## Portas locais esperadas

| Serviço | Porta local | Observação |
| --- | --- | --- |
| `hub` | `8080` | Porta interna do container e execução direta por Maven/IDE |
| `hub` | `8082` | Porta publicada pelo compose raiz |
| `api-gateway` | `8081` | Porta publicada pelo compose raiz |
| `checklist` | `8083` | Porta publicada pelo compose raiz |
| `seat-map` | `8084` | Porta publicada pelo compose raiz |
| `comunicados` | `8085` | Porta publicada pelo compose raiz |

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

`hub`, `api-gateway`, `checklist`, `seat-map` e `comunicados` estão configurados como targets da observabilidade local.

## Dashboards

| Dashboard | Uso recomendado |
| --- | --- |
| Portal Conecta - Visao Geral | Primeira tela de diagnóstico: disponibilidade, HTTP, logs, traces e correlationId por serviço |
| Portal Conecta - Runtime JVM / Prometheus | Acompanhamento técnico das métricas genéricas do Actuator/Micrometer, como CPU, memória, threads, GC, uptime e scrape |

As variáveis `environment` e `service` são carregadas dinamicamente a partir do Prometheus usando os labels enviados pelo Alloy.
