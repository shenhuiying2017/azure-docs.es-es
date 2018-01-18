---
title: "Compatibilidad de las herramientas de administración y los controladores de MySQL | Microsoft Docs"
description: "Herramientas de administración y controladores de MySQL compatibles con Azure Database for MySQL"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/27/2017
ms.openlocfilehash: 7578ae710a3d6c81fdfa2952c53a20c2cdccb6d0
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Herramientas de administración y controladores de MySQL compatibles con Azure Database for MySQL
En este artículo se describen las herramientas de administración y los controladores compatibles con Azure Database for MySQL.

## <a name="mysql-drivers"></a>Controladores de MySQL
Azure Database for MySQL usa la edición comunitaria más popular del mundo de la base de datos MySQL. Por lo tanto, es compatible con una amplia variedad de controladores y lenguajes de programación. El objetivo es la compatibilidad con las tres versiones más recientes de los controladores MySQL, y continúan los esfuerzos con los autores de la comunidad de código abierto para mejorar constantemente la funcionalidad y la facilidad de uso de los controladores de MySQL. En la tabla siguiente se proporciona una lista de controladores que se han probado y son compatibles con Azure Database for MySQL 5.6 y 5.7:

| **Controlador** | **Vínculos** | **Versiones compatibles** | **Versiones no compatibles** | **Notas** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | http://php.net/downloads.php | 5.5 5.6 7.x | 5.3 | Para la conexión PHP 7.0 con SSL MySQLi, agregue MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT en la cadena de conexión. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO: establezca la opción ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` en false.|
| .Net | [MySqlConnector en GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Paquete de instalación de NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0.27 y posterior | 0.26.5 y anterior | |
| Nodejs |  [MySQLjs en GitHub](https://github.com/mysqljs/mysql/releases) <br> Paquete de instalación de NPM:<br> Ejecute `npm install mysql` en NPM | 2.15 | 2.14.1 y anterior | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 y anterior | Use allowNativePasswords=true en la cadena de conexión |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 y anterior | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1 2.0 1.6 | 1.5.5 y anterior | |

## <a name="management-tools"></a>Herramientas de administración
La ventaja de compatibilidad se amplía también a las herramientas de administración de base de datos. Sus herramientas actuales deben continuar funcionando con Azure Database for MySQL, siempre y cuando la manipulación de la base de datos tenga lugar dentro de los límites establecidos por los permisos de usuario. En la tabla siguiente se indican las tres herramientas comunes de administración de base de datos que se han probado y son compatibles con Azure Database for MySQL 5.6 y 5.7:

|                                     | **MySQL Workbench 6.x y superiores** | **Navicat 12** | **PHPMyAdmin 4.x y superiores** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Crear, actualizar, leer, escribir, eliminar | X | X | X |
| Conexión SSL | X | X | X |
| Completado automático de consultas SQL | X | X |  |
| Importación y exportación de datos | X | X | X |
| Exportación en varios formatos | X | X | X |
| Copia de seguridad y restauración |  | X |  |
| Muestra de parámetros de servidor | X | X | X |
| Muestra de conexiones de cliente | X | X | X |
