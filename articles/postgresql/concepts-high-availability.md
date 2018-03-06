---
title: Conceptos de alta disponibilidad en Azure Database for PostgreSQL
description: "En este artículo se proporciona información de alta disponibilidad al usar Azure Database for PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 203a142a21153935e172508e62b813dca95468cb
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Conceptos de alta disponibilidad en Azure Database for PostgreSQL
El servicio Azure Database for PostgreSQL garantiza un alto nivel de disponibilidad. El Acuerdo de Nivel de Servicio con respaldo financiero tiene un 99,99 % de disponibilidad general. Al usar este servicio, las aplicaciones prácticamente no tienen tiempo de inactividad.

## <a name="high-availability"></a>Alta disponibilidad
El modelo de alta disponibilidad se basa en los mecanismos de conmutación por error integrados cuando se produce una interrupción en el nivel de nodo. Una interrupción en el nivel de nodo podría producirse debido a un error de hardware o como respuesta a la implementación de un servicio.

En todo momento, se realizan cambios en un servidor de base de datos de Azure Database for PostgreSQL en el contexto de una transacción. Los cambios se registran de forma sincrónica en Azure Storage cuando se confirma la transacción. Si se produce una interrupción en el nivel de nodo, el servidor de bases de datos crea automáticamente un nodo nuevo y adjunta el almacenamiento de datos. Todas las conexiones activas se eliminarán y no se confirmará ninguna transacción en proceso.

## <a name="application-retry-logic-is-essential"></a>La lógica de reintento de la aplicación es esencial
Es importante que las aplicaciones de la base de datos PostgreSQL se creen para detectar y reintentar conexiones eliminadas y transacciones erróneas. Cuando la aplicación realiza un reintento, su conexión se redirige de forma transparente a la instancia recién creada, que sustituye a la instancia errónea.

Internamente en Azure, se usa una puerta de enlace para redirigir las conexiones a la instancia nueva. Tras una interrupción, todo el proceso de conmutación por error tarda normalmente decenas de segundos. Puesto que la redirección se controla internamente mediante la puerta de enlace, la cadena de conexión externa permanece igual en las aplicaciones cliente.

## <a name="scaling-up-or-down"></a>Escalado o reducción vertical
De forma similar al modelo de alta disponibilidad, cuando Azure Database for PostgreSQL se escala o reduce verticalmente, se crea una nueva instancia de servidor con el tamaño especificado. El almacenamiento de datos existente se separa de la instancia original y se agrega a una nueva instancia.

Durante la operación de escalado, se produce una interrupción en las conexiones de la base de datos. Las aplicaciones cliente se desconectan y las transacciones abiertas no confirmadas se cancelan. Una vez que la aplicación cliente reintenta la conexión o crea una conexión nueva, la puerta de enlace dirige la conexión a la instancia cuyo tamaño se modificó. 

## <a name="next-steps"></a>pasos siguientes
- Para obtener una visión general del servicio, consulte la [introducción a Azure Database for PostgreSQL](overview.md).
