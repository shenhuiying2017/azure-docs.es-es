---
title: Tutorial de Adventure Works de Azure Analysis Services | Microsoft Docs
description: Presenta el tutorial de Adventure Works de Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 9b81e011d3e1ed23465bbd554a0c7376b432b585
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services: Tutorial de Adventure Works

Este tutorial contiene lecciones en las que se explica cómo crear e implementar un modelo tabular en el nivel de compatibilidad 1400 mediante Visual Studio con [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

Si no está familiarizado con Analysis Services y los modelos tabulares, este tutorial es la forma más rápida de aprender a crear e implementar un modelo tabular básico mediante Visual Studio. Cuando se cumplan todos los requisitos previos, tardará aproximadamente entre dos y tres horas en completarlo.  
  
## <a name="what-you-learn"></a>Conocimientos que adquirirá   
  
-   Cómo crear un proyecto de modelos tabulares en el **nivel de compatibilidad 1400** en Visual Studio con SSDT.
  
-   Cómo importar datos de una base de datos relacional en una base de datos de un área de trabajo de un proyecto de modelos tabulares.  
  
-   Cómo crear y administrar las relaciones entre las tablas del modelo.  
  
-   Cómo crear columnas calculadas, medidas e indicadores clave de rendimiento que ayudan a los usuarios a analizar métricas empresariales fundamentales.  
  
-   Cómo crear y administrar perspectivas y jerarquías que ayudan a los usuarios a examinar más fácilmente los datos del modelo al proporcionar puntos de vista específicos del negocio y de la aplicación.  
  
-   Cómo crear particiones que dividen los datos de la tabla en partes lógicas más pequeñas que se pueden procesar de manera independiente de otras particiones.  
  
-   Cómo proteger los datos y los objetos de modelo mediante la creación de roles con miembros de usuario.  
  
-   Cómo implementar un modelo tabular en un servidor de **Azure Analysis Services** o en un servidor local de SQL Server 2017 Analysis Services.  
  
## <a name="prerequisites"></a>Requisitos previos  
Para completar este tutorial, necesita:  
  
-   Un servidor de Azure Analysis Services. Registrarse para obtener una [prueba gratuita de Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) y [crear un servidor](../analysis-services-create-server.md). 

-   Una instancia de [Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md) con la **base de datos de ejemplo AdventureWorksDW** o una instancia de SQL Server Data Warehouse con la [base de datos de ejemplo Adventure Works](http://go.microsoft.com/fwlink/?LinkID=335807).

    **Importante:** Si instala la base de datos de ejemplo en una instancia local de SQL Server Data Warehouse e implementa el modelo en un servidor de Azure Analysis Services, se requiere una [puerta de enlace de datos local](../analysis-services-gateway.md).

-   La versión más reciente de [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) para Visual Studio.

-   La versión más reciente de [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).    

-   Una aplicación cliente como [Power BI Desktop](https://powerbi.microsoft.com/desktop/) o Excel. 

## <a name="scenario"></a>Escenario  
Este tutorial se basa en Adventure Works Cycles, una compañía ficticia. Adventure Works es una gran multinacional manufacturera que produce y distribuye bicicletas, piezas y accesorios en mercados de Norteamérica, Europa y Asia. La empresa tiene 500 trabajadores. Además, Adventure Works emplea varios equipos de ventas regionales en su base comercial. Su proyecto es crear un modelo tabular para que los usuarios de ventas y marketing analicen los datos de la venta por Internet de la base de datos AdventureWorksDW.  
  
Para completar el tutorial, debe realizar varias lecciones. Cada lección consta de varias tareas. Es necesario completarlas todas en orden para finalizar la lección. Una lección en concreto puede contener varias tareas que obtienen un resultado similar, pero la manera en que se completa cada tarea es ligeramente diferente. Este método muestra que a menudo existe más de una manera de realizar una tarea y le pone a prueba para que aplique los conocimientos que ha adquirido en tareas y lecciones anteriores.  
  
Las lecciones están pensadas para guiarle por el proceso de creación de un modelo tabular básico mediante el uso de las numerosas características incluidas en SSDT. Dado que cada lección se basa en la lección anterior, debe completar las lecciones en orden.
  
Este tutorial no proporciona lecciones sobre la administración de un servidor en Azure Portal, la administración de un servidor o una base de datos con SSMS o el uso de una aplicación cliente para examinar los datos del modelo. 


## <a name="lessons"></a>Lecciones  
Este tutorial incluye las siguientes lecciones:  
  
|Lección|Tiempo estimado para completarla|  
|----------|------------------------------|  
|[1: Creación de un nuevo proyecto de modelo tabular](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minutos|  
|[2: Obtención de datos](../tutorials/aas-lesson-2-get-data.md)|10 minutos|  
|[3: Marcado como tabla de fechas](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minutos|  
|[4: Creación de relaciones](../tutorials/aas-lesson-4-create-relationships.md)|10 minutos|  
|[5: Creación de columnas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minutos|
|[6: Creación de medidas](../tutorials/aas-lesson-6-create-measures.md)|30 minutos|  
|[7: Creación de indicadores clave de rendimiento (KPI)](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minutos|  
|[8: Creación de perspectivas](../tutorials/aas-lesson-8-create-perspectives.md)|5 minutos|  
|[9: Creación de jerarquías](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minutos|  
|[10: Creación de particiones](../tutorials/aas-lesson-10-create-partitions.md)|15 minutos|  
|[11: Creación de roles](../tutorials/aas-lesson-11-create-roles.md)|15 minutos|  
|[12: Análisis en Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 minutos| 
|[13: Implementación](../tutorials/aas-lesson-13-deploy.md)|5 minutos|  
  
## <a name="supplemental-lessons"></a>Lecciones complementarias  
Estas lecciones no son necesarias para completar el tutorial, pero pueden resultar útiles para entender mejor las características avanzadas de creación de modelos tabulares.  
  
|Lección|Tiempo estimado para completarla|  
|----------|------------------------------|  
|[Filas de detalles](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minutos|
|[Seguridad dinámica](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minutos|
|[Jerarquías desiguales](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minutos| 

  
## <a name="next-steps"></a>pasos siguientes  
Para empezar, vea [Lección 1: Creación de un proyecto de modelo tabular](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  

