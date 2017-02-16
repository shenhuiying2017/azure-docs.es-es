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
ms.sourcegitcommit: 4dbd2c87d648f6923772b070fdfe7379de22e6c5
ms.openlocfilehash: e1adeaca702de3bad1c2e572f89ade9eba753173


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Proveedores de datos para la conexión a Azure Analysis Services

Los proveedores de datos, también conocidos como bibliotecas de cliente, son necesarios para que las aplicaciones cliente se puedan conectar a servidores de Analysis Services. 

Analysis Services usa tres proveedores de datos. ADOMD.NET y objetos de administración de Analysis Services (AMO) son proveedores de datos administrados. El proveedor OLE DB de Analysis Services (MSOLAP DLL) es un proveedor de datos nativo. Normalmente, los tres proveedores se instalan al mismo tiempo. Azure Analysis Services requiere la versión más reciente de los proveedores de datos. 

Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel instalarán los tres proveedores de datos. Sin embargo, en función de la versión de Excel, o de si nuevas versiones de Excel y Power BI Desktop se actualizan cada mes, puede que los proveedores de datos no estén actualizados con las versiones más recientes que se necesitan en Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones requieren la instalación manual de los proveedores. Los proveedores de datos para instalación manual se incluyen en los paquetes de características de SQL Server como paquetes distribuibles; sin embargo, estos están asociados a la versión de SQL Server y no pueden ser la versión más reciente.  

Los proveedores de datos para conexiones de cliente son diferentes de los proveedores de datos necesarios para conectarse desde un servidor de Azure Analysis Services a un origen de datos. Para más información sobre las conexiones de origen de datos, consulte [Conexiones a origen de datos](analysis-services-datasource.md).
 
## <a name="download-the-latest-data-providers"></a>Descarga de los proveedores de datos más recientes  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Pasos siguientes
Cuando los proveedores de datos más recientes están instalados, la aplicación cliente está lista para conectarse a un servidor. Para aprender más sobre la conexión, consulte [Obtención de datos de Azure Analysis Services](analysis-services-connect.md).



<!--HONumber=Jan17_HO1-->


