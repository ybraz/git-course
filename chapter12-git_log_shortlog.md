# Git Log e Git Shortlog

Nesta aula você vai:
- Explorar o histórico de commits com filtros e formatações úteis
- Gerar resumos por autor e por intervalo com `git shortlog`
- Usar combinações práticas para investigações do dia a dia

---
O `git log` é a ferramenta mais completa para **investigar o histórico de commits**.

Já o `git shortlog` é sua forma condensada — ideal para **resumos, estatísticas e changelogs**.

Este guia é um **passo a passo completo**, mostrando filtros, formatos e truques avançados para navegar o tempo no Git.

---
## 1. Introdução rápida

`git log` navega e filtra o histórico de commits; `git shortlog` resume por autor/intervalo — use-os para investigar e gerar resumos práticos.


## 2. O comando básico

```bash
git log
```

Ver o histórico em ordem reversa (mais recente primeiro):
- hash completo (commit SHA)
- data
- mensagem

### Mostrar apenas os últimos commits

```bash
git log -n 5
```

ou:

```bash
git log --max-count=5
```

---

## 3. Filtros

### Filtrar por autor

```bash
git log --author="Yuri"
git log --author=".*yuri.*" --ignore-case
```

### Filtrar por data

```bash
git log --since="2024-01-01"
git log --until="2024-12-31"
git log --since="2 weeks ago"
git log --since="last month" --until="yesterday"
```

### Filtrar por arquivo ou diretório

```bash
git log -- <arquivo.py>
git log -- src/
```

Exibe apenas commits que tocaram aquele arquivo ou diretório.

### Filtrar por mensagem

```bash
git log --grep="hotfix"
git log --grep="bug|fix" --extended-regexp
```

### Filtrar por conteúdo (diferença)

```bash
git log -S"def main"
git log -G"regex"
```

- `-S` busca mudanças que **adicionam ou removem** a string literal.
- `-G` busca commits que modificaram linhas **casando com expressão regular**.

### Filtrar por intervalo de commits

```bash
git log HEAD~10..HEAD
git log v1.0.0..v2.0.0
git log v1.0.0..
```

# commits após v1.0.0
Exemplo: listar commits após v1.0.0
### One-line

- `man git-log`
- `man git-shortlog`
- Pro Git Book — capítulo de histórico
```bash
git log --oneline
```

Exemplo de saída:

```
a3f2b1e Corrige bug no parser
91d2a10 Adiciona logging detalhado
```

### Mostrar apenas merges

```bash
git log --merges
```

### Mostrar commits normais (sem merge)

```bash
git log --no-merges
```

### Mostrar estatísticas de diffs

```bash
git log --stat
```

Mostra, para cada commit, quais arquivos foram alterados e quantas linhas mudaram.

### Mostrar o patch completo (conteúdo das mudanças)

```bash
git log -p
git log -p -- <arquivo>
```

### Visualizar o grafo

```bash
git log --oneline --graph --all --decorate
```

- `--decorate`: mostra nomes de branches e tags.

### Formato personalizado

```bash
git log --pretty=full
git log --pretty=format:"%h | %an | %ar | %s"
```

Outras opções:
- `oneline`
- `full`
- `raw`

Campos mais úteis:
- `%H`: hash completo
- `%ae`: e-mail do autor
- `%ar`: tempo relativo (ex: 2 days ago)
- `%ad`: data formatada
- `%b`: corpo da mensagem

Exemplo:

```bash
git log --pretty=format:"%C(yellow)%h %Cgreen%an %Creset%s"
```

---

## 5. Git Shortlog — resumo por autor, versão ou tag

### Por padrão (agrupado por autor)

```bash
git shortlog
```

Mostra cada autor seguido de suas mensagens de commit agrupadas.

### Contagem simples de commits

```bash
git shortlog -s
```

### Ordenar por número de commits

```bash
git shortlog -s -n
```

Saída:

```
58  Yuri de Carvalho
12  Ana Torres
```

### Filtrar por intervalo (ex: changelog entre versões)

```bash
git shortlog v1.0.0..v2.0.0
```

### Apenas para uma branch específica

```bash
git shortlog main
```

### Formatar para changelog

```bash
git shortlog --summary --numbered --email v1.0.0..HEAD
```

---

## 6. Combinações úteis no dia a dia

**Ver commits de um autor desde ontem:**

```bash
git log --author="Yuri" --since="yesterday" --oneline
```

**Ver commits que modificaram determinado arquivo recentemente:**

```bash
git log -p --follow -- app/config.py
```

(`--follow` mantém histórico mesmo após renomeação do arquivo)

**Ver commits que adicionaram uma palavra específica:**

```bash
git log -S"senha" --source --oneline
```

**Ver commits com uma palavra no título (sem precisar de diff):**

```bash
git log --grep="security" --oneline
```

**Resumo de commits de todos os autores desde última tag:**

```bash
git shortlog v1.2.0..HEAD -s -n
```

---

## 7. Inspeções avançadas e truques

**Ver log entre dois autores:**

```bash
git log --author="Yuri" --author="João" --oneline
```

**Ver commits que tocam múltiplos arquivos específicos:**

```bash
git log -- <file1> <file2>
```

**Ver histórico com datas formatadas:**

```bash
git log --date=short --pretty=format:"%ad | %h | %s"
```

**Ver estatísticas agregadas:**

```bash
git log --numstat --pretty="%H" | awk '{ add += $1 - $2 } END { print add }'
```

(Conta número líquido de linhas adicionadas/removidas)

**Ver número total de commits desde o início:**

```bash
git rev-list --count HEAD
```

---

## 8. Filosofia final

O `log` é o diário do projeto; o `shortlog`, seu sumário.
Ler um `log` limpo é como ler uma boa história: cada commit tem um propósito claro, uma sequência lógica e uma autoria reconhecível.

Se o repositório é um corpo vivo,
o `log` é a memória,
e o `.gitignore` é o esquecimento necessário.

Um desenvolvedor maduro não apenas escreve código — ele **cultiva um histórico legível**,
pois sabe que o futuro precisará entender o caminho que o trouxe até aqui.

---
