---
title: Usar la ventana Azure Cloud Shell | Microsoft Docs
description: "Información general sobre cómo usar la ventana Azure Cloud Shell."
services: azure
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
ms.date: 10/16/2017
ms.author: juluk
ms.openlocfilehash: 4eb5680c618d78e0722e1eb4a0f551f26b4dc902
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="using-the-azure-cloud-shell-window"></a>Uso de la ventana Azure Cloud Shell

En este documento se explica cómo usar la ventana Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Intercambio entre los entornos Bash y PowerShell
![](media/using-the-shell-window/env-selector.png)

Use el selector de entorno de la barra de herramientas de Cloud Shell para cambiar entre los entornos Bash y PowerShell.

## <a name="restart-cloud-shell"></a>Reinicio de Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Al reiniciar Cloud Shell se restablecerá el estado de la máquina y todos los archivos que no conserve el recurso compartido de archivos de Azure se perderán.

* Haga clic en el icono de reinicio de la barra de herramientas de Cloud Shell para restablecer el estado de la máquina.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar y maximizar la ventana Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Haga clic en el icono Minimizar situado en la parte superior derecha de la ventana para ocultarla. Haga clic en el icono Cloud Shell de nuevo para mostrarla.
* Haga clic en el icono Maximizar para establecer la ventana en la altura máxima. Para restablecer el tamaño anterior de la ventana, haga clic en Restaurar.

## <a name="concurrent-sessions"></a>Sesiones simultáneas
Cloud Shell permite varias sesiones simultáneas en pestañas del explorador posibilitando que cada sesión exista como un proceso Bash independiente.
Si sale de una sesión, asegúrese de salir de cada ventana de sesión, ya que cada proceso se ejecuta de forma independiente aunque se ejecuten en la misma máquina.

## <a name="copy-and-paste"></a>Copiar y pegar
[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Cambio del tamaño de la ventana Cloud Shell
* Haga clic y arrastre el borde superior de la barra de herramientas hacia arriba o hacia abajo para cambiar el tamaño de la ventana Cloud Shell.

## <a name="scrolling-text-display"></a>Desplazamiento de la presentación del texto
* Desplácese con el mouse o panel táctil para mover texto terminal.

## <a name="changing-the-text-size"></a>Cambio del tamaño del texto
![](media/using-the-shell-window/text-size.png)
* Haga clic en el icono de configuración que se encuentra en la esquina superior izquierda de la ventana y, luego, mantenga el puntero sobre la opción "Tamaño de texto" y seleccione el tamaño de texto que prefiera. La selección se conservará de una sesión a otra.

## <a name="exit-command"></a>Comando Exit
Al ejecutar `exit` se terminará la sesión activa. Este comportamiento se produce de forma predeterminada después de 20 minutos sin interacción.

## <a name="next-steps"></a>Pasos siguientes

[Guía de inicio rápido de Bash en Cloud Shell](quickstart.md)
[Guía de inicio rápido de PowerShell en Cloud Shell](quickstart-powershell.md)