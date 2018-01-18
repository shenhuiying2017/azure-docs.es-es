---
title: "Migración de una base de datos mediante importación y exportación en Azure Database for PostgreSQL | Microsoft Docs"
description: "Describe cómo extraer una base de datos PostgreSQL en un archivo de script e importar los datos en la base de datos de destino desde ese archivo."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: ddbfd9ef8b2ae4c3c851afc18b010b234b654c81
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migración de una base de datos de PostgreSQL mediante exportación e importación
Puede usar [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extraer una base de datos PostgreSQL en un archivo de script y [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para importar los datos en la base de datos de destino desde ese archivo.

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) con reglas de firewall para permitir el acceso a las bases de datos que hay en él.
- La utilidad de línea de comandos [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) instalada.
- La utilidad de línea de comandos [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) instalada.

Siga estos pasos para exportar e importar la base de datos de PostgreSQL.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Creación de un archivo de script mediante pg_dump con los datos que se van a cargar
Para exportar la base de datos de PostgreSQL existente en el entorno local o en una máquina virtual a un archivo de script de SQL, ejecute el siguiente comando en el entorno existente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por ejemplo, si tiene un servidor local y una base de datos llamada **testdb** en él:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importación de los datos en la instancia de Azure Database for PostgreSQL de destino
Puede usar la utilidad de la línea de comandos psql y el parámetro --dbname (-d) para importar los datos en el servidor de Azure Database for PostrgeSQL y cargar los datos desde el archivo de SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
En este ejemplo se usa la utilidad psql y el archivo de script **testdb.sql** del paso anterior para importar los datos en la base de datos **mypgsqldb** del servidor de destino **mypgserver-20170401.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mypgserver-20170401.database.windows.net --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb
```

## <a name="next-steps"></a>pasos siguientes
- Para migrar una base de datos de PostgreSQL mediante volcado y restauración, consulte [Migración de una base de datos de PostgreSQL mediante volcado y restauración](howto-migrate-using-dump-and-restore.md).
