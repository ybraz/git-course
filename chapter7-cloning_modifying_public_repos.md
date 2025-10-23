# Complete Git Course

## Chapter 7 - Cloning, Exploring, and Modifying Public Repositories

### Cloning Repositories

Existe a opção de download no Github, porém nesta opção apenas o conteúdo do Working Directory é baixado. O repositório Git (pasta .git com todos arquivos e branches históricos) não serão baixados. A opção de clonar é a única que permite o repositório completo para a máquina local.

O comando `git clone` serve para **criar uma cópia local exata de um repositório remoto**. Ele copia todos os commits, branches, tags e histórico do repositório original para sua máquina, permitindo que você trabalhe localmente sem alterar diretamente a origem. É uma das formas mais comuns de iniciar um projeto existente.

- `git clone <url>`  
  Clona o repositório remoto para um diretório local com o mesmo nome do projeto.  
  **Exemplo:**  
  `git clone https://github.com/user/projeto.git`

- `git clone <url> <nome-diretorio>`  
  Permite especificar o nome do diretório local onde o repositório será clonado.  
  **Exemplo:**  
  `git clone https://github.com/user/projeto.git meu-projeto`

- `git clone --branch <nome-branch>` *(ou `-b`)*  
  Clona o repositório já posicionando você em uma branch específica.  
  **Exemplo:**  
  `git clone -b develop https://github.com/user/projeto.git`

- `git clone --single-branch`  
  Clona apenas a branch especificada, ignorando o histórico das outras. Útil para economizar espaço e tempo.  
  **Exemplo:**  
  `git clone -b main --single-branch https://github.com/user/projeto.git`

- `git clone --depth <n>`  
  Faz um clone “raso”, trazendo apenas os últimos *n* commits, em vez de todo o histórico.  
  **Exemplo:**  
  `git clone --depth 1 https://github.com/user/projeto.git` *(apenas o commit mais recente)*

- `git clone --recurse-submodules`  
  Clona também os submódulos (outros repositórios incluídos dentro do projeto).  
  **Exemplo:**  
  `git clone --recurse-submodules https://github.com/user/projeto.git`

Um pack file no Git é, essencialmente, um formato comprimido e otimizado usado para armazenar objetos Git (commits, trees, blobs e tags) de forma eficiente.

Quando você clona um repositório, o Git não copia cada objeto individualmente — ele envia um ou mais arquivos .pack que contêm um “pacote” desses objetos comprimidos e um .idx correspondente que funciona como um índice para buscá-los rapidamente.

No diretório .git/objects/pack você normalmente encontra:
- pack-<hash>.pack → contém os objetos comprimidos.
- pack-<hash>.idx → índice para localizar rapidamente objetos dentro do pack.
- (opcional) pack-<hash>.rev → índice auxiliar para navegação reversa.

Por que isso existe
- Eficiência de rede: em vez de transferir milhares de pequenos arquivos, transfere-se um pacote binário só.
- Eficiência de disco: o pack usa delta-compression — ou seja, armazena diferenças entre objetos para economizar espaço.
- Velocidade de lookup: o .idx permite que o Git encontre objetos rapidamente sem descomprimir tudo.

Se você rodar:
```bash
git verify-pack -v .git/objects/pack/pack-xxxxxxxxxxxx.idx
```
você verá uma listagem de commits, trees e blobs com tamanhos e offsets — ou seja, os objetos que vieram nesse “pacote” inicial.

Quando você cria novos commits, eles primeiro ficam como arquivos soltos em `.git/objects/<xx>/`. Periodicamente, ou após certos comandos (como git gc), o Git reempacota esses objetos novamente num novo .pack para otimizar.

O comando git unpack-objects é o complemento “inverso” do empacotamento de objetos (git pack-objects).
Enquanto os pack files comprimem vários objetos em um único arquivo binário para eficiência, o unpack-objects desempacota esse conteúdo, recriando cada objeto individualmente no diretório .git/objects/.

```bash
git unpack-objects < .git/objects/pack/pack-xxxxxxxx.pack
```

ou 

```bash
cat arquivo.pack | git unpack-objects
```

Cada objeto armazenado no .pack (commits, trees, blobs, tags) é descomprimido e salvo individualmente em .git/objects/ no formato SHA-1 tradicional. O comando não atualiza nenhuma referência (branch, tag) — apenas descomprime os objetos para que fiquem acessíveis no formato “solto”.

### Contribuindo Com Repositórios Públicos

1. **Forkar o repositório**
   - Clique em “Fork” no repositório original para criar uma cópia na sua conta.

2. **Clonar o fork**
   - `git clone https://github.com/<seu-usuario>/<repo>.git`

3. **Criar uma branch**
   - `git switch -c nome-da-branch`

4. **Fazer alterações**
   - Edite, adicione arquivos e teste localmente.

5. **Adicionar e commitar**
   - `git add .`
   - `git commit -m "Mensagem clara da mudança"`

6. **Fazer push para o fork**
   - `git push origin nome-da-branch`

7. **Abrir Pull Request**
   - No GitHub, vá ao seu fork → clique em “Compare & pull request” → descreva suas mudanças e envie.

8. *(Opcional, mas recomendado)* **Sincronizar com upstream**
   - `git remote add upstream https://github.com/<original>/<repo>.git`
   - `git fetch upstream`
   - `git merge upstream/main` ou `git rebase upstream/main`


### Git Diff

O comando `git diff` mostra as diferenças entre versões de arquivos em um repositório Git. Ele compara conteúdo linha a linha, destacando **o que foi adicionado, removido ou modificado** em relação a outro estado do projeto — seja entre o *working directory* e a *staging area*, entre commits, branches ou tags. Ele não altera nada no repositório: serve apenas como ferramenta de inspeção para entender o impacto das mudanças antes de validá-las.

Ver alterações não adicionadas ao staging
`git diff`

Comparar staging area com o último commit
`git diff --cached`

Comparar duas branches
`git diff main feature/login`

Comparar dois commits específicos
`git diff abc123 def456`

Comparar alterações em um arquivo específico
`git diff main feature/login -- src/app.js`

Mostrar apenas nomes dos arquivos modificados
`git diff --name-only`

Mostrar estatísticas resumidas de mudanças
`git diff --stat`

Ignorar espaços em branco (útil para mudanças de formatação)
`git diff -w`
