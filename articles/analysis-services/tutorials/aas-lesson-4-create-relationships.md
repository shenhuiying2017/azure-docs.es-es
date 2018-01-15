---
title: "Lección 4 del tutorial de Azure Analysis Services: creación de roles | Microsoft Docs"
description: "En este artículo se describe cómo crear relaciones en el proyecto del tutorial de Azure Analysis Services."
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
ms.openlocfilehash: 57b3a172445047291f0aea5b1616b9dcbf6bf745
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="create-relationships"></a>Crear relaciones

En esta lección, comprobará las relaciones que se crearon automáticamente cuando importó los datos y agregará nuevas relaciones entre tablas diferentes. Una relación es una conexión entre dos tablas que establecen cómo se deben relacionar los datos de esas tablas. Por ejemplo, las tablas DimProduct y DimProductSubcategory tienen una relación basada en el hecho de que cada producto pertenece a una subcategoría. Para obtener más información, consulte el artículo de [relaciones](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular).
  
Tiempo estimado para completar esta lección: **10 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 3: Marcado como tabla de fechas](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Revisión de las relaciones existentes e incorporación de nuevas relaciones  
Al importar los datos mediante Obtener datos, se obtendrán siete tablas de la base de datos AdventureWorksDW2014. Por lo general, al importar datos de un origen relacional, las relaciones existentes se importan automáticamente junto con los datos. A fin de obtener datos para crear automáticamente relaciones en el modelo de datos, debe haber relaciones entre tablas en el origen de datos.

Antes de continuar creando el modelo, debe comprobar que las relaciones entre tablas se crearon correctamente. En este tutorial, también agrega tres relaciones nuevas.  

  
#### <a name="to-review-existing-relationships"></a>Procedimiento para revisar las relaciones existentes  
  
1.  Haga clic en el menú **Modelo** > **Vista de modelo** > **Vista de diagrama**.  

    El diseñador de modelos aparece ahora en la vista de diagrama, un formato gráfico que muestra todas las tablas que importó con líneas entre ellas. Las líneas entre las tablas indican las relaciones que se crearon automáticamente cuando importó los datos.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > Si no ve las relaciones entre tablas, probablemente significa que no hay ninguna relación entre esas tablas en el origen de datos.

    Incluya todas las tablas posibles utilizando los controles de minimapa de la esquina inferior derecha del diseñador de modelos. También puede hacer clic y arrastrar las tablas a ubicaciones diferentes, aunando las tablas o colocándolas en un orden concreto. Mover las tablas no afecta a las relaciones existentes entre ellas. Para ver todas las columnas de una tabla determinada, haga clic y arrastre un borde de la tabla para expandir o reducir el tamaño.  
  
2.  Haga clic en la línea sólida entre las tablas **DimCustomer** y **DimGeography**. La línea sólida entre estas dos tablas muestra que esta relación está activa, es decir, se utiliza de forma predeterminada al calcular las fórmulas DAX.  
  
    Observe la columna **GeographyKey** de la tabla **DimCustomer** y la columna **GeographyKey** de la tabla **DimGeography**, que ahora aparecen dentro de un cuadro de tabla. Estas columnas se utilizan en la relación. Las propiedades de la relación aparecen ahora también en la ventana **Propiedades**.  
  
    > [!TIP]  
    > Además de utilizar el Diseñador de modelos en la vista de diagrama, también puede usar el cuadro de diálogo Administrar relaciones para mostrar las relaciones entre todas las tablas en un formato de tabla. En el Explorador de modelos tabulares, haga clic con el botón derecho en **Relaciones** > **Administrar relaciones**.
  
3.  Compruebe que se crearon las siguientes relaciones cuando se importó cada una de las tablas de la base de datos AdventureWorksDW:  
  
    |Active|Tabla|Tabla de búsquedas relacionadas|  
    |----------|---------|------------------------|  
    |Sí|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Sí|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Sí|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Sí|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Sí|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Si falta alguna de las relaciones, compruebe que el modelo incluye las siguientes tablas: DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory y FactInternetSales. Si las tablas de la misma conexión de origen de datos se importaron en momentos distintos, las relaciones entre esas tablas no se crearon y tendrá que crearlas manualmente. Si no aparece ninguna relación, significa que no la hay en el origen de datos. Puede crearlas manualmente en el modelo de datos.

### <a name="take-a-closer-look"></a>Un examen más profundo
En la vista de diagrama, observe una flecha, un asterisco y un número en las líneas que muestran la relación entre las tablas.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

La flecha muestra la dirección del filtro. El asterisco muestra que esta tabla es el lado Varios en la cardinalidad de la relación y el 1 indica que esta tabla es el lado Uno de la relación. Si tiene que modificar una relación; por ejemplo, cambie la dirección del filtro de la relación y cardinalidad, y haga doble clic en la línea de relación para abrir el cuadro de diálogo Editar relación.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Estas características están diseñadas para el modelado de datos avanzados y están fuera del ámbito de este tutorial. Para obtener más información, consulte el artículo sobre los [filtros cruzados bidireccionales para modelos tabulares de Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

En algunos casos, debe crear relaciones adicionales entre las tablas del modelo para admitir una determinada lógica de negocios. Para este tutorial, debe crear tres relaciones adicionales entre las tablas FactInternetSales y DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Procedimiento para agregar nuevas relaciones entre tablas  
  
1.  En la tabla **FactInternetSales** del diseñador de modelos, haga clic en la columna **OrderDate** y mantenga presionado el botón; luego, arrastre el cursor hasta la columna **Date** de la tabla **DimDate**. Finalmente, suelte el botón.  

    Aparece una línea sólida que indica que ha creado una relación activa entre la columna **OrderDate** de la tabla **Internet Sales** y la columna **Date** de la tabla **Date**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Al crear relaciones, se selecciona automáticamente la dirección del filtro y cardinalidad entre la tabla principal y la tabla de búsqueda relacionada.  
  
2.  En la tabla **FactInternetSales**, haga clic en la columna **DueDate** y mantenga presionado el botón izquierdo del mouse; luego, arrastre el cursor hasta la columna **Date** de la tabla **DimDate**. Finalmente, suelte el botón.  
  
    Aparece una línea punteada que indica que ha creado una relación inactiva entre la columna **DueDate** de la tabla **FactInternetSales** y la columna **Date** de la tabla **DimDate**. Puede tener varias relaciones entre tablas, pero solo una relación puede estar activa simultáneamente. Las relaciones inactivas pueden hacerse activas para realizar agregaciones especiales en expresiones de DAX personalizadas.  
  
3.  Finalmente, cree una relación más. En la tabla **FactInternetSales**, haga clic en la columna **ShipDate** y mantenga presionado el botón; luego, arrastre el cursor hasta la columna **Date** de la tabla **DimDate**. Finalmente, suelte el botón.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>Pasos siguientes
[Lección 5: Creación de columnas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md)
  
  
  
