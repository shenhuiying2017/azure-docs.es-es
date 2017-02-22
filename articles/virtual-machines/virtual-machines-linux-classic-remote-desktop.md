---
title: "Escritorio remoto a una máquina virtual Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo instalar y configurar Escritorio remoto para conectarse a una máquina virtual Linux de Microsoft Azure."
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 5acf43ac97fca98bc6d06a174b5fcc66f06a03b2


---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Uso de Escritorio remoto para conectarse a una máquina virtual Linux de Microsoft Azure
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

## <a name="overview"></a>Información general
RDP (protocolo de escritorio remoto) es un protocolo propietario que se usa para Windows. ¿Cómo se puede usar RDP para conectarse a una máquina virtual de Linux de forma remota?

Esta guía le proporcionará la respuesta. Le ayudará a instalar y configurar xrdp en su VM Linux de Microsoft Azure y puede conectarlo a Escritorio remoto desde un equipo de Windows. Usaremos la VM Linux que ejecuta Ubuntu u OpenSUSE como en el ejemplo de esta guía.

Xrdp es un servidor RDP de código abierto, que le permite conectar su servidor Linux a Escritorio remoto desde un equipo de Windows. Su rendimiento es mucho mejor que VNC (Virtual Network Computing). VNC tiene esta racha de calidad "JPEG" y comportamiento lento, mientras que RDP es rápido y clarísimo.

> [!NOTE]
> Ya debe tener una VM de Microsoft Azure que ejecuta Linux. Para crear y configurar una máquina virtual Linux, consulte el [tutorial de máquinas virtuales Linux de Azure](virtual-machines-linux-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
> 
> 

## <a name="create-endpoint-for-remote-desktop"></a>Crear extremo para Escritorio remoto
Usaremos el extremo predeterminado 3389 para Escritorio remoto en este documento. Por lo tanto, configure el extremo 3389 como Escritorio remoto en su VM Linux como se muestra a continuación:

![imagen](./media/virtual-machines-linux-classic-remote-desktop/no1.png)

Si no sabe cómo configurar el punto de conexión en su máquina virtual, consulte la [guía](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="install-gnome-desktop"></a>Instalar Gnome Desktop
Conéctese a su VM Linux mediante PuTTY e instale `Gnome Desktop`.

Para Ubuntu, use:

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


Para OpenSUSE, use:

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Instalación de xrdp
Para Ubuntu, use:

    #sudo apt-get install xrdp

Para OpenSUSE, use:

> [!NOTE]
> Actualice la versión OpenSUSE con la versión que usa en el comando siguiente. A continuación, se muestra un comando de ejemplo para `OpenSUSE 13.2`.
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Iniciar xrdp y establecer el servicio xdrp durante el arranque
Para OpenSUSE, use:

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Para Ubuntu, se iniciará y habilitará xrdp durante el arranque automáticamente después de la instalación.

## <a name="using-xfce-if-you-are-using-ubuntu-version-later-than-ubuntu-1204lts"></a>Uso de xfce si usa una versión de Ubuntu posterior a Ubuntu 12.04LTS
Como xrdp actual no podría admitir Gnome Desktop de una versión de Ubuntu posterior a Ubuntu 12.04LTS, usaremos `xfce` Desktop en su lugar.

Instale `xfce`, use:

    #sudo apt-get install xubuntu-desktop

A continuación, habilite `xfce`, use:

    #echo xfce4-session >~/.xsession

Edite el archivo de configuración `/etc/xrdp/startwm.sh`, use:

    #sudo vi /etc/xrdp/startwm.sh   

Agregue la línea `xfce4-session` antes de la línea `/etc/X11/Xsession`.

Reinicie el servicio xrdp, use:

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Conexión de su VM Linux desde un equipo de Windows
En un equipo de Windows, inicie el cliente de Escritorio remoto, escriba el nombre DNS de su máquina virtual de Linux o vaya a `Dashboard` de su máquina virtual en el Portal de Azure clásico y haga clic en `Connect` para conectar su máquina virtual de Linux. Verá la siguiente ventana de inicio de sesión:

![imagen](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Inicie sesión con `user` & `password` de su VM Linux y disfrute de Escritorio remoto en su VM Linux de Microsoft Azure en este momento.

## <a name="next"></a>Pasos siguientes
Para obtener más información sobre el uso de xrdp, puede consultar [aquí](http://www.xrdp.org/).




<!--HONumber=Feb17_HO3-->


