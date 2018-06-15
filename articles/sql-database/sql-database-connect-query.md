---
title: Guías de inicio rápido de conexión y consulta de Azure SQL Database | Microsoft Docs
description: Guías de inicio rápido de Azure SQL Database que muestran cómo conectarse a una instancia de Azure SQL Database y consultarla.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc
ms.devlang: ''
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: carlrab
ms.openlocfilehash: ec39c5ad0771c2bc78655e52c58949db6e9b3353
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186018"
---
# <a name="azure-sql-database-connect-and-query-quickstarts"></a>Guías de inicio rápido de conexión y consulta de Azure SQL Database

En el documento siguiente se incluyen vínculos a ejemplos de Azure que muestran cómo conectarse a una instancia de Azure SQL Database y realizar consultas en ella. También se proporciona algunas recomendaciones de seguridad de nivel de transporte.

## <a name="quickstarts"></a>Guías de inicio rápido

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Este inicio rápido muestra cómo usar SSMS para conectarse a una base de datos de SQL Azure Database, para después usar las instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos en la base de datos.|
|[SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Esta guía de inicio rápido muestra cómo se usa SQL Operations Studio (versión preliminar) para conectarse a una instancia de Azure SQL Database y que, a continuación, usa instrucciones Transact-SQL (T-SQL) para crear el TutorialDB que se utiliza en los tutoriales de SQL Operations Studio (versión preliminar).|
|[Azure Portal](sql-database-connect-query-portal.md)|Esta guía de inicio rápido muestra cómo usar el Editor de consultas para conectarse a una base de datos SQL y, después, usar instrucciones Transact-SQL para realizar consultas, insertar datos, actualizarlos y eliminarlos de la base de datos.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Esta guía de inicio rápido muestra cómo usar Visual Studio Code para conectarse a una base de datos de SQL Azure Database y después usar las instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos en la base de datos.|
|[.NET con Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Esta guía de inicio rápido muestra cómo se usa .NET Framework para crear un programa de C# con Visual Studio que se conecte a una instancia de Azure SQL Database y que use instrucciones Transact-SQL para consultar los datos.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Esta guía de inicio rápido muestra cómo se usa .NET Core en Windows, Linux o macOS para crear un programa de C# que se conecte a una instancia de Azure SQL Database y que use instrucciones Transact-SQL para consultar los datos.|
|[Go](sql-database-connect-query-go.md)|En esta guía de inicio rápido se muestra cómo usar Go para conectarse a una instancia de Azure SQL Database. También se muestran las instrucciones Transact-SQL para consultar y modificar los datos.|
|[Java](sql-database-connect-query-java.md)|Esta guía de inicio rápido muestra cómo utilizar Java para conectarse a una instancia de Azure SQL Database y luego utilizar instrucciones Transact-SQL para consultar los datos.|
|[Node.js](sql-database-connect-query-nodejs.md)|En esta guía de inicio rápido se muestra cómo se usa Node.js para crear un programa que se conecte a una instancia de Azure SQL Database y que use instrucciones Transact-SQL para consultar los datos.|
|[PHP](sql-database-connect-query-php.md)|En esta guía de inicio rápido se muestra cómo se usa PHP para crear un programa que se conecte a una instancia de Azure SQL Database y que use instrucciones Transact-SQL para consultar los datos.|
|[Python](sql-database-connect-query-python.md)|Esta guía de inicio rápido muestra cómo se usa Python para conectarse a una instancia de Azure SQL Database y usar instrucciones Transact-SQL para consultar los datos. |
|[Ruby](sql-database-connect-query-ruby.md)|En esta guía de inicio rápido se muestra cómo se usa Ruby para crear un programa que se conecte a una instancia de Azure SQL Database y que use instrucciones Transact-SQL para consultar los datos.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Consideraciones de TLS para la conectividad de SQL Database
El protocolo Seguridad de capa de transporte (TLS) se usa en todos los controladores que Microsoft proporciona o admite para la conexión a Azure SQL Database. No es necesario realizar ninguna configuración especial. En todas las conexiones a SQL Server o Azure SQL Database, se recomienda que todas las aplicaciones establezcan las siguientes configuraciones o sus equivalentes:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Algunos sistemas usan palabras clave diferentes pero equivalentes para esas palabras clave de configuración. Estas configuraciones garantizan que el controlador cliente comprueba la identidad del certificado TLS recibido del servidor.

También se recomienda deshabilitar TLS 1.1 y 1.0 en el cliente si debe cumplir con el estándar de seguridad de datos de la industria de tarjetas de pago, o PCI-DSS (Payment Card Industry - Data Security Standard).

Puede que los controladores que no son de Microsoft no usen TLS de forma predeterminada. Esto puede influir al conectarse a Azure SQL Database. Las aplicaciones con controladores insertados quizás no le permitan controlar estas configuraciones de conexión. Se recomienda que examine la seguridad de estos controladores y aplicaciones antes de usarlos en sistemas que interactúan con datos confidenciales.

## <a name="next-steps"></a>Pasos siguientes

Para información sobre la arquitectura de conectividad, consulte [Arquitectura de conectividad de Azure SQL Database](sql-database-connectivity-architecture.md).