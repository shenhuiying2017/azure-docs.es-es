---
title: Bibliotecas de conexiones de Azure Database for PostgreSQL | Microsoft Docs
description: Enumera cada biblioteca o controlador que pueden usar los programas cliente al conectarse a Azure Database for PostgreSQL.
keywords: nube de azure postgresql postgres
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de conexiones de Azure Database for PostgreSQL
En este tema se muestra cada biblioteca o controlador que pueden usar los programas cliente al conectarse a Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
La mayoría de bibliotecas de cliente de lenguajes para conectarse al servidor de PostgreSQL son proyectos externos y se distribuyen de manera independiente. Estas se admiten en plataformas Windows, Linux y Mac. A continuación se enumeran algunos de los controladores cliente más conocidos.
| **Lenguaje** | **Interfaz de cliente** | **Información adicional** | **Descargar** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatible con API 2.0 de BD | [Descargar](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Extensión de base de datos | [Instalación](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Paquete de Pg npm](https://www.npmjs.com/package/pg) | Cliente sin bloqueo de JavaScript puro | [Instalación](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Controlador JDBC tipo 4 | [Descargar](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interfaz Ruby | [Descargar](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Controlador de Postgres de Go puro | [Instalación](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Proveedor de datos de ADO.NET | [Descargar](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Controlador ODBC | [Descargar](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interfaz de idioma C principal | Se incluye |
| C++ | [libpqxx](http://pqxx.org/) | Interfaz de C++ de nuevo estilo | [Descargar](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Pasos siguientes
- Para obtener una visión general del servicio, consulte [Introducción a Azure Database for PostgreSQL](overview.md).
- Para más información sobre los servidores, consulte [Servidores de Azure Database for PostgreSQL](concepts-servers.md).
- Para crear el primer servidor de PostgreSQL, consulte [Create an Azure Database for PostgreSQL in the Azure portal](quickstart-create-server-database-portal.md) (Creación de una instancia de Azure Database for PostgreSQL en el portal de Azure).

