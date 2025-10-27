# Capítulo 10 — Git Push, Fetch e Pull

## 1. Introdução

Após versionar e commitar suas alterações localmente, surge a necessidade de sincronizar seu repositório com o servidor remoto (ex: GitHub).  
Os comandos `git push`, `git fetch` e `git pull` são fundamentais para esse fluxo de colaboração e sincronização.

+-----------+   git add   +-----------+  git commit  +-----------+ git push  +---------+
| Working   | ----------> | Staging   | -----------> | Local Git | --------> | Remote  |
| Directory |             | Area      |              | Repo      |           | Repo    |
+-----------+             +-----------+              +-----------+           +---------+

      ^                        ^                        ^                        ^
      |              git merge FETCH_HEAD               |      git fetch         |
      |<------------------------------------------------|<-----------------------|
      |                              git pull (faz tudo)                         |
      |<-------------------------------------------------------------------------|

Comandos:
- git add                        → Working Dir → Staging  
    (Seleciona mudanças locais para entrarem no próximo commit)

- git commit                     → Staging → Local Repo  
    (Registra um snapshot permanente das mudanças no histórico local)

- git push                       → Local Repo → Remote Repo  
    (Envia commits locais para o repositório remoto)

- git fetch                      → Remote Repo → Local Repo (atualiza refs, sem mudar WD)  
    (Baixa as atualizações do remoto e atualiza os ponteiros, sem integrar nada ao histórico local)

- git log FETCH_HEAD      → Lista commits que estão no remoto e ainda não existem localmente  
    (Útil para revisar antes de integrar as mudanças)

- git diff FETCH_HEAD    → Mostra as diferenças de conteúdo entre local e remoto  
    (Permite ver linha a linha o que mudaria se fosse feito um merge)

- git merge FETCH_HEAD          → Integra as mudanças do remoto na branch local ativa  
    (Avança o ponteiro local — fast-forward ou cria merge commit se necessário)

- git pull                       → Faz fetch + merge/switch automaticamente  
    (Atualiza o local com as mudanças do remoto em um único comando)

## 2. `git push` — Enviando alterações para o remoto

O comando `push` envia seus commits locais para o repositório remoto.  
Normalmente é utilizado após `git add` e `git commit`.

```bash
# Envia os commits locais para o branch remoto correspondente
$ git push 

# Especificando explicitamente o remoto e o branch
$ git push origin main
```

**Observações importantes:**
- Se o branch remoto ainda não existir, o `push` irá criá-lo.
- Caso haja divergências entre o branch local e remoto, será necessário sincronizar antes de enviar novamente.

## 3. `git fetch` — Buscando alterações sem aplicar

O `fetch` baixa informações atualizadas do repositório remoto **sem mesclar automaticamente** com seu branch local.  
Serve para inspecionar mudanças antes de integrá-las.

```bash
# Baixa atualizações do remoto
$ git fetch

# Mostra os branches remotos disponíveis
$ git branch -r

# Visualizar último commit baixando no FETCH_HEAD
$ cat .git/FETCH_HEAD

# Verifica diferenças entre local e remoto
$ git log FETCH_HEAD

# Analisa as diferenças exatas entre local e remoto
$ git diff FETCH_HEAD

# Realiza merge das mudanças remotas no repositório local
$ git merge FETCH_HEAD
```

**Vantagens do fetch:**
- Você mantém controle manual sobre o momento de mesclar alterações.
- É útil para evitar conflitos inesperados durante o desenvolvimento.

Fazendo fetch e merge manual:

```bash
$ git fetch
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 2), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (3/3), 970 bytes | 194.00 KiB/s, done.
From github.com:ybraz/git-course
   2e777ad..3d834e3  main       -> origin/main

$ git status
On branch main
Your branch is behind 'origin/main' by 1 commit, and can be fast-forwarded.
  (use "git pull" to update your local branch)

nothing to commit, working tree clean

$ git log FETCH_HEAD
commit 3d834e3a6be7113d58db7365b57e54d8160115ba (origin/main, origin/HEAD)
Author: Yuri Braz <ybraz@live.com>
Date:   Sun Oct 26 05:47:26 2025 -0300

    docs: Remove redundant text from prerequisites section

$ git diff FETCH_HEAD
diff --git a/README.md b/README.md
index 643a73f..694dae8 100644
--- a/README.md
+++ b/README.md
@@ -26,7 +26,7 @@ Este material foi desenvolvido com uma abordagem progressiva: cada capítulo con
 
 - Conhecimentos básicos de terminal (bash/zsh ou equivalente)  
 - Git instalado na máquina local  
-- Conta no GitHub (ou plataforma equivalente)  
+- Conta no GitHub
 - Editor de texto ou IDE de sua preferência (ex: Neovim, VSCode)
 
 ---

$ git merge FETCH_HEAD
Updating 2e777ad..3d834e3
Fast-forward
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git status
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean

```

## 4. `git pull` — Baixando e integrando alterações

O `pull` é, na prática, um `fetch` seguido de `merge` automático.  
Ele sincroniza o branch local com o branch remoto correspondente.

```bash
# Atualiza o branch atual com o remoto
$ git pull

# Especificando explicitamente
$ git pull origin main
```

**Atenção:**  
Se houver commits divergentes, o Git tentará mesclar automaticamente. Caso não consiga, surgirão **conflitos de merge** que deverão ser resolvidos manualmente.

## 5. Ciclo típico de trabalho com repositório remoto

```bash
# 1. Atualize seu repositório local
$ git pull

# 2. Faça suas alterações
$ nvim arquivo.md

# 3. Adicione as mudanças
$ git add .

# 4. Commit
$ git commit -m "feat: adicionando conteúdo do capítulo 10"

# 5. Envie para o remoto
$ git push
```

## 6. Dicas úteis

- Use `git fetch` regularmente para acompanhar mudanças sem alterar seu código.
- Prefira `git pull --rebase` quando quiser manter o histórico linear.
- Caso ocorra erro no `push`, use `git pull` para sincronizar antes de reenviar.
- Sempre leia as mensagens de log antes de aceitar merges automáticos.

## 7. Rastreando e removendo branches remotas

Como parte do fluxo com remotos é comum criar novas branches locais e começar a compartilhá-las com o remoto, e também remover branches remotas quando o trabalho termina. Abaixo há instruções didáticas e exemplos práticos.

### Começar a rastrear (setar upstream) uma nova branch no remoto

1) Crie a branch localmente e mude para ela:

```bash
$ git checkout -b feature-x
# faça alterações, git add/commit normalmente
```

2) Envie a branch para o remoto e configure o upstream em um único comando (método mais simples):

```bash
$ git push -u origin feature-x
```

O que acontece:
- O Git cria `origin/feature-x` no servidor remoto (se não existir).
- O `-u` (ou `--set-upstream`) diz ao Git para associar a branch local `feature-x` ao branch remoto `origin/feature-x`.
- Depois disso, `git push` e `git pull` funcionam sem precisar especificar o remoto/branch.

3) Se você já tiver enviado a branch antes ou quiser apenas configurar o upstream depois de criar a branch remota, use:

```bash
$ git branch --set-upstream-to=origin/feature-x feature-x
# ou, equivalente em versões mais antigas:
$ git branch -u origin/feature-x feature-x
```

Dica: confirme o upstream configurado com `git status` ou `git rev-parse --abbrev-ref --symbolic-full-name @{u}`.

### Deletar uma branch no remoto (após deletar localmente)

Quando o trabalho numa branch terminar você pode deletá-la localmente e também removê-la do remoto para manter o repositório limpo.

1) Deletar a branch localmente (modo seguro):

```bash
$ git checkout main
$ git branch -d feature-x   # -d falha se a branch não foi merged
# se precisar forçar (perder commits), use:
$ git branch -D feature-x
```

2) Deletar a branch no remoto:

```bash
$ git push origin --delete feature-x
# alternativa equivalente (sintaxe histórica):
$ git push origin :feature-x
```

3) Limpar referências remotas locais obsoletas (opcional / bom hábito):

```bash
$ git fetch --prune
# ou automaticamente nas próximas operações de fetch:
$ git remote prune origin
```

4) Remover o ref remoto rastreado localmente (se necessário):

```bash
$ git branch -dr origin/feature-x
```

Verificações úteis:
- `git branch -r` lista branches remotos atuais.
- `git ls-remote --heads origin` mostra heads existentes no remoto.
- Após `git push --delete`, confirme com `git fetch --prune` e `git branch -r`.

Observações de segurança:
- Prefira `git branch -d` (não forçado) para evitar perder trabalho não integrado.
- Ao deletar remotamente, confirme com a equipe se necessário — a exclusão afeta todos que usam o repositório.

## 8. `git show-ref` — Listando referências locais

O comando `git show-ref` lista referências (refs) conhecidas pelo repositório local, mostrando o SHA-1/objetc id seguido do nome da ref. É útil para inspecionar quais branches, tags e refs remotas (rastreios locais) existem no repositório local.

Exemplos básicos:

```bash
# lista todas as refs conhecidas localmente (heads, tags, remotes etc.)
$ git show-ref

# lista apenas branches locais (heads)
$ git show-ref --heads

# lista apenas tags
$ git show-ref --tags

# filtrar por uma ref específica
$ git show-ref refs/heads/main
```

Consultar existência de uma ref (útil em scripts):

```bash
# retorna 0 (sucesso) se a ref existir, 1 se não existir
$ git show-ref --verify --quiet refs/heads/feature-x && echo "exists" || echo "not exists"
```

Observações e diferenças importantes:
- `git show-ref` consulta as refs que existem localmente (inclui refs em `refs/` e `packed-refs`).
- Para consultar o estado no servidor remoto (sem confiar no que há em seus refs locais), use `git ls-remote origin` — esse comando pergunta ao remoto diretamente.
- `git show-ref` é útil para scripts, depuração e para confirmar se um branch remoto já foi baixado como um ref de rastreamento (por exemplo `refs/remotes/origin/feature-x`).

Boas práticas:
- Use `git fetch --prune` para limpar refs remotos locais obsoletos antes de confiar na saída de `git show-ref` para tomadas de decisão.
- Combine `git show-ref --verify --quiet` em scripts para checagens robustas de existência de refs.

## 9. Referências

- [Pro Git Book — Capítulo sobre Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)
- `man git-push`
- `man git-fetch`
- `man git-pull`
