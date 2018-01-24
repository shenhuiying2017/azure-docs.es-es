---
title: Conceptos de servidor en Azure Database for PostgreSQL | Microsoft Docs
description: En este tema se incluyen consideraciones e instrucciones para configurar y administrar servidores de Azure Database for PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 12/02/2017
ms.openlocfilehash: d7eec2735e48f57500eb2ea822f0949d2ec2e585
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="azure-database-for-postgresql-servers"></a>Servidores de Azure Database for PostgreSQL
En este artículo se incluyen consideraciones e instrucciones para trabajar con servidores de Azure Database for PostgreSQL.

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
- Está disponible en varias versiones. Para más información, consulte las [versiones de base de datos admitidas de PostgreSQL](concepts-supported-versions.md).
- Es ampliable por los usuarios. Para más información, consulte la información sobre [extensiones de PostgreSQL](concepts-extensions.md).

Dentro del servidor de Azure Database for PostgreSQL, puede crear una o varias bases de datos. Puede optar por crear una sola base de datos por servidor para que use todos los recursos, o bien crear varias para que los compartan. El precio está estructurado por servidor y se basa en la configuración del plan de tarifa, las unidades de proceso y el almacenamiento (GB). Para más información, consulte [Planes de tarifa](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>¿Cómo conectarse a un servidor de Azure Database for PostgreSQL y autenticarse en él?
Los elementos siguientes ayudan a garantizar el acceso seguro a la base de datos:

|||
|:--|:--|
| **Autenticación y autorización** | El servidor de Azure Database for PostgreSQL admite la autenticación nativa de PostgreSQL. Puede conectarse al servidor y autenticarse en él con el inicio de sesión de administrador del servidor. |
| **Protocolo** | El servicio admite un protocolo basado en mensajes utilizado por PostgreSQL. |
| **TCP/IP** | Se admite el protocolo a través de TCP/IP y a través de sockets de dominio de Unix. |
| **Firewall** | Para mantener los datos protegidos, una regla de firewall impide todo acceso al servidor y a las bases de datos, hasta que se especifique qué equipos cuentan con permiso. Vea la información sobre las [reglas de firewall del servidor de Azure Database for PostgreSQL](concepts-firewall-rules.md). |

## <a name="how-do-i-manage-a-server"></a>¿Cómo se administra un servidor?
Puede administrar servidores de Azure Database for PostgreSQL en [Azure Portal](https://portal.azure.com) o con la [CLI de Azure](/cli/azure/postgres).

## <a name="server-parameters"></a>Parámetros del servidor
Los parámetros de servidor de PostgreSQL determinan la configuración del servidor. En Azure Database for PostgreSQL, la lista de parámetros puede verse y editarse mediante Azure Portal o la CLI de Azure. 

Como servicio administrado para Postgres, los parámetros configurables en Azure Database for PostgreSQL son un subconjunto de los parámetros en una instancia local de Postgres (para obtener más información sobre los parámetros de Postgres, consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). El servidor de Azure Database for PostgreSQL está habilitado con los valores predeterminados para cada parámetro en el momento de la creación. El usuario no puede configurar los parámetros que requieren un reinicio del servidor o acceso de superusuario para que los cambios se apliquen.


## <a name="next-steps"></a>pasos siguientes
- Para obtener una visión general del servicio, consulte [Introducción a Azure Database for PostgreSQL](overview.md).
- Para obtener información sobre las cuotas y las limitaciones aplicables a recursos específicos en función de su **nivel de servicio**, consulte [Niveles de servicio](concepts-service-tiers.md).
- Para obtener información sobre cómo conectarse al servicio, vea la información sobre [bibliotecas de conexión de Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Vea y edite los parámetros del servidor a través de [Azure Portal](howto-configure-server-parameters-using-portal.md) o la [CLI de Azure](howto-configure-server-parameters-using-cli.md).
