---
title: "Lección 10 del tutorial de Azure Analysis Services: Creación de particiones | Microsoft Docs"
description: "Describe cómo crear particiones en el proyecto del tutorial de Azure Analysis Services."
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
ms.openlocfilehash: a73836b784a5e86d01df51fb83d619890d56502a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="create-partitions"></a>Creación de particiones

En esta lección creará particiones para dividir la tabla FactInternetSales en partes lógicas más pequeñas que se puedan procesar (actualizar) de manera independiente de otras particiones. De forma predeterminada, todas las tablas que incluya en el modelo tienen una partición, que incluye todas las columnas y las filas de la tabla. En el caso de la tabla FactInternetSales, queremos dividir los datos por año: una partición para cada uno de los cinco años de la tabla. Así, cada partición se podrá procesar de manera independiente. Para obtener más información, consulte [Partitions](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular) (Particiones). 
  
Tiempo estimado para completar esta lección: **15 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de llevar a cabo las tareas de esta lección, debe haber finalizado la lección anterior: [Lección 9: Creación de jerarquías](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Creación de particiones  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Para crear particiones en la tabla FactInternetSales  
  
1.  En el Explorador de modelos tabulares, expanda **Tablas** y haga clic con el botón derecho en **FactInternetSales** > **Particiones**.  
  
2.  En el Administrador de particiones, haga clic en **Copiar** y cambie el nombre por **FactInternetSales2010**.
  
    Como quiere que la partición incluya solo las filas de un período determinado (para el año 2010), debe modificar la expresión de consulta.
  
4.  Haga clic en **Diseño** para abrir el Editor de consultas y, luego, haga clic en la consulta **FactInternetSales2010**.

5.  En la vista previa, haga clic en la flecha hacia abajo del encabezado de columna **OrderDate** y haga clic en **Filtros de fecha y hora** > **Entre**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  En el cuadro de diálogo Filtrar filas, en **Mostrar filas donde: OrderDate**, deje **posterior a o igual que** y, en el campo de fecha, escriba **1/1/2010**. Deje seleccionado el operador **Y**, seleccione **anterior a**; luego, en el campo de fecha, escriba **1/1/2011** y haga clic en **Aceptar**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Observe que en PASOS APLICADOS del Editor de consultas encontrará otro paso denominado Filas filtradas. Este filtro consiste en seleccionar solo las fechas de los pedidos de 2010.

8.  Haga clic en **Import**.

    En el Administrador de particiones, observe que la expresión de consulta ahora tiene una cláusula Filas filtradas adicional.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Esta instrucción especifica que esta partición debe incluir solo los datos de esas filas donde el valor OrderDate está en el año 2010, tal como se especifica en la cláusula Filas filtradas.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Para crear una partición para el año 2011  
  
1.  En la lista de particiones, haga clic en la partición **FactInternetSales2010** que ha creado y, luego, haga clic en **Copiar**.  Cambie el nombre de la partición a **FactInternetSales2011**. 

    No es necesario usar el Editor de consultas para crear otra cláusula Filas filtradas. Como ha creado una copia de la consulta para el 2010, lo único que tiene que hacer es efectuar un pequeño cambio en la consulta del 2011.
  
2.  En **Expresión de consulta**, para que esta partición incluya solo las filas del año 2011, reemplace los años de la cláusula Filas filtradas por **2011** y **2012**, respectivamente; así:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Para crear particiones para los años 2012, 2013 y 2014  
  
- Siga los pasos anteriores (creación de particiones para los años 2012, 2013 y 2014, y cambio de los años en la cláusula Filas filtradas) para incluir solo las filas correspondientes a ese año. 
  

## <a name="delete-the-factinternetsales-partition"></a>Eliminar la partición FactInternetSales
Ahora que tiene particiones para cada año, puede eliminar la partición FactInternetSales. Procure que no se produzca ninguna superposición al elegir Procesar todo para procesar las particiones.

#### <a name="to-delete-the-factinternetsales-partition"></a>Para eliminar la partición FactInternetSales
-  Haga clic en la partición FactInternetSales y haga clic en **Eliminar**.



## <a name="process-partitions"></a>Procesar las particiones  
En el Administrador de particiones, observe que en la columna **Procesado por última vez** de cada una de las nuevas particiones que ha creado se muestra que estas particiones nunca se han procesado. Al crear particiones debe ejecutar la operación Procesar particiones o Procesar tabla para actualizar los datos de esas particiones.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Para procesar las particiones FactInternetSales  
  
1.  Haga clic en **Aceptar** para cerrar el Administrador de particiones.  
  
2.  Haga clic en la tabla **FactInternetSales** y, luego, haga clic en el menú **Modelo** > **Proceso** > **Procesar particiones**.  
  
3.  En el cuadro de diálogo Procesar particiones, compruebe que **Modo** está establecido en **Proceso predeterminado**.  
  
4.  Seleccione la casilla de la columna **Proceso** de cada una de las cinco particiones que ha creado y haga clic en **Aceptar**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Si se le piden las credenciales de suplantación, escriba el nombre de usuario y la contraseña de Windows que ha especificado en la lección 2.  
  
    Aparece el cuadro de diálogo **Procesamiento de datos**, en el que se muestran los detalles del proceso de cada partición. Tenga en cuenta que se transfiere un número de filas diferente para cada partición. Cada partición incluye solamente las filas del año especificado en la cláusula WHERE de la instrucción SQL. Cuando el procesamiento haya finalizado, continúe y cierre el cuadro de diálogo Procesamiento de datos.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>Pasos siguientes
Vaya a la siguiente lección: [Lección 11: Creación de roles](../tutorials/aas-lesson-11-create-roles.md). 
