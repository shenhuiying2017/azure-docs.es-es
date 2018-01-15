---
title: "Lección 5 del tutorial de Azure Analysis Services: Creación de columnas calculadas | Microsoft Docs"
description: "Describe cómo crear columnas calculadas en el proyecto del tutorial de Azure Analysis Services."
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
ms.openlocfilehash: 9b491af1e5c9da03f48335f5793ed71ddaf644fb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="create-calculated-columns"></a>Crear columnas calculadas

En esta lección, se crean datos en el modelo mediante la adición de columnas calculadas. Puede crear columnas calculadas (como columnas personalizadas) cuando use Obtención de datos, mediante el Editor de consultas, o más adelante en el diseñador de modelos, del mismo modo que aquí. Para obtener más información, vea [Calculated columns](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns) (Columnas calculadas).
  
Creará cinco columnas calculadas en tres tablas diferentes. Los pasos son ligeramente diferentes en cada tarea para mostrar que existen diversas maneras de crear columnas, cambiarlas el nombre y colocarlas en ubicaciones diferentes en una tabla.  

En esta lección también usará por primera vez Expresiones de análisis de datos (DAX). DAX es un lenguaje especial para crear expresiones de fórmula altamente personalizables para modelos tabulares. En este tutorial, se usa DAX para crear columnas calculadas, medidas y filtros de rol. Para obtener más información, vea [DAX in tabular models](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular) (DAX en modelos tabulares). 
  
Tiempo estimado para completar esta lección: **15 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 4: Creación de relaciones](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Crear columnas calculadas  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Crear una columna calculada MonthCalendar en la tabla DimDate  
  
1.  Haga clic en el menú **Modelo** > **Vista de modelo** > **Vista de datos**.  
  
    Las columnas calculadas solo se pueden crear mediante el diseñador de modelos en la vista de datos.  
  
2.  En el diseñador de modelos, haga clic en la tabla **DimDate** (pestaña).  
  
3.  Haga clic con el botón derecho en el encabezado de columna **CalendarQuarter** y, después, haga clic en **Insertar columna**.  
  
    Se inserta una nueva columna denominada **Calculated Column 1** a la izquierda de la columna **CalendarQuarter**.  
  
4.  En la barra de fórmulas sobre la tabla, escriba la siguiente fórmula DAX; Autocompletar le ayuda a escribir los nombres completos de columnas y tablas y enumera las funciones disponibles.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    Después, se rellenan los valores de todas las filas de la columna calculada. Si se desplaza hacia abajo por la tabla, verá que las filas pueden tener valores diferentes para esta columna, en función de los datos de cada fila.    
  
5.  Cambie el nombre de esta columna a **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
La columna calculada MonthCalendar proporciona un nombre que se puede ordenar para el valor de mes.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Crear una columna calculada DayOfWeek en la tabla DimDate  
  
1.  Con la tabla **DimDate** todavía activa, haga clic en el menú **Columna** y, a continuación, en **Agregar columna**.  
  
2.  En la barra de fórmulas, escriba la fórmula siguiente:  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Cuando haya terminado de crear la fórmula, presione ENTRAR. La nueva columna se agrega a la derecha de la tabla.  
  
3.  Cambie el nombre de la columna a **DayOfWeek**.  
  
4.  Haga clic en el encabezado de columna y arrastre la columna entre la columna **EnglishDayNameOfWeek** y la columna **DayNumberOfMonth**.  
  
    > [!TIP]  
    > Si mueve las columnas de la tabla, es más fácil navegar.  
  
La columna calculada DayOfWeek proporciona un nombre que se puede ordenar para el valor de día de la semana.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Crear una columna calculada ProductSubcategoryName en la tabla DimProduct  
  
  
1.  En la tabla **DimProduct**, desplácese hasta el extremo derecho. Observe que la columna de la derecha se denomina **Agregar columna** (en cursiva). Haga clic en el encabezado de columna.  
  
2.  En la barra de fórmulas, escriba la fórmula siguiente:  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Cambie el nombre de la columna a **ProductSubcategoryName**.  
  
La columna calculada ProductSubcategoryName se usa para crear una jerarquía en la tabla DimProduct que incluye datos de la columna EnglishProductSubcategoryName en la tabla DimProductSubcategory. Las jerarquías no pueden abarcar más de una tabla. Creará jerarquías más adelante en la lección 9.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Crear una columna calculada ProductCategoryName en la tabla DimProduct  
  
1.  Con la tabla **DimProduct** todavía activa, haga clic en el menú **Columna** y en **Agregar columna**.  
  
2.  En la barra de fórmulas, escriba la fórmula siguiente:  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Cambie el nombre de la columna a **ProductCategoryName**.  
  
La columna calculada ProductCategoryName se usa para crear una jerarquía en la tabla DimProduct que incluye datos de la columna EnglishProductCategoryName en la tabla DimProductCategory. Las jerarquías no pueden abarcar más de una tabla.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Crear una columna calculada Margin en la tabla FactInternetSales  
  
1.  En el diseñador de modelos, seleccione la tabla **FactInternetSales**.  
  
2.  Cree una columna calculada entre la columna **SalesAmount** y la columna **TaxAmt**.  
  
3.  En la barra de fórmulas, escriba la fórmula siguiente:  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Cambie el nombre de la columna a **Margin**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    La columna calculada Margin se usa para analizar los márgenes de beneficios de cada venta.  
  
## <a name="whats-next"></a>Pasos siguientes
[Lección 6: Creación de medidas](../tutorials/aas-lesson-6-create-measures.md)
  
  
  
