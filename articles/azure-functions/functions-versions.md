---
title: "Introducción a las versiones de tiempo de ejecución de Azure Functions"
description: "Azure Functions admite varias versiones del runtime. Conozca las diferencias entre ellas y cómo elegir la más adecuada en su caso."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-runtime-versions-overview"></a>Introducción a las versiones de tiempo de ejecución de Azure Functions

 Hay dos versiones principales de tiempo de ejecución de Azure Functions: 1.x y 2.x. En este artículo se explica cómo elegir la versión principal que se debe usar.

> [!IMPORTANT] 
> La versión 1.x del runtime es la única versión aprobada para su uso en producción.

| Tiempo de ejecución | Status |
|---------|---------|
|1.x|Disponible con carácter general|
|2.x|Vista previa|

Para más información sobre cómo configurar una aplicación de función o el entorno de desarrollo para una versión determinada, consulte [How to target Azure Functions runtime versions](set-runtime-version.md) (Cómo elegir el destino de las versiones de tiempo de ejecución de Azure Functions) y [Codificación y comprobación de las funciones de Azure Functions en un entorno local](functions-run-local.md).

## <a name="cross-platform-development"></a>Desarrollo multiplataforma

El tiempo de ejecución 1.x admite solo desarrollo y hospedaje en el portal o en Windows. El tiempo de ejecución 2.x funciona en .NET Core, lo que significa que se puede ejecutar en todas las plataformas admitidas por .NET Core, incluidos macOS y Linux. Esto hace posible escenarios de desarrollo y hospedaje multiplataforma que no son posibles con 1.x.

## <a name="languages"></a>Idiomas

El tiempo de ejecución 2.x emplea un nuevo modelo de extensibilidad de lenguajes. Inicialmente, JavaScript y Java aprovechan las ventajas de este nuevo modelo. Los lenguajes experimentales de 1.x de Azure Functions no se han actualizado para usar el nuevo modelo, así que no se admiten en 2.x. En la tabla siguiente se indican qué lenguajes de programación se admiten en cada versión del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para más información, consulte [Lenguajes admitidos](supported-languages.md).

## <a name="bindings"></a>Enlaces 

El tiempo de ejecución 2.x usa un nuevo [modelo de extensibilidad de enlaces](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que ofrece estas ventajas:

* Compatibilidad con extensiones de enlace de terceros.
* Desacoplamiento de tiempo de ejecución y enlaces. Esto permite el control de versiones y la publicación de las extensiones de enlace de forma independiente. Por ejemplo, puede elegir actualizar a una versión de una extensión que se basa en una versión más reciente de un SDK subyacente.
* Un entorno de ejecución más ligero, donde solo se conocen y se cargan en tiempo de ejecución los enlaces que están en uso.

Todos los enlaces integrados de Azure Functions han adoptado este modelo y ya no se incluyen de forma predeterminada, excepto los desencadenadores Timer y HTTP. Esas extensiones se instalan automáticamente al crear las funciones a través de herramientas como Visual Studio o el portal.

En la tabla siguiente se indica qué enlaces se admiten en cada versión de tiempo de ejecución.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Problemas conocidos de 2.x

Para información acerca de la compatibilidad con los enlaces y otros espacios funcionales en la versión 2.x, consulte los [problemas conocidos del Runtime 2.0](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Selección del destino de la versión 2.0 del runtime en el entorno de desarrollo local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Vea las notas de versión para las versiones del runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)