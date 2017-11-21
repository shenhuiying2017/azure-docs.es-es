---
title: Conectividad SSL de Azure Database for MySQL | Microsoft Docs
description: "Información para configurar Azure Database for MySQL y las aplicaciones asociadas, a fin de usar correctamente las conexiones SSL"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 11/02/2017
ms.openlocfilehash: 37e2bbbe1ed4b6a9cca0e0b001e5e632b9b73be2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividad SSL de Azure Database for MySQL
Azure Database for MySQL permite conectar el servidor de bases de datos a las aplicaciones cliente con la Capa de sockets seguros (SSL). El establecimiento de conexiones SSL entre el servidor de bases de datos y las aplicaciones cliente ofrece protección frente a ataques de tipo "Man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.

## <a name="default-settings"></a>Configuración predeterminada
De forma predeterminada, el servicio de base de datos debe configurarse para requerir conexiones SSL al conectar con MySQL.  Se recomienda evitar la desactivación de la opción SSL siempre que sea posible. 

Al aprovisionar un nuevo servidor de Azure Database for MySQL en Azure Portal o con la CLI de Azure, el establecimiento de conexiones SSL está habilitado de forma predeterminada. 

Las cadenas de conexión para distintos lenguajes de programación se muestran en Azure Portal. Estas cadenas de conexión incluyen los parámetros SSL que se requieren para conectarse a la base de datos. En Azure Portal, seleccione el servidor. En el encabezado **Configuración**, seleccione las **cadenas de conexión**. El parámetro SSL varía según el conector, por ejemplo, "ssl = true" o "sslmode = require" o "sslmode = required" y otras variaciones.

Para información sobre cómo habilitar o deshabilitar la conexión SSL al desarrollar la aplicación, consulte [Configuración de SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Pasos siguientes
[Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md)
