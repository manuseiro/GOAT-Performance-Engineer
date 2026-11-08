# Prompts especializados GOAT

Cada prompt deve ser executado depois de uma auditoria e sempre respeitar o fluxo de plano + aprovação descrito no Skill.

## 01 — Skeleton e estados de carregamento
Encontre telas que buscam dados e mostram tela branca, spinner isolado ou conteúdo que pula quando os dados chegam. Liste arquivo:linha. Para cada tela, proponha skeleton com dimensões próximas do conteúdo final para evitar CLS. Cubra loading, vazio e erro: loading com skeleton; vazio com explicação e próximo passo; erro com mensagem clara e ação de tentar novamente. No Next App Router, avalie `loading.tsx`/Suspense; em React/Vite, use estado de carregamento existente. Faça primeiro a tela mais usada. Mostre arquivos e plano antes de alterar.

## 02 + 15 — Imagens eficientes e lazy loading
Liste imagens: arquivo, tela, formato, peso, dimensões originais e renderizadas, prioridade e se está acima/abaixo da dobra. Proponha WebP/AVIF e tamanhos responsivos. Lazy-load somente imagens fora da primeira tela; a imagem principal/LCP não deve receber lazy loading. Garanta dimensões para evitar layout shift. No Next, confira a versão instalada e API compatível (`next/image`, `preload`/`fetchPriority` conforme versão); em Vite/React, avalie `srcset` e `sizes`. Para uploads no Supabase Storage, avalie redimensionamento/transformação disponível. Apresente inventário e plano antes de mudar arquivos.

## 08 + 17 — N+1 e índices no Supabase/Postgres
Procure consultas dentro de loops, `map`, `forEach` e componentes que buscam um dado por item. Para cada caso, informe arquivo:linha e número estimado/observado de consultas numa tela com 20 itens. Proponha relação embutida, JOIN ou busca em lote (`IN`) sem comprometer RLS e autorização. Liste colunas usadas em filtros/ordenação e FKs das tabelas relevantes. Para cada índice suspeito, forneça consulta EXPLAIN ANALYZE somente leitura, plano atual se disponível e `CREATE INDEX` sugerido como proposta, nunca execute. Considere volume, seletividade, índices existentes e custo de escrita.

## 24 — Selecionar somente colunas necessárias
Localize `select('*')`, `select()` amplo ou respostas com campos não usados. Para cada consulta, identifique quais campos a tela realmente consome e proponha seleção explícita. Mova texto longo, JSON, base64 ou embedding para carregamento de detalhe se apropriado. Preserve tipos TypeScript, autorização, RLS e dados necessários. Explique como comparar tamanho da resposta no Network. Apresente plano antes de editar.

## 04 — Debounce e cancelamento de busca
Localize campos que disparam busca, filtro remoto, autocomplete ou autosave a cada tecla. Proponha debounce inicial próximo de 300 ms, ajustado à UX; cancele requisição anterior (AbortController ou mecanismo equivalente); impeça respostas antigas de sobrescreverem as novas; avalie mínimo de caracteres conforme produto. Não instale biblioteca se um hook pequeno resolver. Valide com sequência de digitação e Network: número de chamadas, cancelamentos e resultado correto.

## 13 — Streaming de resposta de IA
Localize chamadas de IA que aguardam resposta completa. Proponha streaming end-to-end compatível com SDK e arquitetura existentes (por exemplo, `streamText` no Vercel AI SDK ou `stream: true` na SDK apropriada). Mantenha chave apenas no servidor, autenticação, autorização, timeout, cancelamento, tratamento de interrupção e persistência da resposta completa ao final. Verifique formato de resposta, limites e compatibilidade. Teste se as primeiras palavras aparecem antes do término. Não altere sem aprovação.

## 03 — Deduplicação de chamadas
Mapeie chamadas duplicadas por rota/tela e identifique origem (componentes, hooks, efeitos, revalidação). Diferencie duplicação real de comportamento do StrictMode em desenvolvimento. Proponha cache/deduplicação com a solução já instalada, mantendo invalidação e isolamento por usuário/tenant. Compare contagem de requests em build de produção.

## 05 — Paginação
Identifique listas que crescem e payloads excessivos. Recomende paginação offset ou cursor/keyset conforme ordenação e volume. Preserve filtros, ordenação estável, total quando necessário e estado de navegação. Verifique limites do provedor e comportamento com novos registros. Não pagine listas pequenas sem evidência.

## 12 — Timeout em APIs externas
Mapeie integrações e SDKs. Proponha timeout proporcional, cancelamento, mensagens específicas, retry com backoff somente quando seguro e idempotência para operações de escrita/pagamento. Não repita transações financeiras sem chave idempotente.

## 16 — Paralelismo seguro
Mapeie chamadas independentes no carregamento de cada tela. Proponha paralelismo apenas onde não há dependência de dados, usando tratamento adequado para falhas parciais. Compare waterfall e tempo total.

## 21 — Scripts de terceiros
Inventarie pixels, analytics, chat, mapas e scripts externos. Meça custo em rede/main thread. Proponha carregamento posterior sem quebrar consentimento, eventos essenciais ou requisitos legais.

## 22 — Code splitting
Analise bundles/chunks e bibliotecas pesadas. Identifique módulos exclusivos de uma rota ou interação. Proponha lazy/dynamic import quando reduzir custo inicial real, sem gerar cascata de requests ou loading ruim.

## 26 — Re-renders
Use React Profiler para localizar renders caros. Identifique causa raiz (estado, contexto, props, listas) antes de memoizar. Considere React Compiler e versão do projeto. Compare responsividade e INP, não apenas quantidade de renders.

## 29 — Fontes
Liste fontes, pesos, formatos, origem e uso. Proponha hospedagem local/subsets e apenas pesos necessários. Avalie `font-display` e preload somente para fonte crítica. Respeite licenças.

## 30 — RUM
Proponha monitoramento de LCP, INP e CLS por rota e dispositivo, com versão de release e privacidade. Considere Speed Insights ou `web-vitals` conforme plataforma. Não confunda Lighthouse com experiência real.

## 19 — Região backend/banco
Identifique região real de funções/backend e banco, caminho de rede e latência. Proponha alinhamento somente após confirmar opções, impacto em usuários, residência de dados e disponibilidade.

## 06 + 09 + 27 — CDN, compressão e cache estático
Verifique o que a plataforma já fornece por headers e documentação atual. Não duplicar serviços. Para assets versionados, proponha cache longo/immutable; para HTML e dados mutáveis, política adequada. Nunca cachear conteúdo privado publicamente.

## 07 + 10 + 18 + 28 — Escala e infraestrutura
Avalie fila, Redis, tracing e múltiplas instâncias somente com métricas de volume, duração, saturação ou falha que justifiquem custo operacional. Antes, corrigir consultas, cache nativo, logs e arquitetura simples. Inclua idempotência, invalidação, estado compartilhado e custo.

## 11 — Dependências
Use análise estática e imports para encontrar pacotes não usados. Verifique scripts, plugins, configuração, geração e dependências indiretas antes de recomendar remoção. Nunca remova automaticamente.
