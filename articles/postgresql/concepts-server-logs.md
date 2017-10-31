---
title: Registros de servidor en Azure Database for PostgreSQL | Microsoft Docs
description: Genera registros de errores y consultas en Azure Database for PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 696af85cd5609171a719a7e77efbfcdeba0aaaaa
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2017
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Registros de servidor en Azure Database for PostgreSQL 
Azure Database for PostgreSQL genera registros de errores y consultas. Sin embargo, no se admite el acceso a los registros de transacciones. Los registros de consulta y errores se pueden usar para identificar, solucionar y reparar errores de configuración y casos de rendimiento no óptimo. Para obtener más información, consulte [Error Reporting and Logging](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) (Notificación y registro de errores).

## <a name="access-server-logs"></a>Acceso a registros de servidor
Puede enumerar y descargar los registros de error del servidor de Azure PostgreSQL mediante Azure Portal, [la CLI de Azure](howto-configure-server-logs-using-cli.md) y las API de REST de Azure.

## <a name="log-retention"></a>Retención de registros
Puede establecer el período de retención para los registros del sistema mediante el parámetro **log\_retention\_period** asociado al servidor. La unidad de este parámetro en días. El valor predeterminado es 3 días. El valor máximo es siete días. El servidor debe tener suficiente almacenamiento asignado para contener los archivos de registro retenidos.
Los archivos de registro rotan cada hora o 100 MB de tamaño, lo que ocurra primero.

## <a name="configure-logging-for-azure-postgresql-server"></a>Configuración del registro para el servidor de Azure PostgreSQL
Puede habilitar el registro de consultas y errores para el servidor. Los registros de error pueden contener información de vaciado automático, conexión y puntos de comprobación.

Puede habilitar el registro de consultas para la instancia de PostgreSQL DB estableciendo dos parámetros de servidor: `log\_statement` y `log\_min\_duration\_statement`.

El parámetro **log\_statement** controla las instrucciones SQL que se registran. Se recomienda establecer este parámetro en ***all*** para registrar todas las instrucciones; el valor predeterminado es none.

El parámetro **log\_min\_duration\_statement** establece el límite en milisegundos para el registro de una instrucción. Se registran todas las instrucciones SQL que se ejecutan durante más tiempo que el valor del parámetro. Este parámetro está deshabilitado y configurado en menos 1 (-1) de manera predeterminada. La habilitación de este parámetro puede ser útil para hacer un seguimiento de consultas no optimizadas en sus aplicaciones.

El parámetro **log\_min\_messages** permite controlar qué niveles de mensajes se escriben en el registro del servidor. El valor predeterminado es WARNING 

Para obtener más información sobre estas opciones de configuración, consulte la documentación sobre [notificación y registro de errores](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html). Para configurar específicamente los parámetros del servidor de Azure Database for PostgreSQL, consulte el artículo [Personalización de los parámetros de configuración del servidor con la CLI de Azure](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Pasos siguientes
- Para obtener acceso a registros mediante la interfaz de la línea de comandos de la CLI de Azure, consulte el artículo [Configuración y acceso a los registros del servidor con la CLI de Azure](howto-configure-server-logs-using-cli.md).
- Para más información sobre los parámetros del servidor, consulte cómo [personalizar los parámetros de configuración del servidor mediante la CLI de Azure](howto-configure-server-parameters-using-cli.md).
