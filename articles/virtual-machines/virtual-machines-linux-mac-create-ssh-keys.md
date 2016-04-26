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
	ms.date="04/12/2016"
	ms.author="v-livech"/>

# Creación de claves SSH en Linux y Mac para máquinas virtuales de Linux en Azure

Para crear una clave pública y privada SSH protegida con contraseña, necesita un terminal abierto en su estación de trabajo. Cuando tenga las claves SSH, puede crear nuevas máquinas virtuales con esas claves de forma predeterminada o agregar la clave pública a máquinas virtuales existentes mediante la CLI de Azure y plantillas de Azure.

## Lista rápida de comandos

En los siguientes ejemplos de comandos, reemplace los valores entre &lt; y &gt; con los valores de su propio entorno.

```bash
[ahmet@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
<azure_fedora_id_rsa>

#Enter passphrase for azure_fedora_id_rsa:
<correct horse battery staple>

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[ahmet@fedora ~]$ eval "$(ssh-agent -s)"
[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[ahmet@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[ahmet@fedora ~]$ ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
[ahmet@fedora ~]$

```

## Introducción

El uso de claves públicas y privadas SSH es la forma más fácil de iniciar sesión en los servidores Linux pero, además, la [criptografía de clave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) también proporciona una manera mucho más segura de iniciar sesión en la máquina virtual de Linux o de BSD en Azure que las contraseñas, que pueden ser forzadas más fácilmente. La clave pública se puede compartir con cualquiera. Sin embargo, la clave privada es propiedad exclusivamente suya (o de su infraestructura de seguridad local). La clave privada SSH creada tendrá una [contraseña segura](https://www.xkcd.com/936/) para protegerla y esta contraseña es solo para tener acceso a la clave privada SSH, **no** es la contraseña de la cuenta de usuario. Cualquiera que tenga una clave privada sin una contraseña puede tener acceso a cualquier servidor con la clave pública instalada. Sin la contraseña no se puede usar la clave privada.


En este artículo se crean archivos de clave con el formato *ssh-rsa*, como los que se recomiendan para las implementaciones en Resource Manager y se requieren en el [Portal](https://portal.azure.com) tanto para las implementaciones clásicas como para las de Resource Manager.


## Creación de las claves SSH

Azure requiere al menos claves públicas y privadas de 2048 bits con el formato ssh-rsa. Para crear el par, se usará `ssh-keygen`, que realiza una serie de preguntas y, a continuación, escribe una clave privada y una clave pública correspondiente. Cuando se crea la máquina virtual de Azure, se pasa el contenido de la clave pública, que se copia en la máquina virtual de Linux y se usa con la clave privada almacenada de forma segura y local para autenticarle al iniciar sesión.

### Uso de `ssh-keygen`

Este comando crea un par de claves SSH protegidas con contraseña mediante RSA de 2048 bits y se marcan como comentario para identificarlas fácilmente.

```
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

##### Explicación del comando

`ssh-keygen`= el programa usado para crear las claves.

`-t rsa`= tipo de clave para crear, que es el [formato RSA] (https://en.wikipedia.org/wiki/RSA_(cryptosystem)).

`-b 2048`= bits de la clave.

`-C "ahmet@fedoraVMAzure"`= un comentario que se anexa al final del archivo de clave pública para identificarlo fácilmente. Normalmente se usa un correo electrónico como comentario, pero puede utilizar lo que mejor le vaya para su infraestructura.

#### Tutorial de `ssh-keygen`

```bash
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
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

ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa` El nombre del par de claves en este artículo. El valor predeterminado es un par de claves llamado **id\_rsa** y algunas herramientas podrían esperar el nombre de archivo de clave privada **id\_rsa**, así que es buena idea tener uno. (`~/.ssh/` es la ubicación predeterminada típica de todos los pares de claves SSH y del archivo de configuración SSH).

`Enter passphrase (empty for no passphrase):` Es muy recomendable agregar a los pares de clave una contraseña (`ssh-keygen` llama a esto "frase de contraseña"). Sin una contraseña que proteja el par de claves, cualquiera con una copia del archivo de clave privada puede usarlo para iniciar sesión en cualquier servidor (sus servidores) que tenga la clave pública correspondiente. Por lo tanto, agregar una contraseña ofrece una protección mucho mayor en caso de que alguien obtenga acceso al archivo de clave privada, lo que le dará tiempo para cambiar las claves que se usan para autenticarlo.

`ahmet@fedora$ ls -al ~/.ssh` Muestra los nuevos pares de clave y sus permisos. `ssh-keygen` crea el directorio `~/.ssh` si no existe y también establece los modos de propiedad y de archivo correctos.

## Uso de ssh-agent para almacenar la contraseña de clave privada

Para evitar tener que escribir la contraseña del archivo de clave privada con cada inicio de sesión SSH, puede usar `ssh-agent` para almacenar en caché la contraseña del archivo de clave privada, lo que permite inicios de sesión rápidos y seguros a la máquina virtual de Linux. Si usa OSX, la cadena de llaves almacenará de forma segura las contraseñas de clave privada al invocar `ssh-agent`.

En primer lugar, compruebe que `ssh-agent` se está ejecutando

`[ahmet@fedora ~]$ eval "$(ssh-agent -s)"`

Ahora, agregue la clave privada a `ssh-agent` mediante el comando `ssh-add`; de nuevo en OSX se iniciará la cadena de llaves que almacenará las credenciales.

`[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa`

La contraseña de clave privada se almacena ahora, por lo que no tendrá que escribir la contraseña de clave con cada inicio de sesión SSH.

## Creación y configuración de un archivo de configuración de SSH

Aunque no es absolutamente necesario para empezar a trabajar con una máquina virtual de Linux, se recomienda crear y configurar un archivo `~/.ssh/config` para impedir el uso accidental de contraseñas para iniciar sesión en sus máquinas virtuales, automatizar el uso de pares de claves diferentes para las distintas máquinas virtuales de Azure y configurar otros programas como **git** para que usen también varios servidores de destino.

En el ejemplo siguiente se muestra una configuración estándar.

### Creación del archivo

```bash
ahmet@fedora$ touch ~/.ssh/config
```

### Edite el archivo para agregar la nueva configuración de SSH

```bash
ahmet@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/private_repo_azure_fedora_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Esta configuración de SSH ofrece secciones para cada servicio para que cada uno tenga su propio par de claves dedicado. La configuración predeterminada es para los hosts en los que haya iniciado sesión y que no se correspondan con ninguno de los grupos anteriores. La configuración de SSH también le permite tener dos inicios de sesión de [GitHub](https://github.com) diferentes, uno para el trabajo público y un segundo solo para los repositorios privados que podrían ser comunes en su trabajo.


##### Explicación del archivo de configuración

`Host` = nombre del host al que se llama en el terminal. `ssh fedora22` indica a `SSH` que use los valores del bloque de configuración con la etiqueta `Host fedora22`. NOTA: Puede ser cualquier etiqueta lógica para su uso y no representa el nombre de host real de ningún servidor.

`Hostname 102.160.203.241` = dirección IP o nombre DNS del servidor en el que está iniciando sesión. Se usa para enrutar al servidor y puede ser una dirección IP externa que se asigna a una dirección IP interna.

`User git` = cuenta de usuario remoto que se usará al iniciar sesión en la máquina virtual de Azure.

`PubKeyAuthentication yes` = indica a SSH que quiere usar una clave SSH para el inicio de sesión.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = indica a SSH qué par de claves se presenta al servidor para autenticar el inicio de sesión.


## SSH en una máquina virtual de Linux sin una contraseña

Ahora que tiene un par de claves SSH y un archivo de configuración SSH configurado, puede iniciar sesión en la máquina virtual de Linux de forma rápida y segura. La primera vez que inicie sesión en un servidor mediante una clave SSH, el símbolo del sistema le pedirá la frase de contraseña para ese archivo de claves.

`ahmet@fedora$ ssh fedora22`

##### Explicación del comando

Cuando `ahmet@fedora$ ssh fedora22` se ejecuta, SSH primero busca y carga la configuración del bloque `Host fedora22` y, después, carga la configuración restante del último bloque, `Host *`.

## Pasos siguientes

Ahora puede continuar y usar los archivos de clave SSH para:

- [Crear una máquina virtual de Linux segura mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Crear una máquina virtual de Linux segura mediante el Portal de Azure](virtual-machines-linux-quick-create-portal.md)
- [Crear una máquina virtual de Linux segura mediante la CLI de Azure](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0420_2016-->