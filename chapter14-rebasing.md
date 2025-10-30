# Chapter 14 - Git Rebase

## 1. Conceito central

Rebase significa “mudar a base” de uma sequência de commits.  
Em vez de criar um *merge commit*, o Git **reaplica os commits da sua branch** sobre uma nova base,  
criando uma linha de histórico linear, como se o trabalho tivesse começado no ponto mais recente da branch principal.

No fundo, o rebase não altera o conteúdo dos commits — ele **recria novos commits** com pais diferentes,  
o que gera novos hashes (SHAs).

## 2. Fluxo de trabalho — “rebase → merge fast-forward”

### Situação inicial

    main:     A---B---C
    feature:  A---B---X---Y

A branch `feature` saiu de `B`, mas `main` avançou até `C`.

### Etapa 1 — Atualizar a feature (rebase)

    git checkout feature
    git fetch origin
    git rebase origin/main

O Git:
1. Encontra a base comum entre as duas branches (aqui, o commit `B`);
2. Extrai seus commits exclusivos (`X`, `Y`);
3. Move o ponteiro da `feature` para `C`;
4. Recria `X'` e `Y'` sobre `C`.

Agora temos:

    main:     A---B---C
    feature:              C---X'---Y'

Se houver conflitos, o rebase pausa em cada commit.  

Você resolve, faz `git add <arquivos>` e segue com `git rebase --continue`.  

Se quiser desistir, `git rebase --abort`.

### Etapa 2 — Revisão e testes

Você trabalha em `feature`, confirma que tudo está estável, e prepara para integrar na `main`.

### Etapa 3 — Merge final (fast-forward)

    git checkout main
    git merge feature

Como o histórico é linear, o Git simplesmente **avança o ponteiro da main** até o commit final da `feature`.  
## 1. Conceito central

Rebase significa “mudar a base” de uma sequência de commits.
Em vez de criar um *merge commit*, o Git **reaplica os commits da sua branch** sobre uma nova base,
criando uma linha de histórico linear, como se o trabalho tivesse começado no ponto mais recente da branch principal.

No fundo, o rebase não altera o conteúdo dos commits — ele **recria novos commits** com pais diferentes,
o que gera novos hashes (SHAs).

## 2. Fluxo de trabalho — “rebase → merge fast-forward”

### Situação inicial

```text
main:     A---B---C
feature:  A---B---X---Y
```

A branch `feature` saiu de `B`, mas `main` avançou até `C`.

### Etapa 1 — Atualizar a feature (rebase)

```bash
git checkout feature
git fetch origin
git rebase origin/main
```

O Git:
1. Encontra a base comum entre as duas branches (aqui, o commit `B`);
2. Extrai seus commits exclusivos (`X`, `Y`);
3. Move o ponteiro da `feature` para `C`;
4. Recria `X'` e `Y'` sobre `C`.

Agora temos:

```text
main:     A---B---C
feature:              C---X'---Y'
```

Se houver conflitos, o rebase pausa em cada commit.
Resolva, faça `git add <arquivos>` e siga com `git rebase --continue`.
Se quiser desistir, execute `git rebase --abort`.

### Etapa 2 — Revisão e testes

Trabalhe em `feature`, confirme que tudo está estável e prepare a integração na `main`.

### Etapa 3 — Merge final (fast-forward)

```bash
git checkout main
git merge feature
```

Como o histórico é linear, o Git simplesmente **avança o ponteiro da main** até o commit final da `feature`.
Nenhum *merge commit* é criado.

Resultado:

```text
main: A---B---C---X'---Y'
```

Esse é o fluxo mais limpo e recomendado em times maduros:
rebase para alinhar, merge final para integrar.

### Etapa 4 — Push e limpeza

```bash
git push origin main
git branch -d feature
git push origin --delete feature
```

O ciclo se encerra: a `main` contém os commits reaplicados e testados, e a branch de feature pode ser removida.

## 3. O que o Git faz internamente durante o rebase

Durante o rebase, o Git cria diretórios e arquivos temporários:

```text
.git/rebase-merge/
.git/rebase-apply/
```

Neles há:

- `git-rebase-todo`: lista dos commits a serem reaplicados;
- `head-name`: branch em rebase;
- `orig-head`: o commit original antes do rebase;
- `stopped-sha`: o commit atual em caso de conflito.

Enquanto reaplica commits, o Git atualiza variáveis internas como `HEAD` e `REBASE_HEAD`,
criando novos objetos de tipo `commit` em `.git/objects`.

O histórico anterior é preservado por um tempo no `reflog`.
Você pode vê-lo com:

```bash
git reflog
```

Para restaurar, se necessário:

```bash
git checkout <sha-antigo>
```

Ao terminar, o Git remove `.git/rebase-*` e aponta `.git/refs/heads/feature` para o novo último commit.

## 4. Estrutura interna — refs e objects

Branches e tags são armazenadas como referências (*refs*):

```text
.git/refs/heads/main
.git/refs/heads/feature
```

Cada arquivo contém o hash SHA do commit mais recente.
Quando você faz rebase, o SHA muda, e o Git atualiza o conteúdo da ref correspondente.

Os commits são objetos em `.git/objects/` — arquivos zlib com cabeçalho de tipo (`commit`, `tree`, `blob`, etc.).
Para ver o conteúdo de um commit:

```bash
git cat-file -p <sha>
```

Durante o rebase, o Git cria novos objetos do tipo `commit`, cada um com um `parent` apontando para o commit anterior da nova base.

## 5. Verificando o estado do rebase

- **Commit atual em reaplicação:**

    ```bash
    git status
    ```

- **Lista dos commits em rebase:**

    ```bash
    cat .git/rebase-merge/git-rebase-todo
    ```

- **Commit original da branch:**

    ```bash
    cat .git/rebase-merge/orig-head
    ```

- **Hash da base usada no rebase:**

    ```bash
    git rev-parse HEAD
    git rev-parse REBASE_HEAD
    ```

- **Interromper, pular ou continuar:**

    ```bash
    git rebase --abort
    git rebase --skip
    git rebase --continue
    ```

## 6. Conflitos e resolução

Durante o rebase, cada commit é reaplicado separadamente.
Se surgir um conflito, ele é resolvido individualmente antes de seguir para o próximo commit.

Fluxo típico:

```bash
git status
git add <arquivos resolvidos>
git rebase --continue
```

Para ignorar um commit com conflito irrelevante:

```bash
git rebase --skip
```

Para abandonar o rebase:

```bash
git rebase --abort
```

---

## 7. Depois do rebase — push e sincronização

Após um rebase, os commits antigos e novos têm **hashes diferentes**.
Por isso, o remoto vê divergência e o push comum falha com erro de *non-fast-forward*.

Use o push seguro:

```bash
git push --force-with-lease origin feature
```

Essa opção força o push **apenas se o remoto ainda estiver no ponto esperado**,
evitando sobrescrever o trabalho de outros.

Nunca use `--force` puro em branches compartilhadas.
Rebase só deve ser feito em branches **locais ou pessoais**.

## 8. Comparando rebase e merge em profundidade

- Merge cria um novo commit que une dois históricos.
    O resultado preserva o formato de árvore, mostrando que houve paralelismo.
- Rebase descarta a bifurcação visual,
    reaplicando commits sobre outra linha para deixar o histórico linear.

Internamente:

- Merge cria **um novo objeto commit** com dois pais (commit A e commit B).
- Rebase cria **novos commits individuais**, cada um com **um único pai**, o que muda os SHAs.

O merge preserva o que aconteceu.
O rebase reescreve para mostrar como *poderia ter acontecido sem bifurcação*.

## 9. Comandos e inspeções úteis

- **Tipo de um objeto:**

    ```bash
    git cat-file -t <sha>
    ```

- **Conteúdo de um commit (autor, data, parent):**

    ```bash
    git cat-file -p <sha>
    ```

- **Refs e objetos associados:**

    ```bash
    git show-ref
    ```

- **Commits do rebase atual:**

    ```bash
    git log --oneline --reflog
    ```

- **Diferença entre commit anterior e reaplicado:**

    ```bash
    git diff <old-sha> <new-sha>
    ```

- **Verificar se há rebase em andamento:**

    ```bash
    test -d .git/rebase-merge && echo "Rebase em andamento"
    ```

## 10. Quando não usar rebase

- Nunca rebase commits que já foram empurrados e podem estar em uso por outros.
- Não rebase branches que fazem parte de releases já marcadas.
- Não rebase histórico público — isso reescreve o passado de todos.

## 11. Reflexão filosófica

O *merge* preserva a história como ela foi: com suas bifurcações, conflitos e reconciliações.
O *rebase* reconta essa história de forma idealizada — linear, limpa, mas reconstruída.

Ambos são legítimos.
O primeiro expressa a **verdade cronológica**,
o segundo, a **verdade lógica** do código.

Saber quando usar cada um é compreender a diferença entre **história** e **narrativa**.
