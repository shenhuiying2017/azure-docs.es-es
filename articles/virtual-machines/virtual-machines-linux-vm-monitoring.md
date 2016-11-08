---
title: Habilitación o deshabilitación de la supervisión de máquinas virtuales de Azure
description: Se describe cómo habilitar o deshabilitar la supervisión de máquinas virtuales de Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: ''

ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss

---
# Habilitación o deshabilitación de la supervisión de máquinas virtuales de Azure
En esta sección se describe cómo habilitar o deshabilitar la supervisión de las máquinas virtuales que se ejecutan en Azure. De forma predeterminada, la supervisión está habilitada en las máquinas virtuales de Azure en el caso de que se hayan implementado desde el [Portal de Azure](https://portal.azure.com) y de que se hayan proporcionado los gráficos de supervisión de forma predeterminada con un período de un minuto. Puede habilitar o deshabilitar la supervisión mediante el portal o la interfaz de la línea de comandos de Azure para Mac, Linux y Windows (la CLI de Azure).

## Habilitación o deshabilitación de la supervisión a través del Portal de Azure
Puede habilitar la supervisión de la máquina virtual de Azure, que proporciona datos sobre la instancia en períodos de un minuto (se efectuarán cambios de almacenamiento). En los gráficos del portal o la API se pueden consultar los datos de diagnóstico detallados de la máquina virtual. De forma predeterminada, el Portal de Azure habilita la supervisión, aunque puede desactivarla si sigue los pasos que se describen a continuación. Puede habilitar la supervisión mientras la máquina virtual se ejecuta o mientras está detenida.

* Abra el Portal de Azure en **[https://portal.azure.com](https://portal.azure.com)**.
* En el panel de navegación de la izquierda, haga clic en Máquinas virtuales.
* En la lista de máquinas virtuales, seleccione una instancia en ejecución o detenida. Se abrirá la hoja Máquina virtual.
* Haga clic en "Toda la configuración".
* Haga clic en "Diagnóstico".
* Cambie el estado a Activado o Desactivado. En esta hoja también puede elegir el nivel de los detalles de supervisión que desea habilitar en la máquina virtual.

[Azure.Note] El conmutador del diagnóstico está activado de forma predeterminada cuando se crea una máquina virtual.

![Habilitación o deshabilitación de la supervisión a través del Portal de Azure][1]

## Habilitación y deshabilitación de la supervisión con la CLI de Azure
Para habilitar la supervisión de una máquina virtual de Azure:

* Cree un archivo denominado, por ejemplo, PrivateConfig.json con el siguiente contenido: { "StorageAccountName":"cuenta de almacenamiento en la que se recibirán datos", "StorageAccountKey":"clave de la cuenta" }
* Ejecute el siguiente comando de la CLI de Azure.
  
        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] En caso de estar disponible, puede cambiar de la versión 2.0 a una versión posterior.

Para más información sobre la configuración de las métricas de supervisión y ejemplos, visite el documento **[Uso de la extensión de diagnóstico de Linux para supervisar los datos de rendimiento y diagnóstico de una máquina virtual Linux](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png


<!---HONumber=AcomDC_0824_2016-->