---
title: "Migración de la Base de datos MySQL mediante el volcado y la restauración en Azure Database for MySQL | Microsoft Docs"
description: "En este artículo se explican dos maneras comunes de realizar una copia de seguridad de las bases de datos y restaurarlas en Azure Database for MySQL, con herramientas como mysqldump, MySQL Workbench y PHPMyAdmin."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: e962fd65244ceebfc7544dc5a1d1956dad811fea
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migre su Base de datos MySQL a Azure Database for MySQL mediante el volcado y la restauración.
En este artículo se explican dos formas habituales de hacer una copia de seguridad y restaurar bases de datos en Azure Database for MySQL.
- Volcado y restauración desde la línea de comandos (mediante mysqldump) 
- Volcado y restauración mediante PHPMyAdmin 

## <a name="before-you-begin"></a>Antes de empezar
Para seguir esta guía de procedimientos, necesita lo siguiente:
- Conocer la [creación de una base de datos de Azure para el servidor MySQL: Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Utilidad de línea de comandos [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalada en la máquina.
- MySQL Workbench [Descargar MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat u otra herramienta de MySQL de terceros para ejecutar los comandos de volcado y restauración.

## <a name="use-common-tools"></a>Uso de herramientas comunes
Use herramientas y utilidades comunes como MySQL Workbench, mysqldump, Toad o Navicat para conectarse a Azure Database for MySQL de manera remota y restaurar ahí los datos. Use estas herramientas en el equipo cliente con una conexión a Internet para conectarse a Azure Database for MySQL. Use una conexión cifrada SSL para aprovechar los procedimientos recomendados de seguridad; vea también la información sobre [conectividad SSL en Azure Database for MySQL](concepts-ssl-connection-security.md). No es necesario mover los archivos de volcado a ninguna ubicación en la nube especial al migrar a Azure Database for MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Usos habituales de volcado y restauración
Puede emplear utilidades de MySQL, como mysqldump y mysqlpump para realizar un volcado de las bases de datos y cargarlas en una base de datos de Azure MySQL en varios escenarios comunes. En otros escenarios, puede usar en su lugar el enfoque de [importación y exportación](concepts-migrate-import-export.md).

- Use volcados de base de datos cuando vaya a migrar la base de datos entera. Esta recomendación es adecuada si va a mover una gran cantidad de datos de MySQL o si quiere reducir la interrupción del servicio en sitios o aplicaciones dinámicos. 
-  Asegúrese de que todas las tablas de la base de datos usen el motor de almacenamiento InnoDB al cargar datos en Azure Database for MySQL. Azure Database for MySQL solo admite el motor de almacenamiento InnoDB y, por tanto, no es compatible con otros alternativos. Si las tablas están configuradas con otros motores de almacenamiento, conviértalos al formato del motor InnoDB antes de realizar la migración a Azure Database for MySQL.
   Por ejemplo, si tiene una aplicación WordPress o WebApp que usa las tablas MyISAM, convierta primero esas tablas; para ello, mígrelas al formato InnoDB antes de restaurarlas en Azure Database for MySQL. Use la cláusula `ENGINE=InnoDB` para configurar el motor utilizado al crear una nueva tabla y luego transfiera los datos a la tabla compatible antes de la restauración. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidad, asegúrese de usar la misma versión de MySQL en los sistemas de origen y de destino al realizar el volcado de las bases de datos. Por ejemplo, si el servidor MySQL existente tiene la versión 5.7, debe migrar a Azure Database for MySQL configurado para ejecutar esta versión. El comando `mysql_upgrade` no funciona en un servidor de Azure Database for MySQL y no se admite. Si tiene que actualizar entre versiones de MySQL, primero vuelque o exporte la base de datos con una versión menor a una versión superior de MySQL en su propio entorno. A continuación, ejecute `mysql_upgrade` antes de intentar la migración a una instancia de Azure Database for MySQL.

## <a name="performance-considerations"></a>Consideraciones sobre rendimiento
Para optimizar el rendimiento, tenga en cuenta estas consideraciones al volcar grandes bases de datos:
-   Use la opción `exclude-triggers` en mysqldump al volcar las bases de datos. Excluya los desencadenadores de los archivos de volcado para evitar que los comandos de desencadenamiento se disparen durante la restauración de datos. 
-   Use la opción `single-transaction` para establecer el modo de aislamiento de transacción a REPEATABLE READ y enviar una instrucción SQL START TRANSACTION al servidor antes de volcar datos. El volcado de muchas tablas en una única transacción provoca el consumo de almacenamiento adicional durante la restauración. Las opciones `single-transaction` y `lock-tables` son mutuamente excluyentes porque LOCK TABLES hace que las transacciones pendientes se confirmen implícitamente. Para volcar las tablas grandes, combine la opción `single-transaction` con la opción `quick`. 
-   Use la sintaxis de varias filas `extended-insert` que incluye varias listas VALUE. Esto da como resultado un archivo de volcado de memoria más pequeño y acelera las inserciones cuando se vuelve a cargar el archivo.
-  Use la opción `order-by-primary` de mysqldump al volcar las bases de datos, para que el script de los datos se genere en el orden de la clave principal.
-   Use la opción `disable-keys` de mysqldump al volcar los datos para deshabilitar las restricciones de clave externa antes de la carga. El hecho de deshabilitar las comprobaciones de clave externa favorece un aumento del rendimiento. Habilite las restricciones y compruebe los datos después de la carga para garantizar la integridad referencial.
-   Use tablas con particiones cuando sea necesario.
-   Cargue los datos en paralelo. Evite demasiada paralelismo que podría provocar que se alcanzara un límite de recursos, y supervise los recursos mediante las métricas disponibles en Azure Portal. 
-   Use la opción `defer-table-indexes` de mysqlpump al volcar las bases de datos, para que la creación de índices tenga lugar una vez cargados los datos de las tablas.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Creación de un archivo de copia de seguridad a partir de la línea de comandos mediante mysqldump
Para hacer copia de seguridad de una base de datos MySQL existente en el servidor local en el entorno local o en una máquina virtual, ejecute el siguiente comando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Los parámetros que se proporcionan son los siguientes:
- [uname] El nombre de usuario de base de datos 
- [pass] La contraseña de la base de datos (observe que no hay ningún espacio entre -p y la contraseña) 
- [dbname] El nombre de la base de datos 
- [backupfile.sql] El nombre de archivo para la copia de seguridad de la base de datos 
- [--opt] La opción mysqldump 

Por ejemplo, para hacer una copia de seguridad de una base de datos llamada "testdb" en el servidor MySQL con el nombre de usuario "testuser" y sin contraseña en archivo untestdb_backup.sql, use el siguiente comando. El comando realiza una copia de la base de datos `testdb` en un archivo denominado `testdb_backup.sql`, que contiene todas las instrucciones SQL necesarias para volver a crear la base de datos. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Si quiere incluir en la copia de seguridad solo algunas de las tablas, ordene los nombres de tabla en una lista separados por espacios y seleccione los que desee. Por ejemplo, para realizar una copia de seguridad solo de las tablas table1 y table2 de "testdb", siga este ejemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Para hacer una copia de seguridad de más de una base de datos a la vez, use el conmutador --database y ordene los nombres de las bases de datos en una lista separados por espacios. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Para hacer una copia de seguridad de todas las bases del servidor a la vez, use la opción --all-databases.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Creación de una base de datos en el servidor de destino de Azure Database for MySQL
Cree una base de datos vacía en el servidor de destino de Azure Database for MySQL donde se van a migrar los datos. Use una herramienta como MySQL Workbench, Toad o Navicat para crear la base de datos. La base de datos puede tener el mismo nombre que la base de datos que contiene los datos volcados, o puede crear una base de datos con un nombre diferente.

Para conectarse, busque la información de conexión en la página de propiedades de su instancia de Azure Database for MySQL.
![Búsqueda de la información de conexión en Azure Portal](./media/concepts-migrate-dump-restore/1_server-properties-name-login.png)

Agregue la información de conexión a MySQL Workbench.
![Cadena de conexión de MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restauración de la base de datos MySQL mediante la línea de comandos o MySQL Workbench
Una vez que haya creado la base de datos de destino, puede usar el comando mysql o MySQL Workbench para restaurar los datos en la base de datos específica recién creada desde el archivo de volcado.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
En este ejemplo, restaure los datos en la base de datos recién creada en el servidor de destino de Azure Database for MySQL server.
```bash
$ mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportación mediante PHPMyAdmin
Para exportar, puede usar la herramienta común phpMyAdmin que puede haber instalado ya localmente en su entorno. Para exportar la Base de datos MySQL mediante PHPMyAdmin:
- Abra phpMyAdmin.
- Seleccione la base de datos. Haga clic en el nombre de la base de datos en la lista de la izquierda. 
- Haga clic en el vínculo **Exportar**. Aparece una nueva página para ver el volcado de la base de datos.
- En el área de exportación, haga clic en el vínculo **Select All** (Seleccionar todo) para seleccionar todas las tablas de la base de datos. 
- En el área de opciones de SQL, haga clic en las opciones adecuadas. 
- Haga clic en la opción **Save as file** (Guardar como archivo) y en la opción correspondiente de compresión y luego haga clic en el botón **Go** (Ir). Debería aparecer un cuadro de diálogo en el que se le pide que guarde el archivo localmente.

## <a name="import-using-phpmyadmin"></a>Importación mediante PHPMyAdmin
La importación de la base de datos es similar a la exportación. Haga lo siguiente:
- Abra phpMyAdmin. 
- En la página de instalación de phpMyAdmin, haga clic en **Add** (Agregar) para agregar el servidor de Azure Database for MySQL. Proporcione la información de conexión e inicio de sesión.
- Cree una base de datos con el nombre adecuado y selecciónela en la parte izquierda de la pantalla. Para volver a escribir la base de datos existente, haga clic en el nombre de la base de datos, active todas las casillas situadas al lado de los nombres de tabla y seleccione **Drop** (Eliminar) para eliminar las tablas existentes. 
- Haga clic en el vínculo **SQL** para mostrar la página donde puede escribir comandos SQL, o bien cargue su archivo SQL. 
- Use el botón **Browse** (Examinar) para buscar el archivo de base de datos. 
- Haga clic en **Go** (Ir) para exportar la copia de seguridad, ejecutar los comandos SQL y volver a crear la base de datos.

## <a name="next-steps"></a>Pasos siguientes
[Conexión de aplicaciones a Azure Database for MySQL](./howto-connection-string.md)
