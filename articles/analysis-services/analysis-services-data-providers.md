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
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 197e32058908533bc46b2ea2e38ff521a4a1fa3f
ms.lasthandoff: 04/07/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Proveedores de datos para la conexión a Azure Analysis Services

Los proveedores de datos, también conocidos como bibliotecas de cliente, son necesarios para que las aplicaciones cliente se puedan conectar a servidores de Analysis Services. 

Analysis Services usa tres proveedores de datos. ADOMD.NET y objetos de administración de Analysis Services (AMO) son proveedores de datos administrados. El proveedor OLE DB de Analysis Services (MSOLAP DLL) es un proveedor de datos nativo. Normalmente, los tres proveedores se instalan al mismo tiempo. Azure Analysis Services requiere la versión más reciente de los proveedores de datos. 

Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel instalarán los tres proveedores de datos. Sin embargo, en función de la versión de Excel, o de si nuevas versiones de Excel y Power BI Desktop se actualizan cada mes, puede que los proveedores de datos no estén actualizados con las versiones más recientes que se necesitan en Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones requieren la instalación manual de los proveedores. Los proveedores de datos para instalación manual se incluyen en los Feature Packs de SQL Server como paquetes distribuibles; sin embargo, estos están asociados a la versión de SQL Server y puede que no sean la versión más reciente.  

Los proveedores de datos para conexiones de cliente son diferentes de los proveedores de datos necesarios para conectarse desde un servidor de Azure Analysis Services a un origen de datos. Para más información sobre las conexiones de origen de datos, consulte [Conexiones a origen de datos](analysis-services-datasource.md).

## <a name="download-the-latest-preview-data-providers"></a>Descarga de los proveedores de dato de **versión preliminar** más recientes  
Use los siguientes proveedores de datos para obtener las últimas correcciones de errores y actualizaciones. Se recomiendan estos proveedores de datos cuando se conecte a la versión preliminar de Azure Analysis Services o SQL Server vNext Analysis Services.

[MSOLAP (amd64) (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[ADOMD (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>Descarga de los proveedores de datos **RTM** más recientes  
Use los siguientes proveedores de datos si se encuentra en un entorno de producción y necesita versiones totalmente finales y que ofrezcan una compatibilidad plena.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Pasos siguientes
Cuando los proveedores de datos más recientes están instalados, la aplicación cliente está lista para conectarse a un servidor. Para aprender más sobre la conexión desde un cliente, consulte [Obtención de datos de Azure Analysis Services](analysis-services-connect.md).

