# Capítulo 9 — Repositórios Remotos no Git

## 1. Conceito Teórico

Um **repositório remoto** é uma versão hospedada do seu repositório Git em um servidor externo (por exemplo: GitHub, GitLab, Bitbucket). 

Enquanto o repositório local está na sua máquina, o remoto permite **colaboração**, **backup** e **distribuição** do código.  

Na prática, ele funciona como um “espelho” sincronizado do repositório local, permitindo enviar e receber alterações.

> **Importante:** Git é um sistema distribuído. Isso significa que cada clone contém o histórico completo do projeto — o remoto é apenas um ponto de encontro, não a “única verdade”.

---

## 2. Operações Fundamentais

### 2.1 Adicionar um repositório remoto
`git remote add origin git@github.com:usuario/repositorio.git`  
Associa o repositório local a um remoto chamado `origin` (nome padrão).

---

### 2.2 Verificar repositórios remotos configurados
`git remote`  
Lista os remotos configurados.

`git remote -v`  
Lista remotos com suas URLs de **fetch** (obter) e **push** (enviar).

---

### 2.3 Obter atualizações do remoto
`git fetch origin`  
Baixa as atualizações, **mas não** as aplica na sua branch local.

`git pull origin main`  
Baixa as atualizações **e** realiza o merge na branch atual.

---

### 2.4 Enviar alterações para o remoto
`git push -u origin main`  
Envia os commits locais para a branch `main` no remoto.  
A flag `-u` define um **rastreamento**: nas próximas vezes, basta usar `git push`.

---

## 3. Gerenciamento de Remotos

### 3.1 Renomear um remoto
`git remote rename origin upstream`  
Altera o nome do remoto.

---

### 3.2 Remover um remoto
`git remote remove origin`  
Desassocia o repositório remoto.

---

### 3.3 Alterar a URL do remoto
`git remote set-url origin git@github.com:usuario/novo.git`  
Atualiza a URL do remoto sem precisar removê-lo.

---

## 4. Convenções e Boas Práticas

- `origin` → nome padrão do remoto principal.  
- `upstream` → nome usado quando você faz um fork de outro repositório.  
- Sempre sincronize (`fetch`/`pull`) **antes de dar push** para evitar conflitos.  
- Trabalhe com **branches** bem definidas e faça pull requests para integrar mudanças.

