---
title: "Lección 3 del tutorial de Azure Analysis Services: Marcar como tabla de fechas |Microsoft Docs"
description: "Se describe cómo marcar una tabla de fechas en el proyecto del tutorial de Azure Analysis Services."
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
ms.openlocfilehash: 25e4475c77a25e4dcdcb90729f8633656bb186ff
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="mark-as-date-table"></a>Marcar como tabla de fechas

En la lección 2: Obtención de datos, importó una tabla de dimensiones denominada DimDate. Mientras que en su modelo esta tabla se denomina DimDate, también se conoce como *tabla de fechas* porque contiene datos de fecha y hora.  
  
Cada vez que se usan funciones de inteligencia de tiempo de DAX, igual que hará cuando cree medidas un poco más tarde, debe especificar propiedades que incluyan una *tabla de fechas* y un identificador único de *columna de fecha* en esa tabla.
  
En esta lección, marcará la tabla DimDate como la *tabla de fechas* y la columna de fecha (de la tabla de fechas) como la *columna de fecha* (identificador único).  

Antes de marcar la tabla de fechas y la columna de fecha, es hora de realizar algunas tareas para que su modelo sea más fácil de comprender. Observe en la tabla DimDate una columna llamada **FullDateAlternateKey**. Esta columna contiene una fila por cada día de cada año de calendario incluido en la tabla. Esta columna se usa mucho en las fórmulas de medida y los informes. Sin embargo, FullDateAlternateKey no es realmente un buen identificador en esta columna. Cambie el nombre por **Date**, para que sea más fácil identificarlo e incluirlo en fórmulas. Siempre que sea posible, es una buena idea cambiar el nombre de los objetos, como tablas y columnas, para que sean más fáciles de identificar en SSDT y en aplicaciones cliente de generación de informes como Power BI y Excel. 
  
Tiempo estimado para completar esta lección: **tres minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 2: Obtención de datos](../tutorials/aas-lesson-2-get-data.md). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Para cambiar el nombre de la columna FullDateAlternateKey, siga estos pasos:

1.  En el Diseñador de modelos, haga clic en la tabla **DimDate**.

2.  Haga doble clic en el encabezado de la columna **FullDateAlternateKey** y luego cambie su nombre por **Date**.

  
### <a name="to-set-mark-as-date-table"></a>Para establecer Marcar como tabla de fechas, siga estos pasos:  
  
1.  Seleccione la columna **Date** y, luego, en la ventana **Propiedades**, en **Tipo de datos**, asegúrese de que esté seleccionado **Fecha**.  
  
2.  Haga clic en el menú **Tabla**, haga clic en **Fecha** y luego en **Marcar como tabla de fechas**.  
  
3.  En el cuadro de diálogo **Marcar como tabla de fechas**, en el cuadro de lista **Fecha**, seleccione la columna **Date** como identificador único. Normalmente se selecciona de forma predeterminada. Haga clic en **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>Pasos siguientes
[Lección 4: Creación de relaciones](../tutorials/aas-lesson-4-create-relationships.md).
  
