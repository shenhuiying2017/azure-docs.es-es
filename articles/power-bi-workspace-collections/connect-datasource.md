---
title: "Conexión a un origen de datos en Colecciones de áreas de trabajo de Power BI | Microsoft Docs"
description: "Obtenga información sobre cómo conectarse a un origen de datos en Colecciones de áreas de trabajo de Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 24600c4343e3bfebe14f25020c5a7ba02d15af64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-data-source"></a>Conectarse a un origen de datos

Con **Colecciones de áreas de trabajo de Power BI**, puede insertar informes en su propia aplicación. Cuando inserta un informe de Power BI en su aplicación, el informe se conecta a los datos subyacentes **importando** una copia de los datos o **conectándose directamente** al origen de datos mediante **DirectQuery**.

> [!IMPORTANT]
> Colecciones de áreas de trabajo de Power BI está en desuso y estará disponible hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para obtener más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de la colección de áreas de trabajo de Power BI Embedded a Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Estas son las diferencias entre usar **Importación** y **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Las tablas, columnas *y datos* se importan o se copian en el conjunto de datos del informe. Para ver los cambios que se han producido en los datos subyacentes, debe actualizar o importar de nuevo un conjunto de datos actual completo. |Solo las *tablas y columnas* se importan o se copian en el conjunto de datos del informe. Siempre verá los datos más actuales. |

En estos momentos, con Colecciones de áreas de trabajo de Power BI, se puede usar DirectQuery con orígenes de datos en la nube, pero no en orígenes de datos locales.

> [!NOTE]
> No se admite la puerta de enlace de datos local con Colecciones de áreas de trabajo de Power BI en este momento. Esto significa que no se puede usar DirectQuery con orígenes de datos locales.

## <a name="supported-data-sources"></a>Orígenes de datos admitidos

**DirectQuery**
* Base de datos SQL de Azure
* Almacenamiento de datos SQL de Azure

**Importaciónación**

Puede realizar la importación mediante todos los orígenes de datos disponibles en Power BI Desktop. **No** podrá actualizar dichos datos en Colecciones de áreas de trabajo de Power BI. Tendrá que cargar los cambios en el archivo PBIX en Colecciones de áreas de trabajo de Power BI. Esto se debe a que no hay ninguna puerta de enlace disponible. 

## <a name="benefits-of-using-directquery"></a>Ventajas del uso de DirectQuery

Existen dos ventajas principales al usar **DirectQuery**:

* **DirectQuery** permite crear visualizaciones sobre conjuntos de datos muy grandes, cuando, de otro modo, sería inviable importar por primera vez todos los datos.
* Los cambios en los datos subyacentes pueden requerir una actualización de datos, y para algunos informes, la necesidad de mostrar los datos actuales puede requerir grandes transferencias de datos, de modo que sería inviable volver a importar datos. Por el contrario, los informes de **DirectQuery** siempre usan datos actuales.

## <a name="limitations-of-directquery"></a>Limitaciones de DirectQuery

Existen algunas limitaciones al usar **DirectQuery**:

* Todas las tablas deben proceder de una base de datos única.
* Si la consulta es demasiado compleja, se producirá un error. Para solucionar el error debe refactorizar la consulta, de forma que sea menos compleja. Si la consulta debe ser compleja, deberá importar los datos en lugar de usar **DirectQuery**.
* El filtrado de relación se limita a una dirección única, en lugar de a ambas direcciones.
* No puede cambiar el tipo de datos de una columna.
* De forma predeterminada, las limitaciones se colocan en expresiones DAX permitidas en las medidas. Consulte [DirectQuery y medidas](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery y medidas
Para asegurarse de que las consultas que se envían al origen de datos subyacente tienen un rendimiento adecuado, se imponen limitaciones en las medidas. Al usar **Power BI Desktop**, los usuarios avanzados pueden elegir derivar esta limitación eligiendo **Archivo > Opciones y configuración > Opciones**. En el cuadro de diálogo **Opciones**, elija **DirectQuery** y seleccione la opción **Permitir medidas sin restricciones en el modo DirectQuery**. Cuando se selecciona esta opción, se puede usar cualquier expresión DAX que sea válida para una medida. Sin embargo, los usuarios deben tener en cuenta que algunas expresiones que se ejecutan correctamente cuando se importan los datos, pueden provocar consultas lentas en el origen de back-end del modo **DirectQuery**. 

## <a name="see-also"></a>Otras referencias

* [Introducción a Colecciones de áreas de trabajo de Power BI](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)

