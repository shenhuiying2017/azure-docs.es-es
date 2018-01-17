---
title: "Lección complementaria del tutorial de Azure Analysis Services:Filas de detalles | Microsoft Docs"
description: "Se describe cómo crear una expresión de filas de detalle en el tutorial de Azure Analysis Services."
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
ms.openlocfilehash: 5a4dc7004245923fa6bda779114166ecf08d075f
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="supplemental-lesson---detail-rows"></a>Lección complementaria: Filas de detalles

En esta lección complementaria, usará el Editor DAX para definir una expresión personalizada de filas de detalles. Una expresión de filas de detalles es una propiedad de una medida, que proporciona a los usuarios finales más información sobre los resultados agregados de una medida. 
  
Tiempo estimado para completar esta lección: **10 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Esta lección complementaria forma parte de un tutorial de modelas tabulares. Antes de realizar las tareas de esta lección complementaria, debería haber finalizado todas las lecciones anteriores o haber completado un proyecto de modelo de ejemplo de ventas por Internet de Adventure Works.  
  
## <a name="whats-the-issue"></a>¿Cuál es el problema?
Vamos a echar un vistazo a los detalles de nuestra medida InternetTotalSales, antes de agregar una expresión de filas de detalles.

1.  En SSDT, haga clic en el menú **Modelo** > **Analizar en Excel** para abrir Excel y crear una tabla dinámica en blanco.
  
2.  En **Campos de tabla dinámica**, agregue la medida **InternetTotalSales** de la tabla FactInternetSales a **Valores**, **CalendarYear** de la tabla DimDate a **Columnas** y **EnglishCountryRegionName** a **Filas**. Ahora, la tabla dinámica nos proporciona resultados agregados de la medida InternetTotalSales por regiones y año. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. En la tabla dinámica, haga doble clic en un valor agregado de un año y una región. Aquí, hacemos doble clic en el valor de Australia y el año 2014. Se abre una nueva hoja que contiene datos, pero los datos no resultan de utilidad.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Lo que nos gustaría ver aquí es una tabla que contenga columnas y filas de datos que contribuyan al resultado agregado de nuestra medida InternetTotalSales. Para ello, podemos agregar una expresión de filas de detalles como una propiedad de la medida.

## <a name="add-a-detail-rows-expression"></a>Adición de una expresión de filas de detalles

#### <a name="to-create-a-detail-rows-expression"></a>Para crear una expresión de filas de detalles, siga estos pasos: 
  
1. En la cuadrícula de medidas de la tabla FactInternetSales, haga clic en la medida **InternetTotalSales**. 

2. En **Propiedades** > **Expresión de filas de detalles**, haga clic en el botón del editor para abrir el Editor DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. En el Editor DAX, escriba la siguiente expresión:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Esta expresión especifica nombres, columnas y resultados de medida de la tabla FactInternetSales y las tablas relacionadas devueltas cuando un usuario hace doble clic en un resultado agregado en una tabla dinámica o un informe.

4. De nuevo en Excel, elimine la hoja creada en el paso 3 y luego haga doble clic en un valor agregado. Esta vez, con la propiedad Expresión de filas de detalles definida para la medida, se abre una nueva hoja que contiene muchos datos útiles.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Vuelva a implementar el modelo.

  
## <a name="see-also"></a>Otras referencias  
[Función SELECTCOLUMNS (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)  
[Lección complementaria: Seguridad dinámica](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Lección complementaria: Jerarquías desiguales](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  
