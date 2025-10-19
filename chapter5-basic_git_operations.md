# Complete Git Course

## Chapter 5 - Basic Git Operations

### Commit

Commits tem o mesmo formato dos outros objetos Git: tipo, tamanho, delimitador (\0) e conteúdo. O conteúdo de um commit é formado por:
- Tree: Um hash que aponta para o objeto tree que representa o estado exato do diretório no momento do commit.
- Author: Nome, e-mail e timestamp da pessoa que fez a modificação original.
- Committer: Nome, e-mail e timestamp da pessoa que efetivamente aplicou o commit (num merge, por exemplo, pode ser diferente do autor).
- Mensagem: Descrição do commit
- Parent (hashes do commit pai): Em um comit inicial, não há parent. Em commits normais, há um único parent. Em merges, há dois ou mais parent.

Um commit é um registro permanente de um conjunto de mudanças no repositório Git, representando um ponto específico e versionado da história do projeto.

Para realizar commits deve haver mudanças prontas na Staging Area (precisam ter sido adicionados com `git add`):

```bash
$ git commit -m "chorus: Commit inicial apenas com arquivos de teste." 
[main (root-commit) 9cad4f5] chorus: Commit inicial apenas com arquivos de teste.
 3 files changed, 13 insertions(+)
 create mode 100644 file1.txt
 create mode 100644 file2.txt
 create mode 100644 file3.txt

$ git log
commit 9cad4f528b92ac30cf072c83b5c3c5bcf89a4bcc (HEAD -> main)
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 10:24:35 2025 -0300

    chorus: Commit inicial apenas com arquivos de teste.

$ git cat-file -p 9cad4f528b92ac30cf072c83b5c3c5bcf89a4bcc
tree 2ce4ec19223e7f1b8a460f18b7335343de7ff471
author Yuri Rodrigues Braz <ybraz@live.com> 1760880275 -0300
committer Yuri Rodrigues Braz <ybraz@live.com> 1760880275 -0300

chorus: Commit inicial apenas com arquivos de teste.

$ git cat-file -t 9cad                                    
commit

$ git cat-file -s 9cad
225

```

As duas últimas colunas nas linhas de author e committer são:
- timestamp no formato unix (tempo atual em segundos desde 1 de janeiro de 1970)
- timezone

### Boas Práticas para Escrita de Commits

- Escreva títulos curtos, claros e objetivos (até ~50 caracteres)  
- Use verbos no imperativo no título (ex.: add, fix, remove)  
- Separe título e corpo com uma linha em branco (quando houver corpo)  
- Explique o “porquê” da mudança no corpo, não apenas o “o que”  
- Use prefixos padronizados para indicar o tipo de mudança:  
  - feat: nova funcionalidade  
  - fix: correção de bug  
  - docs: mudanças em documentação  
  - style: mudanças de formatação sem impacto no código  
  - refactor: refatoração sem alterar comportamento  
  - test: alterações ou adições de testes  
  - chore: manutenção, tarefas auxiliares  
  - perf: melhorias de desempenho  
  - ci: alterações em pipelines ou configuração de integração contínua  
- Faça commits pequenos e coesos, com uma única unidade lógica de mudança  
- Referencie issues ou tickets quando aplicável  
- Evite mensagens vagas ou genéricas  
- Revise a mensagem antes de confirmar o commit

### Configurações Globais

Para configurar de forma global o autor e o e-mail na sua estação, utilize:
- `git config --global user.name <Nome>`
- `git config --global user.email <Email>`

> É uma boa prática utilizar o mesmo nome e e-mail cadastrados no GitHub.

```bash
$ git config --list | grep user
user.email=ybraz@live.com
user.name=Yuri Rodrigues Braz
```

### Comandos Básicos e Status de Rastreamento

- `git status` → Mostra o estado atual do repositório, indicando arquivos modificados, no stage ou não rastreados.
- `git add` → Adiciona alterações ao staging area, preparando-as para o próximo commit.
- `git commit` → Salva as alterações no histórico do repositório com uma mensagem descritiva.
- `git log` → Exibe o histórico de commits, mostrando autor, data e mensagens.
- `git checkout` → Alterna entre branches ou restaura arquivos/commits para um estado anterior.

No Git, um arquivo pode assumir diferentes status de rastreamento:
- **Untracked**: O arquivo existe no diretório de trabalho, mas o Git ainda não o conhece. 	Após esse comando, ele passa para “staged”. Aparece em `git status`.
- **Modified**: O arquivo rastreado sofreu alterações, mas ainda não foi adicionado ao stage. Aparece em `git status`.
- **Staged**: Arquivo rastreado cujas modificações foram adicionadas à staging area, pronto para entrar no próximo commit.
- **Unmodified**: Arquivo rastreado sem mudanças desde o último commit. Aparece em `git ls-files -s`.
- **Ignored**: Arquivos que o Git foi instruído a não rastrear, geralmente via `.gitignore`. Esses arquivos não aparecem no git status por padrão.

Quando inserimos um arquivo no Working Directory, esse arquivo automaticamente torna-se `Untracked`. Cabe a você decidir quando ele irá começar a rastrea-lo (inserir na Staging Area com `git add`.


### Retirar Arquivos da Staging Area (`git rm`) e `git restore`

O comando `git rm` remove arquivos do diretório de trabalho e do índice (Stagin Area), preparando a deleção para o próximo commit.

Quando utiliza-se o parâmetro `--cached`, remove apenas o índice (remove apenas da Stagin Area), mantendo o arquivo no Working Directory. Ou seja, paramos de rastrear o arquivo sem de fato apagá-lo.

```bash
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   teste.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   chapter5-basic_git_operations.md

$ git rm --cached teste.txt 
rm 'teste.txt'

$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   chapter5-basic_git_operations.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	teste.txt

no changes added to commit (use "git add" and/or "git commit -a")

# O arquivo estava na Stagin Area porém voltou para Untracked.

```

Já o comando `git restore` desfaz alterações do Working Directory, voltando ao estado do último commit ou do índice, caso seja ultilizado o parâmetro `--staged`. Ou seja, com esse parâmetro, o arquivo sai da Staging Area e volta a modified.

```bash
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   teste.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   chapter5-basic_git_operations.md
	modified:   teste.txt

# O arquivo já estava na Staging Area, porém com mudanças depois de ser adicionado à Stagin Area. 

$ git restore --staged teste.txt 

$ git status                    
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   chapter5-basic_git_operations.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	teste.txt

no changes added to commit (use "git add" and/or "git commit -a")

# Retornou a Untracked. Se fosse um arquivo já rastreado, voltaria a modified.

```

Com o comando `git restore` também é possível restaurar apenas um arquivo específico, sem precisar restaurar um commito completo:

```bash
$ git restore --source=<commit-hash> caminho/do/arquivo.txt

```

### Explorando Mudanças em um Repositório Git

A melhor forma de explorar mudanças em um repositório Git é utilizando `git log` para verificar os commits (snapshots) e então ir utilizando `git cat-file -p <hash>` para explorar as árvores de diretórios e arquivos incluídos naquele commit.

É possível fazer checkout em commits específicos ou apenas em arquivos específicos.

```bash
$ git log
commit 02af087b6aed5a5a3e2c27fd640afe777793c3cd (HEAD -> main)
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 17:18:03 2025 -0300

    docs: Quatro primeiros capítulos completos e o quinto capítulo parcial.

commit 9cad4f528b92ac30cf072c83b5c3c5bcf89a4bcc
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 10:24:35 2025 -0300

    chorus: Commit inicial apenas com arquivos de teste.

$ git cat-file -p 02af087b6aed5a5a3e2c27fd640afe777793c3cd
tree aba9f744c5a9862bde3e357c22d98988f3795f95
parent 9cad4f528b92ac30cf072c83b5c3c5bcf89a4bcc
author Yuri Rodrigues Braz <ybraz@live.com> 1760905083 -0300
committer Yuri Rodrigues Braz <ybraz@live.com> 1760905237 -0300

docs: Quatro primeiros capítulos completos e o quinto capítulo parcial.

$ git cat-file -p aba9f744c5a9862bde3e357c22d98988f3795f95
100644 blob 70ddeca589dcf8c633a7002f8b740701da0817bd	chapter1-introduction.md
100644 blob 76743ef544c7b5c7b092ac344b3aa72ba83ce5ce	chapter2-installation.md
100644 blob cbd4cd12dd79b8cc134bc03e8ed981fab5d73e67	chapter3-basic_shell.md
100644 blob 8be6987f670ea17ab4adab3fbd755290b6082a82	chapter4-how_git_works.md
100644 blob ca6a920dd98fa5343f6fb7c5f905e1ea636e7aa0	chapter5-basic_git_operations.md

```

