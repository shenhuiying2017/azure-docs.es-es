---
title: "Creación y administración de reglas de firewall en Azure Database for PostgreSQL | Microsoft Docs"
description: "Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/12/2018
ms.openlocfilehash: 137d3f7f45a1865cc7eb76ef3b066960f61d6b2f
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal
Las reglas de firewall de nivel de servidor permiten a los administradores tener acceso a un servidor de Azure Database for PostgreSQL desde una dirección IP o desde un intervalo de direcciones IP especificado. 

## <a name="prerequisites"></a>requisitos previos
Para seguir esta guía, necesitará:
- Un servidor [Creación de una instancia de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creación de una regla de firewall de nivel a servidor en Azure Portal
1. En la página del servidor de PostgreSQL, en el encabezado Configuración, haga clic en **Seguridad de conexión** para abrir la página de seguridad de conexión para Azure Database for PostgreSQL.

  ![Azure Portal: haga clic en Seguridad de conexión](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Haga clic en **Agregar mi dirección IP** en la barra de herramientas. Se crea automáticamente una regla de firewall con la dirección IP pública del equipo, según la percibe el sistema de Azure.

  ![Azure Portal: haga clic en Agregar mi dirección IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Compruebe la dirección IP antes de guardar la configuración. En algunos casos, la dirección IP observada por Azure Portal difiere de la dirección IP utilizada para acceder a Internet y a los servidores de Azure. Por tanto, puede necesitar cambiar las direcciones IP inicial y final para que la regla funcione según lo previsto.
Use un motor de búsqueda u otra herramienta en línea para comprobar su propia dirección IP. Por ejemplo, busque "¿cuál es mi IP?".

  ![Búsqueda en Bing "cuál es mi ip"](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Agregue intervalos de direcciones adicionales. En las reglas de firewall de Azure Database for PostgreSQL, puede especificar una dirección IP única o un intervalo de direcciones. Si desea limitar la regla a una única dirección IP, escriba la misma dirección en los campos de dirección IP inicial y dirección IP final. Abrir el firewall permite a administradores, usuarios y aplicaciones iniciar sesión en cualquier base de datos del servidor de PostgreSQL para el que tengan unas credenciales válidas.

  ![Azure Portal: reglas de firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Haga clic en **Guardar**, en la barra de herramientas, para guardar esta regla de firewall de nivel de servidor. Espere la confirmación de que la actualización de las reglas de firewall se realizó correctamente.

  ![Azure Portal: haga clic en Guardar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Conexión desde Azure
Para permitir que las aplicaciones de Azure se conecten al servidor Azure Database for PostgreSQL, deben habilitarse las conexiones de Azure. Por ejemplo, para hospedar una aplicación Azure Web Apps o una aplicación que se ejecute en una VM de Azure, o para conectarse desde una puerta de enlace de administración de datos de Azure Data Factory. No es necesario que los recursos se encuentren en la misma red virtual (VNET) o grupo de recursos para que la regla de firewall habilite las conexiones. Cuando una aplicación desde Azure intenta conectarse a su servidor de s de datos, el firewall comprueba que se permiten las conexiones de Azure. Hay un par de métodos para habilitar estos tipos de conexiones. Una configuración del firewall con dirección inicial y final igual a 0.0.0.0 indica que se permiten estas conexiones. Como alternativa, puede establecer la opción **Permitir el acceso a servicios de Azure** en **ACTIVADO** en el portal desde el panel **Seguridad de conexión** y seleccionar **Guardar**. Si no se permite el intento de conexión, la solicitud no llega al servidor Azure Database for PostgreSQL.

> [!IMPORTANT]
> Esta opción configura el firewall para permitir todas las conexiones de Azure, incluidas las de las suscripciones de otros clientes. Al seleccionar esta opción, asegúrese de que los permisos de usuario y el inicio de sesión limiten el acceso solamente a los usuarios autorizados.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Administración de reglas de firewall de nivel de servidor existentes a través del Portal de Azure
Repita los pasos para administrar las reglas de firewall.
* Para agregar el equipo actual, haga clic en el botón + **Agregar mi dirección IP**. Haga clic en **Guardar** para guardar los cambios.
* Para agregar direcciones IP adicionales, escriba el nombre de la regla, la dirección IP inicial y la dirección IP final. Haga clic en **Guardar** para guardar los cambios.
* Para modificar una regla existente, haga clic en cualquiera de los campos de la regla y realice la modificación. Haga clic en **Guardar** para guardar los cambios.
* Para eliminar una regla existente, haga clic en el botón de puntos suspensivos […] y luego en **Eliminar** para quitar la regla. Haga clic en **Guardar** para guardar los cambios.

## <a name="next-steps"></a>pasos siguientes
- De modo similar, puede utilizar scripts para la [Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure](howto-manage-firewall-using-cli.md).
- Para obtener ayuda para la conexión a un servidor de Azure Database for PostgreSQL, consulte [Bibliotecas de conexión para Azure Database for PostgreSQL](concepts-connection-libraries.md).
