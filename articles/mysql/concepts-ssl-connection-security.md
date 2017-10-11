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
ms.date: 05/10/2017
ms.openlocfilehash: 4b03b3a2dbfad92cc0cfa84777b38ddff90452cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividad SSL de Azure Database for MySQL
Azure Database for MySQL permite conectar el servidor de bases de datos a las aplicaciones cliente con la Capa de sockets seguros (SSL). El establecimiento de conexiones SSL entre el servidor de bases de datos y las aplicaciones cliente ofrece protección frente a ataques de tipo "Man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.

## <a name="default-settings"></a>Configuración predeterminada
De forma predeterminada, el servicio de base de datos debe configurarse para requerir conexiones SSL al conectar con MySQL.  Se recomienda evitar que se deshabilite la opción SSL siempre que sea posible. 

Al aprovisionar un nuevo servidor de Azure Database for MySQL en Azure Portal o con la CLI de Azure, el establecimiento de conexiones SSL está habilitado de forma predeterminada. 

Del mismo modo, las cadenas de conexión que están predefinidas en la configuración de "Cadenas de conexión" del servidor en Azure Portal incluyen los parámetros necesarios para que los lenguajes comunes se conecten al servidor de base de datos mediante SSL. El parámetro SSL varía según el conector; por ejemplo "ssl=true", "sslmode=require", "sslmode=required" y otras variaciones.

Para obtener información sobre cómo habilitar o deshabilitar la conexión SSL al desarrollar la aplicación, vea [Configuración de SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Pasos siguientes
[Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md)
