---
title: "Uso de la ventana Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: Tutorial de la ventana Azure Cloud Shell.
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4e78dadb7fa036dcb4c5706fe774cbd85d57a746
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="using-the-shell-window"></a>Uso de la ventana de shell
En este documento se explica cómo utilizar la ventana Azure Cloud Shell.

## <a name="concurrent-sessions"></a>Sesiones simultáneas
Cloud Shell permite varias sesiones simultáneas en pestañas del explorador posibilitando que cada sesión exista como un proceso Bash independiente.
Si sale de una sesión, asegúrese de salir de cada ventana de sesión, ya que cada proceso se ejecuta de forma independiente aunque se ejecuten en la misma máquina.

## <a name="restart-cloud-shell"></a>Reinicio de Cloud Shell
![](media/recycle.png)
* Haga clic en el icono de reinicio de la barra de herramientas para restablecer la máquina de Cloud Shell.

> ![ADVERTENCIA] Al reiniciar Cloud Shell se restablecerá el estado de la máquina y todos los archivos que no conserva clouddrive se perderán.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar y maximizar la ventana Cloud Shell
![](media/minmax.png)
* Haga clic en el icono Minimizar situado en la parte superior derecha de la ventana para ocultarla. Haga clic en el icono Cloud Shell de nuevo para mostrarla.
* Haga clic en el icono Maximizar para establecer la ventana en la altura máxima. Para restablecer el tamaño anterior de la ventana, haga clic en Restaurar.

## <a name="copy-and-paste"></a>Copiar y pegar
* Windows: `Ctrl-insert` y `Shift-insert` o haga clic con el botón derecho en la lista desplegable para copiar y pegar.
  * FireFox o IE pueden no admitir los permisos del Portapapeles correctamente.
* Mac OS: `Cmd-c` y `Cmd-v` o haga clic con el botón derecho en la lista desplegable para copiar y pegar.

## <a name="resize-cloud-shell-window"></a>Cambio del tamaño de la ventana Cloud Shell
* Haga clic y arrastre el borde superior de la barra de herramientas hacia arriba o hacia abajo para cambiar el tamaño de la ventana Cloud Shell.

## <a name="scrolling-text-display"></a>Desplazamiento de la presentación del texto
* Desplace el texto con el mouse o panel táctil.

## <a name="exit-command"></a>Comando Exit
Al ejecutar `exit` se terminará la sesión activa. Este comportamiento se produce de forma predeterminada después de 10 minutos sin interacción.

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md)  
