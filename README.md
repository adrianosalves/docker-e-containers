# Docker & Containers

Necessário conhecimento de Linux!

Gerenciar Virtualização (Hyperv, AWS, GCP, Azure).

Saber usar ferramentas como o Putty para Acessar Remoto o Servidores Linux.

cat /etc/*release*  =  Verifica a versão do kernek do Linux.

#1.Realizando o download de imagens e verificando seu estado:#

docker pull "nome-da-imagem" = Faz o Download da versão mais atualizada da imagem para seu sistema.

docker images = verifica as imagens baixadas.

docker run "nome-da-imagem" = Executa a imagem no seu sistema por alguns segundo e finaliza.

docker ps = Verifica quais os container está em execução.

docker ps -a = Mostra os container executado recentemente e se eles estão em execução ou não!

*2.Executando um contêiner*

docker run "nome-da-imagem" sleep 10 = Executa a imagem no seu sistema por 10s depois finaliza.

docker run "nome-da-imagem" sleep 1500 = Executa a imagem no seu sistema por 1500s depois finaliza.

docker stop "id-da-imagem" ou "nome-da-imagem" = Finaliza a execução da imagem.

docker run -it "nome-da-imagem" = Executa a imagem no seu sistema, o uso do paramento "t" aloca um terminal (bash) permitindo trabalha com essa imagem, e o "i" permite interação com esse container.











