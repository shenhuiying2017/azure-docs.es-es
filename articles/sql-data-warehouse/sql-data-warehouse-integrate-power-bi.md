---
title: Uso de Power BI con SQL Data Warehouse | Microsoft Docs
description: Sugerencias para usar Power BI con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 201efc5225d6c12028b48ccaf6764c60b44dce58
ms.contentlocale: es-es
ms.lasthandoff: 12/08/2016


---
# <a name="use-power-bi-with-sql-data-warehouse"></a>Uso de Power BI con Almacenamiento de datos SQL
De manera similar a lo que sucede con Base de datos SQL de Azure, Conexión directa de Almacenamiento de datos SQL permite que el usuario aproveche la aplicación lógica y eficaz junto con las funcionalidades analíticas de Power BI.  Con Conexión directa, las consultas se envían de vuelta a la instancia de Almacenamiento de datos SQL de Azure en tiempo real mientras explora los datos.  Esto, en combinación con la escala de Almacenamiento de datos SQL, permite que los usuarios creen, en minutos, informes dinámicos sobre terabytes de datos.  Además, la introducción del botón Abrir en Power BI permite que los usuarios conecten directamente Power BI a su instancia de Almacenamiento de datos SQL sin recopilar información de otras partes de Azure.

Cuando use Conexión directa, tenga en cuenta lo siguiente:

* Cuando se conecte, especifique el nombre completo del servidor (consulte a continuación para obtener más detalles).
* Asegúrese de que las reglas del firewall de la base de datos estén configuradas para "Permitir el acceso a los servicios de Azure".
* Cada acción, como seleccionar una columna o agregar un filtro, enviará directamente una consulta al almacenamiento de datos
* Los iconos se actualizan, aproximadamente, cada 15 minutos (no es necesario programar la actualización).
* La sección de Preguntas y respuestas no está disponible para los conjuntos de datos de Conexión directa.
* Los cambios de esquema no se seleccionan automáticamente.
* Todas las consultas de conexión directa agotarán el tiempo de espera al cabo de 2 minutos.

Estas restricciones y notas pueden cambiar a medida que continuamos mejorando las experiencias. A continuación, se detallan los pasos para realizar la conexión.  

## <a name="using-the-open-in-power-bi-button"></a>Con el botón "Abrir en Power BI"
La manera más fácil de trasladarse entre la instancia de Almacenamiento de datos SQL y Power BI es con el botón Abrir en Power BI. Este botón le permiten comenzar a crear sin problemas paneles nuevos en Power BI.  

1. Para comenzar, vaya a su instancia de Almacenamiento de datos SQL en el Portal de Azure clásico.
2. Haga clic en el botón "Abrir en Power BI".
3. Si no puede iniciar sesión directamente, o bien no tiene una cuenta de Power BI, deberá iniciar sesión.  
4. Se le dirigirá a la página de conexión a Almacenamiento de datos SQL, con la información de su instancia de Almacenamiento de datos SQL previamente rellenada.
5. Una vez haya escrito sus credenciales estará completamente conectado a su Almacenamiento de datos SQL.

## <a name="connecting-through-the-power-bi-portal"></a>Conexión a través del Portal de Power BI
Además de utilizar el botón Abrir en Power BI, los usuarios también pueden conectarse a su instancia de Almacenamiento de datos SQL a través del Portal de Power BI.

1. En la parte inferior del panel de navegación, haga clic en Obtener datos.
2. Seleccione "Bases de datos".
3. En la página Bases de datos, seleccione "Almacenamiento de datos SQL de Azure" y luego haga clic en "Conectar".
4. Escriba la información de conexión necesaria.  Se puede encontrar el nombre del servidor y de la base de datos en el Portal de Azure.
5. Se le dirigirá a la página principal de Power BI y después de que se realice su conexión, aparecerá una nueva entrada en "Conjuntos de datos" con el nombre de la instancia.  
6. Puede hacer clic en el nuevo conjunto de datos para explorar todas las tablas y vistas de la base de datos. Al seleccionar una columna se enviará una consulta de vuelta al origen, creando dinámicamente su objeto visual. Estos objetos visuales se pueden guardar en un informe nuevo y anclar de nuevo en el panel.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->

