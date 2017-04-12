---
title: "Descripción detallada de la creación de un par de claves SSH para máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Descripción detallada sobre cómo crear un par de claves SSH pública y privada para máquinas virtuales Linux en Azure junto con los certificados específicos para distintos casos de uso."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 2/6/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f452e8b3802bef5dc61bff64a8ac9ec5bb2a5bd9
ms.lasthandoff: 04/03/2017


---

# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>Tutorial detallado sobre la creación de un par de claves SSH y certificados adicionales para una máquina virtual Linux en Azure
Con un par de claves SSH, puede crear máquinas virtuales en Azure que usen claves SSH para autenticación de forma predeterminada, lo que elimina la necesidad de usar contraseñas para iniciar sesión. Las contraseñas se pueden adivinar y dejan las máquinas virtuales expuestas a intentos constantes de adivinarlas. Las máquinas virtuales creadas con la CLI de Azure o con plantillas de Resource Manager pueden incluir la clave SSH pública como parte de la implementación, por lo que no es necesario deshabilitar el inicio de sesión con contraseña después de configurar la implementación. Este artículo proporciona pasos detallados y ejemplos adicionales de la generación de certificados como los que se usan en el portal clásico. Si desea crear y utilizar rápidamente un par de claves SSH, consulte [Creación de un par de claves SSH pública y privada para máquinas virtuales Linux](mac-create-ssh-keys.md).

## <a name="understanding-ssh-keys"></a>Descripción de las claves SSH

El uso de claves públicas y privadas SSH es la manera más fácil de iniciar sesión en los servidores de Linux. La [criptografía de clave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) ofrece una manera mucho más segura que las contraseñas de iniciar sesión en la máquina virtual Linux o BSD en Azure, que pueden ser forzadas con mucha más facilidad.

La clave pública se puede compartir con cualquiera. Sin embargo, la clave privada es propiedad exclusivamente suya (o de su infraestructura de seguridad local).  La clave privada SSH debe tener una [contraseña muy segura ](https://www.xkcd.com/936/) (origen:[xkcd.com](https://xkcd.com)) para protegerla.  Esta contraseña solo sirve para acceder al archivo de clave privada SSH y **no es** la contraseña de la cuenta de usuario.  Cuando agrega una contraseña a la clave SSH, esta cifra la clave privada mediante AES de 128 bits para que no se pueda usar sin la contraseña que la descifra.  Si un atacante robara la clave privada y esta no tuviera contraseña, podría usarla para iniciar sesión en los servidores que tuvieran instalada la clave pública correspondiente.  Si una clave privada está protegida con una contraseña, el atacante no podrá usarla, ya que la contraseña constituye un nivel adicional de seguridad en la infraestructura de Azure.

En este artículo se describe la creación de un par de archivos de clave pública y privada en formato RSA versión 2 del protocolo SSH (también denominadas claves "ssh-rsa"), que es recomendable para implementaciones con Azure Resource Manager. Las claves *ssh-rsa* se requieren en el [portal](https://portal.azure.com) tanto para las implementaciones clásicas como para las de Resource Manager.

## <a name="ssh-keys-use-and-benefits"></a>Uso y ventajas de las claves SSH

Azure necesita como mínimo claves públicas y privadas en formato RSA versión 2 del protocolo SSH de 2048 bits. El archivo de clave pública tiene el formato de contenedor `.pub`. (El portal clásico usa el formato de archivo `.pem`. Para crear las claves, use `ssh-keygen`, que realiza una serie de preguntas y después escribe una clave privada y una clave pública correspondiente. Cuando se crea una máquina virtual de Azure, Azure copia la clave pública en la carpeta `~/.ssh/authorized_keys` de la máquina virtual. Las claves SSH en `~/.ssh/authorized_keys` se utilizan para presentar un desafío al cliente, que debe proporcionar la clave privada correspondiente en una conexión de inicio de sesión SSH.  Cuando se crea una máquina virtual Linux de Azure que usa claves SSH para la autenticación, Azure configura el servidor SSHD para no permitir inicios de sesión con contraseña, solo las claves SSH.  Por lo tanto, mediante la creación de máquinas virtuales Linux de Azure con claves SSH, puede ayudar a proteger la implementación de la máquina virtual y a ahorrarse el paso de configuración que es habitual después de la implementación para deshabilitar las contraseñas en el archivo **sshd_config**.

## <a name="using-ssh-keygen"></a>Uso de ssh-keygen

Este comando crea un par de claves SSH protegidas con contraseña (cifradas) mediante RSA de 2048 bits e incluye un comentario para que se pueda identificar fácilmente.  

Las claves SSH se mantienen de forma predeterminada en el directorio `~/.ssh`.  Si no tiene un directorio `~/.ssh`, el comando `ssh-keygen` lo crea automáticamente con los permisos correctos.

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*Explicación del comando*

`ssh-keygen` = programa usado para crear las claves;

`-t rsa` = el tipo de clave que se va a crear que está en el formato RSA [wikipedia] (https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bits de la clave;

`-C "azureuser@myserver"` = comentario que se anexa al final del archivo de clave pública para identificarlo fácilmente.  Normalmente se usa un correo electrónico como comentario, pero puede utilizar lo que mejor le vaya para su infraestructura.

## <a name="classic-portal-and-x509-certs"></a>Portal clásico y certificados X.509

Si usa el [Portal de Azure clásico](https://manage.windowsazure.com/), este requiere archivos .pem de certificados X.509 para las claves SSH.  No se permite ningún otro tipo de claves públicas SSH, *deben* ser certificados X.509.

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

Si usa el modelo de implementación clásica (modo `asm` de la CLI), puede usar una clave pública SSH-RSA o una clave con formato RFC4716 en un contenedor pem.  La clave pública SSH-RSA es la que se creó anteriormente en este artículo mediante `ssh-keygen`.

Para crear una clave con formato RFC4716 a partir de una clave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Ejemplo de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
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

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

El nombre del par de claves en este artículo.  Tener un par de claves denominado **id_rsa** es el valor predeterminado y algunas herramientas podrían esperar **id_rsa** como nombre del archivo de la clave privada, así que es buena idea tener uno. El directorio `~/.ssh/` es la ubicación predeterminada para los pares de claves SSH y el archivo de configuración de SSH.  Si no se especifica con una ruta de acceso completa, `ssh-keygen` creará las claves en el directorio de trabajo actual, no en el predeterminado `~/.ssh`.

Una lista del directorio `~/.ssh` .

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

Contraseña de la clave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` hace referencia a una contraseña para el archivo de clave privada, como "una frase de contraseña".  Es *muy* recomendable agregar una contraseña a la clave privada. Sin una contraseña que proteja el archivo de claves, cualquiera que disponga del archivo puede usarlo para iniciar sesión en cualquier servidor que tenga la clave pública correspondiente. Agregar una contraseña (frase de contraseña) ofrece más protección en caso de que alguien obtenga acceso al archivo de clave privada, lo que le dará tiempo para cambiar las claves que se usan para autenticarse.

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

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Uso de `ssh-copy-id` para copiar la clave en una máquina virtual existente
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

* [Creación de una máquina virtual Linux protegida mediante una plantilla de Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux protegida mediante el Portal de Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Crear una máquina virtual de Linux segura mediante la CLI de Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

