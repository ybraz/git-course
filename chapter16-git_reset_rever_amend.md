# Chapter 16 - Git Reset, Git Revert and Git Amend

Git é uma ferramenta que trata o histórico como uma estrutura viva. 

Esses três comandos — `reset`, `revert` e `commit --amend` — são instrumentos de **cirurgia temporal**:  
cada um altera o histórico de modo diferente, com graus variados de risco e propósito.

---

## 1. Git Reset — mover o ponteiro do tempo

`git reset` **move o ponteiro HEAD** (e opcionalmente o index e a árvore de trabalho) para um commit anterior.  
É o comando mais poderoso — e potencialmente destrutivo — entre os três.

### Quando usar
- Reorganizar commits locais antes de publicar
- Desfazer commits recentes mantendo (soft) ou descartando (hard) mudanças

### Modos do reset

1. **Soft reset**
   Mantém tudo no *staging area* (index).  
   Apenas move o ponteiro HEAD.

   ```bash
   git reset --soft HEAD~1
   ```

   Uso: “quero refazer o último commit, mas manter as mudanças preparadas”.

2. **Mixed reset (padrão)**
   Move HEAD e o index.  
   As mudanças voltam a ser *untracked* no diretório de trabalho.

   ```bash
   git reset HEAD~1
   ```

   Uso: “quero desfazer o commit e reavaliar o que adicionar novamente”.

3. **Hard reset**
   Move HEAD, index e diretório de trabalho.  
   Desfaz **todas** as alterações — perigoso.

   ```bash
   git reset --hard HEAD~1
   ```

   Uso: “quero apagar tudo e voltar a um estado anterior”.

### Níveis de impacto
- Soft → apenas histórico
- Mixed → histórico + staging
- Hard → histórico + staging + diretório

### Verificação
Ver o commit atual:

```bash
git log --oneline -n 5
```

Ver o HEAD:

```bash
git rev-parse HEAD
```

Ver o hash do commit anterior (para possíveis restaurações):

```bash
git reflog
```

Se se arrepender de um reset, o `reflog` é o caminho de volta:

```bash
git checkout <sha-anterior>
```

---

## 2. Git Revert — desfazer sem reescrever

`git revert` **cria um novo commit que inverte outro**.  
Não apaga o passado — adiciona um registro que o contradiz.

### Quando usar
- Desfazer mudanças já publicadas mantendo um histórico auditável
- Reverter merges problemáticos com `-m`

### Uso básico
```bash
git revert <sha-do-commit>
```

O Git cria automaticamente um novo commit com as alterações opostas.  
O histórico fica assim:

    A---B---C---D
            ↑
          revert(C)

O commit `revert(C)` aplica o inverso de `C`, mantendo tudo rastreável.

### Exemplos

Reverter o último commit:
```bash
git revert HEAD
```

Reverter múltiplos commits:
```bash
git revert HEAD~2..HEAD
```

Desfazer um merge (mais avançado):
```bash
git revert -m 1 <sha-do-merge>
```

O `-m 1` indica qual “pai” do merge será considerado a base.  
Isso evita que o Git tente aplicar o conteúdo de ambas as linhas de desenvolvimento novamente.

### Vantagem ética
`revert` preserva o histórico.  
Por isso, é **seguro em branches públicas** ou já compartilhadas.

---

## 3. Git Commit --amend — correção imediata

`git commit --amend` altera o commit **mais recente**.  
Serve para ajustar mensagens, incluir arquivos esquecidos ou pequenas correções.

### Quando usar
- Ajustar a mensagem do último commit
- Incluir arquivos esquecidos no commit mais recente

### Uso

Adicionar um arquivo esquecido ao último commit:
```bash
git add arquivo_faltando.py
git commit --amend
```

Editar apenas a mensagem:
```bash
git commit --amend -m "Mensagem corrigida"
```

Internamente, o Git cria **um novo commit** com novo hash, substituindo o anterior.  
O antigo ainda existe, mas é perdido no histórico normal (pode ser recuperado via `reflog`).

### Atenção
Nunca use `--amend` após já ter feito `push`.  
Isso reescreve o commit remoto, causando divergência para quem já o baixou.

---

## 4. Comparação conceitual

| Comando | O que faz | Altera histórico? | Risco em branch pública | Filosofia |
|----------|------------|-------------------|--------------------------|------------|
| `reset` | Move HEAD e pode apagar commits | Sim | Alto | Reescrever a história |
| `revert` | Cria commit inverso | Não | Nenhum | Corrigir sem apagar |
| `amend` | Reescreve último commit | Sim (último) | Médio | Aperfeiçoar o recente |

*(sem tabela visual, apenas para leitura lógica)*  
Reset destrói, Revert adiciona, Amend refina.

---

## 5. Fluxos práticos e diagnósticos

**Desfazer último commit, mas manter alterações:**
```bash
git reset --soft HEAD~1
```

**Desfazer último commit e alterações, de vez:**
```bash
git reset --hard HEAD~1
```

**Desfazer um commit público sem apagar histórico:**
```bash
git revert <sha>
```

**Corrigir o commit anterior:**
```bash
git commit --amend
```

**Ver commits recentes com índice:**
```bash
git log --oneline --decorate -n 10
```

**Recuperar commit perdido após reset/amend:**
```bash
git reflog
git checkout <sha>
```

---

## 6. Resumo rápido

- `reset` (soft/mixed/hard): move HEAD; reescreve histórico local
- `revert`: cria um commit que desfaz outro; seguro em branches públicas
- `commit --amend`: reescreve somente o último commit

