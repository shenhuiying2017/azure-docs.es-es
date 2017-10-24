---
title: "Conexión a Azure Database for MySQL desde C# | Microsoft Docs"
description: "En este tutorial rápido se proporciona un ejemplo de código de C# (.NET) que puede usar para conectarse a Azure Database for MySQL y consultar datos en este servicio."
services: MySQL
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: MySQL
ms.custom: mvc
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: f87c3c302ec25f33af4334c3753dfae0084e4393
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-net-c-to-connect-and-query-data"></a>Azure Database for MySQL: uso de .NET (C#) para conectarse y consultar datos
En este tutorial rápido se muestra cómo conectarse a una instancia de Azure Database for MySQL mediante una aplicación de C#. Se indica cómo usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos en la base de datos. En este tema se da por hecho que está familiarizado con el desarrollo mediante C# y que nunca ha usado Azure Database for MySQL.

## <a name="prerequisites"></a>Requisitos previos
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- [Create an Azure Database for MySQL server using Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)
- [Create an Azure Database for MySQL server using Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) (Creación de un servidor de Azure Database for MySQL mediante la CLI de Azure)

Además, deberá:
- Instale [.NET](https://www.microsoft.com/net/download). Siga los pasos descritos en el artículo vinculado para instalar .NET específicamente para su plataforma (Windows, Ubuntu Linux o macOS). 
- Instale [Visual Studio](https://www.visualstudio.com/downloads/).
- Instale [ODBC Driver for MySQL](https://dev.mysql.com/downloads/connector/odbc/).

## <a name="get-connection-information"></a>Obtención de información sobre la conexión
Obtenga la información de conexión necesaria para conectarse a Azure Database for MySQL. Necesitará el nombre completo del servidor y las credenciales de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú izquierdo de Azure Portal, haga clic en **Todos los recursos** y, luego, busque el servidor que ha creado, por ejemplo, **myserver4demo**.
3. Haga clic en el nombre del servidor.
4. Seleccione la página **Propiedades** del servidor y anote la información que figura en **Nombre del servidor** y en **Nombre de inicio de sesión del administrador del servidor**.
 ![Nombre del servidor de Azure Database for MySQL](./media/connect-csharp/1_server-properties-name-login.png)
5. Si olvida la información de inicio de sesión del servidor, navegue hasta la página **Información general** para ver el nombre de inicio de sesión del administrador del servidor y, si es necesario, restablecer la contraseña.

## <a name="connect-create-table-and-insert-data"></a>Conexión, creación de una tabla e inserción de datos
Use el código siguiente para conectarse y cargar los datos mediante las instrucciones SQL **CREATE TABLE** e **INSERT INTO**. El código usa la clase ODBC con el método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para establecer una conexión con MySQL. A continuación, el código usa el método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), establece la propiedad CommandText y llama al método [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) para ejecutar los comandos de base de datos. 

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;

namespace driver
{
    class MySQLCreate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "DROP TABLE IF EXISTS inventory;";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished dropping table (if existed)");

            command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished creating table");

            command.CommandText =
                String.Format(
                    @"INSERT INTO inventory (name, quantity) VALUES ({0}, {1});
                    INSERT INTO inventory (name, quantity) VALUES ({2}, {3});
                    INSERT INTO inventory (name, quantity) VALUES ({4}, {5});",
                    "\'banana\'", 150,
                    "\'orange\'", 154,
                    "\'apple\'", 100
                    );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }

    }
}

```

## <a name="read-data"></a>Lectura de datos

Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **SELECT**. El código usa la clase ODBC con el método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para establecer una conexión con MySQL. A continuación, el código usa el método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) y el método [ExecuteReader()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executereader(v=vs.110).aspx) para ejecutar los comandos de base de datos. A continuación, el código usa [Read()](https://msdn.microsoft.com/library/system.data.odbc.odbcdatareader.read(v=vs.110).aspx) para avanzar a los registros de los resultados. A continuación, el código usa GetInt32() y GetString() para analizar los valores del registro.

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;


namespace driver
{
    class MySQLRead
    {

        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "SELECT * FROM inventory;";

            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(
                    string.Format(
                        "Reading from table=({0}, {1}, {2})",
                        reader.GetInt32(0).ToString(),
                        reader.GetString(1),
                        reader.GetInt32(2).ToString()
                        )
                    );
            }

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

## <a name="update-data"></a>Actualización de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **UPDATE**. El código usa la clase ODBC con el método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para establecer una conexión con MySQL. A continuación, el código usa el método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), establece la propiedad CommandText y llama al método [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) para ejecutar los comandos de base de datos.

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLUpdate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("UPDATE inventory SET quantity = {0} WHERE name = {1};",
                200,
                "\'banana\'"
                );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}



```


## <a name="delete-data"></a>Eliminación de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **DELETE**. 

El código usa la clase ODBC con el método [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) para establecer una conexión con MySQL. A continuación, el código usa el método [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), establece la propiedad CommandText y llama al método [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) para ejecutar los comandos de base de datos.

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLDelete
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
                String.Format("DELETE FROM inventory WHERE name = {0};",
                    "\'orange\'");
            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}

```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Migre su Base de datos MySQL a Azure Database for MySQL mediante el volcado y la restauración](concepts-migrate-dump-restore.md)
