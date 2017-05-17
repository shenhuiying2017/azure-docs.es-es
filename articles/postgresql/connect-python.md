---
title: Lectura en Python desde Azure Database for PostgreSQL | Microsoft Docs
description: "Se parte de que no dispone de ningún conocimiento previo y, por ello, se explica cómo ejecutar un ejemplo de código de Python para escribir y leer datos de una tabla de Azure Database for PostgreSQL."
services: postgresql
author: MightyPen
ms.author: genemi
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f5e8191e52d1e2a4502a961158b6fec1c061d1
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="python-reading-from-azure-database-for-postgresql"></a>Lectura en Python desde Azure Database for PostgreSQL


En este artículo se proporciona un breve programa de Python que utiliza una instrucción SELECT de SQL para leer una fila de una tabla. En el artículo también se explica cómo buscar e instalar todos los requisitos previos necesarios para ejecutar el programa de Python.

Nuestro programa de Python de ejemplo y las herramientas relacionadas con Python descritas se aplican por igual a distintas plataformas, como Linux, Mac y Windows. 


## <a name="install-the-python-interpreter"></a>Instalación del intérprete de Python


El ejemplo de código de Python de este artículo se ha escrito para la versión 2.7 del intérprete de Python y no se puede ejecutar con la versión 3.x.

Descargue e instale la versión 2.7 del intérprete de Python, desde:

- [Descargue el intérprete de Python de python.org](https://www.python.org/downloads/)

Después de la instalación, pruebe que puede encontrar y ejecutar el intérprete en una línea de comandos. Use un comando como:

`python.exe -?`


## <a name="install-pipexe-the-python-module-installer"></a>Instalación de pip.exe, el instalador del módulo de Python


La instalación del intérprete de Python puede haber instalado también pip.exe, posiblemente en un directorio denominado *Scripts/*. Pip.exe instala módulos especializados de Python. Pruebe que puede buscar y ejecutar pip.exe.

`pip.exe`

Si no puede ejecutar pip.exe, consulte si tiene el archivo de la utilidad de Python denominado **get-pip.py**. Si tiene get-pip.py, puede ejecutarlo para obtener pip.exe:

`python.exe get-pip.py`


## <a name="install-psycopg-the-connection-module"></a>Instalación de psycopg, el módulo de conexión


El programa de Python necesita un módulo que sepa cómo conectar el programa al servidor de Azure Database for PostgreSQL. El módulo de conexión se llama **psycopg2**. Si desea más detalles, vea:

- [Sitio web de psycopg2](http://initd.org/psycopg/)

Instale psycopg2 mediante el siguiente comando de instalación de pip.exe:

`pip.exe install psycopg2`


## <a name="create-an-azure-database-for-postgresql-server"></a>Creación de un servidor de Azure Database for PostgreSQL


Si no dispone de acceso a un servidor de Azure Database for PostgreSQL, aquí está la documentación que explica las maneras en que puede crear un servidor:

- [Create an Azure Database for PostgreSQL by using the Azure portal](quickstart-create-server-database-portal.md) (Creación de un servidor de Azure Database for PostgreSQL en Azure Portal)
- [Create an Azure Database for PostgreSQL by using the Azure CLI](quickstart-create-server-database-azure-cli.md) (Creación de un servidor de Azure Database for PostgreSQL con la CLI de Azure)


## <a name="obtain-the-connection-string-values"></a>Obtención de valores de la cadena de conexión


En Azure Portal, puede obtener los valores de la cadena de conexión del servidor de Azure Database for PostgreSQL. Necesita los valores de parámetros descritos en la siguiente tabla.

- En la columna *Nombre*&ndash; se muestran los identificadores de parámetros que psycopg2 requiere.
- En la columna *-Símbolo*&ndash; se muestran los identificadores de parámetros que el programa de ejemplo de Python necesita, *PythonDriver.py*.


| Nombre | -Símbolo | Ejemplo de valor |
| :--  | :--     | :--           |
| host | -h | myazurepostgresql.database.windows.net |
| user | -U | myalias@myazurepostgresql |
| dbname | -d | postgres<br />*(Todos los servidores PostgreSQL tienen una base de datos denominada **postgres**).* |
| puerto | -p | 5432 *(Probablemente este valor específico de 5432.)* |
| contraseña | -P | MySecretPassword |
||||


## <a name="the-python-sample-program"></a>Programa de ejemplo de Python


En esta sección se proporciona el código fuente para el programa de ejemplo de Python. Ejecute el programa más adelante en este artículo.

```python
# PythonDriver.py
# Python version 2.7

import psycopg2
import sys
import getopt

def main():

    host = ""
    user = ""
    dbname = ""
    port = ""
    password = ""

    try:
        opts, args = getopt.getopt(sys.argv[1:], "h:U:d:p:P:", [])
    except getopt.GetoptError as exc:
        print str(exc)
        usage()
        exit(2)

    for o, a in opts:
        if o == "-h": host = str(a)
        if o == "-U": user = str(a)
        if o == "-d": dbname = str(a)
        if o == "-p": port = str(a)
        if o == "-P": password = str(a)

    conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)

    try:
        # Create a connection.  Raise an exception if cannot connect.
        conn = psycopg2.connect(conn_string) 
        cursor = conn.cursor()

        #cursor.execute("DROP TABLE testpy1;")
        #conn.commit()

        cursor.execute("CREATE TABLE testpy1 (id serial PRIMARY KEY, num integer, data varchar);")

        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (100, "First'row"))
        cursor.execute("INSERT INTO testpy1 (num, data) VALUES (%s, %s)", (110, "Second_row"))
        conn.commit()

        cursor.execute("SELECT id, num, data FROM testpy1;")
        result = cursor.fetchone()

        # Optionally, you can comment these clean-up lines to leave
        # the testpy1 table available for your inspection by pgAdmin. 
        cursor.execute("DROP TABLE testpy1;")
        conn.commit()

    except Exception as exc:
        print "FAILED ", exc
        exit(1)

    print "SUCCESS: ", result

if __name__ == "__main__":
    main()
```


## <a name="command-lines-to-run-the-sample-program"></a>Líneas de comandos para ejecutar el programa de ejemplo


En esta sección se muestran las líneas de comandos usadas para probar el programa de ejemplo de Python, junto con la salida de confirmación real.

En el caso de los parámetros descritos anteriormente, debe reemplazar los valores de ejemplo mostrados aquí por los valores reales de la cadena de conexión.

La sintaxis exacta para ejecutar el programa de Python de ejemplo anterior puede variar ligeramente. La sintaxis exacta depende del sistema operativo y del tipo de consola utilizado.


#### <a name="windows-cmdexe-console"></a>Consola de Windows cmd.exe


El bloque de código siguiente muestra una serie de pruebas real del programa de Python de ejemplo. Se ha usado una línea de comandos de cmd.exe sencilla. Después de escribir cada carácter de continuación de línea "^":

1. Se presionó la tecla ENTRAR.
2. Se mostró la frase **More?**.
3. Se escribió otra parte de la línea completa, y así sucesivamente.

Esta técnica de continuación de línea se ha usado para evitar que el ejemplo sea demasiado general hasta el punto de mostrarse aquí de forma poco precisa, o bien al imprimirlo.

```cmd
set Prompt=[$P\]$_$+$G$G$S

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>> .\python.exe ".\_myalias\PythonDriver.py" ^
More?  -h myazurepostgresql.database.windows.net ^
More?  -p 5432 ^
More?  -d postgres ^
More?  -U myalias@myazurepostgresql ^
More?  -P mySecretPassword
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
>>
```

Puede ver la línea **SUCCESS** como una confirmación de que el programa se ha ejecutado.

Como una técnica alternativa, puede poner toda la línea de comandos en un archivo .bat. Después, el archivo .bat podría ejecutarse desde la línea de comandos de cmd.exe.


#### <a name="powershell-file"></a>Archivo de PowerShell


En la consola de PowerShell, la línea de comandos no admite el carácter de continuación de línea. Por lo tanto, en esta sección de PowerShell, se colocan los comandos en un archivo de PowerShell. Luego, se ejecuta el archivo desde la línea de comandos de PowerShell.

Copie el código siguiente en un archivo denominado *PythonDriverRun.ps1*.

```powershell
# PythonDriverRun.ps1
cd C:\Users\myalias\AppData\Local\Programs\Python\Python27\

.\python.exe `
 .\_myalias\PythonDriver.py `
 -h myazurepostgresql.database.windows.net `
 -p 5432 `
 -d postgres `
 -U myalias@myazurepostgresql `
 -P mySecretPassword
```

Ejecute PythonDriverRun.ps1 tal y como se muestra a continuación. Puede ver la línea **SUCCESS** como una confirmación de que el programa imprime.

```cmd
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> .\_myalias\PythonDriverRun.ps1
SUCCESS:  (1, 100, "First'row")

[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >>
```

Si prefiere ejecutar PythonDriverRun.ps1 directamente en la línea de comandos de PowerShell, debe anteponer un carácter "&" seguido de un espacio. Sin el carácter "&" inicial, el mensaje de confirmación desaparece demasiado rápido como para verlo.

```
[C:\Users\myalias\AppData\Local\Programs\Python\Python27\]
0 >> & .\python.exe ".\_myalias\PythonDriver.py" -h myazurepostgresql.database.windows.net -p 5432 -d postgres -U myalias@myazurepostgresql -P mySecretPassword
SUCCESS:  (1, 100, "First'row")
```


## <a name="install-pgadmin-to-inspect-your-server"></a>Instalación de pgAdmin para inspeccionar el servidor


Cuando finaliza el programa PythonDriverRun.ps1, se limpia después al quitar la tabla testpy1 que el programa ha creado. Tiene la opción de usar un símbolo "#" para convertir en comentarios la línea de código fuente que la instrucción **QUITAR TABLA** genera. Esta opción dejaría la tabla para poder inspeccionarla más adelante.

La herramienta pgAdmin permite inspeccionar cualquier servidor PostgreSQL y los objetos que contiene. Los usuarios de Microsoft SQL Server o de Azure SQL Database encontrarían similitudes entre SQL Server Management Studio (SSMS) y pgAdmin.

Si lo desea, puede instalar **pgAdmin** para inspeccionar el servidor y la tabla **testpy1**.


#### <a name="1-install-pgadmin"></a>1. Instalación de pgAdmin


Las instrucciones de instalación de pgAdmin están disponibles en:

- [http://www.pgadmin.org/](http://www.pgadmin.org/)

**pgAdmin4.exe** podría ser el nombre del archivo ejecutable, no simplemente pgAdmin.exe.

Para ejecutar pgAdmin4.exe en un equipo Windows, escriba algo similar al siguiente comando en una línea de comandos:

`"C:\Program Files (x86)\pgAdmin 4\v1\runtime\pgAdmin4.exe"`


#### <a name="2-connect-pgadmin-to-your-server"></a>2. Conexión de pgAdmin al servidor

 
Cuando aparece la interfaz de usuario de pgAdmin, busque el panel **Browser** (Explorador). Después, haga clic con el botón derecho en **Servers** (Servidores)  > **Create** (Crear)  > **Servers** (Servidores). Aquí el término *Crear* significa crear una *conexión* a cualquier servidor PostgreSQL existente, incluso a cualquier servidor de Azure Database for PostgreSQL.

Cuando se establece la conexión, se muestra un árbol de objetos en el panel **Browser** (Explorador).


#### <a name="3-navigate-in-the-pgadmin-tree-to-your-table"></a>3. Navegación por el árbol de pgAdmin en la tabla


Para ver la tabla, expanda los elementos de árbol como se indica a continuación:

- **Servers** (Servidores) &gt; *[Su servidor]* &gt; **Databases** (Bases de datos) &gt; postgres &gt; **Schemas** (Esquemas) &gt; public (público) &gt; **tables** (tablas) &gt; testpy1

![pgAdmin muestra la tabla testpy1 en el árbol](./media/connect-python/pgAdmin-postgresql-table-testpy1.jpg)


#### <a name="4-drop-the-testpy1-table"></a>4. Eliminación de la tabla testpy1


Para una limpieza definitiva, haga clic con el botón derecho en el nodo testpy1 y luego haga clic en **Delete/Drop** (Eliminar/Quitar).


## <a name="next-steps"></a>Pasos siguientes

- [Conexión de Python a Azure SQL Database](../sql-database/sql-database-connect-query-python.md)
- [Bibliotecas de conexiones de Azure Database for PostgreSQL](concepts-connection-libraries.md)

