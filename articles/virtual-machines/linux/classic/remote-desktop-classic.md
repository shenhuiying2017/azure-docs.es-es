---
title: "Escritorio remoto a una máquina virtual Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo instalar y configurar Escritorio remoto para conectarse a una máquina virtual Linux de Microsoft Azure para el modelo de implementación clásico"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: mingzhan
ms.openlocfilehash: 72c814aece7626b19cefccb18e1b90f8c44d7f57
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Uso de Escritorio remoto para conectarse a una máquina virtual Linux de Microsoft Azure
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Para la versión actualizada para Resource Manager de este artículo, consulte [aquí](../use-remote-desktop.md).

## <a name="overview"></a>Información general
RDP (protocolo de escritorio remoto) es un protocolo propietario que se usa para Windows. ¿Cómo se puede usar RDP para conectarse a una máquina virtual de Linux de forma remota?

Esta guía le proporcionará la respuesta. Le ayudará a instalar y configurar xrdp en su máquina virtual Linux de Microsoft Azure, lo que le permite conectarlo a Escritorio remoto desde un equipo de Windows. Usaremos la VM Linux que ejecuta Ubuntu u OpenSUSE como en el ejemplo de esta guía.

La herramienta xrdp es un servidor RDP de código abierto, que le permite conectar su servidor Linux a Escritorio remoto desde un equipo de Windows. El rendimiento de RDP es superior al de VNC (Virtual Network Computing). VNC representa con gráficos de calidad JPEG y puede ser lento, mientras que RDP ofrece rapidez y nitidez.

> [!NOTE]
> Ya debe tener una VM de Microsoft Azure que ejecuta Linux. Para crear y configurar una máquina virtual Linux, consulte el [tutorial de máquinas virtuales Linux de Azure](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Creación de un punto de conexión para Escritorio remoto
Usaremos el extremo predeterminado 3389 para Escritorio remoto en este documento. Configure el punto de conexión 3389 como `Remote Desktop` en su máquina virtual Linux como se muestra a continuación:

![imagen](./media/remote-desktop/endpoint-for-linux-server.png)

Si no sabe cómo configurar un punto de conexión para la máquina virtual, consulte [esta guía](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Instalar Gnome Desktop
Conéctese a su máquina virtual Linux mediante `putty` e instale `Gnome Desktop`.

Para Ubuntu, use:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

Para OpenSUSE, use:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Instalación de xrdp
Para Ubuntu, use:

```bash
sudo apt-get install xrdp
```

Para OpenSUSE, use:

> [!NOTE]
> Actualice la versión de OpenSUSE con la versión que está utilizando en el siguiente comando. El ejemplo siguiente es para `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Iniciar xrdp y establecer el servicio xdrp durante el arranque
Para OpenSUSE, use:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

En Ubuntu, xrdp se iniciará y habilitará durante el arranque automáticamente después de la instalación.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Uso de xfce si utiliza una versión de Ubuntu posterior a Ubuntu 12.04LTS
Como la versión actual de xrdp no admite Gnome Desktop para versiones de Ubuntu posteriores a Ubuntu 12.04LTS, usaremos `xfce` Desktop en su lugar.

Para instalar `xfce`, use este comando:

```bash
sudo apt-get install xubuntu-desktop
```

A continuación, habilite `xfce` con este comando:

```bash
echo xfce4-session >~/.xsession
```

Edite el archivo de configuración `/etc/xrdp/startwm.sh`:

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Agregue la línea `xfce4-session` antes de la línea `/etc/X11/Xsession`.

Para reiniciar el servicio xrdp, use esto:

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Conexión de su VM Linux desde un equipo de Windows
En un equipo Windows, inicie el cliente de Escritorio remoto y escriba el nombre DNS de la máquina virtual Linux. O vaya al panel de la máquina virtual en Azure Portal y haga clic en `Connect` para conectarse a la máquina virtual Linux. En ese caso, verá la ventana de inicio de sesión:

![imagen](./media/remote-desktop/no2.png)

Inicie sesión con el nombre de usuario y la contraseña de la máquina virtual Linux.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre el uso de xrdp, consulte [http://www.xrdp.org/](http://www.xrdp.org/).
