---
title: CI/CD con Azure Container Service y DC/OS | Microsoft Docs
description: "Cómo automatizar completamente la creación e implementación de una aplicación de Docker de varios contenedores en un clúster de Azure Container Service que ejecuta DC/OS."
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: acfba48585b960a1ce39b77e35d292189aa9732b
ms.lasthandoff: 04/03/2017


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>Integración e implementación continuas de aplicaciones de Docker de varios contenedores en Azure Container Service 
En este tutorial, se explica cómo automatizar completamente la creación e implementación de una aplicación de Docker de varios contenedores en un clúster de Azure Container Service que ejecuta DC/OS. Aunque son conocidas las ventajas de la integración e implementación continuas (CI/CD), existen nuevas consideraciones que hay que tener en cuenta al integrar contenedores en el flujo de trabajo. Con los nuevos comandos de CLI y Azure Container Registry, configuramos un flujo de principio a fin, que puede personalizar.

## <a name="get-started"></a>Primeros pasos
Puede realizar este tutorial en OS X, Windows o Linux.
- Necesita una suscripción de Azure. Si no tiene ninguna, puede [registrarse para obtener una cuenta](https://azure.microsoft.com/).
- Instale la [CLI de Azure 2.0](/cli/azure/install-az-cli2).

## <a name="what-well-create"></a>Qué vamos a crear
Vamos a ver algunos aspectos claves de la aplicación y el flujo de implementación que estamos configurando:
* **La aplicación se compone de varios servicios**. Recursos de Docker, Dockerfile y docker-compose.yml, para definir los servicios en nuestra aplicación; cada uno se ejecuta en contenedores diferentes. Estas instrucciones permiten escalar partes de la aplicación de forma independiente; además, cada servicio puede escribirse en un lenguaje de programación y una plataforma diferentes. El código de la aplicación se puede hospedar en uno o varios repositorios de código fuente de Git (actualmente, las herramientas admiten GitHub o Visual Studio Team Services).

* La aplicación se ejecuta en un **clúster de ACS configurado con DC/OS**. El orquestador del contenedor puede administrar el estado de nuestro clúster y asegurarse de que el número necesario de instancias de contenedor continúa ejecutándose. 

* El proceso de **creación e implementación de imágenes del contenedor se automatiza totalmente sin ningún tiempo de inactividad**. Queremos que los desarrolladores del equipo ejecuten "git push" en una rama, lo que desencadena automáticamente un proceso de integración. Es decir, crear y etiquetar las imágenes del contenedor, ejecutar pruebas en cada contenedor e insertar esas imágenes en un registro privado de Docker. A partir de ahí, las nuevas imágenes se implementan automáticamente en un entorno de preproducción compartido en un clúster de ACS para otras pruebas.

* **Promover una versión de un entorno al siguiente**; por ejemplo, desarrollo -> prueba -> ensayo -> producción. Cuando se promueve a un entorno posterior, no es necesario volver a generar nuestras imágenes del contenedor para asegurarse de implementar las mismas imágenes probadas en un entorno anterior. Este proceso es el concepto de *servicios inmutables*, que reduce la probabilidad de que errores sin detectar pasen a producción.

* Para utilizar de forma más eficaz los recursos de proceso en el clúster de ACS, usamos el mismo clúster para realizar los pasos de creación e implementación de tareas totalmente incluidas en contenedores. El clúster también hospeda nuestros diversos entornos de desarrollo, prueba y producción.


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>Creación de un clúster de Azure Container Service configurado con DC/OS

>[!IMPORTANT]
> Para crear un clúster seguro, se pasa el archivo de claves públicas de SSH al llamar a `az acs create`. Puede hacer que la versión 2.0 de la CLI de Azure genere las claves automáticamente y las pase al mismo tiempo mediante la opción `--generate-ssh-keys`, o bien, puede pasar la ruta de acceso a las claves mediante la opción `--ssh-key-value` (la ubicación predeterminada en Linux es `~/.ssh/id_rsa.pub` y en Windows `%HOMEPATH%\.ssh\id_rsa.pub`, pero se puede cambiar).
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> Para crear archivos de claves públicas y privadas de SSH en Linux, consulte el artículo sobre la [creación de claves SSH en Linux y Mac](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 
> Para crear archivos de claves públicas y privadas de SSH en Windows, consulte el artículo sobre la [creación de claves SSH en Windows](../virtual-machines/linux/ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 

1. En primer lugar, escriba el comando [az login](/cli/azure/#login) en una ventana de terminal para iniciar sesión en su suscripción de Azure con la CLI de Azure: 

    `az login`

1. Cree un grupo de recursos en el que se colocará el clúster mediante [az group create](/cli/azure/group#create):
    
    `az group create --name myacs-rg --location westus`

    Puede que desee especificar la [región del centro de datos de Azure](https://azure.microsoft.com/regions) más cercana. 

1. Cree un clúster de ACS con la configuración predeterminada mediante [az acs create](/cli/azure/acs#create) y pase la ruta de acceso al archivo de claves públicas de SSH: 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
Este paso tarda varios minutos, así que puede seguir leyendo.  El comando `acs create` devuelve información sobre el clúster recién creado (o puede enumerar los clústeres de ACS en su suscripción con `az acs list`). Para ver más opciones de configuración de ACS, [lea más información sobre la creación y configuración de un clúster de ACS](container-service-deployment.md).

## <a name="set-up-sample-code"></a>Configuración del código de ejemplo
Mientras se crea el clúster, podemos configurar el código de ejemplo que se implementa en ACS.

1. [Bifurque](https://help.github.com/articles/fork-a-repo/) el repositorio de GitHub de ejemplo para tener su propia copia: [https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git). La aplicación es básicamente una versión de varios contenedores de "hello world".
1. Una vez creada una bifurcación en su propia cuenta de GitHub, clone localmente el repositorio en el equipo:

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
Veamos el código más detalladamente:
* `/service-a` es una aplicación web basada en Angular.js con un back-end de Node.js.
* `/service-b` es un servicio de .NET Core y `service-a` lo llama a través de REST.
* `service-a` y `service-b` contienen un `Dockerfile` en cada uno de sus directorios que describen respectivamente imágenes de contenedores basados en Node.js- y .NET Core. 
* `docker-compose.yml` declara el conjunto de servicios que se crea e implementa.
* Además de `service-a` y `service-b`, un tercer servicio, denominado `cache`, ejecuta una caché de Redis que `service-a` puede utilizar. `cache` difiere de los dos primeros servicios en que no tenemos código para él en nuestro repositorio de código fuente. En lugar de ello, obtenemos una imagen `redis:alpine` elaborada previamente de Docker Hub y la implementamos en ACS.
* `/service-a/server.js` contiene código donde `service-a` llama a `service-b` y `cache`. Observe que el código `service-a` hace referencia a `service-b` y `cache` de acuerdo con su denominación en `docker-compose.yml`. Si ejecutamos estos servicios en nuestra máquina local a través de `docker-compose`, Docker garantiza que todos los servicios estén conectados en red adecuadamente para poder buscarse entre ellos por su nombre. Ejecutar los servicios en un entorno de clúster con una conexión de red con carga equilibrada normalmente resulta mucho más complejo que ejecutarlos localmente. La buena noticia es que los comandos de CLI de Azure configuran un flujo de CI/CD que garantiza que el código de detección del servicio directo continúa ejecutándose tal cual en ACS. 

    ![Introducción a las aplicaciones de ejemplo con varios contenedores](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>Configuración de la integración e implementación continuas
1. Asegúrese de que el clúster de ACS está listo: ejecute [az acs list](/cli/azure/acs#list) y confirme que el clúster de ACS aparece. (Nota: ACS debe estar ejecutando DC/OS 1.8 o superior).

1. [Cree un token de acceso personal a GitHub](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) y concédale al menos el ámbito `repo`.  No olvide copiar el token en el Portapapeles, ya que lo vamos a usar en el siguiente comando (configurará un [webhook](https://help.github.com/articles/about-webhooks/) en nuestro repositorio de GitHub). 

1. Establezca el directorio actual en la raíz del repositorio de código fuente clonado y cree una canalización de compilación y versión, mediante el elemento _&lt;GitHubPersonalAccessToken&gt; que acaba de crear:
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    Donde `--target-name` es el nombre de su clúster de ACS y `--target-resource-group` es el nombre del grupo de recursos del clúster de ACS.

En la primera ejecución, este comando puede tardar un minuto o más en completarse. Una vez completado, se devuelve información importante en relación a la canalización de compilación y versión que creó:
* `sourceRepo`: se configura un elemento [webhook](https://help.github.com/articles/about-webhooks/) para el repositorio del código fuente para que la canalización de compilación y versión se desencadena automáticamente siempre que se inserte el código fuente.  
* `vstsProject`: [Visual Studio Team Services](https://www.visualstudio.com/team-services/) (VSTS) está configurado para *controlar* el flujo de trabajo (las tareas de compilación e implementación reales se ejecutan dentro de contenedores en ACS). Si desea usar una cuenta de VSTS y un proyecto específicos, se puede definir mediante los parámetros `--vsts-account-name` y `--vsts-project-name`.
* `buildDefinition`: define las tareas que se ejecuta para cada compilación. Las imágenes del contenedor se generan para cada servicio definido en docker-compose.yml y, a continuación, se insertan en un registro de contenedores de Docker. 
* `containerRegistry`: Azure Container Registry es un servicio administrado que ejecuta un registro de contenedores de Docker. Se crea una instancia de Azure Container Registry con un nombre predeterminad; si lo prefiere, puede especificar un nombre de Azure Container Registry mediante el parámetro `--registry-name`.
* `releaseDefinition`: define las tareas que se ejecutan para cada implementación. Las imágenes del contenedor para los servicios definidos en docker compose.yml se extraen del registro de contenedores y se implementan en el clúster de ACS. De forma predeterminada, se crean tres entornos: *Dev*, *prueba* y *producción*. La definición de la versión se configura de forma predeterminada para implementarse automáticamente en *Dev* cada vez que una compilación se complete correctamente. Una versión se puede promover a *prueba* o *producción* manualmente sin necesidad de volver a compilarla. El flujo predeterminado puede personalizarse en VSTS. 
* `containerService`: el clúster ACS de destino (debe estar ejecutando DC/OS 1.8).


El fragmento de código siguiente es un comando de ejemplo que se escribe si ya tiene una instancia de Azure Container Registry denominada `myregistry`. Cree y genere definiciones de versión con una cuenta de VSTS en `myvstsaccount.visualstudio.com` y un proyecto de VSTS `myvstsproject` existente:
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>Visualización del progreso de la canalización de implementación
Una vez creada la canalización, se inician automáticamente una implementación y una compilación que tienen lugar la primera vez. Las compilaciones posteriores se activan cada vez que se inserta código en el repositorio del código fuente. Puede comprobar el progreso de una compilación o de una versión; para ello, abra el explorador con la URL de la definición de la compilación o de la versión.

Para encontrar la dirección URL de la definición de la versión asociada a un clúster de ACS, ejecute este comando:

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![Compilación de VSTS](media/container-service-setup-ci-cd/vsts-build.PNG)

*Captura de pantalla de VSTS que muestra los resultados de los elementos de configuración de nuestra aplicación con varios contenedores*

![Versión de VSTS](media/container-service-setup-ci-cd/vsts-release.PNG)

*Versión docker-compose de VSTS con varios entornos*

## <a name="view-the-application"></a>Visualización de la aplicación
En este momento, nuestra aplicación se implementa en nuestro entorno de desarrollo compartido y no se expone públicamente. Mientras tanto, use el panel de DC/OS para ver y administrar nuestros servicios y [crear un túnel de SSH a los puntos de conexión relacionados con DC/OS](https://azure.microsoft.com/documentation/articles/container-service-connect/) o ejecutar un comando cómodo proporcionado por la CLI de Azure.

> [!IMPORTANT]
> En una implementación realizada por primera vez, confirme que la versión de VSTS se ha implementado correctamente antes de continuar.

> [!NOTE]
> Solo Windows: es necesario configurar [Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para completar esta sección.
> 
>* Inicie *PuttyGen* y cargue la clave privada de SSH utilizada para crear el clúster de ACS (%homepath%\id_rsa).
>* Guarde la clave privada de SSH como `id_rsa.ppk` en la misma carpeta.
>* Iniciar *Pageant*; empezará a ejecutarse y mostrará un icono en la bandeja del sistema, situada en la parte inferior derecha.
>* Haga clic con el botón derecho en el icono de la bandeja del sistema y seleccione *Agregar clave*.
>* Agregue el archivo `id_rsa.ppk`.
> 
> 

1. Abra el panel de DC/OS del clúster de ACS mediante el comando de CLI de Azure:
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g` es el nombre del grupo de recursos del clúster de ACS de destino.
    * `-n` es el nombre del clúster de ACS de destino.
    * Se le puede solicitar la contraseña de cuenta local, ya que este comando requiere privilegios de administrador. El comando crea un túnel de SSH a un punto de conexión de DC/OS, abre el explorador predeterminado en ese punto de conexión y configura temporalmente el proxy web del explorador. 

    > [!TIP]
    > Si necesita buscar el nombre de su clúster de ACS, puede enumerar todos los clústeres de ACS en su suscripción mediante la ejecución de `az acs list`. 

1. En el panel de DC/OS, haga clic en **Servicios** en el menú de navegación izquierdo ([http://localhost/#/services](http://localhost/#/services)). Los servicios implementados a través de la canalización se agrupan en una carpeta raíz denominada *dev* (con el nombre del entorno en la definición de la versión VSTS). 

![Interfaz de usuario de Marathon](media/container-service-setup-ci-cd/marathon-ui.png)

Puede realizar muchas operaciones útiles en el panel de DC/OS.

* Seguimiento del estado de implementación de cada servicio
* Visualización de los requisitos de CPU y memoria
* Visualización de los registros
* Escalado del número de instancias para cada servicio

**Para ver la aplicación web para service-a**: acceda a la carpeta raíz *dev* y desplácese por la jerarquía de carpetas hasta llegar a `service-a`. Esta vista muestra las tareas (o instancias del contenedor) que están en ejecución para `service-a`.

![service a](media/container-service-setup-ci-cd/service-a.png)

Haga clic en una tarea para abrir su vista y haga clic en uno de sus puntos de conexión disponibles.

![Tarea de service a](media/container-service-setup-ci-cd/service-a-task.PNG)

Nuestra aplicación web simple llama a `service-a`, que llama a `service-b`, y devuelve un mensaje hello world. Se incrementa un contador en Redis cada vez que se realiza una solicitud.

![aplicación web de service a](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>(Opcional) Acceso a un servicio desde la línea de comandos
Si desea acceder a un servicio mediante curl desde la línea de comandos:

1. Ejecute `az acs dcos browse --verbose -g myacs-rg -n myacs` y, después de escribir la contraseña, anote la línea que indica que el Proxy se ejecuta en <dirección-ip><port>.
1. En una nueva ventana de terminal, escriba:

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    Por ejemplo: `export http_proxy=http://127.0.0.1:55405`

1. Ahora puede ejecutar curl en su punto de conexión de servicio, `curl http://service-url`, donde `service-url` es la dirección que ve cuando se desplaza a su punto de conexión de servicio desde la interfaz de usuario de Marathon. Al anular la variable http_proxy desde la línea de comandos, escriba `unset http_proxy`.
 

## <a name="scale-services"></a>Escalado de servicios
En el panel de DC/OS, vamos a escalar nuestros servicios.
1. Desplácese a la aplicación en la subcarpeta *dev*.
1. Mantenga el puntero sobre `service-b`, haga clic en el icono de engranaje y seleccione **Scale** (Escalar).

    ![Menú Acción](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. Aumente el número a 3 y haga clic en **Scale Service** (Servicio de escala).

    ![Escalado de servicios](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. Vuelva a la aplicación web en ejecución y haga clic repetidamente en el botón *Say It Again* (Repítalo). Tenga en cuenta que las invocaciones a `service-b` empiezan a usar el mecanismo round robin a través de una colección de nombres de host, mientras la instancia única de `service-a` sigue indicando el mismo host.   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>Promoción de una versión a entornos posterior sin volver a generar las imágenes del contenedor
Nuestra canalización de la versión VSTS configura tres entornos de forma predeterminada: *Dev*, *prueba* y *producción*. Hasta ahora hemos implementado la aplicación en *Dev*. Echemos un vistazo a cómo se puede promover una versión al siguiente entorno posterior, *Prueba*, sin volver a generar nuestras imágenes del contenedor. Este flujo de trabajo garantiza que estemos implementando las mismas imágenes que hemos probado en el entorno anterior y es el concepto de *servicios inmutables*, que reduce la probabilidad de que errores sin detectar pasen a producción.

1. En la interfaz de usuario web de VSTS, vaya a **Releases** (Versiones).

    ![Menú Releases (Versiones) de VSTS](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. Abra la versión más reciente.

1. En la barra de menús de la definición de la versión, haga clic en **Deploy** (Implementar), seleccione **Test** (Prueba) como el siguiente entorno en el que desea realizar la nueva implementación, volviendo a usar las mismas imágenes que previamente se implementaron en *Dev*. Haga clic en **Logs** (Registros) si desea seguir la implementación con más detalle.

    ![Promoción de la versión en VSTS](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

Una vez realizada correctamente la implementación en *Test* (Prueba), aparece una nueva carpeta de raíz en la interfaz de usuario de Marathon denominada *test* que contiene los servicios en ejecución para ese entorno. 

![Subcarpetas para cada entorno de DC/OS](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>Desencadenamiento de una nueva compilación e implementación
Vamos a simular lo que sucedería si un desarrollador de nuestro equipo inserta un cambio en el código en el repositorio del código fuente.

1. De vuelta en el editor de código, abra `service-a/public/index.html`. 
1. Modifique esta línea de código:

    `<h2>Server Says</h2>`

    a algo parecido a:

    `<h2>Server Says Hello</h2>`

1. Guarde el archivo, confírmelo e inserte el cambio en el código en el repositorio del código fuente.

    ```bash
    git commit -am 'updated title'
    git push
    ```

La confirmación inicia automáticamente una nueva compilación y una nueva versión para su implementación en *Dev*. Los servicios en entornos posteriores (*Prueba* o *Producción*) permanecen sin cambios hasta que decidamos promover la aplicación a una versión específica para ese entorno.

Si abre la definición de compilación en VSTS, verá algo parecido a esto: 

![Nueva compilación activada mediante git push](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>Exposición de un punto de conexión público para la producción

1. Agregue el siguiente código de yaml a un nuevo archivo denominado `docker-compose.env.production.yml` en la carpeta raíz del repositorio de código fuente. Esto agrega una etiqueta que hace que un punto de conexión público se exponga para `service-a`. 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * Como valor de la etiqueta, puede especificar la dirección URL del nombre de dominio completo de su agente de ACS o un dominio personalizado (por ejemplo, app.contoso.com). Para averiguar el nombre de dominio completo del agente de ACS, ejecute el comando `az acs list` y compruebe la propiedad para `agentPoolProfiles.fqdn`. Por ejemplo: `myacsagents.westus.cloudapp.azure.com`.
    * La aplicación de ejemplo escucha de forma predeterminada en el puerto 80; para los usuarios que tengan sus aplicaciones de Docker escuchando en otros puertos, por ejemplo, `port 8080` o `443`, conecte el número de puerto al FQDN. Por ejemplo: `myacsagents.westus.cloudapp.azure.com:8080`. Sin embargo, cuando trata de acceder a la aplicación desde fuera, debe consultar en el puerto 80.
    * Si sigue la convención de nombre de archivo docker-compose.env.*environment-name*.yml, esta configuración solo afecta al entorno con nombre (en este caso, el entorno con el nombre *Production* [Producción]). Inspeccione la definición de la versión en VSTS, la tarea de implementación de cada entorno está configurada para leer de un archivo docker-compose cuyo nombre sigue esta convención.

1. Confirme e inserte el archivo en el repositorio del código fuente maestro para iniciar otra compilación.

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. Espere hasta que se haya generado e implementado la actualización en *Dev* y promúevala a *Prueba*; a continuación, promúevala a *Producción*. (Para los fines de este tutorial, puede realizar la implementación directamente en *Producción*, pero es conveniente acostumbrarse a realizar la implementación solo en el siguiente entorno posterior).

1. (Opcional) **Si ha especificado un dominio personalizado** para vhost (por ejemplo, app.contoso.com), agregue un registro DNS en la configuración del proveedor del dominio. Inicie sesión en la interfaz de usuario administrativa del proveedor de su dominio y agregue un registro DNS como sigue:

    * Tipo: CNAME
    * Host: el dominio personalizado, por ejemplo, app.contoso.com
    * Respuesta: nombre de dominio completo del agente de ACS; por myacsagents.westus.cloudapp.azure.com
    * TTL (opcional): a veces, el proveedor de dominio ofrece la posibilidad de editar el TTL. Un valor inferior hace que una actualización del registro de DNS se propague más rápidamente.   

1. Una vez que se ha implementado la versión en *Producción*, esa versión también estará accesible para todos los usuarios. Abra el explorador en la dirección URL especificada para la etiqueta `com.microsoft.acs.dcos.marathon.vhost`. (Nota: las versiones para los entornos de preproducción siguen siendo privadas).

## <a name="summary"></a>Resumen
¡Enhorabuena! Ha aprendido a crear un clúster de ACS con DC/OS y configurar una canalización de compilación e implementación con contenedores totalmente automatizada y en contenedores para una aplicación con varios contenedores.

Algunos de los siguientes pasos:
* **Escalado de agentes de VSTS.** Si necesita más rendimiento para ejecutar las tareas de compilación y versión, puede aumentar el número de instancias del agente de VSTS. Vaya a **Services** (Servicios) en el panel de DC/OS, abra la carpeta vsts-agents y realice experimentos de escalado del número de instancias del agente de VSTS.
* **Integración de las pruebas de unidades.** Este repositorio de GitHub muestra cómo realizar pruebas de unidades y de integración ejecutadas en contenedores e incluirlas en las tareas de compilación: [https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app). 
    * Sugerencia: busque estos archivos en el repositorio: `service-a/unit-tests.js`, `service-a/service-tests.js`, `docker-compose.ci.unit-tests.yml` y `docker-compose.ci.service-tests.yml`.

## <a name="clean-up"></a>Limpieza
Para limitar los cargos de proceso relacionado con este tutorial, ejecute el comando siguiente y anote los recursos de canalización de la implementación que están relacionados con un clúster de ACS:

```azurecli    
az container release list --resource-name myacs --resource-group myacs-rg
```

Elimine el clúster de ACS:
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque el grupo de recursos que contiene el clúster de ACS.
1. Abra la interfaz de usuario de la hoja del grupo de recursos y haga clic en **Eliminar** en la barra de comandos de la hoja.

Elimine Azure Container Registry: en Azure Portal, busque la instancia de Azure Container Registry y elimínela. 

La [cuenta de Visual Studio Team Services ofrece un nivel de acceso básico gratuito para los cinco primeros usuarios](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/), pero se pueden eliminar las definiciones de compilación y versión.

Elimine la definición de la compilación de VSTS:
        
1. Abra la dirección URL de la definición de compilación en el explorador y haga clic en el vínculo **Definiciones de compilación** (junto al nombre de la definición de compilación que está viendo actualmente).
2. Haga clic en el menú de acción al lado de la definición de compilación que desea eliminar y seleccione **Eliminar definición**.

`![Eliminación de la definición de compilación en VSTS](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

Elimine la definición de la versión en VSTS:

1. Abra la dirección URL de la definición de la versión en el explorador.
2. En la lista Definición de versiones, situada en la parte izquierda, haga clic en la lista desplegable situada junto a la definición de la versión que desea eliminar y seleccione **Eliminar**.

`![Eliminación de la definición de la versión en VSTS](media/container-service-setup-ci-cd/vsts-delete-release-def.png)

