---
title: "Implementación continua de Jenkins con Kubernetes en Azure Container Service"
description: "Cómo automatizar un proceso de implementación continua con Jenkins para implementar y actualizar una aplicación en contenedor en Kubernetes en Azure Container Service"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1293fda45602203570a0f7f75481f67bdcb6edf3
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-container-service"></a>Implementación continua con Jenkins y Azure Container Service

En este documento se muestra cómo configurar un flujo de trabajo de implementación continua básico entre Jenkins y un clúster de Azure Container Service (AKS). 

El flujo de trabajo de ejemplo incluye los siguientes pasos:

> [!div class="checklist"]
> * Se implementa la aplicación de Azure Vote en el clúster de Kubernetes.
> * Se actualiza el código de aplicación de Azure Vote y se inserta en un repositorio de GitHub, que inicia el proceso de implementación continua.
> * Jenkins clona el repositorio y genera una nueva imagen de contenedor con el código actualizado.
> * Esta imagen se inserta en una instancia de Azure Container Registry (ACR).
> * La aplicación en ejecución en el clúster de AKS se actualiza con la nueva imagen de contenedor.

## <a name="prerequisites"></a>requisitos previos

Necesita los elementos siguientes para completar los pasos de este artículo.

- Conocimientos básicos de Kubernetes, Git, CI/CD y Azure Container Registry (ACR).
- Un [clúster de Azure Container Service (AKS)][aks-quickstart] y [credenciales de AKS configuradas][aks-credentials] en el sistema de desarrollo.
- Un [registro de Azure Container Registry (ACR)][acr-quickstart], el nombre del servidor de inicio de sesión en ACR y [credenciales de ACR] [ acr-authentication] con acceso de inserción y extracción.
- CLI de Azure instalada en el sistema de desarrollo.
- Docker instalado en el sistema de desarrollo.
- Cuenta de GitHub, [token de acceso personal a GitHub][git-access-token] y el cliente de Git instalado en el sistema de desarrollo.

## <a name="prepare-application"></a>Preparación de la aplicación

La aplicación de Azure Vote que se usa en este documento contiene una interfaz web hospedada en uno o varios pods y un segundo pod que hospeda Redis para el almacenamiento de datos temporales. 

Antes de generar la integración de Jenkins/AKS, prepare e implemente la aplicación de Azure Vote en el clúster de AKS. Considere esta como la versión uno de la aplicación.

Bifurque el siguiente repositorio de GitHub.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Cuando haya creado la bifurcación, clónela el sistema de desarrollo. Asegúrese de que usa la dirección URL de la bifurcación al clonar este repositorio.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Cambie los directorios para trabajar desde el directorio clonado.

```bash
cd azure-voting-app-redis
```

Ejecute el archivo `docker-compose.yaml` para crear la imagen de contenedor de `azure-vote-front` e inicie la aplicación.

```bash
docker-compose up -d
```

Cuando haya finalizado, use el comando [docker images][docker-images] para ver la imagen creada.

Tenga en cuenta que se han descargado o creado tres imágenes. La imagen `azure-vote-front` contiene la aplicación y usa la imagen `nginx-flask` como base. La imagen `redis` se usa para iniciar una instancia de Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Obtenga el servidor de inicio de sesión de ACR con el comando [az acr list][az-acr-list]. Asegúrese de actualizar el nombre del grupo de recursos con el grupo de recursos que hospeda el registro de ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use el comando [docker tag] [ docker-tag] para etiquetar la imagen con el nombre del servidor de inicio de sesión y un número de versión `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Actualice el valor del servidor de inicio de sesión de ACR con el nombre del servidor de inicio de sesión de ACR e inserte la imagen `azure-vote-front` en el registro. 

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Implementación de una aplicación en Kubernetes

Puede encontrar un archivo de manifiesto de Kubernetes en la raíz del repositorio de Azure Vote que puede usar para implementar la aplicación en el clúster de Kubernetes.

En primer lugar, actualice el archivo de manifiesto **azure-vote-all-in-one-redis.yaml** con la ubicación del registro de ACR. Abra el archivo con cualquier editor de texto y reemplace `microsoft` por el nombre del servidor de inicio de sesión de ACR. Este valor se encuentra en la línea **47** del archivo de manifiesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Luego, use el comando [kubectl create][kubectl-create] para ejecutar la aplicación. Este comando analiza el archivo de manifiesto y crea los objetos de Kubernetes definidos.

```bash
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Se crea un [servicio de Kubernetes][kubernetes-service] que expone la aplicación a Internet. Este proceso puede tardar unos minutos. 

Para supervisar el progreso, utilice el comando [kubectl get service][kubectl-get] con el argumento `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

En un primer momento, el parámetro *EXTERNAL-IP* del servicio *azure-vote-front* aparece como *pendiente*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Una vez que la dirección *EXTERNAL-IP* haya cambiado de *pendiente* a una *dirección IP*, use `control+c` para detener el proceso de inspección de kubectl. 

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Para ver la aplicación, navegue hasta la dirección IP externa.

![Imagen del clúster de Kubernetes en Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Implementación de Jenkins en una máquina virtual

Se ha creado previamente un script que implementa una máquina virtual, configura el acceso de red y completa una instalación básica de Jenkins. Además, el script copia el archivo de configuración de Kubernetes del sistema de desarrollo en el sistema Jenkins. Este archivo se usa para la autenticación entre Jenkins y el clúster de AKS.

Ejecute los siguientes comandos para descargar y ejecutar el script. También se puede usar la siguiente dirección URL para revisar el contenido del script.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Cuando se completa, el script genera una dirección para el servidor Jenkins, además de una clave para desbloquear Jenkins. Vaya a la dirección URL, escriba la clave y siga las indicaciones en pantalla para completar la configuración de Jenkins.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Variables de entorno de Jenkins

Una variable de entorno de Jenkins se usa para contener el nombre de inicio de sesión de Azure Container Registry (ACR). Se hace referencia a esta variable durante el trabajo de implementación continua de Jenkins.

En el portal de administración de Jenkins, haga clic en **Manage Jenkins** >  (Administrar Jenkins) **Configure System** (Configurar sistema).

En **Global Properties** (Propiedades globales), seleccione **Environment variables** (Variables de entorno) y agregue una variable con el nombre `ACR_LOGINSERVER` y un valor del servidor de inicio de sesión de ACR.

![Variables de entorno de Jenkins](media/aks-jenkins/env-variables.png)

Cuando haya finalizado, haga clic en **Save** (Guardar) en la página de configuración de Jenkins.

## <a name="jenkins-credentials"></a>Credenciales de Jenkins

Almacene ahora sus credenciales de ACR en un objeto de credencial de Jenkins. A estas credenciales se hace referencia durante el trabajo de compilación de Jenkins.

De nuevo en el portal de administración de Jenkins, haga clic en **Credentials** (Credenciales) > **Jenkins** > **Global credentials (unrestricted)** (Credenciales globales [sin restricción]) > **Add Credentials** (Agregar credenciales).

Asegúrese de que el tipo de credencial es **Username with password** (Nombre de usuario con contraseña) y especifique lo siguiente:

- **Username** (Nombre de usuario): identificador de la entidad de servicio que se usa en la autenticación con el registro de ACR.
- **Password** (Contraseña): secreto de cliente de la entidad de servicio que se usa en la autenticación con el registro de ACR.
- **ID** (Id.): identificador de credencial como, por ejemplo, `acr-credentials`.

Cuando haya finalizado, el formulario de credenciales debe tener un aspecto similar al de la siguiente imagen:

![Credenciales de ACR](media/aks-jenkins/acr-credentials.png)

Haga clic en **OK** (Aceptar) y vuelva al portal de administración de Jenkins.

## <a name="create-jenkins-project"></a>Creación del proyecto de Jenkins

En el portal de administración de Jenkins, haga clic en **New Item** (Nuevo elemento).

Asigne al proyecto un nombre, por ejemplo `azure-vote`, seleccione **Freestyle project** (Proyecto de estilo libre) y haga clic en **OK** (Aceptar). 

![Proyecto de Jenkins](media/aks-jenkins/jenkins-project.png)

En **General**, seleccione **GitHub project** (Proyecto de GitHub) y escriba la dirección URL en la bifurcación del proyecto de GitHub de Azure Vote.

![Proyecto de GitHub](media/aks-jenkins/github-project.png)

En **Source Code Management** (Administración del código fuente), seleccione **Git** y escriba la dirección URL en la bifurcación del repositorio de GitHub de Azure Vote. 

Para las credenciales, haga clic en y **Add** (Agregar) > **Jenkins**. En **Kind** (Tipo), seleccione **Secret text** (Texto secreto) y especifique el [token de acceso personal a GitHub][git-access-token] como secreto. 

Seleccione **Add** (Agregar) cuando haya finalizado.

![Credenciales de GitHub](media/aks-jenkins/github-creds.png)

En **Build Triggers** (Desencadenadores de compilación), seleccione **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para sondeo de GITScm).

![Desencadenadores de compilación de Jenkins](media/aks-jenkins/build-triggers.png)

En **Build Environment** (Entorno de compilación), seleccione **Use secret texts or files** (Usar textos secretos o archivos).

![Entorno de compilación de Jenkins](media/aks-jenkins/build-environment.png)

En **Bindings** (Enlaces), seleccione **Add** (Agregar) > **Username and password (separated)** (Nombre de usuario y contraseña [separados]). 

Escriba `ACR_ID` como **Username Variable** (Variable de nombre de usuario) y `ACR_PASSWORD` como **Password Variable** (Variable de contraseña).

![Enlaces de Jenkins](media/aks-jenkins/bindings.png)

Agregue un **Build Step** (Paso de compilación) de tipo **Execute shell** (Ejecutar shell) y use el texto siguiente. Este script genera una nueva imagen de contenedor y la inserta en el registro de ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Agregue otro **Build Step** (Paso de compilación) de tipo **Execute shell** (Ejecutar shell) y use el texto siguiente. Este script actualiza la implementación de Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Cuando termine, haga clic en **Save** (Guardar).

## <a name="test-the-jenkins-build"></a>Prueba de la compilación de Jenkins

Antes de continuar, pruebe la compilación de Jenkins. Así se confirma que el trabajo de compilación se ha configurado correctamente, se ha implementado el archivo de autenticación Kubernetes adecuado y se han especificado las credenciales adecuadas de ACR.

Haga clic en **Build Now** (Compilar ahora) en el menú de la izquierda del proyecto. 

![Compilación de prueba de Jenkins](media/aks-jenkins/test-build.png)

Durante este proceso, se clona el repositorio de GitHub en el servidor de compilación de Jenkins. Se genera una nueva imagen de contenedor y se inserta en el registro de ACR. Por último, se actualiza la aplicación de Azure Vote en ejecución en el clúster de AKS para usar la nueva imagen. Dado que no se ha efectuado ningún cambio en el código de aplicación, la aplicación no se modifica.

Cuando el proceso haya finalizado, puede hacer clic en **build #1** (compilación n.º 1) en el historial de compilaciones y seleccionar **Console Output** (Salida de consola) para ver toda la salida del proceso de compilación. La última línea debe indicar una compilación correcta. 

## <a name="create-github-webhook"></a>Creación de un webhook de GitHub

A continuación, enlace el repositorio de aplicación con el servidor de compilación de Jenkins para que, en caso de confirmación, se desencadene una nueva compilación.

1. Vaya al repositorio de GitHub bifurcado.
2. Seleccione **Settings** (Configuración) y, luego, **Integrations & services** (Integración y servicios) en el lado izquierdo.
3. Elija **Add Service** (Agregar servicio), escriba `Jenkins (GitHub plugin)` en el cuadro de filtro y seleccione el complemento.
4. En la dirección URL de servicio de Jenkins, escriba `http://<publicIp:8080>/github-webhook/` donde `publicIp` corresponde a dirección IP del servidor de Jenkins. Asegúrese de incluir la barra diagonal (/) final.
5. Seleccione Add service (Agregar servicio).
  
![Webhook de GitHub](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Prueba del proceso de CI/CD de extremo a extremo

En la máquina de desarrollo, abra la aplicación clonada con un editor de código. 

En el directorio **/azure-vote/azure-vote**, encontrará un archivo denominado **config_file.cfg**. Actualice los valores de Vote en este archivo a algo distinto de gatos y perros. 

En el ejemplo siguiente se muestra y actualiza el archivo **config_file.cfg**.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Cuando haya finalizado, guarde el archivo, confirme los cambios e insértelos en la bifurcación del repositorio de GitHub. Una vez completada la confirmación, el webhook de GitHub desencadena una nueva compilación de Jenkins, que actualiza la imagen del contenedor y la implementación de AKS. Supervise el proceso de compilación en la consola de administración de Jenkins. 

Una vez completada la compilación, vaya de nuevo al punto de conexión de la aplicación para observar los cambios.

![Azure Vote actualizado](media/aks-jenkins/azure-vote-updated-safari.png)

Llegados a este punto, se ha completado un proceso de implementación continua simple. Los pasos y configuraciones que se muestran en este ejemplo pueden usarse para crear una automatización de compilación continua más sólida y lista para producción.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli