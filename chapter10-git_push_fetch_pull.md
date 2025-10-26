# Capítulo 10 — Git Push, Fetch e Pull

## 1. Introdução

Após versionar e commitar suas alterações localmente, surge a necessidade de sincronizar seu repositório com o servidor remoto (ex: GitHub).  
Os comandos `git push`, `git fetch` e `git pull` são fundamentais para esse fluxo de colaboração e sincronização.

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

# Verifica diferenças entre local e remoto
$ git log HEAD..origin/main
```

**Vantagens do fetch:**
- Você mantém controle manual sobre o momento de mesclar alterações.
- É útil para evitar conflitos inesperados durante o desenvolvimento.

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

## 7. Referências

- [Pro Git Book — Capítulo sobre Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)
- `man git-push`
- `man git-fetch`
- `man git-pull`
