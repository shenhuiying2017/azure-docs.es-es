---
title: Proveedores de datos necesarios para las conexiones de cliente a Azure Analysis Services | Microsoft Docs
description: Se describen los proveedores de datos necesarios para que los clientes se conecten a Azure Analysis Services.
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
ms.date: 01/20/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 13eb8ab1bf3c218f14b4c23ca1a46e9552d55b25
ms.openlocfilehash: d70b9f2c2a0cb1abe11dcfee9acb51dcb5552a60


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Proveedores de datos para la conexión a Azure Analysis Services

Los proveedores de datos, también conocidos como bibliotecas de cliente, son necesarios para que las aplicaciones cliente se puedan conectar a servidores de Analysis Services. 

Analysis Services usa tres proveedores de datos. ADOMD.NET y objetos de administración de Analysis Services (AMO) son proveedores de datos administrados. El proveedor OLE DB de Analysis Services (MSOLAP DLL) es un proveedor de datos nativo. Normalmente, los tres proveedores se instalan al mismo tiempo. Azure Analysis Services requiere la versión más reciente de los proveedores de datos. 

Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel instalarán los tres proveedores de datos. Sin embargo, en función de la versión de Excel, o de si nuevas versiones de Excel y Power BI Desktop se actualizan cada mes, puede que los proveedores de datos no estén actualizados con las versiones más recientes que se necesitan en Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones requieren la instalación manual de los proveedores. Los proveedores de datos para instalación manual se incluyen en los Feature Packs de SQL Server como paquetes distribuibles; sin embargo, estos están asociados a la versión de SQL Server y puede que no sean la versión más reciente.  

Los proveedores de datos para conexiones de cliente son diferentes de los proveedores de datos necesarios para conectarse desde un servidor de Azure Analysis Services a un origen de datos. Para más información sobre las conexiones de origen de datos, consulte [Conexiones a origen de datos](analysis-services-datasource.md).
 
## <a name="download-the-latest-data-providers"></a>Descarga de los proveedores de datos más recientes  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Pasos siguientes
Cuando los proveedores de datos más recientes están instalados, la aplicación cliente está lista para conectarse a un servidor. Para aprender más sobre la conexión desde un cliente, consulte [Obtención de datos de Azure Analysis Services](analysis-services-connect.md).



<!--HONumber=Feb17_HO2-->


