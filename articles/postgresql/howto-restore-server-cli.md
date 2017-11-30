---
title: "Copia de seguridad y restauración de un servidor de Azure Database for PostgreSQL | Microsoft Docs"
description: "Aprenda a realizar la copia de seguridad y la restauración de un servidor de Azure Database for PostgreSQL mediante la CLI de Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 7027669597b8c1989f7baac5c5f9d997b218750a
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-by-using-the-azure-cli"></a>Copia de seguridad y restauración de un servidor de Azure Database for PostgreSQL mediante la CLI de Azure

Use Azure Database for PostgreSQL para restaurar una base de datos de servidor a una fecha de 7 a 35 días antes.

## <a name="prerequisites"></a>Requisitos previos
Para completar esta guía, necesita:
- Un [servidor y una base de datos de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Si instala y usa la CLI de Azure localmente, para esta guía se necesita la versión 2.0 o posterior de la CLI de Azure. Para confirmar la versión, en el símbolo del sistema de la CLI de Azure, escriba `az --version`. Para la instalación o la actualización, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="backup-happens-automatically"></a>Las copias de seguridad se realizan automáticamente
Cuando se usa Azure Database for PostgreSQL, el servicio de base de datos realiza una copia de seguridad del servicio automáticamente cada 5 minutos. 

Para el nivel básico, las copias de seguridad están disponibles durante 7 días. Para el nivel estándar, las copias de seguridad están disponibles durante 35 días. Para más información, consulte el artículo de [planes de tarifa de Azure Database for PostgreSQL](concepts-service-tiers.md).

Con esta característica de copia de seguridad automática, puede restaurar el servidor y todas sus bases de datos a una fecha o un momento dado anteriores.

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>Restauración de una base de datos a un momento dado anterior con la CLI de Azure
Use Azure Database for PostgreSQL para restaurar el servidor a un momento dado anterior. Los datos restaurados se copian en un nuevo servidor y el existente se queda tal cual. Por ejemplo, si una tabla se eliminó por error hoy a mediodía, puede restaurar hasta el momento justo antes del mediodía. Así podrá recuperar la tabla y los datos que faltan de la copia restaurada del servidor. 

Para restaurar el servidor, utilice el comando [az postgres server restore](/cli/azure/postgres/server#az_postgres_server_restore) de la CLI de Azure.

### <a name="run-the-restore-command"></a>Ejecutar el comando restore

Para restaurar el servidor, en el símbolo del sistema de la CLI de Azure, escriba el siguiente comando:

```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

El comando `az postgres server restore` requiere los siguientes parámetros:
| Configuración | Valor sugerido | Descripción  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Grupo de recursos donde existe el servidor de origen.  |
| name | mypgserver-restored | Nombre del nuevo servidor que se crea mediante el comando de restauración. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Seleccione un momento dado anterior para restaurar. Esta fecha y hora debe estar dentro del período de retención de copia de seguridad del servidor de origen. Use el formato de fecha y hora ISO8601. Por ejemplo, puede usar su propia zona horaria, como `2017-04-13T05:59:00-08:00`. También puede utilizar el formato de hora Zulú UTC, por ejemplo, `2017-04-13T13:59:00Z`. |
| source-server | mypgserver-20170401 | Nombre o identificador del servidor de origen desde el que se va a restaurar. |

Cuando se restaura un servidor a un momento dado anterior, se crea un servidor. El servidor de origen y las bases de datos de ese momento dado anterior se copian en el servidor nuevo.

Los valores de ubicación y plan de tarifa del servidor restaurado son los mismos que los del servidor de origen. 

El comando `az postgres server restore` es sincrónico. Después de restaura el servidor, puede usarlo para repetir el proceso con otro momento dado anterior. 

Una vez finalizada la restauración, busque el servidor nuevo y compruebe que los datos se restauraron según lo previsto.

## <a name="next-steps"></a>Pasos siguientes
[Bibliotecas de conexiones de Azure Database para PostgreSQL](concepts-connection-libraries.md)
