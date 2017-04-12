---
title: "Habilitación o deshabilitación de la supervisión de máquinas virtuales de Azure"
description: "Se describe cómo habilitar o deshabilitar la supervisión de máquinas virtuales de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ffc9a84a3a89b37f772826c612f4fa9abadc1a0e
ms.lasthandoff: 04/03/2017


---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Habilitación o deshabilitación de la supervisión de máquinas virtuales de Azure
En esta sección se describe cómo habilitar o deshabilitar la supervisión de las máquinas virtuales que se ejecutan en Azure. De forma predeterminada, la supervisión está habilitada en las máquinas virtuales de Azure en el caso de que se hayan implementado desde el [Portal de Azure](https://portal.azure.com) y de que se hayan proporcionado los gráficos de supervisión de forma predeterminada con un período de un minuto. Puede habilitar o deshabilitar la supervisión mediante el portal o la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (la CLI de Azure). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Habilitación o deshabilitación de la supervisión a través del Portal de Azure
Puede habilitar la supervisión de la máquina virtual de Azure, que proporciona datos sobre la instancia en períodos de un minuto. (se aplican cambios de almacenamiento). En los gráficos del portal o la API se pueden consultar los datos de diagnóstico detallados de la máquina virtual. De forma predeterminada, el Portal de Azure habilita la supervisión, aunque puede desactivarla si sigue los pasos que se describen a continuación. Puede habilitar la supervisión mientras la máquina virtual se ejecuta o mientras está detenida.

* Abra el portal de Azure en **[https://portal.azure.com](https://portal.azure.com)**.
* En el panel de navegación de la izquierda, haga clic en Máquinas virtuales.
* En la lista de máquinas virtuales, seleccione una instancia en ejecución o detenida. Se abrirá la hoja Máquina virtual.
* Haga clic en "Toda la configuración".
* Haga clic en "Diagnóstico".
* Cambie el estado a Activado o Desactivado. En esta hoja también puede elegir el nivel de los detalles de supervisión que desea habilitar en la máquina virtual.

[Azure.Note] El conmutador del diagnóstico está activado de forma predeterminada cuando se crea una máquina virtual.

![Habilitación o deshabilitación de la supervisión a través del Portal de Azure][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Habilitación y deshabilitación de la supervisión con la CLI de Azure
Para habilitar la supervisión de una máquina virtual de Azure:

* Cree un archivo llamado PrivateConfig.json con el siguiente contenido.
        { "storageAccountName":"la cuenta de almacenamiento para recibir datos", "storageAccountKey":"la clave de la cuenta" }
* Ejecute el siguiente comando de la CLI de Azure.
  
        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] En caso de estar disponible, puede cambiar de la versión 2.0 a una versión posterior. 

Para más información sobre la configuración de las métricas de supervisión y ejemplos, consulte el documento **[Uso de la extensión de diagnóstico de Linux para supervisar los datos de rendimiento y diagnóstico de una máquina virtual Linux](classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png



