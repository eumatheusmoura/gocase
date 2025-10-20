# Plano de Contingência — Black Friday (Gocase)

## Objetivo

Assegurar 99,9%+ de uptime, TTFB consistente nas rotas cacheáveis e estabilidade do checkout sob carga, reduzindo a pressão na origem via cache no edge, otimização do Nuxt e proteção de APIs.

## Escopo

- **CDN/Cloudflare**: cache e segurança no edge.
- **Nuxt.js**: estratégia híbrida SSG/ISR/SSR, redução de payload e hidratação eficiente.
- **Interações com APIs**: fallback, cache de leitura e controles de latência.

## Abordagem Técnica

### Cloudflare

- **Cache Everything + stale-while-revalidate** nas rotas públicas; bypass por cookie de sessão.
- **Brotli** e **HTTP/2/HTTP/3** habilitados; **Tiered Cache** para reduzir idas à origem.
- **Otimização de imagens no edge** (Image Resizing/Polish/Mirage) para melhorar LCP.
- **WAF/Firewall** ajustados; **Rate Limiting** em `/api`, `/login`, `/checkout`; **Bot Management** com ações graduais.

### Nuxt.js

- Migrar **páginas** elegíveis para **SSG/ISR**; SSR apenas onde necessário.
- **Code splitting** e **lazy load**; **prefetch** controlado com `<NuxtLink prefetch>`.
- **Sourcemaps** desativados em produção; quando SSR for obrigatório, **cache curto de HTML** no servidor (ex.: Redis).

### APIs

- **Fallbacks** no front (skeletons, mensagens e feature flags) para lidar com latência/erros.
- **Timeouts** curtos, **retries** com backoff e **circuit breaker** em serviços instáveis.
- **Cache** de **GETs públicos** no edge/Redis e cache leve no cliente para dados pouco voláteis.

## Riscos Prioritários e Mitigações

- **Explosão de RPS no origin** → Cache agressivo no edge + Tiered Cache + Rate Limiting.
- **Degradação do SSR** → SSG/ISR onde possível + cache de HTML de curta duração.
- **APIs lentas ou intermitentes** → timeouts curtos, retries com jitter, circuit breaker e fallback visual.
- **Tráfego malicioso/scraping** → WAF ajustado, Bot Management e desafios graduais.
