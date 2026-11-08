# Checklist GOAT — 31 otimizações de performance

Use este catálogo como roteiro de investigação, não como lista de tarefas obrigatórias. A aplicabilidade depende do framework, hospedagem, arquitetura, tráfego, volume de dados e experiência desejada.

## Metas de referência — Core Web Vitals

Metas consideradas boas pelo Google, avaliadas no percentil 75 das visitas, separadamente em mobile e desktop:
- **LCP:** até 2,5 s.
- **INP:** até 200 ms.
- **CLS:** até 0,1.

São metas de referência, não garantias de resultado. Consulte a documentação atual de web.dev antes de decisões baseadas em limites ou mudanças de nomenclatura.

## Frontend

### 01 — Skeleton de carregamento
- **O que é:** placeholder com dimensões próximas ao conteúdo final enquanto dados carregam.
- **Quando vale:** telas que buscam dados e hoje exibem branco/spinner ou sofrem layout shift.
- **Quando não vale:** conteúdo já entregue no HTML ou skeleton que aumenta complexidade sem melhorar percepção.
- **Como verificar:** DevTools → Network → Slow 4G; recarregue e observe se o skeleton mantém dimensões e evita saltos.
- **Cuidados:** cobrir estados loading, vazio e erro; respeitar acessibilidade e `prefers-reduced-motion`.

### 02 — Imagens em WebP/AVIF e tamanho adequado
- **O que é:** servir formatos modernos e dimensões próximas ao tamanho exibido.
- **Quando vale:** fotos, banners e imagens de conteúdo, especialmente uploads.
- **Quando não vale:** logos/ícones que devem ser vetoriais (SVG) ou formatos com necessidade específica.
- **Como verificar:** Network → Img; conferir formato, bytes, dimensões intrínsecas e tamanho renderizado.
- **Cuidados:** manter fallback/negociação de formato; não reduzir qualidade perceptível; não servir imagem minúscula ampliada.

### 03 — Evitar chamadas duplicadas
- **O que é:** deduplicar requisições idênticas disparadas por componentes diferentes.
- **Quando vale:** praticamente sempre que a mesma tela pede o mesmo recurso repetidamente.
- **Como verificar:** Network → Fetch/XHR; comparar URL, parâmetros e payload. Confirmar em build de produção quando React StrictMode puder duplicar efeitos em desenvolvimento.
- **Cuidados:** considerar cache de dados (TanStack Query/SWR), escopo do cache, invalidação e dados específicos por usuário.

### 04 — Debounce em busca/autocomplete
- **O que é:** aguardar uma pausa curta (ex.: ~300 ms) antes de buscar após digitação.
- **Quando vale:** busca remota, autocomplete, filtros remotos e autosave.
- **Quando não vale:** filtros locais instantâneos em listas pequenas já carregadas.
- **Como verificar:** digitar uma sequência e conferir no Network se há menos chamadas; validar latência percebida.
- **Cuidados:** cancelar requisições antigas (AbortController), impedir resposta obsoleta, mínimo de caracteres apenas se adequado ao produto.

### 05 — Paginação
- **O que é:** buscar conjuntos limitados de registros por página/cursor.
- **Quando vale:** listas que crescem (pedidos, clientes, mensagens, transações).
- **Quando não vale:** listas pequenas e estáveis.
- **Como verificar:** Network → Response; verificar quantidade retornada e estratégia de paginação.
- **Cuidados:** preferir cursor/keyset em conjuntos muito grandes ou ordenação mutável; manter filtros, ordenação e consistência.

### 06 — CDN para arquivos estáticos
- **O que é:** distribuir imagens, CSS, JS e outros assets por pontos de presença próximos.
- **Quando vale:** hospedagem própria sem CDN efetiva.
- **Quando pode já estar resolvido:** Vercel, Netlify e Cloudflare normalmente oferecem distribuição de assets; confirme configuração e headers.
- **Como verificar:** headers de cache/CDN e domínio/POP de entrega.
- **Cuidados:** invalidar corretamente assets mutáveis; não adicionar CDN externa sem necessidade.

### 07 — Tarefas lentas em fila/background
- **O que é:** separar tarefas demoradas da requisição interativa.
- **Quando vale:** geração de PDFs/vídeos, processamento em lote, envio massivo, tarefas que excedem timeout.
- **Quando não vale:** tarefas pequenas, rápidas e pouco frequentes.
- **Alternativas:** recursos do framework (ex.: `after()` quando compatível) ou serviços como Inngest, Trigger.dev, QStash.
- **Como verificar:** resposta HTTP retorna rapidamente e tarefa é acompanhável; medir duração e falhas.
- **Cuidados:** idempotência, retries, dead-letter, observabilidade e consistência eventual.

### 08 — Corrigir consultas N+1
- **O que é:** uma consulta principal seguida de uma consulta adicional por registro.
- **Quando vale:** listas que carregam dados relacionados item a item.
- **Como verificar:** Network/DB logs; para 20 itens, identificar padrão de 1 + 20 consultas.
- **Alternativas:** JOIN, relações embutidas no Supabase, `IN` em lote, pré-carregamento.
- **Cuidados:** evitar JOINs gigantes, duplicação de linhas e payload excessivo; preservar autorização/RLS.

### 09 — Compressão Brotli/Gzip
- **O que é:** compactar respostas textuais (HTML, CSS, JS, JSON).
- **Quando vale:** servidor próprio sem compressão habilitada.
- **Quando pode já estar resolvido:** plataformas gerenciadas frequentemente comprimem automaticamente.
- **Como verificar:** `content-encoding: br` ou `gzip` em Response Headers.
- **Cuidados:** não comprimir formatos já comprimidos; evitar compressão redundante.

### 10 — Cache de dados consultados frequentemente (Redis/framework)
- **O que é:** reutilizar resultado de consulta cara por um período ou regra de invalidação.
- **Quando vale:** consultas caras, repetidas, com dados que toleram cache.
- **Quando não vale:** app pequeno ou dados que mudam a cada instante; primeiro corrigir query e usar cache nativo quando suficiente.
- **Como verificar:** hit/miss, latência, taxa de invalidação e consistência.
- **Cuidados:** chave por tenant/usuário/permissão, evitar vazamento cross-user, definir TTL e invalidação. Redis tem custo operacional.

### 11 — Remover dependências não utilizadas
- **O que é:** identificar pacotes/arquivos sem uso real.
- **Quando vale:** projetos com iterações frequentes e dependências acumuladas.
- **Como verificar:** ferramentas como Knip, análise do bundle e busca de imports; confirmar manualmente antes de remover.
- **Cuidados:** pacotes usados por scripts, plugins, configuração, geração ou runtime indireto podem não aparecer como import comum. Nunca remover automaticamente.

### 12 — Timeout em APIs externas
- **O que é:** limitar o tempo de espera e tratar falhas previsivelmente.
- **Quando vale:** toda chamada a serviços externos.
- **Como verificar:** revisar timeout da SDK/AbortSignal e testar indisponibilidade controlada em ambiente seguro.
- **Cuidados:** timeout diferente para IA longa, pagamento, e-mail e consulta simples; retries com backoff e idempotência; não repetir operações financeiras sem chave idempotente.

### 13 — Streaming de resposta de IA
- **O que é:** apresentar tokens/fragmentos conforme são gerados.
- **Quando vale:** aplicações que exibem texto de IA ao usuário.
- **Quando não vale:** respostas estruturadas que precisam ser validadas/processadas integralmente antes da exibição.
- **Como verificar:** primeira parte aparece antes de a geração terminar; observar stream no Network.
- **Cuidados:** chave da API somente no servidor, cancelamento, timeout, erros parciais, validação e persistência final correta.

### 14 — UI otimista
- **O que é:** atualizar interface imediatamente e reconciliar com a resposta do servidor.
- **Quando vale:** ações simples e reversíveis (like, check, edição não crítica).
- **Quando não vale:** pagamento, estoque, saldo ou ação cujo resultado depende de confirmação autoritativa.
- **Como verificar:** ação aparece imediatamente; simular falha/offline e verificar rollback e aviso.
- **Cuidados:** rollback, concorrência, idempotência e reconciliação com estado do servidor.

### 15 — Lazy loading de imagens fora da primeira tela
- **O que é:** adiar download de imagens abaixo da dobra.
- **Quando vale:** listas, feeds e páginas longas.
- **Quando não vale:** imagem principal/LCP no topo — lazy nela pode piorar LCP.
- **Como verificar:** recarregar sem rolar e observar Network; rolar e verificar carregamento próximo ao viewport.
- **Cuidados:** reservar dimensões; considerar `loading="lazy"` e `fetchpriority` conforme contexto.

### 16 — Requisições independentes em paralelo
- **O que é:** disparar ao mesmo tempo chamadas que não dependem umas das outras.
- **Quando vale:** telas que precisam de perfil, pedidos e notificações sem dependência entre si.
- **Como verificar:** waterfall no Network; comparar início/fim e tempo total.
- **Cuidados:** `Promise.all` rejeita ao primeiro erro; considerar `Promise.allSettled` quando falhas parciais forem aceitáveis. Não paralelizar dependências reais.

### 17 — Índices nas consultas frequentes
- **O que é:** índices para filtros, ordenações, joins e chaves estrangeiras relevantes.
- **Quando vale:** tabelas com volume e consultas comprovadamente custosas.
- **Quando não vale:** tabelas pequenas ou índices sem seletividade/uso.
- **Como verificar:** plano `EXPLAIN ANALYZE`, estatísticas e ferramentas do banco; procurar scans caros, não apenas `Seq Scan` isoladamente.
- **Cuidados:** índice custa armazenamento e escrita; conferir índices existentes, ordem de colunas, cardinalidade e workload. Em produção, avaliar `CREATE INDEX CONCURRENTLY`; não executar sem aprovação.

### 18 — Tracing/observabilidade de requisições (OpenTelemetry)
- **O que é:** medir etapas internas de uma requisição (DB, APIs externas, código).
- **Quando vale:** backend com múltiplas etapas e gargalos difíceis de localizar.
- **Quando não vale:** app pequeno em que logs simples com duração resolvem.
- **Como verificar:** trace distribuído e spans com duração/erro.
- **Cuidados:** custo, amostragem, dados pessoais em atributos e retenção.

### 19 — Região do servidor próxima ao banco
- **O que é:** reduzir latência de rede entre função/backend e banco.
- **Quando vale:** backend/serverless consulta banco remoto com frequência.
- **Como verificar:** região do banco e região real das funções; medir latência de round-trip.
- **Quando não vale:** frontend acessa diretamente API gerenciada do banco sem backend intermediário, ou região não pode ser alterada.
- **Cuidados:** usuários globais, residência de dados, disponibilidade e latência de outras dependências; proximidade geográfica não é a única variável.

### 20 — SSR/SSG para páginas públicas
- **O que é:** entregar HTML com conteúdo renderizado no servidor ou build.
- **Quando vale:** landing pages, conteúdo público, páginas de produto e SEO.
- **Quando não vale:** painéis privados e aplicações altamente interativas sem necessidade de indexação.
- **Como verificar:** View Source e HTML inicial; conteúdo principal deve estar presente quando esperado.
- **Cuidados:** não migrar app inteiro por causa de uma landing; avaliar cache, hidratação, dados dinâmicos e framework. Em Vite SPA, SSR pode ser migração relevante.

### 21 — Adiar scripts de terceiros
- **O que é:** carregar pixels, chat, mapas e analytics depois do conteúdo crítico.
- **Quando vale:** páginas com scripts externos que bloqueiam ou competem por rede/CPU.
- **Como verificar:** waterfall e impacto no main thread; comparar carregamento.
- **Cuidados:** consentimento, tags obrigatórias, medição e eventos que não podem ser perdidos.

### 22 — Code splitting por rota/componente
- **O que é:** carregar apenas o JavaScript necessário à página e adiar módulos pesados.
- **Quando vale:** editor, gráfico, mapa ou biblioteca pesada em telas específicas.
- **Quando não vale:** dividir componentes minúsculos ou duplicar dependências.
- **Como verificar:** bundle analyzer, Coverage, chunks e waterfall.
- **Cuidados:** evitar cascata de imports, loading ruim e divisão que aumenta requests sem reduzir custo total.

### 23 — Virtualização de listas longas
- **O que é:** renderizar somente itens visíveis/próximos ao viewport.
- **Quando vale:** centenas/milhares de linhas montadas de uma vez.
- **Quando não vale:** listas paginadas pequenas.
- **Como verificar:** DOM contém apenas janela visível e buffer, não todos os registros.
- **Cuidados:** acessibilidade, altura variável, teclado, busca, seleção e scroll restoration.

### 24 — Selecionar apenas colunas necessárias
- **O que é:** substituir `select('*')` por colunas usadas na tela.
- **Quando vale:** listagens, especialmente com JSON, texto longo, imagens/base64 ou embeddings.
- **Quando não vale:** benefício mínimo em tabelas minúsculas de poucas colunas.
- **Como verificar:** buscar `select('*')`/seleção ampla e comparar bytes da resposta.
- **Cuidados:** manter tipos, autorização e dados necessários; carregar detalhes sob demanda.

### 25 — Pool de conexões
- **O que é:** reutilizar conexões com Postgres em vez de abrir uma por requisição.
- **Quando vale:** backend conecta diretamente ao Postgres em serverless (Prisma, Drizzle, `pg`).
- **Quando não vale:** `supabase-js` usa API HTTP e não abre conexão Postgres direta no browser.
- **Como verificar:** string/driver, pooler, métricas de conexões e erros de limite.
- **Cuidados:** modo transaction pode ter limitações de prepared statements/session state; seguir documentação do provedor.

### 26 — Evitar re-renders desnecessários
- **O que é:** reduzir renders que causam lentidão em inputs, listas ou telas complexas.
- **Quando vale:** profiler demonstra trabalho excessivo e interação travada.
- **Quando não vale:** aplicar memoização em tudo por precaução.
- **Como verificar:** React DevTools Profiler e highlight updates; medir interação/INP.
- **Cuidados:** identificar causa (estado, contexto, props, componentes); React Compiler pode alterar necessidade de memoização.

### 27 — Cache-Control para estáticos
- **O que é:** permitir cache de assets imutáveis no navegador.
- **Quando vale:** arquivos versionados/hashados.
- **Quando não vale:** HTML ou dados mutáveis sem estratégia de invalidação.
- **Como verificar:** headers `cache-control`; para assets fingerprinted, política longa e `immutable` pode ser adequada.
- **Cuidados:** nunca cachear conteúdo privado/sensível publicamente; invalidar nomes de arquivo quando conteúdo muda.

### 28 — Múltiplas instâncias/load balancing
- **O que é:** distribuir tráfego entre instâncias do backend.
- **Quando vale:** servidor próprio com CPU/memória saturada ou capacidade insuficiente comprovada.
- **Quando não vale:** app pequeno ou plataformas que já escalam funções automaticamente.
- **Como verificar:** métricas de CPU/memória, concorrência, saturação e teste de carga autorizado.
- **Cuidados:** estado compartilhado, sessões, sticky sessions, autoscaling, custo e limites do banco.

### 29 — Hospedar fontes localmente e limitar pesos
- **O que é:** servir fontes do próprio domínio e baixar somente pesos/estilos usados.
- **Quando vale:** fontes customizadas.
- **Quando não vale:** fontes do sistema.
- **Como verificar:** Network → Font; domínio, quantidade, peso e carregamento.
- **Cuidados:** subset/latin, `font-display`, preload apenas da fonte crítica e licenças. Next `next/font` pode resolver parte do trabalho.

### 30 — Monitorar performance de usuários reais (RUM)
- **O que é:** coletar Core Web Vitals e métricas reais por página/dispositivo.
- **Quando vale:** quando há tráfego real suficiente.
- **Quando não vale:** antes de existir tráfego representativo.
- **Como verificar:** painel Speed Insights ou coleta `web-vitals`; segmentar por rota/dispositivo.
- **Cuidados:** consentimento/privacidade, amostragem, atribuição por versão e não confundir média com p75.

### 31 — Testar em celular e rede lenta
- **O que é:** validar a experiência em dispositivo e condições móveis realistas.
- **Quando vale:** sempre, antes e depois de mudanças relevantes.
- **Como verificar:** DevTools device emulation, Slow 4G/CPU throttling e, idealmente, celular real em rede móvel.
- **Cuidados:** repetir condições, limpar/registrar cache, testar rotas e fluxos principais, verificar acessibilidade e interação.

## Ordem inicial sugerida para apps comuns (React/Next + Supabase, Vercel/Lovable)

É apenas uma heurística; a auditoria deve confirmar:
1. 31 — medir no celular e rede lenta.
2. 19 — conferir proximidade da função/backend com o banco.
3. 24 — selecionar somente colunas necessárias.
4. 08 — corrigir N+1.
5. 17 — índices justificados por planos reais.
6. 02 + 15 — imagens leves e lazy loading fora da primeira tela.
7. 01 — skeleton e estados loading/vazio/erro.
8. 05 — paginação em listas que crescem.
9. 13 — streaming se houver geração de texto por IA.
10. 04 — debounce em buscas remotas.

Depois, avaliar 03, 12, 16, 21, 22, 29, 14, 26 e 30. Itens 07, 10, 18, 23 e 28 tendem a depender de escala ou evidência específica. Itens 06, 09 e 27 podem já estar cobertos pela hospedagem; item 25 depende de conexão direta ao Postgres.
