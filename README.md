# GOAT Performance Engineer — Skill Pack

O **GOAT-Performance-Engineer** é uma Skill avançada de Agente de IA projetada para atuar como um Engenheiro de Performance de elite. Ela fornece fluxos de trabalho estruturados, auditorias baseadas em checklists rígidos e rotinas de prompts especializados para identificar gargalos arquiteturais, otimizar o uso de recursos (CPU, Memória, I/O) e aplicar padrões de engenharia de software de alta eficiência em qualquer stack tecnológica.


## Conteúdo

- `SKILL.md` — instruções centrais do agente.
- `references/checklist-31.md` — catálogo completo das 31 otimizações, aplicabilidade e verificação.
- `references/workflow.md` — fluxo de auditoria/correção, segurança, autorização e validação.
- `prompts/auditoria-completa.md` — prompt de auditoria somente leitura.
- `prompts/correcao-individual.md` — prompt para corrigir um item com antes/depois.
- `prompts/prompts-especializados.md` — prompts dos itens mais relevantes e grupos adicionais.

## Instalação e uso

### ChatGPT
1. Abra a área de criação/configuração de um GPT personalizado, se disponível na sua conta.
2. Use o conteúdo de `SKILL.md` como instruções principais.
3. Envie os arquivos de `references/` e `prompts/` como conhecimento/arquivos de referência.
4. Inicie uma conversa com: **"Faça uma auditoria completa de performance deste projeto, somente leitura, seguindo o GOAT Performance Engineer."**
5. Se a interface/conta oferecer suporte a Skills ou arquivos de instrução diretamente, mantenha a estrutura de diretórios e carregue o pacote conforme o mecanismo disponível.

### Claude Code
1. Coloque a pasta `goat-performance-engineer` em `.claude/skills/` do projeto ou no diretório de skills pessoais reconhecido pela versão instalada.
2. Mantenha `SKILL.md` na raiz da pasta; preserve `references/` e `prompts/`.
3. Invoque o Skill pelo nome ou peça uma auditoria GOAT.
4. Confirme a estrutura de Skills na documentação atual do Claude Code, pois caminhos e recursos podem mudar.

### Cursor
1. Adicione `SKILL.md` como regra do projeto (por exemplo, em `.cursor/rules/` ou no mecanismo de Skills disponível na versão atual).
2. Inclua `references/` e `prompts/` no contexto do projeto ou disponibilize os arquivos no repositório.
3. Para uma instrução persistente simples, crie uma regra que diga: "Siga as instruções em `SKILL.md` e consulte as referências quando necessário."
4. Confirme a sintaxe/caminho da versão do Cursor instalada.

### Lovable
1. Abra o modo Chat/Plan antes de qualquer edição.
2. Cole o conteúdo de `prompts/auditoria-completa.md` ou forneça `SKILL.md` e as referências como contexto.
3. Reforce: auditoria somente leitura; mostrar comandos antes; nenhum arquivo/banco alterado sem aprovação.
4. Use `prompts/correcao-individual.md` apenas após selecionar um item e aprovar o plano.

## Primeira execução recomendada

Comece com auditoria completa somente leitura. Não tente corrigir todos os 31 itens. O objetivo é descobrir os gargalos reais, priorizar até cinco oportunidades e corrigir uma de cada vez.

## Limites importantes

- O pacote não executa auditorias sozinho: ele fornece instruções para um agente com acesso ao código/ambiente.
- Métricas de produção, regiões de banco, headers e RUM dependem de acesso e medição.
- Os nomes de menus e APIs variam por versão. Confirme a documentação atual antes de aplicar.
- Não compartilhe chaves, tokens ou credenciais no chat.
