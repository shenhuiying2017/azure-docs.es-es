---
title: Tutorial de Adventure Works de Azure Analysis Services | Microsoft Docs
description: Presenta el tutorial de Adventure Works de Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: a613bbe84a3834ab4fb237779248c7ad8d75b563
ms.contentlocale: es-es
ms.lasthandoff: 06/03/2017

---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services: Tutorial de Adventure Works

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Este tutorial contiene lecciones sobre cómo crear e implementar un modelo tabular en el nivel de compatibilidad 1400 mediante [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

Si no está familiarizado con Analysis Services y el modelado tabular, este tutorial es la forma más rápida de obtener información sobre cómo crear e implementar un modelo tabular básico. Cuando se cumplan todos los requisitos previos, tardará aproximadamente entre dos y tres horas en completarlo.  
  
## <a name="what-you-learn"></a>Conocimientos que adquirirá   
  
-   Cómo crear un proyecto de modelo tabular en el **nivel de compatibilidad 1400** en SSDT.
  
-   Cómo importar datos de una base de datos relacional en un proyecto de modelo tabular.  
  
-   Cómo crear y administrar las relaciones entre las tablas del modelo.  
  
-   Cómo crear columnas calculadas, medidas e indicadores clave de rendimiento que ayudan a los usuarios a analizar métricas empresariales fundamentales.  
  
-   Cómo crear y administrar perspectivas y jerarquías que ayudan a los usuarios a examinar más fácilmente los datos del modelo al proporcionar puntos de vista específicos del negocio y de la aplicación.  
  
-   Cómo crear particiones que dividen los datos de la tabla en partes lógicas más pequeñas que se pueden procesar de manera independiente de otras particiones.  
  
-   Cómo proteger los datos y los objetos de modelo mediante la creación de roles con miembros de usuario.  
  
-   Cómo implementar un modelo tabular en un servidor de **Azure Analysis Services** o en un servidor local de SQL Server 2017 Analysis Services.  
  
## <a name="prerequisites"></a>Requisitos previos  
Para completar este tutorial, necesita:  
  
-   Una instancia de Azure Analysis Services o de SQL Server 2017 Analysis Services en la que implementar el modelo. Registrarse para obtener una [prueba gratuita de Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) y [crear un servidor](../analysis-services-create-server.md). O bien, iniciar sesión y descargar [SQL Server 2017 Community Technology Preview](https://www.microsoft.com/evalcenter/evaluate-sql-server-vnext-ctp). 

-   Una base de datos SQL Server o Azure SQL Database con la [base de datos de ejemplo AdventureWorksDW2014](http://go.microsoft.com/fwlink/?LinkID=335807). Esta base de datos de ejemplo incluye los datos necesarios para completar este tutorial. Descargar [ediciones gratuitas de SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads). O bien, registrarse para obtener una [prueba gratuita de Azure SQL Database](https://azure.microsoft.com/services/sql-database/). 

    **Importante:** Si ha instalado la base de datos de ejemplo en un servidor local de SQL Server e implementa el modelo en un servidor de Azure Analysis Services, se requiere una [puerta de enlace de datos local](../analysis-services-gateway.md).

-   La versión más reciente de [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).

-   La versión más reciente de [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).    

-   Una aplicación cliente como [Power BI Desktop](https://powerbi.microsoft.com/desktop/) o Excel. 

## <a name="scenario"></a>Escenario  
Este tutorial se basa en Adventure Works Cycles, una compañía ficticia. Adventure Works es una gran multinacional manufacturera que produce y distribuye bicicletas de metal y de materiales compuestos en mercados de Norteamérica, Europa y Asia. La empresa tiene 500 trabajadores. Además, Adventure Works emplea varios equipos de ventas regionales en su base comercial. Su proyecto es crear un modelo tabular para que los usuarios de ventas y marketing analicen los datos de la venta por Internet de la base de datos AdventureWorksDW.  
  
Para completar el tutorial, debe realizar varias lecciones. Cada lección consta de varias tareas. Es necesario completarlas todas en orden para finalizar la lección. Una lección en concreto puede contener varias tareas que obtienen un resultado similar, pero la manera en que se completa cada tarea es ligeramente diferente. Este método muestra que a menudo existe más de una manera de realizar una tarea y le pone a prueba para que aplique los conocimientos que ha adquirido en tareas y lecciones anteriores.  
  
Las lecciones están pensadas para guiarle por el proceso de creación de un modelo tabular básico que se ejecuta mediante el uso de las numerosas características incluidas en SSDT. Dado que cada lección se basa en la lección anterior, debe completar las lecciones en orden.
  
Este tutorial no proporciona lecciones sobre la administración de un servidor en Azure Portal, la administración de un servidor o una base de datos con SSMS o el uso de una aplicación cliente para examinar los datos del modelo. 


## <a name="lessons"></a>Lecciones  
Este tutorial incluye las siguientes lecciones:  
  
|Lección|Tiempo estimado para completarla|  
|----------|------------------------------|  
|[Lección 1: Creación de un proyecto de modelo tabular](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minutos|  
|[Lección 2: Obtención de datos](../tutorials/aas-lesson-2-get-data.md)|10 minutos|  
|[Lección 3: Marcado como tabla de fechas](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minutos|  
|[Lección 4: Creación de relaciones](../tutorials/aas-lesson-4-create-relationships.md)|10 minutos|  
|[Lección 5: Creación de columnas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minutos|
|[Lección 6: Creación de medidas](../tutorials/aas-lesson-6-create-measures.md)|30 minutos|  
|[Lección 7: Creación de indicadores clave de rendimiento](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minutos|  
|[Lección 8: Creación de perspectivas](../tutorials/aas-lesson-8-create-perspectives.md)|5 minutos|  
|[Lección 9: Creación de jerarquías](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minutos|  
|[Lección 10: Creación de particiones](../tutorials/aas-lesson-10-create-partitions.md)|15 minutos|  
|[Lección 11: Creación de roles](../tutorials/aas-lesson-11-create-roles.md)|15 minutos|  
|[Lección 12: Analizar en Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 minutos| 
|[Lección 13: Implementación](../tutorials/aas-lesson-13-deploy.md)|5 minutos|  
  
## <a name="supplemental-lessons"></a>Lecciones complementarias  
Estas lecciones no son necesarias para completar el tutorial, pero pueden resultar útiles para entender mejor las características avanzadas de creación de modelos tabulares.  
  
|Lección|Tiempo estimado para completarla|  
|----------|------------------------------|  
|[Filas de detalles](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minutos|
|[Seguridad dinámica](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minutos|
|[Jerarquías desiguales](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minutos| 

  
## <a name="next-steps"></a>Pasos siguientes  
Para empezar, vea [Lección 1: Creación de un proyecto de modelo tabular](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  


