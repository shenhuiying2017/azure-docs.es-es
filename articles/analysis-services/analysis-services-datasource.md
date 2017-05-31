---
title: Conexiones a origen de datos | Microsoft Docs
description: "Describe las conexiones a orígenes de datos para los modelos de datos en Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/14/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: aebabd15e781e726def545960ce3c1ec7674c530
ms.contentlocale: es-es
ms.lasthandoff: 05/05/2017


---
# <a name="datasource-connections"></a>Conexiones a origen de datos
Los modelos de datos de Azure Analysis Services pueden requerir distintos proveedores de datos al conectarse a algunos orígenes de datos. En algunos casos, los modelos tabulares se conectan a orígenes de datos mediante proveedores nativos como SQL Server Native Client (SQLNCLI11) pueden devolver un error.

Para modelos de datos en memoria o DirectQuery que se conectan a un origen de datos en la nube, como Azure SQL Database, si usa proveedores nativos que no sean SQLOLEDB, puede ver el mensaje de error: **"El proveedor 'SQLNCLI11.1' no está registrado"**. O bien, si tiene un modelo DirectQuery conectarse a orígenes de datos locales, si utiliza los proveedores nativos, puede ver el mensaje de error: **"Error al crear el conjunto de filas OLE DB. Sintaxis incorrecta cerca de 'LIMIT'”**.

## <a name="data-source-providers"></a>Proveedores de orígenes de datos
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


