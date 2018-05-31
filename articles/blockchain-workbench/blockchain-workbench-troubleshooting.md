---
title: Solución de problemas de Azure Blockchain Workbench
description: Procedimientos para la solución de problemas de una aplicación de Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 419ed6dc76101366e47ae94067f7b671a10c94e2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196341"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Solución de problemas de Azure Blockchain Workbench

Está disponible un script de PowerShell para ayudar con la depuración del desarrollo y soporte técnico. El script genera un resumen y recopila registros detallados para solucionar el problema. Los registros recopilados incluyen:

* Red de Blockchain, por ejemplo, Ethereum
* Microservicios de Blockchain Workbench
* Application Insights
* Supervisión de Azure (OMS)

Puede usar la información para determinar los próximos pasos y determinar la causa raíz de los problemas. 

## <a name="troubleshooting-script"></a>Script de solución de problemas

El script de solución de problemas de PowerShell está disponible en GitHub. [Descargue un archivo zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) o clone el ejemplo de GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Ejecute el script
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Ejecute el script `collectBlockchainWorkbenchTroubleshooting.ps1` para recopilar registros y crear un archivo ZIP que contiene una carpeta con información para la solución de problemas. Por ejemplo: 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
El script acepta los siguientes parámetros:

| .  | DESCRIPCIÓN | Obligatorio |
|---------|---------|----|
| SubscriptionID | Identificador de suscripción para crear o ubicar todos los recursos. | Sí |
| ResourceGroupName | Nombre del grupo de recursos de Azure donde se haya implementado Blockchain Workbench. | Sí |
| OutputDirectory | Ruta de acceso para crear el archivo .ZIP de salida. Si no se especifica, tiene como valor predeterminado el directorio actual. | Sin  |
| LookbackHours | Número de horas que se usará al extraer la telemetría. El valor predeterminado es 24 horas. El valor máximo es 90 horas. | Sin  |
| OmsSubscriptionId | El identificador de la suscripción donde se ha implementado OMS. Pase este parámetro únicamente si se implementa OMS para la red de la cadena de bloques fuera del grupo de recursos de Blockchain Workbench.| Sin  |
| OmsResourceGroup |El grupo de recursos en el que se ha implementado OMS. Pase este parámetro únicamente si se implementa OMS para la red de la cadena de bloques fuera del grupo de recursos de Blockchain Workbench.| Sin  |
| OmsWorkspaceName | El nombre del área de trabajo de OMS. Pase este parámetro únicamente si se implementa OMS para la red de la cadena de bloques fuera del grupo de recursos de Blockchain Workbench | Sin  |

## <a name="what-is-collected"></a>¿Qué información se recopila?

El archivo ZIP de salida contiene la estructura de carpetas siguiente:

| Archivo o carpeta | DESCRIPCIÓN  |
|---------|---------|
| \Summary.txt | Resumen del sistema |
| \Metrics\blockchain | Métricas relacionadas con la cadena de bloques |
| \Metrics\Workbench | Métricas relacionadas con Workbench |
| \Details\Blockchain | Registros detallados sobre la cadena de bloques |
| \Details\Workbench | Registros detallados sobre Workbench |

El archivo de resumen proporciona una instantánea del mantenimiento de la aplicación y el estado general de la aplicación. El resumen proporciona las acciones recomendadas, resalta los errores destacados y proporciona metadatos sobre la ejecución de servicios.

La carpeta **Metrics** (Métricas) contiene las métricas de los diversos componentes del sistema a lo largo del tiempo. Por ejemplo, el archivo de salida `\Details\Workbench\apiMetrics.txt` contiene un resumen de los diferentes códigos de respuesta, y tiempos de respuesta a lo largo del período de recopilación. La carpeta **Details** (Detalles) contiene registros detallados para solucionar problemas específicos con Workbench o la red de la cadena de bloques subyacente. Por ejemplo, `\Details\Workbench\Exceptions.csv` contiene una lista de las excepciones más recientes que se han producido en el sistema, lo cual resulta útil para solucionar problemas de errores con contratos inteligentes o las interacciones con la cadena de bloques. 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Arquitectura de Azure Blockchain Workbench](blockchain-workbench-architecture.md)