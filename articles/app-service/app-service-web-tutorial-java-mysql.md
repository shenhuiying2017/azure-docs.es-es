---
title: "Compilación de una aplicación web Java y MySQL en Azure"
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
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: ad53575b655ebec5a134c8d76b963708caf14334
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Compilación de una aplicación web Java y MySQL en Azure

> [!NOTE]
> En este artículo se implementa una aplicación en App Service en Windows. Para implementar en App Service en _Linux_, consulte [Implementación de una aplicación en contenedores de Spring Boot en Azure](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin).
>

En este tutorial se muestra cómo crear una aplicación web Java en Azure y conectarla a una base de datos MySQL. Cuando haya terminado, tendrá una aplicación [Spring Boot](https://projects.spring.io/spring-boot/) que almacena datos en [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) y que se ejecuta en [Azure App Service Web Apps](app-service-web-overview.md).

![Aplicación Java que se ejecuta en Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una base de datos MySQL en Azure
> * Conectar una aplicación de ejemplo a la base de datos
> * Implementación de la aplicación en Azure
> * Actualización de la aplicación y nueva implementación
> * Transmitir registros de diagnóstico desde Azure
> * Supervisar la aplicación en Azure Portal


## <a name="prerequisites"></a>requisitos previos

1. [Descarga e instalación de Git](https://git-scm.com/)
1. [Descarga e instalación de Java 7 JDK o superior](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Descarga, instalación e inicio de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparación de MySQL local 

En este paso, creará una base de datos en un servidor MySQL local para usarlo al probar la aplicación localmente en la máquina.

### <a name="connect-to-mysql-server"></a>Conexión a un servidor MySQL

En una ventana de terminal, conéctese al servidor MySQL local. Esta ventana de terminal se puede usar para ejecutar todos los comandos de este tutorial.

```bash
mysql -u root -p
```

Si se le pide una contraseña, escriba la de la cuenta `root`. Si no recuerda la contraseña de la cuenta raíz, consulte [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: Instrucciones de restablecimiento de la contraseña de la cuenta raíz).

Si el comando se ejecuta correctamente, significa que el servidor MySQL ya se está ejecutando. De lo contrario, siga los [pasos posteriores a la instalación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) para asegurarse de iniciar el servidor local de MySQL.

### <a name="create-a-database"></a>Creación de una base de datos 

En el símbolo del sistema `mysql`, cree una base de datos y una tabla para los elementos pendientes.

```sql
CREATE DATABASE tododb;
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Creación y ejecución de la aplicación de ejemplo 

En este paso, va a clonar una aplicación de Spring Boot de ejemplo, la va a configurar para utilizar la base de datos MySQL local y la ejecutará en el equipo. 

### <a name="clone-the-sample"></a>Clonación del ejemplo

En la ventana de terminal, vaya a un directorio de trabajo y clone el repositorio de ejemplo. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Configuración de la aplicación para que use la base de datos de MySQL

Actualice el valor `spring.datasource.password` en *spring-boot-mysql-todo/src/main/resources/application.properties* con la misma contraseña raíz usada para abrir el símbolo del sistema de MySQL:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo

Compile y ejecute el ejemplo mediante el contenedor de Maven incluido en el repositorio:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Abra el explorador en `http://localhost:8080` para ver el ejemplo en acción. A medida que agrega tareas a la lista, utilice los siguientes comandos SQL de la línea de comandos de MySQL para ver los datos almacenados en MySQL.

```SQL
use testdb;
select * from todo_item;
```

Detenga la aplicación presionando `Ctrl`+`C` en el terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Creación de una base de datos MySQL de Azure

En este paso, va a crear una instancia de [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) mediante la [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Va a configurar la aplicación de ejemplo para usar esta base de datos más adelante en el tutorial.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y administran recursos relacionados como aplicaciones web, bases de datos y cuentas de almacenamiento. 

En el siguiente ejemplo, se crea un grupo de recursos en la región de Europa del Norte:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Para ver los valores posibles que puede usar para `--location`, use el comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Creación de un servidor MySQL

En Cloud Shell, cree un servidor en Azure Database for MySQL (versión preliminar) con el comando [az mysql server create](/cli/azure/mysql/server#create).    
Reemplace su nombre de servidor MySQL único donde vea el marcador de posición `<mysql_server_name>`. Este nombre forma parte del nombre de host del servidor MySQL, `<mysql_server_name>.mysql.database.azure.com`, por lo que debe ser único. Reemplace también `<admin_user>` y `<admin_password>` por sus propios valores.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

Cuando se crea el servidor MySQL, la CLI de Azure muestra información similar a la del siguiente ejemplo:

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configuración del firewall del servidor

En Cloud Shell, cree una regla de firewall para que el servidor MySQL permita conexiones de clientes con el comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure Database for MySQL (versión preliminar) no permite en este momento habilitar conexiones automáticamente desde servicios de Azure. Dado que las direcciones IP se asignan de forma dinámica en Azure, es mejor habilitarlas todas por el momento. Como el servicio continúa en versión preliminar, se habilitarán métodos mejores para proteger la base de datos.

## <a name="configure-the-azure-mysql-database"></a>Configuración de la base de datos MySQL de Azure

En la ventana del terminal local, conéctese al servidor MySQL de Azure. Use el valor que especificó anteriormente para `<admin_user>` y `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Creación de una base de datos 

En el símbolo del sistema `mysql`, cree una base de datos y una tabla para los elementos pendientes.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Creación de un usuario con permisos

Cree un usuario de base de datos y concédale todos los privilegios de la base de datos `tododb`. Reemplace los marcadores de posición `<Javaapp_user>` y `<Javaapp_password>` por su propio nombre de aplicación único.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Implementación de la aplicación en Azure App Service

Cree un plan de Azure App Service con el plan de tarifa **GRATIS** mediante el comando [az appservice plan create](/cli/azure/appservice/plan#create) de la CLI. Un plan de servicio de aplicaciones define los recursos físicos que se usan para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de servicio de aplicaciones comparten los recursos, lo que permite ahorrar costos al hospedar varias aplicaciones. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Una vez preparado el plan, la CLI de Azure muestra un resultado similar al ejemplo siguiente:

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
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Creación de una aplicación web de Azure

 En Cloud Shell, use el comando de la CLI [az webapp create](/cli/azure/appservice/web#create) para crear una definición de aplicación web en el plan de App Service `myAppServicePlan`. La definición de la aplicación web proporciona una dirección URL para acceder a la aplicación y configura varias opciones para implementar el código en Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Sustituya el marcador de posición `<app_name>` por su propio nombre de aplicación único. Este nombre único forma parte del nombre de dominio predeterminado para la aplicación web, por lo que debe ser único en todas las aplicaciones de Azure. Puede asignar una entrada de nombre de dominio personalizada a la aplicación web antes de exponerla a los usuarios.

Cuando está preparada la definición de la aplicación web, la CLI de Azure muestra información similar a la del ejemplo siguiente: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configuración de Java 

En Cloud Shell, establezca la configuración del sistema en tiempo de ejecución de Java que necesita la aplicación con el comando [az appservice web config update](/cli/azure/appservice/web/config#update).

El siguiente comando configura la aplicación web para que se ejecute en una versión reciente de Java 8 JDK y en [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Configuración de la aplicación para que use Azure SQL Database

Antes de ejecutar la aplicación de ejemplo, establezca la configuración de la aplicación en la aplicación web para usar la base de datos MySQL de Azure que creó en Azure. Estas propiedades se exponen a la aplicación web como variables de entorno y reemplazan los valores establecidos en el archivo application.properties dentro de la aplicación web empaquetada. 

En Cloud Shell, establezca la configuración de la aplicación con el parámetro [az webapp config appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) en la CLI:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Obtención de credenciales de implementación de FTP 
Puede implementar la aplicación en el servicio de aplicaciones Azure de diversas formas, incluidos un FTP, un repositorio de Git local, GitHub, Visual Studio Team Services y BitBucket. En este ejemplo, utilice un comando de FTP para implementar el archivo .WAR creado previamente en el equipo local a Azure App Service.

Para determinar qué credenciales pasar a la aplicación web en un comando de FTP, use el comando [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) como se indica en Cloud Shell: 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Carga de la aplicación mediante FTP

Utilice la herramienta FTP que prefiera para implementar el archivo .WAR en la carpeta */site/wwwroot/webapps* en la dirección del servidor procedente del campo `URL` en el comando anterior. Quite el directorio de aplicación predeterminado (raíz) existente y reemplace el archivo ROOT.war existente con el archivo .WAR creado anteriormente en el tutorial.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Prueba de la aplicación web

Vaya a `http://<app_name>.azurewebsites.net/` y agregue algunas tareas a la lista. 

![Aplicación Java que se ejecuta en Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**¡Enhorabuena!** Ya está ejecutando una aplicación Java orientada a datos en Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Actualización de la aplicación y nueva implementación

Actualice la aplicación para que incluya una columna adicional en la lista de tareas para el día en que se creó el elemento. Sprint Boot controla la actualización del esquema de base de datos automáticamente cuando cambia el modelo de datos sin modificar los registros de base de datos existente.

1. En el sistema local, abra *src/main/java/com/example/fabrikam/TodoItem.java* y agregue las siguientes importaciones a la clase:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Agregue una propiedad `String` de `timeCreated` a *src/main/java/com/example/fabrikam/TodoItem.java*, inicializándola con una marca de tiempo en la creación de objeto. Agregue los métodos captadores/establecedores a la nueva propiedad `timeCreated` mientras edita este archivo.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Actualice *src/main/java/com/example/fabrikam/TodoDemoController.java* con una línea en el método `updateTodo` para establecer la marca de tiempo:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Agregue compatibilidad al nuevo campo de la plantilla Thymeleaf. Actualice *src/main/resources/templates/index.html* con un nuevo encabezado de tabla para la marca de tiempo y un nuevo campo para mostrar el valor de la marca de tiempo en cada fila de datos de tabla.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Vuelva a generar la aplicación:

    ```bash
    mvnw clean package 
    ```

6. Utilice la FTP en el archivo .WAR actualizado como antes, quitando el directorio *site/wwwroot/webapps/ROOT* existente y archivo *ROOT.war*, y después cargando el archivo .WAR actualizado como ROOT.war. 

Al actualizar la aplicación, ahora es visible la columna **Hora de creación**. Cuando se agrega una nueva tarea, la aplicación rellenará automáticamente la marca de tiempo. Las tareas existentes permanecen sin cambios y trabajan con la aplicación aunque haya cambiado el modelo de datos subyacente. 

![Aplicación de Java actualizada con una nueva columna](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Transmisión de registros de diagnóstico 

Mientras se ejecuta la aplicación Java en Azure App Service, puede tener los registros de consola insertados directamente en el terminal. De este modo, puede obtener los mismos mensajes de diagnóstico para ayudarle a depurar errores de la aplicación.

Para iniciar la transmisión del registro, use el comando [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) en Cloud Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya al portal de Azure para ver la aplicación web que ha creado.

Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com).

En el menú izquierdo, haga clic en **App Service**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-java-mysql/access-portal.png)

De forma predeterminada, la hoja de la aplicación web muestra la página de **introducción**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración como detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la hoja muestran las diferentes páginas de configuración que puede abrir.

![Hoja de App Service en Azure Portal](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Estas pestañas de la hoja muestran las muchas y excepcionales características que puede agregar a la aplicación web. La lista siguiente proporciona solo algunas de las posibilidades:
* Asignación de un nombre DNS personalizado
* Enlace de un certificado SSL personalizado
* Configuración de la implementación continua
* Escalado vertical y horizontal
* Adición de la autenticación de usuarios

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no necesita estos recursos para otro tutorial (consulte [Pasos siguientes](#next)), ejecute el siguiente comando para eliminarlos en Cloud Shell: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>pasos siguientes

> [!div class="checklist"]
> * Crear una base de datos MySQL en Azure
> * Conexión de una aplicación Java de ejemplo a MySQL
> * Implementación de la aplicación en Azure
> * Actualización de la aplicación y nueva implementación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

Pase al siguiente tutorial para aprender cómo asignar un nombre DNS personalizado a la aplicación.

> [!div class="nextstepaction"] 
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)
