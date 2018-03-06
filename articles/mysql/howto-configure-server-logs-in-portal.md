---
title: "Configuración y acceso a los registros de servidor de Azure Database for MySQL en Azure Portal"
description: "En este artículo se describe cómo configurar los registros de servidor de Azure Database for MySQL, y acceder a ellos, mediante Azure Portal."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b68d92688b18e69f74c0e0a9b2e572795a288579
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configuración y acceso a los registros de servidor en Azure Portal

Los [registros de servidor de Azure Database for MySQL](concepts-server-logs.md) se pueden configurar, enumerar y descargar desde Azure Portal.

## <a name="prerequisites"></a>requisitos previos
Para seguir esta guía, necesitará:
- [Servidor de Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>registro
Configure el acceso al registro de consultas lentas de MySQL. 

1. Inicie sesión en el [Azure Portal](http://portal.azure.com/).

2. Seleccione un servidor de Azure Database for MySQL.

3. En la sección **Supervisión** de la barra lateral, seleccione **Registros de servidor**. 
   ![Seleccione Registros de servidor y haga clic para configurarlos](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Seleccione el título **Haga clic aquí para habilitar los registros y configurar los parámetros** para ver los parámetros del servidor.

5. Cambie los parámetros que necesita ajustar. Todos los cambios que realice en esta sesión se resaltan en color púrpura. 

   Cuando haya cambiado los parámetros, haga clic en **Guardar**. O bien puede **descartar** los cambios.

   ![Haga clic en Guardar o en Descartar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Para volver a la lista de registros, haga clic en el **botón Cerrar** (icono de X) de la página **Parámetros de servidor**.

## <a name="view-list-and-download-logs"></a>Visualización de lista y descarga de registros
Una vez que comience el registro, puede ver una lista de registros disponibles y descargar archivos de registro individuales en el panel Registros de servidor. 

1. Abra Azure Portal.

2. Seleccione un servidor de Azure Database for MySQL.

3. En la sección **Supervisión** de la barra lateral, seleccione **Registros de servidor**. En la página se muestra una lista de los archivos de registro de esta forma:

   ![Lista de registros](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convención de nomenclatura del registro es **mysql-slow-< nombre de su servidor>-yyyymmddhh.log**. La fecha y hora que se utilizan en el nombre de archivo es el momento en que se emitió el registro. Los archivos de registros se rotan cada 24 horas o 7;5 GB,;lo que ocurra primero.

4. Si es necesario, utilice el **cuadro de búsqueda** para reducir rápidamente a un registro específico en función de la fecha y hora. La búsqueda se encuentra en el nombre del registro.

5. Descargue los archivos de registro individuales con el botón de **descarga** (icono de flecha hacia abajo) que hay junto a cada archivo de registro en la fila de tabla, como se muestra:

   ![Haga clic en el icono de descarga](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>pasos siguientes
- Consulte [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md) (Configuración y acceso a los registros del servidor con la CLI de Azure) para más información acerca de cómo descargar registros mediante programación.
- Aprenda más sobre los [registros del servidor](concepts-server-logs.md) en Azure Database for MySQL. 
- Para más información sobre las definiciones de parámetros y el registro de MySQL, consulte la documentación de MySQL sobre [registros](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

