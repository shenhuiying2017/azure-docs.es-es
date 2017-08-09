---
title: "Uso de la ventana Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: Tutorial de la ventana Azure Cloud Shell.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 345217452a25448f31fabdcc25dca182bbcf72eb
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Uso de la ventana Azure Cloud Shell

En este documento se explica cómo usar la ventana Cloud Shell.

## <a name="concurrent-sessions"></a>Sesiones simultáneas
Cloud Shell permite varias sesiones simultáneas en pestañas del explorador posibilitando que cada sesión exista como un proceso Bash independiente.
Si sale de una sesión, asegúrese de salir de cada ventana de sesión, ya que cada proceso se ejecuta de forma independiente aunque se ejecuten en la misma máquina.

## <a name="restart-cloud-shell"></a>Reinicio de Cloud Shell
![](media/recycle.png)
> [!WARNING]
> Al reiniciar Cloud Shell se restablecerá el estado de la máquina y todos los archivos que no conserve el recurso compartido de archivos se perderán.

* Haga clic en el icono de reinicio de la barra de herramientas para recibir el nuevo entorno Cloud Shell.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar y maximizar la ventana Cloud Shell
![](media/minmax.png)
* Haga clic en el icono Minimizar situado en la parte superior derecha de la ventana para ocultarla. Haga clic en el icono Cloud Shell de nuevo para mostrarla.
* Haga clic en el icono Maximizar para establecer la ventana en la altura máxima. Para restablecer el tamaño anterior de la ventana, haga clic en Restaurar.

## <a name="copy-and-paste"></a>Copiar y pegar
* Windows: `Ctrl-insert` para copiar y `Shift-insert` para pegar. Hacer clic con el botón derecho en la lista desplegable también habilita copiar y pegar.
  * Es posible que Firefox o IE no admitan los permisos del Portapapeles correctamente.
* Mac OS: `Cmd-c` para copiar y `Cmd-v` para pegar. Hacer clic con el botón derecho en la lista desplegable también habilita copiar y pegar.

## <a name="resize-cloud-shell-window"></a>Cambio del tamaño de la ventana Cloud Shell
* Haga clic y arrastre el borde superior de la barra de herramientas hacia arriba o hacia abajo para cambiar el tamaño de la ventana Cloud Shell.

## <a name="scrolling-text-display"></a>Desplazamiento de la presentación del texto
* Desplácese con el mouse o panel táctil para mover texto terminal.

## <a name="exit-command"></a>Comando Exit
Al ejecutar `exit` se terminará la sesión activa. Este comportamiento se produce de forma predeterminada después de 10 minutos sin interacción.

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md)
