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

### Comandos Básicos

- `git status` → Mostra o estado atual do repositório, indicando arquivos modificados, no stage ou não rastreados.
- `git add` → Adiciona alterações ao staging area, preparando-as para o próximo commit.
- `git commit` → Salva as alterações no histórico do repositório com uma mensagem descritiva.
- `git log` → Exibe o histórico de commits, mostrando autor, data e mensagens.
- `git checkout` → Alterna entre branches ou restaura arquivos/commits para um estado anterior.
