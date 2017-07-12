---
title: "Conexión a Azure Database for PostgreSQL mediante el lenguaje Go | Microsoft Docs"
description: "En este tutorial rápido se proporciona un ejemplo del lenguaje de programación Go que puede usar para conectarse a Azure Database for PostgreSQL y consultar datos en este servicio."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017

---

<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>

# Azure Database for PostgreSQL: uso del lenguaje Go para conectarse y consultar datos
En este tutorial rápido se muestra cómo conectarse a una instancia de Azure Database for PostgreSQL mediante código escrito en el lenguaje [Go](https://golang.org/). Se indica cómo usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos en la base de datos. En este artículo se da por hecho que está familiarizado con el desarrollo mediante Go, pero que nunca ha trabajado con Azure Database para PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Requisitos previos
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- [Creación de la base de datos: Azure Portal](quickstart-create-server-database-portal.md)
- [Creación de la base de datos: CLI de Azure](quickstart-create-server-database-azure-cli.md)

<a id="install-go-and-pq" class="xliff"></a>

## Instalación de Go y pq
- Descargue e instale Go de acuerdo con las [instrucciones de instalación](https://golang.org/doc/install) que coincidan con su plataforma.
- Cree una carpeta para su proyecto, como C:\Postgresql\. Mediante la línea de comandos, cambie el directorio por la carpeta del proyecto, por ejemplo `cd C:\Postgres\`.
- Descargue el [controlador puro de Postgres para Go (pq)](https://github.com/lib/pq) en la carpeta del proyecto; para ello, escriba el comando `go get github.com/lib/pq` mientras está en el mismo directorio.

<a id="build-and-run-go-code" class="xliff"></a>

## Compilación y ejecución del código de Go 
- Guarde el código en un archivo de texto con la extensión *.go y guárdelo en la carpeta del proyecto, por ejemplo, `C:\postgres\read.go`.
- Para ejecutar el código, cambie el directorio por la carpeta del proyecto `cd postgres`, escriba el comando `go run read.go` para compilar la aplicación y ejecútelo.
- Para compilar el código en una aplicación nativa, `go build read.go`, inicie read.exe para ejecutar la aplicación.

<a id="get-connection-information" class="xliff"></a>

## Obtención de información sobre la conexión
Obtenga la información de conexión necesaria para conectarse a Azure Database for PostgreSQL. Necesitará el nombre completo del servidor y las credenciales de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú izquierdo de Azure Portal, haga clic en **Todos los recursos** y busque el servidor que ha creado, por ejemplo, **mypgserver-20170401**.
3. Haga clic en el nombre del servidor **mypgserver-20170401**.
4. Seleccione la página **Introducción** del servidor. Tome nota del **Nombre del servidor** y del **Server admin login name** (Nombre de inicio de sesión del administrador del servidor).
 ![Azure Database for PostgreSQL: inicio de sesión del Administrador del servidor](./media/connect-go/1-connection-string.png)
5. Si olvida la información de inicio de sesión del servidor, navegue hasta la página **Información general** y vea el nombre de inicio de sesión del Administrador del servidor. Si es necesario, restablezca la contraseña.

<a id="connect-and-create-a-table" class="xliff"></a>

## Conexión y creación de una tabla
Use el código siguiente para conectarse y crear una tabla mediante la instrucción SQL **CREATE TABLE**, seguida de las instrucciones SQL **INSERT INTO** para agregar filas a la tabla.

El código importa tres paquetes: el [paquete sql](https://golang.org/pkg/database/sql/), el [paquete pq](http://godoc.org/github.com/lib/pq), como controlador para la comunicación con el servidor de Postgres, y el [paquete fmt](https://golang.org/pkg/fmt/) para la entrada y la salida impresas en la línea de comandos.

El código llama al método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para conectarse a Azure Database for PostgreSQL y comprueba la conexión mediante el método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Durante todo el proceso se usa un [identificador de base de datos](https://golang.org/pkg/database/sql/#DB), que contiene el grupo de conexiones del servidor de base de datos. El código llama al método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) varias veces para ejecutar varios comandos SQL. En todo momento, un método checkError() personalizado comprueba si se ha producido un error y, en caso afirmativo, avisa para salir.


Reemplace los parámetros `HOST`, `DATABASE`, `USER` y `PASSWORD` por sus propios valores. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

<a id="read-data" class="xliff"></a>

## Lectura de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **SELECT**. 

El código importa tres paquetes: el [paquete sql](https://golang.org/pkg/database/sql/), el [paquete pq](http://godoc.org/github.com/lib/pq), como controlador para la comunicación con el servidor de Postgres, y el [paquete fmt](https://golang.org/pkg/fmt/) para la entrada y la salida impresas en la línea de comandos.

El código llama al método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para conectarse a Azure Database for PostgreSQL y comprueba la conexión mediante el método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Durante todo el proceso se usa un [identificador de base de datos](https://golang.org/pkg/database/sql/#DB), que contiene el grupo de conexiones del servidor de base de datos. Para ejecutar la consulta de selección, se llama al método [db.Query()](https://golang.org/pkg/database/sql/#DB.Query), y las filas resultantes se guardan en una variable de tipo [rows](https://golang.org/pkg/database/sql/#Rows). El código lee los valores de los datos de las columnas de la fila actual mediante el método [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) y recorre las filas mediante el iterador [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) hasta que no existen más filas. Los valores de las columnas de cada fila se imprimen en la salida de la consola. En todo momento, un método checkError() personalizado comprueba si se ha producido un error y, en caso afirmativo, avisa para salir.

Reemplace los parámetros `HOST`, `DATABASE`, `USER` y `PASSWORD` por sus propios valores. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

<a id="update-data" class="xliff"></a>

## Actualización de datos
Use el código siguiente para conectarse y actualizar los datos mediante la instrucción SQL **UPDATE**.

El código importa tres paquetes: el [paquete sql](https://golang.org/pkg/database/sql/), el [paquete pq](http://godoc.org/github.com/lib/pq), como controlador para la comunicación con el servidor de Postgres, y el [paquete fmt](https://golang.org/pkg/fmt/) para la entrada y la salida impresas en la línea de comandos.

El código llama al método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para conectarse a Azure Database for PostgreSQL y comprueba la conexión mediante el método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Durante todo el proceso se usa un [identificador de base de datos](https://golang.org/pkg/database/sql/#DB), que contiene el grupo de conexiones del servidor de base de datos. El código llama al método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para ejecutar la instrucción SQL que actualiza la tabla. Un método checkError() personalizado comprueba si se ha producido un error y, en caso afirmativo, avisa para salir.

Reemplace los parámetros `HOST`, `DATABASE`, `USER` y `PASSWORD` por sus propios valores. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

<a id="delete-data" class="xliff"></a>

## Eliminación de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **DELETE**. 

El código importa tres paquetes: el [paquete sql](https://golang.org/pkg/database/sql/), el [paquete pq](http://godoc.org/github.com/lib/pq), como controlador para la comunicación con el servidor de Postgres, y el [paquete fmt](https://golang.org/pkg/fmt/) para la entrada y la salida impresas en la línea de comandos.

El código llama al método [sql.Open()](http://godoc.org/github.com/lib/pq#Open) para conectarse a Azure Database for PostgreSQL y comprueba la conexión mediante el método [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Durante todo el proceso se usa un [identificador de base de datos](https://golang.org/pkg/database/sql/#DB), que contiene el grupo de conexiones del servidor de base de datos. El código llama al método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para ejecutar la instrucción SQL que actualiza la tabla. Un método checkError() personalizado comprueba si se ha producido un error y, en caso afirmativo, avisa para salir.

Reemplace los parámetros `HOST`, `DATABASE`, `USER` y `PASSWORD` por sus propios valores. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./howto-migrate-using-export-and-import.md)

