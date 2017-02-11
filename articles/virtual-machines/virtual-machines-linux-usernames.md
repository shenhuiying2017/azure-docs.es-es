---
title: "Selección de nombres de usuario para Linux | Microsoft Docs"
description: "Aprenda a seleccionar nombres de usuario para una máquina virtual de Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 33b50c97-92f1-46c9-a623-e37f67459c5c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eaea0ce42537789278d3a0476b261e6a750dde2b


---
# <a name="selecting-user-names-for-linux-on-azure"></a>Selección de nombres de usuario para Linux en Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cuando aprovisiona una máquina virtual con Linux en Azure, debe especificar el nombre de un usuario no raíz que más adelante pueda usar para iniciar sesión en la máquina virtual. Puede elegir el nombre del nuevo usuario o, si el aprovisionamiento es a través del Portal de Azure clásico, puede aceptar el nombre predeterminado "azureuser".

En la mayoría de los casos, este usuario no existirá en la imagen base y se creará durante el proceso de aprovisionamiento. Si el usuario ya existe en la imagen de máquina virtual base, el agente Linux de Azure configura simplemente la contraseña (o la clave SSH) para ese usuario de acuerdo con la información especificada al crear la máquina virtual.

**Sin embargo**, Linux define un conjunto de nombres de usuario que no se deben usar al crear usuarios nuevos. El proceso de aprovisionamiento generará un **error** si trata de aprovisionar una máquina virtual Linux con un usuario del sistema existente, que se define como un usuario con UID 0-99. Un ejemplo típico es el usuario `root` , que tiene el UID de 0.

* Consulte también: [Base estándar de Linux - Intervalos de identificadores de usuarios](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

A continuación encontrará una lista de usuarios de sistema integrados comunes para CentOS y Ubuntu que se debe intentar no usar al aprovisionar una máquina virtual Linux en Azure. Esta lista es solo un ejemplo, consulte la documentación de su distribución para asegurarse de que el nombre de usuario que elija no entra en conflicto con un usuario existente del sistema.

## <a name="centos"></a>CentOS
* abrt
* adm
* audio
* bin
* cdrom
* cgred
* daemon
* dbus
* dialout
* dip
* disk
* floppy
* ftp
* ftp
* games
* gopher
* haldaemon
* halt
* kmem
* lock
* lp
* mail
* man
* mem
* nfsnobody
* nobody
* ntp
* operator
* oprofile
* postdrop
* postfix
* qpidd
* root
* rpc
* rpcuser
* saslauth
* shutdown
* slocate
* sshd
* stapdev
* stapusr
* sync
* sys
* tape
* test
* tcpdump
* tty
* users
* utempter
* utmp
* uucp
* vcsa
* video
* wheel

## <a name="ubuntu"></a>Ubuntu
* adm
* admin
* audio
* backup
* bin
* cdrom
* crontab
* daemon
* dialout
* dip
* disk
* fax
* floppy
* fuse
* games
* gnats
* irc
* kmem
* landscape
* libuuid
* list
* lp
* mail
* man
* messagebus
* mlocate
* netdev
* news
* nobody
* nogroup
* operator
* plugdev
* proxy
* root
* sasl
* shadow
* src
* ssh
* sshd
* staff
* sudo
* sync
* sys
* syslog
* tape
* tty
* users
* utmp
* uucp
* video
* voice
* whoopsie
* www-data




<!--HONumber=Nov16_HO3-->


