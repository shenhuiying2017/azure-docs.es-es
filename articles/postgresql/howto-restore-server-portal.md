---
title: "Restauración de un servidor de Azure Database for PostgreSQL | Microsoft Docs"
description: "En este artículo se describe cómo restaurar un servidor en Azure Database for PostgreSQL mediante Azure Portal."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 07/20/2017
ms.openlocfilehash: 3fbdb7741481bd3620466c3489d3609f9ea6961f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Copia de seguridad y restauración de un servidor en Azure Database for PostgreSQL mediante Azure Portal

## <a name="backup-happens-automatically"></a>Las copias de seguridad se realizan automáticamente
Cuando se usa Azure Database for PostgreSQL, el servicio de base de datos realiza automáticamente una copia de seguridad del servicio de cada 5 minutos. 

Las copias de seguridad están disponibles durante 7 días en el nivel Básico y 35 días en el nivel Estándar. Para más información, consulte [Niveles de servicio de Azure Database for PostgreSQL](concepts-service-tiers.md)

Con esta característica de copia de seguridad automática, puede restaurar el servidor y todas sus bases de datos en un servidor nuevo a un momento dado anterior.

## <a name="restore-in-the-azure-portal"></a>Restauración en Azure Portal
Azure Database for PostgreSQL permite restaurar el servidor a un momento dado y en una nueva copia del servidor. Puede usar este nuevo servidor para recuperar los datos. 

Por ejemplo, si una tabla se quitó accidentalmente a mediodía de hoy, podría restaurar al momento justo antes de mediodía y recuperar la tabla y los datos que faltan desde esa copia nueva del servidor.

Los siguientes pasos restauran el servidor de ejemplo a un momento dado:
1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Localice su servidor de Azure Database for PostgreSQL. En Azure Portal, haga clic en **Todos los recursos**, en el menú izquierdo, y escriba el nombre **mypgserver-20170401** para buscar el servidor existente. Haga clic en el nombre del servidor que aparece en el resultado de la búsqueda. Se abrirá la página **Información general** del servidor, que proporciona opciones para continuar la configuración.

   ![Azure Portal: busque el servidor](media/postgresql-howto-restore-server-portal/1-locate.png)

3. En la parte superior de la hoja de información general del servidor, haga clic en **Restaurar** en la barra de herramientas. Se abre la hoja Restaurar.

   ![Azure Database for PostgreSQL - Información general - Botón Restaurar](./media/postgresql-howto-restore-server-portal/2_server.png)

4. Rellene el formulario Restaurar con la información necesaria:

   ![Azure Database for PostgreSQL - Información sobre restauración ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - **Punto de restauración**: seleccione el momento antes de que se modificara la base de datos.
  - **Servidor de destino**: especifique el nombre del nuevo servidor donde desea restaurar
  - **Ubicación**: no se puede seleccionar la región; de forma predeterminada, es la misma que la del servidor de origen
  - **Plan de tarifa**: no se puede cambiar este valor al restaurar un servidor. Es el mismo que el del servidor de origen. 

5. Haga clic en **Aceptar** para restaurar el servidor a un momento dado. 

6. Una vez finalizada la restauración, busque el nuevo servidor que se crea para comprobar que los datos se restauraron del modo esperado.

## <a name="next-steps"></a>Pasos siguientes
- [Bibliotecas de conexiones de Azure Database para PostgreSQL](concepts-connection-libraries.md)
