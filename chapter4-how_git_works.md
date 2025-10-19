# Complete Git Course

## Chapter 4 - How Git Works Under the Hood

### Iniciar o repositório

Para criar um novo repositório Git:

```bash
$ git init
```

O novo repositório Git estará inicialmente vazio, mesmo que no diretório que o repositório foi inicializado contenha arquivos e/ou diretórios.

Após digitar o comando `git init` um novo diretório oculto `.git` será criado automaticamente. 

```bash
$ git init
Initialized empty Git repository in /Users/yuribraz/Code/git-course/.git/
```

### Diretório .git

> **Atenção**: Nunca edite manualmente arquivos no diretório `.git`.

Conteúdo do diretório `.git` criado na inicialização do repositório:

```bash
$ cd .git

$ ls -la
total 24
drwxr-xr-x@  9 yuribraz  staff  288 Oct 17 06:22 .
drwxr-xr-x   8 yuribraz  staff  256 Oct 17 06:20 ..
-rw-r--r--@  1 yuribraz  staff   21 Oct 17 06:20 HEAD
-rw-r--r--@  1 yuribraz  staff  137 Oct 17 06:20 config
-rw-r--r--@  1 yuribraz  staff   73 Oct 17 06:20 description
drwxr-xr-x@ 16 yuribraz  staff  512 Oct 17 06:20 hooks
drwxr-xr-x@  3 yuribraz  staff   96 Oct 17 06:20 info
drwxr-xr-x@  4 yuribraz  staff  128 Oct 17 06:20 objects
drwxr-xr-x@  4 yuribraz  staff  128 Oct 17 06:20 refs
```

Descrição:

HEAD
- Aponta para a branch ou commit atualmente “checado” (checked out).
- Normalmente contém algo como `ref: refs/heads/main`, indicando que você está na branch `main`.
- É a referência central que diz ao Git “onde você está” no histórico.

config
- Arquivo de configuração local do repositório.
- Define informações como URL do remote, branch padrão e opções personalizadas.
- Complementa as configurações globais (`~/.gitconfig`).

description
- Usado por ferramentas como GitWeb para exibir uma descrição amigável do repositório.
- Não afeta o funcionamento interno do Git.

hooks/
- Diretório com scripts executados automaticamente em certos eventos do Git (ex: pre-commit, post-merge).
- Inicialmente contém arquivos `.sample` comentados.
- Permite automatizar validações e integrações no fluxo de versionamento.

info/
- Contém configurações adicionais, incluindo o arquivo `exclude`.
- `exclude` funciona como um `.gitignore` local que não é versionado.
- Útil para ignorar arquivos específicos apenas na sua máquina.

objects/
- Onde o Git armazena todos os objetos internos: commits, árvores (trees), blobs (conteúdo dos arquivos) e tags.
- É o núcleo do Git: cada versão e histórico são representados aqui.
- Os objetos são comprimidos e organizados em subpastas pelo hash.
- commits — registros de mudanças que apontam para uma árvore, com metadados (autor, data, mensagem) e pais (histórico).
- árvores (trees) — diretórios que mapeiam nomes para blobs e/ou outras trees, formando a estrutura de pastas.
- blobs — conteúdo bruto dos arquivos (bytes) sem nome nem metadados de caminho.
- tags — rótulos que apontam para um objeto (geralmente um commit), opcionais com assinatura e mensagem.

refs/
- Contém referências para branches, tags e remotes.
- Por exemplo: `refs/heads/main` aponta para o commit mais recente da branch main.
- É a base que permite ao Git entender a topologia dos branches.

### Low-Level Commands

Vamos discutir alguns comandos internos do Git:

- *git hash-object* — Calcula e retorna o hash SHA-1/SHA-256 de um arquivo, podendo também armazená-lo no banco de objetos.  
Exemplo (calcula o hash e grava como blob em `.git/objects/`): `git hash-object -w arquivo.txt`

- *git cat-file* — Exibe informações detalhadas ou conteúdo bruto de um objeto Git pelo hash.  
Exemplo -p (pretty-print): `git cat-file -p <hash_do_objeto>`

- *git mktree* — Lê uma descrição textual de uma árvore a partir da entrada padrão e cria um objeto tree no banco de objetos.  
Exemplo: `git mktree < tree.txt` → cria uma árvore com base na estrutura definida em `tree.txt` e retorna o hash gerado.

Exemplos de uso:

```bash
$ echo "Hello, Git" | git hash-object --stdin
b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e

echo "Hello, Git" | git hash-object --stdin -w
b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e

ls .git/objects/b7/
aec520dec0a7516c18eb4c68b64ae1eb9b5a5e

# Concatenando o nome do diretório criado (b7) com o arquivo criado, temos o hash do objeto
# O objeto foi criado APENAS no repositório Git no diretório `.git/objects`

git cat-file -p b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e
Hello, Git

```

**git hash-object**
- Permite inserir conteúdo no banco de objetos do Git **sem necessariamente adicioná-lo ao diretório de trabalho ou ao índice**.
- Quando usado com `-w`, o arquivo vira um *blob* armazenado em `.git/objects/`.
- Porém, ele **não cria referência** (branch, commit ou árvore) a esse objeto — por isso o arquivo “não aparece” no diretório do projeto.
- Dessa forma, o `git hash-object` apenas cria um blob no banco de objetos local (.git/objects/). Ele não cria referências (como branches, tags ou commits).
- O Git só envia objetos para o remoto quando eles fazem parte de um commit referenciado por alguma branch ou tag que você faz push.
- Para criar um objeto baseado em um arquivo existente: `git hash-object <filename> -w

Caso de uso
- Criar objetos *blobs* para testes, auditoria ou manipulação avançada de objetos Git (por exemplo, ao construir árvores e commits manualmente).
- Scripts ou ferramentas que geram conteúdo dinamicamente e querem armazenar no repositório sem sujar o *working directory*.
- Construir manualmente a história de um repositório (ex: replays, importações, reconstruções).

Exemplo prático
```bash
$echo "conteúdo secreto" | git hash-object -w --stdin
# → retorna o hash, mas nada muda no diretório

$ git cat-file -p <hash>
# → mostra o conteúdo armazenado no banco de objetos

$ git hash-object chapter1-introduction.md -w
70ddeca589dcf8c633a7002f8b740701da0817bd
```

**git cat-file**
- Exibe informações ou conteúdo bruto de um objeto Git já armazenado no banco de objetos.
- Permite inspecionar blobs, trees, commits ou tags a partir do hash.
- É uma ferramenta de “raio X” do Git: mostra o que está por trás das referências visíveis.
- -p Conteúdo do objeto
- -s Tamanho do objeto
- -t Tipo do objeto (blob, tree, commit ou tag)

Caso de uso
- Verificar o conteúdo exato de um arquivo armazenado como blob, mesmo que não esteja no diretório de trabalho.
- Investigar commits, árvores ou tags internamente (útil em auditorias, recuperação ou aprendizado profundo de Git).
- Validar a estrutura de objetos antes de criar referências (branches/commits).

Exemplo prático
```bash
$ git cat-file -p <hash>
# → mostra conteúdo ou estrutura do objeto

$ git cat-file -t <hash>
# → mostra o tipo do objeto (blob, tree, commit, tag)

```

**git mktree**
- Lê uma descrição textual de uma árvore (via STDIN ou arquivo) e cria um objeto tree no banco de objetos.
- Não altera o diretório de trabalho nem cria commits — apenas registra a estrutura da árvore internamente.
- É usado para construir manualmente árvores que podem ser ligadas a commits depois.

Caso de uso
- Criar uma árvore a partir de blobs já existentes (por exemplo, gerados com `git hash-object`).
- Montar a estrutura de diretórios e arquivos sem precisar criar fisicamente esses arquivos no working directory.
- Automatizar reconstruções ou importações de histórico em scripts e ferramentas avançadas.

Exemplo prático
```bash
echo "100644 blob <hash_do_blob>    nome_arquivo.txt" | git mktree
# → retorna o hash da tree criada

git cat-file -p <hash_da_tree>
# → mostra a estrutura da árvore criada
```

### Hash Functions

A database do Git é formada de pares de chave-valor, similar a objetos JSON.

**JSON**

Um objeto JSON tem o seguinte formato, os onde as chaves devem ser únicas, mas os valores podem repetir:

```JSON
{
    "id": "1234567",
    "name": "Mike",
    "age": 25,
    "city": "New York",
    "hobbies": ["skateboarding", "running"]
}
```

Uma hash function é uma função que transforma dados de qualquer tamanho em uma sequência fixa de bits (hash), de forma determinística e difícil de reverter.
- Determinística — a mesma entrada sempre gera a mesma saída.  
- Saída de tamanho fixo — independentemente do tamanho da entrada.  
- Unidirecional — difícil (ou inviável) reverter o hash para o valor original.  
- Sensível — pequenas mudanças na entrada alteram completamente a saída.  
- Uniforme — distribui os valores resultantes de forma ampla e imprevisível.  
- Eficiente — cálculo rápido mesmo para dados grandes.  
- Resistente a colisões — idealmente, duas entradas diferentes não produzem o mesmo hash.

```bash
# Exemplo usando SHA-256 no macOS
shasum -a 256 arquivo.txt
```

O Git usa **SHA-1** (160 bit) como função de hash padrão para identificar objetos (commits, trees, blobs e tags).
- 160 bits em Base 2 e 40 caracteres base 16 (hexadecimal): `c9d5d04925b93d2fb99c73ab2b5869bde7405ca4`
- Hexadecimal utiliza os caracteres [0 1 2 3 4 5 6 7 8 9 A B C D E F]
- Em versões mais recentes, ele também oferece suporte opcional a **SHA-256**, para aumentar a segurança.  
- Esses hashes são a “coluna vertebral” da integridade dos dados no repositório.
- Atualmente, o GitHub ainda não suporta SHA-256 em produção para repositórios hospedados.
- A única forma é criar um novo repositório com SHA-256 já habilitado:

```bash
$ git init --object-format=sha256 novo-repo

$ git config extensions.objectformat
# → sha256
```

O Git não tem um limite rígido de arquivos — ele pode armazenar **milhões de objetos**, limitado apenas por espaço em disco e desempenho.

A chance de colisão de hash é extremamente baixa — para SHA-1 é cerca de 1 em 2¹⁶⁰ e para SHA-256 é 1 em 2²⁵⁶, ou seja, **praticamente nula na prática**.

### Git objects

Um objeto git é formado pelo:
- Conteúdo
- Tipo
- Tamanho
- Delimitador (\0)

O hash é realizado em cima desse conjunto, por isso se realizar apenas o hash direto, com hashsum, os resultados são diferentes.

Os objetos no Git são armazenados em um formato binário comprimido, portanto não podem ser lidos com um comando `cat` normal.
- Pode, entretanto, ser lido utilizando a `zlib` em python para descomprir objetos Git.

```bash
$ echo "blob 11\0Hello, Git"
blob 11Hello, Git

$ echo "blob 11\0Hello, Git" | shasum
b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e  -
# produziu o mesmo sha1 que o Git

$ echo "Hello, Git" | git hash-object --stdin
b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e

$ echo "Second file in Git repository" | git hash-object --stdin -w
4400aae52a27341314f423095846b1f215a7cf08

$ git cat-file -t 4400aae52a27341314f423095846b1f215a7cf08              
blob

$ git cat-file -s 4400aae52a27341314f423095846b1f215a7cf08
30

$ git cat-file -p 4400aae52a27341314f423095846b1f215a7cf08
Second file in Git repository

$ echo "blob 30\0Second file in Git repository" | shasum
4400aae52a27341314f423095846b1f215a7cf08  -
```

### Trees 

Objetos Git (tipo Blob) não armazenam o nome do arquivo, apenas o tipo, tamanho e conteúdo. 

Trees são a representação de diretórios no Git. Cada árvore pode conter objetos (blobs) ou outras árvores. Elas são compostas dos seguintes itens:
- permissões (ex: 100644)
- tipo (blob ou tree)
- SHA-1 do objeto
- nome (ex: package.json)

Existem seis tipos de permissões possíveis, sendo que as duas primeiras são as mais comuns:
- 040000: Diretório
- 100644: Arquivo comum
- 100755: Arquivo executável
- 120000: Link simbólico (symlink)
- 160000: Submódulo Git (gitlink)

> **Importante**: essas permissões não controlam o acesso real ao arquivo no seu sistema operacional. Elas apenas indicam ao Git como reconstruir a árvore de diretórios ao fazer um checkout.

Um gitlink não armazena conteúdo do diretório. Ele armazena apenas o SHA-1 do commit do submódulo. Quando você faz git checkout, o Git não recria arquivos — ele apenas marca que, naquele caminho, deve haver um repositório externo vinculado àquele commit específico.

```bash
$ find .git/objects -type f
.git/objects/44/00aae52a27341314f423095846b1f215a7cf08
.git/objects/b7/aec520dec0a7516c18eb4c68b64ae1eb9b5a5e
.git/objects/70/ddeca589dcf8c633a7002f8b740701da0817bd


$ cat ../temp-tree.txt 
100644 blob 4400aae52a27341314f423095846b1f215a7cf08	file1.txt
100644 blob b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e	file2.txt
100644 blob 70ddeca589dcf8c633a7002f8b740701da0817bd	file3.txt

$ cat ../temp-tree.txt | git mktree
2ce4ec19223e7f1b8a460f18b7335343de7ff471

$ git cat-file -p 2ce4ec19223e7f1b8a460f18b7335343de7ff471
100644 blob 4400aae52a27341314f423095846b1f215a7cf08	file1.txt
100644 blob b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e	file2.txt
100644 blob 70ddeca589dcf8c633a7002f8b740701da0817bd	file3.txt

$ git cat-file -t 2ce4ec19223e7f1b8a460f18b7335343de7ff471
tree

$ git cat-file -s 2ce4ec19223e7f1b8a460f18b7335343de7ff471
111
```

### Git Areas

Existem três áreas onde arquivos e diretórios podem existir no Git:
- **Working Directory**: É onde você trabalha de fato — os arquivos estão no sistema de arquivos local, ainda não rastreados pelo Git ou com modificações não preparadas para commit.
- **Staging Area (Index)**: É a zona intermediária — onde você seleciona quais mudanças quer incluir no próximo commit, funcionando como um “rascunho oficial” da sua próxima versão.
- **Git Repository**: É o banco de dados interno — onde ficam armazenados permanentemente os commits, árvores, blobs e histórico versionado.

Para recuperar arquivos do repositório Git (como os que criamos na mão) e coloca-los na Staging Area utilizamos o comando `git read-tree <hash>`.

```bash
$ find .git/objects -type f
.git/objects/44/00aae52a27341314f423095846b1f215a7cf08
.git/objects/b7/aec520dec0a7516c18eb4c68b64ae1eb9b5a5e
.git/objects/2c/e4ec19223e7f1b8a460f18b7335343de7ff471
.git/objects/70/ddeca589dcf8c633a7002f8b740701da0817bd

$ git cat-file -t b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e 
blob

$ git cat-file -t 4400                                    
blob

$ git cat-file -t 2ce4
tree

$ git read-tree 2ce4

$ git status
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   file1.txt
	new file:   file2.txt
	new file:   file3.txt

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	deleted:    file1.txt
	deleted:    file2.txt
	deleted:    file3.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	chapter1-introduction.md
	chapter2-installation.md
	chapter3-basic_shell.md
	chapter4-how_git_works.md
```

Podemos ler conteúdo da Staging Area utilizando o comando `git ls-files -s`. Sua saída é composta de:
- permissão 
- hash 
- 0 ou 1, indicando se foram modificados em relação ao que consta no repositório Git 
- nome do arquivo

```bash
$ git ls-files -s
100644 4400aae52a27341314f423095846b1f215a7cf08 0	file1.txt
100644 b7aec520dec0a7516c18eb4c68b64ae1eb9b5a5e 0	file2.txt
100644 70ddeca589dcf8c633a7002f8b740701da0817bd 0	file3.txt
```

O comando `git checkout-index -a` inclui os arquivos que estão na Staging Area no nosso Working Directory. O parâmetro `-a` diz para ler todos os arquivos que estão na Staging Area e colocá-los nos Working Directory.

```bash
$ git checkout-index -a

$ ls
chapter1-introduction.md	chapter4-how_git_works.md	file3.txt
chapter2-installation.md	file1.txt
chapter3-basic_shell.md		file2.txt
```


