---
title: Conectividad SSL de Azure Database for MySQL
description: "Información para configurar Azure Database for MySQL y las aplicaciones asociadas, a fin de usar correctamente las conexiones SSL"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f59d5eab9772515a3c59f887a48d597d27bab135
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividad SSL de Azure Database for MySQL
Azure Database for MySQL permite conectar el servidor de bases de datos a las aplicaciones cliente con la Capa de sockets seguros (SSL). El establecimiento de conexiones SSL entre el servidor de bases de datos y las aplicaciones cliente ofrece protección frente a ataques de tipo "Man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.

## <a name="default-settings"></a>Configuración predeterminada
De forma predeterminada, el servicio de base de datos debe configurarse para requerir conexiones SSL al conectar con MySQL.  Se recomienda evitar la desactivación de la opción SSL siempre que sea posible. 

Al aprovisionar un nuevo servidor de Azure Database for MySQL en Azure Portal o con la CLI de Azure, el establecimiento de conexiones SSL está habilitado de forma predeterminada. 

Las cadenas de conexión para distintos lenguajes de programación se muestran en Azure Portal. Estas cadenas de conexión incluyen los parámetros SSL que se requieren para conectarse a la base de datos. En Azure Portal, seleccione el servidor. En el encabezado **Configuración**, seleccione las **cadenas de conexión**. El parámetro SSL varía según el conector, por ejemplo, "ssl = true" o "sslmode = require" o "sslmode = required" y otras variaciones.

Para información sobre cómo habilitar o deshabilitar la conexión SSL al desarrollar la aplicación, consulte [Configuración de SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>pasos siguientes
[Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md)
