# Chapter 15 - Ignoring Files in Git

## 1. Conceito

O arquivo `.gitignore` define padrões para o Git ignorar arquivos e pastas que não devem ser versionados (builds, caches, logs, artefatos temporários, etc.). Mantém o repositório focado apenas no que é necessário para construir e entender o projeto.

---

## 2. Status dos arquivos no Git

Quando você executa `git status`, cada arquivo do seu diretório de trabalho pode estar em um dos seguintes estados:

- **Untracked** → o arquivo existe no sistema de arquivos, mas o Git nunca o adicionou (`git add`).  
- **Tracked** → o arquivo está sendo monitorado (está em staging ou já comitado).  
- **Ignored** → o Git sabe que o arquivo existe, mas o ignora por regra (definida em `.gitignore`).

Fluxo típico:

1. Cria um arquivo novo → aparece como *untracked*.  
2. Adiciona com `git add` → vira *tracked*.  
3. Define no `.gitignore` → passa a ser *ignored*, desde que ainda não tenha sido comitado.

---

## 3. Regras básicas de `.gitignore`

Um arquivo `.gitignore` contém padrões de caminhos (glob patterns).  
Regras simples e diretas:

- Linhas em branco ou começando com `#` são comentários.
- Um padrão simples (ex: `*.log`) ignora todos os arquivos com aquela extensão.
- `/` no início indica caminho relativo à raiz do repositório.
- `/build/` ignora a pasta `build` e todo o seu conteúdo.
- `!` inverte a regra (ex: `!important.log` mantém o arquivo mesmo se `*.log` estiver ignorado).
- `**` faz correspondência recursiva (ex: `**/__pycache__/` ignora todos os `__pycache__` em qualquer subpasta).

Exemplo simples:

````gitignore
# Logs e temporários
*.log
*.tmp

# Diretórios de build
/dist/
/build/

# Pastas de cache do Python
__pycache__/
*.py[cod]
````

---

## 4. Ignorando arquivos já comitados

Se você acidentalmente comitou algo que deveria ser ignorado (ex: `.env`, `venv/`, `__pycache__/`), o `.gitignore` **não o remove automaticamente do controle de versão**.  
O Git apenas deixa de rastrear novos arquivos que correspondam à regra.

Para parar de rastrear um arquivo já comitado, mas mantê-lo no disco:

````bash
git rm --cached arquivo.txt
````

E então fazer novo commit:

````bash
git commit -m "Remove arquivo.txt do controle de versão"
````

A partir daí, o arquivo seguirá as regras do `.gitignore`.

Se quiser fazer isso para uma pasta inteira:

````bash
git rm -r --cached venv/
git commit -m "Remove venv do versionamento"
````

---

## 5. Forçar commit de arquivos ignorados

Se você quiser **comitar manualmente** algo que está no `.gitignore` (caso excepcional):

````bash
git add -f arquivo.log
````

O `-f` (“force”) ignora temporariamente as regras do `.gitignore` e adiciona o arquivo ao staging.  
Use com cautela — normalmente é um indício de que algo está errado na configuração do `.gitignore`.

---

## 6. Inspecionar regras e diagnósticos

Saber se um arquivo está sendo ignorado:

````bash
git check-ignore -v caminho/do/arquivo
````

O `-v` mostra qual linha do `.gitignore` causou a exclusão.  
Excelente para depurar conflitos em regras complexas.

Listar todos os arquivos ignorados no projeto:

````bash
git status --ignored
````

---

## 7. Template básico de `.gitignore` para projetos Python

Templates de .gitignore podem ser encontrados em: gitignore.io 

Um `.gitignore` funcional e limpo para Python deve cobrir:

````gitignore
# Compilação e cache
__pycache__/
*.py[cod]
*$py.class

# Virtual environments
venv/
env/
.env/
.venv/

# Logs e dados temporários
*.log
*.pot
*.pyproj
*.tmp
*.bak

# Arquivos de IDEs
.vscode/
.idea/
*.sublime-project
*.sublime-workspace

# Distribuição e build
build/
dist/
*.egg-info/
.eggs/

# Testes e cobertura
.pytest_cache/
htmlcov/
.coverage

# Configurações locais
.DS_Store
.mypy_cache/
.ruff_cache/
.ipynb_checkpoints/
````

Esse template cobre:
- tudo o que é gerado automaticamente,
- tudo que é específico de ambiente,
- tudo que é transitório ou pessoal.

---

## 8. Dicas finais

- Crie o `.gitignore` no início do projeto e mantenha-o curto e objetivo.
- Prefira templates por linguagem/plataforma e ajuste ao seu contexto.
- Se algo já foi comitado por engano, use `git rm --cached` e adicione a regra correspondente.
