---
title: Uso de la CLI de Azure en Windows | Microsoft Docs
description: Uso de la CLI de Azure en Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5554097904598a7bdb7bb0148f3a00923da2cfa7
ms.lasthandoff: 04/03/2017

---

# <a name="using-the-azure-cli-on-windows"></a>Uso de la CLI de Azure en Windows

La interfaz de línea de comandos (CLI) de Azure proporciona un entorno de scripting para crear y administrar recursos de Azure. La CLI de Azure está disponible para los sistemas operativos Windows, Linux y Mac OS X. En estos sistemas operativos, los comandos de la CLI son idénticos; sin embargo, puede diferir la sintaxis de scripting de cada sistema operativo.

En este documento se describen las maneras en que la CLI de Azure se puede instalar y ejecutar en Windows, y ofrece información sobre las consideraciones sintácticas de cada uno. Para ver documentación más detallada sobre la CLI de Azure, consulte la [documentación de la CLI de Azure]( https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="windows-subsystem-for-linux"></a>Subsistema de Windows para Linux

El subsistema de Windows para Linux (WSL) proporciona un entorno Linux de Ubuntu en Windows 10 Anniversary Edition. Cuando está habilitado, WSL proporciona una experiencia de Bash nativa que se puede usar para crear y ejecutar scripts de la CLI de Azure. Como WSL proporciona una experiencia de Bash nativa, los scripts de la CLI de Azure pueden compartirse entre Mac OS X, Linux y Windows sin ninguna modificación.

Para usar la CLI de Azure en WSL, haga lo siguiente.

|Tarea | Instrucciones |
|---|---|
| Habilitación de WSL | [Instalación de la documentación de WSL](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Instalación de la CLI de Azure |[Instalación de la CLI en WSL/Ubuntu 14.04](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

La CLI de Azure se puede ejecutar de forma nativa en Windows. En esta configuración, el paquete de la CLI de Azure está instalado en el sistema operativo Windows y se pueden ejecutar comandos de PowerShell. En esta configuración, los scripts y comandos de la CLI de Azure se pueden ejecutar en cualquier versión admitida de Windows; sin embargo, se requiere la sintaxis de scripts específica de cada plataforma. Por este motivo, los scripts no se comparten necesariamente entre Mac OS X, Linux y Windows sin ninguna modificación.

Para usar la CLI de Azure en Windows, instale el paquete siguiendo estas instrucciones, [Instalación de la CLI en Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#windows).

## <a name="docker-image"></a>Imagen de Docker

Cuando se usa Docker para Windows, se puede iniciar una imagen de Docker que incluye la CLI de Azure. Esta imagen se basa en Linux e incluye una experiencia de Bash nativa.  Cuando se usa Docker para Windows y la imagen de la CLI de Azure, los scripts deben compartirse entre Mac OS X, Linux y Windows. 

Para usar la CLI de Azure en Docker para Windows, asegúrese de que se está ejecutando Docker para Windows y ejecute el siguiente comando.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Una vez completado, se iniciará una sesión de Bash que se cargó previamente con las herramientas de la CLI de Azure.

## <a name="next-steps"></a>Pasos siguientes

[Ejemplo de la CLI para máquinas virtuales de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Ejemplos de la CLI para Azure Web Apps](../../app-service-web/app-service-cli-samples.md)

[Ejemplos de la CLI para SQL de Azure](../../sql-database/sql-database-cli-samples.md)

