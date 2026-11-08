# Prompt — Auditoria completa (somente leitura)

Faça uma auditoria de performance deste projeto. **É SOMENTE LEITURA.** Não altere, crie ou apague arquivos; não instale pacotes; não rode migrações; não altere banco, variáveis, permissões ou produção. Antes de executar qualquer comando, mostre o comando e aguarde minha liberação. Se não puder executar comandos, leia o código e indique o que falta verificar.

1. Entenda e resuma em até 5 linhas: framework/versão, hospedagem/runtime, banco/região, como o frontend busca dados e integrações de IA/APIs externas.
2. Defina o escopo das páginas/fluxos críticos e registre baseline disponível: Lighthouse Mobile, LCP, INP, CLS, TTFB, FCP, transferência e requisições. Informe condições do teste. Não invente medições.
3. Confira os 31 itens do checklist GOAT em `references/checklist-31.md`.
4. Para cada item, devolva tabela: `# | item | status | evidência (arquivo:linha ou medição) | impacto | esforço | risco | próximo passo`. Status permitidos: OK / FALTA / NÃO SE APLICA / NÃO CONSEGUI VER.
5. Liste consultas ao banco com arquivo:linha. Destaque `select('*')`, consultas dentro de loops/map/forEach, N+1, filtros/ordenações e possíveis índices. Para cada suspeita, proponha EXPLAIN ANALYZE de consulta real, somente leitura. Não execute SQL de alteração.
6. Separe fatos observados, hipóteses e itens dependentes de acesso a painel/produção.
7. Selecione as 5 oportunidades com maior impacto e menor esforço/risco, justificando com evidências.
8. Explique por que cada item NÃO SE APLICA ao tamanho/arquitetura atual.
9. Finalize com plano recomendado em etapas e aguarde minha escolha. Não aplique nenhuma correção.

Se não conseguir ver uma informação, diga exatamente onde devo olhar e o que devo enviar. Não trate ausência de evidência como prova de que está correto.
