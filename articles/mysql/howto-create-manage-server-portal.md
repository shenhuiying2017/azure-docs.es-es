---
title: "Creación y administración de un servidor de Azure Database for MySQL con Azure Portal"
description: "Este artículo describe cómo crear rápidamente un nuevo servidor de Azure Database for MySQL y administrarlo mediante Azure Portal."
services: mysql
author: ajlam
ms.author: nolanwu
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0e274c0ada3de5e9000ae41516e5b9b67ef1490b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Creación y administración de un servidor de Azure Database for MySQL con Azure Portal
En este tema se describe cómo puede crear rápidamente un servidor de Azure Database for MySQL. También incluye información sobre cómo administrar el servidor mediante Azure Portal. La administración del servidor incluye ver detalles del mismo y bases de datos, restablecer la contraseña, escalar recursos y eliminar el servidor.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Siga estos pasos para crear un servidor de Azure Database for MySQL llamado “mydemoserver”.

1. Haga clic en el botón **Crear un recurso** de la esquina superior izquierda de Azure Portal.

2. En la página Nuevo, seleccione **Bases de datos** y, en la página Bases de datos, seleccione **Azure Database for MySQL**.

    > Se crea un servidor de Azure Database for MySQL con un conjunto definido de [recursos de proceso y almacenamiento](./concepts-pricing-tiers.md). La base de datos se crea dentro de un grupo de recursos de Azure y en un servidor de Azure Database for MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Rellene el formulario de Azure Database for MySQL utilizando la siguiente información:

    | **Campo del formulario** | **Descripción del campo** |
    |----------------|-----------------------|
    | *Nombre del servidor* | mydemoserver (el nombre del servidor es único globalmente) |
    | *Suscripción* | mysubscription (seleccione en el menú desplegable) |
    | *Grupos de recursos* | myresourcegroup (cree un nuevo grupo de recursos o use uno existente) |
    | *Seleccionar origen* | En blanco (cree un servidor de MySQL en blanco) |
    | *Inicio de sesión del administrador del servidor* | myadmin (dé un nombre a la cuenta de administrador) |
    | *Password* | indique la contraseña de la cuenta de administrador |
    | *Confirmar contraseña* | confirme la contraseña de la cuenta de administrador |
    | *Ubicación* | Sudeste Asiático (seleccione Europa del Norte u Oeste de EE. UU.) |
    | *Versión* | 5.7 (elija la versión del servidor de Azure Database for MySQL) |

4. Haga clic en **Plan de tarifa** para especificar el nivel de rendimiento y el nivel de servicio del nuevo servidor. Seleccione la pestaña **Uso general**. *Gen 4*, *2 núcleos virtuales*, *5 GB* y *7 días* son los valores predeterminados de **Generación de procesos**, **Núcleos virtuales**, **Almacenamiento** y **Período de retención de copia de seguridad**. Puede dejar los controles deslizantes tal y como están. Para habilitar las copias de seguridad del servidor en el almacenamiento con redundancia geográfica, seleccione **Redundancia geográfica** en **Opciones de redundancia de copia de seguridad**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Haga clic en **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento tarda unos minutos.

    > Seleccione la opción **Anclar al panel** para realizar el seguimiento de las implementaciones fácilmente.

## <a name="update-an-azure-database-for-mysql-server"></a>Actualización de un servidor de Azure Database for MySQL
Una vez que se haya aprovisionado el nuevo servidor, el usuario tiene varias opciones para configurar el servidor existente. Entre estas opciones cabe incluir el restablecimiento de la contraseña de administrador y el escalado o reducción vertical cambiando los núcleos virtuales o el almacenamiento.

### <a name="change-the-administrator-user-password"></a>Cambio de la contraseña del usuario administrador
1. En **Información general** del servidor, haga clic en **Restablecer contraseña** para mostrar la ventana de restablecimiento de contraseñas.

   ![Información general](./media/howto-create-manage-server-portal/overview.png)

2. Escriba y confirme la contraseña en la ventana tal y como se muestra en:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Haga clic en **Aceptar** para guardar la nueva contraseña.

### <a name="scale-vcores-updown"></a>Escalado y reducción vertical de los núcleos virtuales

1. Haga clic en **Plan de tarifa**, que se encuentra en **Configuración**.

2. Cambie el valor de **vCore** moviendo el control deslizante hasta alcanzar el valor deseado.

    ![escalar proceso](./media/howto-create-manage-server-portal/scale-compute.png)

3. Haga clic en **Aceptar** para guardar los cambios.

### <a name="scale-storage-up"></a>Escalado vertical del almacenamiento

1. Haga clic en **Plan de tarifa**, que se encuentra en **Configuración**.

2. Cambie el valor de **Almacenamiento** moviendo el control deslizante hasta alcanzar el valor deseado.

    ![escalar almacenamiento](./media/howto-create-manage-server-portal/scale-storage.png)

3. Haga clic en **Aceptar** para guardar los cambios.

## <a name="delete-an-azure-database-for-mysql-server"></a>Eliminación de un servidor de Azure Database for MySQL

1. En la hoja **Información general** del servidor, haga clic en el botón **Eliminar** para abrir la pregunta de confirmación de eliminación.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Escriba el nombre del servidor en el cuadro de entrada para realizar una doble confirmación.

    ![confirmar eliminación](./media/howto-create-manage-server-portal/confirm.png)

3. Haga clic en el botón **Eliminar** para confirmar la eliminación del servidor. Espere hasta que aparezca el aviso emergente "El servidor MySQL se eliminó correctamente" en la barra de notificaciones.

## <a name="list-the-azure-database-for-mysql-databases"></a>Lista de las bases de datos de Azure Database for MySQL
En **Información general** del servidor, desplácese hacia abajo hasta que vea el icono de la base de datos en la parte inferior. Todas las bases de datos del servidor se muestran en la tabla.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Presentación de los detalles de un servidor de Azure Database for MySQL
Haga clic en **Propiedades**, que se encuentra en **Configuración** para ver información detallada acerca del servidor.

![propiedades](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>pasos siguientes

[Inicio rápido: Creación de un servidor de Azure Database for MySQL con Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)