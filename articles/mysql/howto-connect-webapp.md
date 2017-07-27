---
title: "Conexión de Azure App Service existente a Azure Database for MySQL | Microsoft Docs"
description: "Instrucciones sobre cómo conectar correctamente un servicio existente de Azure App Service a Azure Database para MySQL"
services: mysql
author: v-chenyh
ms.author: v-chenyh
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 2488a28353f3cfe76dc5aa9f9a9159a37ee9901b
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017

---

# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Conexión de un servicio existente de Azure App Service a un servidor de Azure Database for MySQL
Este documento explica cómo conectar un servicio existente de Azure App Service a un servidor de Azure Database for MySQL.

## <a name="before-you-begin"></a>Antes de empezar
Inicie sesión en el [Portal de Azure](https://portal.azure.com). Cree de un servidor de Azure Database for MySQL. Para obtener más información, consulte [Creación de un servidor de Azure Database for MySQL con Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) o [Creación de un servidor de Azure Database for MySQL con la CLI de Azure](quickstart-create-mysql-server-database-using-azure-cli.md).

Actualmente hay dos soluciones para habilitar el acceso desde un servicio de Azure App Service a un servidor de Azure Database for MySQL. En ambas soluciones hay que configurar las reglas de firewall de nivel de servidor.

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>Solución 1: crear una regla de firewall para permitir todas las direcciones IP
Azure Database for MySQL proporciona seguridad de acceso mediante un firewall para proteger los datos. Al conectarse desde un servicio de Azure App Service a un servidor de Azure Database for MySQL server, tenga en cuenta que las direcciones IP de salida de App Service son de naturaleza dinámica. 

Para garantizar la disponibilidad del servicio Azure App Service, se recomienda usar esta solución para permitir TODAS las direcciones IP.

> [!NOTE]
> Microsoft está trabajando para que una solución a largo plazo para evitar permitir todas las direcciones IP para los servicios de Azure en las conexiones a Azure Database for MySQL.

1. En la hoja del servidor de MySQL, en el encabezado Configuración, haga clic en **Seguridad de conexión** para abrir la hoja de seguridad de conexión para Azure Database for MySQL.

   ![Azure Portal: haga clic en Seguridad de conexión](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Escriba **NOMBRE DE LA REGLA**, **IP INICIAL** e **IP FINAL**. A continuación, haga clic en **Guardar**.
   - Nombre de la regla: Permitir-Todas-IP
   - IP inicial: 0.0.0.0
   - IP final: 255.255.255.255

   ![Azure Portal: agregar todas las IP](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solución 2: crear una regla de firewall para permitir explícitamente las direcciones IP de salida
Puede agregar explícitamente todas las IP de salida de su Azure App Service.

1. En la hoja de propiedades de App Service, consulte su **DIRECCIÓN IP DE SALIDA**.

   ![Azure Portal: ver direcciones IP de salida](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. En la hoja de Seguridad de conexión de MySQL, agregue las direcciones IP de salida una por una.

   ![Azure Portal: agregar direcciones IP explícitas](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. No olvide **Guardar** las reglas de firewall.

Aunque el Azure App Service intenta que no se modifiquen las direcciones IP con el tiempo, hay casos en los que pueden cambiar las direcciones IP. Por ejemplo, se produce cuando se recicla la aplicación o cuando se realiza una operación de escala, o cuando se añaden nuevos equipos en los centros de datos regionales de Azure para aumentar la capacidad. Cuando cambian las direcciones IP, la aplicación podría experimentar un tiempo de inactividad en caso de que ya no pueda conectarse al servidor MySQL. Al elegir una de las soluciones anteriores, tenga en cuenta esta posibilidad.

## <a name="ssl-configuration"></a>Configuración de SSL
Azure Database for MySQL tiene SSL habilitado de forma predeterminada. Si la aplicación no usa SSL para conectarse a la base de datos, debe deshabilitar SSL en el servidor MySQL. Para obtener más información sobre cómo configurar SSL, consulte [Uso de SSL con Azure Database for MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las cadenas de conexión, consulte [Cadenas de conexión](howto-connection-string.md).

