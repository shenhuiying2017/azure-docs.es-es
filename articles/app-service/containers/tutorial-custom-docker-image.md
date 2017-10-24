---
title: Uso de una imagen personalizada de Docker para Azure Web App for Containers | Microsoft Docs
description: "Cómo usar una imagen personalizada de Docker para Azure Web App for Containers."
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
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 760772d1d1c79dd4a1114c36971de0b3693ab74f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>Uso de una imagen personalizada de Docker para Azure Web App for Containers

[Web App for Containers](app-service-linux-intro.md) proporciona imágenes integradas de Docker en Linux con compatibilidad para versiones específicas, como PHP 7.0 y Node.js 4.5. Web App for Containers aprovecha la tecnología de contenedores de Docker para hospedar imágenes integradas e imágenes personalizadas como una plataforma como servicio. En este tutorial aprenderá a crear una imagen personalizada de Docker para usarla en Web App for Containers, que se trata de un patrón común si no hay una imagen integrada para su lenguaje o si la aplicación requiere una configuración específica que no se proporcione dentro de las imágenes integradas.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

* [Git](https://git-scm.com/downloads)
* Una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/) activa
* [Docker](https://docs.docker.com/get-started/#setup)
* Una [cuenta de Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Descarga del ejemplo

En una ventana de terminal, ejecute el siguiente comando para clonar el repositorio de la aplicación de ejemplo en el equipo local y, luego, cambie al directorio que contiene el código de ejemplo.

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>Compilación de la imagen desde el archivo de Docker

En el archivo de Docker siguiente se describe el entorno Python que se requiere para ejecutar la aplicación. Además, la imagen configura un servidor [SSH](https://www.ssh.com/ssh/protocol/) para una comunicación segura entre el contenedor y el host.

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Para compilar la imagen de Docker, ejecute el comando `docker build` y proporcione un nombre, `mydockerimage`, y una etiqueta, `v1.0.0`. Reemplace `<docker-id>` por el identificador de su cuenta de Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

El comando genera una salida similar a la siguiente:

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

Ejecute el contenedor de Docker para comprobar que la compilación funciona. Emita el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) y transmítale el nombre y la etiqueta de la imagen. También debe especificar el puerto con el argumento `-p`.

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

Examine de manera local la aplicación web para comprobar que la aplicación web y el contenedor funcionan correctamente.

![Prueba local de la aplicación web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>Inserción de una imagen de Docker en Docker Hub

Un registro es una aplicación que hospeda imágenes y proporciona servicios de imágenes y servicios de contenedores. Si desea compartir una imagen, debe insertarla en un registro. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> ¿Desea insertar una imagen en un registro de Docker privado? Consulte las instrucciones opcionales para la [inserción de una imagen de Docker en un registro privado](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub es un registro para imágenes de Docker que le permite hospedar sus propios repositorios, ya sean públicos o privados. Para insertar una imagen personalizada de Docker en el Docker Hub público, use el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) y proporcione un nombre completo y una etiqueta para la imagen. Un nombre completo y una etiqueta para la imagen se ven como en el ejemplo siguiente:

```bash
<docker-id>/image-name:tag
```

Si no inició sesión en Docker Hub, hágalo con el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) antes de intentar insertar una imagen.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Un mensaje de inicio de sesión correcto confirmará que inició sesión. Una vez que inicie sesión, puede insertar la imagen en Docker Hub con el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Examine la salida del comando para comprobar que la inserción se realizó correctamente.

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>Creación de Web App for Containers

Puede hospedar aplicaciones Linux nativas en la nube con Azure Web Apps. Para crear una instancia de Web App for Containers, debe ejecutar comandos de la CLI de Azure que crean un grupo, luego un plan de servicios y, finalmente, la aplicación web misma. En primer lugar, ejecute el comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) y pase una ubicación y un nombre único.

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

Verá una salida similar al ejemplo siguiente:

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Use el nombre del grupo para crear un plan de App Service con el comando [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create). También debe asignarle un nombre único y establecer la marca `--is-linux`.

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

La creación de un plan de servicio genera resultados similares al ejemplo siguiente:

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

Ahora que creó el grupo de recursos y el plan de servicio, puede ejecutar el comando [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) para crear la aplicación web. Tenga en cuenta que la pila de tiempo de ejecución es Python 3.4 y la aplicación web usa la configuración del grupo de recursos y el plan de servicio de los pasos anteriores.

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

El comando para crear una aplicación web genera esta salida:

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

En la mayoría de las aplicaciones web se debe configurar la aplicación. Si usa una imagen de Docker existente creada por otra persona, la imagen puede requerir un puerto distinto del puerto 80 para la aplicación. Para establecer `WEBSITES_PORT`, ejecute el comando [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings) tal como se muestra en el ejemplo de código siguiente:

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> La configuración de la aplicación distingue mayúsculas de minúsculas.
>

Examine la aplicación web para comprobar que funciona. No olvide el número de puerto.

![Prueba de la configuración del puerto de la aplicación web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>Configuración de Web App para usar el contenedor de Docker de Docker Hub

El comando [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) permite usar una imagen personalizada de Docker.

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> ¿Desea implementar un registro de Docker privado? Consulte las instrucciones opcionales para la [configuración de Web App para usar un contenedor de Docker de un registro privado](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

Para configurar la aplicación web para usar un registro público de Docker, pase el nombre de la aplicación, el grupo de recursos y la dirección URL y el nombre de la imagen en el comando [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

Un cambio de configuración correcto proporciona información general sobre el contenedor.

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>Prueba de la aplicación en Azure

Antes de hacer la prueba, debe reiniciar la aplicación web con [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) para que surtan efecto los cambios de configuración.

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

El comando de restauración reinicia silenciosamente la aplicación web, por lo que no se ve ningún comentario en el terminal. Una vez que se ejecuta la aplicación web, examine su dirección URL en `http://<username>.azurewebsites.net` para probarla. Compruebe que la aplicación muestra el mensaje de bienvenida nuevo.

![Prueba de la aplicación web en Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Cambio de la aplicación web y nueva implementación

Hay un archivo Python llamado `app.py` en el directorio `using-custom-docker-image`. El archivo contiene una línea de código que muestra un mensaje `Hello World!`. Cambie la línea para que se muestre el mensaje `Hello World of Web Apps running in Docker Containers!`.

```python
return "Hello World of Web Apps running in Docker Containers!"
```

Una vez que modifique y guarde el archivo Python, debe recompilar e insertar la imagen de Docker nueva. Luego reinicie la aplicación web para que los cambios surtan efecto. Use los mismos comandos que usó anteriormente en el tutorial. Puede consultas las secciones sobre la [compilación de la imagen a partir del archivo de Docker](#build-the-image-from-the-docker-file) y la [inserción de la imagen de Docker](#push-docker-image). Pruebe la aplicación web con las instrucciones que aparecen en [Prueba de la aplicación en Azure](#tTest-the-application-in-azure)

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Conexión a Web App for Containers mediante SSH

SSH habilita la comunicación segura entre un contenedor y un cliente. Para que una imagen de Docker personalizada admita SSH, debe compilarla en un Dockerfile. SSH se habilita en el mismo archivo de Docker. Las instrucciones de SSH ya se agregaron al dockerfile de ejemplo, por lo que puede seguir estas instrucciones con su propia imagen personalizada:

* Una instrucción [RUN](https://docs.docker.com/engine/reference/builder/#run) que llama a `apt-get` y luego establece la contraseña para la cuenta raíz en `"Docker!"`.

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > Esta configuración no permite realizar conexiones externas al contenedor. SSH solo está disponible en el sitio de Kudu/SCM. El sitio de Kudu/SCM se autentica con las credenciales de publicación.

* Una instrucción [COPY](https://docs.docker.com/engine/reference/builder/#copy) que indica al motor Docker que copie el archivo [sshd_config](http://man.openbsd.org/sshd_config) en el directorio */etc/ssh/*. Su archivo de configuración se debe basar en el [archivo sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config) del repositorio Azure-App-Service de GitHub.

    > [!NOTE]
    > El archivo *sshd_config* debe incluir los elementos siguientes: 
    > * `Ciphers` debe incluir al menos un elemento de esta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` debe incluir al menos un elemento de esta lista: `hmac-sha1,hmac-sha1-96`.

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* Una instrucción [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) que expone el puerto 2222 en el contenedor. Aunque se conozca la contraseña raíz, no es posible acceder al puerto 2222 desde Internet. Se trata de un puerto interno al que solo pueden acceder los contenedores que se encuentren en el puente de una red privada virtual. Después de eso, los comandos copias los detalles de la configuración de SSH e inician el servicio `ssh`.

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

El archivo `init_container.sh` que se encuentra en el código de ejemplo contiene instrucciones sobre cómo inicializar el contenedor cuando se ejecuta. El Dockerfile usa las instrucciones [COPY](https://docs.docker.com/engine/reference/builder/#copy), [RUN](https://docs.docker.com/engine/reference/builder/#run) y [CMD](https://docs.docker.com/engine/reference/builder/#cmd) para iniciar correctamente el script `init_container.sh`.

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Puede que desee comprobar que ciertas aplicaciones están en ejecución en el contenedor. Para inspeccionar el contenedor y comprobar los procesos en ejecución, comience abriendo un explorador y navegue a `https://<app name>.scm.azurewebsites.net/webssh/host`. Luego se le redirigirá a una página que muestra una consola interactiva. Emita el comando `top` en el símbolo del sistema.

```bash
top
```

El comando `top` expone todos los procesos en ejecución de un contenedor.

```bash
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

¡Enhorabuena! Ya configuró una imagen personalizada de Docker para una instancia de Azure Web App for Containers.

## <a name="push-a-docker-image-to-private-registry-optional"></a>Inserción de una imagen de Docker en un registro privado (opcional)

Azure Container Registry es un servicio administrado de Docker de Azure para hospedar imágenes privadas. Pueden ser implementaciones de cualquier tipo, incluidos contenedores de servicios de aplicación Azure, [Docker Swarm](https://docs.docker.com/engine/swarm/) y [Kubernetes](https://kubernetes.io/). Usar Azure Container Registry es igual que usa cualquier registro privado, por lo tanto, si necesita usar su registro privado, los pasos para completar la tarea serán similares.

Use el comando [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) para crear una instancia de Azure Container Registry. Pase el nombre, el grupo de recursos y `Basic` para la SKU. Las SKU disponibles son `Classic`, `Basic`, `Standard` y `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Crear un contenedor genera la salida siguiente:

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
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

Para insertar una imagen en el registro, debe suministrar credenciales para que el registro acepte la inserción. Use el comando [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) para recuperar estas credenciales. 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
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
  "username": "<azure-container-registry-name>"
}
```

Ahora que tiene las credenciales necesarias, inicie sesión en Azure Container Registry con el comando `docker login`. El nombre de servidor es obligatorio para iniciar sesión. Use el formato `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

Confirme que el inicio de sesión se realizó correctamente. Inserte la imagen con el comando `docker push` y etiquete la imagen con el nombre del registro seguido del nombre y la etiqueta de la imagen.

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

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>Configuración de Web App para usar un contenedor de Docker de un registro privado (opcional)

Puede configurar las aplicaciones web en Linux para que ejecuten un contenedor almacenado en Azure Container Registry. Usar Azure Container Registry es igual que usa cualquier registro privado, por lo tanto, si necesita usar su registro privado, los pasos para completar la tarea serán similares.

El comando [az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) muestra las contraseñas del registro de contenedor. Copie el nombre de usuario y una de las contraseñas para que pueda usarlo para configurar la aplicación web en el paso siguiente.

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
  "username": "<azure-container-registry-name>"
}
```

Ejecute el comando [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set). Este comando asigna la imagen personalizada de Docker a la aplicación web. Tenga en cuenta que necesita una dirección URL con el formato `https://{your-registry-username}.azurecr.io`. Además, la aplicación web necesita el nombre de usuario y la contraseña que se obtuvieron en un paso anterior para acceder al registro de contenedor.

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> La dirección URL del registro necesita el `https`.
>

El comando revela una salida similar a la cadena JSON siguiente, lo que muestra que el cambio de configuración se realizó correctamente:

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

[Preguntas más frecuentes sobre Web App for Containers de Azure App Service ](app-service-linux-faq.md)
