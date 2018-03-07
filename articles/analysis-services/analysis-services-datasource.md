---
title: "Orígenes de datos admitidos en Azure Analysis Services | Microsoft Docs"
description: "Describe los orígenes de datos admitidos para los modelos de datos en Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2018
ms.author: owend
ms.openlocfilehash: e2f7e356b260c0e5af67d28811bd88a63a601312
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Orígenes de datos admitidos en Azure Analysis Services

Los orígenes de datos y los conectores que se muestran en Obtener datos o en el Asistente para importación de Visual Studio corresponden tanto a Azure Analysis Services como a SQL Server Analysis Services. Sin embargo, no todos los orígenes de datos y los conectores que aparecen son compatibles con Azure Analysis Services. Los tipos de orígenes de datos con los que se puede establecer conexión dependen de muchos factores, como el nivel de compatibilidad del modelo, los conectores de datos disponibles, el tipo de autenticación, los proveedores y la compatibilidad con la puerta de enlace de datos local. 

## <a name="azure-data-sources"></a>Orígenes de datos de Azure

|Origen de datos  |En memoria  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   Sí      |    Sí      |
|Azure SQL Data Warehouse     |   Sí      |   Sí       |
|Azure Blob Storage*     |   Sí       |    Sin       |
|Azure Table Storage*    |   Sí       |    Sin       |
|Azure Cosmos DB (Beta)*     |  Sí        |  Sin         |
|Azure Data Lake Store*     |   Sí       |    Sin       |
|Azure HDInsight HDFS*     |     Sí     |   Sin        |
|Azure HDInsight Spark (Beta)*     |   Sí       |   Sin        |
|Azure Database for MySQL (versión preliminar)*     |   Sí       |   Sin       |
|Azure Database for PostgreSQL (versión preliminar)*     | Sí         |  Sin        |
||||

\* Solo modelos tabulares 1400.

**Proveedor**   
Los modelos En memoria y DirectQuery que se conectan a orígenes de datos de Azure utilizan el proveedor de datos .NET Framework para SQL Server.

## <a name="on-premises-data-sources"></a>Orígenes de datos locales

Para establecer conexión con un origen de datos local, se necesita una puerta de enlace local. Si utiliza una puerta de enlace, asegúrese de que se han instalado proveedores de 64 bits.

### <a name="in-memory-and-directquery"></a>En memoria y DirectQuery

|Origen de datos | Proveedor en memoria | Proveedor de DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, Proveedor OLE DB de Microsoft para SQL Server, Proveedor de datos .NET Framework para SQL Server | Proveedor de datos .NET Framework para SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, Proveedor OLE DB de Microsoft para SQL Server, Proveedor de datos .NET Framework para SQL Server | Proveedor de datos .NET Framework para SQL Server |
| Oracle |Proveedor OLE DB de Microsoft para Oracle, Proveedor de datos de Oracle para .NET |Proveedor de datos de Oracle para .NET | |
| Teradata |Proveedor OLE DB para Teradata, Proveedor de datos de Teradata para .NET |Proveedor de datos de Teradata para .NET | |
| | | |

\* Solo modelos tabulares 1400.

### <a name="in-memory-only"></a>Solo En memoria

> [!IMPORTANT]
> Las pruebas de los proveedores con los siguientes orígenes de datos siguen en proceso. 

|Origen de datos  |  
|---------|---------|
|Base de datos de Access     |  
|Active Directory*     |  
|Analysis Services     | 
|Analytics Platform System     |  
|Dynamics CRM*     |  
|Libro de Excel     | 
|Exchange*     |  
|Carpeta*     | 
|Documento JSON*     |  
|Líneas de archivo binario*     | 
|Base de datos MySQL     | 
|Fuente OData*     | 
|Consulta ODBC     | 
|OLE DB     |  
|Base de datos de Postgre SQL*    | 
|SAP HANA*    |   
|SAP Business Warehouse*    |  
|SharePoint*     |   
|Base de datos de Sybase     |  
|Tabla XML*    |  
|||
 
\* Solo modelos tabulares 1400.

## <a name="specifying-a-different-provider"></a>Especificación de un proveedor diferente

Los modelos de datos de Azure Analysis Services pueden requerir distintos proveedores de datos al conectarse a algunos orígenes de datos. En algunos casos, los modelos tabulares se conectan a orígenes de datos mediante proveedores nativos como SQL Server Native Client (SQLNCLI11) pueden devolver un error. Si utiliza proveedores nativos distintos de SQLOLEDB, puede aparecer un mensaje de error similar a este: **El proveedor "SQLNCLI11.1" no está registrado**. Por otro lado, si tiene un modelo de DirectQuery que se conecta a orígenes de datos locales y utiliza proveedores nativos, puede aparecer un mensaje de error similar a este: **Error al crear el conjunto de filas OLE DB. Sintaxis incorrecta cerca de "LIMIT"**.

Cuando se migra un modelo tabular de SQL Server Analysis Services local a Azure Analysis Services, puede ser necesario cambiar el proveedor.

**Para especificar un proveedor**

1. En SSDT > **Tabular Model Explorer**(Explorador de modelos tabulares) > **Orígenes de datos**, haga clic en una conexión de origen de datos y en **Editar origen de datos**.
2. En **Editar conexión**, haga clic en **Avanzadas** para abrir la ventana de propiedades avanzadas.
3. En **Establecer propiedades avanzadas** > **Proveedores**, seleccione el proveedor adecuado.

## <a name="impersonation"></a>Suplantación
En algunos casos, puede ser necesario especificar otra cuenta de suplantación. La cuenta de suplantación se puede especificar en Visual Studio (SSDT) o SSMS.

Para orígenes de datos locales:

* Si se utiliza la autenticación de SQL, la suplantación debe ser la Cuenta de servicio.
* Si se utiliza la autenticación de Windows, establezca la contraseña y el usuario de Windows. Para SQL Server, se admite la autenticación de Windows con una cuenta de suplantación específica solo para los modelos de datos en memoria.

Para orígenes de datos en la nube:

* Si se utiliza la autenticación de SQL, la suplantación debe ser la Cuenta de servicio.

## <a name="next-steps"></a>pasos siguientes
[Puerta de enlace local](analysis-services-gateway.md)   
[Administración del servidor](analysis-services-manage.md)   

