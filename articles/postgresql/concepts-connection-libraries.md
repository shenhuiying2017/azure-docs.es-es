---
title: Bibliotecas de conexiones de Azure Database for PostgreSQL | Microsoft Docs
description: "En este artículo se describen varias bibliotecas y controladores que los desarrolladores pueden usar al codificar aplicaciones para conectarse a Azure Database for PostgreSQL y realizar ahí consultas."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 35b7f026aa05fb16d57f49b93edff831b43268db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de conexiones de Azure Database for PostgreSQL
En este tema se enumeran las bibliotecas y los controladores que pueden usar los desarrolladores para programar aplicaciones con el fin de conectarse a Azure Database for PostgreSQL y realizar ahí consultas.

## <a name="client-interfaces"></a>Interfaces de cliente
La mayoría de las bibliotecas de cliente de lenguajes para conectarse al servidor PostgreSQL son proyectos externos y se distribuyen de manera independiente. Estas se admiten en plataformas Windows, Linux y Mac. A continuación, se enumeran algunos de los controladores cliente más conocidos:

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
Lea estos tutoriales rápidos sobre cómo conectarse a Azure Database for PostgreSQL y realizar consultas ahí mediante el lenguaje de su elección:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [.NET (C#)](./connect-csharp.md)
