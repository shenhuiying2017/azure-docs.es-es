---
title: "Conexión a Azure SQL Database mediante Python | Microsoft Docs"
description: "Este tema muestra un ejemplo de código Pyton que puede usar para conectarse a la base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 03/27/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 91e1dcd5b4a7dc62a09c9deb26622dacba1dcaa1
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database: uso de Python para conectar y consultar datos

Use [Python](https://python.org) para conectarse a una instancia de Azure SQL Database y realizar consultas en ella. En esta guía se explica cómo utilizar Python para conectarse a una instancia de Azure SQL Database y, después, ejecutar la consulta, insertar instrucciones, actualizarlas y eliminarlas.

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>Configuración del entorno de desarrollo
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
Instale [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339). pyodbc usa Microsoft ODBC Driver on Linux para conectarse a bases de datos SQL. 

Luego, instale pyodbc con pip

```cmd
pip install pyodbc==3.1.1
```

Se pueden encontrar instrucciones para habilitar el uso de pip [aquí](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtenga la cadena de conexión en Azure Portal. La cadena de conexión se usa para conectarse a Azure SQL Database.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En el panel **Essentials** de la base de datos, revise el nombre completo del servidor. 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
   
## <a name="select-data"></a>Selección de datos
Use la función [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) con una instrucción [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) de Transact-SQL para consultar los datos de la instancia de Azure SQL Database. La función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) puede usarse para recuperar un conjunto de resultados de una consulta realizada a la base de datos SQL. Esta función acepta cualquier consulta y devuelve un conjunto de resultados que se puede iterar mediante el uso de [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html).

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
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
En SQL Database, la propiedad [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) y el objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente los valores de [clave principal](https://msdn.microsoft.com/library/ms179610.aspx). 

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>Actualización de datos
La función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) se puede usar para hacer una instrucción [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) de Transact-SQL con el fin de actualizar sus datos en la instancia de Azure SQL Database.

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```


## <a name="delete-data"></a>Eliminación de datos
La función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) se puede usar para hacer una instrucción [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) de Transact-SQL con el fin de eliminar sus datos en la instancia de Azure SQL Database.

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md).
* Puede encontrar más información en [Microsoft Python Driver para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/).
* Visite el [Centro para desarrolladores de Python](/develop/python/).
* Explore todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/).

