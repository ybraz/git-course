# 📘 Complete Git Course

Bem-vindo ao **Complete Git Course**, um guia prático e teórico para dominar o Git — desde os comandos mais básicos até o trabalho com repositórios remotos e fluxos de colaboração profissional.  
Este material foi desenvolvido com uma abordagem progressiva: cada capítulo constrói a base para o próximo.

---

## 📚 Estrutura do Curso

| Capítulo | Título                                      | Conteúdo Principal                                                                                   |
|----------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| 1        | Introdução                                  | História do Git, conceitos fundamentais e motivação                                                   |
| 2        | Instalação                                  | Instalação em diferentes sistemas operacionais e configurações iniciais                               |
| 3        | Shell Básico                                | Navegação no terminal e comandos essenciais                                                           |
| 4        | Como o Git Funciona                          | Arquitetura interna, snapshots vs. diffs, SHA-1 e objetos                                              |
| 5        | Operações Básicas do Git                     | `init`, `add`, `commit`, `status`, `log` e controle de histórico                                      |
| 6        | Branches e HEAD                              | Conceitos de ramificação, `checkout`, `branch`, `merge`, HEAD e fluxo de trabalho                      |
| 7        | Clonando e Modificando Repositórios Públicos | `clone`, `fork`, `fetch`, `pull` e atualização de repositórios existentes                              |
| 8        | Merge e Resolução de Conflitos               | Estratégias de merge, conflitos comuns e práticas recomendadas                                        |
| 9        | GitHub e Repositórios Remotos               | `remote`, `push`, `pull`, `set-url`, `origin` e colaboração em equipe                                 |
|10        | Git Push, Fetch, and Pull                   | `push`, `pull`, `fetch`                                                                               | 

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

