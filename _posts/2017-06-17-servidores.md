# Servidores
###### por Lincon Dias.
> Em informática, um servidor é um software ou computador, com sistema de computação centralizada que fornece serviços a uma rede de computadores, chamada de cliente.

###### [Wikipedia](https://pt.wikipedia.org/wiki/VirtualBox)

---

## Preparando a o ambiente

### Instalando o VirtualBox
> VirtualBox é um software de virtualização que visa criar ambientes para instalação de sistemas distintos. Ele permite a instalação e utilização de um sistema operacional dentro de outro, assim como seus respectivos softwares, como dois ou mais computadores independentes, mas compartilhando fisicamente o mesmo hardware.

###### [Wikipedia](https://pt.wikipedia.org/wiki/VirtualBox)


Vamos utilizar o virtualbox para simular os nossos serviços rodando em um servidor, para isso baixe o versão para o seu sistema nesse
[link](https://www.virtualbox.org/wiki/Downloads).

### Instalando o Ubuntu
O Ubuntu fornece uma distro focada em servidores que pode ser baixada [aqui](https://www.ubuntu.com/download/server).
Depois de baixar é preciso criar uma nova máquina virtual no VBox e adicionar a imagem (.img) na controladora de disco e seguir as intruções para a instalação no disco rígido virtual.

----
## Conhecendo um pouco das ferramentas

### SSH, a telinha preta
> Secure Shell (SSH) é um protocolo de rede criptográfico para operação de serviços de rede de forma segura sobre uma rede insegura.[1] A **melhor aplicação** de exemplo conhecida é para **login remoto a sistemas de computadores** pelos usuários.

###### [Wikipedia](https://pt.wikipedia.org/wiki/Secure_Shell)

O SSH permite o acesso remoto a shell do servidor e através dela instalamos novos serviços, atualizamos e damos manutenção na máquina. Ele é a nossa principal ferramenta.

Para conectar no ssh do nosso servidor podemos executar o comando
```
	ssh -p <porta> <usuario>@<dominio>
```. 
Exemplo,
```
	ssh -p 22 lincon@level5jr.com.br
```
Lembrando que **não** é necessario acrescentar o __-p__ se a porta for a **padrão(22)**, mas como somos pessoas sensatas, não utilizamos a porta padrão por questões de segurança.

### Vim, o editor para hipsters

O Vim(Vi iMproved) é um **editor de textos** que **não** requer modo gráfico, perfeito para utilizar em um sessão **SSH**. Levando em conta que a maioria dos serviços rodando em servidores utilizam **arquivos de configuração**, o vim é uma importante ferramenta que é utilizada junto ao SSH para configurar e subir serviços no servidor.

Para abrir um arquivo com o vim utilizamos o comando ```vim arquivo```.  

Os homens sem fé costumam utilizar o **nano**, que também é uma opção.

----
## Navegando pelo sistema
### Diretórios e arquivos

Um dos comandos mais básicos que temos é o ```ls```, que lista todos os arquivos e diretórios do caminho atual(que pode ser checado com ```pwd```), porém, para visualizar os arquivos de um forma mais interessante utilizamos os argumentos __-lah__ que lista os arquivos em uma forma de lista, além de mostar o tamanho de uma forma visível, as permissões e o dono do arquivo(algo muito importante para os serviços funcionarem de forma correta) e também os arquivos ocultos(que começam com .) Então o comando fica: 
```
	ls -lah
```  
Para navegação dentro dos diretórios do servidor utilizamos o comando ```cd nome_da_pasta```, lembrando que o caminho pode ser composto, exemplo, ```cd /home/lincon/public_html```. Para autocompletar caminhos podemos utilizar a tecla **TAB** enquanto digitamos parte do nome do diretório. 

### Pemissões e donos
**NUNCA, NUNCA FAÇA ```chmod 777 arquivo```!**  

![Permissões](http://photos1.blogger.com/blogger/862/3613/1600/perm.png)  

As permissões criam cenários para que todos os serviços executem somente onde precisam. Se tratando de um servidor onde ele possue pastas publicas(como por exemplo pastas com arquivos de um site), as permissões impede que arquivos indesejados executem no servidor. 

Para mudar as permissões utilizamos o comando ```chmod <permissoes> <arquivo>```, e para alterar o dono ```chown <usuario>.<grupo> <arquivo>``` 

----
## Os fundamentais
### Leia a *$#! do manual!

O linux possui um manual para quase tudo! Tá em dúvida do que um comando faz? Ou esqueceu como usar um programa? Leia o manual! Para isso é só executar ```man <comando>``` ou man ```man -k <comando>``` para buscar algum comando no manual.

## systemctl

Como quase tudo que roda no servidor são serviços, ninguém melhor para cuidar disso do que um gerenciador de serviços, lhes apresento o bom e velho: systemctl  
Os comandos básicos são:  
```
	systemctl start <servico>
	systemctl restart <servico>
	systemctl stop <servico>
```

## aptitude

Nem sempre o que precisamos já está instalado no servidor, logo precisamos fazer o trabalho sujo
