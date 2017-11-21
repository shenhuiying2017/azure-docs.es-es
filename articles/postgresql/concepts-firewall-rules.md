---
title: Reglas de firewall del servidor de Azure Database for PostgreSQL | Microsoft Docs
description: Describe las reglas de firewall para el servidor de Azure Database for PostgreSQL.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 7fec71f621ffeff2fc42a5a9464ae9011b2e2fee
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Reglas de firewall del servidor de Azure Database for PostgreSQL
El firewall del servidor de Azure Database for PostgreSQL impide todo acceso al servidor de base de datos, excepto a aquellos equipos a los que haya concedido permiso. Asimismo, otorgan acceso al servidor según la dirección IP de origen de cada solicitud.
Para configurar su firewall, cree reglas de firewall que especifiquen intervalos de direcciones IP aceptables. Puede crear reglas de firewall a nivel de servidor.

**Reglas de firewall:** estas reglas permiten a los clientes tener acceso a todo el servidor de Azure Database for PostgreSQL; es decir, a todas las bases de datos que se encuentren en el mismo servidor lógico. Las reglas de firewall de nivel de servidor pueden configurarse a través de Azure Portal o los comandos de la CLI de Azure. Para crear reglas de firewall en el nivel del servidor, debe ser propietario o colaborador de la suscripción.

## <a name="firewall-overview"></a>Información general de firewalls
Todos los accesos de base de datos al servidor de Azure Database for PostgreSQL se bloquean por el firewall de manera predeterminada. Para comenzar a usar el servidor desde otro equipo, debe especificar una o varias reglas de firewall a nivel de servidor para habilitar el acceso a su servidor. Use las reglas de firewall para especificar los intervalos de direcciones IP de Internet que se permitirán. La acción de las reglas de firewall no repercute sobre el acceso al propio sitio web de Azure Portal.
Los intentos de conexión desde Internet y Azure deben atravesar primero el firewall antes de poder alcanzar PostgreSQL Database, tal y como se muestra en el siguiente diagrama:

![Ejemplo de flujo de cómo funciona el firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Conexión desde Internet
Las reglas de firewall de nivel de servidor se aplican a todas las bases de datos del mismo servidor de Azure Database for PostgreSQL. Si la dirección IP de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión se concede.
Si la dirección IP de la solicitud no se encuentra dentro de los intervalos especificados en cualquiera de las reglas de firewall de nivel de servidor, la solicitud de la conexión genera error.
Por ejemplo, si la aplicación se conecta con un controlador JDBC para PostgreSQL, puede encontrar este error al intentar conectarse cuando el firewall está bloqueando la conexión.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="programmatically-managing-firewall-rules"></a>Administración mediante programación de reglas de firewall
Además de Azure Portal, las reglas de firewall se pueden administrar mediante programación con la CLI de Azure.
Vea también la información sobre la [creación y la administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure](howto-manage-firewall-using-cli.md).

## <a name="troubleshooting-the-database-server-firewall"></a>Solución de problemas del firewall del servidor de la base de datos
Tenga en cuenta los siguientes puntos cuando el acceso al servicio del servidor de Microsoft Azure Database for PostgreSQL no se comporte de la manera prevista:

* **Los cambios realizados en la lista de permitidos no han surtido efecto todavía:** puede haber un retraso de hasta cinco minutos hasta que los cambios en la configuración del firewall del servidor de Azure Database for PostgreSQL surtan efecto.

* **El inicio de sesión no está autorizado o se ha usado una contraseña incorrecta:** si un inicio de sesión no tiene permisos en el servidor de Azure Database for PostgreSQL o la contraseña usada es incorrecta, se denegará la conexión al servidor de Azure Database for PostgreSQL. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad de intentar conectarse al servidor; cada cliente debe continuar ofreciendo las credenciales de seguridad necesarias.

Por ejemplo, mediante un cliente JDBC, puede aparecer el siguiente error.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **Dirección IP dinámica:** si tiene una conexión a Internet con una direccionamiento IP dinámica y tiene problemas al acceder al firewall, podría intentar una de las siguientes soluciones:

* Pida a su proveedor de acceso a Internet (ISP) el intervalo de direcciones IP asignado a los equipos cliente que acceden al servidor de Azure Database for PostgreSQL y agréguelo como regla de firewall.

* Obtenga el direccionamiento IP estático en su lugar para los equipos cliente y luego agregue la dirección IP estática como regla de firewall.

## <a name="next-steps"></a>Pasos siguientes
Para leer artículos sobre cómo crear reglas de firewall de nivel de servidor, consulte:
* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](howto-manage-firewall-using-portal.md) (Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal).
* [Create and manage Azure Database for PostgreSQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md) (Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure).
