---
title: "Azure Portal: Creación de una instancia de Azure Database for PostgreSQL | Microsoft Docs"
description: "Guía de inicio rápido para crear y administrar la instancia de Azure Database for PostgreSQL a través de la interfaz de usuario de Azure Portal."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 8bba5a97fdc4bbc15fe996ee68daf5b796d1bfac
ms.contentlocale: es-es
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Creación de una instancia de Azure Database for PostgreSQL en Azure Portal

Azure Database for PostgreSQL es un servicio administrado que le permite ejecutar, administrar y escalar bases de datos de PostgreSQL de alta disponibilidad en la nube. Esta guía de inicio rápido muestra cómo crear un servidor de Azure Database for PostgreSQL a través de Azure Portal en unos cinco minutos.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Abra el explorador web y vaya a [Microsoft Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

## <a name="create-an-azure-database-for-postgresql"></a>Creación de una instancia de Azure Database for PostgreSQL

Un servidor de Azure Database for PostgreSQL se crea con un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-unit-and-storage.md). El servidor se crea dentro de un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

Para crear un servidor de Azure Database for PostgreSQL, siga estos pasos:
1.  Haga clic en el botón **Nuevo** (+) de la esquina superior izquierda de Azure Portal.
2.  En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **Azure Database for PostgreSQL**.
 ![Azure Database for PostgreSQL: creación de la base de datos](./media/quickstart-create-database-portal/1-create-database.png)

3.  Rellene el formulario con los datos del nuevo servidor con la siguiente información, como se muestra en la imagen anterior:

    Configuración|Valor sugerido|Descripción
    ---|---|---
    Nombre de servidor |*mypgserver-20170401*|Elija un nombre único que identifique al servidor de Azure Database for PostgreSQL. El nombre de dominio *postgres.database.azure.com* se anexa al nombre del servidor proporcionado para que las aplicaciones se conecten. El nombre de servidor puede contener solo letras minúsculas, números y el carácter guion (-) y tiene que tener una extensión de entre 3 y 63 caracteres.
    La suscripción|*Su suscripción*|La suscripción de Azure que desea usar para el servidor. Si tiene varias suscripciones, elija la suscripción en la que se factura el recurso.
    Grupo de recursos|*myresourcegroup*| Puede crear un nuevo nombre de grupo de recursos o usar uno existente de la suscripción.
    Inicio de sesión de administrador de servidor |*mylogin*| Cree su propia cuenta de inicio de sesión para usarla al conectarse al servidor. El nombre de inicio de sesión de administrador no puede ser 'azure_superuser', 'azure_pg_admin', 'admin', 'administrator', 'root', 'guest' ni 'public' y no puede comenzar con 'pg_'.
    Password |*Su elección* | Cree una nueva contraseña para la cuenta de administrador del servidor. Debe contener entre 8 y 128 caracteres. La contraseña debe contener caracteres de tres de las siguientes categorías: letras en mayúsculas del alfabeto inglés, letras en minúscula del alfabeto inglés, números (0-9) y caracteres no alfanuméricos (!, $, #, %, etc.).
    Ubicación|*Región más cercana a los usuarios*| Elija la ubicación más cercana a los usuarios.
    Versión de PostgreSQL|*Elija la versión más reciente*| Elija la versión más reciente a menos que tenga requisitos específicos.
    Nivel de precios | **Básico**, **50 unidades de proceso** **50 GB** | Haga clic en **Plan de tarifa** para especificar el tanto el nivel de rendimiento como el nivel de servicio de la nueva base de datos. Elija el nivel Básico en la pestaña en la parte superior. Haga clic en el extremo izquierdo del control deslizante de unidades de proceso para ajustar el valor en la menor cantidad disponible para este tutorial rápido. Haga clic en **Aceptar** para guardar este plan de tarifa. Vea la siguiente captura de pantalla.
    | Anclar al panel | Comprobar | Active la opción **Anclar al panel** para permitir realizar un seguimiento fácil del servidor en la página del panel frontal de Azure Portal.

  > [!IMPORTANT]
  > Se requiere el inicio de sesión y la contraseña de administrador de servidor que especifique aquí para iniciar sesión en el servidor y a sus bases de datos más adelante en esta guía de inicio rápido. Recuerde o grabe esta información para su uso posterior.

    ![Azure Database for PostgreSQL: selección del plan de tarifa](./media/quickstart-create-database-portal/2-service-tier.png)

4.  Haga clic en **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento tarda unos minutos, hasta un máximo de 20 minutos.

5.  En la barra de herramientas, haga clic en **Notificaciones** para supervisar el proceso de implementación.
 ![Azure Database for PostgreSQL: consulta de las notificaciones](./media/quickstart-create-database-portal/3-notifications.png)
   
  De forma predeterminada, la base de datos de **postgres** se crea en el servidor. La base de datos [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) es una base de datos predeterminada pensada para que la usen los usuarios, las utilidades y aplicaciones de otros fabricantes. 

## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

El servicio Azure Database for PostgreSQL crea un firewall en el nivel de servidor. Este firewall evita que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos de este, a menos que se cree una regla de firewall que lo abra para direcciones IP concretas. 

1.  Localice el servidor una vez finalizada la implementación. Si es necesario, puede buscarlo. Por ejemplo, haga clic en **Todos los recursos** del menú izquierdo y escriba el nombre del servidor (en el ejemplo, *mypgserver-20170401*) para buscar el servidor recién creado. Haga clic en el nombre del servidor que aparece en el resultado de la búsqueda. Se abrirá la página **Introducción** del servidor, que proporciona opciones para continuar la configuración.
 
    ![Azure Database for PostgreSQL: búsqueda del nombre del servidor](./media/quickstart-create-database-portal/4-locate.png)

2.  En la página del servidor, seleccione **Seguridad de la conexión**. 
    ![Azure Database for PostgreSQL: creación de una regla de firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  En el encabezado **Reglas de firewall**, haga clic en el cuadro de texto en blanco de la columna **Nombre de la regla** para empezar a crear la regla de firewall. 

    En este inicio rápido, vamos a permitir todas las direcciones IP en el servidor rellenando el cuadro de texto de cada columna con los valores siguientes:

    Nombre de la regla | Dirección IP inicial | Dirección IP final 
    ---|---|---
    AllowAllIps (permitir todas las direcciones IP) |  0.0.0.0 | 255.255.255.255

4. En la barra de herramientas superior de la página Seguridad de la conexión, haga clic en **Guardar**. Espere unos segundos y observe la notificación que muestra que la actualización de seguridad de la conexión ha finalizado correctamente antes de continuar.

    > [!NOTE]
    > Las conexiones al servidor de Azure Database for PostgreSQL se comunican a través de puerto 5432. Si intenta conectarse desde una red corporativa, es posible que el firewall de la red no permita el tráfico saliente a través del puerto 5432. Si es así, no podrá conectarse al servidor a menos que el departamento de TI abra el puerto 5432.
    >

## <a name="get-the-connection-information"></a>Obtención de la información de conexión

Al crear el servidor de Azure Database for PostgreSQL, también se crea la base de datos predeterminada denominada **postgres**. Para conectarse al servidor de bases de datos, recuerde que debe utilizar las credenciales de inicio de sesión de administrador y el nombre de servidor completo. Ha tomado nota de esos valores anteriormente en el artículo de inicio rápido. En caso de que no lo hiciera, encontrará fácilmente el nombre del servidor y la información de inicio de sesión en la página de introducción del servidor en Azure Portal.

1. Abra la página **Información general** del servidor. Tome nota del **Nombre del servidor** y del **Server admin login name** (Nombre de inicio de sesión del administrador del servidor).
    Desplace el cursor sobre cada campo y el icono de copiar aparece a la derecha del texto. Haga clic en el icono de copiar según sea necesario para copiar los valores.

 ![Azure Database for PostgreSQL: inicio de sesión del administrador del servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Conexión a la base de datos de PostgreSQL mediante psql de Cloud Shell

Hay una serie de aplicaciones que se pueden usar para conectarse al servidor de Azure Database for PostgreSQL. Ahora vamos a usar la utilidad de línea de comandos psql para conectarnos al servidor.  Puede utilizar un explorador web y Azure Cloud Shell como se describe aquí sin necesidad de instalar ningún software adicional. Si tiene la utilidad psql instalada localmente en su propio equipo, puede conectarse desde allí también.

1. Inicie Azure Cloud Shell desde el icono del terminal en el panel de navegación superior.

   ![Azure Database for PostgreSQL: icono del terminal de Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Azure Cloud Shell se abrirá en el explorador y podrá escribir comandos de shell de Bash.

   ![Azure Database for PostgreSQL: indicador de Bash de Azure Shell](./media/quickstart-create-database-portal/8-bash.png)

3. En el símbolo de sistema de Cloud Shell, conéctese a una base de datos del servidor de Azure Database for PostgreSQL, para lo que debe escribir la línea de comandos de psql en el símbolo verde del sistema.

    El formato siguiente sirve para conectarse a un servidor de Azure Database for PostgreSQL con la utilidad [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Por ejemplo, el siguiente comando conecta con un servidor de ejemplo:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    Parámetro de psql |Valor sugerido|Descripción
    ---|---|---
    --host | *nombre del servidor* | Especifique el valor de nombre de servidor que se usó cuando creó el servidor de Azure Database for PostgreSQL anteriormente. El servidor de ejemplo que se muestra es mypgserver-20170401.postgres.database.azure.com. Use el nombre de dominio completo (\*. postgres.database.azure.com) tal como se muestra en el ejemplo. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre del servidor. 
    --port | **5432** | Utilice siempre el puerto 5432 para conectarse a Azure Database for PostgreSQL. 
    --username | *nombre de inicio de sesión del administrador del servidor* |Escriba el valor de nombre de inicio de sesión del administrador del servidor que se usó cuando creó el servidor de Azure Database for PostgreSQL anteriormente. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre de usuario.  El formato es *username@servername*.
    --dbname | **postgres** | Use el nombre de base de datos predeterminado generado por el sistema *postgres* para la primera conexión. Posteriormente, puede crear su propia base de datos.

    Después de ejecutar el comando psql, con sus propios valores de parámetros, se le pedirá que escriba la contraseña del administrador del servidor. Es la misma contraseña que especificó al crear el servidor. 

    Parámetro de psql |Valor sugerido|Descripción
    ---|---|---
    Contraseña | *contraseña del administrador* | Tenga en cuenta que los caracteres que escriba en la contraseña no se muestran en el símbolo del sistema de bash. Presione ENTRAR después de haber escrito todos los caracteres para autenticarse y conectarse.

    Una vez conectado, la utilidad psql muestra un símbolo de sistema de postgres donde escribir comandos sql. En la salida de la conexión inicial, puede mostrarse una advertencia, ya que la versión de psql de Azure Cloud Shell puede diferir de la versión del servidor de Azure Database for PostgreSQL. 
    
    Ejemplo de salida de psql:
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Si el firewall no está configurado para permitir que la dirección IP de la instancia de Azure Cloud Shell, se produce el siguiente error:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL:  SSL connection is required. Please specify SSL options and retry.
    > 
    > Para resolver el error, asegúrese de que la configuración del servidor coincida con los pasos descritos en la sección *Configuración de una regla de firewall de nivel de servidor* del artículo.

4.  Escriba el comando siguiente para crear una base de datos vacía en el símbolo del sistema:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    El comando puede tardar un rato en completarse. 

5.  En el símbolo del sistema, ejecute el siguiente comando para cambiar la conexión a la base de datos **mypgsqldb** recién creada.
    ```bash
    \c mypgsqldb
    ```

6.  Escriba \q y, a continuación, presione ENTRAR para salir de psql. Cuando haya terminado, puede cerrar Azure Cloud Shell.

Ahora está conectado a Azure Database for PostgreSQL y ha creado una base de datos de usuario en blanco. Pase a la sección siguiente para la conexión con otra herramienta común, pgAdmin.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Conexión a la base de datos de PostgreSQL mediante pgAdmin

Para conectarse al servidor Azure PostgreSQL mediante la herramienta _pgAdmin_ de la GUI:
1.  Inicie la aplicación _pgAdmin_ en el equipo cliente. Puede instalar _pgAdmin_ desde http://www.pgadmin.org/.
2.  Haga clic en el icono **Agregar nuevo servidor** desde la sección **Vínculos rápidos** del centro de la página Panel.
3.  En el cuadro de diálogo **Create - Server** (Crear: servidor) de la pestaña **General**, escriba un nombre descriptivo único para el servidor, como **Azure PostgreSQL Server**.
![Herramienta pgAdmin: crear servidor](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  En la pestaña **Connection** (Conexión) del cuadro de diálogo **Create - Server** (Crear: servidor), utilice la configuración especificada y haga clic en **Save** (Guardar).
   ![pgAdmin: creación del servidor](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    parámetro pgAdmin |Valor sugerido|Descripción
    ---|---|---
    Host Name/Address | *nombre del servidor* | Especifique el valor de nombre de servidor que se usó cuando creó el servidor de Azure Database for PostgreSQL anteriormente. El servidor de ejemplo que se muestra es mypgserver-20170401.postgres.database.azure.com. Use el nombre de dominio completo (\*. postgres.database.azure.com) tal como se muestra en el ejemplo. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre del servidor. 
    Port | **5432** | Utilice siempre el puerto 5432 para conectarse a Azure Database for PostgreSQL.  
    Maintenance Database | **postgres** | Use el nombre de base de datos predeterminado que genera el sistema, *postgres*.
    User Name | *nombre de inicio de sesión del administrador del servidor* | Escriba el valor de nombre de inicio de sesión del administrador del servidor que se usó al crear el servidor de Azure Database for PostgreSQL. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre de usuario. El formato es *username@servername*.
    Password | *contraseña del administrador* |  La contraseña que eligió al crear el servidor antes en esta guía de inicio rápido.
    Rol | *déjelo en blanco* | No es necesario proporcionar un nombre de rol en este momento. Deje el campo en blanco.
    SSL Mode | Necesario | De forma predeterminada, todos los servidores de Azure PostgreSQL se crean para que se EXIJA SSL. Para DESACTIVAR la obligación de SSL, consulte los detalles de la sección sobre la [obligación de SSL](./concepts-ssl-connection-security.md).
    
5.  Haga clic en **Guardar**.
6.  En el panel izquierdo del explorador, expanda el nodo **Servidores**. Elija el servidor, por ejemplo **Azure PostgreSQL Server** y haga clic para conectarse a él.
7. Expanda el nodo de servidor y **Bases de datos**. La lista debe incluir la base de datos *postgres* existente y las bases de datos de usuario recién creadas, como *mypgsqldb*, que hemos creado en la sección anterior. Tenga en cuenta que con Azure Database for PostgreSQL puede crear varias bases de datos por servidor.
8. Haga clic con el botón derecho en **Bases de datos**, elija el menú **Crear** y haga clic en **Base de datos**.
9.  Escriba el nombre de base de datos que quiera en el campo **Base de datos**, como *mypgsqldb* en el ejemplo. 
10. Seleccione el **propietario** de la base de datos en el cuadro desplegable. Elija el nombre de inicio de sesión de administrador del servidor, como *mylogin* en nuestro ejemplo.
10. Haga clic en **Guardar** para crear una base de datos vacía.
11. En el panel del **explorador**, consulte la base de datos que ha creado en la lista de bases de datos debajo del nombre del servidor.
 ![pgAdmin: Crear base de datos](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Limpie los recursos que creó en la guía de inicio rápido mediante la eliminación, ya sea del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) (que incluye todos los recursos del grupo de recursos), o bien, del recurso del servidor (si desea mantener intactos los demás recursos).

> [!TIP]
> Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido, no elimine los recursos creados en esta. Si no tiene previsto continuar, siga estos pasos para eliminar los recursos creados en esta guía de inicio rápido en Azure Portal.

Para eliminar todo el grupo de recursos, incluido el servidor recién creado:
1.  Elimine el grupo de recursos en Azure Portal. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del grupo de recursos, en nuestro ejemplo, **myresourcegroup**.
2.  En la página del grupo de recursos, haga clic en **Eliminar**. A continuación, escriba el nombre del grupo de recursos (en nuestro ejemplo, **myresourcegroup**) en el cuadro de texto para confirmar la eliminación y haga clic en **Eliminar**.

Como alternativa, para eliminar el servidor recién creado:
1.  Busque el servidor en Azure Portal si no lo tiene abierto. En el menú izquierdo de Azure Portal, haga clic en **Todos los recursos** y busque el servidor que ha creado.
2.  En la página **Introducción**, haga clic en el botón **Eliminar** del panel superior.
![Azure Database for PostgreSQL: eliminación del servidor](./media/quickstart-create-database-portal/12-delete.png)
3.  Confirme el nombre del servidor que desea eliminar y muestre las bases de datos incluidas. Escriba el nombre del servidor en el cuadro de texto (en nuestro ejemplo **mypgserver-20170401**) y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./howto-migrate-using-export-and-import.md)

