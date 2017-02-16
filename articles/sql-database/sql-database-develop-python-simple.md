---
title: "Conexión a SQL Database mediante Python | Microsoft Docs"
description: "Este tema muestra un ejemplo de código Pyton que puede usar para conectarse a la base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 01/03/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 631baac839f4045c4b0fcf23810d9459c45a4998
ms.openlocfilehash: 558d6660235a76bc7f5d23e7b28025496c2d8271


---
# <a name="connect-to-sql-database-by-using-python"></a>Conexión a Base de datos SQL mediante Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

En este tema se muestra cómo conectarse a una instancia de Azure SQL Database y consultarla mediante Python. Puede ejecutar esta muestra desde las plataformas Windows, Ubuntu Linux o Mac.

## <a name="step-1-create-a-sql-database"></a>Paso 1: Creación de una base de datos SQL
Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo.  Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**. Una vez creada la base de datos, asegúrese de habilitar el acceso a su dirección IP mediante la habilitación de las reglas de firewall como se describe en la [página de introducción](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Paso 2: Configuración del entorno de desarrollo
### <a name="mac-os"></a>**Mac OS**
Abra el terminal y desplácese hasta el directorio donde planea crear secuencias de comandos de Python. Escriba los comandos siguientes para instalar **brew**, **FreeTDS** y **pyodbc**. pyodbc usa FreeTDS en MacOS para conectarse a bases de datos SQL.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew uninstall FreeTDS #if you have an existing installed FreeTDS
    brew update
    brew doctor
    brew install freetds --with-unixodbc
    sudo pip install pyodbc==3.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra el terminal y desplácese hasta el directorio donde planea crear secuencias de comandos de Python. Escriba los comandos siguientes para instalar **Microsoft ODBC Driver for Linux** y **pyodbc**. pyodbc usa Microsoft ODBC Driver on Linux para conectarse a bases de datos SQL.

    sudo su
    curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
    curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
    exit
    sudo apt-get update
    sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
    sudo pip install pyodbc==3.1.1

### <a name="windows"></a>**Windows**
Instale [Microsoft ODBC Driver 13.1](https://www.microsoft.com/en-us/download/details.aspx?id=53339). pyodbc usa Microsoft ODBC Driver on Linux para conectarse a bases de datos SQL. 

Luego, instale pyodbc con pip

    pip install pyodbc==3.1.1

Se pueden encontrar instrucciones para habilitar el uso de pip [aquí](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3-run-sample-code"></a>Paso 3: Ejecución del código de ejemplo
Cree un archivo llamado **sql_sample.py** y pegue dentro de él el código siguiente. Puede ejecutar esto desde la línea de comandos mediante:

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Conexión a la base de datos SQL
La función [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) se usa para conectarse a SQL Database.

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux of windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    if row:
        print row

### <a name="execute-an-sql-select-statement"></a>Ejecución de la instrucción SQL SELECT
La función [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) puede usarse para recuperar un conjunto de resultados de una consulta realizada a la base de datos SQL. Esta función acepta cualquier consulta y devuelve un conjunto de resultados que se puede iterar mediante el uso de [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html).

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])     
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserción de filas, paso de parámetros y recuperación de la clave principal generada
En SQL Database, la propiedad [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) y el objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente los valores de [clave principal](https://msdn.microsoft.com/library/ms179610.aspx). 

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transacciones
Este ejemplo de código muestra el uso de transacciones con las que podrá realizar lo siguiente:

* Iniciar una transacción
* Insertar una fila de datos
* Revertir la transacción para deshacer la inserción 

Pegar el código siguiente dentro de sql_sample.py.

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* Puede encontrar más información en [Microsoft Python Driver para SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visite el [Centro para desarrolladores de Python](/develop/python/).

## <a name="additional-resources"></a>Recursos adicionales
* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Jan17_HO1-->


