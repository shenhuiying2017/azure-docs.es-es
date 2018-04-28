---
title: Creación y uso de un par de claves SSH para máquinas virtuales Linux en Azure | Microsoft Docs
description: Creación y uso de un par de claves pública-privada SSH para máquinas virtuales Linux en Azure para mejorar la seguridad del proceso de autenticación.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Pasos rápidos: Creación y uso de un par de claves pública-privada SSH para máquinas virtuales Linux en Azure
Con un par de claves de shell seguro (SSH), puede crear máquinas virtuales en Azure que usen claves SSH para autenticación, lo que elimina la necesidad de usar contraseñas para iniciar sesión. En este artículo se muestra cómo generar y usar rápidamente un par de archivos de claves pública-privada SSH para máquinas virtuales Linux. Puede seguir estos pasos con Azure Cloud Shell, un host macOS o Linux, el subsistema de Windows para Linux y otras herramientas compatibles con OpenSSH. 

Para obtener más datos y ejemplos, vea los [pasos detallados para crear pares de claves SSH](create-ssh-keys-detailed.md).

Para que ver otras formas de generar y usar claves SSH en un equipo Windows, consulte [Uso de SSH con Windows en Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Creación de un par de claves SSH
Use el comando `ssh-keygen` para crear archivos de claves pública y privada SSH que, de manera predeterminada, se crean en el directorio `~/.ssh`. Puede especificar una ubicación distinta y una frase de contraseña adicional (una contraseña para acceder al archivo de clave privada) cuando se le solicite. Si existe un par de claves SSH en la ubicación actual, esos archivos se sobrescribirán.

```bash
ssh-keygen -t rsa -b 2048
```

Si usa la [CLI de Azure 2.0](/cli/azure) para crear la máquina virtual, tiene la opción de generar archivos de claves pública y privada SSH mediante la ejecución del comando [az vm create](/cli/azure/vm#az_vm_create) con la opción `--generate-ssh-keys`. Las claves se almacenan en el directorio ~/.ssh. Tenga en cuenta que esta opción de comando no sobrescribe las claves si ya existen en esa ubicación.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Especificación de la clave pública SSH al implementar una máquina virtual
Para crear una VM con Linux que usa claves SSH para la autenticación, especifique la clave pública SSH al crear la máquina virtual mediante Azure Portal, la CLI, las plantillas de Resource Manager u otros métodos.

* [Creación de una máquina virtuales Linux desde Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux con la CLI de Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual de Linux mediante una plantilla de Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Si no está familiarizado con el formato de una clave pública SSH, puede ver su clave pública si ejecuta `cat` de la siguiente manera y reemplaza `~/.ssh/id_rsa.pub` por la ubicación de su propio archivo de clave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

Si copia y pega el contenido del archivo de clave pública para usarlo en Azure Portal o en una plantilla de Resource Manager, asegúrese de no copiar ningún espacio en blanco adicional. Por ejemplo, si utiliza macOS, puede canalizar el archivo de clave pública (de forma predeterminada, `~/.ssh/id_rsa.pub`) hacia **pbcopy** para copiar el contenido (hay otros programas de Linux que hacen lo mismo que **xclip**).

La clave pública que coloca en la máquina virtual Linux en Azure se almacena de forma predeterminada en `~/.ssh/id_rsa.pub`, a menos que cambiara la ubicación cuando creó las claves. Si usa la [CLI de Azure 2.0](/cli/azure) para crear la máquina virtual con una clave pública existente, especifique el valor o la ubicación de esta clave pública mediante la ejecución del comando [az vm create](/cli/azure/vm#az_vm_create) con la opción `--ssh-key-value`. 

## <a name="ssh-to-your-vm"></a>SSH en la máquina virtual
Con la clave pública implementada en la VM de Azure y la clave privada en el sistema local, ejecute SSH en la máquina virtual con la dirección IP o el nombre DNS de la máquina virtual. Reemplace *azureuser* y *myvm.westus.cloudapp.azure.com* en el siguiente comando por el nombre de usuario del administrador y el nombre de dominio completo (o la dirección IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Si proporcionó una frase de contraseña cuando creó el par de claves, escríbala cuando se le solicite durante el proceso de inicio de sesión. (El servidor se agrega a la carpeta `~/.ssh/known_hosts` y no se le pedirá que se conecte de nuevo hasta que la clave pública de la máquina virtual de Azure cambie o se quite el nombre del servidor de `~/.ssh/known_hosts`).

Las máquinas virtuales creadas con claves SSH están configuradas de forma predeterminada con las contraseñas deshabilitadas, a fin de evitar los intentos de adivinarlas por fuerza bruta que resultan bastante más caros y más complejos. 

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe cómo crear un par de claves SSH simple para uso rápido. 

* Si necesita más ayuda para usar el par de claves SSH, consulte los [pasos detallados para crear y administrar pares de claves SSH](create-ssh-keys-detailed.md).

* Si tiene problemas con las conexiones SSH a una máquina virtual de Azure, consulte [Solución de problemas de conexiones SSH a una máquina virtual Linux de Azure](troubleshoot-ssh-connection.md).


