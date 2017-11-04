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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 5ba7ef5aa2cccad7cda3cb39459a5a5722516524
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Orígenes de datos admitidos en Azure Analysis Services
Los servidores de Azure Analysis Services admiten la conexión a orígenes de datos en la nube y de forma local en su organización. Con el tiempo se van agregando más orígenes de datos compatibles, por lo que se recomienda consultarlos con asiduidad. 

Actualmente se admiten los siguientes orígenes de datos:

| Nube  |
|---|
| Azure Blob Storage*  |
| Base de datos SQL de Azure  |
| Almacenamiento de datos de Azure |


| Local  |   |   |   |
|---|---|---|---|
| Base de datos de Access  | Carpeta* | Base de datos de Oracle  | Base de datos de Teradata |
| Active Directory*  | Documento JSON*  | Base de datos de Postgre SQL*  |Tabla XML* |
| Analysis Services  | Líneas de archivo binario*  | SAP HANA*  |
| Analytics Platform System  | Base de datos MySQL  | SAP Business Warehouse*  | |
| Dynamics CRM*  | Fuente OData*  | SharePoint*  |
| Libro de Excel  | Consulta ODBC  | SQL Database  |
| Exchange*  | OLE DB  | Base de datos de Sybase  |

\* Solo modelos tabulares 1400. 

> [!IMPORTANT]
> Para establecer conexión con orígenes de datos locales se debe instalar una [puerta de enlace de datos locales](analysis-services-gateway.md) en un equipo de su entorno.

## <a name="data-providers"></a>Proveedores de datos

Los modelos de datos de Azure Analysis Services pueden requerir distintos proveedores de datos al conectarse a algunos orígenes de datos. En algunos casos, los modelos tabulares se conectan a orígenes de datos mediante proveedores nativos como SQL Server Native Client (SQLNCLI11) pueden devolver un error.

Para los modelos de datos que se conectan a un origen de datos en la nube, como Azure SQL Database, si usa proveedores nativos que no sean SQLOLEDB, puede ver el mensaje de error: **"El proveedor 'SQLNCLI11.1' no está registrado"**. O bien, si tiene un modelo DirectQuery conectarse a orígenes de datos locales, si utiliza los proveedores nativos, puede ver el mensaje de error: **"Error al crear el conjunto de filas OLE DB. Sintaxis incorrecta cerca de 'LIMIT'”**.

Los siguientes proveedores de orígenes de datos se admiten en los modelos de datos en memoria o DirectQuery cuando se conectan a orígenes de datos locales o en la nube:

### <a name="cloud"></a>Nube
| **Origen de datos** | **En memoria** | **DirectQuery** |
|  --- | --- | --- |
| Almacenamiento de datos SQL de Azure |Proveedor de datos .NET Framework para SQL Server |Proveedor de datos .NET Framework para SQL Server |
| Base de datos SQL de Azure |Proveedor de datos .NET Framework para SQL Server |Proveedor de datos .NET Framework para SQL Server | |

### <a name="on-premises-via-gateway"></a>Local (mediante una puerta de enlace)
|**Origen de datos** | **En memoria** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |Proveedor de datos .NET Framework para SQL Server |
| SQL Server |Proveedor OLE DB de Microsoft para SQL Server |Proveedor de datos .NET Framework para SQL Server | |
| SQL Server |Proveedor de datos .NET Framework para SQL Server |Proveedor de datos .NET Framework para SQL Server | |
| Oracle |Proveedor OLE DB de Microsoft para Oracle |Proveedor de datos de Oracle para .NET | |
| Oracle |Proveedor de datos de Oracle para .NET |Proveedor de datos de Oracle para .NET | |
| Teradata |Proveedor OLE DB para Teradata |Proveedor de datos de Teradata para .NET | |
| Teradata |Proveedor de datos de Teradata para .NET |Proveedor de datos de Teradata para .NET | |
| Analytics Platform System |Proveedor de datos .NET Framework para SQL Server |Proveedor de datos .NET Framework para SQL Server | |

> [!NOTE]
> Asegúrese de que estén instalados proveedores de 64 bits cuando se use la puerta de enlace local.
> 
> 

Cuando se migra un modelo tabular de SQL Server Analysis Services local a Azure Analysis Services, puede ser necesario cambiar el proveedor.

**Para especificar un proveedor de origen de datos**

1. En SSDT > **Tabular Model Explorer**(Explorador de modelos tabulares) > **Orígenes de datos**, haga clic en una conexión de origen de datos y en **Editar origen de datos**.
2. En **Editar conexión**, haga clic en **Avanzadas** para abrir la ventana de propiedades avanzadas.
3. En **Establecer propiedades avanzadas** > **Proveedores**, seleccione el proveedor adecuado.

## <a name="impersonation"></a>Suplantación
En algunos casos, puede ser necesario especificar otra cuenta de suplantación. La cuenta de suplantación se puede especificar en SSDT o SSMS.

Para orígenes de datos locales:

* Si se utiliza la autenticación de SQL, la suplantación debe ser la Cuenta de servicio.
* Si se utiliza la autenticación de Windows, establezca la contraseña y el usuario de Windows. Para SQL Server, se admite la autenticación de Windows con una cuenta de suplantación específica solo para los modelos de datos en memoria.

Para orígenes de datos en la nube:

* Si se utiliza la autenticación de SQL, la suplantación debe ser la Cuenta de servicio.

## <a name="next-steps"></a>Pasos siguientes
Si tiene orígenes de datos locales, asegúrese de instalar la [puerta de enlace local](analysis-services-gateway.md).   
Para aprender más acerca de la administración del servidor en SSDT o SSMS, consulte el artículo sobre la[administración del servidor](analysis-services-manage.md).

