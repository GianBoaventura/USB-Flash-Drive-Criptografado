# USB-Flash-Drive-Criptografado

Segue um passo-a-passo de como criei um *Flash Drive* USB (vulgo Pendrive) criptografado no OpenBSD para armazenar arquivos de forma segura.

Obs.: Esse pequeno tutorial não substitui a documentação oficial dos comandos aqui utilizados. *RTFM*. Sempre que for utilizar um comando que desconheça usa o *man*. Exemplo: # man dd

Usaremos todo o espaço disponível da unidade, portanto tenha certeza de que não haja nenhum arquivo importante nesse momento.

Encontrando a unidade *sd* do dispositivo: 

#### \# dmesg

Para esse exemplo usaremos ***sd2***

A partir daqui os comandos serão executados como ***root*** ou utilizando ***doas***.

É recomendável gravar dados aleatórios antes de qualquer procedimento: 
#### \# dd if=/dev/urandom of=/dev/rsd2c bs=1m

Particularmente, em seguida, eu faço uma "limpeza" na unidade:
#### \# dd if=/dev/zero of=/dev/rsd2c bs=1m

Agora estamos prontos para particionar a unidade. A opção ***i*** reinicializa a tabela de partição e a opção ***y*** assume *sim* para todas as perguntas:
#### \# fdisk -iy sd2

Criamos a partição "***a***" do tipo ***RAID***:
#### \# disklabel -E sd3
> *a*
> *[enter]*
> *[enter]*
> *RAID*
> *w q*

É hora de cria o volume criptografado. O parâmetro "***-c***" especifica o tipo de RAID do nosso volume que no caso é "***C***" (CRYPTO), criptografado, o "***-l sd2a***" cria a parte a ser utilizada e ***softraid0*** é o dispositivo:
#### \# bioctl -c C -l sd2a softraid0
digite a senha
redigite a senha
> *softraid0: CRYPTO volume attached as sd3*

Agora o seu volume já é o ***#sd3***

Seguimos a mesma idéia anterior e particionamos a unidade:
#### \# fdisk -iy sd3

Porém criando agora uma partição "***i***":
#### \# disklabel -E sd3

Criamos o sistema de arquivos:
#### \# newfs sd3i

Criamos uma pasta para a montagem:
#### \# mkdir -p /mnt/USBCriptografado

Montamos o dispositivo:
#### \# mount /dev/sd3i /mnt/USBCriptografado

A unidade está pronta para ser utilizada.

Para remover siga essa ordem:
Desmontar a unidade:
#### \# umount /mnt/USBCriptografado
Desmontar o dispositivo:
#### \# bioctl -d sd3

Para montar novamente:
#### \# bioctl -c C -l sd2a softraid0
#### \# mount /dev/sd3i /mnt/USBCriptografado

Lembre-se sempre de desmontar a unidade e o dispositivo.

...
