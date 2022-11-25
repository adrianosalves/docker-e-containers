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

Mountando volumes no docker.

```
docker volume create data-sistema


──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# touch arquivo1.txt                                                        
                                                                                               
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# touch arquivo2.txt

                                                                                               
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker images                                                                
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
mysql        latest    3842e9cdffd2   8 days ago      538MB
debian       10        1036dd279580   9 days ago      114MB
debian       latest    c31f65dd4cc9   9 days ago      124MB
ubuntu       latest    a8780b506fa4   3 weeks ago     77.8MB
debian       9         662c05203bab   5 months ago    101MB
centos       latest    5d0da3dc9764   14 months ago   231MB
                                                                                              
                                                                                               
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker run -dti --name debianA --mount type=volume,src=data-sistema,dst=/data debian 
817346bc22a042ae7f213c379cb257a126f1e48b4967c5155eadb405faad8b64


┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker exec -ti debianA bash
root@817346bc22a0:/# cd /data
root@817346bc22a0:/data# ls
arquivo1.txt  arquivo2.txt
root@817346bc22a0:/data# 

┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker exec -ti debianA bash
root@817346bc22a0:/# cd /data/
root@817346bc22a0:/data# ls
arquivo1.txt  arquivo2.txt
root@817346bc22a0:/data# toucho obakkkkkkvaleu.txt
bash: toucho: command not found
root@817346bc22a0:/data# touch obakkkkkkvaleu.txt
root@817346bc22a0:/data# exit
exit
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# ls                          
arquivo1.txt  arquivo2.txt  obakkkkkkvaleu.txt
                                                 
```
Para remover volumes é necessário parar e remover os container associado a esse volume.

```
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker ps                   
CONTAINER ID   IMAGE     COMMAND   CREATED        STATUS        PORTS     NAMES
817346bc22a0   debian    "bash"    22 hours ago   Up 22 hours             debianA
69ce416ee293   debian    "bash"    24 hours ago   Up 24 hours             nice_elion
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker ps      
CONTAINER ID   IMAGE     COMMAND   CREATED        STATUS        PORTS     NAMES
817346bc22a0   debian    "bash"    22 hours ago   Up 22 hours             debianA
69ce416ee293   debian    "bash"    24 hours ago   Up 24 hours             nice_elion
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker stop debianA
debianA
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker rm debianA                                                                   
debianA                                                                                                                                                                                  
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker ps 
CONTAINER ID   IMAGE     COMMAND   CREATED        STATUS        PORTS     NAMES
69ce416ee293   debian    "bash"    24 hours ago   Up 24 hours             nice_elion
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker volume rm data-sistema
data-sistema
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker volume list           
DRIVER    VOLUME NAME
local     2f8c5a3c05acab5a1ebaa3577ecf06ab8bde099d7a69954201d7c7a20998b0bc
local     03d6a487252d81e6c45a34fb07fc9e73f52a7e82cc0fb20468fea786ad8aa4b7
local     8c8f206c16a37f166287f99678cd3236196ccb1035f02785288a354e402035b9
local     9fc08ac8fb813edb5218132d2b1566aae87a6cfb7f312d33899f708705d644bb
local     0475f5b898adc5c9150b80d6281bd7af8f251618a57aefe15302cd37dd6f76b9
local     0852db82a6211c587a5bdced065b0dc4ecdd9232553e9e5c60d6908e2a5e3645
local     5032bebe86cf64111c2e6f451890c2a2a3e9aad051de7022b31f68ae432fa9df
local     11465415a31905f3470bfaeec62dbd08a6ceb05eefdf0a824e84ec0ec2548ea8
local     ae303802a373c7a2432ef61a9938cde9aeca7405f0002169eaf6310687e246b3
local     da67b732e1838da60068c27e67658298bbfc0d8ae256ed52d2be83fb28da2195
local     eaf600e1e8ea4ef7b27311055e731c69bbeb632eb2a0d632571d3656a33e4082

```

### Mount: 14 Conclusão

Podemos remover um container sem a necessidade de usar o "stop".

```
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker ps              
CONTAINER ID   IMAGE     COMMAND   CREATED        STATUS        PORTS     NAMES
69ce416ee293   debian    "bash"    25 hours ago   Up 25 hours             nice_elion
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker rm -f nice_elion
nice_elion
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker ps              
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# 
```

Podemos remover todos os container que não estão em execução.

```
──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED        STATUS                      PORTS     NAMES
1ba5e7586889   debian    "bash"        25 hours ago   Exited (137) 25 hours ago             beautiful_saha
b222867a5090   ubuntu    "bash"        5 days ago     Exited (255) 4 days ago               Ubuntu-B
db46ed6d1fba   centos    "/bin/bash"   5 days ago     Exited (255) 4 days ago               CentOS-A
6805a80756eb   ubuntu    "bash"        5 days ago     Exited (255) 4 days ago               Ubuntu-A
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker container prune 
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
1ba5e75868892da9c0491bde16e01f165beb61a9e0e628f998fc21bb1698e667
b222867a5090b83b356b224e3378c8e624759e1ef988afdf5a59f161e9774d7b
db46ed6d1fba06a9008a81adb254a80b42e9b8a3ffa0bdd2dd8bff65fb515850
6805a80756eb0065b8b09553cc890a5e4214192e7b1a4099d28ffbcc3ea726de

Total reclaimed space: 108.7MB
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker ps -a          
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# 
```

Podemos remover todos os volumes que não estão sendo usado por container.

```
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker volume list  
DRIVER    VOLUME NAME
local     2f8c5a3c05acab5a1ebaa3577ecf06ab8bde099d7a69954201d7c7a20998b0bc
local     03d6a487252d81e6c45a34fb07fc9e73f52a7e82cc0fb20468fea786ad8aa4b7
local     8c8f206c16a37f166287f99678cd3236196ccb1035f02785288a354e402035b9
local     9fc08ac8fb813edb5218132d2b1566aae87a6cfb7f312d33899f708705d644bb
local     0475f5b898adc5c9150b80d6281bd7af8f251618a57aefe15302cd37dd6f76b9
local     0852db82a6211c587a5bdced065b0dc4ecdd9232553e9e5c60d6908e2a5e3645
local     5032bebe86cf64111c2e6f451890c2a2a3e9aad051de7022b31f68ae432fa9df
local     11465415a31905f3470bfaeec62dbd08a6ceb05eefdf0a824e84ec0ec2548ea8
local     ae303802a373c7a2432ef61a9938cde9aeca7405f0002169eaf6310687e246b3
local     da67b732e1838da60068c27e67658298bbfc0d8ae256ed52d2be83fb28da2195
local     eaf600e1e8ea4ef7b27311055e731c69bbeb632eb2a0d632571d3656a33e4082
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker volume prune 
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Volumes:
0475f5b898adc5c9150b80d6281bd7af8f251618a57aefe15302cd37dd6f76b9
ae303802a373c7a2432ef61a9938cde9aeca7405f0002169eaf6310687e246b3
5032bebe86cf64111c2e6f451890c2a2a3e9aad051de7022b31f68ae432fa9df
8c8f206c16a37f166287f99678cd3236196ccb1035f02785288a354e402035b9
eaf600e1e8ea4ef7b27311055e731c69bbeb632eb2a0d632571d3656a33e4082
2f8c5a3c05acab5a1ebaa3577ecf06ab8bde099d7a69954201d7c7a20998b0bc
9fc08ac8fb813edb5218132d2b1566aae87a6cfb7f312d33899f708705d644bb
03d6a487252d81e6c45a34fb07fc9e73f52a7e82cc0fb20468fea786ad8aa4b7
11465415a31905f3470bfaeec62dbd08a6ceb05eefdf0a824e84ec0ec2548ea8
da67b732e1838da60068c27e67658298bbfc0d8ae256ed52d2be83fb28da2195
0852db82a6211c587a5bdced065b0dc4ecdd9232553e9e5c60d6908e2a5e3645

Total reclaimed space: 587.8MB
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# docker volume list 
DRIVER    VOLUME NAME
                                                                                              
┌──(root㉿kali)-[/var/…/docker/volumes/data-sistema/_data]
└─# 
```

### 15. Exemplo: Apache Contêiner

Instalar um Servidor Apache "httpd".

```
┌──(root㉿kali)-[/home/kali]
└─# docker ps                              
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker pull httpd      
Using default tag: latest
latest: Pulling from library/httpd
a603fa5e3b41: Pull complete 
4691bd33efec: Pull complete 
ff7b0b8c417a: Pull complete 
9df1012343c7: Pull complete 
b1c114085b25: Pull complete 
Digest: sha256:f2e89def4c032b02c83e162c1819ccfcbd4ea6bdbc5ff784bbc68cba940a9046
Status: Downloaded newer image for httpd:latest
docker.io/library/httpd:latest
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# mkdir /data/apache-A
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# nano index.html       
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# nano index.html
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker run --name apache-A -d -p 80:80 --volume=/data/apache-A:/usr/local/apache2/htdocs httpd
1ea6a8c3850c7ac0c080b28458f560a46fce3bc52d00ebb78263a6a57b3d8af2
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker ps                                                                                     
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS                               NAMES
1ea6a8c3850c   httpd     "httpd-foreground"   19 seconds ago   Up 17 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   apache-A
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 

```

Identificando o IP do meu servidor Apache.

```
┌──(root㉿kali)-[/home/kali]
└─# ip a              
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:db:96:6a brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.107/24 brd 192.168.1.255 scope global dynamic noprefixroute eth0
       valid_lft 6106sec preferred_lft 6106sec
    inet6 fe80::a00:27ff:fedb:966a/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

### 16. Exemplo: PHP - Apache

Instalação do PHP junto com o Apache.

```
┌──(root㉿kali)-[/home/kali]
┌──(root㉿kali)-[/home/kali]
└─# docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS                               NAMES
1ea6a8c3850c   httpd     "httpd-foreground"   6 minutes ago   Up 6 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   apache-A
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker pull php:7.4-apache
7.4-apache: Pulling from library/php
a603fa5e3b41: Already exists 
c428f1a49423: Pull complete 
156740b07ef8: Pull complete 
fb5a4c8af82f: Pull complete 
25f85b498fd5: Pull complete 
9b233e420ac7: Pull complete 
fe42347c4ecf: Pull complete 
d14eb2ed1e17: Pull complete 
66d98f73acb6: Pull complete 
d2c43c5efbc8: Pull complete 
ab590b48ea47: Pull complete 
80692ae2d067: Pull complete 
05e465aaa99a: Pull complete 
Digest: sha256:c9d7e608f73832673479770d66aacc8100011ec751d1905ff63fae3fe2e0ca6d
Status: Downloaded newer image for php:7.4-apache
docker.io/library/php:7.4-apache
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# mkdir /data/php-A           
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker run --name php-A -d -p 8080:80 --volume=/data/php-A:/var/www/html php:7.4-apache       
6ae8347767cbe444b26322b21e7b6f301c62694386b680701d9e0be58f45c17b
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker ps                                                                              
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                   NAMES
6ae8347767cb   php:7.4-apache   "docker-php-entrypoi…"   6 seconds ago    Up 4 seconds    0.0.0.0:8080->80/tcp, :::8080->80/tcp   php-A
1ea6a8c3850c   httpd            "httpd-foreground"       11 minutes ago   Up 11 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp       apache-A
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# nano /data/php-A/index.php
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# nano /data/php-A/index.php
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 


```

### Processamento, Logs e Rede

**17. 









