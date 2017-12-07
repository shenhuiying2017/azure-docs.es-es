---
title: "Creación de usuarios en el servidor de Azure Database for MySQL | Microsoft Docs"
description: "En este artículo se describe cómo puede crear nuevas cuentas de usuario para interactuar con un servidor de Azure Database for MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 8adb74e11570ac60ad3b898b737cff4699f2bbf1
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Creación de usuarios en un servidor de Azure Database for MySQL 
En este artículo se describe cómo puede crear usuarios en un servidor de Azure Database for MySQL.

La primera vez que creó su instancia de Azure Database for MySQL, proporcionó un nombre de usuario y una contraseña de inicio de sesión de administrador del servidor. Para más información, puede seguir la [Guía de inicio rápido](quickstart-create-mysql-server-database-using-azure-portal.md). Puede encontrar su nombre de usuario de inicio de sesión de administrador del servidor en Azure Portal.

El usuario obtiene ciertos privilegios para su servidor, como son: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Una vez creado el servidor de Azure Database for MySQL, puede usar la primera cuenta de usuario administrador del servidor para crear usuarios adicionales y concederles acceso de administrador. Además, la cuenta de administrador del servidor puede usarse para crear usuarios con menos privilegios que tengan acceso a esquemas de base de datos individuales.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Creación de usuarios administradores adicionales en Azure Database for MySQL
1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Puede encontrar fácilmente el nombre del servidor y la información de inicio de sesión en la página **Información general** del servidor o en la página **Propiedades** de Azure Portal. 

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use la herramienta preferida de cliente, como MySQL Workbench, mysql.exe, HeidiSQL u otras. 
   Si no está seguro de cómo conectarse, consulte [Uso de MySQL Workbench para conectarse y consultar datos](./connect-workbench.md).

3. Modifique y ejecute el siguiente código SQL. Reemplace el valor de marcador de posición `new_master_user` por el nuevo nombre de usuario. Esta sintaxis concede los privilegios enumerados en todos los esquemas de base de datos (*.*) al nombre de usuario (new_master_user en este ejemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Comprobación de las concesiones 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Creación de usuarios de base de datos en Azure Database for MySQL

1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Puede encontrar fácilmente el nombre del servidor y la información de inicio de sesión en la página **Información general** del servidor o en la página **Propiedades** de Azure Portal. 

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use la herramienta preferida de cliente, como MySQL Workbench, mysql.exe, HeidiSQL u otras. 
   Si no está seguro de cómo conectarse, consulte [Uso de MySQL Workbench para conectarse y consultar datos](./connect-workbench.md).

3. Modifique y ejecute el siguiente código SQL. Reemplace el valor de marcador de posición `db_user` por su nuevo nombre de usuario deseado y el valor de marcador de posición `testdb` por su propio nombre de base de datos.

   Esta sintaxis de código sql crea una nueva base de datos denominada testdb para que sirva de ejemplo. A continuación, crea un nuevo usuario en el servicio MySQL y concede todos los privilegios al nuevo esquema de base de datos (testdb.\*) de ese usuario. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Compruebe las concesiones dentro de la base de datos.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Inicie sesión en el servidor mediante el nuevo nombre de usuario y contraseña, sin olvidarse de especificar la base de datos designada. En este ejemplo se muestra la línea de comandos de mysql. Con este comando, se le pedirá la contraseña del nombre de usuario. Reemplace su propio nombre de servidor, nombre de base de datos y nombre de usuario.

   ```azurecli-interactive
   mysql --host myserver4demo.mysql.database.azure.com --database testdb --user db_user@myserver4demo -p
   ```

## <a name="next-steps"></a>Pasos siguientes
Abra el firewall para las direcciones IP de las máquinas de los nuevos usuarios para permitirles que se conecten: [Creación y administración de reglas de firewall de Azure Database for MySQL mediante Azure Portal](howto-manage-firewall-using-portal.md) o la [CLI de Azure](howto-manage-firewall-using-cli.md).

Para más información respecto a la administración de cuentas de usuario, consulte la documentación del producto de MySQL relativa a la [administración de cuentas de usuario](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), la [sintaxis GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html) y los [privilegios](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
