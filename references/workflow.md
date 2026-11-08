# Fluxo operacional, segurança e validação

## Matriz de autorização

| Ação | Pode fazer durante auditoria? | Aprovação necessária |
|---|---|---|
| Ler arquivos/configuração e inspecionar código | Sim, se acesso disponível | Não |
| Consultar métricas/painéis | Somente se acesso já autorizado | Não adicional |
| Executar comando somente leitura | Mostre o comando antes se o ambiente exigir confirmação | Conforme ambiente |
| Alterar/criar/apagar arquivos | Não | Sim |
| Instalar/remover/atualizar dependência | Não | Sim, separada |
| Executar SQL de leitura/EXPLAIN | Somente se autorizado e seguro | Confirmar comando antes |
| Executar SQL de escrita/migração/índice | Não | Sim, explícita |
| Teste de carga | Não | Sim, ambiente e alvo confirmados |
| Deploy/publicação/produção | Não | Sim, explícita |
| Acessar ou expor segredo | Nunca pedir para colar | Segredos devem permanecer no gerenciador seguro |

## Modelo de auditoria

### Contexto
- Aplicação/URL:
- Framework/versão:
- Hospedagem/região:
- Banco/região:
- Caminho de dados:
- Rotas/fluxos críticos:
- Data/hora e ambiente:

### Baseline
| Métrica | Valor | Ferramenta | Condições |
|---|---:|---|---|
| LCP | | | |
| INP | | | |
| CLS | | | |
| TTFB | | | |
| FCP | | | |
| Lighthouse Performance | | | |
| Transferência total | | | |
| Requisições | | | |

### Resultado dos 31 itens
| # | Item | Status | Evidência | Impacto | Esforço | Risco | Próximo passo |
|---:|---|---|---|---|---|---|---|

### Top 5
Para cada item:
- Evidência observada:
- Sintoma/efeito:
- Correção mínima:
- Métrica de sucesso:
- Risco/regressão:
- Dependência:
- Aprovação necessária:

## Plano de correção de um item

1. Identificar item e comportamento atual.
2. Definir medida reproduzível.
3. Capturar baseline, se possível.
4. Enumerar arquivos e linhas afetados.
5. Descrever solução mínima e alternativas.
6. Listar riscos e rollback.
7. Aguardar autorização.
8. Aplicar somente a mudança aprovada.
9. Rodar build, lint, testes e typecheck disponíveis.
10. Repetir medição em condições equivalentes.
11. Comparar e recomendar manter/ajustar/reverter.

## Critérios de validação

- Mesma rota, dispositivo, viewport, navegador e throttling.
- Registrar cache frio/quente e não misturar resultados.
- Repetir testes quando houver variabilidade.
- Reportar mediana/intervalo quando fizer sentido, não escolher o melhor resultado isolado.
- Comparar métricas de laboratório com laboratório e RUM com RUM.
- Verificar funcionalidade, acessibilidade, SEO e erros de console/rede.
- Confirmar que a mudança não compromete autenticação, RLS, isolamento de tenant ou dados privados.

## Modelo de relatório antes/depois

| Indicador | Antes | Depois | Variação | Condições |
|---|---:|---:|---:|---|
| LCP | | | | |
| INP | | | | |
| CLS | | | | |
| Bytes transferidos | | | | |
| Requisições | | | | |
| Duração da rota/query | | | | |

Não calcular variação se os dados não forem comparáveis. Marcar como "não medido" quando não houver evidência.

## Regras específicas de banco

- Não inferir índice apenas porque existe `Seq Scan`; considerar cardinalidade, seletividade, tamanho e custo real.
- Revisar índice composto e ordem das colunas de acordo com filtros/ordenação reais.
- Verificar índices duplicados ou redundantes.
- Não criar índice sem plano de execução e aprovação.
- Em produção, avaliar bloqueio e estratégia de criação concorrente.
- Não expor dados pessoais ou resultados de consulta sensíveis no relatório.
- Preservar RLS, tenant scoping e filtros de autorização ao consolidar consultas.

## Regras de cache

- Identificar escopo (global, tenant, usuário, sessão).
- Definir TTL e invalidação.
- Evitar cache compartilhado de dados privados.
- Não tratar cache como substituto de query ruim.
- Testar consistência depois de escrita e troca de usuário/tenant.

## Regras de API e retry

- Definir timeout proporcional à operação.
- Não repetir operações não idempotentes sem proteção.
- Usar cancelamento quando o usuário iniciar nova busca ou sair da tela.
- Distinguir timeout, falha de rede, erro de validação e erro de servidor.
- Não registrar tokens, payloads sensíveis ou chaves.

## Critério de conclusão

Um item só é considerado concluído quando:
- mudança aprovada foi aplicada;
- testes disponíveis passaram ou falhas foram documentadas;
- medida posterior foi repetida;
- resultado e limitações foram apresentados;
- comportamento funcional foi validado ou pendências foram declaradas;
- rollback está descrito quando necessário.
