---
title: "NeonGrid — Dashboard de Monitoramento em Tempo Real"
date: 2026-05-20
description: "Painel de telemetria em tempo real com WebSockets, Go e visualização cyberpunk."
tags: ["Go", "WebSocket", "TypeScript", "Dashboard"]
cover: ""
---

## `> cat resumo.txt`

**NeonGrid** é um dashboard de monitoramento em tempo real que coleta
métricas de múltiplos serviços e as exibe em uma interface estilizada de
sala de controle. Pensado para times de plantão que precisam enxergar o
estado do sistema num piscar de olhos.

## `> stack.json`

- **Back-end:** Go com goroutines para coleta concorrente de métricas
- **Transporte:** WebSockets para streaming sub-segundo
- **Front-end:** TypeScript + Canvas para os gráficos animados
- **Infra:** Docker Compose, Prometheus como fonte de dados

## `> features[]`

1. Streaming de métricas com latência abaixo de 200ms
2. Alertas visuais com efeito de glitch quando um limite é ultrapassado
3. Tema escuro neon de alto contraste, legível em ambientes 24/7
4. Histórico de 24h armazenado em memória com snapshot periódico

## `> aprendizados`

O maior desafio foi manter o front-end fluido com centenas de pontos
chegando por segundo. A solução foi agrupar atualizações em *frames* e
desenhar tudo no `requestAnimationFrame`, evitando reflows do DOM.

```go
// coletor concorrente — um worker por serviço monitorado
func (g *Grid) Collect(ctx context.Context) {
    for _, svc := range g.services {
        go func(s Service) {
            ticker := time.NewTicker(500 * time.Millisecond)
            defer ticker.Stop()
            for {
                select {
                case <-ctx.Done():
                    return
                case <-ticker.C:
                    g.broadcast(s.Sample())
                }
            }
        }(svc)
    }
}
```

> **Repositório:** [github.com/patrickcavalcantte/neongrid](https://github.com/patrickcavalcantte/neongrid) · **Demo:** em breve
