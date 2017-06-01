---
title: "Conexión a Azure SQL Database mediante Python | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código Python que puede usar para conectarse a Azure SQL Database y realizar consultas."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 5fae11119500fd3be3af3e573d45f6cc5880e037
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017

---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database: uso de Python para conectar y consultar datos

 En esta guía de inicio rápido se muestra cómo usar [Python](https://python.org) para conectarse a una base de datos de Azure SQL Database y, después, usar instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos de la base de datos desde las plataformas Mac OS, Ubuntu Linux y Windows.

## <a name="prerequisites"></a>Requisitos previos

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
- [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-the-python-and-database-communication-libraries"></a>Instalación de bibliotecas de comunicación de bases de datos y Python

En esta sección se da por hecho que está familiarizado con el desarrollo mediante Python y que nunca ha utilizado Azure SQL Database. Si no está familiarizado con el desarrollo con Python, vaya a [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **Python** y, a continuación, seleccione el sistema operativo.

### <a name="mac-os"></a>**Mac OS**
Abra el terminal y desplácese hasta el directorio donde planea crear secuencias de comandos de Python. Escriba los comandos siguientes para instalar **brew**, **Microsoft ODBC Driver for Mac** y **pyodbc**. pyodbc usa Microsoft ODBC Driver on Linux para conectarse a bases de datos SQL.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra el terminal y desplácese hasta el directorio donde planea crear secuencias de comandos de Python. Escriba los comandos siguientes para instalar **Microsoft ODBC Driver for Linux** y **pyodbc**. pyodbc usa Microsoft ODBC Driver on Linux para conectarse a bases de datos SQL.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Instale [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) (actualice el controlador si se le solicita). Pyodbc usa Microsoft ODBC Driver on Linux para conectarse a bases de datos SQL. 

Luego, instale **pyodbc** con pip.

```cmd
pip install pyodbc==3.1.1
```

Se pueden encontrar instrucciones para habilitar el uso de pip [aquí](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor, tal como se muestra en la imagen siguiente. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si olvida la información de inicio de sesión del servidor, navegue hasta la página del servidor de SQL Database para ver el nombre de administrador del servidor y, si es necesario, restablecer la contraseña.     
   
## <a name="select-data"></a>Selección de datos

Utilice el código siguiente para consultar los 20 primeros productos por categoría mediante la función [pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) con una instrucción [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) de Transact-SQL. La función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) se usa para recuperar un conjunto de resultados de una consulta realizada a SQL Database. Esta función acepta una consulta y devuelve un conjunto de resultados que se puede iterar mediante el uso de [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html). Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

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
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>Insertar datos
Utilice el código siguiente para insertar un nuevo producto en la tabla SalesLT.Product mediante la función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) y la instrucción [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>Actualización de datos
Utilice el código siguiente para actualizar el nuevo producto que ha agregado anteriormente mediante la función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) y la instrucción [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>Eliminación de datos
Utilice el código siguiente para eliminar el nuevo producto que ha agregado anteriormente mediante la función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) y la instrucción [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>Pasos siguientes

- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Controladores de Microsoft para Python para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro para desarrolladores de Python](/develop/python/)


