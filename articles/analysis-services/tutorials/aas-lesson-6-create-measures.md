---
title: "Lección 6 del tutorial de Analysis Services Azure: Creación de medidas | Microsoft Docs"
description: "Describe cómo crear medidas en el proyecto del tutorial de Azure Analysis Services."
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
ms.openlocfilehash: fa47d4ea9aa019464e465c051b016dac7c224dc9
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="create-measures"></a>Crear medidas

En esta lección creará medidas para incluirlas en el modelo. De forma similar a las columnas calculadas que ha creado, una medida es un cálculo creado con una fórmula DAX. Sin embargo, a diferencia de las columnas calculadas, las medidas se evalúan en función de un *filtro* seleccionado por el usuario. Por ejemplo, una columna en particular o una segmentación de datos que se agrega al campo etiquetas de fila en una tabla dinámica. Luego, la medida aplicada calculará un valor para cada celda del filtro. Las medidas son cálculos eficaces y flexibles que puede incluir en casi cualquier modelo tabular para efectuar cálculos dinámicos sobre datos numéricos. Para obtener más información, consulte [Measures](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular) (Medidas).
  
Para crear medidas, use la *cuadrícula de medidas*. De forma predeterminada, cada tabla tiene una cuadrícula de medidas vacía, aunque en principio no se crean medidas para todas las tablas. La cuadrícula de medidas aparece debajo de una tabla del Diseñador de modelos cuando se encuentra en Vista de datos. Para mostrar u ocultar la cuadrícula de medidas de una tabla, haga clic en el menú **Tabla** y haga clic en **Mostrar cuadrícula de medidas**.  
  
Puede crear una medida haciendo clic en una celda vacía de la cuadrícula de medidas y, luego, escribiendo una fórmula DAX en la barra de fórmulas. Al hacer clic en ENTRAR para completar la fórmula, la medida aparece en la celda. También puede crear medidas usando una función de agregación estándar; para ello, haga clic en una columna y, luego, haga clic en el botón Autosuma (**∑**) de la barra de herramientas. Las medidas creadas con la característica Autosuma aparecen en la celda de la cuadrícula de medidas, justo debajo de la columna, aunque se pueden mover.  
  
En esta lección creará medidas especificando una fórmula DAX en la barra de fórmulas y usando la característica Autosuma.  
  
Tiempo estimado para completar esta lección: **30 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de llevar a cabo las tareas de esta lección, debe haber finalizado la lección anterior: [Lección 5: Creación de columnas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Crear medidas  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Para crear una medida DaysCurrentQuarterToDate en la tabla DimDate  
  
1.  En el Diseñador de modelos, haga clic en la tabla **DimDate**.  
  
2.  En la cuadrícula de medidas, haga clic en la celda vacía situada en la esquina superior izquierda.  
  
3.  En la barra de fórmulas, escriba la fórmula siguiente:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Observe que la celda superior izquierda ahora contiene un nombre de medida, **DaysCurrentQuarterToDate**, seguido del resultado, **92**. El resultado no es pertinente en este momento porque no se ha aplicado ningún filtro de usuario.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    A diferencia de las columnas calculadas, con las fórmulas de medidas puede escribir el nombre de la medida, seguido de dos puntos, seguido de la expresión de la fórmula.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Para crear una medida DaysInCurrentQuarter en la tabla DimDate  
  
1.  Con la tabla **DimDate** activa en el diseñador de modelos, en la cuadrícula de medidas, haga clic en la celda vacía situada debajo de la medida que ha creado.  
  
2.  En la barra de fórmulas, escriba la fórmula siguiente:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    Al crear una relación de comparación entre un período incompleto y el período anterior. La fórmula debe calcular la proporción del período que ha transcurrido y compararla con la misma proporción del período anterior. En este caso, [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] proporciona la proporción transcurrida del período actual.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Para crear una medida InternetDistinctCountSalesOrder en la tabla FactInternetSales  
  
1.  Haga clic en la tabla **FactInternetSales**.   
  
2.  Haga clic en el encabezado de la columna **SalesOrderNumber**.  
  
3.  En la barra de herramientas, haga clic en la flecha hacia abajo situada junto al botón Autosuma (**∑**) y, luego, seleccione **DistinctCount**.  
  
    La característica Autosuma crea automáticamente una medida para la columna seleccionada con la fórmula de agregación estándar DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  En la cuadrícula de medidas, haga clic en la nueva medida y, en la ventana **Propiedades**, en **Nombre de la medida**, cambie el nombre de la medida por **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Para crear medidas adicionales en la tabla FactInternetSales  
  
1.  Con la característica Autosuma, cree las siguientes medidas y asígneles un nombre:  

    |Columna|Nombre de la medida|Autosuma (∑)|Fórmula|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Recuento|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Suma|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Suma|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Suma|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Suma|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Suma|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Suma|=SUM([TaxAmt])|  
    |Freight|InternetTotalFreight|Suma|=SUM([Freight])|  
  
2.  Haga clic en una celda vacía de la cuadrícula de medidas y use la barra de fórmulas para crear las siguientes medidas personalizadas en orden:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
Las medidas creadas para la tabla FactInternetSales se pueden usar para analizar datos financieros esenciales, como las ventas, los costos y el margen de beneficio de los elementos definidos por el filtro seleccionado por el usuario.  
  
## <a name="whats-next"></a>Pasos siguientes
[Lección 7: Creación de indicadores clave de rendimiento](../tutorials/aas-lesson-7-create-key-performance-indicators.md)  

  
