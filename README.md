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

Importante especificar variavel de ambiente obrigatória **MYSQL_ROOT_PASSWORD**.

No portal "https://hub.docker.com/_/mysql" em Environment Variables.

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
Quando estamos na mesma faixa de IP do docker conseguimos entrar no mysql sem abrir o bash do container.

```
┌──(root㉿kali)-[/home/kali]
└─# mysql -u root -p --protocol=tcp
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 14
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| aula               |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.002 sec)

MySQL [(none)]> 
```
### 10. Acessando um container externamente

https://www.mariadbtutorial.com/getting-started/connect-to-mariadb/

Abaixo mostramos como podemos acessar o MysSQL remoto atraves de uma maquina Windows.

```
c:\Program Files\MariaDB 10.10\bin>mysql.exe -u root -h 192.168.0.102 -p
Enter password: ********
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 27
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| aula               |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.008 sec)

MySQL [(none)]>
```

Estamos agora entrando no banco de **aula** e criando um tabela **aluno**.

```
MySQL [(none)]> use aula;
Database changed
MySQL [aula]> create table aluno (
    -> AlunoID int,
    -> Nome varchar(50),
    -> Sobrenome varchar(50),
    -> Endereco varchar(150),
    -> Cidade varchar(50));
Query OK, 0 rows affected (0.133 sec)

MySQL [aula]>
```
Inserindo dados na tabela do Banco de Dados.

```
MySQL [aula]> insert into aluno(AlunoID, Nome, Sobrenome, Endereco, Cidade) values (1,'Manoel','Alves','Rua da Magueira','Bahia');
Query OK, 1 row affected (0.023 sec)

MySQL [aula]> select * from aluno;
+---------+--------+-----------+-----------------+--------+
| AlunoID | Nome   | Sobrenome | Endereco        | Cidade |
+---------+--------+-----------+-----------------+--------+
|       1 | Manoel | Alves     | Rua da Magueira | Bahia  |
+---------+--------+-----------+-----------------+--------+
1 row in set (0.004 sec)

MySQL [aula]>
```

### 11. Parando e reiniciando um container.

Quando reiniciamos o servicos do container não perdemos os dados do MySQL.

Agora vamos para nosso container. 

```
docker stop mysql-B
```
Agora vamos iniciar nosso container. 

```
docker start mysql-B
```

Agora vamos remover o container. 

```
docke rm mysql-B
```
**NOTA:** Quando excluimos o container os dados são excluido tambem. Para não perder os dados quando exluir um container ou quando ocorre algum problema com o container é recomendado mapear o local dos dados para fora do container, se eu precisar subir um novo container conseguimos subir com os dados novamente.

### 11. Montando mount um local de armazenamento

Nosso container MySQL está no ar em execução.

```
┌──(root㉿kali)-[/home/kali]
└─# docker ps                   
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS       PORTS                                                  NAMES
5337a48eeb0a   mysql     "docker-entrypoint.s…"   2 hours ago   Up 2 hours   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-B
```
Vamos indentificar onde nosso container armazena os dados.

```
──(root㉿kali)-[/home/kali]
└─# docker inspect mysql-B | grep "Destination"         
                "Destination": "/var/lib/mysql"
```

**NOTA:** Que a linha **"Destination": "/var/lib/mysql"** é o local padrão onde é armazenado os dados, seu quiser armazenar os dados em outro local fora do container eu preciso alterar esse campo para outro local.

Para altera o local podemos fazer da seguinte forma, criar uma pasta em nossa maquina fisica fora do container.

```
──(root㉿kali)-[/]
└─# mkdir /data        
                                                                                               
┌──(root㉿kali)-[/]
└─# mkdir /data/mysql-B
                                                                                               
┌──(root㉿kali)-[/]
```

**NOTA:** Observe acima que criei duas pastas dentro "/" na minha maquina fisica fora do container, indicando o seguinte caminho **/data/mysql-B/**

Precisamos desligar no container e depois vamo remover esse container.

```
docker stop mysql-B
```

```
docker rm mysql-B
```

Reconfigurar nosso container do zero.

```
──(root㉿kali)-[/data/mysql-B]
└─# docker run -e MYSQL_ROOT_PASSWORD=Senha123 --name mysql-B -d -p 3386:3386 --volume=/data/mysql-B:/var/lib/mysql mysql
0af6db093672dc706123236c8f6726bc78acac67a14e35e1faa3884940845bf5
                                                                                               
┌──(root㉿kali)-[/data/mysql-B]
└─# ls
 auto.cnf            '#ib_16384_1.dblwr'  '#innodb_redo'   undo_1_trunc.log
'#ib_16384_0.dblwr'   ibdata1              undo_001
                                                                                               
┌──(root㉿kali)-[/data/mysql-B]
└─# 

```

**NOTA:** Observe que após iniciar o novo container os arquivos do banco de dados ja está sendo gravado no novo caminho **/data/mysql-B**.

Dessa forma mesmo ser perder o container podemos criar um novo container basta indicar o mesmo caminho e os dados seram recuperados.

A partir do servidor onde está o container vamos entrar no Mysql e criar um banco de dados novamente.

```
mysql> create database aula;
Query OK, 1 row affected (0.01 sec)

mysql> use aula;
Database changed

mysql> select * from alunos;
Empty set (0.00 sec)

mysql> insert into alunos (AlunoID, Nome, Sobrenome, Endereco, Cidade) values (1,"Adriano","Alves","Rua....","Bahia");
Query OK, 1 row affected (0.02 sec)

mysql> select * from alunos;
+---------+---------+-----------+----------+--------+
| AlunoID | Nome    | Sobrenome | Endereco | Cidade |
+---------+---------+-----------+----------+--------+
|       1 | Adriano | Alves     | Rua....  | Bahia  |
+---------+---------+-----------+----------+--------+
1 row in set (0.00 sec)

mysql> 
```

Nosso container está em execução.

```
┌──(root㉿kali)-[/data/mysql-B]
└─# docker ps                   
CONTAINER ID   IMAGE     COMMAND                  CREATED        STATUS       PORTS                                                            NAMES
0af6db093672   mysql     "docker-entrypoint.s…"   10 hours ago   Up 9 hours   3306/tcp, 33060/tcp, 0.0.0.0:3386->3386/tcp, :::3386->3386/tcp   mysql-B

````

Agora vou desligar o container.

```
┌──(root㉿kali)-[/data/mysql-B]
└─# docker stop mysql-B
mysql-B

```

Agora vou remover o container.

```
──(root㉿kali)-[/data/mysql-B]
└─# docker rm mysql-B
mysql-B
```

Porem os meu dados continua salvo no seguinte local.

```
──(root㉿kali)-[/data/mysql-B]
└─# ls /data/mysql-B 
 aula            ca-key.pem           ib_buffer_pool   mysql.sock           sys
 auto.cnf        ca.pem               ibdata1          performance_schema   undo_001
 binlog.000001   client-cert.pem     '#innodb_redo'    private_key.pem      undo_002
 binlog.000002   client-key.pem      '#innodb_temp'    public_key.pem
 binlog.000003  '#ib_16384_0.dblwr'   mysql            server-cert.pem
 binlog.index   '#ib_16384_1.dblwr'   mysql.ibd        server-key.pem
```

**NOTA:** Agora posso iniciar um novo container que vamos ter acesso ao dados novamente, podemos criar o container com o mesmo nome ou com um nome diferente. 

Nesse momento não temos nenhum container em execução.

```
┌──(root㉿kali)-[/data/mysql-B]
└─# docker ps        
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Criando um novo container com um nome diferente onde teremo o acesso as mesmas informações do container que foi removido.

```
┌──(root㉿kali)-[/data/mysql-B]
└─# docker run -e MYSQL_ROOT_PASSWORD=Senha123 --name mysql-Novo -d -p 3306:3306 --volume=/data/mysql-B:/var/lib/mysql mysql
df8f8256016aafc2f0a8b1364179bc3a7ece4dc70e820b069a183330ddac52a8
                                                                                               
┌──(root㉿kali)-[/data/mysql-B]
└─# 

┌──(root㉿kali)-[/data/mysql-B]
└─# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
df8f8256016a   mysql     "docker-entrypoint.s…"   25 seconds ago   Up 23 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-Novo
                                                                                               
┌──(root㉿kali)-[/data/mysql-B]
└─# mysql -u root -p --protocol=tcp --port=3306
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| aula               |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.137 sec)

MySQL [(none)]> use aula;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [aula]> select * from alunos;
+---------+---------+-----------+----------+--------+
| AlunoID | Nome    | Sobrenome | Endereco | Cidade |
+---------+---------+-----------+----------+--------+
|       1 | Adriano | Alves     | Rua....  | Bahia  |
+---------+---------+-----------+----------+--------+
1 row in set (0.006 sec)

MySQL [aula]> 
```

**NOTA:** Dessa forma seguindo os passos acima conseguimos mapear os dados para nosso container pois os dados estão sendo mapeado para um local fora do container.


### 12. Montando mount um local de armazenamento

As informações do container são salvas em **"/var/lib/mysql"** esse é a pasta padrão dentro do container.
```
┌──(root㉿kali)-[/data/mysql-B]
└─# docker inspect  mysql-Novo | grep Destination
                "Destination": "/var/lib/mysql",
```
Se quiser direcionar que essas informações seja salvo em outro local fora do container precisam alterar essa configuação, abaixo se como fazer essa configuração.

Consulte qual container voce quer altera.

```
┌──(root㉿kali)-[/]
└─# docker ps                                    
CONTAINER ID   IMAGE     COMMAND                  CREATED        STATUS       PORTS                                                  NAMES
df8f8256016a   mysql     "docker-entrypoint.s…"   10 hours ago   Up 9 hours   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-Novo
```

Cria uma nova pasta onde vai ser direcionado as informações para ser salva, vamos criar uma pasta na raiz do sistema fora do container.

```
┌──(root㉿kali)-[/]
└─# mkdir /data/mysq-Novo                      
                                                                                               
┌──(root㉿kali)-[/]
└─# ls /            
adm   disk2           lib         media    root     sec         texto1.txt  ven
bin   etc             lib32       mnt      run      srv         texto2.txt  vmlinuz
boot  home            lib64       opt      sbin     sys         tmp         vmlinuz.old
data  initrd.img      libx32      proc     script2  teste1.txt  usr
dev   initrd.img.old  lost+found  publico  scripts  teste2.txt  var
                                                                                               
┌──(root㉿kali)-[/]
└─# cd data 
                                                                                               
┌──(root㉿kali)-[/data]
└─# ls  
mysql-B  mysq-Novo
```

Pare o container "mysql-Novo" e remova.

```
──(root㉿kali)-[/data]
└─# docker stop mysql-Novo
mysql-Novo

──(root㉿kali)-[/data]
└─# docker rm mysql-Novo
mysql-Novo
                                                                                               
┌──(root㉿kali)-[/data]
└─# docker ps             
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
                                                                                               
┌──(root㉿kali)-[/data]
└─# 
```

## 13. Tipos de mount bind, named, dockerfile volume

**Tipos de mount**

- bind mounts = permite criar uma pasta e fazer o redirecionamento dos dados do container para essa pasta.
 
- named volumes = permite nomear um volume.

- dockerfile volumes = permite criar volumes dentro do docker.

**bind**

Nos exercicios que fizemos anteriormente usamos o *bind mounts*.
As montagens Bind apenas vincula uma determinada pasta ou arquivo do host dentro do conntainer:

```
docker run -v /host-pasta:/container-pasta mysql
```

**named**

Volumes nomeados são volumes que podemos criar manualmente com o comando:

```
docker volume create "nome-do-volume" 
```
A diferença do "bind" é que especificamos uma pasta, quando trabalhamos com volumes eles são criado em **/var/lib/docker/volumes** e pode se referenciados apenas por seu nome.

Exemplo:

Quando criamos um volume chamado **"mysql_data"** podemos apenas referencia-lo com o comando:

```
docker run -v mysql_data:/container-pasta mysql
```

**dockerfile volume**

São volumes criados pela instrução VOLUME. Esses volumes também são criados em **/var/lib/docker/volumes**, mas não tem um determinado nome. O volume é criado ao executar o contêiner e são uteis para salvar dados persistentes. O desenvolvedor pode dizer onde estão os dados importantes e o que deve ser persistente.

**qual devo utilizar?**

Depende de cada um pessoa, se quiser manter tudo na "área do docker" (**/var/lib/docker**), poderá usar volumes. Caso queira manter sua propria estrutura de pastas, pode usar binds.

**NOTA:** O Docker recomenda o uso de volumes em vez do uso de binds, pois os volumes são criados e gerenciados pelo docker.

## 13. Exemplos de tipos de mount, na prática

Criar um metodo diferente "bind" e depois o "volume create".

```
──(root㉿kali)-[/data]
└─# ls
mysql-B  mysql-Novo

┌──(root㉿kali)-[/data]
└─# docker run -dti --mount type=bind,src=/data/mysql-Novo,dst=/data debian
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
a8ca11554fce: Pull complete 
Digest: sha256:3066ef83131c678999ce82e8473e8d017345a30f5573ad3e44f62e5c9c46442b
Status: Downloaded newer image for debian:latest
1ba5e75868892da9c0491bde16e01f165beb61a9e0e628f998fc21bb1698e667

┌──(root㉿kali)-[/data]
└─# docker exec -ti beautiful_saha bash 
root@1ba5e7586889:/# ls
bin   data  etc   lib    media  opt   root  sbin  sys  usr
boot  dev   home  lib64  mnt    proc  run   srv   tmp  var
root@1ba5e7586889:/# cd data/
root@1ba5e7586889:/data# ls
root@1ba5e7586889:/data# ls -la
total 8
drwxr-xr-x 2 root root 4096 Nov 22 21:27 .
drwxr-xr-x 1 root root 4096 Nov 24 21:22 ..
root@1ba5e7586889:/data# exit
exit
                                                                                               
┌──(root㉿kali)-[/data]
└─# ls
mysql-B  mysql-Novo
                                                                                               
                                                                                             
┌──(root㉿kali)-[/data]
└─# cd mysql-Novo 
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# ls
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# touch MeuArquivo.txt
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# ls
MeuArquivo.txt
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# docker exec -ti beautiful_saha bash
root@1ba5e7586889:/# cd /data/
root@1ba5e7586889:/data# ls
MeuArquivo.txt
root@1ba5e7586889:/data# 
```

Vamos criar um volume tipo "bind" somente leitura.

```
──(root㉿kali)-[/data/mysql-Novo]
└─# docker ps                          
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS         PORTS     NAMES
1ba5e7586889   debian    "bash"    9 minutes ago   Up 9 minutes             beautiful_saha
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# docker stop 1ba       
1ba
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# docker ps      
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# docker run -dti --mount type=bind,src=/data/mysql-Novo,dst=/data debian,ro debian
docker: invalid reference format.
See 'docker run --help'.
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# docker run -dti --mount type=bind,src=/data/mysql-Novo,dst=/data,ro debian       
69ce416ee2938cd0495d6fbb45b57c91d5e5ae0c5fec14c36ce73ee3409bebf5
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# docker ps                                                                 
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
69ce416ee293   debian    "bash"    13 seconds ago   Up 11 seconds             nice_elion
                                                                                               
┌──(root㉿kali)-[/data/mysql-Novo]
└─# docker exec -ti 69ce416ee293 bash  
root@69ce416ee293:/# cd /data/
root@69ce416ee293:/data# ls
MeuArquivo.txt
root@69ce416ee293:/data# rm MeuArquivo.txt 
rm: cannot remove 'MeuArquivo.txt': Read-only file system
root@69ce416ee293:/data# touch Novo.txt
touch: cannot touch 'Novo.txt': Read-only file system
root@69ce416ee293:/data# 
```

### Processamento, Logs e Rede









