---
title: "Lección complementaria del tutorial de Azure Analysis Services: Jerarquías desiguales | Microsoft Docs"
description: "Describe cómo corregir las jerarquías desiguales en el tutorial de Azure Analysis Services."
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
ms.openlocfilehash: c5c4a687ffe512b15372d152b517834771e46328
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Lección complementaria: Jerarquías desiguales

En esta lección complementaria, resolverá un problema común que se produce al dinamizar en jerarquías que contienen valores en blanco (miembros) en distintos niveles. Un ejemplo de esto sería una organización donde un director de alto nivel tiene como subordinados directos a directores de departamento y a trabajadores que no son directores. O bien, las jerarquías geográficas formadas por país-región-ciudad, donde algunas ciudades no tienen un elemento primario de estado o provincia, como Washington D. C. o Ciudad del Vaticano. Cuando una jerarquía tiene miembros en blanco, a menudo desciende en niveles diferentes o desiguales.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Los modelos tabulares en el nivel de compatibilidad 1400 tienen la propiedad adicional **Ocultar miembros** para las jerarquías. La configuración **predeterminada** da por supuesto que no hay ningún miembro en blanco en ningún nivel. La configuración **Ocultar miembros en blanco** excluye los miembros en blanco de la jerarquía cuando se agrega a una tabla dinámica o informe.  
  
Tiempo estimado para completar esta lección: **20 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Esta lección complementaria forma parte de un tutorial de modelado tabular. Antes de realizar las tareas de esta lección complementaria, debería haber finalizado todas las lecciones anteriores o haber completado un proyecto de modelo de ejemplo de ventas por Internet de Adventure Works. 

Si ha creado el proyecto de ventas por Internet de AW como parte del tutorial, el modelo todavía no contendrá datos ni jerarquías desiguales. Para completar esta lección complementaria, primero debe crear el problema. Para ello, agregue algunas tablas y cree relaciones, columnas calculadas, una medida y una jerarquía de organización. Solo tardará unos 15 minutos en hacerlo. Después, podrá solucionar el problema en unos pocos minutos.  

## <a name="add-tables-and-objects"></a>Agregar tablas y objetos
  
### <a name="to-add-new-tables-to-your-model"></a>Para agregar nuevas tablas al modelo
  
1.  En el Explorador de modelos tabulares, expanda **Orígenes de datos** y haga clic con el botón derecho en la conexión > **Importar nuevas tablas**.
  
2.  En el navegador, seleccione **DimEmployee** y **FactResellerSales** y, después, haga clic en **Aceptar**.

3.  En el Editor de consultas, haga clic en **Importar**.

4.  Cree las [relaciones](../tutorials/aas-lesson-4-create-relationships.md) siguientes:

    | Tabla 1           | Columna       | Dirección del filtro   | Tabla 2     | Columna      | Active |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Valor predeterminado            | DimDate     | Date        | Sí    |
    | FactResellerSales | DueDate      | Valor predeterminado            | DimDate     | Date        | Sin      |
    | FactResellerSales | ShipDateKey  | Valor predeterminado            | DimDate     | Date        | Sin      |
    | FactResellerSales | ProductKey   | Valor predeterminado            | DimProduct  | ProductKey  | Sí    |
    | FactResellerSales | EmployeeKey  | A ambas tablas | DimEmployee | EmployeeKey | Sí    |

5. En la tabla **DimEmployee**, cree las siguientes [columnas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md): 

    **Ruta de acceso** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  En la tabla **DimEmployee**, cree una [jerarquía](../tutorials/aas-lesson-9-create-hierarchies.md) denominada **Organización**. Agregue las columnas siguientes en orden: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  En la tabla **FactResellerSales**, cree la [medida](../tutorials/aas-lesson-6-create-measures.md) siguiente:

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Use [Analizar en Excel](../tutorials/aas-lesson-12-analyze-in-excel.md) para abrir Excel y crear automáticamente una tabla dinámica.

9.  En **Campos de tabla dinámica**, agregue la jerarquía **Organización** desde la tabla **DimEmployee** a **Filas** y la medida **ResellerTotalSales** desde la tabla **FactResellerSales** a **Valores**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Como puede ver en la tabla dinámica, la jerarquía muestra filas desiguales. Hay muchas filas en las que se muestran miembros en blanco.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Para corregir la jerarquía desigual mediante el establecimiento de la propiedad Ocultar miembros

1.  En el **Explorador de modelos tabulares**, expanda **Tablas** > **DimEmployee** > **Jerarquías** > **Organización**.

2.  En **Propiedades** > **Ocultar miembros**, seleccione **Ocultar miembros en blanco**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Vuelva a Excel y actualice la tabla dinámica. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Ahora el aspecto es mucho mejor.

## <a name="see-also"></a>Otras referencias   
[Lección 9: Creación de jerarquías](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Lección complementaria: Seguridad dinámica](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Lección complementaria: Filas de detalles](../tutorials/aas-supplemental-lesson-detail-rows.md)  
