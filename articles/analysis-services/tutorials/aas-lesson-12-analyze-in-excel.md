---
title: "Lección 12 del tutorial de Azure Analysis Services: Analizar en Excel | Microsoft Docs"
description: "Describe cómo usar Analizar en Excel en el proyecto del tutorial de Azure Analysis Services."
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
ms.openlocfilehash: 8d7f79db612ee02eaf585f9b45d888b3533a91b2
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="analyze-in-excel"></a>Analizar en Excel

En esta lección se usa la característica Analizar en Excel para abrir Microsoft Excel, crear automáticamente una conexión con el área de trabajo del modelo y agregar automáticamente una tabla dinámica a la hoja de cálculo. La característica Analizar en Excel está pensada para proporcionar una manera rápida y sencilla de probar la eficacia del diseño del modelo antes de implementarlo. En esta lección no se realizan análisis de datos. El propósito de esta lección es conseguir que se familiarice, como autor del modelo, con las herramientas que puede usar para probar el diseño del modelo.   
  
Para completar esta lección, Excel debe estar instalado en el mismo equipo que Visual Studio.
  
Tiempo estimado para completar esta lección: **cinco minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 11: Creación de roles](../tutorials/aas-lesson-11-create-roles.md).  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Examinar mediante las perspectivas predeterminada y Venta por Internet  
En estas primeras tareas, examinará el modelo mediante la perspectiva predeterminada, que incluye todos los objetos del modelo, y mediante la perspectiva Venta por Internet. La perspectiva Venta por Internet no incluye el objeto de tabla de clientes.  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Para examinar mediante la perspectiva predeterminada  
  
1.  Haga clic en el menú **Modelo** > **Analizar en Excel**.  
  
2.  En el cuadro de diálogo **Analizar en Excel**, haga clic en **Aceptar**.  
  
    Excel se abre con un nuevo libro. Se crea una conexión de origen de datos con la cuenta de usuario actual y se usa la perspectiva predeterminada para definir los campos visibles. Se agrega automáticamente una tabla dinámica a la hoja de cálculo.  
  
3.  En Excel, en la **Lista de campos de tabla dinámica**, observe que aparecen los grupos de medida **DimDate** y **FactInternetSales**. También aparecen las tablas **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** y **FactInternetSales** con sus respectivas columnas.  
  
4.  Cierre Excel sin guardar el libro.  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Para examinar mediante la perspectiva Venta por Internet  
  
1.  Haga clic en el menú **Modelo** y en **Analizar en Excel**.  
  
2.  En el cuadro de diálogo **Analizar en Excel**, deje seleccionado **Usuario de Windows actual**. Después, en el cuadro de lista desplegable **Perspectiva**, seleccione **Venta por Internet** y haga clic en **Aceptar**. 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  En Excel, en **Campos de tabla dinámica**, observe que la tabla DimCustomer no está incluida en la lista de campos.  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  Cierre Excel sin guardar el libro.  
  
## <a name="browse-by-using-roles"></a>Examinar mediante roles  
Los roles son una parte importante de los modelos tabulares. Si no hay al menos un rol al que agregar a los usuarios como miembros, los usuarios no pueden acceder a los datos del modelo ni analizarlos. La característica Analizar en Excel le permite probar los roles que ha definido.  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Para examinar mediante el rol de usuario Director de ventas  
  
1.  En SSDT, haga clic en el menú **Modelo** y en **Analizar en Excel**.  
  
2.  En **Especifique el nombre de usuario o rol que se va a usar al conectarse al modelo**, seleccione **Rol**. Después, en el cuadro de lista desplegable, seleccione **Jefe de ventas** y haga clic en **Aceptar**.  
  
    Excel se abre con un nuevo libro. Automáticamente se crea una tabla dinámica. En la lista de campos de tabla dinámica se incluyen todos los campos de datos disponibles en el nuevo modelo.  
      
3.  Cierre Excel sin guardar el libro.  
  
## <a name="whats-next"></a>Pasos siguientes
Vaya a la siguiente lección: [Lección 13: Implementación](../tutorials/aas-lesson-13-deploy.md).

  
  
  
