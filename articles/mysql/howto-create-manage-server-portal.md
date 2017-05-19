---
title: "Creación y administración de un servidor de Azure Database for MySQL con Azure Portal | Microsoft Docs"
description: "Este artículo describe cómo crear rápidamente un nuevo servidor de Azure Database for MySQL y administrarlo mediante Azure Portal."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: cbf1bcd90c53a7221773c7dc7731b37cf762e953
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Creación y administración de un servidor de Azure Database for MySQL con Azure Portal
Este artículo describe cómo crear rápidamente un nuevo servidor de Azure Database for MySQL y administrarlo mediante Azure Portal. La administración del servidor incluye ver las bases de datos y los detalles del servidor, restablecer la contraseña y eliminar el servidor.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en el portal de Azure
Inicie sesión en el [Portal de Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Siga estos pasos para crear un servidor de Azure Database for MySQL llamado "mysqlserver4demo".

1- Haga clic en el botón **Nuevo**, en la esquina superior izquierda de Azure Portal.

2- En la página Nuevo, seleccione **Bases de datos** y, en la página Bases de datos, seleccione **Azure Database for MySQL**.

> Se crea un servidor de Azure Database for MySQL con un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-unit-and-storage.md). La base de datos se crea dentro de un grupo de recursos de Azure y en un servidor de Azure Database for MySQL.

![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3- Rellene el formulario de Azure Database for MySQL con la siguiente información:

| **Campo del formulario** | **Descripción del campo** |
|----------------|-----------------------|
| *Nombre del servidor* | azure-mysql (el nombre del servidor es único globalmente) |
| *Suscripción* | MySQLaaS (seleccione en la lista desplegable) |
| *Grupos de recursos* | myresource (cree un nuevo grupo de recursos o use uno existente) |
| *Inicio de sesión del administrador del servidor* | myadmin (dé un nombre a la cuenta de administrador) |
| *Password* | indique la contraseña de la cuenta de administrador |
| *Confirmar contraseña* | confirme la contraseña de la cuenta de administrador |
| *Ubicación* | Europa del Norte (seleccione entre Europa del Norte y Oeste de EE. UU.) |
| *Versión* | 5.6 (elija la versión de Azure Database for MySQL) |

4- Haga clic en **Plan de tarifa** para especificar el nivel de rendimiento y el nivel de servicio del nuevo servidor. Las unidades de proceso se pueden configurar entre 50 y 100 en el nivel básico o entre 100 y 200 en el nivel estándar, y se puede agregar almacenamiento en función de la cantidad incluida. En esta guía, vamos a seleccionar 50 unidades de proceso y 50 GB. Haga clic en **Aceptar** para guardar la selección.
![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5- Haga clic en **Crear** para aprovisionar el servidor. El aprovisionamiento tarda unos minutos.

> Active la opción **Anclar al panel** para realizar el seguimiento de las implementaciones fácilmente.
> [!NOTE]
> Aunque se admitirán 1000 GB de almacenamiento en el nivel básico y 10 000 GB en el nivel estándar, el almacenamiento máximo está limitado a 1000 GB temporalmente en la versión preliminar pública. 
</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Actualización de un servidor de Azure Database for MySQL
Después de aprovisionar el nuevo servidor, el usuario tiene dos opciones para editar un servidor existente: restablecer la contraseña de administrador o escalar o reducir verticalmente el servidor cambiando las unidades de proceso.

### <a name="change-the-administrator-user-password"></a>Cambio de la contraseña del usuario administrador
1 - En la hoja **Información general** del servidor, haga clic en **Restablecer contraseña** para rellenar una ventana de entrada y confirmación de contraseña.

2- Escriba y confirme la contraseña en la ventana tal y como se muestra en: ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3- Haga clic en **Aceptar** para guardar la nueva contraseña.

### <a name="scale-updown-by-changing-compute-units"></a>Escalar o reducir verticalmente cambiando las unidades de proceso

1- En la hoja del servidor, en **Configuración**, haga clic en **Plan de tarifa** para abrir la hoja del plan de tarifa para el servidor de Azure Database for MySQL.

2- Siga el paso 4 del documento **Creación de un servidor de Azure Database for MySQL** para cambiar las unidades de proceso en el mismo plan de tarifa.

## <a name="delete-an-azure-database-for-mysql-server"></a>Eliminación de un servidor de Azure Database for MySQL

1- En la hoja **Información general** del servidor, haga clic en el botón **Eliminar** para abrir la hoja de confirmación de eliminación.

2- Escriba el nombre del servidor en el cuadro de entrada de la hoja para realizar una doble confirmación.

3- Haga clic de nuevo en el botón **Eliminar** para confirmar la acción de eliminación y espere al mensaje "Eliminación correcta" en la barra de notificación.

## <a name="list-the-azure-database-for-mysql-databases"></a>Lista de las bases de datos de Azure Database for MySQL
En la hoja **Información general** del servidor, desplácese hacia abajo hasta que vea el icono de la base de datos en la parte inferior. La tabla enumera todas las bases de datos. Haga clic en el botón **Eliminar** para abrir la hoja de confirmación de eliminación.

![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Presentación de los detalles de un servidor de Azure Database for MySQL
Haga clic en **Propiedades** en **Configuración** en la hoja del servidor y se abrirá la hoja **Propiedades**. A continuación, podrá ver toda la información detallada del servidor.

## <a name="next-steps"></a>Pasos siguientes

[Inicio rápido: Creación de un servidor de Azure Database for MySQL con Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)

