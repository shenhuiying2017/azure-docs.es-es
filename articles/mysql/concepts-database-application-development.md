---
title: "Introducción al desarrollo de aplicaciones de base de datos de Azure Database for MySQL | Microsoft Docs"
description: "Se presentan las consideraciones de diseño que un desarrollador debe seguir al escribir código de aplicación para conectarse a Azure Database for MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5f8bb1631ccd6dcfe1354e3b4c0897c587149b1d
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="application-development-overview-for-azure-database-for-mysql"></a>Introducción al desarrollo de aplicaciones para Azure Database for MySQL 
En este artículo se describe las consideraciones de diseño que debería seguir un desarrollador al escribir código de aplicación para conectarse a Azure Database for MySQL. 

> [!TIP]
> Para obtener un tutorial que muestre cómo crear un servidor, crear un firewall basado en servidor, ver propiedades de servidor, crear base de datos, conectarse y consultar mediante Workbench y mysql.exe, consulte [Design your first Azure MySQL database](tutorial-design-database-using-portal.md) (Diseño de la primera base de datos MySQL de Azure).

## <a name="language-and-platform"></a>Plataforma y lenguaje
Existen ejemplos de código para diferentes lenguajes de programación y plataformas. Puede encontrar vínculos a los ejemplos de código en: [Bibliotecas de conexiones usadas para conectarse a Azure Database for MySQL](concepts-connection-libraries.md).

## <a name="tools"></a>Herramientas
Azure Database for MySQL usa la versión de la comunidad de MySQL, compatible con herramientas de administración comunes de MySQL, como Workbench, o utilidades de MySQL como mysql.exe, [phpMyAdmin ](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql) y otras. También puede usar el portal de Azure, la CLI de Azure y la API de REST para interactuar con el servicio de base de datos.

## <a name="resource-limitations"></a>Limitaciones de recursos
La base de datos MySQL de Azure administra los recursos disponibles para un servidor mediante dos mecanismos diferentes: 
- Gobierno de recursos 
- Aplicación de límites

## <a name="security"></a>Seguridad
La base de datos MySQL de Azure proporciona recursos para limitar el acceso, proteger los datos, configurar usuarios y roles y supervisar las actividades en una base de datos MySQL.

## <a name="authentication"></a>Autenticación
La base de datos MySQL de Azure admite la autenticación de servidor de usuarios e inicios de sesión.

## <a name="resiliency"></a>Resistencia
Cuando se produce un error transitorio durante la conexión a la base de datos MySQL, el código debe reintentar la llamada. Recomendamos que en la lógica de reintento se haga uso de la lógica de interrupción; de este modo no se sobrecargará SQL Database con los reintentos de varios clientes a la vez.

- Ejemplos de código: para ver ejemplos de código que ilustran la lógica de reintento, consulte los del idioma de su elección en: [Bibliotecas de conexiones usadas para conectarse a Azure Database for MySQL](concepts-connection-libraries.md).

## <a name="managing-connections"></a>Administración de conexiones
Las conexiones de base de datos son un recurso limitado, por lo que recomendamos un uso razonable de conexiones al acceder a la base de datos MySQL con el fin de lograr un mejor rendimiento.
- Acceda a la base de datos mediante la agrupación de conexiones o conexiones persistentes.
- Acceda a la base de datos usando la duración de conexión corta. 
- Use la lógica de reintento en su aplicación en el momento del intento de conexión, para detectar errores debidos a conexiones simultáneas que hayan alcanzado el número máximo permitido. En la lógica de reintento, establezca un retraso breve y, a continuación, espere un tiempo aleatorio antes de los intentos de conexiones adicionales.
