---
title: 'Pasos detallados: par de claves SSH para VM Linux en Azure | Microsoft Docs'
description: Obtenga información acerca de los pasos detallados para crear y administrar un par de claves SSH pública y privada para VM Linux en Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 827c80a70047fd0f1ad67e4f19cb2300e45b2c6b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Los pasos detallados: crear y administrar claves SSH para la autenticación en una VM Linux en Azure 
Con un par de claves de Secure Shell (SSH), puede crear una máquina virtual Linux en Azure que, de forma predeterminada, use claves SSH para la autenticación, lo que elimina la necesidad de contraseñas para iniciar sesión. Las VM creadas con Azure Portal, CLI de Azure, plantillas de Resource Manager u otras herramientas pueden incluir la clave pública SSH como parte de la implementación, lo que configura la autenticación de clave SSH para las conexiones SSH. 

En este artículo se proporciona información detallada y pasos para crear y administrar un par de archivos de claves RSA de SSH pública y privada para las conexiones de cliente SSH. Si busca comandos rápidos, consulte [Creación de un par de claves SSH pública y privada para VM Linux en Azure](mac-create-ssh-keys.md).

Para ver otras formas de generar y usar claves SSH en un equipo con Windows, consulte [Uso de SSH con Windows en Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Frase de contraseña de la clave privada
La clave privada SSH debe tener una frase de contraseña muy segura para protegerla. Esta frase de contraseña solo sirve para acceder al archivo de clave privada SSH y *no es* la contraseña de la cuenta de usuario. Cuando agrega una frase de contraseña a la clave SSH, esta cifra la clave privada mediante AES de 128 bits para que no se pueda usar sin la frase de contraseña que la descifra. Si un atacante robara la clave privada y esta no tuviera una frase de contraseña, podría usarla para iniciar sesión en los servidores que tuvieran instalada la clave pública correspondiente. Si una clave privada está protegida con una frase de contraseña, el atacante no podrá usarla, ya que la contraseña constituye un nivel adicional de seguridad en la infraestructura de Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Uso y ventajas de las claves SSH

Cuando se crea una VM de Azure mediante la especificación de la clave pública, Azure copia la clave pública (en el formato `.pub`) a la carpeta `~/.ssh/authorized_keys` en la VM. Las claves SSH en `~/.ssh/authorized_keys` se utilizan para presentar un desafío al cliente, que debe proporcionar la clave privada correspondiente en una conexión de inicio de sesión SSH. En una VM Linux de Azure que usa claves SSH para la autenticación, Azure configura el servidor SSHD para no permitir inicios de sesión con contraseña, solo claves SSH. Por lo tanto, mediante la creación de VM Linux de Azure con claves SSH, puede ayudar a proteger la implementación de la VM y ahorrarse el paso de configuración que es habitual después de la implementación para deshabilitar las contraseñas en el archivo `sshd_config`.

Si no quiere usar claves de SSH, puede configurar la VM Linux para que use la autenticación de contraseña. Si la máquina virtual no está expuesta a Internet, el uso de contraseñas puede ser suficiente. Aun así, es preciso que administre las contraseñas de cada VM Linux y que mantenga directivas y procedimientos seguros con respecto a las contraseñas, como la elección contraseñas con una longitud mínima y actualizaciones periódicas. El uso de claves SSH reduce la complejidad de la administración de credenciales individuales en varias VM.

## <a name="generate-keys-with-ssh-keygen"></a>Generar claves con ssh-keygen

Para crear las claves, `ssh-keygen` es un comando preferido, disponible con las utilidades de OpenSSH en Azure Cloud Shell, un host de macOS o de Linux, el [subsistema de Windows para Linux](https://docs.microsoft.com/windows/wsl/about) y otras herramientas. `ssh-keygen` realiza una serie de preguntas y, después, escribe una clave privada y una clave pública correspondiente. 

Las claves SSH se mantienen de forma predeterminada en el directorio `~/.ssh`.  Si no tiene un directorio `~/.ssh`, el comando `ssh-keygen` lo crea automáticamente con los permisos correctos.

### <a name="basic-example"></a>Ejemplo básico

El siguiente comando `ssh-keygen` genera archivos RSA de SSH de clave pública y privada de 2048 bits de forma predeterminada en el directorio `~/.ssh`. Si existe un par de claves SSH en la ubicación actual, esos archivos se sobrescribirán.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Ejemplo detallado
En el siguiente ejemplo, se muestran las opciones de comando adicionales para crear un par de claves RSA de SSH. Si existe un par de claves SSH en la ubicación actual, esos archivos se sobrescribirán. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Explicación del comando**

`ssh-keygen` = programa usado para crear las claves;

`-t rsa` = tipo de clave que se va a crear; en este caso, en el formato RSA

`-b 4096` = número de bits de la clave; en este caso, 4096

`-C "azureuser@myserver"` = comentario que se anexa al final del archivo de clave pública para identificarlo fácilmente. Normalmente, se usa una dirección de correo electrónico como comentario, pero utilice lo que funcione mejor para su infraestructura.

`-f ~/.ssh/mykeys/myprivatekey` = nombre del archivo de clave privada, si decide no usar el nombre predeterminado. Se genera un archivo de clave pública correspondiente anexo a `.pub` en el mismo directorio. El directorio debe existir.

`-N mypassphrase` = frase de contraseña adicional que se usa para acceder al archivo de clave privada. 

### <a name="example-of-ssh-keygen"></a>Ejemplo de ssh-keygen

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

#### <a name="saved-key-files"></a>Archivos de clave guardados

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

El nombre del par de claves en este artículo. Tener un par de claves denominado `id_rsa` es la opción predeterminada y algunas herramientas podrían esperar `id_rsa` como nombre del archivo de la clave privada, así que es buena idea tener uno. El directorio `~/.ssh/` es la ubicación predeterminada para los pares de claves SSH y el archivo de configuración de SSH. Si no se especifica con una ruta de acceso completa, `ssh-keygen` creará las claves en el directorio de trabajo actual, no en el predeterminado `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista del directorio `~/.ssh`

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Frase de contraseña de la clave

`Enter passphrase (empty for no passphrase):`

Es *muy* recomendable agregar una frase de contraseña a las claves privadas. Sin una frase de contraseña que proteja el archivo de claves, cualquiera que disponga del archivo puede usarlo para iniciar sesión en cualquier servidor que tenga la clave pública correspondiente. El uso de una frase de contraseña ofrece más protección en caso de que alguien obtenga acceso al archivo de clave privada, ya que le daría tiempo para cambiar las claves.

## <a name="generate-keys-automatically-during-deployment"></a>Generar claves automáticamente durante la implementación

Si usa la [CLI de Azure 2.0](/cli/azure) para crear la VM, de manera opcional, se pueden generar archivos de claves SSH pública y privada mediante la ejecución del comando [az vm create](/cli/azure/vm#az_vm_create) con la opción `--generate-ssh-keys`. Las claves se almacenan en el directorio ~/.ssh. Tenga en cuenta que esta opción de comando no sobrescribe las claves si ya existen en esa ubicación.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Proporcione la clave pública SSH al implementar una VM

Para crear una VM Linux que use claves SSH para la autenticación, proporcione la clave pública SSH al crear la VM mediante el portal de Azure, CLI, plantillas de Resource Manager u otros métodos. Al usar el portal, escriba propia la clave pública. Si usa la [CLI de Azure 2.0](/cli/azure) para crear la VM con una clave pública existente, especifique el valor o la ubicación de esta clave pública mediante la ejecución del comando [az vm create](/cli/azure/vm#az_vm_create) con la opción de `--ssh-key-value`. 

Si no está familiarizado con el formato de una clave pública SSH, puede ver su clave pública ejecutando `cat` de la siguiente manera y reemplazando `~/.ssh/id_rsa.pub` por la ubicación de su propio archivo de clave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

Devuelve una salida similar a la siguiente (que se muestra aquí):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Si copia y pega el contenido del archivo de clave pública en Azure Portal o en una plantilla de Resource Manager, asegúrese de no copiar ningún espacio en blanco adicional y de no introducir saltos de línea adicionales. Por ejemplo, si utiliza macOS, puede canalizar el archivo de clave pública (de forma predeterminada, `~/.ssh/id_rsa.pub`) hacia **pbcopy** para copiar el contenido (hay otros programas de Linux que hacen lo mismo, como **xclip**).

Si prefiere usar una clave pública que se encuentra en un formato de varias líneas, puede generar una clave con formato RFC4716 en un contenedor de pem de la clave pública que creó anteriormente.

Para crear una clave con formato RFC4716 a partir de una clave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH en la VM con un cliente SSH
Con la clave pública implementada en la VM de Azure y la clave privada en su sistema local, aplique SSH en la VM con la dirección IP o nombre DNS de la VM. Reemplace *azureuser* y *myvm.westus.cloudapp.azure.com* en el siguiente comando con el nombre de usuario administrador y el nombre de dominio completo (o dirección IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Si proporcionó una frase de contraseña cuando creó el par de claves, escríbala cuando se le solicite durante el proceso de inicio de sesión. (El servidor se agrega a la carpeta `~/.ssh/known_hosts` y no se le pedirá que se conecte de nuevo hasta que la clave pública de la máquina virtual de Azure cambie o se quite el nombre del servidor de `~/.ssh/known_hosts`).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Use ssh-agent para almacenar la frase de contraseña de clave privada

Para no tener que escribir la frase de contraseña del archivo de clave privada con cada inicio de sesión SSH, puede usar `ssh-agent` para almacenar en caché la frase de contraseña del archivo de clave privada. Si usa Mac, la aplicación Keychain de macOS almacena de forma segura las frases de contraseña de clave privada al invocar `ssh-agent`.

Compruebe y use `ssh-agent` y `ssh-add` para informar al sistema SSH acerca de los archivos de clave para que no sea necesario usar la frase de contraseña de forma interactiva.

```bash
eval "$(ssh-agent -s)"
```

Ahora, agregue la clave privada a `ssh-agent` mediante el comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

La frase de contraseña de clave privada está almacenada ahora en `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Use ssh-copy-id para copiar la clave en una VM existente
Si ya ha creado una VM, puede instalar la nueva clave SSH pública para la VM Linux con un comando similar al siguiente:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Creación y configuración de un archivo de configuración de SSH

Puede crear y configurar un archivo de configuración SSH (`~/.ssh/config`) para acelerar los inicios de sesión y optimizar el comportamiento del cliente SSH. 

En el siguiente ejemplo se muestra una configuración simple que puede utilizar para iniciar sesión rápidamente como un usuario en una VM específica mediante la clave privada de SSH de forma predeterminada. 

### <a name="create-the-file"></a>Creación del archivo

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite el archivo para agregar la nueva configuración de SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Configuración de ejemplo

Agregue valores de configuración adecuados para la VM host.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Puede agregar configuraciones para hosts adicionales para permitir que cada uno use su propio par de claves dedicado. Vea [archivo de configuración SSH](https://www.ssh.com/ssh/config/) para obtener más opciones de configuración avanzada.

Ahora que tiene un par de claves SSH y un archivo de configuración de SSH configurado, puede iniciar sesión en la máquina virtual Linux de forma rápida y segura. Cuando ejecute el siguiente comando, SSH busca y carga los valores de configuración del bloque `Host myvm` en el archivo de configuración SSH.

```bash
ssh myvm
```

La primera vez que inicie sesión en un servidor mediante una clave SSH, el símbolo del sistema le pedirá la frase de contraseña para ese archivo de clave.

## <a name="next-steps"></a>Pasos siguientes

El siguiente paso es crear máquinas virtuales de Linux en Azure con la nueva clave pública SSH. Las máquinas virtuales de Azure que se crean con una clave pública SSH como inicio de sesión están mejor protegidas que las creadas con contraseñas, el método de inicio de sesión predeterminado.

* [Creación de una máquina virtuales Linux desde Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux con la CLI de Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una VM Linux mediante una plantilla de Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
