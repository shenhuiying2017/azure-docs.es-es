---
title: Lenguajes admitidos en Azure Functions
description: "Obtenga información acerca de qué lenguajes se admiten (versión de disponibilidad general) y cuáles son experimentales o están en versión preliminar."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="supported-languages-in-azure-functions"></a>Lenguajes admitidos en Azure Functions

En este artículo se explican los niveles de soporte que se ofrecen para los lenguajes que puede usar con Azure Functions.

## <a name="levels-of-support"></a>Niveles de soporte

Hay tres niveles de soporte:

* **Disponibilidad general (GA)**: totalmente compatible y aprobado para su uso en producción.
* **Versión preliminar**: aún no cuenta con soporte pero se espera que llegue al estado de disponibilidad general en el futuro.
* **Experimental**: no cuenta con soporte y podría abandonarse en el futuro; no se ofrece ninguna garantía de que alcance el estado de versión preliminar o disponibilidad general.

## <a name="languages-in-runtime-1x-and-2x"></a>Lenguajes en el entorno de tiempo de ejecución 1.x y 2.x

[Hay disponibles dos versiones del entorno de tiempo de ejecución de Azure Functions](functions-versions.md). El sistema de tiempo de ejecución 1.x está disponible de forma general. Es el único sistema de tiempo de ejecución que está aprobado para las aplicaciones de producción. El sistema de tiempo de ejecución 2.x está actualmente en versión preliminar, por lo que los lenguajes que admite están en versión preliminar. En la tabla siguiente se indica qué lenguajes se admiten en cada versión del sistema de tiempo de ejecución.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Lenguajes experimentales

En la versión 1.x, los lenguajes experimentales no escalan bien y no admiten todos los enlaces. Por ejemplo, Python es lento porque el sistema de tiempo de ejecución de Functions ejecuta *python.exe* con cada invocación de función. Y aunque Python admite los enlaces HTTP, no puede acceder al objeto de solicitud.

En la versión experimental, solo se admite la versión 4.0 de PowerShell porque es lo que se instala en las máquinas virtuales en las que se ejecutan las aplicaciones de función. Si desea ejecutar scripts de PowerShell, considere la posibilidad de usar [Azure Automation](https://azure.microsoft.com/services/automation/).

El sistema de tiempo de ejecución 2.x no admite lenguajes experimentales. En la versión 2.x, solo se admitirá un lenguaje cuando escale bien y admita desencadenadores avanzados.

Si desea usar uno de los lenguajes que solo están disponibles en la versión 1.x, mantenga el sistema de tiempo de ejecución 1.x. No use los lenguajes experimentales para nada importante, porque no cuentan con soporte oficial. Para solicitar ayuda, puede [crear incidencias en GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues), pero no abra casos de soporte técnico para problemas con lenguajes experimentales. 

### <a name="language-extensibility"></a>Extensibilidad de lenguaje

El sistema de tiempo de ejecución 2.x está diseñado para ofrecer [extensibilidad de lenguaje](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Uno de los primeros lenguajes en basarse en este modelo de extensibilidad es Java, que se encuentra en versión preliminar 2.x.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo usar uno de los lenguajes en versión de disponibilidad general o versión preliminar en Azure Functions, consulte los siguientes recursos:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)