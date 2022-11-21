# Docker & Containers

Necessário conhecimento de Linux!

Gerenciar Virtualização (Hyperv, AWS, GCP, Azure).

Saber usar ferramentas como o Putty para Acessar Remoto o Servidores Linux.

cat /etc/*release*  =  Verifica a versão do kernek do Linux.



### 1.Realizando o download de imagens e verificando seu estado:#

Faz o Download da versão mais atualizada da imagem para seu sistema.
```
docker pull "nome-do-container"
```

Verifica as imagens baixadas.
```
docker images
```

Executa o container no seu sistema por alguns segundo e finaliza. É uma sintaxe mais simple e antiga

```
docker run "nome-do-container" 
```

Executa a imagem no seu sistema por alguns segundo e finaliza. É uma sintaxe mais nova.
```
docker container run "nome-do-container" 
```

Verifica quais os container está em execução.

```
docker ps
```

Mostra os container executado recentemente e se eles estão em execução ou não!. Sintaxe antiga e mais simples.
```
docker ps -a
```

Mostra os container executado recentemente e se eles estão em execução ou não!. Sintaxe mais nova.
```
docker container ls
```

### 2.Executando um contêiner

Executa container por 10s depois finaliza.
```
docker run "nome-do-container" sleep 10
```

Executa o container por 1500s depois finaliza.

```
docker run "nome-do-container" sleep 1500 
```
Finaliza a execução do container.

```
docker stop "id-do-container" ou "nome-do-container"
```
Executa o container, o uso do paramento "t" aloca um terminal (bash) permite trabalha com o container, e o "i" permite interação com esse container instalar aplicações e etc. Porem spós usar o comando "exit" dentro do container ele é finalizado.

```
docker run -it "nome-do-container" 
```

### 4. Executando aplicações no contêiner:

Executa o container, com uso adicional do paramento "-d" exeuta o container em background e imprimi o ID. Assim mesmo que sair do container ele continuará em execução.

```
docker run -dti "nome-do-container"
```
Executa o containe, com uso adicional do paramentro "exec" permite executar comandos dentro do container atraves do /bin/bash.

```
docker exec -it "nome-do-container" ou "id-do-container" /bin/bash
```

### 4. Excluindo e nomeando contêineres:

Remove container para que possa reduzir o uso de recurso e espaço da seu servidor.

```
docker rm "nome-do-container" ou "id-do-container"
```

Remove as images para que possa reduzir o uso de recurso e espaço da seu servidor.

```
docker rmi "nome-da-image"
```

Ao executar o comando docker para executar uma imagem que não foi baixada pelo seu sistema, se o docker não encontrar ele automaticamente alerta "Unable to find image 'xyz' localy" e depois inicia sozinho o download nos repositorio da ultima versão disponivel.
 
```
docker run -dti "nome-da-image"
```

Atribuir novo nome ao seu container.
 
```
docker run -dti --name "novo-nome" "nome-da-image"
```








