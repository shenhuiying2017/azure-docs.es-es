---
title: Bibliotecas de cliente necesarias para conectarse a Azure Analysis Services | Microsoft Docs
description: Se describen las bibliotecas de cliente necesarias para que las aplicaciones cliente y las herramientas de cliente se conecten a Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a96e7fe671dc051da35168fa7b49ecba53b4c4a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para la conexión a Azure Analysis Services

Se requieren bibliotecas de cliente para que las aplicaciones cliente y las herramientas de cliente se puedan conectar a servidores de Analysis Services. 

Analysis Services usa tres bibliotecas de cliente. ADOMD.NET y los objetos de administración de Analysis Services (AMO), son bibliotecas de cliente administradas. El proveedor OLE DB de Analysis Services (MSOLAP DLL) es una biblioteca de cliente nativa. Normalmente, las tres bibliotecas se instalan al mismo tiempo. Azure Analysis Services requiere la versión más reciente de las bibliotecas. 

Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel, instalan las tres bibliotecas de cliente. Sin embargo, dependiendo de la versión o la frecuencia de las actualizaciones, las bibliotecas de cliente pueden no ser las versiones más recientes que necesita Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones requieren la instalación manual de las bibliotecas. Las bibliotecas de cliente para la instalación manual se incluyen en los paquetes de características de SQL Server como paquetes de distribución. Sin embargo, estas bibliotecas de cliente están asociadas a la versión de SQL Server y no pueden ser la versión más reciente.  

Las bibliotecas de cliente para conexiones de cliente son diferentes de los proveedores de datos necesarios para conectarse desde un servidor de Azure Analysis Services a un origen de datos. Para más información sobre las conexiones de origen de datos, consulte [Conexiones a origen de datos](analysis-services-datasource.md).

## <a name="download-the-latest-client-libraries"></a>Descargar las bibliotecas de cliente más recientes  
Use las siguientes bibliotecas de cliente si se encuentra en un entorno de producción y necesita versiones totalmente finales y que ofrezcan una compatibilidad plena.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Pasos siguientes
[Connect with Excel](analysis-services-connect-excel.md)   (Conexión con Excel)  
[Conexión con Power BI](analysis-services-connect-pbi.md)
