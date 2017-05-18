---
title: "Inicio rápido: Creación de un servidor de Azure Database for MySQL con Azure Portal | Microsoft Docs"
description: "Este artículo le ayudará a usar Azure Portal para crear rápidamente un servidor de Azure Database for MySQL de ejemplo, en unos cinco minutos."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 25bfd2c6c25ddb8747dec58fdc68f904f81127fa
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Creación de un servidor de Azure Database for MySQL con Azure Portal

Este artículo le ayudará a usar Azure Portal para crear un servidor de Azure Database for MySQL de ejemplo en unos cinco minutos. 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Abra el explorador web y vaya a [Microsoft Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

![Azure Portal: inicio de sesión y panel](./media/quickstart-create-mysql-server-database-using-azure-portal/1_portal-login.png)

## <a name="create-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL

1. Vaya a **Bases de datos** > **MySQL**. Si no encuentra el servidor de Azure Database for MySQL en la categoría **Bases de datos**, haga clic en **Ver todo** para mostrar todos los servicios de base de datos disponibles. También puede escribir **MySQL** en el cuadro de búsqueda para encontrar el servicio rápidamente.
![Azure Portal: nueva - base de datos - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

2. Haga clic en el icono **MySQL** y en **Crear**.
En nuestro ejemplo, rellene la página de Azure Database for MySQL con la siguiente información:

| **Campo del formulario** | **Descripción del campo** |
|----------------|-----------------------|
| *Nombre del servidor* | mysqlserver4demo (el nombre del servidor es único a nivel global) |
| *Suscripción* | MySQLaaS (selecciónela de la lista desplegable) |
| *Grupos de recursos* | myresource (cree un grupo de recursos o use uno existente) |
| *Inicio de sesión del administrador del servidor* | myadmin (dele un nombre a la cuenta de administrador) |
| *Password* | indique la contraseña de la cuenta de administrador |
| *Confirmar contraseña* | confirme la contraseña de la cuenta de administrador |
| *Ubicación* | Europa del Norte (seleccione **Europa del Norte** o **Oeste de EE. UU.**) |
| *Versión* | 5.6 (elija la versión del servidor MySQL) |
| *Configurar rendimiento* | Básico (elija el **Nivel de rendimiento**, las **Unidades de proceso**, el **Almacenamiento** y haga clic en **Aceptar**) |

![Azure Portal: introducción de los datos del formulario correctos para crear MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

Pasados unos minutos, el servidor de Azure Database for MySQL estará aprovisionado y en ejecución. Puede hacer clic en el botón **Notificaciones** (icono de la campana) de la barra de herramientas para supervisar la implementación.

> [!TIP]
> Se recomienda colocar los servicios de Azure en la misma región y seleccionar la ubicación que le resulte más próxima. Además, puede marcar la opción **Anclar al panel** para realizar el seguimiento de las implementaciones fácilmente.

## <a name="configure-the-firewall"></a>Configuración del firewall
Antes de conectarse a Azure Database for MySQL desde el cliente por primera vez, debe configurar el firewall y agregar la dirección IP (o intervalo de direcciones IP) de red pública del cliente a la lista de admitidos.

1. Haga clic en el servidor recién creado y en **Configuración**.
  ![Azure Portal: MySQL, botón Configuración](./media/quickstart-create-mysql-server-database-using-azure-portal/4_server-settings.png)

2. En la sección **GENERAL**, haga clic en **Configuración de firewall**. Puede hacer clic en **Agregar mi IP** para agregar la dirección IP del equipo local o configurar un intervalo de direcciones IP. Recuerde hacer clic en **Guardar** después de haber creado las reglas.
  ![Azure Portal: incorporación de la regla de firewall y guardado](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Obtención de información sobre la conexión
En Azure Portal encontrará el nombre completo de dominio del servidor Azure MySQL. Utilice el nombre completo de dominio para conectarse al servidor mediante la herramienta **mysql.exe** de la línea de comandos.

1.    En [Azure Portal](https://portal.azure.com/), haga clic en **Todos los recursos** en el menú izquierdo y en el servidor de Azure Database for MySQL.

2.    Haga clic en **Propiedades**. Anote los valores de **NOMBRE DEL SERVIDOR** e **INICIO DE SESIÓN DEL ADMINISTRADOR DEL SERVIDOR**.
En este ejemplo, el nombre del servidor es *mysql4doc.database.windows.net* y el inicio de sesión del administrador del servidor, *mysqladmin@mysql4doc*.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Conexión al servidor mediante la herramienta mysqlexe de la línea de comandos
En un servidor MySQL puede crear varias bases de datos. No hay límite para el número de bases de datos que se pueden crear, pero las bases de datos compartirán los mismos recursos del servidor.  Para conectarse al servidor mediante la herramienta **mysql.exe** de la línea de comandos, abra **Azure Cloud Shell** en el portal e introduzca lo siguiente:

1. Conéctese al servidor con la herramienta **mysql** de la línea de comandos:
```dos
 mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

2. Consulte el estado del servidor:
```dos
 mysql> status
```
  ![Símbolo del sistema: ejemplo de línea de comandos de mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

> [!TIP]
> Para otros comandos, consulte el [capítulo 4.5.1 del Manual de referencia de MySQL 5.6](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conexión al servidor con la herramienta MySQL Workbench de la GUI
1.    Lance la aplicación MySQL Workbench en el equipo cliente. Puede descargar e instalar MySQL Workbench desde [aquí](https://dev.mysql.com/downloads/workbench/).

2.    En el cuadro de diálogo **Setup New Connection** (Establecer nueva conexión), escriba la siguiente información en la pestaña **Parámetros**:

| **Parámetros** | **Descripción** |
|----------------|-----------------|
|    *Nombre de la conexión* | especifique un nombre para esta conexión (puede ser cualquier cosa) |
| *Connection Method* (Método de conexión) | elija Estándar (TCP/IP) |
| *Nombre de host* | mycliserver.database.windows.net (el NOMBRE DEL SERVIDOR que anotó antes) |
| *Puerto* | 3306 |
| *Nombre de usuario* | myadmin@mycliserver(EL INICIO DE SESIÓN DEL ADMINISTRADOR DEL SERVIDOR que anotó antes) |
| *Password* | puede almacenar la contraseña de la cuenta de administrador en el almacén |

![Configuración de una conexión nueva](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

3.    Haga clic en **Probar conexión** para probar si todos los parámetros están configurados correctamente.

4.    Ahora puede hacer clic en la conexión que acaba de crear para conectarse al servidor.

> SSL se aplica de forma predeterminada en el servidor que requiere configuración adicional para conectarse correctamente. Consulte [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configuración de SSL en la aplicación para la conexión segura a Azure Database for MySQL).  Si desea deshabilitar SSL para esta guía de inicio rápido, vaya a "Seguridad de la conexión" en el portal para deshabilitar la obligatoriedad de SSL.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no necesita estos recursos para otra guía de inicio rápido o tutorial, puede eliminarlos de la siguiente manera:

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en **myresource**. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba **myresource** en el cuadro de texto y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Diseño de la primera base de datos de Azure Database for MySQL](./tutorial-design-database-using-portal.md)


