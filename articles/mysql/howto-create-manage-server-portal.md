---
title: "Creación y administración de un servidor de Azure Database for MySQL con Azure Portal | Microsoft Docs"
description: "Este artículo describe cómo crear rápidamente un nuevo servidor de Azure Database for MySQL y administrarlo mediante Azure Portal."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6e9c541aac1241b6af0e4a58f5591d46f9a98c40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Creación y administración de un servidor de Azure Database for MySQL con Azure Portal
En este tema se describe cómo puede crear rápidamente un servidor de Azure Database for MySQL. También incluye información sobre cómo administrar el servidor mediante Azure Portal. La administración del servidor incluye ver detalles del mismo y bases de datos, restablecer la contraseña y eliminar el servidor.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en el [Portal de Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Siga estos pasos para crear un servidor de Azure Database for MySQL llamado “mysqlserver4demo”.

1. Haga clic en el botón **Nuevo** situado en la esquina superior izquierda de Azure Portal.

2. En la página Nuevo, seleccione **Bases de datos** y, en la página Bases de datos, seleccione **Azure Database for MySQL**.

    > Se crea un servidor de Azure Database for MySQL con un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-unit-and-storage.md). La base de datos se crea dentro de un grupo de recursos de Azure y en un servidor de Azure Database for MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Rellene el formulario de Azure Database for MySQL utilizando la siguiente información:

    | **Campo del formulario** | **Descripción del campo** |
    |----------------|-----------------------|
    | *Nombre del servidor* | azure-mysql (el nombre del servidor es único globalmente) |
    | *Suscripción* | MySQLaaS (seleccione en el menú desplegable) |
    | *Grupos de recursos* | myresource (cree un nuevo grupo de recursos o use uno existente) |
    | *Inicio de sesión del administrador del servidor* | myadmin (dé un nombre a la cuenta de administrador) |
    | *Password* | indique la contraseña de la cuenta de administrador |
    | *Confirmar contraseña* | confirme la contraseña de la cuenta de administrador |
    | *Ubicación* | Europa del Norte (seleccione entre Europa del Norte y Oeste de EE. UU.) |
    | *Versión* | 5.6 (elija la versión de Azure Database for MySQL) |

4. Haga clic en **Plan de tarifa** para especificar el nivel de rendimiento y el nivel de servicio del nuevo servidor. Las unidades de proceso se pueden configurar entre 50 y 100 en el nivel Básico o entre 100 y 200 en el nivel Estándar, y se puede agregar almacenamiento en función de la cantidad incluida. En esta guía, vamos a seleccionar 50 unidades de proceso y 50 GB. Haga clic en **Aceptar** para guardar la selección.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Haga clic en **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento tarda unos minutos.

    > Seleccione la opción **Anclar al panel** para realizar el seguimiento de las implementaciones fácilmente.
    > [!NOTE]
    > Aunque se admiten 1000 GB de almacenamiento en el nivel Básico y 10 000 GB en el nivel Estándar, el almacenamiento máximo está limitado temporalmente a 1000 GB en la versión preliminar pública.</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Actualización de un servidor de Azure Database for MySQL
Después de aprovisionar el nuevo servidor, el usuario tiene dos opciones para editar un servidor existente: restablecer la contraseña de administrador o escalar o reducir verticalmente el servidor cambiando las unidades de proceso.

### <a name="change-the-administrator-user-password"></a>Cambio de la contraseña del usuario administrador
1. En la hoja **Información general** del servidor, haga clic en **Restablecer contraseña** para rellenar una ventana de entrada y confirmación de contraseña.

2. Escriba y confirme la contraseña en la ventana tal y como se muestra en:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Haga clic en **Aceptar** para guardar la nueva contraseña.

### <a name="scale-updown-by-changing-compute-units"></a>Escalar o reducir verticalmente cambiando las unidades de proceso

1. En la hoja del servidor, en **Configuración**, haga clic en **Plan de tarifa** para abrir la hoja del plan de tarifa para el servidor de Azure Database for MySQL.

2. Siga el paso 4 del documento **Creación de un servidor de Azure Database for MySQL** para cambiar las unidades de proceso en el mismo plan de tarifa.

## <a name="delete-an-azure-database-for-mysql-server"></a>Eliminación de un servidor de Azure Database for MySQL

1. En la hoja **Información general** del servidor, haga clic en el botón de comando **Eliminar** para abrir la hoja de confirmación de eliminación.

2. Escriba el nombre del servidor correcto en el cuadro de entrada de la hoja para realizar una doble confirmación.

3. Haga clic de nuevo en el botón **Eliminar** para confirmar la acción de eliminación y espere a que aparezca el mensaje “Eliminación correcta” en la barra de notificación.

## <a name="list-the-azure-database-for-mysql-databases"></a>Lista de las bases de datos de Azure Database for MySQL
En la hoja **Información general** del servidor, desplácese hacia abajo hasta que vea el icono de la base de datos en la parte inferior. La tabla enumera todas las bases de datos. Haga clic en el botón **Eliminar** para abrir la hoja de confirmación de eliminación.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Presentación de los detalles de un servidor de Azure Database for MySQL
En la hoja del servidor, en **Configuración**, haga clic en **Propiedades** para abrir la hoja **Propiedades** y, a continuación, vea toda la información detallada sobre el servidor.

## <a name="next-steps"></a>Pasos siguientes

[Inicio rápido: Creación de un servidor de Azure Database for MySQL con Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)