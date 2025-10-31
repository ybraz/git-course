# Git Stash — guardando o trabalho em andamento

## 1. O que é o Git Stash

`git stash` é um comando para **guardar temporariamente modificações não comitadas**, sem precisar fazer um commit.  
Ele cria uma espécie de *pilha de rascunhos* (stash stack), permitindo mudar de branch, atualizar código, ou resolver conflitos **sem perder o que estava em progresso**.

Pense nele como um **“savepoint” temporário** — uma caixa onde você coloca seu trabalho inacabado enquanto resolve outras tarefas.

---

## 2. Filosofia por trás do Stash

A filosofia do `stash` é de **flexibilidade sem sujeira**.  
Ele surgiu para resolver um dilema ético no fluxo de trabalho:

> “E se eu precisar mudar de contexto, mas ainda não quero (ou não posso) fazer commit?”

O `stash` representa o valor do **provisório**:  
uma pausa que preserva o progresso sem corromper o histórico.  
É o antídoto contra o “commit desesperado” — aquele *WIP commit* (“Work In Progress”) que depois polui o log.

Em outras palavras: o `stash` é o **ato consciente de suspender o presente para cuidar do futuro**.

---

## 3. O que acontece internamente

Quando você executa:

```bash
git stash push -m "mensagem opcional"
```

O Git realiza:

1. **Criação de dois commits internos:**
   - um commit com o conteúdo atual do *index* (staging area),
   - outro com o conteúdo do *working directory* (não rastreado ou modificado).
2. Esses commits são armazenados sob a ref:

```text
refs/stash
```

   Cada novo `stash` é empilhado como um novo commit filho dessa ref.
3. O diretório de trabalho é limpo — o HEAD é restaurado ao último commit.

Você pode ver isso olhando em:

```text
.git/logs/refs/stash
```

```bash
git cat-file -p stash@{0}
```

Cada item `stash@{n}` é composto por três objetos:
- **WIP commit** (Working Directory)
- **Index commit** (Staged)
- **Parent commit** (HEAD original)

Internamente, o stash é uma **mini-branch efêmera**, com histórico e commits próprios.

---

## 4. Usos práticos e exemplos

### Salvar modificações temporariamente

```bash
git stash
```

ou com mensagem:

```bash
git stash push -m "ajuste temporário da função X"
```

Isso guarda:
- arquivos modificados rastreados,
- arquivos novos *adicionados ao index*.

---

### Incluir arquivos não rastreados (`untracked`)

Por padrão, o stash ignora arquivos não versionados.  
Para incluí-los:

```bash
git stash push -u
```

ou, para incluir também arquivos ignorados (`.gitignore`):

```bash
git stash push -a
```

(`-a` significa *all*)

---

### Listar os stashes existentes

```bash
git stash list
```

Exemplo de saída:

```text
stash@{0}: On feature-x: ajuste temporário da função X
stash@{1}: On main: correção parcial no parser
```

---

### Ver o conteúdo de um stash

```bash
git stash show
```

Mostra um resumo (arquivos e linhas alteradas).

Para ver o diff completo:

```bash
git stash show -p stash@{0}
```

---

### Aplicar um stash de volta (sem remover da pilha)

```bash
git stash apply stash@{0}
```

O stash continua armazenado.

---

### Aplicar e remover (pop)

```bash
git stash pop
```

Aplica o stash mais recente **e o remove** da pilha.

---

### Remover um stash específico

```bash
git stash drop stash@{1}
```

---

### Limpar todos os stashes

```bash
git stash clear
```

Cuidado — esta ação é irreversível.

---

### Criar uma branch a partir de um stash

```bash
git stash branch correção-temporária stash@{0}
```

Isso cria uma nova branch com o conteúdo do stash aplicado e já removido da pilha.  
Excelente para quando você percebe que o trabalho “temporário” na verdade merece uma branch própria.

---

## 5. Casos típicos de uso

### 1. Mudar de branch no meio do trabalho

Você está em `feature-x`, mas precisa mudar para `main`:

```bash
git stash push -m "trabalho parcial da feature-x"
git checkout main
git pull origin main
git checkout feature-x
git stash pop
```

---

### 2. Resolver conflito sem perder o progresso

Durante um rebase ou merge, se algo dá errado e você quer começar do zero:

```bash
git stash
git rebase --abort
git stash pop
```

---

### 3. Fazer teste rápido em outra branch

```bash
git stash
git checkout outra-branch
# testa algo
git checkout feature-x
git stash pop
```

---

### 4. Guardar múltiplos contextos

O stash funciona como uma pilha (stack).  
Cada vez que você cria um stash, ele é numerado (`stash@{0}` o mais recente).  
Você pode voltar a versões intermediárias:

```bash
git stash apply stash@{2}
```

---

## 6. Diagnóstico e recuperação

Se o stash falhar ao aplicar (ex: conflitos):

- O Git deixará os conflitos no diretório de trabalho.
- Resolva-os manualmente e finalize com:
  
      git add <arquivos>
      git stash drop stash@{0}

Se quiser investigar os objetos internos:

    git log -g refs/stash
    git cat-file -p stash@{0}

Você pode até **converter** um stash em commit visível:

    git stash show -p stash@{0} | git apply
    git commit -am "Recupera stash perdido"

---

## 7. Comandos úteis para o dia a dia

Guardar mudanças:

    git stash

Guardar com mensagem:

    git stash push -m "ajuste temporário"

Incluir arquivos não rastreados:

    git stash -u

Listar:

    git stash list

Ver mudanças:

    git stash show -p stash@{0}

Aplicar sem remover:

    git stash apply

Aplicar e remover:

    git stash pop

Remover específico:

    git stash drop stash@{n}

Remover todos:

    git stash clear

Criar branch a partir do stash:

    git stash branch <nome>

---

## 8. Filosofia final

O `stash` é o refúgio da transitoriedade — o reconhecimento de que o trabalho em progresso tem valor, mesmo quando ainda não é definitivo.  
Ele permite que você **pause o presente**, sem violentar o histórico com commits artificiais.

Enquanto `reset` e `rebase` moldam o passado,  
`stash` protege o agora.

É a forma mais humana do Git: saber que o código ainda não está pronto, mas ainda assim **não querer perdê-lo**.

---

