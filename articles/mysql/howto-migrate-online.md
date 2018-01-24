---
title: "Migración con tiempo de inactividad mínimo a Azure Database for MySQL | Microsoft Docs"
description: "En este artículo se describe cómo realizar una migración con tiempo de inactividad mínimo de una base de datos MySQL a Azure Database for MySQL y cómo configurar la sincronización de datos continuos y la carga inicial desde la base de datos de origen a la base de datos de destino mediante el uso de Attunity Replicate for Microsoft Migrations."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 429f5c9f939a802184a6513a63fb9115abf4b235
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migración con tiempo de inactividad mínimo a Azure Database for MySQL
Puede migrar la base de datos MySQL existente a Azure Database for MySQL con Attunity Replicate for Microsoft Migrations, una oferta conjunta de Attunity y Microsoft que se proporciona junto con Azure Database Migration Service a los clientes de Microsoft sin costo adicional. Attunity Replicate for Microsoft Migrations también permite un tiempo de inactividad mínimo en la migración de bases de datos y la base de datos de origen sigue operativa durante el proceso de migración.

Attunity Replicate es una herramienta de replicación de datos que permite sincronizar los datos entre una variedad de orígenes y destinos, mediante la propagación del script de creación de esquemas y los datos asociados con cada tabla de base de datos. Attunity Replicate no propaga ningún otro artefacto (como SP, desencadenadores, funciones, etc.) ni convierte, por ejemplo, el código PL/SQL hospedado en dichos artefactos, en T-SQL.

> [!NOTE]
> Mientras Attunity Replicate admite un conjunto amplio de escenarios de migración, Attunity Replicate for Microsoft Migrations se centra en la compatibilidad con un subconjunto específico de pareas de origen o destino.

Una introducción al proceso para realizar una migración con tiempo de inactividad mínimo incluye:

1. **Migración del esquema de origen de MySQL** a una base de datos de Azure Database for MySQL mediante el uso de [MySQL Workbench](https://www.mysql.com/products/workbench/).

2. **Configuración de la sincronización de datos continuos y la carga inicial desde la base de datos de origen a la base de datos de destino** mediante Attunity Replicate for Microsoft Migrations. Si lo hace, se minimiza el tiempo durante el cual la base de datos de origen se debe establecer como solo lectura mientras se prepara para cambiar las aplicaciones a la base de datos MySQL de destino en Azure.

Para más información sobre la oferta de Attunity Replicate for Microsoft Migrations, consulte los recursos siguientes:
 - La [página web](https://aka.ms/attunity-replicate) de Attunity Replicate for Microsoft Migrations.
 - [Descargue](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity Replicate for Microsoft Migrations.
 - La [comunidad](https://microsoft.attunity.com/) de Attunity Replicate, con una guía de inicio rápido, tutoriales y soporte técnico.
 - Para una guía paso a paso sobre cómo usar Attunity para migrar desde MySQL a Azure Database for MySQL, consulte la [guía sobre Database Migration](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).