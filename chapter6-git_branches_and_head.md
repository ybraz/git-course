# Complete Git Course

## Chapter 6 - Git Branches and HEAD

O comando `git checkout` permite que você mova o ponteiro do HEAD para um commit ou branch específico, atualizando o Working Directory com os arquivos correspondentes a esse ponto da história do repositório. Em outras palavras, ele “transporta” o estado do seu projeto para um momento anterior (ou alternativo), substituindo o conteúdo atual pelos arquivos registrados no commit ou branch selecionado.

### Branches

Internamente, uma branch no Git é uma **referência em texto que aponta para um commit**.

Ela funciona como um ponteiro móvel: ao criar novos commits, esse ponteiro é automaticamente atualizado para o commit mais recente.

- A branch padrão é `main`.  
- Múltiplas branches podem coexistir no mesmo repositório.  
- Os ponteiros das branches ficam armazenados em `.git/refs/heads/<branch>`.  
- A branch atual rastreia novos commits. O ponteiro da branch atual fica em `.git/HEAD`. Esse arquivo tem o conteúdo semelhante a esse: `ref: refs/heads/main`.
- O ponteiro da branch se move automaticamente a cada novo commit.
- No comando `git log` mostra-se para qual branch o HEAD está apontado.
- É uma boa prática utilizar prefixos, tal como nos commits (feature/, fix/, hotfix/, chore/, journal/).
- Para mudar de branch, utilize:  
  ```bash
  git checkout <branch>
  ```

```bash
$ ls .git/refs/heads 
main

$ cat .git/refs/heads/main 
a91f9c405237e3b17033492c3273281218d3102b

$ cat .git/HEAD 
ref: refs/heads/main

$ git log
commit a91f9c405237e3b17033492c3273281218d3102b (HEAD -> main) #aqui vemos para qual branch o HEAD está apontando.
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 19:00:23 2025 -0300

    docs: Capítulo cinco completo e criação do arquivo do capítulo 6, ainda vazio.

commit 02af087b6aed5a5a3e2c27fd640afe777793c3cd
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 17:18:03 2025 -0300

    docs: Quatro primeiros capítulos completos e o quinto capítulo parcial.

commit 9cad4f528b92ac30cf072c83b5c3c5bcf89a4bcc
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 10:24:35 2025 -0300

    chorus: Commit inicial apenas com arquivos de teste.


```

### HEAD

O `HEAD` é uma **referência simbólica** para a branch ou commit atualmente ativo (checked-out).

- Seu valor é armazenado no arquivo `.git/HEAD`.
- Por padrão, ele aponta para `ref: refs/heads/main`.
- Cada desenvolvedor pode ter o `HEAD` apontando para uma branch ou commit diferente.
- Quando você muda de branch, o `HEAD` passa a apontar para essa nova referência.
- Quando você faz checkout de um commit diretamente, o `HEAD` deixa de apontar para uma branch e passa a apontar para um commit — estado conhecido como *detached HEAD*.
- Para mudar de referência:
  ```bash
  git checkout <branch>   # muda para uma branch
  git checkout <sha1>     # muda para um commit específico

Se fizermos checkout diretamente para um commit específico, entramos em um estado chamado detached HEAD, que significa que o HEAD está apontando para um commit específico mas não existe nenhum ponteiro de branch acompanhando seus novos commits.
- Não é uma boa prática pois pode-se perder facilmente os commits por não haver um ponteiro registrado. Ou seja, fica mais difícil de fazer a gestão.
- O ideal é criar uma nova branch a partir do ponto atual com: `git branch minha-nova-branch`
- Também é possível já fazer o checkout criando uma nova branch, com: `git checkout -b <hash>`. Normalmente, sem o parâmetro `-b`, o git checkout apenas muda o HEAD, sem criar nova branch. 
- Quando estiver em um detached HEAD, pode-se voltar para a última versão de main facilmente com: `git checkout main`, ou o nome de outra branch.
- Em um detached HEAD vemos apenas os commits anteriores. Os posteriores perdem a visibilidade. 

```bash
$ cat .git/HEAD 
ref: refs/heads/main

$ cat .git/refs/heads/main 
d0887e551f907d4a4060dbd70a1fe6469d70c00f

$ git cat-file -p d0887e551f907d4a4060dbd70a1fe6469d70c00f
tree 1d3f6ddc7b8893ac74842f18dd0ffdce01643513
parent a91f9c405237e3b17033492c3273281218d3102b
author Yuri Braz <ybraz@live.com> 1761035424 -0300
committer Yuri Braz <ybraz@live.com> 1761035424 -0300

docs: Pequenas correções no capítulo 5 e uma parte do capítulo 6 (ainda não concluído).

$ git cat-file -p a91f9c405237e3b17033492c3273281218d3102b
tree 6293808aba8f1b624233898a809253b94f511683
parent 02af087b6aed5a5a3e2c27fd640afe777793c3cd
author Yuri Rodrigues Braz <ybraz@live.com> 1760911223 -0300
committer Yuri Rodrigues Braz <ybraz@live.com> 1760911223 -0300

docs: Capítulo cinco completo e criação do arquivo do capítulo 6, ainda vazio.

$ git checkout 02af087b6aed5a5a3e2c27fd640afe777793c3cd                     # Fazendo checkout em um commit específico
Note: switching to '02af087b6aed5a5a3e2c27fd640afe777793c3cd'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 02af087 docs: Quatro primeiros capítulos completos e o quinto capítulo parcial.

$ git log                                                                   # Mostra os commmits da perspectiva atual
commit 02af087b6aed5a5a3e2c27fd640afe777793c3cd (HEAD)
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 17:18:03 2025 -0300

    docs: Quatro primeiros capítulos completos e o quinto capítulo parcial.

commit 9cad4f528b92ac30cf072c83b5c3c5bcf89a4bcc
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 10:24:35 2025 -0300

    chorus: Commit inicial apenas com arquivos de teste.

$ git checkout main                                                         # Retornando para a versão normal
Previous HEAD position was 02af087 docs: Quatro primeiros capítulos completos e o quinto capítulo parcial.
Switched to branch 'main'

$ git log                                                                   # Volta a ver todos os commits
commit d0887e551f907d4a4060dbd70a1fe6469d70c00f (HEAD -> main)
Author: Yuri Braz <ybraz@live.com>
Date:   Tue Oct 21 05:30:24 2025 -0300

    docs: Pequenas correções no capítulo 5 e uma parte do capítulo 6 (ainda não concluído).

commit a91f9c405237e3b17033492c3273281218d3102b
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 19:00:23 2025 -0300

    docs: Capítulo cinco completo e criação do arquivo do capítulo 6, ainda vazio.

commit 02af087b6aed5a5a3e2c27fd640afe777793c3cd
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 17:18:03 2025 -0300

    docs: Quatro primeiros capítulos completos e o quinto capítulo parcial.

commit 9cad4f528b92ac30cf072c83b5c3c5bcf89a4bcc
Author: Yuri Rodrigues Braz <ybraz@live.com>
Date:   Sun Oct 19 10:24:35 2025 -0300

    chorus: Commit inicial apenas com arquivos de teste.

```

O comando `git switch` foi introduzido no Git 2.23 (2019) para separar responsabilidades que antes estavam concentradas no `git checkout`, tornando o uso mais claro e seguro.

Principais usos:
- `git switch <branch>` → muda para a branch especificada.
- `git switch -` → alterna de volta para a branch anterior.
- `git switch -c <nova-branch>` → cria e muda para uma nova branch a partir do commit atual (mesmo se for um detached HEAD).
- `git switch --detach <commit>` → muda para um commit específico em detached HEAD.
- `git switch --force` → força a troca de branch mesmo com mudanças não salvas.

Histórico:
Antes de 2019, `git checkout` fazia tanto a troca de branch quanto a restauração de arquivos, o que causava confusão e erros. A criação de `git switch` (para troca de branch) e `git restore` (para restaurar arquivos) trouxe clareza e reduziu riscos operacionais.

Para mudar para um commit específico no tempo e criar uma nova branch a partir dele:

1. Entrar em detached HEAD:
git switch --detach <hash-do-commit>

2. Criar uma nova branch a partir desse commit:
git switch -c <nome-da-nova-branch>

Observação:
- Após o passo 1, você estará “fora” de qualquer branch, em detached HEAD.
- O passo 2 cria a nova branch e já posiciona a HEAD nela.
- Você também poderia fazer em uma linha: `git switch -c <nova-branch> <hash-do-commit>`.

### Gerenciamento de Branches

- `git branch`  
  Lista as branches locais.

- `git branch -vv`  
  Lista branches locais com detalhes e tracking remoto.

- `git branch <name>`  
  Cria uma nova branch (sem mudar para ela).

- `git switch <name>`  
  Muda para a branch especificada.

- `git switch -c <nova-branch>`  
  Cria e já muda para a nova branch.

- `git branch -d <name>`  
  Deleta uma branch local (se já estiver mesclada).

- `git branch -m <novo_nome>`  
  Renomeia a branch atual.

- `git branch --merged`  
  Lista branches já mescladas na branch atual.

Exemplos:

```bash
$ git branch
* main

$ git branch temp 

$ git branch
* main
  temp

$ ls .git/refs/heads/
main	temp

$ cat .git/refs/heads/main 
d0887e551f907d4a4060dbd70a1fe6469d70c00f

$ cat .git/refs/heads/temp 
d0887e551f907d4a4060dbd70a1fe6469d70c00f

$ git switch temp
M	chapter6-git_branches_and_head.md
Switched to branch 'temp'

$ git status
On branch temp
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   chapter6-git_branches_and_head.md

no changes added to commit (use "git add" and/or "git commit -a")

$ cat .git/HEAD 
ref: refs/heads/temp

$ git switch main
M	chapter6-git_branches_and_head.md
Switched to branch 'main'

$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   chapter6-git_branches_and_head.md

no changes added to commit (use "git add" and/or "git commit -a")

$ git branch -d temp 
Deleted branch temp (was d0887e5).

$ ls .git/refs/heads 
main

```

