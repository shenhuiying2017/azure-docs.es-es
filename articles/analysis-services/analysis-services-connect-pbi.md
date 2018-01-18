---
title: "Conexión a Azure Analysis Services con Power BI | Microsoft Docs"
description: Aprenda a conectarse a un servidor de Azure Analysis Services mediante Power BI.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/10/2018
ms.author: owend
ms.openlocfilehash: ea1094d0ce858cd7df9c49f18fb81b07e31fca53
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="connect-with-power-bi"></a>Conexión con Power BI

Una vez que se ha creado un servidor en Azure y se ha implementado un modelo tabular, los usuarios de su organización pueden conectarse y comenzar a explorar los datos. 

> [!TIP]
> Asegúrese de usar la versión más reciente de [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Conexión en Power BI Desktop

1. En Power BI Desktop, haga clic en **Obtener datos** > **Azure** > **Base de datos de Azure Analysis Services**.

2. En **Servidor**, escriba el nombre del servidor. Asegúrese de incluir la dirección URL completa; por ejemplo, asazure://westcentralus.asazure.windows.net/advworks.

3. En **Base de datos**, si conoce el nombre de la base de datos de modelo tabular o la perspectiva a la que desea conectarse, péguelo aquí. En caso contrario, puede dejar este campo en blanco y seleccionar una base de datos o una perspectiva más adelante.

4. Deje la opción predeterminada **Conectar en directo** y presione **Conectar**. Las conexiones de importación no se admiten actualmente.

5. Escriba sus credenciales de inicio de sesión, si se le solicitan. 

6. En **Navegador**, expanda el servidor, seleccione el modelo o la perspectiva a la que desea conectarse y haga clic en **Conectar**. Haga clic en un modelo o una perspectiva para mostrar todos los objetos de esa vista.

    El modelo se abre en Power BI Desktop con un informe en blanco en la vista de informe. La lista de campos muestra todos los objetos del modelo no ocultos. El estado de la conexión se muestra en la esquina inferior derecha.

## <a name="connect-in-power-bi-service"></a>Conexión en Power BI (servicio)

1. Cree un archivo de Power BI Desktop que tenga una conexión en directo con el modelo del servidor.
2. En [Power BI](https://powerbi.microsoft.com), haga clic en **Obtener datos** > **Archivos**, busque el archivo pbix y selecciónelo.



## <a name="see-also"></a>Otras referencias
[Conexión a Azure Analysis Services](analysis-services-connect.md)   
[Bibliotecas de cliente](analysis-services-data-providers.md)

