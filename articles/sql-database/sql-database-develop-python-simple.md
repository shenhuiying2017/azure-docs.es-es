---
title: Conexión a Base de datos SQL mediante Python | Microsoft Docs
description: Este tema muestra un ejemplo de código Pyton que puede usar para conectarse a la base de datos SQL de Azure.
services: sql-database
documentationcenter: ''
author: meet-bhagdev
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/05/2016
ms.author: meetb

---
# <a name="connect-to-sql-database-by-using-python"></a>Conexión a Base de datos SQL mediante Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

En este tema se muestra cómo conectarse a una instancia de Azure SQL Database y consultarla mediante Python. Puede ejecutar esta muestra desde las plataformas Windows, Ubuntu Linux o Mac.

## <a name="step-1:-create-a-sql-database"></a>Paso 1: Creación de una base de datos SQL
Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo.  Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**. Una vez creada la base de datos, asegúrese de habilitar el acceso a su dirección IP mediante la habilitación de las reglas de firewall como se describe en la [página de introducción](sql-database-get-started.md).

## <a name="step-2:-configure-development-environment"></a>Paso 2: Configuración del entorno de desarrollo
### <a name="**mac-os**"></a>**Mac OS**
### <a name="install-the-required-modules"></a>Instalación de los módulos necesarios
Abra el terminal e instale

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="**linux-(ubuntu)**"></a>**Linux (Ubuntu)**
Abra el terminal y desplácese hasta el directorio donde planea crear secuencias de comandos de Python. Escriba los comandos siguientes para instalar **FreeTDS** y **pymssql**. pymssql usa FreeTDS para conectarse a las bases de datos SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1

### <a name="**windows**"></a>**Windows**
Instale pymssql desde [**aquí**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Asegúrese de elegir el archivo whl correcto. Por ejemplo: si va a usar Python 2.7 en un equipo de 64 bits, elija pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Una vez descargado el archivo .whl, colóquelo en la carpeta C:/Python27.

Instale ahora el controlador pymssql con pip desde la línea de comandos. cd into C:/Python27 y ejecute lo siguiente:

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Se pueden encontrar instrucciones para habilitar el uso de pip [aquí](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3:-run-sample-code"></a>Paso 3: Ejecución del código de ejemplo
Cree un archivo llamado **sql_sample.py** y pegue dentro de él el código siguiente. Puede ejecutar esto desde la línea de comandos mediante:

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Conexión a la base de datos SQL
La función [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) se usa para conectarse a la base de datos SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Ejecución de la instrucción SQL SELECT
La función [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) puede usarse para recuperar un conjunto de resultados de una consulta realizada a la base de datos SQL. Esta función acepta cualquier consulta y devuelve un conjunto de resultados que se puede iterar mediante el uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row,-pass-parameters,-and-retrieve-the-generated-primary-key"></a>Inserción de filas, paso de parámetros y recuperación de la clave principal generada
En SQL Database, la propiedad [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) y el objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente los valores de [clave principal](https://msdn.microsoft.com/library/ms179610.aspx). 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
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

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
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

<!--HONumber=Oct16_HO2-->


