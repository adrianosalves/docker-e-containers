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

### 5. Excluindo e nomeando contêineres:

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

### 6. Copiando arquivos para o contêiner

Criado uma pasta dentro do container.
 
```
docker exec "nome-do-container" mkdir /"nome-da-pasta"
```

Consultando pastas dentro do container.
 
```
docker exec "nome-do-container" ls /
```

Copiando um arquivo para dentro do containers.
 
```
docker cp "nome-do-arquivo" "nome-do-container":/"nome-da-pasta"
```

Para copia mais de um arquivo podemos compactar esses arquivos e depois copiar.

```
docker cp "nome-do-arquivo.zip" "nome-do-container":/"nome-da-pasta"
```

### 7. Copiando arquivos do container

Copiando arquivo do container para seu computador.

```
docker cp "nome-do-container":/"pasta/nome-do-arquivo" "nome-do-arquivo-copia"
```

### 8. TAGS

No portal "https://hub.docker.com" ao escolher a imagem para download é possivel encontrar em *Supported Tags and Respective Dockerfile links* escolher uma versão especifica compativel com seu ambiente.

```
docker pull "nome-da-image":"tag"
```

### 9. Criando um container do MYSQL

Importante especificar variavel de ambiente obrigatória **MYSQL_ROOT_PASSWORD** No portal "https://hub.docker.com/_/mysql" em Environment Variables.

Download de uma container com o Mysql.

```
docker pull mysql
```

Com o paramento "-p" publicamos a porta do container no host, e o "-e" configura as variavesi de ambiente e o "-d" para executar em background, e "-p" especifica a porta do host local para o mysql para qualquer maquina da rede.

```
docker run -e MYSQL_ROOT_PASSWORD="digite-uma-senha" --name "nome-do-container" -d -p 3306:3306 mysql
```

Entrando no container do onde o mysql está instalado.

```
docker exec -it "nome-do-container" bash
```

Apos entrar dentro do container execute o comando abaixo para entrar no mysql será solicitado a senha **'Enter password:"digite-sua-senha"** ela foi cadastrado no passo anterior.

```
# mysql -y -root -p --protocol=tcp
```

Após entrar no **mysql** será possivel criar um banco de dados.

```
mysql> CREATE DATABASE "nome-do-seu-banco-de-dados";
```

Mostrar os bancos disponiveis.

```
mysql> show databases;
```

Quando o Docker é instalado é criado um dispositivo de rede que recebe um ip a mesma faixa de rede que os container está utilizando, segue o comando de como consultar.

```
──(root㉿kali)-[/home/kali]
└─# ip a | grep docker
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
```
Vamos agora identificar o IP do nosso container mysql.

```
──(root㉿kali)-[/home/kali]
└─# docker inspect mysql-B | grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```
