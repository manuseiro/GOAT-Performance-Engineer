# Prompt — Corrigir um item por vez com antes/depois

Vamos corrigir somente o item **[NÚMERO E NOME]** da auditoria GOAT.

1. **ANTES:** defina uma medida repetível para este item (bytes da resposta, número de requisições, duração de rota/query, EXPLAIN ANALYZE, Lighthouse Mobile etc.). Meça se tiver acesso. Se não tiver, forneça passo a passo e aguarde meus números.
2. **PLANO:** liste arquivos/linhas que pretende alterar, a mudança exata, riscos, dependências e rollback. Não modifique nada ainda.
3. Aguarde minha autorização explícita.
4. Aplique somente esse item. Não refatore áreas não relacionadas e não troque biblioteca sem perguntar.
5. Se precisar instalar/remover/atualizar pacote, executar SQL de escrita, alterar schema, mexer em segredo, rodar carga ou publicar, peça aprovação separada.
6. Rode build, lint, typecheck e testes existentes; reporte comandos e resultados.
7. **DEPOIS:** repita a mesma medida, nas mesmas condições. Mostre ANTES × DEPOIS com números reais.
8. Se não melhorou, piorou ou gerou regressão, explique e proponha rollback.
9. Informe os testes manuais específicos (tela, ação, cenário de rede/dispositivo) para validar que nada quebrou.
10. Não faça deploy nem alteração em produção sem minha autorização explícita.

Não invente resultados. Se não puder medir, deixe isso claro.
