---
title: "Compilación de una aplicación web Java y MySQL en Azure | Microsoft Docs"
description: "Aprenda a conseguir que una aplicación Java se conecte al servicio de la base de datos MySQL de Azure y que funcione en un servicio de aplicaciones de Azure."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Compilación de una aplicación web Java y MySQL en Azure
En este tutorial, se muestra cómo crear una aplicación web Java en Azure y conectarla a una base de datos MySQL. El primer paso es clonar una aplicación en el equipo local y hacer que funcione con una instancia local de MySQL.
El siguiente paso es configurar los servicios de Azure para la aplicación Java y MySQL y, seguidamente, implementar la aplicación en un servicio de aplicaciones de Azure.
Cuando termine, tendrá una aplicación de lista de tareas pendientes que se ejecuta en Azure y se conecta al servicio de la base de datos MySQL de Azure.

![Aplicación Java que se ejecuta en Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>Antes de empezar

Antes de ejecutar este ejemplo, instale los siguientes requisitos previos localmente:

1. [Descarga e instalación de Git](https://git-scm.com/)
1. [Descarga e instalación de Java 7 o posterior](http://Java.net/downloads.Java)
1. [Descarga e instalación de Maven](https://maven.apache.org/download.cgi)
1. [Descarga, instalación e inicio de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Descarga e instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>Preparación de una base de datos MySQL local

En este paso, creará una base de datos en un servidor MySQL local para usarlo en este tutorial.

### <a name="connect-to-mysql-server"></a>Conexión a un servidor MySQL
Conéctese al servidor MySQL local desde la línea de comandos:

```bash
mysql -u root -p
```

Si el comando se ejecuta correctamente, significa que el servidor MySQL ya se está ejecutando. De lo contrario, siga los [pasos posteriores a la instalación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) para asegurarse de iniciar el servidor local de MySQL.

Si se le pide una contraseña, escriba la de la cuenta `root`. Si no recuerda la contraseña de la cuenta raíz, consulte [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: Instrucciones de restablecimiento de la contraseña de la cuenta raíz).


### <a name="create-a-database-and-table"></a>Creación de una base de datos y una tabla

En el símbolo del sistema `mysql`, cree una base de datos y una tabla para los elementos pendientes.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

## <a name="create-local-java-application"></a>Creación de una aplicación Java local
En este paso, clonará un repositorio de GitHub, configurará la conexión a la base de datos MySQL y ejecutará la aplicación de forma local. 

### <a name="clone-the-sample"></a>Clonación del ejemplo

Desde el símbolo del sistema, vaya a un directorio de trabajo.  

Ejecute los comandos siguientes para clonar el repositorio de ejemplo. 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

A continuación, configure el archivo lombok.jar como se indica en el archivo Léame del repositorio.


### <a name="configure-mysql-connection"></a>Configuración de la conexión a MySQL

Esta aplicación usa el complemento Jetty de Maven para ejecutarse de forma local y conectarse a la base de datos MySQL.
Para habilitar el acceso a la instancia local de MySQL, establezca el id. de usuario y la contraseña locales de MySQL en WebContent/WEB-INF/jetty-env.xml.

Actualice los valores de usuario y contraseña con el id. de usuario y la contraseña de la instancia local de MySQL:

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt; Para obtener información sobre cómo usa Jetty el archivo `jetty-env.xml`, consulte la [referencia de XML de Jetty](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html).
&gt; &gt;

### <a name="run-the-sample"></a>Ejecución del ejemplo

Use un comando de Maven para ejecutar el ejemplo: 

```bash
mvn package jetty:run
```

A continuación, vaya a `http://localhost:8080` en un explorador. Agregue algunas tareas a la página.

Para detener la aplicación en cualquier momento, escriba `Ctrl`+`C` en el símbolo del sistema. 

## <a name="create-a-mysql-database-in-azure"></a>Crear una base de datos MySQL en Azure

En este paso, creará una base de datos MySQL en [Azure Database for MySQL (versión preliminar)](/azure/mysql). Más adelante, configurará la aplicación Java para que se conecte a esta base de datos.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Use la CLI de Azure 2.0 en una ventana de terminal para crear los recursos necesarios para hospedar la aplicación Java en Azure App Service. Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento. 

En el siguiente ejemplo, se crea un grupo de recursos en la región de Europa del Norte:

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

Para conocer qué valor posible puede usar para `--location`, use el comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Creación de un servidor MySQL

Cree un servidor en Azure Database for MySQL (versión preliminar) con el comando [az mysql server create](/cli/azure/mysql/server#create).

Reemplace su nombre de servidor MySQL único donde vea el marcador de posición `&lt;mysql_server_name&gt;`. Este nombre forma parte del nombre de host del servidor MySQL, `&lt;mysql_server_name&gt;.database.windows.net`, por lo que debe ser único. Reemplace también `&lt;admin_user&gt;` y `&lt;admin_password&gt;` por sus propios valores.

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

Cuando se crea el servidor MySQL, la CLI de Azure muestra información similar a la del siguiente ejemplo:

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>Configuración de un firewall de servidor

Cree una regla de firewall para que el servidor MySQL permita conexiones de clientes usando el comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; Azure Database for MySQL (versión preliminar) todavía no permite realizar conexiones únicamente desde servicios de Azure. Dado que las direcciones IP se asignan de forma dinámica en Azure, es mejor habilitarlas todas por el momento. El servicio se encuentra en versión preliminar, por lo que pronto habrá disponibles métodos mejores para proteger las bases de datos.
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>Conexión al servidor MySQL

En la ventana de terminal, conéctese al servidor MySQL de Azure. Use el valor que especificó anteriormente para `&lt;admin_user&gt;` y `&lt;mysql_server_name&gt;`.

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>Creación de una base de datos y una tabla en el servicio MySQL de Azure

En el símbolo del sistema `mysql`, cree una base de datos y una tabla para los elementos pendientes.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>Creación de un usuario con permisos

Cree un usuario de base de datos y concédale todos los privilegios de la base de datos `todoItemDb`. Reemplace los marcadores de posición `&lt;Javaapp_user&gt;` y `&lt;Javaapp_password&gt;` por su propio nombre de aplicación único.

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>Configuración de la conexión local de MySQL con el nuevo servicio de Azure MySQL

En este paso, conectará la aplicación Java a la base de datos MySQL que creó en Azure Database for MySQL (versión preliminar). 

Para habilitar el acceso al servicio MySQL de Azure desde la aplicación local, establezca el nuevo punto de conexión, el nuevo id. de usuario y la nueva contraseña de MySQL en WebContent/WEB-INF/jetty-env.xml:

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

Guarde los cambios.

## <a name="test-the-application"></a>Prueba de la aplicación

Use el mismo comando de Maven que usó anteriormente para volver a ejecutar el ejemplo de forma local, pero esta vez conectándose al servicio MySQL de Azure: 

```bash
mvn package jetty:run
```

Vaya a `http://localhost:8080` en un explorador. Si la página se carga sin errores, significa que la aplicación Java está conectándose a la base de datos MySQL de Azure. 

No debería aparecer la opción Agregue algunas tareas a la página.

Para detener la aplicación en cualquier momento, escriba `Ctrl`+`C` en el terminal. 

### <a name="secure-sensitive-data"></a>Protección de información confidencial

Asegúrese de que la información confidencial de `WebContent/WEB-INF/jetty-env.xml` no se confirme en Git.

Para ello, abra `.gitignore` en la raíz del repositorio y agregue `WebContent/WEB-INF/jetty-env.xml` en una nueva línea. Guarde los cambios.

Confirme los cambios realizados en `.gitignore`.

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>Implementación de la aplicación Java en Azure
A continuación, implementará la aplicación de Java en un servicio de aplicaciones de Azure.

### <a name="create-an-appservice-plan"></a>Creación de un plan del servicio de aplicaciones

Cree un plan del servicio de aplicaciones con el comando [az appservice plan create](/cli/azure/appservice/plan#create). 

&gt; [!NOTE] 
&gt; Un plan de servicio de aplicaciones representa la colección de recursos físicos que se usan para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de servicio de aplicaciones comparten los recursos definidos por él, lo que permite ahorrar costos al hospedar varias aplicaciones. 
&gt; &gt; Los planes de servicio de aplicaciones definen: &gt; &gt; * La región (Europa del Norte, este de EE. UU. o Sudeste Asiático) &gt; * El tamaño de la instancia (pequeño, mediano o grande) &gt; * El recuento de escala (una, dos, tres instancias, etc.) &gt; * El valor de SKU (Gratis, Compartido, Básico, Estándar y Premium) &gt; 

En el siguiente ejemplo, se crea un plan de servicio de aplicaciones denominado `myAppServicePlan` con el plan de tarifa **Gratis**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Al crear el plan de servicio de aplicaciones, la CLI de Azure muestra información similar a la del ejemplo siguiente:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>Creación de una aplicación web de Azure

Una vez creado un plan del servicio de aplicaciones, cree una aplicación web de Azure dentro del plan `myAppServicePlan`. La aplicación web ofrece un espacio de hospedaje para implementar el código y proporciona una dirección URL para que pueda ver la aplicación implementada. Use el comando [az appservice web create](/cli/azure/appservice/web#create) para crear la aplicación web. 

En el siguiente comando, reemplace el marcador de posición `&lt;app_name&gt;` por su propio nombre de aplicación único. Este nombre único se usará como parte del nombre de dominio predeterminado para la aplicación web, por lo que el nombre debe ser único en todas las aplicaciones de Azure. Más adelante puede asignar cualquier entrada de DNS personalizada a la aplicación web antes de exponerla a los usuarios. 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
```

Cuando se ha creado la aplicación web, la CLI de Azure muestra información similar a la del ejemplo siguiente: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>Establecimiento de la versión de Java, el tipo de servidor de aplicaciones Java y la versión del servidor de aplicaciones

Establezca la versión de Java, el servidor de aplicaciones Java (contenedor) y la versión del contenedor con el comando [az appservice web config update](/cli/azure/appservice/web/config#update).

El siguiente comando establece la versión 8 de Java, el servidor de aplicaciones Java Jetty y la versión de Jetty en la 9.3 más reciente.

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>Obtención de las credenciales de implementación de la aplicación web mediante FTP 

Puede implementar la aplicación en el servicio de aplicaciones Azure de diversas formas, incluidos un FTP, un repositorio de Git local, GitHub, Visual Studio Team Services y BitBucket. En este ejemplo, usamos Maven para compilar un archivo .WAR y un FTP para implementar el archivo .WAR en la aplicación web.

Para determinar qué credenciales pasar a la aplicación web en un comando de FTP, use el comando [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles) como se indica a continuación: 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>Compilación de la aplicación local para la implementación en la aplicación web 

Para preparar la aplicación Java local para ejecutarse en la aplicación web de Azure, vuelva a compilar todos los recursos de la aplicación Java en un único archivo .WAR listo para la implementación. Vaya al directorio en el que se encuentra el archivo pom.xml de la aplicación y escriba:

```bash 
mvn clean package
``` 
Cerca del final del proceso del paquete de Maven, verá la ubicación del archivo .WAR.  El resultado debe parecerse a este:

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

Tenga en cuenta la ubicación del archivo .WAR y use el método de FTP que prefiera para implementar el archivo .WAR en la carpeta WebApps de Jetty.  Tenga en cuenta que la carpeta WebApps de Jetty se encuentra en /sitio/wwwRaíz/webApps en las aplicaciones web de Azure. 

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure

Vaya a `http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` y agregue algunas tareas a la lista. 

![Aplicación Java que se ejecuta en Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**¡Enhorabuena!** Ya está ejecutando una aplicación Java orientada a datos en Azure App Service.
Para actualizar la aplicación, repita el comando de limpieza de paquete de Maven y vuelva a implementar la aplicación mediante FTP.

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya a Azure Portal para consultar la aplicación web que creó. Para ello, inicie sesión en [https://portal.azure.com](https://portal.azure.com).

En el menú izquierdo, haga clic en el **servicio de aplicaciones** y, a continuación, en el nombre de la aplicación web de Azure.

Ahora, debería encontrarse en la _hoja_ de la aplicación web (una página del portal que se abre horizontalmente).

De forma predeterminada, la hoja de la aplicación web muestra la página de **introducción**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la hoja muestran las diferentes páginas de configuración que puede abrir.

En la página **Configuración de la aplicación**, 

![Configuración de la aplicación de las aplicaciones web de Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



Estas pestañas de la hoja muestran las muchas y excepcionales características que puede agregar a la aplicación web. La lista siguiente proporciona solo algunas de las posibilidades:

* Asignación de un nombre DNS personalizado
* Enlace de un certificado SSL personalizado
* Configuración de la implementación continua
* Escalado vertical y horizontal
* Adición de la autenticación de usuarios

## <a name="more-resources"></a>Más recursos

- [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)
- [Enlace de un certificado SSL personalizado a Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
- [Scripts de CLI de aplicaciones web](app-service-cli-samples.md)</PRE>

