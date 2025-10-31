# Complete Git Course

Essas são minhas notas do **Complete Git Course**, um guia prático e teórico para dominar o Git — desde os comandos mais básicos até o trabalho com repositórios remotos e fluxos de colaboração profissional.

Este material foi desenvolvido com uma abordagem progressiva: cada capítulo constrói a base para o próximo.

---

## 📚 Estrutura do Curso

| Capítulo | Título | Conteúdo Principal |
|---------:|--------|--------------------|
| 1 | [Introdução](./chapter1-introduction.md) | História do Git, conceitos fundamentais e motivação |
| 2 | [Instalação](./chapter2-installation.md) | Instalação em diferentes sistemas operacionais e configurações iniciais |
| 3 | [Shell Básico](./chapter3-basic_shell.md) | Navegação no terminal e comandos essenciais |
| 4 | [Como o Git Funciona](./chapter4-how_git_works.md) | Arquitetura interna, snapshots vs. diffs, SHA-1 e objetos |
| 5 | [Operações Básicas do Git](./chapter5-basic_git_operations.md) | `init`, `add`, `commit`, `status`, `log` e controle de histórico |
| 6 | [Branches e HEAD](./chapter6-git_branches_and_head.md) | Conceitos de ramificação, `checkout`, `branch`, `merge`, HEAD e fluxo de trabalho |
| 7 | [Clonando e Modificando Repositórios Públicos](./chapter7-cloning_modifying_public_repos.md) | `clone`, `fork`, `fetch`, `pull` e atualização de repositórios existentes |
| 8 | [Merge e Resolução de Conflitos](./chapter8-merging_branches.md) | Estratégias de merge, resolução de conflitos e práticas recomendadas |
| 9 | [GitHub e Repositórios Remotos](./chapter9-github_remote_repositories.md) | `remote`, `origin`, `set-url` e colaboração em equipe |
| 10 | [Git Push, Fetch e Pull](./chapter10-git_push_fetch_pull.md) | `push`, `pull`, `fetch` |
| 11 | [Pull Requests (PRs)](./chapter11-pull_requests.md) | Revisão de código, abertura de PRs, status checks, estratégias de merge e GitHub CLI |
| 12 | [Git Log e Shortlog](./chapter12-git_log_shortlog.md) | `git log`, formatação, filtros e `git shortlog` |
| 13 | [Git Tags](./chapter13-git_tags.md) | Tags lightweight e anotadas, boas práticas e releases |
| 14 | [Rebasing](./chapter14-rebasing.md) | `rebase` interativo, linearização de histórico e resolução de conflitos |
| 15 | [Ignorando Arquivos no Git](./chapter15-igoring_files_in_git.md) | `.gitignore`, padrões glob, regras e exceções |
| 16 | [Reset, Revert e Amend](./chapter16-git_reset_rever_amend.md) | `reset` (`--soft`, `--mixed`, `--hard`), `revert`, `commit --amend` |
| 17 | [Git Hooks](./chapter17-git_hooks.md) | Hooks client-side e server-side, automação de validações e políticas |
| — | [Git Stash](./git_stash.md) | Salvamento temporário de mudanças não comitadas |

## 🧭 Como usar este repositório

- Clique nos títulos da tabela acima para abrir o capítulo correspondente.
- Leia em sequência ou consulte pontualmente quando precisar de um comando ou conceito.
- Use a busca do editor para encontrar rapidamente tópicos específicos (ex.: "rebase", "tags").

---

## 🛠️ Pré-requisitos

- Conhecimentos básicos de terminal (bash/zsh ou equivalente)  
- Git instalado na máquina local  
- Conta no GitHub
- Editor de texto ou IDE de sua preferência (ex: Neovim, VSCode)

---

## 🧭 Fluxo de Trabalho Essencial

1. **Crie um repositório local**  
   > `git init`

2. **Adicione arquivos e faça commits**  
   > `git add .`  
   > `git commit -m "primeiro commit"`

3. **Configure o repositório remoto**  
   > `git remote add origin git@github.com:usuario/repositorio.git`

4. **Envie suas alterações**  
   > `git push -u origin main`

5. **Mantenha o repositório atualizado**  
   > `git pull origin main`

---

## 🤝 Boas Práticas de Git

- Commits **claros e atômicos** — cada commit deve representar uma unidade lógica de mudança.  
- Use **branches temáticas** (`feature/`, `fix/`, `hotfix/`) para organizar o desenvolvimento.  
- Atualize sua branch antes de dar push (`git pull`) para evitar conflitos.  
- Revise commits com `git log --oneline --graph` para manter um histórico limpo.  
- Utilize `.gitignore` para evitar versionar arquivos desnecessários.

---

## 🌐 Integração com GitHub

- Colabore com Pull Requests (PRs) e Code Reviews.  
- Utilize Issues e Projects para gerenciar tarefas.  
- Habilite Actions para automação e CI/CD.  
- Configure SSH Keys para autenticação segura.  
- Use Tags e Releases para versionamento semântico.

---

## 📎 Recursos Adicionais

- [Documentação Oficial do Git](https://git-scm.com/doc)  
- [GitHub Docs](https://docs.github.com/)  
- [Pro Git Book (Scott Chacon)](https://git-scm.com/book/pt-br/v2)  
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

---

## 🧠 Reflexão Final

> “O Git não é apenas uma ferramenta de versionamento, mas um **modo de pensar** o desenvolvimento: distribuído, colaborativo e com responsabilidade individual.”  
> — *Inspirado no espírito do software livre*

---

## 📜 Licença

Este curso é distribuído sob a licença MIT.  
Sinta-se livre para reutilizar, adaptar e compartilhar, mantendo os créditos.

