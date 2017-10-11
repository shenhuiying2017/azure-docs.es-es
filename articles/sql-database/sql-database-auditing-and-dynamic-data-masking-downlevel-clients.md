---
title: "Auditoría de tablas, redireccionamiento de TDS y puntos de conexión de IP para Azure SQL Database| Microsoft Docs"
description: "Obtenga información sobre auditoría, redireccionamiento de TDS y cambios de puntos de conexión IP cuando se implemente la auditoría de tablas en Azure SQL Database."
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: 
ms.assetid: 4ef19ed1-e798-43a2-ad99-0e563f93ab53
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: giladm
ms.openlocfilehash: d4a7e6658ec65a70bd7e07859e2a69acee58b7b5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>SQL Database: compatibilidad con clientes de nivel inferior y cambios de punto de conexión IP para auditoría de tablas

> [!IMPORTANT]
> Este documento se aplica solo a la auditoría de tablas, que ha quedado **en desuso**.<br>
> Use el nuevo métodos de [auditoría de BLOB](sql-database-auditing.md), que **no** requiere modificaciones de cadena de conexión de cliente de nivel inferior. Encontrará información adicional acerca de la auditoría de BLOB en [Introducción a la auditoría de SQL Database](sql-database-auditing.md).

[Auditoría de base de datos](sql-database-auditing.md) funciona automáticamente con los clientes SQL que admiten el redireccionamiento de TDS. Tenga en cuenta que el redireccionamiento no se aplica cuando se usa el método de auditoría de blobs.

## <a id="subheading-1"></a>Compatibilidad con clientes de nivel inferior
Cualquier cliente que implementa TDS 7.4 también debe admitir el redireccionamiento. Entre las excepciones a esto se incluyen JDBC 4.0, en el que la función de redireccionamiento no es totalmente compatible y Tedious para Node.JS, en cuya redireccionamiento no se ha implementado.

Para "clientes de nivel inferior", es decir, los que admiten TDS versión 7.3 e inferiores, debe modificarse el FQDN del servidor en la cadena de conexión:

FQDN del servidor original en la cadena de conexión: <*nombre del servidor*>. database.windows.net

FQDN del servidor modificado en la cadena de conexión: <*nombre del servidor*>. database.**secure**.windows.net

Una lista parcial de "Clientes de nivel inferior" incluye:

* .NET 4.0 y versiones posteriores,
* ODBC 10.0 y versiones posteriores.
* JDBC (aunque JDBC admite TDS 7.4, la característica de redireccionamiento de TDS no es totalmente compatible)
* Tedious (para Node.JS)

**Comentario:** la anterior modificación de FDQN de servidor puede resultar útil también para aplicar una directiva de auditoría de nivel de SQL Server sin necesidad de un paso de configuración en cada base de datos (mitigación temporal).

## <a id="subheading-2"></a>Cambios de punto de conexión IP al habilitar la auditoría
Tenga en cuenta que cuando se habilita la auditoría de tablas, cambiará el punto de conexión de IP de la base de datos. Si tiene una configuración de firewall estricta, actualice esas opciones de firewall en consecuencia.

El nuevo punto de conexión de IP de la base de datos dependerá de la región de base de datos:

| Región de base de datos | Posibles puntos de conexión de IP |
| --- | --- |
| Norte de China |139.217.29.176, 139.217.28.254 |
| Este de China |42.159.245.65, 42.159.246.245 |
| Australia Oriental |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Sudeste de Australia |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Sur de Brasil |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Central EE. UU.: |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| EUAP del centro de EE. UU. |52.180.178.16, 52.180.176.190 |
| Asia oriental |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Este de EE. UU. 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Este de EE. UU. |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| EUAP del este de EE. UU. |52.225.190.86, 52.225.191.187 |
| India Central |104.211.98.219, 104.211.103.71 |
| Sur de la India |104.211.227.102, 104.211.225.157 |
| Oeste de la India |104.211.161.152, 104.211.162.21 |
| Este de Japón |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Oeste de Japón |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Centro-Norte de EE. UU |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europa del Norte |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Centro-Sur de EE. UU |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sudeste asiático |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa occidental |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Oeste de EE. UU. |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Oeste de EE. UU. 2 |13.66.224.156, 13.66.227.8 |
| Centro occidental de EE.UU. |52.161.29.186, 52.161.27.213 |
| Centro de Canadá |13.88.248.106, 13.88.248.110 |
| Este de Canadá |40.86.227.82, 40.86.225.194 |
| Norte del Reino Unido |13.87.101.18, 13.87.100.232 |
| Sur del Reino Unido 2 |13.87.32.202, 13.87.32.226 |
