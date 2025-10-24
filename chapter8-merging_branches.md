# Complete Git Course

## Chapter 8 - Merging Branches

### O que é a `main` branch?

A **`main` branch** representa a **linha principal de desenvolvimento** de um projeto.  
Ela costuma ser a versão **estável**, testada e pronta para produção — muitas vezes chamada também de **branch de release**.

Todas as novas funcionalidades, correções de bugs ou experimentos costumam ser desenvolvidas em **branches secundárias** e, quando prontas, **integradas à `main`** por meio de um *merge* ou *pull request*.  
Manter a `main` limpa e estável garante previsibilidade e facilita a colaboração.

```bash
# Verificar em qual branch você está  
git branch  
  
# Criar uma nova branch de feature a partir da main  
git switch -c feature/login-page  
  
# Trabalhar, adicionar commits, etc.  
git add .  
git commit -m "Add login page structure"  
  
# Voltar para a main  
git switch main  
   
# Atualizar a main com as últimas mudanças do remoto (boas práticas)  
git pull origin main
```

### Abordagens de *Merge*

Existem duas abordagens principais para unir mudanças de uma branch à outra:

1) **Fast-forward merge**

Ocorre quando **a `main` não avançou desde que a sua branch de feature foi criada**.  
Não há divergência de histórico: o Git apenas move o ponteiro da `main`.

**Características**  
- Histórico linear e limpo  
- Não cria commit de merge  
- Simples e eficiente

```bash
# Estando na main  
git switch main  
  
# Trazer as mudanças da branch de feature  
git merge feature/login-page  
  
# Como não houve novos commits na main, o merge será fast-forward
```

**Histórico (simplificado)**

> A --- B --- C (main, feature)

2) **3-way merge**

Usado quando **`main` e a branch de feature divergiram** (ambas receberam commits).  
O Git compara **base comum**, **HEAD da main** e **HEAD da feature** e cria um **commit de merge**.

**Características**  
- Preserva a história completa  
- Bom para trabalho colaborativo  
- Pode gerar conflitos

```bash
# Estando na main  
git switch main  
  
# Sincronizar a main com o remoto  
git pull origin main  
  
# Integrar a feature  
git merge feature/login-page
```

Conflitos podem acontecer se houverem edições no mesmo arquivo em diferentes branches. Se houver conflitos, edite os arquivos com conflito (as duas edições estarão presentes para que você escolha qual está correta):

```bash
# Abrir e resolver conflitos  
nvim arquivo_em_conflito.txt  
  
# Marcar como resolvido  
git add arquivo_em_conflito.txt  
  
# Finalizar o merge  
git commit
```

**Histórico (simplificado)**

>         --- D --  
>        /        \
> A --- B --- C --- M (main)

> `M` é o novo commit de merge.

Dicas práticas:
- Use **fast-forward** para pequenas features sem divergência.  
- Use **3-way merge** (ou PR com merge commit) quando houve trabalho paralelo.  
- Sempre sincronize a `main` antes de integrar:  
> `git switch main`  
> `git pull origin main`

Comandos úteis:
- Ver apenas commits de merge  
> `git log --merges --oneline`

- Visualizar grafo do histórico  
> `git log --oneline --graph --all` 

- Abortar um merge em andamento  
> `git merge --abort`


