# Chapter 13 - Git tags

## 1) Onde o Git armazena referências (refs)

- **Refs (branches e tags):**

  ```text
  .git/refs/heads/        # branches
  .git/refs/tags/         # tags (cada arquivo contém um SHA-1/SHA-256)
  ```

- **Refs empacotadas:**

  ```text
  .git/packed-refs
  ```

  Quando há muitas refs, o Git escreve pares `SHA refname` nesse arquivo para economizar inodes e melhorar performance.

## 2) Lightweight tag — o que é e onde fica

- **Criação:**

  ```bash
  git tag v1.2.3
  ```

- **Internamente:**
  - O Git cria o arquivo `.git/refs/tags/v1.2.3` contendo o SHA do commit apontado.
  - Não há objeto "tag" no banco de objetos; é apenas um ponteiro (como uma branch, porém normalmente imutável).
  - Se as refs estiverem empacotadas, a entrada pode ir para `.git/packed-refs`.

- **Como inspecionar:**

  ```bash
  git rev-parse refs/tags/v1.2.3
  git show-ref --tags
  cat .git/refs/tags/v1.2.3
  ```

  Se o arquivo não existir, confira o conteúdo de `.git/packed-refs`.

- **Conclusão:** lightweight é apenas um arquivo de ref apontando diretamente para o objeto alvo (tipicamente um commit).

## 3) Annotated tag — o que é e onde fica

- **Criação:**

  ```bash
  git tag -a v2.0.0 -m "Release 2.0"
  git tag -s v2.0.0 -m "Signed release"
  ```

- **Internamente:**
  - O Git cria um **objeto do tipo `tag`** em `.git/objects/...` contendo:
    - referência para o objeto alvo (commit, tree ou blob);
    - tipo do alvo (`commit`, `tree`, etc.);
    - `tagger` (autor, email, data);
    - mensagem da tag;
    - assinatura (se houver).
  - O arquivo `.git/refs/tags/v2.0.0` guarda o SHA do objeto `tag`, não do commit.
  - Cadeia: `refs/tags/v2.0.0` → SHA do objeto tag → commit alvo.

- **Como inspecionar:**

  ```bash
  git rev-parse refs/tags/v2.0.0
  git cat-file -p <sha-do-objeto-tag>
  git cat-file -p <sha-do-objeto-tag> | sed -n '1,10p'
  git show v2.0.0
  ```

- **Conclusão:** annotated combina objeto tag + ref apontando para ele; carrega metadados e pode ser assinado.

## 4) Observações sobre tipos de alvo e resolução de refs

- Um objeto tag geralmente aponta para um `commit`, mas pode referenciar qualquer objeto (`tree`, `blob`).
- Quando comandos esperam um commit, o Git faz *dereference* automático (se a ref aponta para objeto tag, ele resolve até o commit).
- **Comandos úteis:**

  ```bash
  git rev-parse v2.0.0
  git rev-parse v2.0.0^{commit}
  git rev-list -n 1 v2.0.0
  ```

## 5) Packed-refs e por que aparecem

- O Git pode mover refs individuais de `.git/refs/tags/*` para `.git/packed-refs` quando há muitas tags.
- O arquivo `packed-refs` guarda pares `SHA refname` e pode incluir linhas `^<sha>` (peel) para annotated tags.
- Pushes em massa e ferramentas de gerenciamento de histórico costumam gerar `packed-refs`.

- **Inspecionar:**

  ```bash
  cat .git/packed-refs
  ```

## 6) Assinatura (GPG) de tags

- **Criar tag assinada:**

  ```bash
  git tag -s v2.1.0 -m "Release"
  ```

- **Verificar assinatura:**

  ```bash
  git tag -v v2.1.0
  ```

Assinaturas só existem em annotated tags, pois dependem do objeto tag.

## 7) Pushing tags — o que vai ao remoto

- **Push de uma tag específica:**

  ```bash
  git push origin v1.2.3
  ```

  - Lightweight: envia ref apontando direto para o commit.
  - Annotated: envia o objeto tag e a ref apontando para ele.

- **Push de todas as tags locais:**

  ```bash
  git push origin --tags
  ```

- **Deletar tag no remoto:**

  ```bash
  git push origin :refs/tags/v1.2.3
  ```

O remoto preserva a distinção annotated vs lightweight. Se já existir uma tag homônima com SHA diferente, o push será rejeitado (salvo se forçado, recurso muitas vezes desabilitado).

## 8) Exemplos práticos de inspeção

- **Listar todas as tags com metadados:**

  ```bash
  git for-each-ref --format="%(refname) %(objecttype) %(objectname) %(taggername)" refs/tags
  ```

- **Mostrar conteúdo do objeto tag:**

  ```bash
  git cat-file -p $(git rev-parse refs/tags/v2.0.0)
  ```

- **Checar se a tag é lightweight ou annotated:**

  ```bash
  sha=$(git rev-parse refs/tags/v1.2.3)
  git cat-file -t "$sha"
  ```

  Resultado `commit` → lightweight; resultado `tag` → annotated.

- **Resolver o commit final de qualquer tag:**

  ```bash
  git rev-parse v2.0.0^{commit}
  ```

## 9) Por que escolher annotated vs lightweight

- **Annotated:**
  - Metadados, assinatura opcional, histórico auditável, ideal para releases públicas.
  - Overhead mínimo (mais um objeto no banco).
- **Lightweight:**
  - Simples, rápido, útil para marcações locais temporárias.
  - Sem autoria, assinatura ou mensagem.

Para releases destinadas a terceiros, prefira **annotated (de preferência assinadas)**. Para atalhos locais, lightweight é suficiente.


