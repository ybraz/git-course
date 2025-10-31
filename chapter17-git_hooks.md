# Chapter 17 - Git Hooks

## 1. O que são Git Hooks

Os **Git Hooks** são *gatilhos automáticos* que permitem executar scripts  
**em resposta a eventos do Git** — como commits, merges, pushes ou rebases.

Eles tornam o repositório **auto-disciplinado**, inserindo regras e automações  
sem depender apenas da memória ou boa vontade dos desenvolvedores.

Exemplo de usos clássicos:
- Validar mensagens de commit antes de aceitar (`commit-msg`)
- Rodar testes ou linters antes do commit (`pre-commit`)
- Enviar notificações após push (`post-push`)
- Bloquear merges se o código não passar por revisão (`pre-receive`)

---

## 2. Quando usar hooks

Use hooks para automatizar verificações e tarefas frequentes: validar commits, rodar linters/testes rápidos, bloquear pushes indevidos, sincronizar ações pós-merge/push e aplicar políticas de contribuição.

---

## 3. Onde os hooks vivem

Cada repositório Git tem um diretório de hooks:

```
.git/hooks/
```

Ao inicializar um repositório (`git init`), o Git cria scripts de exemplo, como:

```
applypatch-msg.sample
pre-commit.sample
commit-msg.sample
post-update.sample
pre-push.sample
```

Esses arquivos terminam em `.sample` e estão desabilitados por padrão.

Para ativar um hook, basta **remover a extensão `.sample`** e torná-lo executável:

```bash
mv .git/hooks/pre-commit.sample .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

---

## 4. Como funcionam internamente

- Hooks são scripts (geralmente *bash*, mas podem ser Python, Ruby, Node, etc.).
- O Git chama esses scripts **automaticamente** no momento certo.
- Eles são executados **localmente**, dentro do contexto do repositório.
- O *código de saída* (exit code) determina o comportamento:
  - `0` → sucesso, Git continua o fluxo.
  - `!= 0` → falha, Git interrompe o comando (ex: bloqueia o commit).

Os hooks **não são versionados por padrão**, pois vivem dentro de `.git/`.  
Mas podem ser compartilhados via diretórios externos (ver seção 8).

---

## 5. Tipos principais de hooks (client-side)

1. **pre-commit**  
   Executado antes de o commit ser criado.  
   Usado para rodar linters, formatadores, testes rápidos ou bloqueios.

2. **prepare-commit-msg**  
   Executado antes da edição da mensagem de commit.  
   Pode inserir templates ou metadados automáticos.

3. **commit-msg**  
   Executado após a escrita da mensagem, mas antes da finalização.  
   Pode validar padrões (ex: formato do título, ticket ID).

4. **post-commit**  
   Executado depois que o commit foi criado.  
   Ideal para notificações, logs ou integrações (ex: enviar mensagem ao Slack).

5. **pre-push**  
   Rodado antes de enviar commits ao repositório remoto.  
   Usado para checar se os testes passam ou se o repositório remoto está atualizado.

6. **post-merge**  
   Executado após merges bem-sucedidos.  
   Pode restaurar configs locais, regenerar dependências, ou rodar migrações.

7. **pre-rebase**  
   Executado antes de um rebase; pode bloquear rebases indevidos ou validar branches.

---

## 6. Hooks do lado do servidor (server-side)

Usados em repositórios remotos (GitLab, GitHub Enterprise, servidores Git).  
Servem para aplicar **políticas de segurança e governança**.

Principais exemplos:

- **pre-receive** → roda antes de aceitar o push.  
  Pode rejeitar pushes que violam convenções (ex: commits sem assinatura).

- **update** → executado por branch recebida.  
  Permite controle granular sobre quais branches podem ser atualizadas.

- **post-receive** → roda após receber commits.  
  Usado para CI/CD: acionar pipelines, enviar notificações, atualizar sistemas.

Esses hooks vivem em:

```
<repo>.git/hooks/
```

no servidor remoto (não acessíveis pelo cliente comum).

---

## 7. Exemplos práticos

### a) Validação de estilo antes do commit

Arquivo `.git/hooks/pre-commit`:

```bash
#!/bin/bash
echo "🧹 Verificando estilo de código..."
black --check .
if [ $? -ne 0 ]; then
  echo "❌ Formatação incorreta. Rode 'black .' antes de commitar."
  exit 1
fi
echo "✅ Código formatado corretamente."
exit 0
```

### b) Impedir commits sem mensagem descritiva

Arquivo `.git/hooks/commit-msg`:

```bash
#!/bin/bash
MSG_FILE=$1
MSG=$(cat $MSG_FILE)
if [[ ${#MSG} -lt 10 ]]; then
  echo "❌ Mensagem de commit muito curta!"
  exit 1
fi
exit 0
```

### c) Bloquear push para main

Arquivo `.git/hooks/pre-push`:

```bash
#!/bin/bash
branch="$(git rev-parse --abbrev-ref HEAD)"
if [ "$branch" = "main" ]; then
  echo "🚫 Push direto para main bloqueado!"
  exit 1
fi
exit 0
```

---

## 8. Compartilhando hooks entre devs

Como os hooks locais **não são versionados por padrão**, é comum usar diretórios externos.

### a) Diretório customizado

Crie uma pasta versionada no projeto:

```bash
mkdir -p .githooks/
```

Adicione seus scripts lá e aponte o Git para ela:

```bash
git config core.hooksPath .githooks
```

Agora todos os hooks dentro de `.githooks/` serão executados automaticamente.  
Isso permite compartilhamento e versionamento junto ao código.

---

## 9. Verificando configuração e execução

Ver caminho atual dos hooks:

```bash
git config core.hooksPath
```

Rodar um hook manualmente (para testar):

```bash
bash .git/hooks/pre-commit
```

Ver histórico de execução (não há log automático por padrão, mas pode-se registrar):

```bash
echo "Hook executado em $(date)" >> .git/hooks/log.txt
```

---

## 10. Segurança e boas práticas

- Sempre use `chmod +x` para garantir execução.
- Escreva scripts idempotentes — que não quebrem se rodarem mais de uma vez.
- Use `set -e` em Bash para abortar no primeiro erro.
- Para hooks críticos, mostre mensagens claras e coloridas (`echo`, `tput`, `printf`).
- Nunca use hooks para tarefas longas ou interativas (podem travar commits).
- Versione hooks com `core.hooksPath`, nunca manualmente dentro de `.git/hooks`.

---

## 11. Diagnóstico de problemas

Se um hook não rodar:
- Verifique permissões (`chmod +x`).
- Teste o script manualmente com `bash nome-do-hook`.
- Confirme o caminho configurado (`git config core.hooksPath`).
- Adicione `set -x` no início do script para depuração detalhada.

---

## 12. Dicas finais

- Mantenha hooks rápidos; delegue tarefas pesadas para CI
- Compartilhe via `core.hooksPath` e versionamento
- Mensagens claras em caso de falha ajudam a adoção
