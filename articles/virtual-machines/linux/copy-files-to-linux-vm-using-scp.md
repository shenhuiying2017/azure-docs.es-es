---
title: "Traslado de archivos a una máquina virtual Linux o desde ella mediante SCP | Microsoft Docs"
description: "Traslado seguro de archivos a la máquina virtual Linux o desde ella mediante SCP y un par de claves de SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1935bde486d8f6f8e80a73447c4b0ea5d4671b45
ms.lasthandoff: 04/03/2017


---

# <a name="moving-files-to-and-from-a-linux-vm-using-scp"></a>Traslado de archivos a una máquina virtual de Linux o desde ella mediante SCP

Este artículo muestra cómo trasladar archivos desde su estación de trabajo hasta una máquina virtual Linux de Azure, o desde una máquina virtual Linux de Azure hasta la estación de trabajo, mediante Secure Copy (SCP).  Para obtener un ejemplo, estamos trasladando los archivos de configuración de Azure hasta una máquina virtual Linux y extrayendo un directorio de archivos de registro, ambos con claves SCP y SSH.   

En este artículo, los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);

- [archivos de clave SSH pública y privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandos rápidos

Copia de un archivo hacia arriba de la máquina virtual de Linux

```bash
scp file user@host:directory/targetfile
```

Copia de un archivo hacia abajo de la máquina virtual Linux

```bash
scp user@host:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

El traslado de archivos hacia delante y hacia atrás entre su estación de trabajo y una máquina virtual Linux, de forma rápida y segura, es una parte fundamental de la administración de la infraestructura de Azure.  Este artículo le guiará a través del uso de SCP, una herramienta creada sobre SSH e incluida en el shell de Bash predeterminado de Linux, Mac y Windows.

## <a name="ssh-key-pair-authentication"></a>Autenticación del par de claves de SSH

SCP usa SSH para el nivel de transporte.  Si usa SSH para el transporte, SSH administra la autenticación en el host de destino a la vez que traslada el archivo en un túnel cifrado proporcionado de forma predeterminada con SSH.  Para la autenticación de SSH, los nombres de usuario y contraseñas pueden utilizarse, pero se aconseja la autenticación de claves SSH públicas y privadas como práctica recomendada de seguridad. Una vez que SSH ha autenticado la conexión, SCP comienza el proceso de copia del archivo.  Con claves públicas y privadas de SSH y un `~/.ssh/config` configurado adecuadamente, la conexión de SCP puede establecerse sin utilizar un nombre de usuario y solo usando un nombre de servidor.  Si solo tiene una clave SSH, SCP la buscará en el directorio `~/.ssh/` y la usará de forma predeterminada para iniciar sesión en la máquina virtual.

Para obtener más información acerca de cómo configurar las claves públicas y privadas de SSH y `~/.ssh/config`, siga este artículo [Creación de claves SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP en un archivo para una máquina virtual Linux

Para el primer ejemplo, estamos copiando un archivo de credenciales de Azure hasta una máquina virtual Linux que se usa para implementar la automatización.  Dado que este archivo contiene las credenciales de la API de Azure, que incluyen información confidencial, la seguridad es importante y el túnel codificado que proporciona SSH protege el contenido del archivo.

```bash
scp ~/.azure/credentials myserver:/home/ahmet/.azure/credentials
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP de un directorio desde una máquina virtual Linux

En este ejemplo, estamos copiando un directorio lleno de archivos de registro de la máquina virtual Linux hacia abajo hasta la estación de trabajo.  Un archivo de registro puede o no puede contener datos confidenciales o secretos y el uso de SCP garantiza que se cifra el contenido de los archivos de registro.  El uso de SCP para transferir de forma segura los archivos es la manera más fácil de obtener el directorio de registro y archivos hacia abajo en la estación de trabajo mientras se mantiene la seguridad.

```bash
scp -r myserver:/home/ahmet/logs/ /tmp/.
```

La marca de la CLI de `-r` indica a SCP que copie de forma recursiva los archivos y directorios desde el punto del directorio enumerado en el comando.  Tenga también en cuenta que la sintaxis de línea de comandos es similar a un comando de copia de `cp`.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux de Azure con la extensión VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Deshabilitación de las contraseñas SSH en la máquina virtual con Linux mediante la configuración de SSHD](mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

