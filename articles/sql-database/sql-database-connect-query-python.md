---
title: Uso de Python para consultar Azure SQL Database | Microsoft Docs
description: "En este tema se muestra cómo usar Python para crear un programa que se conecta a una base de datos SQL de Azure y realiza consultas mediante instrucciones Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 07/11/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b7c217be41b979f8a7246109cc95a01341dadf3d
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="use-python-to-query-an-azure-sql-database"></a>Uso de Python para consultar una base de datos SQL de Azure

 Esta guía de introducción muestra cómo utilizar [Python](https://python.org) para conectarse a una base de datos SQL de Azure y utilizar instrucciones Transact-SQL para consultar los datos.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de que dispone de lo siguiente:

- Una base de datos SQL de Azure. En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías: 

   - [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
   - [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
   - [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

- Una [regla de firewall de nivel de servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para la dirección IP pública del equipo que usa para seguir este tutorial.

- Ha instalado Python y el software relacionado para el sistema operativo.

    - **MacOS**: instalación de Homebrew y Python, instalación del controlador ODBC y SQLCMD y, a continuación, instalación del controlador Python para SQL Server. Consulte [pasos 1.2, 1.3 y 2.1](https://www.microsoft.com/sql-server/developer-get-started/Python/mac/).
    - **Ubuntu**: instalación de Python y otros paquetes necesarios y, a continuación, instalación del controlador Python para SQL Server. Consulte [pasos 1.2 y 2.1](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
    - **Windows**: instalación de la versión más reciente de Python (la variable de entorno ya viene configurada), instalación del controlador ODBC y SQLCMD y, a continuación, instalación del controlador Python para SQL Server. Consulte [pasos 1.2, 1.3 y 2.1](https://www.microsoft.com/sql-server/developer-get-started/node/windows/). 

## <a name="sql-server-connection-information"></a>Información de conexión de SQL server

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor tal como se muestra en la siguiente imagen. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si olvida la información de inicio de sesión del servidor, navegue hasta la página del servidor de SQL Database para ver el nombre de administrador del servidor y, si es necesario, restablecer la contraseña.     
    
## <a name="insert-code-to-query-sql-database"></a>Inserción de código para consultar la base de datos SQL 

1. En el editor de texto, cree un nuevo archivo, **sqltest.py**.  

2. Reemplace el contenido con el código siguiente y agregue los valores adecuados para el servidor, la base de datos, el usuario y la contraseña.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>Ejecución del código

1. En el símbolo del sistema, ejecute los siguientes comandos:

   ```Python
   python sqltest.py
   ```

2. Compruebe que se han devuelto las primeras 20 filas y, a continuación, cierre la ventana de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Controladores de Microsoft para Python para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro para desarrolladores de Python](https://azure.microsoft.com/develop/python/?v=17.23h)


