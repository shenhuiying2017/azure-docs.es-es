---
title: "Cómo configurar parámetros del servidor en Azure Database para MySQL | Microsoft Docs"
description: "En este artículo se explica cómo configurar parámetros del servidor MySQL en Azure Database for MySQL mediante Azure Portal."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/10/2017
ms.openlocfilehash: f3b32c1f6b33bc60b50f1496414a300db468dc92
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Cómo configurar parámetros del servidor en Azure Database for MySQL mediante Azure Portal

Azure Database para MySQL admite la configuración de algunos parámetros del servidor. En este tema se explica cómo configurar estos parámetros mediante Azure Portal. No es posible ajustar todos los parámetros del servidor. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Ir a Parámetros del servidor de Azure Portal
1. Inicie sesión en Azure Portal y luego localice su servidor de Azure Database for MySQL.
2. En la sección **CONFIGURACIÓN**, haga clic en **Parámetros del servidor** para abrir la página Parámetros del servidor de Azure Database for MySQL.
3. Localice cualquier configuración que deba ajustar. Revise la columna **Descripción** para entender el propósito y los valores permitidos. 
4. Haga clic en **Guardar** para guardar los cambios.

![Hoja Parámetros del servidor de Azure Portal](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parámetros configurables del servidor

La lista de parámetros del servidor admitidos crece constantemente. Use la pestaña Parámetros del servidor en Azure Portal para obtener la definición y configurar parámetros del servidor basados en los requisitos de la aplicación. 

## <a name="nonconfigurable-server-parameters"></a>Parámetros no configurables del servidor
El grupo de búferes de InnoDB y el número máximo de conexiones no son configurables y están vinculados a su [plan de tarifa](concepts-service-tiers.md). 

| **Plan de tarifa** | **Grupo de búferes InnoDB (MB)** | **Conexiones máximas** |
| :------------------------ | :-------- | :----------- |
| 50 unidades de proceso básicas | 1024 | 50 | 
| 100 unidades de proceso básicas  | 2560 | 100 | 
| 100 unidades de proceso estándar | 2560 | 200 | 
| 200 unidades de proceso estándar | 5120 | 400 | 
| 400 unidades de proceso estándar | 10240 | 800 | 
| 800 unidades de proceso estándar | 20480 | 1600 |

Estos parámetros adicionales del servidor no son configurables en el sistema <br>
 innodb_file_per_table en el plan Básico: OFF<br>
 innodb_flush_log_at_trx_commit=1<br>
 sync_binlog=1<br>
 innodb_log_file_size=512 MB<br>
 
Los demás parámetros de servidor que no aparecen aquí se establecen en sus valores predeterminados de MySQL integrado para las versiones [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) y [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>pasos siguientes
- [Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md)
