---
title: "Introducción a FreeBSD en Azure | Microsoft Docs"
description: "Aprenda a utilizar máquinas virtuales de FreeBSD en Azure"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 24410a07995d5ac813b2bf4cdeed320c72ce7e06
ms.openlocfilehash: 7845b552bd1360927eae414f57fefbd74ac0b7f7
ms.lasthandoff: 03/01/2017


---
# <a name="introduction-to-freebsd-on-azure"></a>Introducción a FreeBSD en Azure
Este tema proporciona una visión general de la ejecución de una máquina virtual de FreeBSD en Azure.

## <a name="overview"></a>Información general
FreeBSD para Microsoft Azure es un sistema operativo avanzado que se utiliza para servidores modernos, equipos de escritorio y plataformas insertadas.

Microsoft Corporation ofrece imágenes de FreeBSD en Azure con el [agente de invitado de VM de Azure](https://github.com/Azure/WALinuxAgent/) preconfigurado. Actualmente, Microsoft ofrece las siguientes versiones de FreeBSD como imágenes:

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE

El agente es responsable de la comunicación entre la VM de FreeBSD y el tejido de Azure para operaciones como el aprovisionamiento de la VM cuando se usa por primera vez (nombre de usuario, contraseña o clave SSH, nombre de host, etc.) y la habilitación de la funcionalidad para extensiones de VM selectivas.

En lo que respecta a futuras versiones de FreeBSD, la estrategia es mantenerse al día y que las últimas versiones estén disponibles al poco tiempo de que el equipo de ingeniería de lanzamientos de FreeBSD las publique.

## <a name="deploying-a-freebsd-virtual-machine"></a>Implementación de una máquina virtual de FreeBSD
La implementación de una máquina virtual de FreeBSD es un proceso sencillo cuando se usa una imagen de Azure Marketplace desde Azure Portal:

- [FreeBSD 10.3 en Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 en Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>Crear una máquina virtual de FreeBSD mediante la CLI de Azure 2.0 en FreeBSD
Primero necesita instalar la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) mediante el siguiente comando en una máquina de FreeBSD.

```bash 
    curl -L https://aka.ms/InstallAzureCli | bash
```

Si Bash no está instalado en su máquina de FreeBSD, ejecute el siguiente comando antes de la instalación. 

```
    sudo pkg install bash
```

Si Python no está instalado en su máquina de FreeBSD, ejecute los siguientes comandos antes de la instalación. 

```
    sudo pkg install python35
    cd /usr/local/bin 
    sudo rm /usr/local/bin/python 
    sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Durante la instalación, se le preguntará lo siguiente: `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Si su respuesta es `y` y escribe `/etc/rc.conf` como `a path to an rc file to update`, puede encontrarse el problema `ERROR: [Errno 13] Permission denied`. Para resolverlo, debe conceder el derecho de escritura al usuario actual en el archivo `etc/rc.conf`.

Ahora puede iniciar sesión en Azure y crear su máquina virtual de FreeBSD. A continuación se muestra un ejemplo para crear una máquina virtual de FreeBSD 11.0. También puede agregar el parámetro `--public-ip-address-dns-name` con un nombre DNS único globalmente para una IP pública recién creada. 

```azurecli
    az login 
    az group create -n myResourceGroup -l westus az vm create -n myFreeBSD11 -g myResourceGroup --image MicrosoftOSTC:FreeBSD:11.0:latest --admin-username azureuser --ssh-key-value /etc/ssh/ssh_host_rsa_key.pub 
```

Después, puede iniciar sesión en su máquina virtual de FreeBSD mediante la dirección IP que se imprime en la salida de la implementación anterior. 

```bash
    ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Extensiones de VM para FreeBSD
A continuación se muestran las extensiones de VM compatibles en FreeBSD.

### <a name="vmaccess"></a>VMAccess
La extensión [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) puede:

* Restablecer la contraseña del usuario de sudo original.
* Crear un nuevo usuario de sudo con la contraseña especificada.
* Establecer la clave pública del host con la clave dada.
* Restablecer la clave pública del host proporcionada durante el aprovisionamiento de VM si no se proporciona clave de host.
* Abrir el puerto (22) SSH y restaurar sshd_config si reset_ssh está establecido en true.
* Quitar el usuario existente.
* Comprobar discos.
* Reparar un disco agregado.

### <a name="customscript"></a>CustomScript
La extensión [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) puede:

* Si se proporciona, descargar los scripts desde Azure Storage o desde un almacenamiento público externo (por ejemplo, GitHub).
* Ejecutar el script de punto de entrada.
* Admitir comandos en línea.
* Convertir la nueva línea de estilo de Windows en scripts de Shell y Python automáticamente.
* Quitar la marca BOM en scripts de Shell y Python automáticamente.
* Proteger la información confidencial en CommandToExecute.

> [!NOTE]
> Por el momento, la máquina virtual de FreeBSD solo es compatible con la versión 1.x de CustomScript.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticación: nombres de usuario, contraseñas y claves SSH
Al crear una máquina virtual FreeBSD con el Azure Portal, debe proporcionar un nombre de usuario, una contraseña o una clave pública SSH.
Los nombres de usuario para implementar una máquina virtual de FreeBSD en Azure no deben coincidir con los nombres de cuentas del sistema (UID <&100;) ya presentes en la máquina virtual ("raíz", por ejemplo).
Actualmente, solo se admite la clave RSA SSH. Una clave SSH multilínea debe comenzar con `---- BEGIN SSH2 PUBLIC KEY ----` y terminar con `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Obtención de privilegios de superusuario
La cuenta de usuario especificada durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. Se instaló el paquete de sudo en la imagen de FreeBSD publicada.
Después de iniciar sesión con esta cuenta de usuario, puede ejecutar comandos como root con usando la sintaxis de comando.

```
    $ sudo <COMMAND>
```

También puede obtener un shell root con `sudo -s`.

## <a name="known-issues"></a>Problemas conocidos
La versión 2.2.2 de [Agente invitado de máquina virtual de Azure](https://github.com/Azure/WALinuxAgent/) tiene un [problema conocido] (https://github.com/Azure/WALinuxAgent/pull/517) que provoca un error de aprovisionamiento de la máquina virtual de FreeBSD en Azure. La corrección se incluirá en la versión 2.2.3 de [Agente invitado de máquina virtual de Azure](https://github.com/Azure/WALinuxAgent/) y posteriores. 

## <a name="next-steps"></a>Pasos siguientes
* Acuda a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) para crear una máquina virtual de FreeBSD.
* Si desea incorporar su propio FreeBSD a Azure, consulte [Creación y carga de un VHD de FreeBSD en Azure](./virtual-machines-linux-classic-freebsd-create-upload-vhd.md).

