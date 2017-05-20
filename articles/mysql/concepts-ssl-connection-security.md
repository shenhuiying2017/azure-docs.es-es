---
title: Conectividad SSL de Azure Database for MySQL | Microsoft Docs
description: "Información para configurar Azure Database for MySQL y las aplicaciones asociadas, a fin de usar correctamente las conexiones SSL"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.custom: quick start create
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6dc147c9dd3983c2c599108162fa285d87ffc7a3
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

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

