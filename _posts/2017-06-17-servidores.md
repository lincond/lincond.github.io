---
layout: post
title: Introdução a servidores
---
> Em informática, um servidor é um software ou computador, com sistema de computação centralizada que fornece serviços a uma rede de computadores, chamada de cliente.

[Wikipédia](https://pt.wikipedia.org/wiki/VirtualBox)

Nesse artigo iremos abordar o básico para a manutenção de serviços rodando em um servidor Ubuntu, mostrando desde a navegação pelos diretórios do servidor, permissões, gerenciador de serviços até configuração de hosts virtuais do [Apache](https://pt.wikipedia.org/wiki/Servidor_Apache).

---
## Preparando a o ambiente

### Instalando o VirtualBox
> VirtualBox é um software de virtualização que visa criar ambientes para instalação de sistemas distintos. Ele permite a instalação e utilização de um sistema operacional dentro de outro, assim como seus respectivos softwares, como dois ou mais computadores independentes, mas compartilhando fisicamente o mesmo hardware.

[Wikipédia](https://pt.wikipedia.org/wiki/VirtualBox)


Utilizaremos o virtualbox para simular serviços rodando em um servidor, para isso baixe o versão para o seu sistema nesse [link](https://www.virtualbox.org/wiki/Downloads) e siga os passos do programa para instalar em sua máquina.

### Instalando o Ubuntu
O Ubuntu fornece uma distro focada em servidores que pode ser baixada [nesse link](https://www.ubuntu.com/download/server).
Depois de baixar é preciso criar uma nova máquina virtual no VBox e adicionar a imagem (.img) na controladora de disco e seguir as instruções para a instalação do sistema no disco rígido virtual.
A controlador de disco pode ser encontrada em **Configurações > Armazenamento > Controladora: IDE**

----
## Conhecendo um pouco das ferramentas

### SSH, a telinha preta
> Secure Shell (SSH) é um protocolo de rede criptográfico para operação de serviços de rede de forma segura sobre uma rede insegura.[1] A **melhor aplicação** de exemplo conhecida é para **login remoto a sistemas de computadores** pelos usuários.

[Wikipédia](https://pt.wikipedia.org/wiki/Secure_Shell)

O **SSH** permite o **acesso remoto** a shell do servidor e através dela instalamos novos serviços, atualizamos e damos manutenção na máquina. Ele é a nossa principal ferramenta.

Para conectar no ssh do nosso servidor podemos executar o comando
~~~
ssh -p <porta> <usuario>@<dominio>
~~~
Exemplo,
~~~
ssh -p 22 lincon@level5jr.com.br
~~~  
Lembrando que **não** é necessário acrescentar o __-p__ se a porta for a **padrão(22)**, mas como somos pessoas sensatas, não utilizamos a porta padrão por questões de segurança.

### Vim, o editor para hipsters

O Vim(Vi iMproved) é um **editor de textos** que **não** requer modo gráfico, perfeito para utilizar em um sessão **SSH**. Levando em conta que a maioria dos serviços rodando em servidores utilizam **arquivos de configuração**, o vim é uma importante ferramenta que é utilizada junto ao SSH para configurar e subir serviços no servidor.

Para abrir um arquivo com o vim utilizamos o comando 
~~~
vim arquivo
~~~  

Os homens sem fé costumam utilizar o **nano**, que também é uma opção.

----
## Navegando pelo sistema
### Diretórios e arquivos

Um dos comandos mais básicos que temos é o 
~~~ 
ls 
~~~
, que lista todos os arquivos e diretórios do caminho atual(que pode ser checado com 
~~~
pwd
~~~
), porém, para visualizar os arquivos de um forma mais interessante utilizamos os argumentos __-lah__ que lista os arquivos em uma forma de lista, além de mostrar o tamanho de uma forma visível, as permissões e o dono do arquivo(algo muito importante para os serviços funcionarem de forma correta) e também os arquivos ocultos(que começam com .) Então o comando fica: 
~~~
ls -lah
~~~  
Para navegação dentro dos diretórios do servidor utilizamos o comando 
~~~
cd nome_da_pasta
~~~  
, lembrando que o caminho pode ser composto, exemplo, 
~~~
cd /home/lincon/public_html
~~~   
Para autocompletar caminhos podemos utilizar a tecla **TAB** enquanto digitamos parte do nome do diretório. 

### Permissões e donos
**NUNCA, NUNCA FAÇA chmod 777 arquivo!**  

![Permissões](http://photos1.blogger.com/blogger/862/3613/1600/perm.png)  

As permissões criam cenários para que todos os serviços executem somente onde precisam. Se tratando de um servidor onde ele possui pastas publicas(como por exemplo pastas com arquivos de um site), as permissões impede que arquivos indesejados executem no servidor. 

Para mudar as permissões utilizamos o comando
~~~
chmod <permissoes> <arquivo>
~~~
, e para alterar o dono 
~~~
chown <usuario>.<grupo> <arquivo>
~~~ 
Para executar a troca de dono dos subdiretórios e arquivos utilize a opção recursiva: **-R**.

----
## Os fundamentais
### Leia a $%#! do manual!

O linux possui um manual para quase tudo, por isso se está em dúvida do que um comando faz ou esqueceu como usar um programa leia o manual! Para isso é só executar 
~~~
man <comando>
~~~ 
ou
~~~
man -k <comando>
~~~ 
para buscar algum comando no manual.

## systemctl

Como quase tudo que roda no servidor são serviços, ninguém melhor para cuidar disso do que um gerenciador de serviços, lhes apresento o bom e velho: systemctl  
Os comandos básicos são:  
~~~
systemctl start <servico>
systemctl restart <servico>
systemctl stop <servico>
~~~

## apt-get a life

Nem sempre o que precisamos já está instalado no servidor, logo precisamos fazer o trabalho sujo. Para cuidar dos pacotes instalados temos o [APT](https://pt.wikipedia.org/wiki/Advanced_Packaging_Tool), um gerenciador de pacotes que cuida de todos os softwares e suas dependências instalada a partir dele. A operação do apt é bem simples, são basicamente 3 comandos:
~~~
apt install novo-software
apt update
apt upgrade
~~~
### apt update

O update é utilizado para sincronizar a lista de urls dos repositórios, pode ser útil quando você precisa instalar algum aplicativo e acontece erro 404 ou quando precisa atualizar os softwares já instalados na máquina.

### apt upgrade

O upgrade é o comando que baixa e instala as atualizações dos software instalados no servidor, lembrando que é interessante executar o **apt update** anteriormente para evitar links quebrados.

### apt install

O apt install como sugere o nome baixa e instala algum software do repositório, utilizado somente quando precisamos instalar novos software disponiveis no repositório oficial(preferencialmente).

---
## Apache

> O servidor Apache (ou Servidor HTTP Apache, em inglês: Apache HTTP Server, ou simplesmente: Apache) é o servidor web livre mais utilizado do mundo. Foi criado em 1995 por Rob McCool, então funcionário do NCSA (National Center for Supercomputing Applications).

[Wikipédia](https://pt.wikipedia.org/wiki/Servidor_Apache)

Apache é um serviço que utilizamos para servir conteúdo para web, desde páginas estáticas até sistemas complexos em php. Ele funciona com vários módulos e alguns arquivos de configurações que serão mostrado nos tópicos a seguir.

### Virtual Hosts

Por padrão o apache vem configurado para servir um único site que se encontra no diretório **/var/www/html**, porém, as vezes queremos hospedar mais de um site em um mesmo servidor, para isso temos os hosts virtuais. Os hosts virtuais possui arquivos de configuração para cada um dos sites e esses arquivos deve ser colocados na pasta **/etc/apache2/sites-available/site.com.br.conf**. Um arquivo de configuração padrão pode ser encontrado na mesma pasta com o nome de **000-default.conf**, porém utilizaremos um template um pouco mais completo:
~~~
<VirtualHost *:80>
	ServerName level5jr.com.br
	ServerAlias www.level5jr.com.br
	ServerAdmin admin@level5jr.com.br
	DocumentRoot /var/www/level5jr.com.br/public_html

	ErrorLog ${APACHE_LOG_DIR}/level5.com.br_error.log
	CustomLog ${APACHE_LOG_DIR}/level5.com.br_access.log combined

	<Directory /var/www/level5jr.com.br/public_html/>
		Options FollowSymLinks
		AllowOverride All
		Require all granted
	</Directory>
</VirtualHost>
~~~

Esse template depois de preenchido e salvo estará pronto para ser ativado utilizando o comando:
~~~
a2ensite level5jr.com.br // Nome do site que você salvou no diretório sites-available/ .conf
~~~
e para desativar um site 
~~~
a2dissite level5jr.com.br
~~~
Por fim, reinicie o serviço do apache utilizando o gerenciador de serviços executando:
~~~
systemctl restart apache2
~~~	

