---
title: "Interacción con informes mediante la API de JavaScript | Microsoft Docs"
description: "La API de JavaScript de Power BI permite insertar fácilmente informes de Power BI en las aplicaciones."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 62a95807c35fcba15a8e5ffdf340a307dd22a642
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interacción con informes de Power BI mediante la API de JavaScript

La API de JavaScript de Power BI permite insertar fácilmente informes de Power BI en las aplicaciones. Con ella, las aplicaciones pueden interactuar mediante programación con distintos elementos de los informes, como las páginas y filtros. Esta interactividad hace que los informes Power BI se integren mejor en las aplicaciones.

> [!IMPORTANT]
> Las colecciones de áreas de trabajo de Power BI están en desuso y estarán disponibles hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de la colección de áreas de trabajo de Power BI Embedded a Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Dichos informes se insertan en las aplicaciones mediante un iframe que se hospeda como parte de la aplicación. El iframe hace de límite entre la aplicación y el informe, como se puede ver en la siguiente imagen:

![iframe de Colección de áreas de trabajo de Power BI sin la API de Javascript](media/interact-with-reports/iframe-without-javacript.png)

El iframe facilita mucho el proceso de integración, pero sin la API de JavaScript el informe y la aplicación no pueden interactuar entre sí. Esta falta de interacción puede hacer parecer que el informe no forma parte de la aplicación. El informe y la aplicación necesitan comunicarse bidireccionalmente, como se muestra en la siguiente imagen:

![iframe de Colección de áreas de trabajo de Power BI con la API de Javascript](media/interact-with-reports/iframe-with-javascript.png)

La API de JavaScript de Power BI permite escribir código que puede pasar de forma segura a través del límite del iframe. Esto permite que la aplicación realice una acción en un informe mediante programación y que escuche eventos de acciones que realizan los usuarios dentro del informe.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>¿Qué se puede hacer con la API de JavaScript de Power BI?

Con la API de JavaScript se pueden administrar informes, navegar a las distintas páginas de un informe, filtrar un informe y controlar eventos de inserción. En el siguiente diagrama se muestra la estructura de la API.

![Diagrama de la API de JavaScript de Power BI](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Administración de informes
La API de Javascript permite administrar el comportamiento en el nivel de página y de informe:

* Insertar un informe específico de Power BI de forma segura en la aplicación (pruebe la [aplicación de demostración integrada](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Establecer un token de acceso
* Configurar el informe
  * Habilitar y deshabilitar el panel de filtro y el panel de navegación de página (pruebe la [aplicación de demostración de actualización de configuración](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Establecer los valores predeterminados de las páginas y los filtros: pruebe la [demostración de establecimiento de valores predeterminados](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Entrar y salir de modo de pantalla completa

[Más información acerca de cómo insertar informes](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Navegación a las páginas de un informe
La API de JavaScript permite detectar todas las páginas de un informe y establecer la página actual. Pruebe la [aplicación de demostración de navegación](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Más información acerca de la navegación por la página](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtro de un informe
La API de JavaScript proporciona funcionalidades de filtro básicas y avanzadas para informes páginas de informes integrados. Pruebe la [aplicación de demostración de filtro](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)y revise aquí el código de introducción.

#### <a name="basic-filters"></a>Filtros básicos
Los filtros básicos se colocan en un nivel de jerarquía o de columna, y contienen la lista de valores que se incluyen o excluyen.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Filtros avanzados
Los filtros avanzados utilizan los operadores lógicos AND y OR, y aceptan una o dos condiciones, cada una de ellas con su propio operador y valor. Estas son las condiciones que se admiten:

* None
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Más información acerca de los filtros](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Control de eventos

Además de enviar información al iframe, la aplicación también puede recibir información sobre los siguientes eventos que proceda de iframe:

* Insertar
  * loaded
  * error
* Informes
  * pageChanged
  * dataSelected (próximamente)

[Más información sobre el control de eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la API de JavaScript de Power BI, consulte los siguientes vínculos:

* [Wiki de API de JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Referencia de modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Demostración en directo](https://microsoft.github.io/PowerBI-JavaScript/demo/)