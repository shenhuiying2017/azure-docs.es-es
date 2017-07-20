---
title: "Restauración de un servidor de Azure Database for PostgreSQL | Microsoft Docs"
description: "Este artículo describe cómo realizar una copia de seguridad y cómo restaurar un servidor en Azure Database for PostgreSQL mediante la línea de comandos de la CLI de Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Copia de seguridad y restauración de un servidor en Azure Database for PostgreSQL mediante la CLI de Azure

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor y una base de datos de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="backup-happens-automatically"></a>Las copias de seguridad se realizan automáticamente
Cuando se usa Azure Database for PostgreSQL, el servicio de base de datos realiza automáticamente una copia de seguridad del servicio de cada 5 minutos. 

Las copias de seguridad están disponibles durante 7 días en el nivel Básico y 35 días en el nivel Estándar. Para más información, consulte [Planes de tarifa de Azure Database for PostgreSQL](concepts-service-tiers.md)

Con esta característica de copia de seguridad automática, puede restaurar el servidor y todas sus bases de datos en un servidor nuevo a un momento dado anterior.

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>Restauración de una base de datos a un momento dado anterior mediante la CLI de Azure
Azure Database for PostgreSQL permite restaurar el servidor a un momento dado anterior. Los datos restaurados se copian en un nuevo servidor y el servidor existente se deja tal cual. Por ejemplo, si una tabla se eliminó accidentalmente a mediodía de hoy, puede restaurar hasta el momento justo antes del mediodía. A continuación, recuperar la tabla y los datos que faltan de la copia restaurada del servidor. 

Utilice el comando [az postgres server restore](/cli/azure/postgres/server#restore) de la CLI de Azure para realizar la restauración.

### <a name="run-the-restore-command"></a>Ejecutar el comando restore
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

El comando `az postgres server restore` requiere los siguientes parámetros:
| Configuración | Valor sugerido | Descripción  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Grupo de recursos en el que existe el servidor de origen.  |
| name | mypgserver-restored | Nombre del nuevo servidor que se crea mediante el comando de restauración. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Seleccione un momento dado en el que quiere restaurar. Esta fecha y hora debe estar dentro del período de retención de copia de seguridad del servidor de origen. Use el formato de fecha y hora ISO8601. Por ejemplo, puede usar su propia zona horaria local, como `2017-04-13T05:59:00-08:00`, o usar el formato de hora Zulú UTC `2017-04-13T13:59:00Z`. |
| source-server | mypgserver-20170401 | Nombre o identificador del servidor de origen desde el que se va a restaurar. |

Al restaurar un servidor a un momento dado, se crea un servidor y se copia el servidor original a un momento dado que especifique. Los valores de ubicación y plan de tarifa del servidor restaurado son los mismos que los del servidor de origen. El comando es sincrónico y devolverá respuesta después de que se haya restaurado el servidor. 

Una vez finalizada la restauración, busque el servidor que se ha creado. Compruebe que los datos se han restaurado del modo esperado.

## <a name="next-steps"></a>Pasos siguientes
[Bibliotecas de conexiones de Azure Database para PostgreSQL](concepts-connection-libraries.md)

