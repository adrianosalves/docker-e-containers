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

**17. Limitando Memória e CPU**

```
──(root㉿kali)-[/home/kali]
└─# docker stats php-A

CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O        PIDS
6ae8347767cb   php-A     0.02%     16.28MiB / 974.9MiB   1.67%     3.02kB / 25.2kB   16.1MB / 4.1kB   9

┌──(root㉿kali)-[/home/kali]
└─# docker update php-A -m 128M --cpus 0.2
Error response from daemon: Cannot update container 6ae8347767cbe444b26322b21e7b6f301c62694386b680701d9e0be58f45c17b: Memory limit should be smaller than already set memoryswap limit, update the memoryswap at the same time

┌──(root㉿kali)-[/home/kali]
└─# docker run --name ubuntu-C -dti -m 128M --cpus 0.2 ubuntu

┌──(root㉿kali)-[/home/kali]
└─# docker ps

CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                                   NAMES
e42fa71d93af   ubuntu           "bash"                   2 minutes ago   Up 2 minutes                                           ubuntu-C
6ae8347767cb   php:7.4-apache   "docker-php-entrypoi…"   11 hours ago    Up 11 hours    0.0.0.0:8080->80/tcp, :::8080->80/tcp   php-A
1ea6a8c3850c   httpd            "httpd-foreground"       11 hours ago    Up 11 hours    0.0.0.0:80->80/tcp, :::80->80/tcp       apache-A

┌──(root㉿kali)-[/home/kali]
└─# docker stats ubuntu-C

CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O     PIDS
e42fa71d93af   ubuntu-C   0.00%     3.629MiB / 128MiB   2.84%     726B / 0B   5.04MB / 0B   1
```

Vamos estressar um pouco o sistema operacional.

```
┌──(root㉿kali)-[/home/kali]
└─# docker exec -ti ubuntu-C bash 
root@e42fa71d93af:/# apt update
Get:1 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]
Get:2 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [522 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages [1792 kB]
Get:7 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [4642 B]
Get:8 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [775 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [17.5 MB]
Get:10 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [618 kB]      
Get:11 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages [164 kB]                          
Get:12 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [266 kB]                          
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [924 kB]                        
Get:14 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [579 kB]                  
Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [956 kB]                    
Get:16 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [8056 B]                  
Get:17 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [3175 B]                      
Get:18 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [7275 B]                  
Fetched 24.7 MB in 8s (2960 kB/s)                                                                         
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
root@e42fa71d93af:/# apt install stress -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  stress
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 18.4 kB of archives.
After this operation, 52.2 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy/universe amd64 stress amd64 1.0.5-1 [18.4 kB]
Fetched 18.4 kB in 1s (29.6 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package stress.
(Reading database ... 4395 files and directories currently installed.)
Preparing to unpack .../stress_1.0.5-1_amd64.deb ...
Unpacking stress (1.0.5-1) ...
Setting up stress (1.0.5-1) ...


root@e42fa71d93af:/# stress --cpu 1 --vm-bytes 50m --vm 1 --vm-bytes 50m
stress: info: [267] dispatching hogs: 1 cpu, 0 io, 1 vm, 0 hdd

┌──(root㉿kali)-[/home/kali]
└─# docker stats ubuntu-C

CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT   MEM %     NET I/O       BLOCK I/O        PIDS
e42fa71d93af   ubuntu-C   28.54%    49.91MiB / 128MiB   38.99%    1.02kB / 0B   299kB / 1.25MB   5


```

### Informações, logs e processos

Como coletar informações dos container.

```
┌──(root㉿kali)-[/home/kali]
└─# docker info                            
Client:
 Context:    default
 Debug Mode: false

Server:
 Containers: 3
  Running: 3
  Paused: 0
  Stopped: 0
 Images: 8
 Server Version: 20.10.19+dfsg1
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 1.6.9~ds1-1
 runc version: 1.1.4+ds1-1
 init version: 
 Security Options:
  apparmor
  seccomp
   Profile: default
  cgroupns
 Kernel Version: 5.16.0-kali7-amd64
 Operating System: Kali GNU/Linux Rolling
 OSType: linux
 Architecture: x86_64
 CPUs: 2
 Total Memory: 974.9MiB
 Name: kali
 ID: RD32:D4RY:UJHQ:X2MJ:X5GE:YGXN:7OHW:C7RT:JY4B:CRN2:MNZ7:XM2K
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false

                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

Obter logs dos container, como visualizar processos em execução, senhas criadas, portas liberadas.

```
──(root㉿kali)-[/home/kali]
└─# docker run -e MYSQL_ROOT_PASSWORD=Senha123 --name mysql-A -d -p 3306:3306 mysql
e7ffdcb19cade11990991d60d713e7ff5ec4a74b0bdc02774000f01834c38c5f
                                                                                                                                                                                                               
┌──(root㉿kali)-[/home/kali]
└─# docker logs mysql-A                                                            
2022-11-26 11:23:33+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2022-11-26 11:23:34+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2022-11-26 11:23:34+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2022-11-26 11:23:34+00:00 [Note] [Entrypoint]: Initializing database files
2022-11-26T11:23:34.280803Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2022-11-26T11:23:34.282166Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.31) initializing of server in progress as process 79
2022-11-26T11:23:34.333866Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-11-26T11:23:44.493650Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-11-26T11:23:47.829478Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2022-11-26 11:23:55+00:00 [Note] [Entrypoint]: Database files initialized
2022-11-26 11:23:55+00:00 [Note] [Entrypoint]: Starting temporary server
2022-11-26T11:23:56.319952Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2022-11-26T11:23:56.322826Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 128
2022-11-26T11:23:56.367101Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-11-26T11:23:59.560582Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-11-26T11:24:00.769699Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2022-11-26T11:24:00.770217Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2022-11-26T11:24:00.780770Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2022-11-26T11:24:00.838311Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
2022-11-26T11:24:00.839858Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
2022-11-26 11:24:01+00:00 [Note] [Entrypoint]: Temporary server started.
'/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

Analisar processos dentro de um container.

```
┌──(root㉿kali)-[/home/kali]
└─# docker ps          
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
e7ffdcb19cad   mysql     "docker-entrypoint.s…"   4 minutes ago    Up 4 minutes    0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-A
e42fa71d93af   ubuntu    "bash"                   40 minutes ago   Up 18 minutes                                                          ubuntu-C
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker container top ubuntu-C 
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                3684                3664                0                   06:09               pts/0               00:00:00            bash
root                4131                3664                0                   06:10               pts/1               00:00:00            bash
root                4158                4131                0                   06:10               pts/1               00:00:00            stress --cpu 1 --vm-bytes 50m --vm 1 --vm-bytes 50m
root                4159                4158                7                   06:10               pts/1               00:01:17            stress --cpu 1 --vm-bytes 50m --vm 1 --vm-bytes 50m
root                4160                4158                7                   06:10               pts/1               00:01:14            stress --cpu 1 --vm-bytes 50m --vm 1 --vm-bytes 50m
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker container top mysql-A 
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
stunnel4            7341                7318                1                   06:23               ?                   00:00:04            mysqld
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker ps                    
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
e7ffdcb19cad   mysql     "docker-entrypoint.s…"   5 minutes ago    Up 5 minutes    0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-A
e42fa71d93af   ubuntu    "bash"                   42 minutes ago   Up 19 minutes                                                          ubuntu-C
```                                         

### Redes

No servidor docker para saber o ip do seu servidor.

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
       valid_lft 5276sec preferred_lft 5276sec
    inet6 fe80::a00:27ff:fedb:966a/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ea:66:da:09 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:eaff:fe66:da09/64 scope link 
       valid_lft forever preferred_lft forever
```

Para visualizar as redes disponiveis.

Observe o "host" representa a rede padrão. E temos a rede "bridge" todo contanier criado e adiconado a essa rede e que permite ao comunicação com o seu host.

É por isso que sempre quando voce tem ao ip do seu host consegue ter acesso aos containers.

```
──(root㉿kali)-[/home/kali]
└─# docker network list
NETWORK ID     NAME      DRIVER    SCOPE
5f766f536f97   bridge    bridge    local
713f1bde8a82   host      host      local
45c169e9684f   none      null      local
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

Exemplo.
abaixo temos acesso as informações da rede "bridge" e conseguimos visualizar alguns container que pertence a essa rede.
Quando criamos os container se não especificar em qual rede ele será anexado por padrão ele será adicionado a rede "bridge".

```
──(root㉿kali)-[/home/kali]
└─# docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "5f766f536f97a4ce84567c446d12469d1e362824a8519c3b11913bfaa6720815",
        "Created": "2022-11-26T06:01:07.845369928-05:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "e42fa71d93af5f4f39e0a45e3fda71b15a6b2a06873509b3ef40362b659d95f0": {
                "Name": "ubuntu-C",
                "EndpointID": "0727e1e07e5a9d0edbe70c8b4982117a021c8d234f1d82f66b957f2d2eb1e837",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "e7ffdcb19cade11990991d60d713e7ff5ec4a74b0bdc02774000f01834c38c5f": {
                "Name": "mysql-A",
                "EndpointID": "857190dcb372e0607c8005a03151ea44319a208d2cbfeafb94ab0ab9fb34bef1",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

Vamos fazer um teste de conectividade entre os containers.
E vamos entrar no "ubuntu-C" e fazer um teste de ping no "mysql-A" .

```
──(root㉿kali)-[/home/kali]
└─# docker exec -ti ubuntu-C bash
root@e42fa71d93af:/# ping www.google.com.br
bash: ping: command not found
root@e42fa71d93af:/# apt-get install -y iputils-ping
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libcap2-bin libpam-cap
The following NEW packages will be installed:
  iputils-ping libcap2-bin libpam-cap
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 76.8 kB of archives.
After this operation, 280 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy/main amd64 libcap2-bin amd64 1:2.44-1build3 [26.0 kB]
Get:2 http://archive.ubuntu.com/ubuntu jammy/main amd64 iputils-ping amd64 3:20211215-1 [42.9 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy/main amd64 libpam-cap amd64 1:2.44-1build3 [7932 B]
Fetched 76.8 kB in 1s (65.3 kB/s) 
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libcap2-bin.
(Reading database ... 4404 files and directories currently installed.)
Preparing to unpack .../libcap2-bin_1%3a2.44-1build3_amd64.deb ...
Unpacking libcap2-bin (1:2.44-1build3) ...
Selecting previously unselected package iputils-ping.
Preparing to unpack .../iputils-ping_3%3a20211215-1_amd64.deb ...
Unpacking iputils-ping (3:20211215-1) ...
Selecting previously unselected package libpam-cap:amd64.
Preparing to unpack .../libpam-cap_1%3a2.44-1build3_amd64.deb ...
Unpacking libpam-cap:amd64 (1:2.44-1build3) ...
Setting up libcap2-bin (1:2.44-1build3) ...
Setting up libpam-cap:amd64 (1:2.44-1build3) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 78.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.34.0 /usr/local/share/perl/5.34.0 /usr/lib/x86_64-linux-gnu/perl5/5.34 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.34 /usr/share/perl/5.34 /usr/local/lib/site_perl) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up iputils-ping (3:20211215-1) ...
oot@e42fa71d93af:/# ping 172.17.0.3 -c 3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.061 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.090 ms
64 bytes from 172.17.0.3: icmp_seq=3 ttl=64 time=0.074 ms

--- 172.17.0.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2132ms
rtt min/avg/max/mdev = 0.061/0.075/0.090/0.011 ms
root@e42fa71d93af:/# 
```

Caso seja necessário isolar esses container em uma rede especifica.

O comando abaixo criar uma rede e já especifica um sub-rede para esse rede.

```
┌──(root㉿kali)-[/home/kali]
└─# docker network create minha-rede
6f97c15967793dcd4a1023fcbae11484feece6671409d4068a6dce42d9ce1069
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker network inspect minha-rede                                              
[
    {
        "Name": "minha-rede",
        "Id": "6f97c15967793dcd4a1023fcbae11484feece6671409d4068a6dce42d9ce1069",
        "Created": "2022-11-26T06:48:14.635163039-05:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

vamos criar um novo container e associa a rede "minha-rede" .

```
┌──(root㉿kali)-[/home/kali]
└─# docker ps                        
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS          PORTS                                                  NAMES
e7ffdcb19cad   mysql     "docker-entrypoint.s…"   28 minutes ago      Up 28 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-A
e42fa71d93af   ubuntu    "bash"                   About an hour ago   Up 10 minutes                                                          ubuntu-C
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker run -dti --name ubuntu-A --network minha-rede ubuntu                    
c962f206cdca26d0675537514096fff05703996c65383c374ae11b21d24b0fc2
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker ps                                                  
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS          PORTS                                                  NAMES
c962f206cdca   ubuntu    "bash"                   10 seconds ago      Up 8 seconds                                                           ubuntu-A
e7ffdcb19cad   mysql     "docker-entrypoint.s…"   29 minutes ago      Up 29 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-A
e42fa71d93af   ubuntu    "bash"                   About an hour ago   Up 11 minutes                                                          ubuntu-C
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

Ao consultar a nossa rede "minha-rede" observer que o container criado ja foi associado a rede com o **"IPv4Address": "172.18.0.2/16"**

```
┌──(root㉿kali)-[/home/kali]
└─# docker network inspect minha-rede                          
[
    {
        "Name": "minha-rede",
        "Id": "6f97c15967793dcd4a1023fcbae11484feece6671409d4068a6dce42d9ce1069",
        "Created": "2022-11-26T06:48:14.635163039-05:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "c962f206cdca26d0675537514096fff05703996c65383c374ae11b21d24b0fc2": {
                "Name": "ubuntu-A",
                "EndpointID": "89b58f3e7c496f1f392f14e7bf5a6f25d791f6883e9548ffea2a3f4a975df8d5",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

Criando mais um container para a rede "minha-rede", observer que o container criado ja foi associado a rede com outro ip  **"IPv4Address": "172.18.0.3/16"**.

```
┌──(root㉿kali)-[/home/kali]
└─# docker run -dti --name ubuntu-B --network minha-rede ubuntu  
53ff3ae78011f4b847a9b28626f2f99da912808fe90cd4612a2326518a1c72e6
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker ps                                                    
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS          PORTS                                                  NAMES
53ff3ae78011   ubuntu    "bash"                   5 seconds ago       Up 3 seconds                                                           ubuntu-B
c962f206cdca   ubuntu    "bash"                   10 minutes ago      Up 10 minutes                                                          ubuntu-A
e7ffdcb19cad   mysql     "docker-entrypoint.s…"   39 minutes ago      Up 39 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-A
e42fa71d93af   ubuntu    "bash"                   About an hour ago   Up 21 minutes                                                          ubuntu-C
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 

┌──(root㉿kali)-[/home/kali]
└─# docker network inspect minha-rede
[
    {
        "Name": "minha-rede",
        "Id": "6f97c15967793dcd4a1023fcbae11484feece6671409d4068a6dce42d9ce1069",
        "Created": "2022-11-26T06:48:14.635163039-05:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "53ff3ae78011f4b847a9b28626f2f99da912808fe90cd4612a2326518a1c72e6": {
                "Name": "ubuntu-B",
                "EndpointID": "004d246c3232abc6c8070963d187dcd7d301ac44d6b7beac7f45ddfb42e0667f",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            },
            "c962f206cdca26d0675537514096fff05703996c65383c374ae11b21d24b0fc2": {
                "Name": "ubuntu-A",
                "EndpointID": "89b58f3e7c496f1f392f14e7bf5a6f25d791f6883e9548ffea2a3f4a975df8d5",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 

``` 

O container "myql-A" está na rede **"IPv4Address": "172.17.0.3/16",***

```
        "Containers": {       
            "e7ffdcb19cade11990991d60d713e7ff5ec4a74b0bdc02774000f01834c38c5f": {
                "Name": "mysql-A",
                "EndpointID": "857190dcb372e0607c8005a03151ea44319a208d2cbfeafb94ab0ab9fb34bef1",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
```


Os containers "ubuntu-A" e "ubuntu-B" está na rede **"IPv4Address": "172.18.0.2/16", " "172.18.0.3/16"**

```
        "Containers": {
            "53ff3ae78011f4b847a9b28626f2f99da912808fe90cd4612a2326518a1c72e6": {
                "Name": "ubuntu-B",
                "EndpointID": "004d246c3232abc6c8070963d187dcd7d301ac44d6b7beac7f45ddfb42e0667f",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            },
            "c962f206cdca26d0675537514096fff05703996c65383c374ae11b21d24b0fc2": {
                "Name": "ubuntu-A",
                "EndpointID": "89b58f3e7c496f1f392f14e7bf5a6f25d791f6883e9548ffea2a3f4a975df8d5",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""

```

Teste de comunicao entre o "ubuntu-A" com o "ubuntu-B" e "mysql-A".

Obsever que entre o "ubuntu-A" e "ubuntu-B" comunicação com sucesso pois eles está isolado.

Mas no ultimo teste com o "mysql-A" não teve comunicação pois está em outra rede!.

Dessa forma isolamos os containers.

```
┌──(root㉿kali)-[/home/kali]
└─# docker exec -ti ubuntu-A bash
root@c962f206cdca:/# apt update   
Get:1 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]                                
Get:2 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]             
Get:3 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [522 kB]
Get:4 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages [164 kB]
Get:7 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [266 kB]
Get:8 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [618 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages [1792 kB]
Get:10 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [4642 B]
Get:11 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [775 kB]
Get:12 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [17.5 MB]       
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [956 kB]                   
Get:14 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [924 kB]                       
Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [8056 B]                 
Get:16 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [579 kB]                 
Get:17 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [3175 B]                     
Get:18 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [7275 B]                 
Fetched 24.7 MB in 16s (1582 kB/s)                                                                       
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
root@c962f206cdca:/# apt install -y iputils-ping
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libcap2-bin libpam-cap
The following NEW packages will be installed:
  iputils-ping libcap2-bin libpam-cap
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 76.8 kB of archives.
After this operation, 280 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy/main amd64 libcap2-bin amd64 1:2.44-1build3 [26.0 kB]
Get:2 http://archive.ubuntu.com/ubuntu jammy/main amd64 iputils-ping amd64 3:20211215-1 [42.9 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy/main amd64 libpam-cap amd64 1:2.44-1build3 [7932 B]
Fetched 76.8 kB in 1s (67.1 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libcap2-bin.
(Reading database ... 4395 files and directories currently installed.)
Preparing to unpack .../libcap2-bin_1%3a2.44-1build3_amd64.deb ...
Unpacking libcap2-bin (1:2.44-1build3) ...
Selecting previously unselected package iputils-ping.
Preparing to unpack .../iputils-ping_3%3a20211215-1_amd64.deb ...
Unpacking iputils-ping (3:20211215-1) ...
Selecting previously unselected package libpam-cap:amd64.
Preparing to unpack .../libpam-cap_1%3a2.44-1build3_amd64.deb ...
Unpacking libpam-cap:amd64 (1:2.44-1build3) ...
Setting up libcap2-bin (1:2.44-1build3) ...
Setting up libpam-cap:amd64 (1:2.44-1build3) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 78.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.34.0 /usr/local/share/perl/5.34.0 /usr/lib/x86_64-linux-gnu/perl5/5.34 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.34 /usr/share/perl/5.34 /usr/local/lib/site_perl) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up iputils-ping (3:20211215-1) ...
root@c962f206cdca:/# ping 172.18.0.3 -c 3
PING 172.18.0.3 (172.18.0.3) 56(84) bytes of data.
64 bytes from 172.18.0.3: icmp_seq=1 ttl=64 time=0.612 ms
64 bytes from 172.18.0.3: icmp_seq=2 ttl=64 time=0.207 ms
64 bytes from 172.18.0.3: icmp_seq=3 ttl=64 time=0.193 ms

--- 172.18.0.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2027ms
rtt min/avg/max/mdev = 0.193/0.337/0.612/0.194 ms
root@c962f206cdca:/# ping 172.17.0.3 -c 3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.

--- 172.17.0.3 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2048ms

root@c962f206cdca:/# 
```

Excluindo uma rede.

```
┌──(root㉿kali)-[/home/kali]
└─# docker ps                                                    
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS             PORTS                                                  NAMES
53ff3ae78011   ubuntu    "bash"                   22 minutes ago      Up 22 minutes                                                             ubuntu-B
c962f206cdca   ubuntu    "bash"                   32 minutes ago      Up 32 minutes                                                             ubuntu-A
e7ffdcb19cad   mysql     "docker-entrypoint.s…"   About an hour ago   Up About an hour   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-A
e42fa71d93af   ubuntu    "bash"                   2 hours ago         Up 43 minutes                                                             ubuntu-C
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker rm -f ubuntu-A ubuntu-B                               
ubuntu-A
ubuntu-B
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker network --help         

Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

Run 'docker network COMMAND --help' for more information on a command.
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker network rm minha-rede
minha-rede
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

### Definição e Criação de um Docker File

**Primeiro Docker File**

Como criar minha propria imagem do container com as minhas aplicações.

```
┌──(root㉿kali)-[/home/kali]
└─# docker run -dti --name ubuntu-python ubuntu                  
56279a4bebfc4a64caa551e3b8b230da6089ba6c31e64b171edf735649ed7a17
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker exec -ti ubuntu-python bash        
root@56279a4bebfc:/# apt update 
Get:1 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]
Get:2 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:3 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [522 kB]
Get:4 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:6 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [775 kB]
Get:7 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [266 kB]
Get:8 http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages [1792 kB]
Get:9 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [618 kB]  
Get:10 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [4642 B]
Get:11 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages [164 kB]      
Get:12 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [17.5 MB]
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [924 kB]                       
Get:14 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [579 kB]                 
Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [956 kB]                   
Get:16 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [8056 B]                 
Get:17 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [3175 B]                     
Get:18 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [7275 B]                 
Fetched 24.7 MB in 8s (3015 kB/s)                                                                        
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
root@56279a4bebfc:/# apt install -y python3 nano
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libexpat1 libmpdec3 libpython3-stdlib libpython3.10-minimal libpython3.10-stdlib libreadline8
  libsqlite3-0 media-types python3-minimal python3.10 python3.10-minimal readline-common
Suggested packages:
  hunspell python3-doc python3-tk python3-venv python3.10-venv python3.10-doc binutils binfmt-support
  readline-doc
The following NEW packages will be installed:
  libexpat1 libmpdec3 libpython3-stdlib libpython3.10-minimal libpython3.10-stdlib libreadline8
  libsqlite3-0 media-types nano python3 python3-minimal python3.10 python3.10-minimal readline-common
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 6785 kB of archives.
After this operation, 24.3 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpython3.10-minimal amd64 3.10.6-1~22.04.1 [810 kB]
Get:2 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libexpat1 amd64 2.4.7-1ubuntu0.2 [91.0 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3.10-minimal amd64 3.10.6-1~22.04.1 [2263 kB]
Get:4 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3-minimal amd64 3.10.6-1~22.04 [24.3 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy/main amd64 media-types all 7.0.0 [25.5 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 libmpdec3 amd64 2.5.1-2build2 [86.8 kB]
Get:7 http://archive.ubuntu.com/ubuntu jammy/main amd64 readline-common all 8.1.2-1 [53.5 kB]
Get:8 http://archive.ubuntu.com/ubuntu jammy/main amd64 libreadline8 amd64 8.1.2-1 [153 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libsqlite3-0 amd64 3.37.2-2ubuntu0.1 [641 kB]
Get:10 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpython3.10-stdlib amd64 3.10.6-1~22.04.1 [1831 kB]
Get:11 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3.10 amd64 3.10.6-1~22.04.1 [497 kB]
Get:12 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpython3-stdlib amd64 3.10.6-1~22.04 [6910 B]
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3 amd64 3.10.6-1~22.04 [22.8 kB]
Get:14 http://archive.ubuntu.com/ubuntu jammy/main amd64 nano amd64 6.2-1 [280 kB]
Fetched 6785 kB in 5s (1347 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libpython3.10-minimal:amd64.
(Reading database ... 4395 files and directories currently installed.)
Preparing to unpack .../libpython3.10-minimal_3.10.6-1~22.04.1_amd64.deb ...
Unpacking libpython3.10-minimal:amd64 (3.10.6-1~22.04.1) ...
Selecting previously unselected package libexpat1:amd64.
Preparing to unpack .../libexpat1_2.4.7-1ubuntu0.2_amd64.deb ...
Unpacking libexpat1:amd64 (2.4.7-1ubuntu0.2) ...
Selecting previously unselected package python3.10-minimal.
Preparing to unpack .../python3.10-minimal_3.10.6-1~22.04.1_amd64.deb ...
Unpacking python3.10-minimal (3.10.6-1~22.04.1) ...
Setting up libpython3.10-minimal:amd64 (3.10.6-1~22.04.1) ...
Setting up libexpat1:amd64 (2.4.7-1ubuntu0.2) ...
Setting up python3.10-minimal (3.10.6-1~22.04.1) ...
Selecting previously unselected package python3-minimal.
(Reading database ... 4697 files and directories currently installed.)
Preparing to unpack .../0-python3-minimal_3.10.6-1~22.04_amd64.deb ...
Unpacking python3-minimal (3.10.6-1~22.04) ...
Selecting previously unselected package media-types.
Preparing to unpack .../1-media-types_7.0.0_all.deb ...
Unpacking media-types (7.0.0) ...
Selecting previously unselected package libmpdec3:amd64.
Preparing to unpack .../2-libmpdec3_2.5.1-2build2_amd64.deb ...
Unpacking libmpdec3:amd64 (2.5.1-2build2) ...
Selecting previously unselected package readline-common.
Preparing to unpack .../3-readline-common_8.1.2-1_all.deb ...
Unpacking readline-common (8.1.2-1) ...
Selecting previously unselected package libreadline8:amd64.
Preparing to unpack .../4-libreadline8_8.1.2-1_amd64.deb ...
Unpacking libreadline8:amd64 (8.1.2-1) ...
Selecting previously unselected package libsqlite3-0:amd64.
Preparing to unpack .../5-libsqlite3-0_3.37.2-2ubuntu0.1_amd64.deb ...
Unpacking libsqlite3-0:amd64 (3.37.2-2ubuntu0.1) ...
Selecting previously unselected package libpython3.10-stdlib:amd64.
Preparing to unpack .../6-libpython3.10-stdlib_3.10.6-1~22.04.1_amd64.deb ...
Unpacking libpython3.10-stdlib:amd64 (3.10.6-1~22.04.1) ...
Selecting previously unselected package python3.10.
Preparing to unpack .../7-python3.10_3.10.6-1~22.04.1_amd64.deb ...
Unpacking python3.10 (3.10.6-1~22.04.1) ...
Selecting previously unselected package libpython3-stdlib:amd64.
Preparing to unpack .../8-libpython3-stdlib_3.10.6-1~22.04_amd64.deb ...
Unpacking libpython3-stdlib:amd64 (3.10.6-1~22.04) ...
Setting up python3-minimal (3.10.6-1~22.04) ...
Selecting previously unselected package python3.
(Reading database ... 5126 files and directories currently installed.)
Preparing to unpack .../python3_3.10.6-1~22.04_amd64.deb ...
Unpacking python3 (3.10.6-1~22.04) ...
Selecting previously unselected package nano.
Preparing to unpack .../archives/nano_6.2-1_amd64.deb ...
Unpacking nano (6.2-1) ...
Setting up media-types (7.0.0) ...
Setting up libsqlite3-0:amd64 (3.37.2-2ubuntu0.1) ...
Setting up nano (6.2-1) ...
update-alternatives: using /bin/nano to provide /usr/bin/editor (editor) in auto mode
update-alternatives: warning: skip creation of /usr/share/man/man1/editor.1.gz because associated file /usr/share/man/man1/nano.1.gz (of link group editor) doesn't exist
update-alternatives: using /bin/nano to provide /usr/bin/pico (pico) in auto mode
update-alternatives: warning: skip creation of /usr/share/man/man1/pico.1.gz because associated file /usr/share/man/man1/nano.1.gz (of link group pico) doesn't exist
Setting up libmpdec3:amd64 (2.5.1-2build2) ...
Setting up readline-common (8.1.2-1) ...
Setting up libreadline8:amd64 (8.1.2-1) ...
Setting up libpython3.10-stdlib:amd64 (3.10.6-1~22.04.1) ...
Setting up libpython3-stdlib:amd64 (3.10.6-1~22.04) ...
Setting up python3.10 (3.10.6-1~22.04.1) ...
Setting up python3 (3.10.6-1~22.04) ...
running python rtupdate hooks for python3.10...
running python post-rtupdate hooks for python3.10...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
root@56279a4bebfc:/# 
```

Sempre apos a instalação é importante fazer uma limpeza dos arquivos das instalação que foram baixado que não são necessário dentro do container.

```
root@56279a4bebfc:/# apt clean
root@56279a4bebfc:/# 
```

Criando nossa aplicação de simulacao dentro do container.

```
root@56279a4bebfc:/# cd /opt
root@56279a4bebfc:/opt# nano app.py
root@56279a4bebfc:/opt# cat app.py
nome = input("Qual o seu nome? ")
print(nome)
root@56279a4bebfc:/opt# python3 app.y 
Qual o seu nome? Adriano
Adriano
root@56279a4bebfc:/opt# exit
exit
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker ps                         
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS             PORTS                                                  NAMES
56279a4bebfc   ubuntu    "bash"                   10 minutes ago      Up 10 minutes                                                             ubuntu-python
──(root㉿kali)-[/home/kali]
└─# docker exec -ti ubuntu-python python3 /opt/app.py
Qual o seu nome? Adriano
Adriano
                                                                                                          
──(root㉿kali)-[/home/kali]
└─# docker stop ubuntu-python                        
ubuntu-python
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# docker rm ubuntu-python                    
ubuntu-python
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# 
```

Criando nossa imagem do container.

```
┌──(root㉿kali)-[/]
└─# cd /images 
                                                                                                          
┌──(root㉿kali)-[/images]
└─# cd ubuntu-python 
                                                                                                          
┌──(root㉿kali)-[/images/ubuntu-python]
└─# ls
app.py  dockerfile
                                                                                                          
┌──(root㉿kali)-[/images/ubuntu-python]
└─# cat app.py 
nome = input("Qual o seu nome? ")
print(nome)
                                                                                                          
┌──(root㉿kali)-[/images/ubuntu-python]
└─# cat dockerfile 
FROM ubuntu

RUN apt update && apt install -y python3 && apt clean

COPY app.py /opt/app.y

CMD python3 /opt/app.py
                                                                                                          
┌──(root㉿kali)-[/images/ubuntu-python]
└─# docker build . -t ubuntu-python 
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM ubuntu
 ---> a8780b506fa4
Step 2/4 : RUN apt update && apt install -y python3 && apt clean
 ---> Running in 55c0ed915bc7

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Get:1 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]
Get:2 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:3 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [4642 B]
Get:4 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:5 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [522 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:7 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [618 kB]
Get:8 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [266 kB]
Get:9 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [775 kB]
Get:10 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [17.5 MB]
Get:11 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages [164 kB]
Get:12 http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages [1792 kB]
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [956 kB]
Get:14 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [8056 B]
Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [924 kB]
Get:16 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [579 kB]
Get:17 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [7275 B]
Get:18 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [3175 B]
Fetched 24.7 MB in 13s (1934 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
All packages are up to date.

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.                           
                                                                                                          
Reading package lists...                                                                                  
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  libexpat1 libmpdec3 libpython3-stdlib libpython3.10-minimal
  libpython3.10-stdlib libreadline8 libsqlite3-0 media-types python3-minimal
  python3.10 python3.10-minimal readline-common
Suggested packages:
  python3-doc python3-tk python3-venv python3.10-venv python3.10-doc binutils
  binfmt-support readline-doc
The following NEW packages will be installed:
  libexpat1 libmpdec3 libpython3-stdlib libpython3.10-minimal
  libpython3.10-stdlib libreadline8 libsqlite3-0 media-types python3
  python3-minimal python3.10 python3.10-minimal readline-common
0 upgraded, 13 newly installed, 0 to remove and 0 not upgraded.
Need to get 6506 kB of archives.
After this operation, 23.4 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpython3.10-minimal amd64 3.10.6-1~22.04.1 [810 kB]
Get:2 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libexpat1 amd64 2.4.7-1ubuntu0.2 [91.0 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3.10-minimal amd64 3.10.6-1~22.04.1 [2263 kB]
Get:4 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3-minimal amd64 3.10.6-1~22.04 [24.3 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy/main amd64 media-types all 7.0.0 [25.5 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 libmpdec3 amd64 2.5.1-2build2 [86.8 kB]
Get:7 http://archive.ubuntu.com/ubuntu jammy/main amd64 readline-common all 8.1.2-1 [53.5 kB]
Get:8 http://archive.ubuntu.com/ubuntu jammy/main amd64 libreadline8 amd64 8.1.2-1 [153 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libsqlite3-0 amd64 3.37.2-2ubuntu0.1 [641 kB]
Get:10 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpython3.10-stdlib amd64 3.10.6-1~22.04.1 [1831 kB]
Get:11 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3.10 amd64 3.10.6-1~22.04.1 [497 kB]
Get:12 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpython3-stdlib amd64 3.10.6-1~22.04 [6910 B]
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 python3 amd64 3.10.6-1~22.04 [22.8 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 6506 kB in 5s (1341 kB/s)                                                                         
Selecting previously unselected package libpython3.10-minimal:amd64.
(Reading database ... 4395 files and directories currently installed.)
Preparing to unpack .../libpython3.10-minimal_3.10.6-1~22.04.1_amd64.deb ...
Unpacking libpython3.10-minimal:amd64 (3.10.6-1~22.04.1) ...
Selecting previously unselected package libexpat1:amd64.
Preparing to unpack .../libexpat1_2.4.7-1ubuntu0.2_amd64.deb ...
Unpacking libexpat1:amd64 (2.4.7-1ubuntu0.2) ...
Selecting previously unselected package python3.10-minimal.
Preparing to unpack .../python3.10-minimal_3.10.6-1~22.04.1_amd64.deb ...
Unpacking python3.10-minimal (3.10.6-1~22.04.1) ...
Setting up libpython3.10-minimal:amd64 (3.10.6-1~22.04.1) ...
Setting up libexpat1:amd64 (2.4.7-1ubuntu0.2) ...
Setting up python3.10-minimal (3.10.6-1~22.04.1) ...
Selecting previously unselected package python3-minimal.
(Reading database ... 4697 files and directories currently installed.)
Preparing to unpack .../0-python3-minimal_3.10.6-1~22.04_amd64.deb ...
Unpacking python3-minimal (3.10.6-1~22.04) ...
Selecting previously unselected package media-types.
Preparing to unpack .../1-media-types_7.0.0_all.deb ...
Unpacking media-types (7.0.0) ...
Selecting previously unselected package libmpdec3:amd64.
Preparing to unpack .../2-libmpdec3_2.5.1-2build2_amd64.deb ...
Unpacking libmpdec3:amd64 (2.5.1-2build2) ...
Selecting previously unselected package readline-common.
Preparing to unpack .../3-readline-common_8.1.2-1_all.deb ...
Unpacking readline-common (8.1.2-1) ...
Selecting previously unselected package libreadline8:amd64.
Preparing to unpack .../4-libreadline8_8.1.2-1_amd64.deb ...
Unpacking libreadline8:amd64 (8.1.2-1) ...
Selecting previously unselected package libsqlite3-0:amd64.
Preparing to unpack .../5-libsqlite3-0_3.37.2-2ubuntu0.1_amd64.deb ...
Unpacking libsqlite3-0:amd64 (3.37.2-2ubuntu0.1) ...
Selecting previously unselected package libpython3.10-stdlib:amd64.
Preparing to unpack .../6-libpython3.10-stdlib_3.10.6-1~22.04.1_amd64.deb ...
Unpacking libpython3.10-stdlib:amd64 (3.10.6-1~22.04.1) ...
Selecting previously unselected package python3.10.
Preparing to unpack .../7-python3.10_3.10.6-1~22.04.1_amd64.deb ...
Unpacking python3.10 (3.10.6-1~22.04.1) ...
Selecting previously unselected package libpython3-stdlib:amd64.
Preparing to unpack .../8-libpython3-stdlib_3.10.6-1~22.04_amd64.deb ...
Unpacking libpython3-stdlib:amd64 (3.10.6-1~22.04) ...
Setting up python3-minimal (3.10.6-1~22.04) ...
Selecting previously unselected package python3.
(Reading database ... 5126 files and directories currently installed.)
Preparing to unpack .../python3_3.10.6-1~22.04_amd64.deb ...
Unpacking python3 (3.10.6-1~22.04) ...
Setting up media-types (7.0.0) ...
Setting up libsqlite3-0:amd64 (3.37.2-2ubuntu0.1) ...
Setting up libmpdec3:amd64 (2.5.1-2build2) ...
Setting up readline-common (8.1.2-1) ...
Setting up libreadline8:amd64 (8.1.2-1) ...
Setting up libpython3.10-stdlib:amd64 (3.10.6-1~22.04.1) ...
Setting up libpython3-stdlib:amd64 (3.10.6-1~22.04) ...
Setting up python3.10 (3.10.6-1~22.04.1) ...
Setting up python3 (3.10.6-1~22.04) ...
running python rtupdate hooks for python3.10...
running python post-rtupdate hooks for python3.10...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.                           
                                                                                                          
Removing intermediate container 55c0ed915bc7                                                              
 ---> 9e49494d8f79
Step 3/4 : COPY app.py /opt/app.y
 ---> f4ecc147f359
Step 4/4 : CMD python3 /opt/app.py
 ---> Running in a3e6e1afba6d
Removing intermediate container a3e6e1afba6d
 ---> d395bdf57b29
Successfully built d395bdf57b29
Successfully tagged ubuntu-python:latest
                                                                                                          
┌──(root㉿kali)-[/images/ubuntu-python]
└─# docker images                          
REPOSITORY      TAG          IMAGE ID       CREATED          SIZE
ubuntu-python   latest       d395bdf57b29   53 seconds ago   148MB
mysql           latest       3842e9cdffd2   10 days ago      538MB
httpd           latest       8653efc8c72d   10 days ago      145MB
php             7.4-apache   20a3732f422b   11 days ago      453MB
debian          10           1036dd279580   11 days ago      114MB
debian          latest       c31f65dd4cc9   11 days ago      124MB
ubuntu          latest       a8780b506fa4   3 weeks ago      77.8MB
debian          9            662c05203bab   5 months ago     101MB
centos          latest       5d0da3dc9764   14 months ago    231MB
                                                                                                          
┌──(root㉿kali)-[/images/ubuntu-python]
└─# docker run -ti --name meu-app ubuntu-python
Qual o seu nome? Adriano
Adriano
                                                                                                          
┌──(root㉿kali)-[/images/ubuntu-python]
└─# 

```

## Criando uma imagem personalizada do Apache

Vamos criar uma imagem mais complexa com um servidor web, html e css com uma aplicação e outros arquivos.

```
──(root㉿kali)-[/images/ubuntu-python]
└─# cd ..           
                                                                                                          
┌──(root㉿kali)-[/images]
└─# ls
ubuntu-python
                                                                                                          
┌──(root㉿kali)-[/images]
└─# mkdir debian-apache
                                                                                                          
┌──(root㉿kali)-[/images]
└─# cd debian-apache 
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# ls
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# mkdir site         
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# cd site         
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# ls
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# wget http?//site136863367.hospedagemdesites.ws/site1.zip
--2022-11-26 08:57:46--  http://http/?//site136863367.hospedagemdesites.ws/site1.zip
Resolving http (http)... failed: Name or service not known.
wget: unable to resolve host address ‘http’
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# wget http://site136863367.hospedagemdesites.ws/site1.zip
--2022-11-26 08:57:52--  http://site136863367.hospedagemdesites.ws/site1.zip
Resolving site136863367.hospedagemdesites.ws (site136863367.hospedagemdesites.ws)... failed: Name or service not known.
wget: unable to resolve host address ‘site136863367.hospedagemdesites.ws’
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# ls
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# wget http://site1368633667.hospedagemdesites.ws/site1.zip
--2022-11-26 08:59:08--  http://site1368633667.hospedagemdesites.ws/site1.zip
Resolving site1368633667.hospedagemdesites.ws (site1368633667.hospedagemdesites.ws)... 186.202.153.153
Connecting to site1368633667.hospedagemdesites.ws (site1368633667.hospedagemdesites.ws)|186.202.153.153|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 17781671 (17M) [application/zip]
Saving to: ‘site1.zip’

site1.zip                  100%[======================================>]  16.96M   416KB/s    in 38s     

2022-11-26 08:59:47 (457 KB/s) - ‘site1.zip’ saved [17781671/17781671]

┌──(root㉿kali)-[/images/debian-apache/site]
└─# unzip site1.zip     
Archive:  site1.zip
  inflating: about.html              
  inflating: blog.html               
  inflating: blog-single.html        
  inflating: contact.html            
   creating: css/
  inflating: css/style.css           
  inflating: css/style.css.map       
  inflating: elements.html           
   creating: img/
  inflating: img/.DS_Store           
  inflating: img/about.png           
   creating: img/blog/
  inflating: img/blog/.DS_Store      
  inflating: img/blog/ads-banner.jpg  
  inflating: img/blog/blog-home-banner.jpg  
  inflating: img/blog/c1.jpg         
  inflating: img/blog/c2.jpg         
  inflating: img/blog/c3.jpg         
  inflating: img/blog/c4.jpg         
  inflating: img/blog/c5.jpg         
  inflating: img/blog/c6.jpg         
  inflating: img/blog/cat-widget1.jpg  
  inflating: img/blog/cat-widget2.jpg  
  inflating: img/blog/cat-widget3.jpg  
  inflating: img/blog/feature-img1.jpg  
  inflating: img/blog/feature-img2.jpg  
  inflating: img/blog/feature-img3.jpg  
  inflating: img/blog/feature-img4.jpg  
  inflating: img/blog/feature-img5.jpg  
  inflating: img/blog/next.jpg       
  inflating: img/blog/post-img1.jpg  
  inflating: img/blog/post-img2.jpg  
  inflating: img/blog/pp1.jpg        
  inflating: img/blog/pp2.jpg        
  inflating: img/blog/pp3.jpg        
  inflating: img/blog/pp4.jpg        
  inflating: img/blog/prev.jpg       
  inflating: img/blog/s-img.jpg      
  inflating: img/blog/user-info.png  
  inflating: img/blog1.png           
  inflating: img/blog2.png           
  inflating: img/blog3.png           
  inflating: img/blog4.png           
  inflating: img/blog5.png           
   creating: img/elements/
  inflating: img/elements/.DS_Store  
  inflating: img/elements/a.jpg      
  inflating: img/elements/a2.jpg     
  inflating: img/elements/d.jpg      
  inflating: img/elements/disabled-check.png  
  inflating: img/elements/disabled-radio.png  
  inflating: img/elements/f1.jpg     
  inflating: img/elements/f2.jpg     
  inflating: img/elements/f3.jpg     
  inflating: img/elements/f4.jpg     
  inflating: img/elements/f5.jpg     
  inflating: img/elements/f6.jpg     
  inflating: img/elements/f7.jpg     
  inflating: img/elements/f8.jpg     
  inflating: img/elements/g1.jpg     
  inflating: img/elements/g2.jpg     
  inflating: img/elements/g3.jpg     
  inflating: img/elements/g4.jpg     
  inflating: img/elements/g5.jpg     
  inflating: img/elements/g6.jpg     
  inflating: img/elements/g7.jpg     
  inflating: img/elements/g8.jpg     
  inflating: img/elements/primary-check.png  
  inflating: img/elements/primary-radio.png  
  inflating: img/elements/success-check.png  
  inflating: img/elements/success-radio.png  
  inflating: img/elements/user1.png  
  inflating: img/elements/user2.png  
  inflating: img/footer-bg.png       
  inflating: img/hero-1.png          
  inflating: img/hero-2.png          
  inflating: img/hero-3.png          
   creating: img/icons/
  inflating: img/logo.png            
  inflating: img/portfolio1.png      
  inflating: img/portfolio2.png      
  inflating: img/portfolio3.png      
  inflating: img/portfolio4.png      
  inflating: img/project-bg.png      
  inflating: img/testimonial.png     
  inflating: index.html              
   creating: js/
  inflating: js/main.js              
  inflating: projects.html           
   creating: scss/
  inflating: scss/.DS_Store          
  inflating: scss/style.scss         
   creating: scss/themes/
  inflating: scss/themes/_about.scss  
  inflating: scss/themes/_blog.scss  
  inflating: scss/themes/_breadcrumb.scss  
  inflating: scss/themes/_btn.scss   
  inflating: scss/themes/_contact.scss  
  inflating: scss/themes/_elements.scss  
  inflating: scss/themes/_footer.scss  
  inflating: scss/themes/_form.scss  
  inflating: scss/themes/_global.scss  
  inflating: scss/themes/_header.scss  
  inflating: scss/themes/_hero.scss  
  inflating: scss/themes/_mixin.scss  
  inflating: scss/themes/_overview.scss  
  inflating: scss/themes/_section-intro.scss  
  inflating: scss/themes/_service.scss  
  inflating: scss/themes/_slider.scss  
  inflating: scss/themes/_social-icons.scss  
  inflating: scss/themes/_tips.scss  
  inflating: scss/themes/_variables.scss  
  inflating: service.html            
   creating: Sparsh Architecture - Doc/
  inflating: Sparsh Architecture - Doc/.DS_Store  
   creating: Sparsh Architecture - Doc/css/
  inflating: Sparsh Architecture - Doc/css/.DS_Store  
  inflating: Sparsh Architecture - Doc/css/font-awesome.min.css  
  inflating: Sparsh Architecture - Doc/css/main.css  
  inflating: Sparsh Architecture - Doc/css/normalize.min.css  
   creating: Sparsh Architecture - Doc/fonts/
  inflating: Sparsh Architecture - Doc/fonts/.DS_Store  
  inflating: Sparsh Architecture - Doc/fonts/FontAwesome.otf  
  inflating: Sparsh Architecture - Doc/fonts/fontawesome-webfont.eot  
  inflating: Sparsh Architecture - Doc/fonts/fontawesome-webfont.svg  
  inflating: Sparsh Architecture - Doc/fonts/fontawesome-webfont.ttf  
  inflating: Sparsh Architecture - Doc/fonts/fontawesome-webfont.woff  
   creating: Sparsh Architecture - Doc/img/
  inflating: Sparsh Architecture - Doc/img/.DS_Store  
  inflating: Sparsh Architecture - Doc/img/logo.png  
  inflating: Sparsh Architecture - Doc/index.html  
   creating: Sparsh Architecture - Doc/js/
  inflating: Sparsh Architecture - Doc/js/.DS_Store  
  inflating: Sparsh Architecture - Doc/js/custom.js  
  inflating: Sparsh Architecture - Doc/js/jquery.nav.js  
  inflating: Sparsh Architecture - Doc/js/jquery-1.11.0.min.js  
   creating: Sparsh Architecture - Doc/syntax-highlighter/
  inflating: Sparsh Architecture - Doc/syntax-highlighter/.DS_Store  
   creating: Sparsh Architecture - Doc/syntax-highlighter/scripts/
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shAutoloader.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushAppleScript.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushAS3.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushBash.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushColdFusion.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushCpp.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushCSharp.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushCss.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushDelphi.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushDiff.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushErlang.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushGroovy.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushJava.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushJavaFX.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushJScript.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushPerl.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushPhp.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushPlain.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushPowerShell.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushPython.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushRuby.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushSass.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushScala.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushSql.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushVb.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shBrushXml.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shCore.js  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/scripts/shLegacy.js  
   creating: Sparsh Architecture - Doc/syntax-highlighter/styles/
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCore.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreDefault.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreDjango.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreEclipse.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreEmacs.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreFadeToGrey.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreMDUltra.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreMidnight.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shCoreRDark.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeDefault.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeDjango.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeEclipse.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeEmacs.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeFadeToGrey.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeMDUltra.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeMidnight.css  
  inflating: Sparsh Architecture - Doc/syntax-highlighter/styles/shThemeRDark.css  
   creating: vendor/
  inflating: vendor/.DS_Store        
   creating: vendor/bootstrap/
  inflating: vendor/bootstrap/bootstrap.bundle.min.js  
  inflating: vendor/bootstrap/bootstrap.min.css  
   creating: vendor/flat-icon/
  inflating: vendor/flat-icon/_flaticon.scss  
  inflating: vendor/flat-icon/backup.txt  
  inflating: vendor/flat-icon/debug.log  
  inflating: vendor/flat-icon/flaticon.css  
  inflating: vendor/flat-icon/Flaticon.eot  
  inflating: vendor/flat-icon/flaticon.html  
  inflating: vendor/flat-icon/Flaticon.svg  
  inflating: vendor/flat-icon/Flaticon.ttf  
  inflating: vendor/flat-icon/Flaticon.woff  
   creating: vendor/flat-icon/license/
  inflating: vendor/flat-icon/license/license.html  
   creating: vendor/fonts/
  inflating: vendor/fonts/CircularStd-Black.otf  
  inflating: vendor/fonts/CircularStd-BlackItalic.otf  
  inflating: vendor/fonts/CircularStd-Bold.otf  
  inflating: vendor/fonts/CircularStd-BoldItalic.otf  
  inflating: vendor/fonts/CircularStd-Book.otf  
  inflating: vendor/fonts/CircularStd-BookItalic.otf  
  inflating: vendor/fonts/CircularStd-Medium.otf  
  inflating: vendor/fonts/CircularStd-MediumItalic.otf  
   creating: vendor/iconfont/
  inflating: vendor/iconfont/codepoints  
  inflating: vendor/iconfont/debug.log  
  inflating: vendor/iconfont/material-icons.css  
  inflating: vendor/iconfont/MaterialIcons-Regular.eot  
  inflating: vendor/iconfont/MaterialIcons-Regular.ijmap  
  inflating: vendor/iconfont/MaterialIcons-Regular.svg  
  inflating: vendor/iconfont/MaterialIcons-Regular.ttf  
  inflating: vendor/iconfont/MaterialIcons-Regular.woff  
  inflating: vendor/iconfont/MaterialIcons-Regular.woff2  
  inflating: vendor/iconfont/README.md  
   creating: vendor/jquery/
  inflating: vendor/jquery/jquery-3.2.1.min.js  
   creating: vendor/js/
  inflating: vendor/js/main.js       
   creating: vendor/owl-carousel/
  inflating: vendor/owl-carousel/owl.carousel.min.css  
  inflating: vendor/owl-carousel/owl.carousel.min.js  
  inflating: vendor/owl-carousel/owl.theme.default.min.css  
   creating: vendor/scripts/
  inflating: vendor/scripts/App.js   
   creating: vendor/scripts/modules/
  inflating: vendor/scripts/modules/Slider.js  
  inflating: vendor/scripts/Vendor.js  
   creating: vendor/simplyCountdown/
  inflating: vendor/simplyCountdown/.gitignore  
  inflating: vendor/simplyCountdown/bower.json  
   creating: vendor/simplyCountdown/css/
  inflating: vendor/simplyCountdown/css/demo.css  
   creating: vendor/simplyCountdown/css/demo-only/
  inflating: vendor/simplyCountdown/css/demo-only/prism.css  
   creating: vendor/simplyCountdown/css/less/
  inflating: vendor/simplyCountdown/css/less/demo.less  
  inflating: vendor/simplyCountdown/css/less/simplyCountdown.theme.default.less  
  inflating: vendor/simplyCountdown/css/less/simplyCountdown.theme.losange.less  
  inflating: vendor/simplyCountdown/css/simplyCountdown.theme.custom.css  
  inflating: vendor/simplyCountdown/css/simplyCountdown.theme.default.css  
  inflating: vendor/simplyCountdown/css/simplyCountdown.theme.losange.css  
   creating: vendor/simplyCountdown/dev/
  inflating: vendor/simplyCountdown/dev/simplyCountdown.js  
  inflating: vendor/simplyCountdown/gulpfile.js  
  inflating: vendor/simplyCountdown/index.html  
  inflating: vendor/simplyCountdown/LICENSE  
  inflating: vendor/simplyCountdown/package.json  
  inflating: vendor/simplyCountdown/README.md  
   creating: vendor/simplyCountdown/dist/
  inflating: vendor/simplyCountdown/dist/simplyCountdown.min.js  
   creating: vendor/themify-icons/
   creating: vendor/themify-icons/demo-files/
  inflating: vendor/themify-icons/demo-files/demo.css  
   creating: vendor/themify-icons/fonts/
  inflating: vendor/themify-icons/fonts/themify.eot  
  inflating: vendor/themify-icons/fonts/themify.svg  
  inflating: vendor/themify-icons/fonts/themify.ttf  
  inflating: vendor/themify-icons/fonts/themify.woff  
   creating: vendor/themify-icons/ie7/
  inflating: vendor/themify-icons/ie7/ie7.css  
  inflating: vendor/themify-icons/ie7/ie7.js  
  inflating: vendor/themify-icons/index.html  
  inflating: vendor/themify-icons/readme.txt  
   creating: vendor/themify-icons/SVG/
  inflating: vendor/themify-icons/SVG/agenda.svg  
  inflating: vendor/themify-icons/SVG/alarm-clock.svg  
  inflating: vendor/themify-icons/SVG/alert.svg  
  inflating: vendor/themify-icons/SVG/align-center.svg  
  inflating: vendor/themify-icons/SVG/align-justify.svg  
  inflating: vendor/themify-icons/SVG/align-left.svg  
  inflating: vendor/themify-icons/SVG/align-right.svg  
  inflating: vendor/themify-icons/SVG/anchor.svg  
  inflating: vendor/themify-icons/SVG/android.svg  
  inflating: vendor/themify-icons/SVG/angle-double-down.svg  
  inflating: vendor/themify-icons/SVG/angle-double-left.svg  
  inflating: vendor/themify-icons/SVG/angle-double-right.svg  
  inflating: vendor/themify-icons/SVG/angle-double-up.svg  
  inflating: vendor/themify-icons/SVG/angle-down.svg  
  inflating: vendor/themify-icons/SVG/angle-left.svg  
  inflating: vendor/themify-icons/SVG/angle-right.svg  
  inflating: vendor/themify-icons/SVG/angle-up.svg  
  inflating: vendor/themify-icons/SVG/announcement.svg  
  inflating: vendor/themify-icons/SVG/apple.svg  
  inflating: vendor/themify-icons/SVG/archive.svg  
  inflating: vendor/themify-icons/SVG/arrow.svg  
  inflating: vendor/themify-icons/SVG/arrow-circle-down.svg  
  inflating: vendor/themify-icons/SVG/arrow-circle-left.svg  
  inflating: vendor/themify-icons/SVG/arrow-circle-right.svg  
  inflating: vendor/themify-icons/SVG/arrow-circle-up.svg  
  inflating: vendor/themify-icons/SVG/arrow-down.svg  
  inflating: vendor/themify-icons/SVG/arrow-left.svg  
  inflating: vendor/themify-icons/SVG/arrow-right.svg  
  inflating: vendor/themify-icons/SVG/arrows-corner.svg  
  inflating: vendor/themify-icons/SVG/arrows-horizontal.svg  
  inflating: vendor/themify-icons/SVG/arrows-vertical.svg  
  inflating: vendor/themify-icons/SVG/arrow-top-left.svg  
  inflating: vendor/themify-icons/SVG/arrow-top-right.svg  
  inflating: vendor/themify-icons/SVG/arrow-up.svg  
  inflating: vendor/themify-icons/SVG/back-left.svg  
  inflating: vendor/themify-icons/SVG/back-right.svg  
  inflating: vendor/themify-icons/SVG/bag.svg  
  inflating: vendor/themify-icons/SVG/bar-chart.svg  
  inflating: vendor/themify-icons/SVG/bar-chart-alt.svg  
  inflating: vendor/themify-icons/SVG/basketball.svg  
  inflating: vendor/themify-icons/SVG/bell.svg  
  inflating: vendor/themify-icons/SVG/blackboard.svg  
  inflating: vendor/themify-icons/SVG/bolt.svg  
  inflating: vendor/themify-icons/SVG/bolt-alt.svg  
  inflating: vendor/themify-icons/SVG/book.svg  
  inflating: vendor/themify-icons/SVG/bookmark.svg  
  inflating: vendor/themify-icons/SVG/bookmark-alt.svg  
  inflating: vendor/themify-icons/SVG/briefcase.svg  
  inflating: vendor/themify-icons/SVG/brush.svg  
  inflating: vendor/themify-icons/SVG/brush-alt.svg  
  inflating: vendor/themify-icons/SVG/calendar.svg  
  inflating: vendor/themify-icons/SVG/camera.svg  
  inflating: vendor/themify-icons/SVG/car.svg  
  inflating: vendor/themify-icons/SVG/check.svg  
  inflating: vendor/themify-icons/SVG/check-box.svg  
  inflating: vendor/themify-icons/SVG/clip.svg  
  inflating: vendor/themify-icons/SVG/clipboard.svg  
  inflating: vendor/themify-icons/SVG/close.svg  
  inflating: vendor/themify-icons/SVG/cloud.svg  
  inflating: vendor/themify-icons/SVG/cloud-down.svg  
  inflating: vendor/themify-icons/SVG/cloud-up.svg  
  inflating: vendor/themify-icons/SVG/comment.svg  
  inflating: vendor/themify-icons/SVG/comment-alt.svg  
  inflating: vendor/themify-icons/SVG/comments.svg  
  inflating: vendor/themify-icons/SVG/comments-smiley.svg  
  inflating: vendor/themify-icons/SVG/control-backward.svg  
  inflating: vendor/themify-icons/SVG/control-eject.svg  
  inflating: vendor/themify-icons/SVG/control-forward.svg  
  inflating: vendor/themify-icons/SVG/control-pause.svg  
  inflating: vendor/themify-icons/SVG/control-play.svg  
  inflating: vendor/themify-icons/SVG/control-record.svg  
  inflating: vendor/themify-icons/SVG/control-shuffle.svg  
  inflating: vendor/themify-icons/SVG/control-skip-backward.svg  
  inflating: vendor/themify-icons/SVG/control-skip-forward.svg  
  inflating: vendor/themify-icons/SVG/control-stop.svg  
  inflating: vendor/themify-icons/SVG/credit-card.svg  
  inflating: vendor/themify-icons/SVG/crown.svg  
  inflating: vendor/themify-icons/SVG/css3.svg  
  inflating: vendor/themify-icons/SVG/cup.svg  
  inflating: vendor/themify-icons/SVG/cut.svg  
  inflating: vendor/themify-icons/SVG/dashboard.svg  
  inflating: vendor/themify-icons/SVG/desktop.svg  
  inflating: vendor/themify-icons/SVG/direction.svg  
  inflating: vendor/themify-icons/SVG/direction-alt.svg  
  inflating: vendor/themify-icons/SVG/download.svg  
  inflating: vendor/themify-icons/SVG/dribbble.svg  
  inflating: vendor/themify-icons/SVG/dropbox.svg  
  inflating: vendor/themify-icons/SVG/dropbox-alt.svg  
  inflating: vendor/themify-icons/SVG/drupal.svg  
  inflating: vendor/themify-icons/SVG/email.svg  
  inflating: vendor/themify-icons/SVG/envelope.svg  
  inflating: vendor/themify-icons/SVG/eraser.svg  
  inflating: vendor/themify-icons/SVG/exchange-vertical.svg  
  inflating: vendor/themify-icons/SVG/export.svg  
  inflating: vendor/themify-icons/SVG/eye.svg  
  inflating: vendor/themify-icons/SVG/facebook.svg  
  inflating: vendor/themify-icons/SVG/face-sad.svg  
  inflating: vendor/themify-icons/SVG/face-smile.svg  
  inflating: vendor/themify-icons/SVG/file.svg  
  inflating: vendor/themify-icons/SVG/files.svg  
  inflating: vendor/themify-icons/SVG/filter.svg  
  inflating: vendor/themify-icons/SVG/flag.svg  
  inflating: vendor/themify-icons/SVG/flag-alt.svg  
  inflating: vendor/themify-icons/SVG/flag-alt-2.svg  
  inflating: vendor/themify-icons/SVG/flickr.svg  
  inflating: vendor/themify-icons/SVG/flickr-alt.svg  
  inflating: vendor/themify-icons/SVG/folder.svg  
  inflating: vendor/themify-icons/SVG/fullscreen.svg  
  inflating: vendor/themify-icons/SVG/gallery.svg  
  inflating: vendor/themify-icons/SVG/game.svg  
  inflating: vendor/themify-icons/SVG/gift.svg  
  inflating: vendor/themify-icons/SVG/github.svg  
  inflating: vendor/themify-icons/SVG/google.svg  
  inflating: vendor/themify-icons/SVG/hand-drag.svg  
  inflating: vendor/themify-icons/SVG/hand-open.svg  
  inflating: vendor/themify-icons/SVG/hand-point-down.svg  
  inflating: vendor/themify-icons/SVG/hand-point-left.svg  
  inflating: vendor/themify-icons/SVG/hand-point-right.svg  
  inflating: vendor/themify-icons/SVG/hand-point-up.svg  
  inflating: vendor/themify-icons/SVG/hand-stop.svg  
  inflating: vendor/themify-icons/SVG/harddrive.svg  
  inflating: vendor/themify-icons/SVG/harddrives.svg  
  inflating: vendor/themify-icons/SVG/headphone.svg  
  inflating: vendor/themify-icons/SVG/headphone-alt.svg  
  inflating: vendor/themify-icons/SVG/heart.svg  
  inflating: vendor/themify-icons/SVG/heart-broken.svg  
  inflating: vendor/themify-icons/SVG/help.svg  
  inflating: vendor/themify-icons/SVG/help-alt.svg  
  inflating: vendor/themify-icons/SVG/home.svg  
  inflating: vendor/themify-icons/SVG/html5.svg  
  inflating: vendor/themify-icons/SVG/hummer.svg  
  inflating: vendor/themify-icons/SVG/id-badge.svg  
  inflating: vendor/themify-icons/SVG/image.svg  
  inflating: vendor/themify-icons/SVG/import.svg  
  inflating: vendor/themify-icons/SVG/infinite.svg  
  inflating: vendor/themify-icons/SVG/info.svg  
  inflating: vendor/themify-icons/SVG/info-alt.svg  
  inflating: vendor/themify-icons/SVG/ink-pen.svg  
  inflating: vendor/themify-icons/SVG/instagram.svg  
  inflating: vendor/themify-icons/SVG/Italic.svg  
  inflating: vendor/themify-icons/SVG/joomla.svg  
  inflating: vendor/themify-icons/SVG/jsfiddle.svg  
  inflating: vendor/themify-icons/SVG/key.svg  
  inflating: vendor/themify-icons/SVG/layers.svg  
  inflating: vendor/themify-icons/SVG/layers-alt.svg  
  inflating: vendor/themify-icons/SVG/layout.svg  
  inflating: vendor/themify-icons/SVG/layout-accordion-list.svg  
  inflating: vendor/themify-icons/SVG/layout-accordion-merged.svg  
  inflating: vendor/themify-icons/SVG/layout-accordion-separated.svg  
  inflating: vendor/themify-icons/SVG/layout-column2.svg  
  inflating: vendor/themify-icons/SVG/layout-column2-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-column3.svg  
  inflating: vendor/themify-icons/SVG/layout-column3-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-column4.svg  
  inflating: vendor/themify-icons/SVG/layout-column4-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-cta-btn-left.svg  
  inflating: vendor/themify-icons/SVG/layout-cta-btn-right.svg  
  inflating: vendor/themify-icons/SVG/layout-cta-center.svg  
  inflating: vendor/themify-icons/SVG/layout-cta-left.svg  
  inflating: vendor/themify-icons/SVG/layout-cta-right.svg  
  inflating: vendor/themify-icons/SVG/layout-grid2.svg  
  inflating: vendor/themify-icons/SVG/layout-grid2-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-grid2-thumb.svg  
  inflating: vendor/themify-icons/SVG/layout-grid3.svg  
  inflating: vendor/themify-icons/SVG/layout-grid3-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-grid4.svg  
  inflating: vendor/themify-icons/SVG/layout-grid4-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-line-solid.svg  
  inflating: vendor/themify-icons/SVG/layout-list-large-image.svg  
  inflating: vendor/themify-icons/SVG/layout-list-post.svg  
  inflating: vendor/themify-icons/SVG/layout-list-thumb.svg  
  inflating: vendor/themify-icons/SVG/layout-list-thumb-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-media-center.svg  
  inflating: vendor/themify-icons/SVG/layout-media-center-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-media-left.svg  
  inflating: vendor/themify-icons/SVG/layout-media-left-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-media-overlay.svg  
  inflating: vendor/themify-icons/SVG/layout-media-overlay-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-media-overlay-alt-2.svg  
  inflating: vendor/themify-icons/SVG/layout-media-right.svg  
  inflating: vendor/themify-icons/SVG/layout-media-right-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-menu.svg  
  inflating: vendor/themify-icons/SVG/layout-menu-full.svg  
  inflating: vendor/themify-icons/SVG/layout-menu-separated.svg  
  inflating: vendor/themify-icons/SVG/layout-menu-v.svg  
  inflating: vendor/themify-icons/SVG/layout-placeholder.svg  
  inflating: vendor/themify-icons/SVG/layout-sidebar-2.svg  
  inflating: vendor/themify-icons/SVG/layout-sidebar-left.svg  
  inflating: vendor/themify-icons/SVG/layout-sidebar-none.svg  
  inflating: vendor/themify-icons/SVG/layout-sidebar-right.svg  
  inflating: vendor/themify-icons/SVG/layout-slider.svg  
  inflating: vendor/themify-icons/SVG/layout-slider-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-tab.svg  
  inflating: vendor/themify-icons/SVG/layout-tab-min.svg  
  inflating: vendor/themify-icons/SVG/layout-tab-v.svg  
  inflating: vendor/themify-icons/SVG/layout-tab-window.svg  
  inflating: vendor/themify-icons/SVG/layout-width-default.svg  
  inflating: vendor/themify-icons/SVG/layout-width-default-alt.svg  
  inflating: vendor/themify-icons/SVG/layout-width-full.svg  
  inflating: vendor/themify-icons/SVG/light-bulb.svg  
  inflating: vendor/themify-icons/SVG/line-dashed.svg  
  inflating: vendor/themify-icons/SVG/line-dotted.svg  
  inflating: vendor/themify-icons/SVG/line-double.svg  
  inflating: vendor/themify-icons/SVG/link.svg  
  inflating: vendor/themify-icons/SVG/linkedin.svg  
  inflating: vendor/themify-icons/SVG/linux.svg  
  inflating: vendor/themify-icons/SVG/list.svg  
  inflating: vendor/themify-icons/SVG/list-ol.svg  
  inflating: vendor/themify-icons/SVG/location-arrow.svg  
  inflating: vendor/themify-icons/SVG/location-pin.svg  
  inflating: vendor/themify-icons/SVG/lock.svg  
  inflating: vendor/themify-icons/SVG/loop.svg  
  inflating: vendor/themify-icons/SVG/magnet.svg  
  inflating: vendor/themify-icons/SVG/map.svg  
  inflating: vendor/themify-icons/SVG/map-alt.svg  
  inflating: vendor/themify-icons/SVG/marker.svg  
  inflating: vendor/themify-icons/SVG/marker-alt.svg  
  inflating: vendor/themify-icons/SVG/medall.svg  
  inflating: vendor/themify-icons/SVG/medall-alt.svg  
  inflating: vendor/themify-icons/SVG/menu.svg  
  inflating: vendor/themify-icons/SVG/menu-alt.svg  
  inflating: vendor/themify-icons/SVG/microphone.svg  
  inflating: vendor/themify-icons/SVG/microphone-alt.svg  
  inflating: vendor/themify-icons/SVG/microsoft.svg  
  inflating: vendor/themify-icons/SVG/microsoft-alt.svg  
  inflating: vendor/themify-icons/SVG/minus.svg  
  inflating: vendor/themify-icons/SVG/mobile.svg  
  inflating: vendor/themify-icons/SVG/money.svg  
  inflating: vendor/themify-icons/SVG/more.svg  
  inflating: vendor/themify-icons/SVG/more-alt.svg  
  inflating: vendor/themify-icons/SVG/mouse.svg  
  inflating: vendor/themify-icons/SVG/mouse-alt.svg  
  inflating: vendor/themify-icons/SVG/music.svg  
  inflating: vendor/themify-icons/SVG/music-alt.svg  
  inflating: vendor/themify-icons/SVG/na.svg  
  inflating: vendor/themify-icons/SVG/new-window.svg  
  inflating: vendor/themify-icons/SVG/notepad.svg  
  inflating: vendor/themify-icons/SVG/package.svg  
  inflating: vendor/themify-icons/SVG/paint-bucket.svg  
  inflating: vendor/themify-icons/SVG/paint-roller.svg  
  inflating: vendor/themify-icons/SVG/palette.svg  
  inflating: vendor/themify-icons/SVG/panel.svg  
  inflating: vendor/themify-icons/SVG/paragraph.svg  
  inflating: vendor/themify-icons/SVG/pencil.svg  
  inflating: vendor/themify-icons/SVG/pencil-alt.svg  
  inflating: vendor/themify-icons/SVG/pencil-alt2.svg  
  inflating: vendor/themify-icons/SVG/pie-chart.svg  
  inflating: vendor/themify-icons/SVG/pin.svg  
  inflating: vendor/themify-icons/SVG/pin2.svg  
  inflating: vendor/themify-icons/SVG/pin-alt.svg  
  inflating: vendor/themify-icons/SVG/pinterest.svg  
  inflating: vendor/themify-icons/SVG/pinterest-alt.svg  
  inflating: vendor/themify-icons/SVG/plug.svg  
  inflating: vendor/themify-icons/SVG/plus.svg  
  inflating: vendor/themify-icons/SVG/power-off.svg  
  inflating: vendor/themify-icons/SVG/printer.svg  
  inflating: vendor/themify-icons/SVG/pulse.svg  
  inflating: vendor/themify-icons/SVG/quote-left.svg  
  inflating: vendor/themify-icons/SVG/quote-right.svg  
  inflating: vendor/themify-icons/SVG/receipt.svg  
  inflating: vendor/themify-icons/SVG/reddit.svg  
  inflating: vendor/themify-icons/SVG/reload.svg  
  inflating: vendor/themify-icons/SVG/rocket.svg  
  inflating: vendor/themify-icons/SVG/rss.svg  
  inflating: vendor/themify-icons/SVG/rss-alt.svg  
  inflating: vendor/themify-icons/SVG/ruler.svg  
  inflating: vendor/themify-icons/SVG/ruler-alt.svg  
  inflating: vendor/themify-icons/SVG/ruler-alt-2.svg  
  inflating: vendor/themify-icons/SVG/ruler-pencil.svg  
  inflating: vendor/themify-icons/SVG/save.svg  
  inflating: vendor/themify-icons/SVG/save-alt.svg  
  inflating: vendor/themify-icons/SVG/search.svg  
  inflating: vendor/themify-icons/SVG/server.svg  
  inflating: vendor/themify-icons/SVG/settings.svg  
  inflating: vendor/themify-icons/SVG/share.svg  
  inflating: vendor/themify-icons/SVG/share-alt.svg  
  inflating: vendor/themify-icons/SVG/sharethis.svg  
  inflating: vendor/themify-icons/SVG/sharethis-alt.svg  
  inflating: vendor/themify-icons/SVG/shield.svg  
  inflating: vendor/themify-icons/SVG/shift-left.svg  
  inflating: vendor/themify-icons/SVG/shift-left-alt.svg  
  inflating: vendor/themify-icons/SVG/shift-right.svg  
  inflating: vendor/themify-icons/SVG/shift-right-alt.svg  
  inflating: vendor/themify-icons/SVG/shine.svg  
  inflating: vendor/themify-icons/SVG/shopping-cart.svg  
  inflating: vendor/themify-icons/SVG/shopping-cart-full.svg  
  inflating: vendor/themify-icons/SVG/shortcode.svg  
  inflating: vendor/themify-icons/SVG/signal.svg  
  inflating: vendor/themify-icons/SVG/skype.svg  
  inflating: vendor/themify-icons/SVG/slice.svg  
  inflating: vendor/themify-icons/SVG/smallcap.svg  
  inflating: vendor/themify-icons/SVG/soundcloud.svg  
  inflating: vendor/themify-icons/SVG/split-h.svg  
  inflating: vendor/themify-icons/SVG/split-v.svg  
  inflating: vendor/themify-icons/SVG/split-v-alt.svg  
  inflating: vendor/themify-icons/SVG/spray.svg  
  inflating: vendor/themify-icons/SVG/stack-overflow.svg  
  inflating: vendor/themify-icons/SVG/stamp.svg  
  inflating: vendor/themify-icons/SVG/star.svg  
  inflating: vendor/themify-icons/SVG/stats-down.svg  
  inflating: vendor/themify-icons/SVG/stats-up.svg  
  inflating: vendor/themify-icons/SVG/support.svg  
  inflating: vendor/themify-icons/SVG/tablet.svg  
  inflating: vendor/themify-icons/SVG/tag.svg  
  inflating: vendor/themify-icons/SVG/target.svg  
  inflating: vendor/themify-icons/SVG/text.svg  
  inflating: vendor/themify-icons/SVG/themify-favicon.svg  
  inflating: vendor/themify-icons/SVG/themify-favicon-alt.svg  
  inflating: vendor/themify-icons/SVG/themify-logo.svg  
  inflating: vendor/themify-icons/SVG/thought.svg  
  inflating: vendor/themify-icons/SVG/thumb-down.svg  
  inflating: vendor/themify-icons/SVG/thumb-up.svg  
  inflating: vendor/themify-icons/SVG/ticket.svg  
  inflating: vendor/themify-icons/SVG/time.svg  
  inflating: vendor/themify-icons/SVG/timer.svg  
  inflating: vendor/themify-icons/SVG/trash.svg  
  inflating: vendor/themify-icons/SVG/trello.svg  
  inflating: vendor/themify-icons/SVG/truck.svg  
  inflating: vendor/themify-icons/SVG/tumblr.svg  
  inflating: vendor/themify-icons/SVG/tumblr-alt.svg  
  inflating: vendor/themify-icons/SVG/twitter.svg  
  inflating: vendor/themify-icons/SVG/twitter-alt.svg  
  inflating: vendor/themify-icons/SVG/underline.svg  
  inflating: vendor/themify-icons/SVG/unlink.svg  
  inflating: vendor/themify-icons/SVG/unlock.svg  
  inflating: vendor/themify-icons/SVG/upload.svg  
  inflating: vendor/themify-icons/SVG/uppercase.svg  
  inflating: vendor/themify-icons/SVG/user.svg  
  inflating: vendor/themify-icons/SVG/vector.svg  
  inflating: vendor/themify-icons/SVG/video-camera.svg  
  inflating: vendor/themify-icons/SVG/video-clapper.svg  
  inflating: vendor/themify-icons/SVG/view-grid.svg  
  inflating: vendor/themify-icons/SVG/view-list.svg  
  inflating: vendor/themify-icons/SVG/view-list-alt.svg  
  inflating: vendor/themify-icons/SVG/vimeo.svg  
  inflating: vendor/themify-icons/SVG/vimeo-alt.svg  
  inflating: vendor/themify-icons/SVG/volume.svg  
  inflating: vendor/themify-icons/SVG/wallet.svg  
  inflating: vendor/themify-icons/SVG/wand.svg  
  inflating: vendor/themify-icons/SVG/wheelchair.svg  
  inflating: vendor/themify-icons/SVG/widget.svg  
  inflating: vendor/themify-icons/SVG/widget-alt.svg  
  inflating: vendor/themify-icons/SVG/widgetized.svg  
  inflating: vendor/themify-icons/SVG/window.svg  
  inflating: vendor/themify-icons/SVG/wordpress.svg  
  inflating: vendor/themify-icons/SVG/world.svg  
  inflating: vendor/themify-icons/SVG/write.svg  
  inflating: vendor/themify-icons/SVG/yahoo.svg  
  inflating: vendor/themify-icons/SVG/youtube.svg  
  inflating: vendor/themify-icons/SVG/zip.svg  
  inflating: vendor/themify-icons/SVG/zoom-in.svg  
  inflating: vendor/themify-icons/SVG/zoom-out.svg  
  inflating: vendor/themify-icons/Themify IconFonts 5-23-2014.json  
  inflating: vendor/themify-icons/themify-icons.css  
                                                                                                          
──(root㉿kali)-[/images/debian-apache/site]
└─# ls
 about.html         contact.html    img          projects.html   site1.zip
 blog.html          css             index.html   scss           'Sparsh Architecture - Doc'
 blog-single.html   elements.html   js           service.html    vendor
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# rm site1.zip 
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# ls
 about.html         contact.html    img          projects.html  'Sparsh Architecture - Doc'
 blog.html          css             index.html   scss            vendor
 blog-single.html   elements.html   js           service.html
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# tar -czf site.tar ./
tar: .: file changed as we read it
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# ls
 about.html         contact.html    img          projects.html   site.tar
 blog.html          css             index.html   scss           'Sparsh Architecture - Doc'
 blog-single.html   elements.html   js           service.html    vendor
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# cp site.tar ../          
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache/site]
└─# cd ..          
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# ls
site  site.tar
                                                                                                          
──(root㉿kali)-[/images/debian-apache]
└─# rm -Rf site 
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# ls
site.tar
                                                                                                          
┌──(root㉿kali)-[/home/kali]
└─# cd /images    
                                                                                                          
┌──(root㉿kali)-[/images]
└─# ls
debian-apache  ubuntu-python
                                                                                                          
┌──(root㉿kali)-[/images]
└─# cd debian-apache 
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# ls
dockerfile  site.tar
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# cat dockerfile  

# Versao do sistema que sera executado no container.
FROM debian

# Comandos para atualiza e instalacao do apache no container.
RUN apt-get update && apt-get install -y apache2 && apt-get clean

# Evita que mais de uma versao do apache esteja em execucao no mesmo container.
ENV APACHE_LOCK_DIR="var/lock"

# Arquivo de processo onde ele sera executado.
ENV APACHE_PID_FILE="var/run/apache2.pid"

# Usuario que vai executar o apache
ENV APACHE_RUN_USER="www-data"

# Grupo do usuario do apache
ENV APACHE_RUN_GROUP="www-data"

# Pasta onde sera armazenado LOGS
ENV APACHE_LOG_DIR="/var/log/apache2"


# O "ADD" diferedo do "CP" permite copia os arquivos para o local especificado e vai descompactar esse arquivo.

ADD site.tar /var/www/html

# Apenas uma descricao
LABEL description = "Apache webserver 1.0"

# E o local dentro do container.
VOLUME /var/www/html

# Porta da Minha Aplicacao
EXPOSE 80

# EXECUCAO EM SEGUNDO PLANO
EXTRYPOINT ["/usr/sbin/apachectl"]

CMD ["-d","FOREGROUNF"]
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# docker image build -t debian-apache:1.0 .
Sending build context to Docker daemon  17.53MB
Step 1/13 : FROM debian
 ---> c31f65dd4cc9
Step 2/13 : RUN apt-get update && apt-get install -y apache2 && apt-get clean
 ---> Running in 6498dc851334
Get:1 http://deb.debian.org/debian bullseye InRelease [116 kB]
Get:2 http://deb.debian.org/debian-security bullseye-security InRelease [48.4 kB]
Get:3 http://deb.debian.org/debian bullseye-updates InRelease [44.1 kB]
Get:4 http://deb.debian.org/debian bullseye/main amd64 Packages [8184 kB]
Get:5 http://deb.debian.org/debian-security bullseye-security/main amd64 Packages [207 kB]
Get:6 http://deb.debian.org/debian bullseye-updates/main amd64 Packages [14.6 kB]
Fetched 8614 kB in 18s (468 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  apache2-bin apache2-data apache2-utils bzip2 ca-certificates file libapr1
  libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libbrotli1 libcurl4
  libexpat1 libgdbm-compat4 libgdbm6 libgpm2 libicu67 libjansson4
  libldap-2.4-2 libldap-common liblua5.3-0 libmagic-mgc libmagic1 libncurses6
  libncursesw6 libnghttp2-14 libperl5.32 libprocps8 libpsl5 librtmp1
  libsasl2-2 libsasl2-modules libsasl2-modules-db libsqlite3-0 libssh2-1
  libxml2 mailcap media-types mime-support netbase openssl perl
  perl-modules-5.32 procps psmisc publicsuffix ssl-cert xz-utils
Suggested packages:
  apache2-doc apache2-suexec-pristine | apache2-suexec-custom www-browser
  bzip2-doc gdbm-l10n gpm sensible-utils libsasl2-modules-gssapi-mit
  | libsasl2-modules-gssapi-heimdal libsasl2-modules-ldap libsasl2-modules-otp
  libsasl2-modules-sql perl-doc libterm-readline-gnu-perl
  | libterm-readline-perl-perl make libtap-harness-archive-perl
The following NEW packages will be installed:
  apache2 apache2-bin apache2-data apache2-utils bzip2 ca-certificates file
  libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libbrotli1
  libcurl4 libexpat1 libgdbm-compat4 libgdbm6 libgpm2 libicu67 libjansson4
  libldap-2.4-2 libldap-common liblua5.3-0 libmagic-mgc libmagic1 libncurses6
  libncursesw6 libnghttp2-14 libperl5.32 libprocps8 libpsl5 librtmp1
  libsasl2-2 libsasl2-modules libsasl2-modules-db libsqlite3-0 libssh2-1
  libxml2 mailcap media-types mime-support netbase openssl perl
  perl-modules-5.32 procps psmisc publicsuffix ssl-cert xz-utils
0 upgraded, 49 newly installed, 0 to remove and 4 not upgraded.
Need to get 24.7 MB of archives.
After this operation, 111 MB of additional disk space will be used.
Get:1 http://deb.debian.org/debian bullseye/main amd64 perl-modules-5.32 all 5.32.1-4+deb11u2 [2823 kB]
Get:2 http://deb.debian.org/debian bullseye/main amd64 libgdbm6 amd64 1.19-2 [64.9 kB]
Get:3 http://deb.debian.org/debian bullseye/main amd64 libgdbm-compat4 amd64 1.19-2 [44.7 kB]
Get:4 http://deb.debian.org/debian bullseye/main amd64 libperl5.32 amd64 5.32.1-4+deb11u2 [4106 kB]
Get:5 http://deb.debian.org/debian bullseye/main amd64 perl amd64 5.32.1-4+deb11u2 [293 kB]
Get:6 http://deb.debian.org/debian bullseye/main amd64 libapr1 amd64 1.7.0-6+deb11u1 [106 kB]
Get:7 http://deb.debian.org/debian-security bullseye-security/main amd64 libexpat1 amd64 2.2.10-2+deb11u5 [98.2 kB]
Get:8 http://deb.debian.org/debian bullseye/main amd64 libaprutil1 amd64 1.6.1-5 [92.1 kB]
Get:9 http://deb.debian.org/debian bullseye/main amd64 libsqlite3-0 amd64 3.34.1-3 [797 kB]
Get:10 http://deb.debian.org/debian bullseye/main amd64 libaprutil1-dbd-sqlite3 amd64 1.6.1-5 [18.8 kB]
Get:11 http://deb.debian.org/debian bullseye/main amd64 libsasl2-modules-db amd64 2.1.27+dfsg-2.1+deb11u1 [69.1 kB]
Get:12 http://deb.debian.org/debian bullseye/main amd64 libsasl2-2 amd64 2.1.27+dfsg-2.1+deb11u1 [106 kB]
Get:13 http://deb.debian.org/debian bullseye/main amd64 libldap-2.4-2 amd64 2.4.57+dfsg-3+deb11u1 [232 kB]
Get:14 http://deb.debian.org/debian bullseye/main amd64 libaprutil1-ldap amd64 1.6.1-5 [17.0 kB]
Get:15 http://deb.debian.org/debian bullseye/main amd64 libbrotli1 amd64 1.0.9-2+b2 [279 kB]
Get:16 http://deb.debian.org/debian bullseye/main amd64 libnghttp2-14 amd64 1.43.0-1 [77.1 kB]
Get:17 http://deb.debian.org/debian bullseye/main amd64 libpsl5 amd64 0.21.0-1.2 [57.3 kB]
Get:18 http://deb.debian.org/debian bullseye/main amd64 librtmp1 amd64 2.4+20151223.gitfa8646d.1-2+b2 [60.8 kB]
Get:19 http://deb.debian.org/debian bullseye/main amd64 libssh2-1 amd64 1.9.0-2 [156 kB]
Get:20 http://deb.debian.org/debian bullseye/main amd64 libcurl4 amd64 7.74.0-1.3+deb11u3 [345 kB]
Get:21 http://deb.debian.org/debian bullseye/main amd64 libjansson4 amd64 2.13.1-1.1 [39.7 kB]
Get:22 http://deb.debian.org/debian bullseye/main amd64 liblua5.3-0 amd64 5.3.3-1.1+b1 [120 kB]
Get:23 http://deb.debian.org/debian bullseye/main amd64 libicu67 amd64 67.1-7 [8622 kB]
Get:24 http://deb.debian.org/debian-security bullseye-security/main amd64 libxml2 amd64 2.9.10+dfsg-6.7+deb11u3 [693 kB]
Get:25 http://deb.debian.org/debian bullseye/main amd64 apache2-bin amd64 2.4.54-1~deb11u1 [1425 kB]
Get:26 http://deb.debian.org/debian bullseye/main amd64 apache2-data all 2.4.54-1~deb11u1 [160 kB]
Get:27 http://deb.debian.org/debian bullseye/main amd64 apache2-utils amd64 2.4.54-1~deb11u1 [260 kB]
Get:28 http://deb.debian.org/debian bullseye/main amd64 media-types all 4.0.0 [30.3 kB]
Get:29 http://deb.debian.org/debian bullseye/main amd64 mailcap all 3.69 [31.7 kB]
Get:30 http://deb.debian.org/debian bullseye/main amd64 mime-support all 3.66 [10.9 kB]
Get:31 http://deb.debian.org/debian bullseye/main amd64 libncurses6 amd64 6.2+20201114-2 [102 kB]
Get:32 http://deb.debian.org/debian bullseye/main amd64 libncursesw6 amd64 6.2+20201114-2 [132 kB]
Get:33 http://deb.debian.org/debian bullseye/main amd64 libprocps8 amd64 2:3.3.17-5 [63.9 kB]
Get:34 http://deb.debian.org/debian bullseye/main amd64 procps amd64 2:3.3.17-5 [502 kB]
Get:35 http://deb.debian.org/debian bullseye/main amd64 apache2 amd64 2.4.54-1~deb11u1 [275 kB]
Get:36 http://deb.debian.org/debian bullseye/main amd64 netbase all 6.3 [19.9 kB]
Get:37 http://deb.debian.org/debian bullseye/main amd64 bzip2 amd64 1.0.8-4 [49.3 kB]
Get:38 http://deb.debian.org/debian bullseye/main amd64 openssl amd64 1.1.1n-0+deb11u3 [853 kB]
Get:39 http://deb.debian.org/debian bullseye/main amd64 ca-certificates all 20210119 [158 kB]
Get:40 http://deb.debian.org/debian bullseye/main amd64 libmagic-mgc amd64 1:5.39-3 [273 kB]
Get:41 http://deb.debian.org/debian bullseye/main amd64 libmagic1 amd64 1:5.39-3 [126 kB]
Get:42 http://deb.debian.org/debian bullseye/main amd64 file amd64 1:5.39-3 [69.1 kB]
Get:43 http://deb.debian.org/debian bullseye/main amd64 xz-utils amd64 5.2.5-2.1~deb11u1 [220 kB]
Get:44 http://deb.debian.org/debian bullseye/main amd64 libgpm2 amd64 1.20.7-8 [35.6 kB]
Get:45 http://deb.debian.org/debian bullseye/main amd64 libldap-common all 2.4.57+dfsg-3+deb11u1 [95.8 kB]
Get:46 http://deb.debian.org/debian bullseye/main amd64 libsasl2-modules amd64 2.1.27+dfsg-2.1+deb11u1 [104 kB]
Get:47 http://deb.debian.org/debian bullseye/main amd64 psmisc amd64 23.4-2 [198 kB]
Get:48 http://deb.debian.org/debian bullseye/main amd64 publicsuffix all 20220811.1734-0+deb11u1 [127 kB]
Get:49 http://deb.debian.org/debian bullseye/main amd64 ssl-cert all 1.1.0+nmu1 [21.0 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 24.7 MB in 41s (604 kB/s)                                                                         
Selecting previously unselected package perl-modules-5.32.
(Reading database ... 6661 files and directories currently installed.)
Preparing to unpack .../00-perl-modules-5.32_5.32.1-4+deb11u2_all.deb ...
Unpacking perl-modules-5.32 (5.32.1-4+deb11u2) ...
Selecting previously unselected package libgdbm6:amd64.
Preparing to unpack .../01-libgdbm6_1.19-2_amd64.deb ...
Unpacking libgdbm6:amd64 (1.19-2) ...
Selecting previously unselected package libgdbm-compat4:amd64.
Preparing to unpack .../02-libgdbm-compat4_1.19-2_amd64.deb ...
Unpacking libgdbm-compat4:amd64 (1.19-2) ...
Selecting previously unselected package libperl5.32:amd64.
Preparing to unpack .../03-libperl5.32_5.32.1-4+deb11u2_amd64.deb ...
Unpacking libperl5.32:amd64 (5.32.1-4+deb11u2) ...
Selecting previously unselected package perl.
Preparing to unpack .../04-perl_5.32.1-4+deb11u2_amd64.deb ...
Unpacking perl (5.32.1-4+deb11u2) ...
Selecting previously unselected package libapr1:amd64.
Preparing to unpack .../05-libapr1_1.7.0-6+deb11u1_amd64.deb ...
Unpacking libapr1:amd64 (1.7.0-6+deb11u1) ...
Selecting previously unselected package libexpat1:amd64.
Preparing to unpack .../06-libexpat1_2.2.10-2+deb11u5_amd64.deb ...
Unpacking libexpat1:amd64 (2.2.10-2+deb11u5) ...
Selecting previously unselected package libaprutil1:amd64.
Preparing to unpack .../07-libaprutil1_1.6.1-5_amd64.deb ...
Unpacking libaprutil1:amd64 (1.6.1-5) ...
Selecting previously unselected package libsqlite3-0:amd64.
Preparing to unpack .../08-libsqlite3-0_3.34.1-3_amd64.deb ...
Unpacking libsqlite3-0:amd64 (3.34.1-3) ...
Selecting previously unselected package libaprutil1-dbd-sqlite3:amd64.
Preparing to unpack .../09-libaprutil1-dbd-sqlite3_1.6.1-5_amd64.deb ...
Unpacking libaprutil1-dbd-sqlite3:amd64 (1.6.1-5) ...
Selecting previously unselected package libsasl2-modules-db:amd64.
Preparing to unpack .../10-libsasl2-modules-db_2.1.27+dfsg-2.1+deb11u1_amd64.deb ...
Unpacking libsasl2-modules-db:amd64 (2.1.27+dfsg-2.1+deb11u1) ...
Selecting previously unselected package libsasl2-2:amd64.
Preparing to unpack .../11-libsasl2-2_2.1.27+dfsg-2.1+deb11u1_amd64.deb ...
Unpacking libsasl2-2:amd64 (2.1.27+dfsg-2.1+deb11u1) ...
Selecting previously unselected package libldap-2.4-2:amd64.
Preparing to unpack .../12-libldap-2.4-2_2.4.57+dfsg-3+deb11u1_amd64.deb ...
Unpacking libldap-2.4-2:amd64 (2.4.57+dfsg-3+deb11u1) ...
Selecting previously unselected package libaprutil1-ldap:amd64.
Preparing to unpack .../13-libaprutil1-ldap_1.6.1-5_amd64.deb ...
Unpacking libaprutil1-ldap:amd64 (1.6.1-5) ...
Selecting previously unselected package libbrotli1:amd64.
Preparing to unpack .../14-libbrotli1_1.0.9-2+b2_amd64.deb ...
Unpacking libbrotli1:amd64 (1.0.9-2+b2) ...
Selecting previously unselected package libnghttp2-14:amd64.
Preparing to unpack .../15-libnghttp2-14_1.43.0-1_amd64.deb ...
Unpacking libnghttp2-14:amd64 (1.43.0-1) ...
Selecting previously unselected package libpsl5:amd64.
Preparing to unpack .../16-libpsl5_0.21.0-1.2_amd64.deb ...
Unpacking libpsl5:amd64 (0.21.0-1.2) ...
Selecting previously unselected package librtmp1:amd64.
Preparing to unpack .../17-librtmp1_2.4+20151223.gitfa8646d.1-2+b2_amd64.deb ...
Unpacking librtmp1:amd64 (2.4+20151223.gitfa8646d.1-2+b2) ...
Selecting previously unselected package libssh2-1:amd64.
Preparing to unpack .../18-libssh2-1_1.9.0-2_amd64.deb ...
Unpacking libssh2-1:amd64 (1.9.0-2) ...
Selecting previously unselected package libcurl4:amd64.
Preparing to unpack .../19-libcurl4_7.74.0-1.3+deb11u3_amd64.deb ...
Unpacking libcurl4:amd64 (7.74.0-1.3+deb11u3) ...
Selecting previously unselected package libjansson4:amd64.
Preparing to unpack .../20-libjansson4_2.13.1-1.1_amd64.deb ...
Unpacking libjansson4:amd64 (2.13.1-1.1) ...
Selecting previously unselected package liblua5.3-0:amd64.
Preparing to unpack .../21-liblua5.3-0_5.3.3-1.1+b1_amd64.deb ...
Unpacking liblua5.3-0:amd64 (5.3.3-1.1+b1) ...
Selecting previously unselected package libicu67:amd64.
Preparing to unpack .../22-libicu67_67.1-7_amd64.deb ...
Unpacking libicu67:amd64 (67.1-7) ...
Selecting previously unselected package libxml2:amd64.
Preparing to unpack .../23-libxml2_2.9.10+dfsg-6.7+deb11u3_amd64.deb ...
Unpacking libxml2:amd64 (2.9.10+dfsg-6.7+deb11u3) ...
Selecting previously unselected package apache2-bin.
Preparing to unpack .../24-apache2-bin_2.4.54-1~deb11u1_amd64.deb ...
Unpacking apache2-bin (2.4.54-1~deb11u1) ...
Selecting previously unselected package apache2-data.
Preparing to unpack .../25-apache2-data_2.4.54-1~deb11u1_all.deb ...
Unpacking apache2-data (2.4.54-1~deb11u1) ...
Selecting previously unselected package apache2-utils.
Preparing to unpack .../26-apache2-utils_2.4.54-1~deb11u1_amd64.deb ...
Unpacking apache2-utils (2.4.54-1~deb11u1) ...
Selecting previously unselected package media-types.
Preparing to unpack .../27-media-types_4.0.0_all.deb ...
Unpacking media-types (4.0.0) ...
Selecting previously unselected package mailcap.
Preparing to unpack .../28-mailcap_3.69_all.deb ...
Unpacking mailcap (3.69) ...
Selecting previously unselected package mime-support.
Preparing to unpack .../29-mime-support_3.66_all.deb ...
Unpacking mime-support (3.66) ...
Selecting previously unselected package libncurses6:amd64.
Preparing to unpack .../30-libncurses6_6.2+20201114-2_amd64.deb ...
Unpacking libncurses6:amd64 (6.2+20201114-2) ...
Selecting previously unselected package libncursesw6:amd64.
Preparing to unpack .../31-libncursesw6_6.2+20201114-2_amd64.deb ...
Unpacking libncursesw6:amd64 (6.2+20201114-2) ...
Selecting previously unselected package libprocps8:amd64.
Preparing to unpack .../32-libprocps8_2%3a3.3.17-5_amd64.deb ...
Unpacking libprocps8:amd64 (2:3.3.17-5) ...
Selecting previously unselected package procps.
Preparing to unpack .../33-procps_2%3a3.3.17-5_amd64.deb ...
Unpacking procps (2:3.3.17-5) ...
Selecting previously unselected package apache2.
Preparing to unpack .../34-apache2_2.4.54-1~deb11u1_amd64.deb ...
Unpacking apache2 (2.4.54-1~deb11u1) ...
Selecting previously unselected package netbase.
Preparing to unpack .../35-netbase_6.3_all.deb ...
Unpacking netbase (6.3) ...
Selecting previously unselected package bzip2.
Preparing to unpack .../36-bzip2_1.0.8-4_amd64.deb ...
Unpacking bzip2 (1.0.8-4) ...
Selecting previously unselected package openssl.
Preparing to unpack .../37-openssl_1.1.1n-0+deb11u3_amd64.deb ...
Unpacking openssl (1.1.1n-0+deb11u3) ...
Selecting previously unselected package ca-certificates.
Preparing to unpack .../38-ca-certificates_20210119_all.deb ...
Unpacking ca-certificates (20210119) ...
Selecting previously unselected package libmagic-mgc.
Preparing to unpack .../39-libmagic-mgc_1%3a5.39-3_amd64.deb ...
Unpacking libmagic-mgc (1:5.39-3) ...
Selecting previously unselected package libmagic1:amd64.
Preparing to unpack .../40-libmagic1_1%3a5.39-3_amd64.deb ...
Unpacking libmagic1:amd64 (1:5.39-3) ...
Selecting previously unselected package file.
Preparing to unpack .../41-file_1%3a5.39-3_amd64.deb ...
Unpacking file (1:5.39-3) ...
Selecting previously unselected package xz-utils.
Preparing to unpack .../42-xz-utils_5.2.5-2.1~deb11u1_amd64.deb ...
Unpacking xz-utils (5.2.5-2.1~deb11u1) ...
Selecting previously unselected package libgpm2:amd64.
Preparing to unpack .../43-libgpm2_1.20.7-8_amd64.deb ...
Unpacking libgpm2:amd64 (1.20.7-8) ...
Selecting previously unselected package libldap-common.
Preparing to unpack .../44-libldap-common_2.4.57+dfsg-3+deb11u1_all.deb ...
Unpacking libldap-common (2.4.57+dfsg-3+deb11u1) ...
Selecting previously unselected package libsasl2-modules:amd64.
Preparing to unpack .../45-libsasl2-modules_2.1.27+dfsg-2.1+deb11u1_amd64.deb ...
Unpacking libsasl2-modules:amd64 (2.1.27+dfsg-2.1+deb11u1) ...
Selecting previously unselected package psmisc.
Preparing to unpack .../46-psmisc_23.4-2_amd64.deb ...
Unpacking psmisc (23.4-2) ...
Selecting previously unselected package publicsuffix.
Preparing to unpack .../47-publicsuffix_20220811.1734-0+deb11u1_all.deb ...
Unpacking publicsuffix (20220811.1734-0+deb11u1) ...
Selecting previously unselected package ssl-cert.
Preparing to unpack .../48-ssl-cert_1.1.0+nmu1_all.deb ...
Unpacking ssl-cert (1.1.0+nmu1) ...
Setting up libexpat1:amd64 (2.2.10-2+deb11u5) ...
Setting up media-types (4.0.0) ...
Setting up libpsl5:amd64 (0.21.0-1.2) ...
Setting up libgpm2:amd64 (1.20.7-8) ...
Setting up libicu67:amd64 (67.1-7) ...
Setting up libmagic-mgc (1:5.39-3) ...
Setting up psmisc (23.4-2) ...
Setting up perl-modules-5.32 (5.32.1-4+deb11u2) ...
Setting up libbrotli1:amd64 (1.0.9-2+b2) ...
Setting up libsqlite3-0:amd64 (3.34.1-3) ...
Setting up libsasl2-modules:amd64 (2.1.27+dfsg-2.1+deb11u1) ...
Setting up libnghttp2-14:amd64 (1.43.0-1) ...
Setting up libmagic1:amd64 (1:5.39-3) ...
Setting up libapr1:amd64 (1.7.0-6+deb11u1) ...
Setting up file (1:5.39-3) ...
Setting up bzip2 (1.0.8-4) ...
Setting up libldap-common (2.4.57+dfsg-3+deb11u1) ...
Setting up libjansson4:amd64 (2.13.1-1.1) ...
Setting up libsasl2-modules-db:amd64 (2.1.27+dfsg-2.1+deb11u1) ...
Setting up librtmp1:amd64 (2.4+20151223.gitfa8646d.1-2+b2) ...
Setting up libncurses6:amd64 (6.2+20201114-2) ...
Setting up xz-utils (5.2.5-2.1~deb11u1) ...
update-alternatives: using /usr/bin/xz to provide /usr/bin/lzma (lzma) in auto mode
Setting up libncursesw6:amd64 (6.2+20201114-2) ...
Setting up libsasl2-2:amd64 (2.1.27+dfsg-2.1+deb11u1) ...
Setting up liblua5.3-0:amd64 (5.3.3-1.1+b1) ...
Setting up libssh2-1:amd64 (1.9.0-2) ...
Setting up netbase (6.3) ...
Setting up apache2-data (2.4.54-1~deb11u1) ...
Setting up openssl (1.1.1n-0+deb11u3) ...
Setting up publicsuffix (20220811.1734-0+deb11u1) ...
Setting up libxml2:amd64 (2.9.10+dfsg-6.7+deb11u3) ...
Setting up libprocps8:amd64 (2:3.3.17-5) ...
Setting up libgdbm6:amd64 (1.19-2) ...
Setting up libaprutil1:amd64 (1.6.1-5) ...
Setting up libldap-2.4-2:amd64 (2.4.57+dfsg-3+deb11u1) ...
Setting up libaprutil1-ldap:amd64 (1.6.1-5) ...
Setting up libaprutil1-dbd-sqlite3:amd64 (1.6.1-5) ...
Setting up ca-certificates (20210119) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
Updating certificates in /etc/ssl/certs...
129 added, 0 removed; done.
Setting up ssl-cert (1.1.0+nmu1) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
Setting up libgdbm-compat4:amd64 (1.19-2) ...
Setting up libperl5.32:amd64 (5.32.1-4+deb11u2) ...
Setting up procps (2:3.3.17-5) ...
Setting up libcurl4:amd64 (7.74.0-1.3+deb11u3) ...
Setting up apache2-utils (2.4.54-1~deb11u1) ...
Setting up perl (5.32.1-4+deb11u2) ...
Setting up mailcap (3.69) ...
Setting up mime-support (3.66) ...
Setting up apache2-bin (2.4.54-1~deb11u1) ...
Setting up apache2 (2.4.54-1~deb11u1) ...
Enabling module mpm_event.
Enabling module authz_core.
Enabling module authz_host.
Enabling module authn_core.
Enabling module auth_basic.
Enabling module access_compat.
Enabling module authn_file.
Enabling module authz_user.
Enabling module alias.
Enabling module dir.
Enabling module autoindex.
Enabling module env.
Enabling module mime.
Enabling module negotiation.
Enabling module setenvif.
Enabling module filter.
Enabling module deflate.
Enabling module status.
Enabling module reqtimeout.
Enabling conf charset.
Enabling conf localized-error-pages.
Enabling conf other-vhosts-access-log.
Enabling conf security.
Enabling conf serve-cgi-bin.
Enabling site 000-default.
invoke-rc.d: could not determine current runlevel
invoke-rc.d: policy-rc.d denied execution of start.
Processing triggers for libc-bin (2.31-13+deb11u5) ...
Processing triggers for ca-certificates (20210119) ...
Updating certificates in /etc/ssl/certs...
0 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
Removing intermediate container 6498dc851334
 ---> 26d21afbc42c
Step 3/13 : ENV APACHE_LOCK_DIR="var/lock"
 ---> Running in a9182d868f38
Removing intermediate container a9182d868f38
 ---> 6b141e1f9ff4
Step 4/13 : ENV APACHE_PID_FILE="var/run/apache2.pid"
 ---> Running in 5f8fe0b19996
Removing intermediate container 5f8fe0b19996
 ---> ca8b05fed249
Step 5/13 : ENV APACHE_RUN_USER="www-data"
 ---> Running in f56430d84108
Removing intermediate container f56430d84108
 ---> 00f66ede6ea8
Step 6/13 : ENV APACHE_RUN_GROUP="www-data"
 ---> Running in 5771012acef9
Removing intermediate container 5771012acef9
 ---> 2fcf5e53fe91
Step 7/13 : ENV APACHE_LOG_DIR="/var/log/apache2"
 ---> Running in 997fc1214be1
Removing intermediate container 997fc1214be1
 ---> 77d924c10a91
Step 8/13 : ADD site.tar /var/www/html
 ---> 4a491c51f90a
Step 9/13 : LABEL description = "Apache webserver 1.0"
 ---> Running in ee6df5aaa84e
Removing intermediate container ee6df5aaa84e
 ---> 9eb23b214b6b
Step 10/13 : VOLUME /var/www/html
 ---> Running in f0e60f21c242
Removing intermediate container f0e60f21c242
 ---> 05595ad80047
Step 11/13 : EXPOSE 80
 ---> Running in abfab23e3692
Removing intermediate container abfab23e3692
 ---> 6f3526b4f01e
Step 12/13 : ENTRYPOINT ["/usr/sbin/apachectl"]
 ---> Running in 21de3e1db728
Removing intermediate container 21de3e1db728
 ---> 6fe21dea10a0
Step 13/13 : CMD ["-D", "FOREGROUND"]
 ---> Running in 0d6b44f8f4cc
Removing intermediate container 0d6b44f8f4cc
 ---> 6005e6fee061
Successfully built 6005e6fee061
Successfully tagged debian-apache:1.0
                                                                                                          
──(root㉿kali)-[/images/debian-apache]
└─# docker images                             
REPOSITORY      TAG          IMAGE ID       CREATED              SIZE
debian-apache   1.0          6005e6fee061   About a minute ago   276MB
ubuntu-python   latest       ae7845f0f8d0   43 minutes ago       148MB
<none>          <none>       d395bdf57b29   48 minutes ago       148MB
mysql           latest       3842e9cdffd2   10 days ago          538MB
httpd           latest       8653efc8c72d   10 days ago          145MB
php             7.4-apache   20a3732f422b   11 days ago          453MB
debian          10           1036dd279580   11 days ago          114MB
debian          latest       c31f65dd4cc9   11 days ago          124MB
ubuntu          latest       a8780b506fa4   3 weeks ago          77.8MB
debian          9            662c05203bab   5 months ago         101MB
centos          latest       5d0da3dc9764   14 months ago        231MB
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# docker run -dti -p 80:80 --name meu-apache debian-apache:1.0
28e812dcb0814a1d8b2c93af578c127ef9b4db01bf93dbb95faa5569606eb297
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
└─# docker ps                                                   
CONTAINER ID   IMAGE               COMMAND                  CREATED         STATUS         PORTS                               NAMES
28e812dcb081   debian-apache:1.0   "/usr/sbin/apachectl…"   6 seconds ago   Up 5 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   meu-apache
                                                                                                          
┌──(root㉿kali)-[/images/debian-apache]
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
       valid_lft 3832sec preferred_lft 3832sec
    inet6 fe80::a00:27ff:fedb:966a/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 

```

Outra formar de usar uma diferente linguagem de programacao.
Exemplo vamos baixar uma imagem oficial do python.

```
┌──(root㉿kali)-[/images]
└─# docker images
REPOSITORY      TAG          IMAGE ID       CREATED             SIZE
debian-apache   1.0          6005e6fee061   About an hour ago   276MB
ubuntu-python   latest       ae7845f0f8d0   2 hours ago         148MB
<none>          <none>       d395bdf57b29   2 hours ago         148MB
mysql           latest       3842e9cdffd2   10 days ago         538MB
httpd           latest       8653efc8c72d   11 days ago         145MB
php             7.4-apache   20a3732f422b   11 days ago         453MB
debian          10           1036dd279580   11 days ago         114MB
debian          latest       c31f65dd4cc9   11 days ago         124MB
ubuntu          latest       a8780b506fa4   3 weeks ago         77.8MB
debian          9            662c05203bab   5 months ago        101MB
centos          latest       5d0da3dc9764   14 months ago       231MB
                                                                                                          
┌──(root㉿kali)-[/images]
└─# docker pull python
Using default tag: latest
latest: Pulling from library/python
a8ca11554fce: Already exists 
e4e46864aba2: Pull complete 
c85a0be79bfb: Pull complete 
195ea6a58ca8: Pull complete 
157f16ed0a0c: Pull complete 
884b144bec28: Pull complete 
1c469643b609: Pull complete 
4c0ac982aa89: Pull complete 
049db2c7eb8a: Pull complete 
Digest: sha256:10fc14aa6ae69f69e4c953cffd9b0964843d8c163950491d2138af891377bc1d
Status: Downloaded newer image for python:latest
docker.io/library/python:latest
                                                                                                          
──(root㉿kali)-[/images]
└─# cd python 
                                                                                                          
┌──(root㉿kali)-[/images/python]
└─# ls
app.py
                                                                                                          
┌──(root㉿kali)-[/images/python]
└─# nano dockerfile
                                                                                                          
┌──(root㉿kali)-[/images/python]
└─# cat dockerfile 
FROM python

WORKDIR /usr/src/app

COPY app.py /usr/src/app

CMD ["python", "./app.py"]

──(root㉿kali)-[/images/python]
└─# docker image build -t app-python:1.0 .       
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM python
 ---> ee4e7a0f1c35
Step 2/4 : WORKDIR /usr/src/app
 ---> Running in cc05a9d3981d
Removing intermediate container cc05a9d3981d
 ---> 942c7e125c79
Step 3/4 : COPY app.py /usr/src/app
 ---> 36147e38479d
Step 4/4 : CMD ["python", "./app.py"]
 ---> Running in c1717e4935d9
Removing intermediate container c1717e4935d9
 ---> 377b72ffbce6
Successfully built 377b72ffbce6
Successfully tagged app-python:1.0
                                                                                                          
──(root㉿kali)-[/images/python]
└─# docker images                         
REPOSITORY      TAG          IMAGE ID       CREATED              SIZE
app-python      1.0          377b72ffbce6   About a minute ago   932MB
debian-apache   1.0          6005e6fee061   About an hour ago    276MB
ubuntu-python   latest       ae7845f0f8d0   2 hours ago          148MB
<none>          <none>       d395bdf57b29   2 hours ago          148MB
mysql           latest       3842e9cdffd2   10 days ago          538MB
python          latest       ee4e7a0f1c35   10 days ago          932MB
httpd           latest       8653efc8c72d   11 days ago          145MB
php             7.4-apache   20a3732f422b   11 days ago          453MB
debian          10           1036dd279580   11 days ago          114MB
debian          latest       c31f65dd4cc9   11 days ago          124MB
ubuntu          latest       a8780b506fa4   3 weeks ago          77.8MB
debian          9            662c05203bab   5 months ago         101MB
centos          latest       5d0da3dc9764   14 months ago        231MB
                                                                                                          
──(root㉿kali)-[/images/python]
└─# docker run -ti --name runapp1 app-python:1.0                
Qual o seu nome? Adriano
Adriano
                                                                                                          
┌──(root㉿kali)-[/images/python]
└─# 

```

### Gerando uma imagem MULTISTAGE

Criar um binario de uma aplicacao com codigo.
E publicar esse codigo em um container Linux.

Usaremos uma linguagem "go".

A maior dificuldade é manter essa imagem o menor possivel para publicar e replicar ela de forma mais rapida em container.

Criaremos uma imagem com varios estagios com instrucoes "FROM" diferentes para usar bases diferentes e passar entre os estagios.

Vamos baixar o a imagem do "go" oficial.
https://hub.docker.com/_/golang


Vamos baixar uma versão especifica do Linux com no minima com até 5mb
https://hub.docker.com/_/alpine



```
dir go        
                                                                                                          
┌──(root㉿kali)-[/images]
└─# cd go 
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# ls
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# docker pull golang                          
Using default tag: latest
latest: Pulling from library/golang
a8ca11554fce: Already exists 
e4e46864aba2: Already exists 
c85a0be79bfb: Already exists 
195ea6a58ca8: Already exists 
52908dc1c386: Pull complete 
a2b47720d601: Pull complete 
14a70245b07c: Pull complete 
Digest: sha256:dc76ef03e54c34a00dcdca81e55c242d24b34d231637776c4bb5c1a8e8514253
Status: Downloaded newer image for golang:latest
docker.io/library/golang:latest

┌──(root㉿kali)-[/images/go]
└─# docker pull alpine
Using default tag: latest
latest: Pulling from library/alpine
c158987b0551: Pull complete 
Digest: sha256:8914eb54f968791faf6a8638949e480fef81e697984fba772b3976835194c6d4
Status: Downloaded newer image for alpine:latest
docker.io/library/alpine:latest
                                                                                                          
──(root㉿kali)-[/images/go]
└─# docker images     
REPOSITORY      TAG          IMAGE ID       CREATED          SIZE
app-python      1.0          377b72ffbce6   15 minutes ago   932MB
debian-apache   1.0          6005e6fee061   2 hours ago      276MB
ubuntu-python   latest       ae7845f0f8d0   2 hours ago      148MB
<none>          <none>       d395bdf57b29   2 hours ago      148MB
alpine          latest       49176f190c7e   3 days ago       7.05MB
mysql           latest       3842e9cdffd2   10 days ago      538MB
python          latest       ee4e7a0f1c35   10 days ago      932MB
golang          latest       8ee516e10ce0   10 days ago      992MB
httpd           latest       8653efc8c72d   11 days ago      145MB
php             7.4-apache   20a3732f422b   11 days ago      453MB
debian          10           1036dd279580   11 days ago      114MB
debian          latest       c31f65dd4cc9   11 days ago      124MB
ubuntu          latest       a8780b506fa4   3 weeks ago      77.8MB
debian          9            662c05203bab   5 months ago     101MB
centos          latest       5d0da3dc9764   14 months ago    231MB
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# 

┌──(root㉿kali)-[/home/kali]
└─# cd /images 
                                                                                                          
┌──(root㉿kali)-[/images]
└─# cd go     
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# ls
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# nano app.go
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# nano dockerfile
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# docker image build -t app-go:1.0 .    
Sending build context to Docker daemon  3.072kB
Step 1/9 : FROM golang as exec
 ---> 8ee516e10ce0
Step 2/9 : COPY app.go /go/src/app/
 ---> fe2fa2e462f7
Step 3/9 : WORKDIR /go/src/app
 ---> Running in 81ece781eea1
Removing intermediate container 81ece781eea1
 ---> 26c215be04f6
Step 4/9 : RUN go build -o app.go .
 ---> Running in a29783c60886
go: go.mod file not found in current directory or any parent directory; see 'go help modules'
The command '/bin/sh -c go build -o app.go .' returned a non-zero code: 1
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# nano dockerfile                   
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# docker image build -t app-go:1.0 .
Sending build context to Docker daemon  3.072kB
Step 1/10 : FROM golang as exec
 ---> 8ee516e10ce0
Step 2/10 : COPY app.go /go/src/app/
 ---> Using cache
 ---> fe2fa2e462f7
Step 3/10 : ENV GO111MODULE=auto
 ---> Running in eedd86c4448c
Removing intermediate container eedd86c4448c
 ---> af0bcd993e21
Step 4/10 : WORKDIR /go/src/app
 ---> Running in 509aca1694f9
Removing intermediate container 509aca1694f9
 ---> 04bfc886db74
Step 5/10 : RUN go build -o app.go .
 ---> Running in 6488a61ee396
Removing intermediate container 6488a61ee396
 ---> 444e1f1ee246
Step 6/10 : FROM alpine
 ---> 49176f190c7e
Step 7/10 : WORKDIR /appexec
 ---> Running in b1f97d5ae0c4
Removing intermediate container b1f97d5ae0c4
 ---> 8b21677c2168
Step 8/10 : COPY --from=exec /go/src/app/ /appexec
 ---> fd69fcba1e4b
Step 9/10 : RUN chmod -R 755 /appexec
 ---> Running in b0f044450c4a
Removing intermediate container b0f044450c4a
 ---> fe8778faa4a0
Step 10/10 : ENTRYPOINT ./app.go
 ---> Running in 8baa1b0c7838
Removing intermediate container 8baa1b0c7838
 ---> 0e6591496f59
Successfully built 0e6591496f59
Successfully tagged app-go:1.0
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# docker images                     
REPOSITORY      TAG          IMAGE ID       CREATED              SIZE
app-go          1.0          0e6591496f59   55 seconds ago       11MB
<none>          <none>       444e1f1ee246   58 seconds ago       994MB
<none>          <none>       26c215be04f6   About a minute ago   992MB
app-python      1.0          377b72ffbce6   56 minutes ago       932MB
debian-apache   1.0          6005e6fee061   2 hours ago          276MB
ubuntu-python   latest       ae7845f0f8d0   3 hours ago          148MB
<none>          <none>       d395bdf57b29   3 hours ago          148MB
alpine          latest       49176f190c7e   3 days ago           7.05MB
mysql           latest       3842e9cdffd2   10 days ago          538MB
python          latest       ee4e7a0f1c35   10 days ago          932MB
golang          latest       8ee516e10ce0   10 days ago          992MB
httpd           latest       8653efc8c72d   11 days ago          145MB
php             7.4-apache   20a3732f422b   11 days ago          453MB
debian          10           1036dd279580   11 days ago          114MB
debian          latest       c31f65dd4cc9   11 days ago          124MB
ubuntu          latest       a8780b506fa4   3 weeks ago          77.8MB
debian          9            662c05203bab   5 months ago         101MB
centos          latest       5d0da3dc9764   14 months ago        231MB
                                                                                                          
──(root㉿kali)-[/images/go]
└─# docker run -ti --name meuappOK app-go:1.0   
Qual é o seu nome:? 
Adriano
Oi, Adriano! Eu sou a linguagem Go!                                                                                                           
┌──(root㉿kali)-[/images/go]
└─# 
```

### Realizando o upload de imagens para o Hub do Docker

Vamos publicar nossa imagem na internet para que todos possam utilizar.

```
──(root㉿kali)-[/images/go]
└─# docker rmi -f                            
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# docker rmi -f 0e6591496f59               
Untagged: app-go:1.0
Deleted: sha256:0e6591496f59a35bcdcd00f78b7403f360b8838df500e15649e4762045e06fb4
Deleted: sha256:fe8778faa4a0179d893193a9d912bd14d3dbe790453f596c8a42425ad0c40d51
Deleted: sha256:fd69fcba1e4bc74278e40b8e8f65405e9dd714adfea3f7aa183b89b427943107
Deleted: sha256:8b21677c2168f261fdec40a4f78582f398fccb4f8a558b372cf712058cf105e9
                                                                                                                                

──(root㉿kali)-[/images/go]
└─# docker login              
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: "sua-conta-usuario"
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# docker build . -t "sua-conta-usuario"/my-go-app:2022
Sending build context to Docker daemon  3.072kB
Step 1/10 : FROM golang as exec
 ---> 8ee516e10ce0
Step 2/10 : COPY app.go /go/src/app/
 ---> Using cache
 ---> fe2fa2e462f7
Step 3/10 : ENV GO111MODULE=auto
 ---> Using cache
 ---> af0bcd993e21
Step 4/10 : WORKDIR /go/src/app
 ---> Using cache
 ---> 04bfc886db74
Step 5/10 : RUN go build -o app.go .
 ---> Using cache
 ---> 444e1f1ee246
Step 6/10 : FROM alpine
 ---> 49176f190c7e
Step 7/10 : WORKDIR /appexec
 ---> Running in 5294666b3855
Removing intermediate container 5294666b3855
 ---> 40f57ca9345f
Step 8/10 : COPY --from=exec /go/src/app/ /appexec
 ---> 08209abfdca8
Step 9/10 : RUN chmod -R 755 /appexec
 ---> Running in c7a82f68de04
Removing intermediate container c7a82f68de04
 ---> f3415e62b02b
Step 10/10 : ENTRYPOINT ./app.go
 ---> Running in 9b9b3a6bfb59
Removing intermediate container 9b9b3a6bfb59
 ---> 5ade241aceab
Successfully built 5ade241aceab
Successfully tagged "sua-conta-usuario"/my-go-app:2022
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# docker images                            
REPOSITORY           TAG          IMAGE ID       CREATED             SIZE
"sua-conta-usuario"/my-go-app   2022         5ade241aceab   7 seconds ago       11MB
<none>               <none>       444e1f1ee246   11 minutes ago      994MB
<none>               <none>       26c215be04f6   11 minutes ago      992MB
app-python           1.0          377b72ffbce6   About an hour ago   932MB
debian-apache        1.0          6005e6fee061   2 hours ago         276MB
ubuntu-python        latest       ae7845f0f8d0   3 hours ago         148MB
<none>               <none>       d395bdf57b29   3 hours ago         148MB
alpine               latest       49176f190c7e   3 days ago          7.05MB
mysql                latest       3842e9cdffd2   10 days ago         538MB
python               latest       ee4e7a0f1c35   10 days ago         932MB
golang               latest       8ee516e10ce0   10 days ago         992MB
httpd                latest       8653efc8c72d   11 days ago         145MB
php                  7.4-apache   20a3732f422b   11 days ago         453MB
debian               10           1036dd279580   11 days ago         114MB
debian               latest       c31f65dd4cc9   11 days ago         124MB
ubuntu               latest       a8780b506fa4   3 weeks ago         77.8MB
debian               9            662c05203bab   5 months ago        101MB
centos               latest       5d0da3dc9764   14 months ago       231MB
                                                                                                          
──(root㉿kali)-[/images/go]
└─# docker push alvesnet/my-go-app:2022
The push refers to repository [docker.io/alvesnet/my-go-app]
c77963e7592c: Pushed 
c5c5c75ee643: Pushed 
ded7a220bb05: Mounted from library/alpine 
2022: digest: sha256:61fb086e97fd2b2c5e9de78585443cef13e76481f999b77f08054964bcc3e83b size: 1157
                                                                                                          
┌──(root㉿kali)-[/images/go]
└─# 
 
```

### Registry Criando um servidor de imagens

Criar um servidor de images.

### Docker compose

**Introdução e instalação do Docker Compose

```
┌──(root㉿kali)-[/compose/primeiro]
└─# docker-compose version                              
Command 'docker-compose' not found, but can be installed with:
apt install docker-compose
Do you want to install it? (N/y)y
apt install docker-compose
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  python3-docker python3-dockerpty
The following NEW packages will be installed:
  docker-compose python3-docker python3-dockerpty
0 upgraded, 3 newly installed, 0 to remove and 1517 not upgraded.
Need to get 225 kB of archives.
After this operation, 1,141 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://kali.download/kali kali-rolling/main amd64 python3-docker all 5.0.3-1 [90.2 kB]
Get:2 http://kali.download/kali kali-rolling/main amd64 python3-dockerpty all 0.4.1-4 [11.4 kB]
Get:3 http://kali.download/kali kali-rolling/main amd64 docker-compose all 1.29.2-2 [124 kB]
Fetched 225 kB in 6s (38.0 kB/s)        
Selecting previously unselected package python3-docker.
(Reading database ... 320488 files and directories currently installed.)
Preparing to unpack .../python3-docker_5.0.3-1_all.deb ...
Unpacking python3-docker (5.0.3-1) ...
Selecting previously unselected package python3-dockerpty.
Preparing to unpack .../python3-dockerpty_0.4.1-4_all.deb ...
Unpacking python3-dockerpty (0.4.1-4) ...
Selecting previously unselected package docker-compose.
Preparing to unpack .../docker-compose_1.29.2-2_all.deb ...
Unpacking docker-compose (1.29.2-2) ...
Setting up python3-docker (5.0.3-1) ...
Setting up python3-dockerpty (0.4.1-4) ...
Setting up docker-compose (1.29.2-2) ...
Processing triggers for kali-menu (2022.2.0) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                     
Scanning processor microcode...                                                                           
Scanning linux images...                                                                                  

Running kernel seems to be up-to-date.

The processor microcode seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─# docker-compose version
docker-compose version 1.29.2, build unknown
docker-py version: 5.0.3
CPython version: 3.10.8
OpenSSL version: OpenSSL 3.0.5 5 Jul 2022

```



**Docker compose exemplo prático**

Instalar com o docker compose o servico do Mysql e o Adminer.

```
┌──(root㉿kali)-[/compose/primeiro]
└─# nano docker-compose.yml
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─# cat docker-compose.yml 
version: "3.8"

services:
  mysqlsrv:
    image:mysql:5.7:  
    enviroment:
      image:mysql:5.7
      MYSQL_ROOT_PASSWORD:"Senha123"
      MYSQL_DATABASE:"testedb"
    ports:
      - "3306:3306"
    volumes:
      - /data/mysql-C:/var/lib/mysql
    networks:
      - minha-rede

  adminer:
    image:adminer
    ports:
    - 8080:8080
    networks:
    - minha-rede

networks:
  minha-rede:
    driver: brigde
                                                                                                          
──(root㉿kali)-[/compose/primeiro]
└─# docker-compose up -d   
Creating network "primeiro_minha-rede" with driver "bridge"
Pulling mysqlsrv (mysql:5.7)...
5.7: Pulling from library/mysql
9a0b9cd2dfe6: Pull complete
c637408ee7df: Pull complete
4c517093c276: Pull complete
301cc7d68c2a: Pull complete
17ca9bf9231a: Pull complete
9ae101e5c786: Pull complete
04baa409344e: Pull complete
f0b6015bf853: Pull complete
6005bb052ef8: Pull complete
99f303d57050: Pull complete
307a9a80c1df: Pull complete
Digest: sha256:0e3435e72c493aec752d8274379b1eac4d634f47a7781a7a92b8636fa1dc94c1
Status: Downloaded newer image for mysql:5.7
Pulling adminer (adminer:)...
latest: Pulling from library/adminer
ca7dd9ec2225: Pull complete
1b78b4fe0ca1: Pull complete
9d6040f2a28f: Pull complete
0e2e66b89284: Pull complete
3b1be5f02bec: Pull complete
96243f515dda: Pull complete
4006f78ca99d: Pull complete
3c76b37a5dd7: Pull complete
6491a80416fc: Pull complete
ff1b30214647: Pull complete
e447fcad8764: Pull complete
3814f2022577: Pull complete
e2bcaf3bd070: Pull complete
be31139c5821: Pull complete
d183076dd611: Pull complete
Digest: sha256:3b4e25b39404729b27bbb2895fa0a8fefe8ec19cdad1fe85c85500f4c080c7f4
Status: Downloaded newer image for adminer:latest
Creating primeiro_mysqlsrv_1 ... done
Creating primeiro_adminer_1  ... done
                                                                                                          
──(root㉿kali)-[/compose/primeiro]
└─# docker ps                          
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
8be6d3280936   adminer     "entrypoint.sh docke…"   31 seconds ago   Up 28 seconds   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp              primeiro_adminer_1
666e2115ce57   mysql:5.7   "docker-entrypoint.s…"   31 seconds ago   Up 28 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   primeiro_mysqlsrv_1
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─# 



```

**docker compose down**

Stop nos container e exclui as redes.

```
──(root㉿kali)-[/compose/primeiro]
└─# docker-compose down 
Stopping primeiro_adminer_1  ... done
Stopping primeiro_mysqlsrv_1 ... done
Removing primeiro_adminer_1  ... done
Removing primeiro_mysqlsrv_1 ... done
Removing network primeiro_minha-rede
                                                                                                          
DRIVER    SCOPE
7acf1f13f4c7   bridge    bridge    local
713f1bde8a82   host      host      local
45c169e9684f   none      null      local
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─# docker ps        
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─# docker-compose up   
Creating network "primeiro_minha-rede" with driver "bridge"
Creating primeiro_mysqlsrv_1 ... done
Creating primeiro_adminer_1  ... done
Attaching to primeiro_adminer_1, primeiro_mysqlsrv_1
mysqlsrv_1  | 2022-11-26 19:42:25+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.40-1.el7 started.
adminer_1   | [Sat Nov 26 19:42:25 2022] PHP 7.4.33 Development Server (http://[::]:8080) started
mysqlsrv_1  | 2022-11-26 19:42:25+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
mysqlsrv_1  | 2022-11-26 19:42:25+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.40-1.el7 started.
mysqlsrv_1  | '/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
mysqlsrv_1  | 2022-11-26T19:42:26.462518Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
mysqlsrv_1  | 2022-11-26T19:42:26.466958Z 0 [Note] mysqld (mysqld 5.7.40) starting as process 1 ...
mysqlsrv_1  | 2022-11-26T19:42:26.479106Z 0 [Note] InnoDB: PUNCH HOLE support available
mysqlsrv_1  | 2022-11-26T19:42:26.479292Z 0 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
mysqlsrv_1  | 2022-11-26T19:42:26.479313Z 0 [Note] InnoDB: Uses event mutexes
mysqlsrv_1  | 2022-11-26T19:42:26.479323Z 0 [Note] InnoDB: GCC builtin __atomic_thread_fence() is used for memory barrier
mysqlsrv_1  | 2022-11-26T19:42:26.479332Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.12
mysqlsrv_1  | 2022-11-26T19:42:26.479341Z 0 [Note] InnoDB: Using Linux native AIO
mysqlsrv_1  | 2022-11-26T19:42:26.480977Z 0 [Note] InnoDB: Number of pools: 1
mysqlsrv_1  | 2022-11-26T19:42:26.481730Z 0 [Note] InnoDB: Using CPU crc32 instructions
mysqlsrv_1  | 2022-11-26T19:42:26.486930Z 0 [Note] InnoDB: Initializing buffer pool, total size = 128M, instances = 1, chunk size = 128M
mysqlsrv_1  | 2022-11-26T19:42:26.503739Z 0 [Note] InnoDB: Completed initialization of buffer pool
mysqlsrv_1  | 2022-11-26T19:42:26.509485Z 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
mysqlsrv_1  | 2022-11-26T19:42:26.540119Z 0 [Note] InnoDB: Highest supported file format is Barracuda.
mysqlsrv_1  | 2022-11-26T19:42:26.579526Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
mysqlsrv_1  | 2022-11-26T19:42:26.579738Z 0 [Note] InnoDB: Setting file './ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
mysqlsrv_1  | 2022-11-26T19:42:26.650341Z 0 [Note] InnoDB: File './ibtmp1' size is now 12 MB.
mysqlsrv_1  | 2022-11-26T19:42:26.652201Z 0 [Note] InnoDB: 96 redo rollback segment(s) found. 96 redo rollback segment(s) are active.
mysqlsrv_1  | 2022-11-26T19:42:26.652526Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are active.
mysqlsrv_1  | 2022-11-26T19:42:26.653846Z 0 [Note] InnoDB: Waiting for purge to start
mysqlsrv_1  | 2022-11-26T19:42:26.709793Z 0 [Note] InnoDB: 5.7.40 started; log sequence number 12128391
mysqlsrv_1  | 2022-11-26T19:42:26.714860Z 0 [Note] Plugin 'FEDERATED' is disabled.
mysqlsrv_1  | 2022-11-26T19:42:26.725469Z 0 [Note] InnoDB: Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
mysqlsrv_1  | 2022-11-26T19:42:26.735127Z 0 [Note] Found ca.pem, server-cert.pem and server-key.pem in data directory. Trying to enable SSL support using them.
mysqlsrv_1  | 2022-11-26T19:42:26.736070Z 0 [Note] Skipping generation of SSL certificates as certificate files are present in data directory.
mysqlsrv_1  | 2022-11-26T19:42:26.736141Z 0 [Warning] A deprecated TLS version TLSv1 is enabled. Please use TLSv1.2 or higher.
mysqlsrv_1  | 2022-11-26T19:42:26.736168Z 0 [Warning] A deprecated TLS version TLSv1.1 is enabled. Please use TLSv1.2 or higher.
mysqlsrv_1  | 2022-11-26T19:42:26.742767Z 0 [Warning] CA certificate ca.pem is self signed.
mysqlsrv_1  | 2022-11-26T19:42:26.745098Z 0 [Note] Skipping generation of RSA key pair as key files are present in data directory.
mysqlsrv_1  | 2022-11-26T19:42:26.751749Z 0 [Note] Server hostname (bind-address): '*'; port: 3306
mysqlsrv_1  | 2022-11-26T19:42:26.753508Z 0 [Note] IPv6 is available.
mysqlsrv_1  | 2022-11-26T19:42:26.753632Z 0 [Note]   - '::' resolves to '::';
mysqlsrv_1  | 2022-11-26T19:42:26.755716Z 0 [Note] Server socket created on IP: '::'.
mysqlsrv_1  | 2022-11-26T19:42:26.763392Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
mysqlsrv_1  | 2022-11-26T19:42:26.783721Z 0 [Note] InnoDB: Buffer pool(s) load completed at 221126 19:42:26
mysqlsrv_1  | 2022-11-26T19:42:26.809040Z 0 [Note] Event Scheduler: Loaded 0 events
mysqlsrv_1  | 2022-11-26T19:42:26.811292Z 0 [Note] mysqld: ready for connections.
mysqlsrv_1  | Version: '5.7.40'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51396 Accepted
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51396 [403]: GET /?server=mysqlsrv&username=root&db=testedb&create=
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51396 Closing
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51397 Accepted
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51398 Accepted
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51398 Closed without sending a request; it was probably just an unused speculative preconnection
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51398 Closing
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51397 [200]: GET /?file=favicon.ico&version=4.8.1
adminer_1   | [Sat Nov 26 19:42:54 2022] [::ffff:192.168.1.104]:51397 Closing
adminer_1   | [Sat Nov 26 19:43:00 2022] [::ffff:192.168.1.104]:51513 Accepted
adminer_1   | [Sat Nov 26 19:43:01 2022] [::ffff:192.168.1.104]:51513 [302]: POST /?server=mysqlsrv&username=root&db=testedb&create=
adminer_1   | [Sat Nov 26 19:43:01 2022] [::ffff:192.168.1.104]:51513 Closing
adminer_1   | [Sat Nov 26 19:43:01 2022] [::ffff:192.168.1.104]:51514 Accepted
adminer_1   | [Sat Nov 26 19:43:01 2022] [::ffff:192.168.1.104]:51514 [200]: GET /?server=mysqlsrv&username=root&db=testedb&create=
adminer_1   | [Sat Nov 26 19:43:01 2022] [::ffff:192.168.1.104]:51514 Closing

```

Para interromper a execução "CTRL+C" sera interrompido tudo. 

```
adminer_1   | [Sat Nov 26 19:43:01 2022] [::ffff:192.168.1.104]:51514 Closing
^CGracefully stopping... (press Ctrl+C again to force)
Stopping primeiro_adminer_1  ... done
Stopping primeiro_mysqlsrv_1 ... 
Killing primeiro_mysqlsrv_1  ... done
ERROR: 2
```

Para iniciar em segundo plano.

```
┌──(root㉿kali)-[/compose/primeiro]
└─# docker-compose up -d
Starting primeiro_mysqlsrv_1 ... done
Starting primeiro_adminer_1  ... done
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─# docker ps           
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS          PORTS                                                  NAMES
8b7760d1ee37   adminer     "entrypoint.sh docke…"   3 minutes ago   Up 10 seconds   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp              primeiro_adminer_1
3b708554842c   mysql:5.7   "docker-entrypoint.s…"   3 minutes ago   Up 10 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   primeiro_mysqlsrv_1
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─# docker network ls      
NETWORK ID     NAME                  DRIVER    SCOPE
7acf1f13f4c7   bridge                bridge    local
713f1bde8a82   host                  host      local
45c169e9684f   none                  null      local
a4e43338b2d4   primeiro_minha-rede   bridge    local
                                                                                                          
┌──(root㉿kali)-[/compose/primeiro]
└─#                                     
```

Exemplo PHP APACHE MYSQL

Vamos criar o docker componse mais complexo.

```
┌──(root㉿kali)-[/compose]
└─# cd /data   
                                                                                                          
┌──(root㉿kali)-[/data]
└─# ls
apache-A  mysql-B  mysql-c  mysql-C  mysql-Novo  php-A
                                                                                                          
┌──(root㉿kali)-[/data]
└─# mkdir php     
                                                                                                          
┌──(root㉿kali)-[/data]
└─# cd php
                                                                                                          
┌──(root㉿kali)-[/data/php]
└─# ls
                                                                                                          
┌──(root㉿kali)-[/data/php]
└─# pwd
/data/php
                                                                                                          
┌──(root㉿kali)-[/data/php]
└─# nano index.php         
                                                                                                          
┌──(root㉿kali)-[/data/php]
└─# cat index.php         
<html>

<head>
<title>Exemplo PHP</title>
</head>


<?php
ini_set("display_errors", 1);
header('Content-Type: text/html; charset=iso-8859-1');



echo 'Versao Atual do PHP: ' . phpversion() . '<br>';

$servername = "db";
$username = "root";
$password = "Senha123";
$database = "testedb";

// Criar conexão


$link = new mysqli($servername, $username, $password, $database);

/* check connection */
if (mysqli_connect_errno()) {
    printf("Connect failed: %s\n", mysqli_connect_error());
    exit();
}

$query = "SELECT * FROM tabela_exemplo";

if ($result = mysqli_query($link, $query)) {

    
    while ($row = mysqli_fetch_assoc($result)) {
        printf ("%s %s %s <br>", $row["nome"], $row["cidade"], $row["salario"]);
    }

    
    mysqli_free_result($result);
}


mysqli_close($link);

?>

</html>
                                                                                                          
┌──(root㉿kali)-[/data/php]
┌──(root㉿kali)-[/data/php]
└─# nano upload.ini
                                                                                                          
┌──(root㉿kali)-[/data/php]
└─# cat upload.ini          
file_uploads = On
memory_limit = 500M
upload_max_filesize = 500M
post_max_size = 500M
max_execution_time = 600
max_file_uploads = 50000
max_execution_time = 5000
max_input_time = 5000
                                                                                                          
┌──(root㉿kali)-[/data/php]
└─# cd ..         
──(root㉿kali)-[/data]
└─# cd /compose 
                                                                                                          
┌──(root㉿kali)-[/compose]
└─# ls
primeiro
                                                                                                          
┌──(root㉿kali)-[/compose]
└─# mkdir php-mysql
                                                                                                          
┌──(root㉿kali)-[/compose]
└─# cd php-mysql 
                                                                                                          
┌──(root㉿kali)-[/compose/php-mysql]
└─# nano docker-compose.yml
                                                                                                          
┌──(root㉿kali)-[/compose/php-mysql]
└─# cat docker-compose.yml 
version: "3.7"

services:
  web:
    image: webdevops/php-apache:alpine-php7
    ports:
      - "4500:80"
    volumes:
      - /data/php/:/app

    networks:
      - minha-rede

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: "Senha123"
      MYSQL_DATABASE: "testedb"
    ports:
      - "3306:3306"
    volumes:
      - /data/mysql-C:/var/lib/mysql

    networks:
      - minha-rede

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      MYSQL_ROOT_PASSWORD: "Senha123"
    ports:
      - "8080:80"
    volumes:
      - /data/php/admin/uploads.ini:/usr/local/etc/php/conf.d/php-phpmyadmin.ini

    networks:
      - minha-rede

networks:
   minha-rede:
     driver: bridge
                                                                                                          
┌──(root㉿kali)-[/compose/php-mysql]
└─# 

┌──(root㉿kali)-[/compose/php-mysql]
└─# docker-compose up 
Starting php-mysql_phpmyadmin_1 ... done
Starting php-mysql_web_1        ... done
Starting php-mysql_db_1         ... done
Attaching to php-mysql_db_1, php-mysql_phpmyadmin_1, php-mysql_web_1
db_1          | 2022-11-26 20:38:03+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.40-1.el7 started.
web_1         | -> Executing /opt/docker/provision/entrypoint.d/05-permissions.sh
web_1         | -> Executing /opt/docker/provision/entrypoint.d/20-apache.sh
web_1         | -> Executing /opt/docker/provision/entrypoint.d/20-php-fpm.sh
web_1         | -> Executing /opt/docker/provision/entrypoint.d/20-php.sh
web_1         | -> Executing /opt/docker/bin/service.d/supervisor.d//10-init.sh
db_1          | 2022-11-26 20:38:04+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
db_1          | 2022-11-26 20:38:04+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.40-1.el7 started.
phpmyadmin_1  | AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.20.0.3. Set the 'ServerName' directive globally to suppress this message
phpmyadmin_1  | AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.20.0.3. Set the 'ServerName' directive globally to suppress this message
phpmyadmin_1  | [Sat Nov 26 20:38:04.349929 2022] [mpm_prefork:notice] [pid 1] AH00163: Apache/2.4.53 (Debian) PHP/8.0.19 configured -- resuming normal operations
phpmyadmin_1  | [Sat Nov 26 20:38:04.353550 2022] [core:notice] [pid 1] AH00094: Command line: 'apache2 -D FOREGROUND'
db_1          | '/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
web_1         | 2022-11-26 20:38:04,495 INFO Included extra file "/opt/docker/etc/supervisor.d/apache.conf" during parsing
web_1         | 2022-11-26 20:38:04,495 INFO Included extra file "/opt/docker/etc/supervisor.d/cron.conf" during parsing
web_1         | 2022-11-26 20:38:04,496 INFO Included extra file "/opt/docker/etc/supervisor.d/dnsmasq.conf" during parsing
web_1         | 2022-11-26 20:38:04,497 INFO Included extra file "/opt/docker/etc/supervisor.d/php-fpm.conf" during parsing
web_1         | 2022-11-26 20:38:04,497 INFO Included extra file "/opt/docker/etc/supervisor.d/postfix.conf" during parsing
web_1         | 2022-11-26 20:38:04,497 INFO Included extra file "/opt/docker/etc/supervisor.d/ssh.conf" during parsing
web_1         | 2022-11-26 20:38:04,498 INFO Included extra file "/opt/docker/etc/supervisor.d/syslog.conf" during parsing
web_1         | 2022-11-26 20:38:04,498 INFO Set uid to user 0 succeeded
web_1         | 2022-11-26 20:38:04,524 INFO RPC interface 'supervisor' initialized
web_1         | 2022-11-26 20:38:04,525 INFO supervisord started with pid 1
db_1          | 2022-11-26T20:38:05.138621Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
db_1          | 2022-11-26T20:38:05.149859Z 0 [Note] mysqld (mysqld 5.7.40) starting as process 1 ...
db_1          | 2022-11-26T20:38:05.174636Z 0 [Note] InnoDB: PUNCH HOLE support available
db_1          | 2022-11-26T20:38:05.174720Z 0 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
db_1          | 2022-11-26T20:38:05.174726Z 0 [Note] InnoDB: Uses event mutexes
db_1          | 2022-11-26T20:38:05.174728Z 0 [Note] InnoDB: GCC builtin __atomic_thread_fence() is used for memory barrier
db_1          | 2022-11-26T20:38:05.174730Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.12
db_1          | 2022-11-26T20:38:05.174735Z 0 [Note] InnoDB: Using Linux native AIO
db_1          | 2022-11-26T20:38:05.179218Z 0 [Note] InnoDB: Number of pools: 1
db_1          | 2022-11-26T20:38:05.183464Z 0 [Note] InnoDB: Using CPU crc32 instructions
db_1          | 2022-11-26T20:38:05.187095Z 0 [Note] InnoDB: Initializing buffer pool, total size = 128M, instances = 1, chunk size = 128M
db_1          | 2022-11-26T20:38:05.202624Z 0 [Note] InnoDB: Completed initialization of buffer pool
db_1          | 2022-11-26T20:38:05.207220Z 0 [Note] InnoDB: If the mysqld execution user is authorized, page cleaner thread priority can be changed. See the man page of setpriority().
db_1          | 2022-11-26T20:38:05.230720Z 0 [Note] InnoDB: Highest supported file format is Barracuda.
db_1          | 2022-11-26T20:38:05.331323Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
db_1          | 2022-11-26T20:38:05.333626Z 0 [Note] InnoDB: Setting file './ibtmp1' size to 12 MB. Physically writing the file full; Please wait ...
db_1          | 2022-11-26T20:38:05.373873Z 0 [Note] InnoDB: File './ibtmp1' size is now 12 MB.
db_1          | 2022-11-26T20:38:05.375385Z 0 [Note] InnoDB: 96 redo rollback segment(s) found. 96 redo rollback segment(s) are active.
db_1          | 2022-11-26T20:38:05.375479Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are active.
db_1          | 2022-11-26T20:38:05.375819Z 0 [Note] InnoDB: Waiting for purge to start
db_1          | 2022-11-26T20:38:05.429283Z 0 [Note] InnoDB: 5.7.40 started; log sequence number 12133968
db_1          | 2022-11-26T20:38:05.431519Z 0 [Note] InnoDB: Loading buffer pool(s) from /var/lib/mysql/ib_buffer_pool
db_1          | 2022-11-26T20:38:05.433212Z 0 [Note] Plugin 'FEDERATED' is disabled.
db_1          | 2022-11-26T20:38:05.524153Z 0 [Note] Found ca.pem, server-cert.pem and server-key.pem in data directory. Trying to enable SSL support using them.
db_1          | 2022-11-26T20:38:05.524316Z 0 [Note] Skipping generation of SSL certificates as certificate files are present in data directory.
db_1          | 2022-11-26T20:38:05.524321Z 0 [Warning] A deprecated TLS version TLSv1 is enabled. Please use TLSv1.2 or higher.
db_1          | 2022-11-26T20:38:05.524323Z 0 [Warning] A deprecated TLS version TLSv1.1 is enabled. Please use TLSv1.2 or higher.
db_1          | 2022-11-26T20:38:05.528997Z 0 [Note] InnoDB: Buffer pool(s) load completed at 221126 20:38:05
web_1         | 2022-11-26 20:38:05,534 INFO spawned: 'syslogd' with pid 44
web_1         | 2022-11-26 20:38:05,554 INFO spawned: 'php-fpmd' with pid 45
web_1         | 2022-11-26 20:38:05,556 INFO spawned: 'apached' with pid 46
web_1         | 2022-11-26 20:38:05,559 INFO spawned: 'crond' with pid 47
db_1          | 2022-11-26T20:38:05.565998Z 0 [Warning] CA certificate ca.pem is self signed.
db_1          | 2022-11-26T20:38:05.566328Z 0 [Note] Skipping generation of RSA key pair as key files are present in data directory.
db_1          | 2022-11-26T20:38:05.572464Z 0 [Note] Server hostname (bind-address): '*'; port: 3306
db_1          | 2022-11-26T20:38:05.572580Z 0 [Note] IPv6 is available.
db_1          | 2022-11-26T20:38:05.573847Z 0 [Note]   - '::' resolves to '::';
db_1          | 2022-11-26T20:38:05.575111Z 0 [Note] Server socket created on IP: '::'.
db_1          | 2022-11-26T20:38:05.585218Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
web_1         | -> Executing /opt/docker/bin/service.d/php-fpm.d//10-init.sh
web_1         | Setting php-fpm user to application
web_1         | 2022-11-26 20:38:05,614 INFO success: php-fpmd entered RUNNING state, process has stayed up for > than 0 seconds (startsecs)
web_1         | 2022-11-26 20:38:05,615 INFO success: apached entered RUNNING state, process has stayed up for > than 0 seconds (startsecs)
web_1         | 2022-11-26 20:38:05,615 INFO success: crond entered RUNNING state, process has stayed up for > than 0 seconds (startsecs)
web_1         | -> Executing /opt/docker/bin/service.d/syslog-ng.d//10-init.sh
web_1         | -> Executing /opt/docker/bin/service.d/cron.d//10-init.sh
web_1         | -> Executing /opt/docker/bin/service.d/httpd.d//10-init.sh
db_1          | 2022-11-26T20:38:05.771360Z 0 [Note] Event Scheduler: Loaded 0 events
db_1          | 2022-11-26T20:38:05.772296Z 0 [Note] mysqld: ready for connections.
db_1          | Version: '5.7.40'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
web_1         | [Sat Nov 26 20:38:05.834706 2022] [so:warn] [pid 61:tid 140477215628136] AH01574: module slotmem_shm_module is already loaded, skipping
web_1         | [Sat Nov 26 20:38:05.870581 2022] [so:warn] [pid 61:tid 140477215628136] AH01574: module socache_shmcb_module is already loaded, skipping
web_1         | [2022-11-26T20:38:05.897557] WARNING: With use-dns(no), dns-cache() will be forced to 'no' too!;
web_1         | [SYSLOG] syslog-ng[44]: syslog-ng starting up; version='3.19.1'
web_1         | [Sat Nov 26 20:38:05.921358 2022] [so:warn] [pid 61:tid 140477215628136] AH01574: module slotmem_shm_module is already loaded, skipping
web_1         | [Sat Nov 26 20:38:05.922808 2022] [so:warn] [pid 61:tid 140477215628136] AH01574: module socache_shmcb_module is already loaded, skipping
web_1         | [Sat Nov 26 20:38:05.931523 2022] [lbmethod_heartbeat:notice] [pid 61:tid 140477215628136] AH02282: No slotmem from mod_heartmonitor
web_1         | [Sat Nov 26 20:38:05.933144 2022] [core:warn] [pid 61:tid 140477215628136] AH00098: pid file /run/apache2/httpd.pid overwritten -- Unclean shutdown of previous Apache run?
web_1         | [Sat Nov 26 20:38:05.935512 2022] [mpm_event:notice] [pid 61:tid 140477215628136] AH00489: Apache/2.4.43 (Unix) OpenSSL/1.1.1d configured -- resuming normal operations
web_1         | [Sat Nov 26 20:38:05.937196 2022] [core:notice] [pid 61:tid 140477215628136] AH00094: Command line: '/usr/sbin/httpd -D FOREGROUND'
web_1         | [26-Nov-2022 20:38:06] NOTICE: fpm is running, pid 45
web_1         | [26-Nov-2022 20:38:06] NOTICE: ready to handle connections
web_1         | 2022-11-26 20:38:07,256 INFO success: syslogd entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:47 +0000] "GET /?server=mysqlsrv&username=root&db=testedb&sql=select%20*%20from%20tabela_exemplo HTTP/1.1" 200 6527 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/vendor/codemirror/addon/lint/lint.css?v=5.2.0 HTTP/1.1" 200 1634 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/vendor/codemirror/addon/hint/show-hint.css?v=5.2.0 HTTP/1.1" 200 660 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /themes/pmahomme/jquery/jquery-ui.css HTTP/1.1" 200 8824 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/vendor/codemirror/lib/codemirror.css?v=5.2.0 HTTP/1.1" 200 2848 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/vendor/jquery/jquery.min.js?v=5.2.0 HTTP/1.1" 200 31255 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /themes/pmahomme/css/theme.css?v=5.2.0 HTTP/1.1" 200 61829 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/vendor/sprintf.js?v=5.2.0 HTTP/1.1" 200 2982 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/dist/ajax.js?v=5.2.0 HTTP/1.1" 200 8112 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/vendor/jquery/jquery-migrate.js?v=5.2.0 HTTP/1.1" 200 10026 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/dist/keyhandler.js?v=5.2.0 HTTP/1.1" 200 1385 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/vendor/jquery/jquery-ui.min.js?v=5.2.0 HTTP/1.1" 200 68358 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:48 +0000] "GET /js/dist/name-conflict-fixes.js?v=5.2.0 HTTP/1.1" 200 338 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/vendor/bootstrap/bootstrap.bundle.min.js?v=5.2.0 HTTP/1.1" 200 23406 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/vendor/js.cookie.js?v=5.2.0 HTTP/1.1" 200 1876 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/vendor/jquery/jquery.validate.js?v=5.2.0 HTTP/1.1" 200 14035 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/vendor/jquery/jquery.debounce-1.0.6.js?v=5.2.0 HTTP/1.1" 200 976 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/vendor/jquery/jquery-ui-timepicker-addon.js?v=5.2.0 HTTP/1.1" 200 19282 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/dist/menu_resizer.js?v=5.2.0 HTTP/1.1" 200 2283 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/dist/cross_framing_protection.js?v=5.2.0 HTTP/1.1" 200 613 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/messages.php?l=pt_BR&v=5.2.0&lang=pt_BR HTTP/1.1" 200 9482 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/dist/config.js?v=5.2.0 HTTP/1.1" 200 6771 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/dist/doclinks.js?v=5.2.0 HTTP/1.1" 200 4090 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/dist/navigation.js?v=5.2.0 HTTP/1.1" 200 11942 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:49 +0000] "GET /js/dist/functions.js?v=5.2.0 HTTP/1.1" 200 37758 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:50 +0000] "GET /js/dist/indexes.js?v=5.2.0 HTTP/1.1" 200 6619 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:50 +0000] "GET /js/dist/common.js?v=5.2.0 HTTP/1.1" 200 1902 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:50 +0000] "GET /js/dist/page_settings.js?v=5.2.0 HTTP/1.1" 200 950 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:50 +0000] "GET /js/vendor/codemirror/lib/codemirror.js?v=5.2.0 HTTP/1.1" 200 106796 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:50 +0000] "GET /js/vendor/codemirror/addon/runmode/runmode.js?v=5.2.0 HTTP/1.1" 200 1503 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:50 +0000] "GET /js/vendor/codemirror/mode/sql/sql.js?v=5.2.0 HTTP/1.1" 200 14799 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:50 +0000] "GET /js/vendor/codemirror/addon/hint/show-hint.js?v=5.2.0 HTTP/1.1" 200 5887 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/vendor/codemirror/addon/hint/sql-hint.js?v=5.2.0 HTTP/1.1" 200 3097 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/vendor/codemirror/addon/lint/lint.js?v=5.2.0 HTTP/1.1" 200 3318 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/dist/codemirror/addon/lint/sql-lint.js?v=5.2.0 HTTP/1.1" 200 800 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/vendor/tracekit.js?v=5.2.0 HTTP/1.1" 200 11963 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/dist/error_report.js?v=5.2.0 HTTP/1.1" 200 3043 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/dist/drag_drop_import.js?v=5.2.0 HTTP/1.1" 200 3798 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/dist/shortcuts_handler.js?v=5.2.0 HTTP/1.1" 200 1194 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /js/dist/console.js?v=5.2.0 HTTP/1.1" 200 10450 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /themes/dot.gif HTTP/1.1" 200 325 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /themes/pmahomme/img/logo_right.png HTTP/1.1" 200 4592 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:41:51 +0000] "GET /themes/pmahomme/img/b_help.png HTTP/1.1" 200 989 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 127.0.0.1 - - [26/Nov/2022:20:41:57 +0000] "OPTIONS * HTTP/1.0" 200 126 "-" "Apache/2.4.53 (Debian) PHP/8.0.19 (internal dummy connection)"
phpmyadmin_1  | 127.0.0.1 - - [26/Nov/2022:20:41:58 +0000] "OPTIONS * HTTP/1.0" 200 126 "-" "Apache/2.4.53 (Debian) PHP/8.0.19 (internal dummy connection)"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:00 +0000] "POST /index.php?route=/ HTTP/1.1" 302 1002 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:01 +0000] "GET /index.php?route=/&route=%2F&db=testedb HTTP/1.1" 200 15259 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:01 +0000] "GET /js/dist/database/structure.js?v=5.2.0 HTTP/1.1" 200 4200 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:01 +0000] "GET /js/dist/table/change.js?v=5.2.0 HTTP/1.1" 200 8419 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:01 +0000] "GET /js/messages.php?l=pt_BR&v=5.2.0 HTTP/1.1" 200 9483 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/logo_left.png HTTP/1.1" 200 2998 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/arrow_ltr.png HTTP/1.1" 200 384 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/s_loggoff.png HTTP/1.1" 200 910 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/b_docs.png HTTP/1.1" 200 990 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/b_home.png HTTP/1.1" 200 1018 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/b_sqlhelp.png HTTP/1.1" 200 797 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/s_cog.png HTTP/1.1" 200 726 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/s_reload.png HTTP/1.1" 200 811 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/s_link.png HTTP/1.1" 200 564 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/b_newdb.png HTTP/1.1" 200 869 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "POST /index.php?route=/config/get HTTP/1.1" 200 2470 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "POST /index.php?route=/navigation&ajax_request=1 HTTP/1.1" 200 3226 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/b_plus.png HTTP/1.1" 200 426 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "GET /themes/pmahomme/img/s_db.png HTTP/1.1" 200 612 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:02 +0000] "POST /index.php?route=/config/get HTTP/1.1" 200 2561 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/s_host.png HTTP/1.1" 200 875 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_props.png HTTP/1.1" 200 857 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_sql.png HTTP/1.1" 200 964 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_search.png HTTP/1.1" 200 822 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_export.png HTTP/1.1" 200 798 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_tblops.png HTTP/1.1" 200 821 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_import.png HTTP/1.1" 200 840 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/s_rights.png HTTP/1.1" 200 745 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_routines.png HTTP/1.1" 200 590 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_events.png HTTP/1.1" 200 987 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_triggers.png HTTP/1.1" 200 730 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_relations.png HTTP/1.1" 200 423 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/s_top.png HTTP/1.1" 200 503 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/s_asc.png HTTP/1.1" 200 429 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_no_favorite.png HTTP/1.1" 200 661 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_browse.png HTTP/1.1" 200 770 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_select.png HTTP/1.1" 200 877 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "POST /index.php?route=/navigation&ajax_request=1 HTTP/1.1" 200 2850 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_insrow.png HTTP/1.1" 200 440 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "POST /index.php?route=/config/set HTTP/1.1" 200 2454 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_empty.png HTTP/1.1" 200 805 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_drop.png HTTP/1.1" 200 921 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_tblanalyse.png HTTP/1.1" 200 435 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_print.png HTTP/1.1" 200 923 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_table_add.png HTTP/1.1" 200 855 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/window-new.png HTTP/1.1" 200 752 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_more.png HTTP/1.1" 200 394 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/ajax_clock_small.gif HTTP/1.1" 200 2095 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/console.png HTTP/1.1" 200 576 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /themes/pmahomme/img/b_minus.png HTTP/1.1" 200 406 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:03 +0000] "GET /favicon.ico HTTP/1.1" 200 22788 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /index.php?route=/sql&pos=0&db=testedb&table=tabela_exemplo&ajax_request=true&ajax_page_request=true&_nocache=1669495331968471152&token=4b3b243f6b6b244023377c4849644f64 HTTP/1.1" 200 7724 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /themes/pmahomme/img/s_tbl.png HTTP/1.1" 200 929 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /themes/pmahomme/img/b_tblexport.png HTTP/1.1" 200 798 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /themes/pmahomme/img/b_chart.png HTTP/1.1" 200 734 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /themes/pmahomme/img/b_tblimport.png HTTP/1.1" 200 840 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /themes/pmahomme/img/s_notice.png HTTP/1.1" 200 851 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /themes/pmahomme/img/s_success.png HTTP/1.1" 200 749 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /js/vendor/jquery/jquery.uitablefilter.js?v=5.2.0 HTTP/1.1" 200 1687 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /js/dist/gis_data_editor.js?v=5.2.0 HTTP/1.1" 200 3837 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /index.php?route=/recent-table&ajax_request=1&recent_table=1&no_debug=true&_nocache=166949533231350051&token=4b3b243f6b6b244023377c4849644f64 HTTP/1.1" 200 2516 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /js/dist/sql.js?v=5.2.0 HTTP/1.1" 200 9892 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /js/dist/multi_column_sort.js?v=5.2.0 HTTP/1.1" 200 868 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:06 +0000] "GET /js/dist/makegrid.js?v=5.2.0 HTTP/1.1" 200 17821 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:07 +0000] "GET /themes/pmahomme/img/b_view_add.png HTTP/1.1" 200 962 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 192.168.1.104 - - [26/Nov/2022:20:42:07 +0000] "GET /themes/pmahomme/img/col_pointer.png HTTP/1.1" 200 385 "http://192.168.1.107:8080/themes/pmahomme/css/theme.css?v=5.2.0" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"
phpmyadmin_1  | 127.0.0.1 - - [26/Nov/2022:20:42:12 +0000] "OPTIONS * HTTP/1.0" 200 126 "-" "Apache/2.4.53 (Debian) PHP/8.0.19 (internal dummy connection)"

──(root㉿kali)-[/home/kali]
└─# docker ps                          
CONTAINER ID   IMAGE                              COMMAND                  CREATED         STATUS         PORTS                                                      NAMES
f5db28cee216   phpmyadmin/phpmyadmin              "/docker-entrypoint.…"   9 minutes ago   Up 4 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp                      php-mysql_phpmyadmin_1
87b9b1998b34   mysql:5.7                          "docker-entrypoint.s…"   9 minutes ago   Up 4 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp       php-mysql_db_1
a153fac509d2   webdevops/php-apache:alpine-php7   "/entrypoint supervi…"   9 minutes ago   Up 4 minutes   443/tcp, 9000/tcp, 0.0.0.0:4500->80/tcp, :::4500->80/tcp   php-mysql_web_1
                                                                                                          
──(root㉿kali)-[/home/kali]
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
       valid_lft 2630sec preferred_lft 2630sec
    inet6 fe80::a00:27ff:fedb:966a/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

Acesso o Banco de Dados da Aplicação.

[![Watch the video](https://th.bing.com/th/id/R.085c190ae645e6391ef5194a5891d116?rik=JuswhmeaX8WEYw&pid=ImgRaw&r=0)](https://www.youtube.com/watch?v=NoQpCAki0X8)

**Utilizando exemplos do Github do Docker**

Usaremos outro exemplo.

Nextcloud: mazenamento de Arquivos na Nuvem
Redis: Banco de Dados utilizado em memoria para agilizar o processo antes de gravar no banco de dados.
Mariadb: È uma versão opensource do Mysql os comando são mesmo a instalacao similar.

```
──(root㉿kali)-[/home/kali]
└─# cd /compose 
                                                                                                          
┌──(root㉿kali)-[/compose]
└─# 
                                                                                                          
┌──(root㉿kali)-[/compose]
└─# ls
php-mysql  primeiro
                                                                                                          
┌──(root㉿kali)-[/compose]
└─# mkdir cloud
                                                                                                          
┌──(root㉿kali)-[/compose]
└─# cd cloud   
                                                                                                          
┌──(root㉿kali)-[/compose/cloud]
└─# ls
                                                                                                          
┌──(root㉿kali)-[/compose/cloud]
└─# nano docker-compose.yml
                                                                                                          
┌──(root㉿kali)-[/compose/cloud]
└─# docker-compose up -d                                
Creating network "cloud_redisnet" with the default driver
Creating network "cloud_dbnet" with the default driver
Creating volume "cloud_db_data" with default driver
Creating volume "cloud_nc_data" with default driver
Pulling nc (nextcloud:apache)...
apache: Pulling from library/nextcloud
a603fa5e3b41: Already exists
c428f1a49423: Already exists
156740b07ef8: Already exists
fb5a4c8af82f: Already exists
25f85b498fd5: Already exists
9b233e420ac7: Already exists
fe42347c4ecf: Already exists
9a6d1815e192: Pull complete
f75b3fa4d0aa: Pull complete
a7dfcc62e534: Pull complete
e4c066aae5da: Pull complete
7e78f1cb630f: Pull complete
33ca68bf26ea: Pull complete
073b9282a517: Pull complete
4f88e82c05cc: Pull complete
f2a26e47144e: Pull complete
3e3fbaa76674: Pull complete
7228284e6554: Pull complete
c8a9e0c5a248: Pull complete
a123244e7ee7: Pull complete
Digest: sha256:a78077ed988fef925122d762102883102fb5b375dc1c758dd92b6b39eac22158
Status: Downloaded newer image for nextcloud:apache
Pulling redis (redis:alpine)...
alpine: Pulling from library/redis
ca7dd9ec2225: Already exists
83276aa4de36: Pull complete
731cc432e6da: Pull complete
862de9590cc6: Pull complete
a26b23e71d57: Pull complete
4b937ee5a2e0: Pull complete
Digest: sha256:2700d5097763fda285c463f4eefc3d0730a2df2a9d48e66707b19d5a5e5f23d4
Status: Downloaded newer image for redis:alpine
Pulling db (mariadb:10.5)...
10.5: Pulling from library/mariadb
eaead16dc43b: Pull complete
4f6f4832182b: Pull complete
6eed3156c415: Pull complete
d1adc5c9f5ec: Pull complete
ff10c4143de9: Pull complete
86940c6e6b47: Pull complete
eb690670c568: Pull complete
ee0c944b1557: Pull complete
Digest: sha256:78ee2bce40a0560223349f83a6ffc50422135f4870322509a00d3a79842dcbc3
Status: Downloaded newer image for mariadb:10.5
Creating cloud_redis_1 ... done
Creating cloud_db_1    ... done
Creating cloud_nc_1    ... done
                                                                                                          
┌──(root㉿kali)-[/compose/cloud]
└─# 
──(root㉿kali)-[/compose/cloud]
└─# docker ps           
CONTAINER ID   IMAGE                              COMMAND                  CREATED          STATUS          PORTS                                                      NAMES
cdeb39af3f07   nextcloud:apache                   "/entrypoint.sh apac…"   11 minutes ago   Up 11 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp                          cloud_nc_1
7d074967c1b6   redis:alpine                       "docker-entrypoint.s…"   11 minutes ago   Up 11 minutes   6379/tcp                                                   cloud_redis_1
7a78467e2f2f   mariadb:10.5                       "docker-entrypoint.s…"   11 minutes ago   Up 11 minutes   3306/tcp                                                   cloud_db_1
f5db28cee216   phpmyadmin/phpmyadmin              "/docker-entrypoint.…"   2 hours ago      Up 2 hours      0.0.0.0:8080->80/tcp, :::8080->80/tcp                      php-mysql_phpmyadmin_1
87b9b1998b34   mysql:5.7                          "docker-entrypoint.s…"   2 hours ago      Up 2 hours      0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp       php-mysql_db_1
a153fac509d2   webdevops/php-apache:alpine-php7   "/entrypoint supervi…"   2 hours ago      Up 2 hours      443/tcp, 9000/tcp, 0.0.0.0:4500->80/tcp, :::4500->80/tcp   php-mysql_web_1
```

### Criando um container de uma Aplicação WEB

- Criando um arquivo YML com definicoes de um servidor apache (httpd);
- Especificar no arquivo YML o local onde os arquivos da aplicação estarão. A aplicação será um simples Hello World.
- Subir o arquivo YML e a aplicação para um repositório Github.

# Criando um Cluster com o Docker Swarm

### O que é um Cluster?

Vamos trabalhar com o Docker Swarm, importante saber o que é um cluster.

computadores ligados que trabalham em conjuntos, e que em muitos aspectos  podem ser considerados como um único sistema.
computadores em cluster executam a mesma tarefa controlado e programado por software.

Cada computador presente em cluster é conhecido como nó (node).

### O que é Docker Swarm?

È um recurso do Docker que fornece funcionalidades de orquestração de contêiner, incluindo clustering nativo de hosts do Docker e agendamento de cargas de trabalho de contêineres. Um grupo de hosts do Docker formam um cluster "Swarm".

### Nós gerenciadores e nós de trabalho

Um swarm é composto de dois tipos de hosts de contêiner: nós *gerenciadores* e nós de *trabaho*.
Todos os comandos CLI do Docker para controlar e monitorar um swarm deve ser executados em um de seus nós *gerenciadores*.

Os nós gerenciadores podem ser considerados os "ZELADORES" do estado Swarm - juntos, ele formam um grupo de consenso que mantem o reconhecimento do estado dos servicos em execução no swarm, e o trabalho deles é garantir que o estado real do swarm seja sempre condizente com o estado desejado, conforme definido pelo desenvolvedor ou administrador. Para ingressar em um swarm, um nó de trabalho deve usar "token de associacao' gerado pelo nó gerenciador quando o swarm foi inicializado.

![image](https://user-images.githubusercontent.com/33209944/209484128-442ce3d1-87cd-43df-a7b1-849bac39776b.png)

Vantagens de ter um cluster se perder um nó!. Os containers serão migrados pelo gerenciador swarm para os demais nós.

![image](https://user-images.githubusercontent.com/33209944/209484273-3b86f319-be73-4d91-aabe-61091e93c050.png)

O nó onde o swarm está instalado tambem pode ser o nó gerenciamento somente ou também nó de trabalho recebendo containers, caso não recursos financeiro pode usar tambem.

![image](https://user-images.githubusercontent.com/33209944/209484345-45838f04-aa4b-4f98-80b4-8264eff2fc52.png)

Se caso perder o nó gerenciador com o swarm tambem perderá os outros containers. Por isso recomenda-se que se tenha um backup do nó onde está instalado o swarm.

![image](https://user-images.githubusercontent.com/33209944/209484427-e8a3273d-fbdd-4d77-9b9b-cd3e839e2b48.png)

Caso um nó cair um dos backup será acionado desde que se tenha pelo menos 51% dos managers funcionando. Sempre tenha backup em números impars.

![image](https://user-images.githubusercontent.com/33209944/209484506-55158ffd-e1a4-4855-882f-37872c99afe0.png)

### Cluster em nuvem - Redes e sub redes

O cluster pode estar em nuvem que seria o ideal. Como AWS, GCP, AZUR, ORACLE ou outros...

Exemplo AWS - EC2:

Importante criar uma subrede para gerenciar seu cluster com o VPC.

![image](https://user-images.githubusercontent.com/33209944/209484674-2be84a87-789f-4757-9fc3-eed1110d3e67.png)

![image](https://user-images.githubusercontent.com/33209944/209484686-afc196b0-0700-419a-a1e9-bea2153f48cd.png)

Criar VPC escolha a opção **Somente VPC**, atribuir uma **Tag de nome** no nosso caso foi **docker**, e escolha a opção **Entrada manual de CIDR IPv4** e informe CIDR IPv4 exemplo **172.31.0.0/24**, e escolha a opção **Nenhum bloco CIDR Ipv6**. Depois clique em Criar VPC.

![image](https://user-images.githubusercontent.com/33209944/209484781-8bad9f78-3bc4-4bc4-ad9f-9da9375bfe70.png)

Sua rede será criada:

![image](https://user-images.githubusercontent.com/33209944/209484796-5262bed6-c3b3-4727-a489-4222973582ee.png)

Agora vamos criar uma sub-rede:

![image](https://user-images.githubusercontent.com/33209944/209485237-18dcb650-4926-4aaf-a92a-0b0e4f5d837a.png)

E atribuimos ela a rede **docker**

![image](https://user-images.githubusercontent.com/33209944/209485273-12f86d2e-f1bf-4c55-a7e8-dba1d488475c.png)

Na **Configuracoes de sub-rede** definimos um nome da sub-rede **sub-rede-docker**, selecionamos a **zona de disponibilidade**, e atribuimos o mesmo **Bloco CIDR IPv4** 172.31.0.0/24, depois clicar em Criar sub-rede:

![image](https://user-images.githubusercontent.com/33209944/209485360-51eb41e2-e6a3-4980-a987-724eb68a645e.png)

![image](https://user-images.githubusercontent.com/33209944/209485469-581ae48a-0af8-4560-b62a-732bca461cd3.png)

O proximo passo é configurar o nosso **Gateway** para que a nossa rede tenha acesso a Internet.

![image](https://user-images.githubusercontent.com/33209944/209485520-73329598-ac33-4789-a43f-553deea86547.png)

Vamos aribuir uma **Tag de nome** que será gateway-docker para ficar fácil associar a nossa rede docker.

![image](https://user-images.githubusercontent.com/33209944/209485566-350b531f-027e-4aca-b28f-59cc1e263512.png)

Agora vamos precisar do **ID do gateway da Internet** selecionado e copiado acima, e clique **Associar a uma VPC**:

![image](https://user-images.githubusercontent.com/33209944/209485657-cc1538a2-b90d-4b11-abdf-06e71f28fa74.png)

![image](https://user-images.githubusercontent.com/33209944/209485705-134284bf-3879-4836-92d0-b58e3379c57a.png)

![image](https://user-images.githubusercontent.com/33209944/209485726-c244f767-f768-4db2-b676-0db191709515.png)

Um ultimo passo é associa a **Tabelas de rotas**, clique no ID referente a nossa rede VPC:

![image](https://user-images.githubusercontent.com/33209944/209485783-7cca30b8-4751-4e68-8de2-2449ce932b02.png)

Depois clique em **Editar Rotas**:

![image](https://user-images.githubusercontent.com/33209944/209485875-ad30d785-6526-4810-b0c6-b60dc1bb9749.png)

Associamos o **Destino** para 0.0.0.0/0 e **Alvo** será o nosso ID do Gateway:

![image](https://user-images.githubusercontent.com/33209944/209485922-08567e2c-183c-43fa-a076-86ca61fb7900.png)

![image](https://user-images.githubusercontent.com/33209944/209485949-b0969852-a20b-40f3-bd7c-6b8c381a4212.png)


### Cluster em nuvem - Subindo máquinas EC2

Clique em **Executar instância**:

![image](https://user-images.githubusercontent.com/33209944/209486134-e0bcece5-c665-47dc-b753-05a434ff3033.png)

Nome de nossas maquinas **Nome e tags** será Docker Swarm:

![image](https://user-images.githubusercontent.com/33209944/209486452-d19f993a-4ac7-4e36-8e80-4c62ee5c90f6.png)

Em **Network settings** vamos associar a nossa VPC e nossa Sub-rede, Ativar IP Publico, e Criar u grupo de segurança:

![image](https://user-images.githubusercontent.com/33209944/209486615-ec385820-bf6d-4d2a-8f9e-4724cfc59011.png)

Vamos subir 4 instancias e na opção **Dados do usuário** adicionamos as linhas de nosso script para instalação automatica do docker em todas as maquinas.

![image](https://user-images.githubusercontent.com/33209944/209486368-16cdf63a-bebd-4231-81e7-47f7e8badafd.png)
































