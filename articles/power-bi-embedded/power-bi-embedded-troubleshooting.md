---
title: "Solución de problemas de la versión preliminar de Microsoft Power BI Embedded"
description: "Solución de problemas de la versión preliminar de Microsoft Power BI Embedded"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: c8aee652-ed8b-4c66-9c63-f798b7a655b4
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f4cf4a04e7a921f8e11c94f74d53ac80c231933


---
# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Solución de problemas de la versión preliminar de Microsoft Power BI Embedded
Este artículo ofrece respuestas sobre cómo solucionar problemas de **Power BI Embedded**.

<a name="connection-string"/>

## <a name="setting-sql-server-connection-strings"></a>Configuración de cadenas de conexión de SQL Server
Para establecer una cadena de conexión de SQL Server, debe seguir un formato específico. A continuación se muestra una cadena de conexión de ejemplo para SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Para obtener más información sobre las cadenas de conexión de SQL Server, consulte los artículos siguientes:

* [SQL Server Connection Strings](https://msdn.microsoft.com/library/jj653752.aspx)
* [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>

## <a name="setting-credentials"></a>Configuración de credenciales
En el caso en el que tiene credenciales para un entorno de desarrollo o ensayo, como un nombre de usuario y una contraseña, podría tener que actualizar las credenciales que coincidan con una solución de producción.

## <a name="see-also"></a>Otras referencias
* [Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](power-bi-embedded-get-started-sample.md)
* [What is Microsoft Power BI Embedded (¿Qué es Microsoft Power BI Embedded?)](power-bi-embedded-what-is-power-bi-embedded.md)




<!--HONumber=Nov16_HO3-->


