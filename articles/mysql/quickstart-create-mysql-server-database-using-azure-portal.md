---
title: "Inicio rápido: Creación de un servidor de Azure Database for MySQL con Azure Portal | Microsoft Docs"
description: "Este artículo le ayudará a usar Azure Portal para crear rápidamente un servidor de Azure Database for MySQL de ejemplo, en unos cinco minutos."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: es-es
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Creación de un servidor de Azure Database for MySQL con Azure Portal
Azure Database for MySQL es un servicio administrado que le permite ejecutar, administrar y escalar bases de datos de MySQL de alta disponibilidad en la nube. Esta guía de inicio rápido muestra cómo crear un servidor de Azure Database for MySQL a través de Azure Portal en unos cinco minutos. 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Abra el explorador web y vaya a [Microsoft Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

## <a name="create-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Se crea un servidor de Azure Database for MySQL con un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-unit-and-storage.md). El servidor se crea dentro de un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

Para crear un servidor de Azure Database for MySQL, siga estos pasos:

1. Haga clic en el botón **Nuevo** (+) de la esquina superior izquierda de Azure Portal.

2. En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **Azure Database for MySQL**. También puede escribir **MySQL** en el cuadro de búsqueda de la página Nuevo para encontrar el servicio.
![Azure Portal: nueva - base de datos - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Rellene el formulario con los datos del nuevo servidor con la siguiente información, como se muestra en la imagen anterior:

    **Configuración** | **Valor sugerido** | **Descripción del campo** 
    ---|---|---
    Nombre de servidor | myserver4demo | Elija un nombre único que identifique al servidor de Azure Database for MySQL. El nombre de dominio *mysql.database.azure.com* se anexa al nombre del servidor proporcionado para que las aplicaciones se conecten. El nombre de servidor puede contener solo letras minúsculas, números y el carácter guion (-) y tiene que tener una extensión de entre 3 y 63 caracteres.
    La suscripción | Su suscripción | La suscripción de Azure que desea usar para el servidor. Si tiene varias suscripciones, elija la suscripción en la que se factura el recurso.
    Grupos de recursos | myresourcegroup | Puede crear un nuevo nombre de grupo de recursos o usar uno existente de la suscripción.
    Inicio de sesión de administrador de servidor | myadmin | Cree su propia cuenta de inicio de sesión para usarla al conectarse al servidor. El nombre de inicio de sesión de administrador no puede ser "azure_superuser", "admin", "administrator", "root", "guest" ni "public".
    Password | *Su elección* | Cree una nueva contraseña para la cuenta de administrador del servidor. Debe contener entre 8 y 128 caracteres. La contraseña debe contener caracteres de tres de las siguientes categorías: letras en mayúsculas del alfabeto inglés, letras en minúscula del alfabeto inglés, números (0-9) y caracteres no alfanuméricos (!, $, #, %, etc.).
    Confirmar contraseña | *Su elección*| Confirme la contraseña de la cuenta de administrador.
    Ubicación | *Región más cercana a los usuarios*| Elija la ubicación más cercana a los usuarios u otras aplicaciones de Azure.
    Versión | *Elija la versión más reciente*| Elija la versión más reciente a menos que tenga requisitos específicos.
    Nivel de precios | **Básico**, **50 unidades de proceso** **50 GB** | Haga clic en **Plan de tarifa** para especificar el tanto el nivel de rendimiento como el nivel de servicio de la nueva base de datos. Elija el **nivel Básico** en la pestaña de la parte superior. Haga clic en el extremo izquierdo del control deslizante de **Unidades de proceso** para ajustar el valor en la menor cantidad disponible para esta guía de inicio rápido. Haga clic en **Aceptar** para guardar este plan de tarifa. Vea la siguiente captura de pantalla.
    Anclar al panel | Comprobar | Active la opción **Anclar al panel** para permitir realizar un seguimiento fácil del servidor en la página del panel frontal de Azure Portal.

    > [!IMPORTANT]
    > Para iniciar sesión en el servidor y las bases de datos de esta guía de inicio rápido necesitará el inicio de sesión y la contraseña que especifique aquí. Recuerde o grabe esta información para su uso posterior.
    > 

    ![Azure Portal: introducción de los datos del formulario correctos para crear MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Haga clic en **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento tarda unos minutos, hasta un máximo de 20 minutos.
   
5.  En la barra de herramientas, haga clic en **Notificaciones** (icono de campana) para supervisar el proceso de implementación.

## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

El servicio Azure Database for MySQL crea un firewall en el nivel de servidor. Este firewall evita que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos de este, a menos que se cree una regla de firewall que lo abra para direcciones IP concretas. 

1.  Localice el servidor una vez finalizada la implementación. Si es necesario, puede buscarlo. Por ejemplo, haga clic en **Todos los recursos** del menú izquierdo y escriba el nombre del servidor (en el ejemplo, *myserver4demo*) para buscar el servidor recién creado. Haga clic en el nombre del servidor que aparece en el resultado de la búsqueda. Se abrirá la página **Introducción** del servidor, que proporciona opciones para continuar la configuración.

2. En la página del servidor, seleccione **Seguridad de la conexión**.

3.  En el encabezado **Reglas de firewall**, haga clic en el cuadro de texto en blanco de la columna **Nombre de la regla** para empezar a crear la regla de firewall. 

    En esta guía de inicio rápido, vamos a permitir todas las direcciones IP en el servidor al rellenar el cuadro de texto de cada columna con los valores siguientes:

    Nombre de la regla | Dirección IP inicial | Dirección IP final 
    ---|---|---
    AllowAllIps (permitir todas las direcciones IP) |  0.0.0.0 | 255.255.255.255

4. En la barra de herramientas superior de la página **Seguridad de la conexión**, haga clic en **Guardar**. Espere unos segundos y observe la notificación que muestra que la actualización de seguridad de la conexión ha finalizado correctamente antes de continuar.

    > [!NOTE]
    > Las conexiones a Azure Database for MySQL se comunican a través del puerto 3306. Si intenta conectarse desde una red corporativa, es posible que el firewall de la red no permita el tráfico saliente a través del puerto 3306. Si es así, no podrá conectarse al servidor a menos que el departamento de TI abra el puerto 3306.
    > 

## <a name="get-the-connection-information"></a>Obtención de la información de conexión
Para conectarse al servidor de bases de datos, recuerde que debe utilizar las credenciales de inicio de sesión de administrador y el nombre de servidor completo. Ha tomado nota de esos valores anteriormente en el artículo de inicio rápido. En caso de que no lo hiciera, puede encontrar fácilmente el nombre del servidor y la información de inicio de sesión en la página **Introducción** o **Propiedades** de Azure Portal.

1. Abra la página **Información general** del servidor. Tome nota del **Nombre del servidor** y del **Server admin login name** (Nombre de inicio de sesión del administrador del servidor). 
    Desplace el cursor sobre cada campo y el icono de copiar aparece a la derecha del texto. Haga clic en el icono de copiar según sea necesario para copiar los valores.

    En este ejemplo, el nombre del servidor es *myserver4demo.mysql.database.azure.com* y el inicio de sesión del administrador del servidor, *myadmin@myserver4demo*.

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>Conexión a MySQL con la herramienta de la línea de comandos mysql
Hay una serie de aplicaciones que se pueden usar para conectarse al servidor de Azure Database for MySQL. Ahora vamos a usar la herramienta de línea de comandos [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para conectarnos al servidor.  Puede utilizar un explorador web y Azure Cloud Shell como se describe aquí sin necesidad de instalar ningún software adicional. Si tiene la utilidad mysql instalada en su propia máquina, puede conectarse desde allí también.

1. Inicie Azure Cloud Shell desde el icono del terminal (>_) de la parte superior derecha de la página web de Azure Portal.

2. Azure Cloud Shell se abrirá en el explorador y podrá escribir comandos de shell de Bash.

    ![Símbolo del sistema: ejemplo de línea de comandos de mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. En el símbolo de sistema de Cloud Shell, conéctese al servidor de Azure Database for MySQL, para lo que debe escribir la línea de comandos de mysql en el símbolo del sistema verde.

    El formato siguiente sirve para conectarse a un servidor de Azure Database for MySQL con la utilidad mysql:
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Por ejemplo, el siguiente comando conecta con nuestro servidor de ejemplo:
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    parámetro mysql |Valor sugerido|Descripción
    ---|---|---
    --host | *nombre del servidor* | Especifique el valor de nombre de servidor que se usó cuando creó el servidor de Azure Database for MySQL. El servidor de ejemplo que se muestra es myserver4demo.mysql.database.azure.com. Use el nombre de dominio completo (\*.mysql.database.azure.com) tal como se muestra en el ejemplo. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre del servidor. 
    --user | *nombre de inicio de sesión del administrador del servidor* |Escriba el valor de nombre de inicio de sesión del administrador del servidor que se usó al crear el servidor de Azure Database for MySQL. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre de usuario.  El formato es *username@servername*.
    --password | *espere hasta que se le pida* | Se le pedirá que "escriba la contraseña" después del comando. Cuando esto ocurra, escriba la misma contraseña que especificó cuando al crear el servidor.  Tenga en cuenta que los caracteres que escriba de la contraseña no se muestran en el símbolo del sistema de bash al escribirlos. Presione ENTRAR después de haber escrito todos los caracteres para autenticarse y conectarse.

   Una vez conectado, la utilidad de mysql muestra un símbolo del sistema `mysql>` para que escriba los comandos. 

    Ejemplo de salida de mysql:
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Si el firewall no está configurado para permitir que la dirección IP de la instancia de Azure Cloud Shell, se produce el siguiente error:
    >
    > ERROR 2003 (28000): no está permitido que el cliente con la dirección IP 123.456.789.0 acceda al servidor.
    >
    > Para resolver el error, asegúrese de que la configuración del servidor coincida con los pasos descritos en la sección *Configuración de una regla de firewall de nivel de servidor* del artículo.

4. Mire el estado del servidor para asegurarse de que la conexión funciona. Escriba `status` en el símbolo del sistema mysql> una vez que esté conectado.
    ```sql
    status
    ```

   > [!TIP]
   > Para otros comandos, consulte el [capítulo 4.5.1 del Manual de referencia de MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Escriba el comando siguiente para crear una base de datos vacía en el símbolo del sistema mysql>:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    El comando puede tardar un rato en completarse. 

    En un servidor de Azure Database for MySQL, puede crear una o varias bases de datos. Puede optar por crear una sola base de datos por servidor para que se usen todos los recursos, o bien crear varias bases de datos para compartir los recursos. No hay límite para el número de bases de datos que se pueden crear, pero las bases de datos compartirán los mismos recursos del servidor. 

6. Escriba el comando siguiente para enumerar las bases de datos en el símbolo del sistema mysql>:

    ```sql
    SHOW DATABASES;
    ```

7.  Escriba `\q` y presione ENTRAR para salir de la herramienta mysql. Cuando haya terminado, puede cerrar Azure Cloud Shell.

Ahora está conectado a Azure Database for MySQL y ha creado una base de datos de usuario en blanco. Pase a la sección siguiente para repetir un ejercicio parecido y conectarse al mismo servidor con otra herramienta común, MySQL Workbench.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conexión al servidor con la herramienta MySQL Workbench de la GUI
Para conectarse al servidor Azure MySQL mediante la herramienta MySQL Workbench de la GUI:

1.  Inicie la aplicación MySQL Workbench en el equipo cliente. Puede descargar e instalar MySQL Workbench desde [aquí](https://dev.mysql.com/downloads/workbench/).

2.  En el cuadro de diálogo **Setup New Connection** (Establecer nueva conexión), escriba la siguiente información en la pestaña **Parámetros**:

    ![Configuración de una conexión nueva](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **Configuración** | **Valor sugerido** | **Descripción del campo** |
    |---|---|---|
    |   Nombre de la conexión | Conexión de demostración | Especifique una etiqueta para esta conexión. |
    | Método de conexión | Estándar (TCP/IP) | Estándar (TCP/IP) es suficiente. |
    | Nombre de host. | *nombre del servidor* | Especifique el valor de nombre de servidor que se usó cuando creó el servidor de Azure Database for MySQL. El servidor de ejemplo que se muestra es myserver4demo.mysql.database.azure.com. Use el nombre de dominio completo (\*.mysql.database.azure.com) tal como se muestra en el ejemplo. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre del servidor.  |
    | Port | 3306 | Utilice siempre el puerto 3306 para conectarse a Azure Database for MySQL. |
    | Nombre de usuario |  *nombre de inicio de sesión del administrador del servidor* | Escriba el valor de nombre de inicio de sesión del administrador del servidor que se usó al crear el servidor de Azure Database for MySQL. El nombre de usuario de nuestro ejemplo es myadmin@myserver4demo. Siga los pasos de la sección anterior para obtener la información de conexión si no recuerda el nombre de usuario. El formato es *username@servername*.
    | Password | la contraseña | Haga clic en el botón Store in Vault (Almacenar en el almacén)... para guardar la contraseña. |

    Haga clic en **Probar conexión** para probar si todos los parámetros están configurados correctamente. Haga clic en Aceptar para guardar la conexión. 

    > [!NOTE]
    > SSL se aplica de forma predeterminada en el servidor que requiere configuración adicional para conectarse correctamente. Consulte [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configuración de la conectividad SSL en la aplicación para conectarse de forma segura a Azure Database for MySQL).  Si desea deshabilitar SSL para esta guía de inicio rápido, visite Azure Portal y haga clic en la página Seguridad de la conexión para deshabilitar el botón de alternancia Aplicar conexión SSL.

## <a name="clean-up-resources"></a>Limpieza de recursos
Limpie los recursos que creó en la guía de inicio rápido mediante la eliminación, ya sea del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) (que incluye todos los recursos del grupo de recursos), o bien, del recurso del servidor (si desea mantener intactos los demás recursos).

> [!TIP]
> Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido, no elimine los recursos creados en esta. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal.
>

Para eliminar todo el grupo de recursos, incluido el servidor recién creado:
1.  Elimine el grupo de recursos en Azure Portal. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del grupo de recursos, en nuestro ejemplo, **myresourcegroup**.
2.  En la página del grupo de recursos, haga clic en **Eliminar**. A continuación, escriba el nombre del grupo de recursos (en nuestro ejemplo, **myresourcegroup**) en el cuadro de texto para confirmar la eliminación y haga clic en **Eliminar**.

Como alternativa, para eliminar el servidor recién creado:
1.  Busque el servidor en Azure Portal si no lo tiene abierto. En el menú izquierdo de Azure Portal, haga clic en **Todos los recursos** y busque el servidor que ha creado.
2.  En la página **Introducción**, haga clic en el botón **Eliminar** del panel superior.
![Azure Database for MySQL: eliminación del servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Confirme el nombre del servidor que desea eliminar y muestre las bases de datos incluidas. Escriba el nombre del servidor en el cuadro de texto (en nuestro ejemplo **myserver4demo**) y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Diseño de la primera base de datos de Azure Database for MySQL](./tutorial-design-database-using-portal.md)


