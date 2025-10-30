# Chapter 13 - Git tags

## 1) Onde o Git armazena referências (refs)

- Refs (branches e tags) ficam em:
    .git/refs/heads/        # branches
    .git/refs/tags/         # tags (cada arquivo contém um SHA-1/ SHA-256 dependendo do Git)

- Quando houver **muitas refs**, o Git pode "empacotar" refs em:
    .git/packed-refs
  Nesse arquivo cada linha associa um SHA à ref (economia de inodes / performance).

## 2) Lightweight tag — o que é e onde fica

- Criação:
    git tag v1.2.3

- Internamente:
  - O Git cria um arquivo simples `.git/refs/tags/v1.2.3` cujo conteúdo é o SHA do commit apontado.
  - Não há objeto "tag" no banco de objetos. É só um ponteiro (como uma branch, porém normalmente imutável).
  - Se o repo estiver com refs empacotadas, o ponteiro pode acabar dentro de `.git/packed-refs`.

- Como inspecionar:
    git rev-parse refs/tags/v1.2.3
    git show-ref --tags
    cat .git/refs/tags/v1.2.3              # (às vezes o arquivo está empacotado, então ver packed-refs)

- Conclusão: lightweight = apenas um *ref file* apontando diretamente para o objeto alvo (tipicamente um commit).

## 3) Annotated tag — o que é e onde fica

- Criação:
    git tag -a v2.0.0 -m "Release 2.0"     # annotated
    git tag -s v2.0.0 -m "Signed release"  # annotated + assinatura

- Internamente:
  - O Git cria um **objeto** do tipo `tag` no banco de objetos (`.git/objects/...`). Esse objeto é um *tag object* que contém:
    - a referência para o objeto alvo (ex.: commit SHA),
    - o tipo do alvo (`commit`),
    - o autor/`tagger` (nome, email, data),
    - uma mensagem (mensagem de tag),
    - opcionalmente dados de assinatura (se assinado).
  - Em seguida o Git cria `.git/refs/tags/v2.0.0` contendo o SHA do **objeto tag**, não do commit.
  - Assim: refs/tags/v2.0.0 -> (SHA do objeto tag) -> (internamente aponta para o commit SHA).

- Como inspecionar:
    git rev-parse refs/tags/v2.0.0           # retorna SHA do objeto tag
    git cat-file -p <sha-do-objeto-tag>     # mostra o conteúdo do objeto tag (target, tagger, msg)
    git cat-file -p <sha-do-objeto-tag> | sed -n '1,10p'   # ver cabeçalho e target
    git show v2.0.0                          # mostra a mensagem e o commit

- Conclusão: annotated = *objeto tag* + ref apontando para esse objeto; carrega metadados (autor, data, msg) e pode ser assinado.

## 4) Observações sobre tipos de alvo e resolução de refs

- Um *tag object* normalmente aponta para um `commit`, mas **pode apontar para qualquer objeto** (tree, blob) — raro, mas possível.
- Quando você usa comandos que esperam um commit, Git faz _dereference_ automaticamente (i.e., se o ref aponta para um objeto tag, o Git resolve o objeto tag para o commit alvo).
- Comandos úteis:
    git rev-parse v2.0.0          # pode retornar SHA do objeto tag
    git rev-parse v2.0.0^{commit}# força a resolução ao commit alvo
    git rev-list -n 1 v2.0.0     # mostra o commit que aquela tag referencia

## 5) Packed-refs e por que aparecem

- Quando há muitas refs (muitos tags), o Git pode mover entradas individuais de `.git/refs/tags/*` para `.git/packed-refs`.
- O `packed-refs` armazena pares `SHA refname`, e pode ter uma linha `^<sha>` para anotated tag peeled lines.
- Ferramentas que gerenciam histórico (ou pushes em massa) frequentemente levam à criação de `packed-refs`.

Inspecionar:
    cat .git/packed-refs

## 6) Assinatura (GPG) de tags

- `git tag -s v2.1.0 -m "Release"` cria uma tag assinada. A assinatura fica no objeto tag.
- Verificar:
    git tag -v v2.1.0

Assinaturas só existem nas **annotated tags** (porque há um objeto tag onde a assinatura é armazenada).

## 7) Pushing tags — o que é enviado ao remoto

- `git push origin v1.2.3`
  - envia o ref `refs/tags/v1.2.3` ao remoto. O conteúdo (SHA) empurrado será o SHA do arquivo refs/tags local:
    - se lightweight: SHA do commit (o remoto terá refs/tags/v1.2.3 apontando direto para commit SHA).
    - se annotated: SHA do objeto tag (o remoto receberá o objeto tag e um ref apontando para ele).
- `git push origin --tags`
  - empurra **todas** as tags locais que não existem no remoto.
- Deletar tag no remoto:
    git push origin :refs/tags/v1.2.3
  (isso envia um "delete ref" request)

- Importante: o remoto vai armazenar a mesma distinção annotated vs lightweight, porque os objetos/refs são transmitidos. Se o remoto já tiver uma tag com mesmo nome mas SHA diferente, o push normalmente será rejeitado a menos que forçado (forçar sobrescrever tags remotas é possível, porém perigoso e muitas vezes desabilitado nos servidores).

## 8) Exemplos práticos de inspeção (comandos locais)

- Ver todas as refs de tag e o que apontam:
    git for-each-ref --format="%(refname) %(objecttype) %(objectname) %(taggername)" refs/tags

- Mostrar conteúdo do objeto tag:
    git cat-file -p $(git rev-parse refs/tags/v2.0.0)

- Ver se uma tag local é lightweight (aponta diretamente para commit SHA) ou annotated:
    # obtém SHA que o ref contém
    sha=$(git rev-parse refs/tags/v1.2.3)
    # verifica tipo do objeto
    git cat-file -t $sha

  Resultado:
    commit    -> lightweight (ref contém commit SHA)
    tag       -> annotated (ref contém objeto tag)

- Resolver commit final de uma tag (independente do tipo):
    git rev-parse v2.0.0^{commit}

## 9) Por que escolher annotated vs lightweight (consequências práticas)

- Annotated:
  - Prós: metadados, possibilidade de assinatura, histórico auditável, melhor para releases formais.
  - Contras: mais um objeto no banco (mínimo overhead).
- Lightweight:
  - Prós: simples, rápido, bom para marcações locais temporárias.
  - Contras: sem autoria, sem assinatura, sem mensagem — informação perdida.

Se você publica releases para terceiros, a disciplina favorece **annotated (assinadas)**. Se é só um atalho local, lightweight basta.


