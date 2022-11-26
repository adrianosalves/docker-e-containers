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









