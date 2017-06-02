---
title: "Azure Portal: Creación de una instancia de Azure Database for PostgreSQL | Microsoft Docs"
description: "Guía de inicio rápido para crear y administrar la instancia de Azure Database for PostgreSQL a través de la interfaz de usuario de Azure Portal."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: quick start create, mvc
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c67ada15c11b81021ff5e6f6e5edc5cb530ece98
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Creación de una instancia de Azure Database for PostgreSQL en Azure Portal

Azure Database for PostgreSQL es un servicio administrado que le permite ejecutar, administrar y escalar bases de datos de PostgreSQL de alta disponibilidad en la nube. Esta guía de inicio rápido muestra cómo crear un servidor de Azure Database for PostgreSQL a través de Azure Portal.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en el portal de Azure

Inicie sesión en el [Portal de Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Creación de una instancia de Azure Database for PostgreSQL

Un servidor de Azure Database for PostgreSQL se crea con un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-unit-and-storage.md). El servidor se crea dentro de un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

Para crear un servidor de Azure Database for PostgreSQL, siga estos pasos:
1.    Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2.    En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **Azure Database for PostgreSQL**.
 ![Azure Database for PostgreSQL: creación de la base de datos](./media/quickstart-create-database-portal/1-create-database.png)

3.    Rellene el formulario con los datos del nuevo servidor con la siguiente información, como se muestra en la imagen anterior:
    - Nombre del servidor: **mypgserver-20170401** (el nombre del servidor se asigna al nombre DNS y, por tanto, debe ser único a nivel global) 
    - Suscripción: si tiene varias, elija la suscripción donde se encuentre el recurso o para la cual se facture.
    - Grupo de recursos: **myresourcegroup**
    - El inicio de sesión de administrador y la contraseña que elija para el servidor
    - Ubicación
    - Versión de PostgreSQL

  > [!IMPORTANT]
  > Se requiere el inicio de sesión y la contraseña de administrador de servidor que especifique aquí para iniciar sesión en el servidor y a sus bases de datos más adelante en esta guía de inicio rápido. Recuerde o grabe esta información para su uso posterior.

4.    Haga clic en **Plan de tarifa** para especificar el tanto el nivel de rendimiento como el nivel de servicio de la nueva base de datos. En esta guía de inicio rápido, seleccione el nivel **Básico**, **50 Unidades de proceso** y **50 GB** de almacenamiento incluido.
 ![Azure Database for PostgreSQL: selección del nivel de servicio](./media/quickstart-create-database-portal/2-service-tier.png)
5.    Haga clic en **Aceptar**.
6.    Haga clic en **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento tarda unos minutos.

  > [!TIP]
  > Marque la opción **Anclar al panel** para realizar el seguimiento de las implementaciones fácilmente.

7.    En la barra de herramientas, haga clic en **Notificaciones** para supervisar el proceso de implementación.
 ![Azure Database for PostgreSQL: consulta de las notificaciones](./media/quickstart-create-database-portal/3-notifications.png)
   
  De forma predeterminada, la base de datos de **postgres** se crea en el servidor. La base de datos de [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) es una base de datos predeterminada para que la usen los usuarios, las utilidades y aplicaciones de otros fabricantes. 

## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

El servicio Azure Database for PostgreSQL crea un firewall en el nivel de servidor. Este firewall evita que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos de este, a menos que se cree una regla de firewall para abrirlo para direcciones IP concretas. 

1.    Una vez finalizada la implementación, haga clic en **Todos los recursos** del menú izquierdo y escriba el nombre **mypgserver-20170401** para buscar el servidor recién creado. Haga clic en el nombre del servidor que aparece en el resultado de la búsqueda. Se abrirá la página **Introducción** del servidor, que proporciona opciones para continuar la configuración.
 
 ![Azure Database for PostgreSQL: búsqueda de un servidor ](./media/quickstart-create-database-portal/4-locate.png)

2.    En la hoja del servidor, seleccione **Seguridad de la conexión**. 
3.    Haga clic en el cuadro de texto de **Nombre de la regla,** y agregue una nueva regla de firewall para añadir el intervalo de IP de conectividad a la lista de permitidos. Para esta guía de inicio rápido, vamos a permitir todas las direcciones IP; para ello, escriba **Nombre de la regla = AllowAllIps**, **IP inicial = 0.0.0.0** e **IP final = 255.255.255.255** y haga clic en **Guardar**. Puede establecer una regla de firewall que abarque un intervalo de IP para poder conectarse desde la red.

 ![Azure Database for PostgreSQL: creación de una regla de firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

4.    Haga clic en **Guardar** y en la **X** para cerrar la página **Seguridad de la conexión**.

  > [!NOTE]
  > El servidor Azure PostgreSQL se comunica a través de puerto 5432. Si intenta conectarse desde una red corporativa, es posible que el firewall de la red no permita el tráfico saliente a través del puerto 5432. Si es así, no podrá conectarse al servidor Azure SQL Database, a menos que el departamento de TI abra el puerto 5432.
  >

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Al crear el servidor de Azure Database for PostgreSQL, también se crea la base de datos de **postgres** predeterminada. Para conectarse al servidor de bases de datos, debe proporcionar las credenciales de acceso y la información del host.

1. En el menú izquierdo de Azure Portal, haga clic en **Todos los recursos** y busque el servidor **mypgserver-20170401** que acaba de crear.

  ![Azure Database for PostgreSQL: búsqueda de un servidor ](./media/quickstart-create-database-portal/4-locate.png)

2. Haga clic en el nombre del servidor **mypgserver-20170401**.
3. Seleccione la página **Introducción** del servidor. Tome nota del **Nombre del servidor** y del **Server admin login name** (Nombre de inicio de sesión del administrador del servidor).

 ![Azure Database for PostgreSQL: inicio de sesión del administrador del servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Conexión a la base de datos de PostgreSQL mediante psql de Cloud Shell

Ahora vamos a usar la utilidad de línea de comandos psql para conectarnos al servidor de Azure Database for PostgreSQL. 
1. Inicie Azure Cloud Shell desde el icono del terminal en el panel de navegación superior.

   ![Azure Database for PostgreSQL: icono del terminal de Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Azure Cloud Shell se abrirá en el explorador y podrá escribir comandos de Bash.

   ![Azure Database for PostgreSQL: indicador de Bash de Azure Shell](./media/quickstart-create-database-portal/8-bash.png)

3. En el símbolo de sistema de Cloud Shell, conéctese al servidor de Azure Database for PostgreSQL con los comandos psql. El formato siguiente sirve para conectarse a un servidor de Azure Database for PostgreSQL con la utilidad [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --port=<port> --username=<server admin login> --dbname=<database name>
   ```

   Por ejemplo, el siguiente comando se conecta a la base de datos predeterminada denominada **postgres** en el servidor PostgreSQL **mypgserver-20170401.postgres.database.azure.com** con las credenciales de acceso. Escriba la contraseña de administrador del servidor cuando se le solicite.

   ```bash
   psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
   ```
4.  Una vez conectado al servidor, cree una base de datos vacía en el símbolo del sistema.
```bash
CREATE DATABASE mypgsqldb;
```

5.  En el símbolo del sistema, ejecute el siguiente comando para cambiar la conexión a la base de datos **mypgsqldb** recién creada.
```bash
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Conexión a la base de datos de PostgreSQL mediante pgAdmin

Para conectarse al servidor Azure PostgreSQL mediante la herramienta _pgAdmin_ de la GUI:
1.    Lance la aplicación _pgAdmin_ en el equipo cliente. Puede instalar _pgAdmin_ desde http://www.pgadmin.org/.
2.    Elija **Add New Server** (Agregar nuevo servidor) en el menú **Quick Links** (Vínculos rápidos).
3.    En el cuadro de diálogo **Create - Server** (Crear: servidor) de la pestaña **General**, escriba un nombre descriptivo único para el servidor, como **Azure PostgreSQL Server**.
![Herramienta pgAdmin: crear servidor](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.    En la pestaña **Connection** (Conexión) del cuadro de diálogo **Create - Server** (Crear: servidor), utilice la configuración especificada y haga clic en **Save** (Guardar).
   ![pgAdmin: creación del servidor](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Host Name/Address** (Nombre/dirección del host): mypgserver-20170401.postgres.database.azure.com 
        - Nombre completo del servidor.
    - **Port** (Puerto): 5432
        - El número de puerto que utiliza el servidor de bases de datos es 5432.
    - **Maintenance Database** (Base de datos de mantenimiento): postgres 
        - Nombre de la base de datos predeterminada que crea el sistema.
    - **User Name** (Nombre de usuario): mylogin@mypgserver-20170401 
        - El inicio de sesión de administrador del servidor (user@mypgserver) que se obtuvo antes en esta guía.
    - **Password** (Contraseña): la contraseña que eligió al crear el servidor antes en esta guía.
    - **SSL Mode** (Modo SSL): obligatorio
        - De forma predeterminada, todos los servidores de Azure PostgreSQL se crean para que se EXIJA SSL. Para DESACTIVAR la obligación de SSL, consulte los detalles de la sección sobre la [obligación de SSL](./concepts-ssl-connection-security.md).
5.    Haga clic en **Guardar**.
6.    En el panel izquierdo del explorador, expanda **Server Groups** (Grupos de servidores). Elija el servidor **Azure PostgreSQL Server**.
7.  Elija el **Server** (Servidor) al que se ha conectado y sus **Databases** (Bases de datos). 
8.    Haga doble clic con el botón derecho en **Databases** (Bases de datos) para crear una base de datos.
9.    Elija el nombre de base de datos **mypgsqldb** y su propietario **mylogin** (inicio de sesión de administrador del servidor).
10. Haga clic en **Save** (Guardar) para crear una base de datos vacía.
11. En **Browser** (Explorador), expanda **Servers** (Servidores). Expanda el servidor que ha creado y su base de datos **mypgsqldb**.
 ![pgAdmin: creación de la base de datos](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Elimine el [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) para eliminar todos los recursos que ha creado en la guía de inicio rápido.

> [!TIP]
> Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido, no elimine los recursos creados en esta. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal.

1.    En el menú izquierdo de Azure Portal, haga clic en **Resource groups** (Grupos de recursos) y en **myresourcegroup**.
2.    En la página del grupo de recursos, haga clic en **Delete** (Eliminar), escriba **myresourcegroup** en el cuadro de texto y haga clic en Delete (Eliminar).

Si solo desea eliminar el servidor recién creado:
1.    En el menú izquierdo de Azure Portal, haga clic en los servidores de PostgreSQL y busque el que acaba de crear.
2.    En la página de introducción, haga clic en el botón Delete (Eliminar) del panel superior ![Azure Database for PostgreSQL: eliminación de servidor](./media/quickstart-create-database-portal/12-delete.png).
3.    Confirme el nombre del servidor que desea eliminar y observe si sus bases de datos están incluidas. Escriba **mypgserver-20170401** en el cuadro de texto y haga clic en Delete (Eliminar).

## <a name="next-steps"></a>Pasos siguientes
- Migración de la base de datos mediante [exportación e importación](./howto-migrate-using-export-and-import.md) o [abandono y restauración](./howto-migrate-using-dump-and-restore.md).
- Para crear el servidor de Azure Database for PostgreSQL con la CLI de Azure, consulte el artículo sobre la [Creación de un servidor PostgreSQL con la CLI](./quickstart-create-server-database-azure-cli.md).
- Para información técnica, consulte el artículo [Acerca del servicio Azure Database for PostgreSQL](./overview.md).
