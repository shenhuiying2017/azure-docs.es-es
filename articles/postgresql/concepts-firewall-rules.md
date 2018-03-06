---
title: Reglas de firewall del servidor de Azure Database for PostgreSQL
description: "En este artículo se describen las reglas de firewall para el servidor de Azure Database for PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8a3f5d9fa8f1c36d8468c38f7dda803d3ca1d832
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
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

## <a name="connecting-from-azure"></a>Conexión desde Azure
Para permitir que las aplicaciones de Azure se conecten al servidor Azure Database for PostgreSQL, deben habilitarse las conexiones de Azure. Por ejemplo, para hospedar una aplicación Azure Web Apps o una aplicación que se ejecute en una VM de Azure, o para conectarse desde una puerta de enlace de administración de datos de Azure Data Factory. No es necesario que los recursos se encuentren en la misma red virtual (VNET) o grupo de recursos para que la regla de firewall habilite las conexiones. Cuando una aplicación desde Azure intenta conectarse a su servidor de s de datos, el firewall comprueba que se permiten las conexiones de Azure. Hay un par de métodos para habilitar estos tipos de conexiones. Una configuración del firewall con dirección inicial y final igual a 0.0.0.0 indica que se permiten estas conexiones. Como alternativa, puede establecer la opción **Permitir el acceso a servicios de Azure** como **ACTIVADO** en el portal desde el panel **Seguridad de conexión** y seleccionar **Guardar**. Si no se permite el intento de conexión, la solicitud no llega al servidor Azure Database for PostgreSQL.

> [!IMPORTANT]
> Esta opción configura el firewall para permitir todas las conexiones de Azure, incluidas las de las suscripciones de otros clientes. Al seleccionar esta opción, asegúrese de que los permisos de usuario y el inicio de sesión limiten el acceso solamente a los usuarios autorizados.
> 

![Configurar el permiso de acceso a los servicios de Azure en el portal](media/concepts-firewall-rules/allow-azure-services.png)

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

## <a name="next-steps"></a>pasos siguientes
Para leer artículos sobre cómo crear reglas de firewall de nivel de servidor y de base de datos, consulte:
* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](howto-manage-firewall-using-portal.md) (Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal)
* [Create and manage Azure Database for PostgreSQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md) (Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure)
