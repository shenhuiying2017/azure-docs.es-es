---
title: "Migración de la Base de datos MySQL mediante el volcado y la restauración en Azure Database for MySQL | Microsoft Docs"
description: "Presenta la migración de Azure Database for MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/17/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: c0029e025cf6d0af478d1f21dc6acc7860905a81
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migre su Base de datos MySQL a Azure Database for MySQL mediante el volcado y la restauración.
En este artículo se explican dos formas habituales de hacer una copia de seguridad y restaurar bases de datos en Azure Database for MySQL.
- Copia de seguridad y restauración a partir de la línea de comandos (mediante mysqldump) 
- Copia de seguridad y restauración mediante PHPMyAdmin 

## <a name="before-you-begin"></a>Antes de empezar
Para seguir esta guía de procedimientos, necesita lo siguiente:
- Conocer la [creación de una base de datos de Azure para el servidor MySQL: Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Disponer de la utilidad de línea de comandos [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalada en un equipo
- MySQL Workbench [Descargar MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat o cualquier herramienta de MySQL de terceros

## <a name="use-common-tools"></a>Uso de herramientas comunes
Use herramientas comunes como MySQL Workbench, mysqldump, Toad o Navicat para conectarse y restaurar datos de forma remota en Azure Database for MySQL. Use estas herramientas en el equipo cliente con una conexión a Internet para conectarse a Azure Database for MySQL. Use una conexión cifrada SSL para aprovechar los procedimientos recomendados de seguridad; vea también la información sobre [conectividad SSL en Azure Database for MySQL](concepts-ssl-connection-security.md). No es necesario mover los archivos de volcado a ninguna ubicación en la nube especial al migrar a Azure Database for MySQL. 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Creación de un archivo de copia de seguridad a partir de la línea de comandos mediante mysqldump
Para hacer una copia de seguridad de una base de datos MySQL que esté almacenada localmente o en una VM, ejecute el comando siguiente: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Los parámetros que se proporcionan son los siguientes:
- [uname] El nombre de usuario de base de datos 
- [pass] La contraseña de la base de datos (observe que no hay ningún espacio entre -p y la contraseña) 
- [dbname] El nombre de la base de datos 
- [backupfile.sql] El nombre de archivo para la copia de seguridad de la base de datos 
- [--opt] La opción mysqldump 

Por ejemplo, para hacer una copia de seguridad de una base de datos denominada "testdb" con el nombre de usuario "testuser" y sin contraseña en untestdb_backup.sql, utilice el siguiente comando. Este comando realizará una copia de seguridad de la base de datos "testdb" en un archivo denominado testdb_backup.sql que contendrá todas las instrucciones SQL necesarias para volver a crear la base de datos. 

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
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>Creación de una base de datos en el servidor de MySQL de Azure de destino
Debe crear una base de datos vacía en el servidor de Azure Database for MySQL de destino en el que desea a migrar los datos con MySQL Workbench, Toad, Navicat o cualquier herramienta de terceros para MySQL. La base de datos puede tener el mismo nombre que la base de datos que contiene los datos volcados, o puede crear una base de datos con un nombre diferente.

![Cadena de conexión de Azure Database for MySQL](./media/concepts-migrate-import-export/p5.png)

![Cadena de conexión de MySQL Workbench](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restauración de la base de datos MySQL mediante la línea de comandos o MySQL Workbench
Una vez que haya creado la base de datos de destino, puede usar el comando mysql o MySQL Workbench para restaurar los datos en la base de datos específica recién creada desde el archivo de volcado.
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
En este ejemplo, se restaurarán los datos a la base de datos recién creada testdb3 en el servidor de destino.
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportación mediante PHPMyAdmin
Para exportar, puede utilizar la herramienta común phpMyAdmin que puede haber instalado ya localmente en su entorno. Para exportar la Base de datos MySQL mediante PHPMyAdmin:
- Abra phpMyAdmin.
- Seleccione la base de datos haciendo clic en su nombre en la lista a la izquierda de la pantalla. 
- Haga clic en el vínculo Exportar. De este modo debería abrirse una nueva pantalla en la que ponga "View dump of database" (Ver volcado de la base de datos). 
- En el área de exportación, haga clic en el vínculo "Select All" (Seleccionar todo) para seleccionar todas las tablas de la base de datos. 
- En el área de opciones de SQL, haga clic en las opciones adecuadas. 
- Haga clic en la opción "Save as file" (Guardar como archivo) y en la correspondiente opción de compresión y entonces haga clic en el botón "Go" (Ir). Debería aparecer un cuadro de diálogo en el que se le pide que guarde el archivo localmente.

## <a name="import-using-phpmyadmin"></a>Importación mediante PHPMyAdmin
La importación de la base de datos es similar a la exportación. Haga lo siguiente:
- Abra phpMyAdmin. 
- Cree una base de datos con un nombre apropiado y selecciónela haciendo clic en su nombre en la lista a la izquierda de la pantalla. Si desea volver a escribir la importación a través de una base de datos existente, haga clic en el nombre de la base de datos, seleccione todas las casillas junto a los nombres de tabla y seleccione Drop (Quitar) para eliminar todas las tablas existentes en la base de datos. 
- Haga clic en el vínculo SQL. De este modo debería abrirse una nueva pantalla en la que puede escribir los comandos SQL o cargar su archivo de SQL. 
- Utilice el botón Browse (Examinar) para buscar el archivo de base de datos. 
- Haga clic en el botón Go (Ir). De este modo se exportará la copia de seguridad; ejecute los comandos SQL y vuelva a crear la base de datos.

## <a name="next-steps"></a>Pasos siguientes

[Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal) 
[Create an Azure Database for MySQL server using Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)

