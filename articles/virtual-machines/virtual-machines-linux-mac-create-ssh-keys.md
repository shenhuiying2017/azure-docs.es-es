<properties
	pageTitle="Creación de claves SSH en Linux y Mac | Microsoft Azure"
	description="Generar y usar claves SSH en Linux y Mac para los modelos de implementación clásica en Azure y el Administrador de recursos."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/08/2016"
	ms.author="v-livech"/>

# Creación de claves SSH en Linux y Mac para máquinas virtuales de Linux en Azure

Con un par de claves SSH, puede crear máquinas virtuales en Azure que usen de forma predeterminada claves SSH para la autenticación, lo que elimina la necesidad de usar contraseñas para iniciar sesión. Las contraseñas se pueden adivinar y dejan las máquinas virtuales expuestas a intentos constantes de adivinarlas por fuerza bruta. Las máquinas virtuales creadas con plantillas de Azure o `azure-cli` pueden incluir la clave pública SSH como parte de la implementación, por lo que no es necesario configurarla después de esta. Si va a conectarse a una máquina virtual Linux desde Windows, consulte [este documento.](virtual-machines-linux-ssh-from-windows.md)

## Lista rápida de comandos

En los siguientes ejemplos de comandos, reemplace los valores entre &lt; y &gt; con los valores de su propio entorno.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Escriba el nombre del archivo que se guarda en el directorio `~/.ssh/`:

```bash
<azure_fedora_id_rsa>
```

Escriba la frase de contraseña para azure\_fedora\_id\_rsa:

```bash
<correct horse battery staple>
```

Agregue la clave recién creada a `ssh-agent` en Linux y Mac (también se agrega a Llaveros en OSX):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Copie la clave pública SSH en el servidor Linux:

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

Pruebe el inicio de sesión mediante claves en lugar de contraseña:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## Introducción

El uso de claves públicas y privadas SSH es la manera más fácil de iniciar sesión en los servidores de Linux. La [criptografía de clave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) ofrece una manera mucho más segura que las contraseñas de iniciar sesión en la máquina virtual Linux o BSD en Azure, que pueden ser forzadas con mucha más facilidad. La clave pública se puede compartir con cualquiera. Sin embargo, la clave privada es propiedad exclusivamente suya (o de su infraestructura de seguridad local). La clave privada SSH debe tener una [contraseña muy segura ](https://www.xkcd.com/936/) (origen:[xkcd.com](https://xkcd.com)) para protegerla. Esta contraseña solo sirve para acceder a la clave privada SSH y **no es** la contraseña de la cuenta de usuario. Cuando agrega una contraseña a la clave SSH, cifra la clave privada para que no se pueda usar sin la contraseña que la desbloquea. Si un atacante robara la clave privada y esta no tuviera contraseña, podría usarla para iniciar sesión en los servidores que tuvieran instalada la clave pública correspondiente. Si una clave privada está protegida con una contraseña, el atacante no podrá usarla, ya que la contraseña constituye un nivel adicional de seguridad en la infraestructura de Azure.



En este artículo se crean archivos de clave con el formato *ssh-rsa*, que son los recomendados para las implementaciones en Resource Manager. Las claves *ssh-rsa* se requieren en el [Portal](https://portal.azure.com) tanto para las implementaciones clásicas como para las de Resource Manager.


## Creación de las claves SSH

Azure requiere al menos claves públicas y privadas de 2048 bits con el formato ssh-rsa. Para crear las claves, use `ssh-keygen`, que realiza una serie de preguntas y después escribe una clave privada y una clave pública correspondiente. Cuando se crea una máquina virtual de Azure, la clave pública se copia en `~/.ssh/authorized_keys`. Las claves SSH en `~/.ssh/authorized_keys` se utilizan para presentar un desafío al cliente, que debe proporcionar la clave privada correspondiente en una conexión de inicio de sesión SSH.


## Uso de ssh-keygen

Este comando crea un par de claves SSH protegidas con contraseña (cifradas) mediante RSA de 2048 bits e incluye un comentario para que se pueda identificar fácilmente.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_Explicación del comando_

`ssh-keygen` = programa usado para crear las claves;

`-t rsa` = tipo de clave que se va a crear, que es el [formato RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem);

`-b 2048` = bits de la clave;

`-C "ahmet@fedoraVMAzure"` = comentario que se anexa al final del archivo de clave pública para identificarlo fácilmente. Normalmente se usa un correo electrónico como comentario, pero puede utilizar lo que mejor le vaya para su infraestructura.

### Uso de claves PEM

Si usa el modelo de implementación clásica (Portal de Azure clásico o la CLI de administración de servicios de Azure `asm`), es posible que tenga que usar claves SSH con formato PEM para acceder a las máquinas virtuales Linux. Aquí se muestra cómo crear una clave PEM a partir de una clave SSH pública y un certificado x509 existentes.

Para crear una clave con formato PEM a partir de una clave pública SSH existente:

```bash
ssh-keygen -f id_rsa.pub -m 'PEM' -e > id_rsa.pem
```

## Tutorial de ssh-keygen

Cada paso se explica en detalle. Comience ejecutando `ssh-keygen`.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Archivos de clave guardados:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

El nombre del par de claves en este artículo. Tener un par de claves denominado **id\_rsa** es el valor predeterminado y algunas herramientas podrían esperar **id\_rsa** como nombre del archivo de la clave privada, así que es buena idea tener uno. El directorio `~/.ssh/` es la ubicación predeterminada para los pares de claves SSH y el archivo de configuración de SSH.

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
Una lista del directorio `~/.ssh`. `ssh-keygen` crea el directorio `~/.ssh` si no existe y también establece los modos de propiedad y de archivo correctos.

Contraseña de la clave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` se refiere a una contraseña como "frase de contraseña". Es *muy* recomendable agregar una contraseña a los pares de clave. Sin una contraseña que los proteja, cualquiera que disponga del archivo de clave privada puede usarlo para iniciar sesión en cualquier servidor que tenga la clave pública correspondiente. Agregar una contraseña ofrece más protección en caso de que alguien obtenga acceso al archivo de clave privada, lo que le dará tiempo para cambiar las claves que se usan para autenticarse.

## Uso de ssh-agent para almacenar la contraseña de clave privada

Para no tener que escribir la contraseña del archivo de clave privada con cada inicio de sesión SSH, puede usar `ssh-agent` para almacenar en caché la contraseña del archivo de clave privada. Si usa Mac, Llaveros en OSX almacena de forma segura las contraseñas de clave privada cuando invoque `ssh-agent`.

En primer lugar, compruebe que `ssh-agent` se esté ejecutando.

```bash
eval "$(ssh-agent -s)"
```

Ahora, agregue la clave privada a `ssh-agent` mediante el comando `ssh-add`.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

La contraseña de clave privada está almacenada ahora en `ssh-agent`.

## Creación y configuración de un archivo de configuración de SSH

Es procedimiento recomendado crear y configurar un archivo `~/.ssh/config` para acelerar los inicios de sesión y optimizar el comportamiento del cliente SSH.

En el ejemplo siguiente se muestra una configuración estándar.

### Creación del archivo

```bash
touch ~/.ssh/config
```

### Edite el archivo para agregar la nueva configuración de SSH:

```bash
vim ~/.ssh/config
```

### Archivo `~/.ssh/config` de ejemplo:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Esta configuración de SSH ofrece secciones para cada servidor para que cada uno tenga su propio par de claves dedicado. La configuración predeterminada (`Host *`) es para los hosts que no coincidan con ninguno de los hosts específicos más arriba en el archivo de configuración.


### Explicación del archivo de configuración

`Host` = nombre del host al que se llama en el terminal. `ssh fedora22` indica a `SSH` que use los valores del bloque de configuración con la etiqueta `Host fedora22`. NOTA: Puede ser cualquier etiqueta lógica para su uso y no representa el nombre de host real de ningún servidor.

`Hostname 102.160.203.241` = dirección IP o nombre DNS del servidor al que se está accediendo.

`User git` = cuenta de usuario remoto que se va a utilizar.

`PubKeyAuthentication yes` = indica a SSH que desea usar una clave SSH para iniciar sesión.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = clave privada SSH y clave pública correspondiente que se usan para la autenticación.


## SSH en Linux sin contraseña

Ahora que tiene un par de claves SSH y un archivo de configuración de SSH configurado, puede iniciar sesión en la máquina virtual Linux de forma rápida y segura. La primera vez que inicie sesión en un servidor mediante una clave SSH, el símbolo del sistema le pedirá la frase de contraseña para ese archivo de clave.

```bash
ssh fedora22
```

### Explicación del comando

Cuando se ejecuta `ssh fedora22`, primero SSH busca y carga la configuración del bloque `Host fedora22` y, después, carga la configuración restante del último bloque, `Host *`.

## Pasos siguientes

El siguiente paso es crear máquinas virtuales de Linux en Azure con la nueva clave pública SSH. Las máquinas virtuales de Azure que se crean con una clave pública SSH como inicio de sesión están mejor protegidas que las creadas con contraseñas, el método de inicio de sesión predeterminado. Las máquinas virtuales de Azure creadas con claves SSH están configuradas de forma predeterminada con las contraseñas deshabilitadas, a fin de evitar los intentos de adivinarlas por fuerza bruta.

- [Creación de una máquina virtual Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creación de una máquina virtual Linux protegida mediante el Portal de Azure](virtual-machines-linux-quick-create-portal.md)
- [Crear una máquina virtual de Linux segura mediante la CLI de Azure](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0831_2016-->