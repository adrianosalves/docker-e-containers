# Docker & Containers

Necessário conhecimento de Linux!

Gerenciar Virtualização (Hyperv, AWS, GCP, Azure).

Saber usar ferramentas como o Putty para Acessar Remoto o Servidores Linux.

cat /etc/*release*  =  Verifica a versão do kernek do Linux.

### 1.Realizando o download de imagens e verificando seu estado:#

Faz o Download da versão mais atualizada da imagem para seu sistema.
```
docker pull "nome-da-imagem"
```

Verifica as imagens baixadas.
```
docker images
```

Executa a imagem no seu sistema por alguns segundo e finaliza. É uma sintaxe mais simple e antiga

```
docker run "nome-da-imagem" 
```

Executa a imagem no seu sistema por alguns segundo e finaliza. É uma sintaxe mais nova.
```
docker container run ""nome-da-imagem" 
```

Verifica quais os container está em execução.

```
docker ps
```

Mostra os container executado recentemente e se eles estão em execução ou não!
```
docker ps -a
```
### 2.Executando um contêiner

Executa a imagem no seu sistema por 10s depois finaliza.
```
docker run "nome-da-imagem" sleep 10
```

Executa a imagem no seu sistema por 1500s depois finaliza.

```
docker run "nome-da-imagem" sleep 1500 
```
Finaliza a execução da imagem.

```
docker stop "id-da-imagem" ou "nome-da-imagem"
```
Executa a imagem no seu sistema, o uso do paramento "t" aloca um terminal (bash) permitindo trabalha com essa imagem, e o "i" permite interação com esse container instalar aplicações e etc. Porem pós usar o comando "exit" dentro do container ele é finalizado.

```
docker run -it "nome-da-imagem" 
```

Executa a imagem no seu sistema, com uso adicional do paramento "d" aloca um terminal (bash) permitindo trabalha com essa imagem, e o "i" permite interação com esse container instalar aplicações e etc. Porem pós usar o comando "exit" dentro do container ele é finalizado.

```
docker run -it "nome-da-imagem" 
```

```
docker run -dit "nome-da-imagem" = 
```











