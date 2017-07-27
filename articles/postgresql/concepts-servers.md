---
title: Conceptos de servidor en Azure Database for PostgreSQL | Microsoft Docs
description: En este tema se incluyen consideraciones e instrucciones para trabajar con servidores de Azure Database for PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 5197a3f44a085d25d964f355154d92b08e65560b
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017

---
# <a name="azure-database-for-postgresql-servers"></a>Servidores de Azure Database for PostgreSQL

En este tema se incluyen consideraciones e instrucciones para trabajar con servidores de Azure Database for PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>¿Qué es un servidor de Azure Database for PostgreSQL?

Un servidor de Azure Database for PostgreSQL es un punto central de administración de varias bases de datos. Se trata de la misma construcción de servidores de PostgreSQL con la que puede estar familiarizado en entornos locales. En concreto, el servicio PostgreSQL se administra, ofrece garantías de rendimiento y expone el acceso y las características a nivel de servidor.

Un servidor de Azure Database for PostgreSQL:

- Se crea dentro de una suscripción de Azure.
- Es el recurso principal de las bases de datos.
- Proporciona un espacio de nombres para las bases de datos.
- Es un contenedor con semántica de duración segura. Si se elimina un servidor, se eliminan las bases de datos que contiene.
- Coloca recursos en una región.
- Proporciona un punto de conexión para el acceso a la base de datos y al servidor (.postgresql.database.azure.com).
- Proporciona el ámbito de las directivas de administración que se aplican a sus bases de datos: inicio de sesión, firewall, usuarios, roles, configuración, etc.
- Está disponible en varias versiones. Para más información, vea la información sobre [versiones de base de datos admitidas de PostgreSQL](concepts-supported-versions.md).
- Es ampliable por los usuarios. Para más información, consulte la información sobre [extensiones de PostgreSQL](concepts-extensions.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>¿Cómo conectarse a un servidor de Azure Database for PostgreSQL y autenticarse en él?

Los elementos siguientes ayudan a garantizar el acceso seguro a la base de datos.

|||
| :-- | :-- |
| **Autenticación y autorización** | El servidor de Azure Database for PostgreSQL admite la autenticación nativa de PostgreSQL. Puede conectarse al servidor y autenticarse en él con el inicio de sesión de administrador del servidor. |
| **Protocolo** | El servicio admite un protocolo basado en mensajes utilizado por PostgreSQL. |
| **TCP/IP** | Se admite el protocolo a través de TCP/IP y a través de sockets de dominio de Unix. |
| **Firewall** | Para ayudar a mantener los datos protegidos, una regla de firewall impide todo acceso al servidor de bases de datos, o a estas últimas, hasta que se especifique qué equipos cuentan con permiso. Vea la información sobre las [reglas de firewall del servidor de Azure Database for PostgreSQL](concepts-firewall-rules.md). |
|||

## <a name="how-do-i-manage-a-server"></a>¿Cómo se administra un servidor?

Puede administrar servidores de Azure Database for PostgreSQL en Azure Portal o con la [CLI de Azure](/cli/azure/postgres).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una visión general del servicio, consulte la [introducción a Azure Database for PostgreSQL](overview.md).
- Para obtener información sobre las cuotas y limitaciones aplicables a recursos específicos en función de su **nivel de servicio**, consulte [Niveles de servicio](concepts-service-tiers.md).
- Para obtener información sobre cómo conectarse al servicio, vea la información sobre [bibliotecas de conexión de Azure Database for PostgreSQL](concepts-connection-libraries.md).

