---
title: Bibliotecas de cliente necesarias para conectarse a Azure Analysis Services | Microsoft Docs
description: Se describen las bibliotecas de cliente necesarias para que las aplicaciones cliente y las herramientas de cliente se conecten a Azure Analysis Services.
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
ms.date: 12/14/2017
ms.author: owend
ms.openlocfilehash: 870d430d1926859894f452e0af812d794272a9e6
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para la conexión a Azure Analysis Services

Se requieren bibliotecas de cliente para que las aplicaciones cliente y las herramientas de cliente se puedan conectar a servidores de Analysis Services. 

## <a name="download-the-latest-client-libraries"></a>Descargar las bibliotecas de cliente más recientes  

|Descargar  |Versión  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    14.0.801.241      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    14.0.801.241      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   14.0.800.117      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    14.0.801.241      |

## <a name="understanding-client-libraries"></a>Descripción de las bibliotecas de cliente

Analysis Services usa tres bibliotecas de cliente, también conocidas como proveedores de datos. ADOMD.NET y Analysis Services Management Objects (AMO) son bibliotecas de cliente administradas. El proveedor OLE DB de Analysis Services (MSOLAP DLL) es una biblioteca de cliente nativa. Normalmente, las tres bibliotecas se instalan al mismo tiempo. **Azure Analysis Services requiere la versión más reciente de las tres bibliotecas**. 

Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel, instalan las tres bibliotecas de cliente y las actualizan cuando hay nuevas versiones disponibles. Dependiendo de la versión o la frecuencia de las actualizaciones, algunas bibliotecas de cliente pueden no ser las versiones más recientes que necesita Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones requieren la instalación manual o mediante programación de las bibliotecas. Las bibliotecas de cliente para la instalación manual se incluyen en los paquetes de características de SQL Server como paquetes de distribución. Sin embargo, estas bibliotecas de cliente están asociadas a la versión de SQL Server y no pueden ser la versión más reciente.  

Las bibliotecas de cliente para conexiones de cliente son diferentes de los proveedores de datos necesarios para conectarse desde un servidor de Azure Analysis Services a un origen de datos. Para más información sobre las conexiones de origen de datos, consulte [Conexiones a origen de datos](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipos de biblioteca de cliente

### <a name="analysis-services-ole-db-provider-msolap"></a>Proveedor OLE DB de Analysis Services (MSOLAP) 

 El proveedor OLE DB de Analysis Services (MSOLAP) es la biblioteca de cliente nativa para las conexiones de base de datos de Analysis Services. Es utilizada indirectamente por ADOMD.NET y AMO, que delegan las solicitudes de conexión en el proveedor de datos. También se puede llamar al proveedor OLE DB directamente desde el código de la aplicación.  
  
 El proveedor OLE DB de Analysis Services se instala automáticamente con la mayoría de las herramientas y aplicaciones cliente usadas para tener acceso a las bases de datos de Analysis Services. Debe instalarse en los equipos que se utilizan para tener acceso a datos de Analysis Services.  
  
 Los proveedores OLE DB suelen especificarse en las cadenas de conexión. Una cadena de conexión de Analysis Services usa una nomenclatura diferente para hacer referencia al proveedor OLE DB: MSOLAP.\<versión>.dll.

### <a name="amo"></a>AMO  

 AMO es una biblioteca de cliente administrada que se usa para la administración del servidor y la definición de datos. Las herramientas y aplicaciones de cliente la instalan y utilizan. Por ejemplo, SQL Server Management Studio (SSMS) usa AMO para conectarse a Analysis Services.  
  
 La conexión mediante AMO suele ser mínima y consta de `“data source=\<servername>”`. Una vez establecida una conexión, use la API para trabajar con las colecciones de base de datos y los objetos principales. Tanto SSDT como SSMS usan AMO para conectarse a una instancia de Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET es una biblioteca de cliente de datos administrados que se usa para consultar datos de Analysis Services. Las herramientas y aplicaciones de cliente la instalan y utilizan. 
  
 Al conectarse a una base de datos, las propiedades de la cadena de conexión para las tres bibliotecas son similares. Casi cualquier cadena de conexión que defina para ADOMD.NET utilizando [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) funciona también para AMO y el proveedor OLE DB de Analysis Services (MSOLAP). Para más información, consulte [Propiedades de la cadena de conexión de &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Cómo determinar la versión de la biblioteca de cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Vaya a `C:\Program Files\Microsoft Analysis Services\AS OLEDB\140`. Si tiene más de una carpeta, elija el número más alto.
  
2.  Haga clic con el botón derecho en **msolap140.dll** > **Propiedades** > **Detalles**.  
    
    ![Detalles de la biblioteca de cliente](media/analysis-services-data-providers/aas-msolap-details.png)
  
### <a name="amo"></a>AMO

1. Vaya a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\v4.0_14.0.0.0__89845dcd8080cc91`.
2. Haga clic con el botón derecho en **Microsoft.AnalysisServices** > **Propiedades** > **Detalles**.  

### <a name="adomd"></a>ADOMD

1. Vaya a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\v4.0_14.0.0.0__89845dcd8080cc91`.
2. Haga clic con el botón derecho en **Microsoft.AnalysisServices.AdomdClient** > **Propiedades** > **Detalles**.  


## <a name="next-steps"></a>pasos siguientes
[Connect with Excel](analysis-services-connect-excel.md)   (Conexión con Excel)  
[Conexión con Power BI](analysis-services-connect-pbi.md)
