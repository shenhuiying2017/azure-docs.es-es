---
title: "Creación de un par de claves SSH para máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Cree de forma segura un par de claves SSH pública y privada para máquinas virtuales Linux."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/6/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: e5f93bab46620e06e56950ba7b3686b15f789a9d
ms.openlocfilehash: 1ee0368b75e4ef2fc759251db32c5aed5c1a168d


---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Creación de un par de claves SSH pública y privada para máquinas virtuales Linux

En este artículo se muestra cómo generar un par de claves SSH pública y privada para usar con máquinas virtuales Linux.  Con un par de claves SSH, puede crear máquinas virtuales en Azure que usen claves SSH para autenticación de forma predeterminada, lo que elimina la necesidad de usar contraseñas para iniciar sesión.  Las contraseñas se pueden adivinar y dejan las máquinas virtuales expuestas a intentos constantes de adivinarlas. Las máquinas virtuales creadas con plantillas de Azure o la `azure-cli` pueden incluir la clave SSH pública como parte de la implementación, por lo que no es necesario deshabilitar el inicio de sesión con contraseña después de configurar la implementación.

## <a name="quick-commands"></a>Comandos rápidos

Ejecute los siguientes comandos desde un shell de Bash y reemplace los ejemplos por sus propios valores.

Las claves SSH se mantienen de forma predeterminada en el directorio `~/.ssh`.  Si no tiene un directorio `~/.ssh`, el comando `ssh-keygen` lo crea automáticamente con los permisos correctos.  El argumento `-N` especifica la contraseña para cifrar la clave SSH privada y *no* es la contraseña de usuario.

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N mypassword
```

Agregue la clave recién creada al `ssh-agent`:

```bash
ssh-add ~/.ssh/id_rsa
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

El uso de claves públicas y privadas SSH es la manera más fácil de iniciar sesión en los servidores de Linux. La [criptografía de clave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) ofrece una manera mucho más segura que las contraseñas de iniciar sesión en la máquina virtual Linux o BSD en Azure, que pueden ser forzadas con mucha más facilidad.

La clave pública se puede compartir con cualquiera. Sin embargo, la clave privada es propiedad exclusivamente suya (o de su infraestructura de seguridad local).  La clave privada SSH debe tener una [contraseña muy segura ](https://www.xkcd.com/936/) (origen:[xkcd.com](https://xkcd.com)) para protegerla.  Esta contraseña solo sirve para acceder a la clave privada SSH y **no es** la contraseña de la cuenta de usuario.  Cuando agrega una contraseña a la clave SSH, esta cifra la clave privada mediante AES de 128 bits para que no se pueda usar sin la contraseña que la descifra.  Si un atacante robara la clave privada y esta no tuviera contraseña, podría usarla para iniciar sesión en los servidores que tuvieran instalada la clave pública correspondiente.  Si una clave privada está protegida con una contraseña, el atacante no podrá usarla, ya que la contraseña constituye un nivel adicional de seguridad en la infraestructura de Azure.

En este artículo se crean archivos de clave con el formato *ssh-rsa* , que son los recomendados para las implementaciones en Resource Manager.  Las claves *ssh-rsa* se requieren en el [portal](https://portal.azure.com) tanto para las implementaciones clásicas como para las de Resource Manager.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>Deshabilitar contraseñas SSH mediante claves SSH

Azure requiere al menos claves públicas y privadas de 2048 bits con el formato ssh-rsa. Para crear las claves, use `ssh-keygen`, que realiza una serie de preguntas y después escribe una clave privada y una clave pública correspondiente. Cuando se crea una máquina virtual de Azure, la clave pública se copia en `~/.ssh/authorized_keys`.  Las claves SSH en `~/.ssh/authorized_keys` se utilizan para presentar un desafío al cliente, que debe proporcionar la clave privada correspondiente en una conexión de inicio de sesión SSH.  Cuando se crea una máquina virtual Linux de Azure que usa claves SSH para la autenticación, Azure configura el servidor SSHD para no permitir inicios de sesión con contraseña, solo las claves SSH.  Por lo tanto, mediante la creación de máquinas virtuales Linux de Azure con claves SSH, puede ayudar a proteger la implementación de la máquina virtual y a ahorrarse el paso de configuración que es habitual después de la implementación para deshabilitar las contraseñas en el archivo de configuración sshd_config.

## <a name="using-ssh-keygen"></a>Uso de ssh-keygen

Este comando crea un par de claves SSH protegidas con contraseña (cifradas) mediante RSA de 2048 bits e incluye un comentario para que se pueda identificar fácilmente.  

Las claves SSH se mantienen de forma predeterminada en el directorio `~/.ssh`.  Si no tiene un directorio `~/.ssh`, el comando `ssh-keygen` lo crea automáticamente con los permisos correctos.

```bash
ssh-keygen \
-t rsa \
-b 2048 \
-C "ahmet@myserver" \
-f ~/.ssh/id_rsa \
-N mypassword
```

*Explicación del comando*

`ssh-keygen` = programa usado para crear las claves;

`-t rsa` = el tipo de clave que se va a crear que está en el formato RSA [wikipedia] (https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bits de la clave;

`-C "myusername@myserver"` = comentario que se anexa al final del archivo de clave pública para identificarlo fácilmente.  Normalmente se usa un correo electrónico como comentario, pero puede utilizar lo que mejor le vaya para su infraestructura.

## <a name="classic-portal-and-x509-certs"></a>Portal clásico y certificados X.509

Si usa el [Portal de Azure clásico](https://manage.windowsazure.com/), este requiere certificados X.509 para las claves SSH.  No se permite ningún otro tipo de claves públicas SSH, *deben* ser certificados X.509.

Para crear un certificado X.509 a partir de la clave privada SSH-RSA existente:

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>Implementación clásica mediante `asm`

Si usa el modelo de implementación clásica (CLI de administración de servicios de Azure `asm`), puede usar una clave pública SSH-RSA o una clave con formato RFC4716 en un contenedor de pem.  La clave pública SSH-RSA es la que se creó anteriormente en este artículo mediante `ssh-keygen`.

Para crear una clave con formato RFC4716 a partir de una clave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Ejemplo de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

El nombre del par de claves en este artículo.  Tener un par de claves denominado **id_rsa** es el valor predeterminado y algunas herramientas podrían esperar **id_rsa** como nombre del archivo de la clave privada, así que es buena idea tener uno. El directorio `~/.ssh/` es la ubicación predeterminada para los pares de claves SSH y el archivo de configuración de SSH.  Si no se especifica con una ruta de acceso completa, `ssh-keygen` creará las claves en el directorio de trabajo actual, no en el predeterminado `~/.ssh`.

Una lista del directorio `~/.ssh` .

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

Contraseña de la clave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` se refiere a una contraseña como "frase de contraseña".  Es *muy* recomendable agregar una contraseña a los pares de clave. Sin una contraseña que los proteja, cualquiera que disponga del archivo de clave privada puede usarlo para iniciar sesión en cualquier servidor que tenga la clave pública correspondiente. El uso de una contraseña ofrece más protección en caso de que alguien obtenga acceso al archivo de clave privada, lo que le dará tiempo para cambiar las claves que se usan para autenticarse.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Uso de ssh-agent para almacenar la contraseña de clave privada

Para no tener que escribir la contraseña del archivo de clave privada con cada inicio de sesión SSH, puede usar `ssh-agent` para almacenar en caché la contraseña del archivo de clave privada. Si usa Mac, Llaveros en OSX almacena de forma segura las contraseñas de clave privada cuando invoque `ssh-agent`.

Compruebe y use ssh-agent y ssh-add para informar al sistema SSH acerca de los archivos de clave para que no sea necesario usar la frase de contraseña de forma interactiva.

```bash
eval "$(ssh-agent -s)"
```

Ahora, agregue la clave privada a `ssh-agent` mediante el comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

La contraseña de clave privada está almacenada ahora en `ssh-agent`.

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>Uso de `ssh-copy-id` para instalar la nueva clave
Si ya ha creado una máquina virtual, puede instalar la nueva clave SSH pública para la máquina virtual Linux con:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Creación y configuración de un archivo de configuración de SSH

Es procedimiento recomendado crear y configurar un archivo `~/.ssh/config` para acelerar los inicios de sesión y optimizar el comportamiento del cliente SSH.

En el ejemplo siguiente se muestra una configuración estándar.

### <a name="create-the-file"></a>Creación del archivo

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite el archivo para agregar la nueva configuración de SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Archivo `~/.ssh/config` de ejemplo:

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

### <a name="config-file-explained"></a>Explicación del archivo de configuración

`Host` = nombre del host al que se llama en el terminal.  `ssh fedora22` indica a `SSH` que use los valores del bloque de configuración con la etiqueta `Host fedora22`. NOTA: el host puede ser cualquier etiqueta que resulte lógica para su uso y no representa el nombre de host real de ningún servidor.

`Hostname 102.160.203.241` = dirección IP o nombre DNS del servidor al que se está accediendo.

`User ahmet` = cuenta de usuario remoto que se usará al iniciar sesión en el servidor.

`PubKeyAuthentication yes` = indica a SSH que desea usar una clave SSH para iniciar sesión.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = clave privada SSH y clave pública correspondiente que se usan para la autenticación.

## <a name="ssh-into-linux-without-a-password"></a>SSH en Linux sin contraseña

Ahora que tiene un par de claves SSH y un archivo de configuración de SSH configurado, puede iniciar sesión en la máquina virtual Linux de forma rápida y segura. La primera vez que inicie sesión en un servidor mediante una clave SSH, el símbolo del sistema le pedirá la frase de contraseña para ese archivo de clave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Explicación del comando

Cuando se ejecuta `ssh fedora22`, primero SSH busca y carga la configuración del bloque `Host fedora22` y, después, carga la configuración restante del último bloque, `Host *`.

## <a name="next-steps"></a>Pasos siguientes

El siguiente paso es crear máquinas virtuales de Linux en Azure con la nueva clave pública SSH.  Las máquinas virtuales de Azure que se crean con una clave pública SSH como inicio de sesión están mejor protegidas que las creadas con contraseñas, el método de inicio de sesión predeterminado.  Las máquinas virtuales de Azure creadas con claves SSH están configuradas de forma predeterminada con las contraseñas deshabilitadas, a fin de evitar los intentos de adivinarlas por fuerza bruta.

* [Creación de una máquina virtual Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux protegida mediante el Portal de Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Crear una máquina virtual de Linux segura mediante la CLI de Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->


