# Capítulo 11 — Pull Requests (PRs)

## 1. Introdução

Pull Requests (PRs) são o coração da colaboração em plataformas como GitHub. Um PR propõe integrar mudanças de uma branch (geralmente de feature ou fix) em outra branch (comum ser `main`), abrindo espaço para revisão, discussão, validações automatizadas (CI) e, por fim, merge.

Resumo do fluxo típico:

1) Você cria uma branch de trabalho a partir da `main`.
2) Faz commits e envia para o remoto (`git push -u origin minha-branch`).
3) Abre um Pull Request no GitHub comparando sua branch com a base (ex.: `main`).
4) Colegas revisam, sugerem mudanças e aprovam.
5) Checks automáticos (CI/tests/linters) passam com sucesso.
6) O PR é integrado com uma estratégia de merge.

---

## 2. Conceitos Essenciais

- Base branch: a branch de destino do PR (ex.: `main`).
- Head branch: a branch que contém suas alterações (ex.: `feature/login`).
- Review: análise de código com comentários, sugestões e aprovações.
- Status checks: resultados de pipelines (CI), testes e validações obrigatórias.
- Draft PR: PR em rascunho para discussão inicial, ainda sem intenção de merge imediato.

---

## 3. Preparando o PR (local)

Crie uma branch descritiva, faça commits pequenos e claros, e sincronize com o remoto:

```bash
# Partir da main atualizada
git switch main
git pull origin main

# Criar branch de feature
git switch -c feature/login-form

# Trabalhar normalmente
nvim src/LoginForm.tsx
git add .
git commit -m "feat(login): add basic login form and validation"

# Publicar a branch e setar upstream
git push -u origin feature/login-form
```

Dica: atualize a sua branch com a `main` antes de abrir o PR para reduzir conflitos:

```bash
git fetch origin
git rebase origin/main   # ou git merge origin/main
git push --force-with-lease
```

`--force-with-lease` é mais seguro do que `--force` porque evita sobrescrever trabalho alheio por engano.

---

## 4. Abrindo um PR no GitHub (UI)

1) Acesse seu repositório no GitHub.
2) Clique em “Compare & pull request” assim que o GitHub detectar sua nova branch, ou use a aba “Pull requests” → “New pull request”.
3) Confirme os pares Base (ex.: `main`) ← Head (ex.: `feature/login-form`).
4) Preencha título e descrição. Inclua contexto, motivação, prints e links para issues.
5) Marque revisores, labels e, se necessário, um milestone.
6) Se ainda não está pronto, marque como Draft.

Estrutura sugerida para a descrição do PR:

- Contexto: por que isso é necessário?
- O que foi feito: lista objetiva das mudanças.
- Como testar: passos para validar manualmente.
- Impactos: riscos, migrações, deprecations.
- Checklist: ver seção de boas práticas.

---

## 5. Revisão de Código e Iteração

Durante a revisão, comentários e sugestões podem surgir. Para ajustar:

```bash
# Continue commitando na mesma branch
git add .
git commit -m "fix(login): address reviewer feedback on validation"
git push
```

O PR é automaticamente atualizado com novos commits. Evite abrir múltiplos PRs para o mesmo escopo.

Comentários comuns de revisão:
- Nomes de variáveis/arquivos mais claros
- Dividir PRs grandes em PRs menores
- Cobrir casos de borda e adicionar testes
- Remover código morto ou debugging

---

## 6. Estratégias de Merge do PR

As opções variam por configuração do repositório, mas as mais comuns são:

1) Merge commit (padrão)
	- Preserva todos os commits e cria um commit de merge na `main`.
	- Bom para rastrear o contexto de integração.

2) Squash and merge
	- Compacta todos os commits do PR em um único commit na `main`.
	- Ideal para manter histórico linear e limpo.

3) Rebase and merge
	- Rebase dos commits do PR sobre a `main` e depois fast‑forward.
	- Mantém histórico linear, mas reescreve SHAs (atenção para branches compartilhadas).

Recomendação prática:
- Projetos com muitos contribuidores tendem a preferir “Squash and merge”.
- Para bibliotecas que valorizam granularidade de commits, “Merge commit” pode ser útil.

---

## 7. Status Checks (CI), Regras e Proteções

Repositórios podem exigir que certos checks passem antes do merge (ex.: testes, lint, build). Outros requisitos comuns:
- Aprovação mínima de revisores (ex.: 1 ou 2 aprovações)
- Sem mudanças solicitadas pendentes
- Branch protegida (impede push direto na `main`)
- Commits assinados (GPG/SSH) em projetos que exigem verificação de autoria

Se um check falhar, corrija localmente e envie novos commits. O GitHub atualizará o PR automaticamente.

---

## 8. Conflitos em PRs

Se houver conflitos entre sua branch e a `main`, resolva-os localmente:

```bash
git fetch origin
git switch feature/login-form
git rebase origin/main      # ou git merge origin/main

# Resolver arquivos marcados com <<<<<<<, =======, >>>>>>>
git add arquivos_resolvidos
git rebase --continue       # ou git commit (se usou merge)
git push --force-with-lease
```

No GitHub, use “Resolve conflicts” apenas para conflitos simples; para casos mais complexos, prefira resolver localmente.

---

## 9. Boas Práticas de PR

- Escopo reduzido: um PR por assunto. Evite PRs gigantescos.
- Título e descrição claros; explique “por que” e “o que”.
- Commits coerentes e mensagens significativas.
- Inclua testes e atualize docs quando aplicável.
- Capture métricas/prints para mudanças visuais.
- Marque revisores certos e aceite feedback com objetividade.
- Sincronize com `main` com frequência para minimizar conflitos.

Checklist sugerido (cole na descrição do PR):

- [ ] Código compila e passa nos testes locais
- [ ] Cobertura de testes adequada para as mudanças
- [ ] Linters e formatadores executados (ex.: ESLint/Prettier)
- [ ] Documentação atualizada (README/CHANGELOG)
- [ ] Sem secrets, chaves ou dados sensíveis no código
- [ ] Verificado impacto de performance/segurança
- [ ] Migrações (se houver) testadas e reversíveis

---

## 10. GitHub CLI (opcional, avançado)

Se você utiliza a GitHub CLI (`gh`), dá para abrir e gerenciar PRs pelo terminal:

```bash
# Criar PR a partir da branch atual
gh pr create \
  --base main \
  --head feature/login-form \
  --title "feat(login): add basic login form" \
  --body-file .github/PULL_REQUEST_TEMPLATE.md

# Listar PRs abertos
gh pr list

# Ver PR específico
gh pr view 123 --web

# Fazer checkout da branch do PR
gh pr checkout 123

# Mesclar PR (se tiver permissões)
gh pr merge 123 --squash --delete-branch
```

---

## 11. Exercício Prático (mão na massa)

1) Crie uma branch `feature/readme-links` a partir da `main`.
2) Edite o `README.md` adicionando um link para este capítulo.
3) `git add`, `git commit` e `git push -u origin feature/readme-links`.
4) Abra um PR no GitHub para `main`.
5) Peça revisão para um colega e ajuste conforme feedback.
6) Faça merge usando “Squash and merge” e delete a branch.

---

## 12. Referências

- GitHub Docs — About pull requests: https://docs.github.com/en/pull-requests
- Pro Git Book — Branching and Merging: https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging
- Git — Rebase, Merge: `man git-rebase`, `man git-merge`

