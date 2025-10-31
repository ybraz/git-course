# Complete Git Course

## Chapter 3 - Basic Shell Commands

Principais comandos discutidos na seção:
- nano: editar arquivos
- clear: limpar o terminal
- tab: comando para autocompletar
- echo: escrever no terminal
- man: ajuda em comandos específicos
- touch: criar um novo arquivo
- >: escrever em um arquivo
- >>: adicionar ao arquivo
- cat: listar o conteúdo de um arquivo

Exemplos:

```
$ man clear

$ touch new-file.txt

$ ls -la 

$ echo "Hello, World"

$ echo "Hello, World" > new-file.txt

$ cat new-file.txt

```

Mais comandos:
- ls — lista arquivos e pastas. Ex: `ls -la /etc`
- cd — muda o diretório atual. Ex: `cd ~/projetos/crypto`
- pwd — imprime o caminho absoluto do diretório atual. Ex: `pwd`
- cat — concatena/mostra conteúdo de arquivos (rápido). Ex: `cat README.md`
- less — visualizador paginado (bom para arquivos longos). Ex: `less /var/log/syslog`
- grep — pesquisa texto usando expressões regulares simples. Ex: `grep -n "TODO" src/*.py`
- rg — (ripgrep) pesquisa recursiva rápida por padrão (substituto moderno do grep). Ex: `rg "password" ~/repos`
- find — encontra arquivos por nome, tipo, data ou permissão. Ex: `find . -type f -name "*.sql"`
- sed — editor de fluxo para substituir/transformar texto em pipelines. Ex: `sed 's/foo/bar/g' arquivo.txt`
- awk — processador de texto poderoso para colunas e agregações. Ex: `awk '{print $1, $3}' access.log`
- curl — faz requisições HTTP e transfere dados (API / download). Ex: `curl -sS https://api.ipify.org`
- ssh — conecta-se a outro host de forma segura. Ex: `ssh -p 2222 usuario@10.0.0.5`
- scp — copia arquivos via SSH entre máquinas. Ex: `scp arquivo.zip user@host:/tmp/`
- nc — netcat: liga/desliga conexões TCP/UDP, debug de portas e túnel simples. Ex: `nc -lvp 9001`
- nmap — scanner de portas e descobrimento de rede. Ex: `nmap -sS -Pn 10.0.0.0/24`
- git — controle de versão distribuído; commits, branches e revisão. Ex: `git checkout -b feat/login && git commit -am "início"`
- docker — gerencia containers (build/run/exec). Ex: `docker run --rm -it python:3.11 bash`
- chmod — altera permissões de arquivos. Ex: `chmod 700 ~/.ssh/id_rsa`
- sudo — executa comandos com privilégios elevados. Ex: `sudo apt update && sudo apt upgrade`
- ps — lista processos; combine com aux/-ef para detalhes. Ex: `ps aux | grep nginx`


