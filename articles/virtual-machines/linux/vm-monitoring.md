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
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Habilitación o deshabilitación de la supervisión de máquinas virtuales de Azure

En esta sección se describe cómo habilitar o deshabilitar la supervisión de las máquinas virtuales que se ejecutan en Azure. Puede habilitar o deshabilitar la supervisión mediante el portal o la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (la CLI de Azure).

> [!IMPORTANT]
> En este documento se describe la versión 2.3 de la extensión Diagnostics de Linux., que está en desuso. Se admitirá la versión 2.3 hasta el 30 de junio de 2018.
>
> En su lugar, puede instalarse la versión 3.0 de la extensión Diagnostics de Linux. Consulte [la documentación](./diagnostic-extension.md) para obtener más información.

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Habilitación o deshabilitación de la supervisión a través de Azure Portal

Puede habilitar la supervisión de la máquina virtual de Azure, que proporciona datos sobre la instancia en períodos de un minuto. (se aplican cambios de almacenamiento). En los gráficos del portal o la API se pueden consultar los datos de diagnóstico detallados de la máquina virtual. De forma predeterminada, Azure Portal permite la supervisión basada en host de un conjunto limitado de métricas. Puede habilitar la supervisión de métricas dentro de una máquina virtual tanto mientras está en ejecución como estando detenida.

* Abra Azure Portal en [https://portal.azure.com](https://portal.azure.com).
* En el panel de navegación de la izquierda, haga clic en Máquinas virtuales.
* En la lista de máquinas virtuales, seleccione una instancia en ejecución o detenida. Se abre la hoja "Máquinas virtuales".
* Haga clic en Toda la configuración.
* Haga clic en Diagnóstico.
* Cambie el estado a Activado o Desactivado. En esta hoja también puede elegir el nivel de los detalles de supervisión que desea habilitar en la máquina virtual.

![Habilitación o deshabilitación de la supervisión a través de Azure Portal.][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Habilitación y deshabilitación de la supervisión con la CLI de Azure

Para habilitar la supervisión de una máquina virtual de Azure:

* Cree un archivo (con un nombre como PrivateConfig.json):

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* Habilite la extensión mediante la CLI de Azure.

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

Para obtener más información, consulte [Uso de la extensión de diagnóstico de Linux para supervisar los datos de rendimiento y diagnóstico de una máquina virtual Linux](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

