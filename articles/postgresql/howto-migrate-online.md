---
title: "Migración con tiempo de inactividad mínimo a Azure Database for PostgreSQL | Microsoft Docs"
description: "En este artículo se describe cómo realizar una migración con tiempo de inactividad mínimo mediante la extracción de una base de datos PostgreSQL a un archivo de volcado, la restauración de la base de datos PostgreSQL desde un archivo creado por pg_dump en Azure Database for PostgreSQL y la configuración de la sincronización de datos continuos y la carga inicial desde la base de datos de origen a la base de datos de destino mediante el uso de Attunity Replicate for Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: efbd4f227880875c11e2c43c84716dfc49c5717d
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migración con tiempo de inactividad mínimo a Azure Database for PostgreSQL
Puede migrar la base de datos de PostgreSQL existente a Azure Database for PostgreSQL mediante el uso de Attunity Replicate para Microsoft Migrations. Attunity Replicate es una oferta conjunta de Attunity y Microsoft. Junto con Azure Database Migration Service, se incluye sin costo adicional para los clientes de Microsoft. 

Attunity Replicate le ayuda a minimizar el tiempo de inactividad durante las migraciones de bases de datos y mantiene la base de datos de origen operativa a lo largo del proceso.

Attunity Replicate es una herramienta de replicación de datos que habilita la sincronización de datos entre diferentes orígenes y destinos. Propaga el script de creación de esquemas y los datos asociados a cada tabla de base de datos. Attunity Replicate no propaga ningún otro artefacto (como SP, desencadenadores, funciones, etc.) ni convierte, por ejemplo, el código PL/SQL hospedado en dichos artefactos, en T-SQL.

> [!NOTE]
> A pesar de que Attunity Replicate admite un conjunto amplio de escenarios de migración, se centra en la compatibilidad con un subconjunto específico de pareas de origen o destino.

Una introducción al proceso para realizar una migración con tiempo de inactividad mínimo incluye:

* **Migración del esquema de origen de PostgreSQL** a una base de datos de Azure Database for PostgreSQL mediante el uso del comando [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) con el parámetro -n y, luego, con el comando [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html).

* **Configuración de la sincronización de datos continuos y la carga inicial desde la base de datos de origen a la base de datos de destino** mediante Attunity Replicate for Microsoft Migrations. Si lo hace, se minimiza el tiempo durante el cual la base de datos de origen se debe establecer como solo lectura mientras se prepara para cambiar las aplicaciones a la base de datos PostgreSQL de destino en Azure.

Para más información sobre la oferta de Attunity Replicate for Microsoft Migrations, consulte los recursos siguientes:
 - Vaya a la página web de [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Descargue [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Visite la [comunidad de Attunity Replicate](https://aka.ms/attunity-community/), para obtener una guía de inicio rápido, tutoriales y soporte técnico.
 - Para una guía paso a paso sobre cómo usar Attunity Replicate para migrar desde PostgreSQL a Azure Database for PostgreSQL, consulte la [guía sobre Database Migration](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).