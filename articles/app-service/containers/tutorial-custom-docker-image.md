---
title: Uso de una imagen personalizada de Docker para Web App for Containers | Microsoft Docs
description: Uso de una imagen personalizada de Docker para Web App for Containers.
keywords: "azure app service, aplicación web, linux, docker, contenedor"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2580c2109ce33b1ce99aa491f7d0002edf060693
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Uso de una imagen personalizada de Docker para Web App for Containers

[Web App for Containers](app-service-linux-intro.md) proporciona imágenes integradas de Docker en Linux con compatibilidad para versiones específicas, como PHP 7.0 y Node.js 4.5. Web App for Containers usa la tecnología de contenedores de Docker para hospedar imágenes integradas e imágenes personalizadas como una plataforma como servicio. En este tutorial, aprenderá a crear una imagen de Docker personalizada y a implementarla en Web App for Containers. Este patrón es útil cuando las imágenes integradas no incluyen el lenguaje elegido o cuando la aplicación requiere una configuración específica que no se proporciona en las imágenes integradas.

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesita:

* [Git](https://git-scm.com/downloads)
* Una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) activa
* [Docker](https://docs.docker.com/get-started/#setup)
* Una [cuenta de Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Descarga del ejemplo

En una ventana de terminal, ejecute el siguiente comando para clonar el repositorio de la aplicación de ejemplo en el equipo local y, luego, cambie al directorio que contiene el código de ejemplo.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Compilación de la imagen desde el archivo de Docker

En el repositorio de Git, examine _Dockerfile_. Este archivo describe el entorno de Python que se requiere para ejecutar la aplicación. Además, la imagen configura un servidor [SSH](https://www.ssh.com/ssh/protocol/) para una comunicación segura entre el contenedor y el host.

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Para compilar la imagen de Docker, ejecute el comando `docker build` y proporcione un nombre, `mydockerimage`, y una etiqueta, `v1.0.0`. Reemplace `<docker-id>` por el identificador de su cuenta de Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

El comando genera una salida similar a la siguiente:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Ejecute el contenedor de Docker para comprobar que la compilación funciona. Emita el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) y transmítale el nombre y la etiqueta de la imagen. Asegúrese de especificar el puerto con el argumento `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Compruebe que la aplicación web y el contenedor funcionan correctamente, para lo que debe examinar `http://localhost:2222`.

![Prueba local de la aplicación web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>Inserción de una imagen de Docker en Docker Hub

Un registro es una aplicación que hospeda imágenes y proporciona servicios de imágenes y servicios de contenedores. Si desea compartir una imagen, debe insertarla en un registro. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> ¿Desea insertar una imagen en un registro de Docker privado? Consulte las instrucciones opcionales para la [inserción de una imagen de Docker en un registro privado](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub es un registro para imágenes de Docker que le permite hospedar sus propios repositorios, ya sean públicos o privados. Para insertar una imagen personalizada de Docker en el Docker Hub público, use el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) y proporcione un nombre completo y una etiqueta para la imagen. Un nombre completo y una etiqueta para la imagen se ven como en el ejemplo siguiente:

```
<docker-id>/image-name:tag
```

Si no inició sesión en Docker Hub, hágalo con el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) antes de intentar insertar una imagen.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Un mensaje de inicio de sesión correcto confirmará que inició sesión. Una vez que inicie sesión, puede insertar la imagen en Docker Hub con el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Examine la salida del comando para comprobar que la inserción se realizó correctamente.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Implementación de aplicación en Azure

Puede hospedar aplicaciones Linux nativas en la nube con Azure Web Apps. Para crear una instancia de Web App for Containers, debe ejecutar comandos de la CLI de Azure que crean un grupo, luego un plan de servicios y, finalmente, la aplicación web misma. 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Creación de un plan de App Service de Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Creación de una aplicación web

En Cloud Shell, cree una [aplicación web](app-service-linux-intro.md) en el plan de App Service `myAppServicePlan` con el comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). No olvide reemplazar `<app_name>` por un nombre de aplicación único y < docker-ID > por el identificador de Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

Cuando se haya creado la aplicación web, la CLI de Azure mostrará información similar a la del ejemplo siguiente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Configuración de las variables de entorno

La mayoría de las imágenes de Docker tienen variables de entorno que deben configurarse. Si usa una imagen de Docker existente que ha creado otra persona, esta puede usar un puerto que no sea el 80. Para indicar a Azure el puerto que usa la imagen se utiliza el valor de la aplicación `WEBSITES_PORT`. La página de GitHub que contiene el [ejemplo de Python de este tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) muestra que es preciso establecer `WEBSITES_PORT` en _8000_.

Para establecer la configuración de la aplicación, use el comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) en Cloud Shell. La configuración de la aplicación distingue mayúsculas de minúsculas y la separación se realiza mediante espacios.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> ¿Desea implementar un registro de Docker privado? Consulte las instrucciones opcionales para la [configuración de Web App para usar un contenedor de Docker de un registro privado](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Prueba de la aplicación web

Para comprobar que la aplicación web funciona desplácese hasta ella (`http://<app_name>azurewebsites.net`). 

![Prueba de la configuración del puerto de la aplicación web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Cambio de la aplicación web y nueva implementación

En el repositorio de Git local, abra app/templates/app/index.html. Busque el primer elemento HTML y cambie a él.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Una vez que modifique y guarde el archivo Python, debe recompilar e insertar la imagen de Docker nueva. Luego reinicie la aplicación web para que los cambios surtan efecto. Use los mismos comandos que usó anteriormente en el tutorial. Puede consultar [Compilación de la imagen desde el archivo de Docker](#build-the-image-from-the-docker-file) e [Inserción de una imagen de Docker en Docker Hub](#push-the-docker-image-to-docker-hub). Pruebe la aplicación web con las instrucciones que aparecen en [Prueba de la aplicación web](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Conexión a Web App for Containers mediante SSH

SSH habilita la comunicación segura entre un contenedor y un cliente. Para que una imagen de Docker personalizada admita SSH, debe compilarla en un Dockerfile. SSH se habilita en el mismo archivo de Docker. Las instrucciones de SSH ya se agregaron al dockerfile de ejemplo, por lo que puede seguir estas instrucciones con su propia imagen personalizada:

* Una instrucción [RUN](https://docs.docker.com/engine/reference/builder/#run) que llama a `apt-get` y luego establece la contraseña para la cuenta raíz en `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Esta configuración no permite realizar conexiones externas al contenedor. SSH solo está disponible en el sitio de Kudu/SCM. El sitio de Kudu/SCM se autentica con las credenciales de publicación.

* Una instrucción [COPY](https://docs.docker.com/engine/reference/builder/#copy) que indica al motor Docker que copie el archivo [sshd_config](http://man.openbsd.org/sshd_config) en el directorio */etc/ssh/*. El archivo de configuración debe basarse en [este archivo sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > El archivo *sshd_config* debe incluir los elementos siguientes: 
    > * `Ciphers` debe incluir al menos un elemento de esta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` debe incluir al menos un elemento de esta lista: `hmac-sha1,hmac-sha1-96`.

* Una instrucción [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) que expone el puerto 2222 en el contenedor. Aunque se conozca la contraseña raíz, no es posible acceder al puerto 2222 desde Internet. Se trata de un puerto interno al que solo pueden acceder los contenedores que se encuentren en el puente de una red privada virtual. Después de eso, los comandos copias los detalles de la configuración de SSH e inician el servicio `ssh`.

    ```docker
    EXPOSE 8000 2222
    ```

* No olvide [iniciar el servicio ssh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) mediante un script de shell en el directorio /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Apertura de una conexión SSH a un contenedor

Web App for Containers no permite realizar conexiones externas al contenedor. SSH solo está disponible a través del sitio de Kudu, al que se puede acceder en `https://<app_name>.scm.azurewebsites.net`.

Para conectarse, vaya a `https://<app_name>.scm.azurewebsites.net/webssh/host` e inicie sesión con su cuenta de Azure.

Luego se le redirigirá a una página que muestra una consola interactiva. 

Puede que desee comprobar que ciertas aplicaciones están en ejecución en el contenedor. Para inspeccionar el contenedor y comprobar los procesos en ejecución, emita el comando `top` en el símbolo del sistema.

```bash
top
```

El comando `top` expone todos los procesos en ejecución de un contenedor.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Felicidades. Ya configuró una imagen personalizada de Docker para una instancia de Web App for Containers.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Uso de una imagen privada de Docker Hub (opcional)

En [Crear una aplicación web](#create-a-web-app), especificó una imagen de Docker Hub en el comando `az webapp create`. Dicha imagen suficientemente buena como para ser una imagen pública. Para usar una imagen privada, debe configurar el identificador y la contraseña de su cuenta de Docker en su aplicación web de Azure.

En Cloud Shell, siga el comando `az webapp create` con [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Reemplace *\<app_name >* y también _< docker-id >_ y  _<password>_  con el Id. de Docker y la contraseña.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

El comando revela una salida similar a la cadena JSON siguiente, lo que muestra que el cambio de configuración se realizó correctamente:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Uso de una imagen de Docker desde cualquier registro privado (opcional)

En esta sección, aprenderá a utilizar una imagen de Docker desde un registro privado de Web App for Containers y usa Azure Container Registry como ejemplo. Los pasos para utilizar otros registros privados son similares. 

Azure Container Registry es un servicio administrado de Docker de Azure para hospedar imágenes privadas. Las implementaciones pueden ser de cualquier tipo, lo que incluye [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) y Web App for Containers. 

### <a name="create-an-azure-container-registry"></a>Creación de una instancia de Azure Container Registry

En Cloud Shell, use el comando [az acr create](/cli/azure/acr?view=azure-cli-latest#az_acr_create) para crear una instancia de Azure Container Registry. Pase el nombre, el grupo de recursos y `Basic` para la SKU. Las SKU disponibles son `Classic`, `Basic`, `Standard` y `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Crear un contenedor genera la salida siguiente:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Inicio de sesión en Azure Container Registry

Para insertar una imagen en el registro, debe suministrar credenciales para que el registro acepte la inserción. Para recuperar dichas credenciales, ese el comando [az acr show](/cli/azure/acr?view=azure-cli-latest#az_acr_show) en Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

El comando revela dos contraseñas que se pueden usar con el nombre de usuario.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

En la ventana del terminal local, inicie sesión en Azure Container Registry mediante el comando `docker login`. El nombre de servidor es obligatorio para iniciar sesión. Use el formato `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username> --password <password> 
```

Confirme que el inicio de sesión se realizó correctamente. 

### <a name="push-an-image-to-azure-container-registry"></a>Inserción de imágenes en Azure Container Registry

> [!NOTE]
> Si usa su propia imagen, etiquete la imagen como se indica a continuación:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Para insertar una imagen, use el comando `docker push`. Etiquete la imagen con el nombre del registro, seguido del nombre y la etiqueta de la imagen.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Enumere los repositorios ACR para comprobar que la inserción agregó correctamente un contenedor al registro. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Enumerar las imágenes en el registro confirma que `mydockerimage` está en el registro.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Configuración de Web APP para utilizar la imagen de Azure Container Registry (o cualquier registro privado)

Web App for Containers se puede configurar para que ejecute un contenedor almacenado en Azure Container Registry. El uso de Azure Container Registry no se diferencia en nada del uso de cualquier registro privado, por lo que si necesita usar su registro privado, los pasos para completar la tarea serán similares.

En Cloud Shell, ejecute [az acr credential show](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show) para mostrar el nombre de usuario y la contraseña de Azure Container Registry. Copie el nombre de usuario y una de las contraseñas para que pueda usarlo para configurar la aplicación web en el paso siguiente.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

En Cloud Shell, ejecute el comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) comando para asignar la imagen de Docker personalizada a la aplicación web. Reemplace *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ y _\<password>_. En el caso de Azure Container Registry,  *\<docker-registry-server-url >* tiene el formato `https://<azure-container-registry-name>.azurecr.io`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://` se requiere en *\<docker-registry-server-url>*.
>

El comando revela una salida similar a la cadena JSON siguiente, lo que muestra que el cambio de configuración se realizó correctamente:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Compilación de una aplicación web Docker Python con PostgreSQL en Azure](tutorial-docker-python-postgresql-app.md)
