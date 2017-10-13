---
title: "Creación y uso de un par de claves SSH para máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure para mejorar la seguridad del proceso de autenticación."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: iainfou
ms.openlocfilehash: 6fcdcc96c7762e2362aebf909ef25f4a5ab62f99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure
Con un par de claves de shell seguro (SSH), puede crear máquinas virtuales en Azure que usen claves SSH para autenticación, lo que elimina la necesidad de usar contraseñas para iniciar sesión. En este artículo se muestra cómo generar y usar rápidamente un par de archivos de clave pública y privada en formato RSA versión 2 del protocolo SSH para máquinas virtuales Linux. Para pasos más detallados y ejemplos adicionales, consulte los [pasos detallados para crear pares de claves SSH y certificados](create-ssh-keys-detailed.md).

## <a name="create-an-ssh-key-pair"></a>Creación de un par de claves SSH
Use el comando `ssh-keygen` para crear los archivos de clave SSH pública y privada que se crean de forma predeterminada en el directorio `~/.ssh`. Puede especificar una ubicación diferente y una frase de contraseña adicional (una contraseña para acceder al archivo de clave privada) cuando se le solicite. Ejecute el siguiente comando desde un shell de Bash y responda a los mensajes con su propia información.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="use-the-ssh-key-pair"></a>Uso de un par de claves SSH
La clave pública que coloca en la máquina virtual Linux en Azure se almacena de forma predeterminada en `~/.ssh/id_rsa.pub`, a menos que cambiara la ubicación cuando la creó. Si usa la [CLI de Azure 2.0](/cli/azure) para crear la máquina virtual, especifique la ubicación de esta clave pública cuando use [az vm create](/cli/azure/vm#create) con la opción `--ssh-key-path`. Si copia y pega el contenido del archivo de clave pública para usarlo en Azure Portal o en una plantilla de Resource Manager, asegúrese de no copiar ningún espacio en blanco adicional. Por ejemplo, si utiliza OS X, puede canalizar el archivo de clave pública (de forma predeterminada, **~/.ssh/id_rsa.pub**) hacia **pbcopy** para copiar el contenido (hay otros programas de Linux que hacen lo mismo que `xclip`).

Si no está familiarizado con las claves públicas SSH, puede ver su clave pública ejecutando `cat` de la siguiente manera y reemplazando `~/.ssh/id_rsa.pub` por la ubicación de su propio archivo de clave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

Con la clave pública en la máquina virtual de Azure, SSH en la máquina virtual mediante la dirección IP o el nombre DNS de la máquina virtual (no olvide reemplazar `azureuser` y `myvm.westus.cloudapp.azure.com` que aparecen a continuación por el nombre de usuario y el nombre de dominio completo, o la dirección IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Si proporcionó una frase de contraseña cuando creó el par de claves, escríbala cuando se le solicite durante el proceso de inicio de sesión. (El servidor se agrega a la carpeta `~/.ssh/known_hosts` y no se le pedirá que se conecte de nuevo hasta que la clave pública de la máquina virtual de Azure cambie o se quite el nombre del servidor de `~/.ssh/known_hosts`).

## <a name="next-steps"></a>Pasos siguientes

Las máquinas virtuales creadas con claves SSH están configuradas de forma predeterminada con las contraseñas deshabilitadas, a fin de evitar los intentos de adivinarlas por fuerza bruta que resultan bastante más caros y más complejos. En este tema se describe cómo crear un par de claves SSH simple para uso rápido. Si necesita más ayuda para crear el par de claves SSH o requiere más certificados, consulte los [pasos detallados para crear pares de claves SSH y certificados](create-ssh-keys-detailed.md).

También puede crear máquinas virtuales que usen el par de claves SSH mediante Azure Portal, la CLI y las plantillas:

* [Creación de una máquina virtual Linux protegida mediante el Portal de Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux segura mediante la CLI de Azure 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux protegida mediante una plantilla de Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
