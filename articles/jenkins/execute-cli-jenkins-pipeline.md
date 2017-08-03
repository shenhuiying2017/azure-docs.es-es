---
title: "Implementación en Azure App Service con Jenkins y la CLI de Azure | Microsoft Docs"
description: "Aprenda a usar la CLI de Azure para implementar una aplicación web de Java para Azure en Jenkins Pipeline"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 73dd4b7ecde2b334fa01d105c27eba602b887aca
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Implementación en Azure App Service con Jenkins y la CLI de Azure
Para implementar una aplicación web de Java en Azure, puede utilizar la CLI de Azure en [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/). En este tutorial, creará una canalización de CI/CD en una máquina virtual de Azure, y aprenderá los siguientes temas:

> [!div class="checklist"]
> * Crear una máquina virtual de Jenkins
> * Configuración de Jenkins
> * Creación de una aplicación web en Azure
> * Preparación de un repositorio de GitHub
> * Creación de una canalización de Jenkins
> * Ejecución de la canalización y comprobación de la aplicación web

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Para encontrar la versión, ejecute `az --version`. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Creación y configuración de una instancia de Jenkins
Si aún no tiene un servidor maestro de Jenkins, comience con la [Solution Template](install-jenkins-solution-template.md) (Plantilla de la solución), que incluye el complemento [Azure Credentials](https://plugins.jenkins.io/azure-credentials) (Credenciales de Azure) necesario de forma predeterminada. 

El complemento Azure Credential permite almacenar las credenciales de la entidad de servicio de Microsoft Azure en Jenkins. En la versión 1.2, hemos agregado la compatibilidad para que Jenkins Pipeline pueda obtener las credenciales de Azure. 

Compruebe que dispone de la versión 1.2 o posterior:
* En el panel de Jenkins, haga clic en **Manage Jenkins -> Plugin Manager ->** (Administrar Jenkins -> Administrador de complementos) y busque **Azure Credential** (Credencial de Azure). 
* Actualice el complemento si la versión es anterior a la 1.2.

Java JDK y Maven también son necesarios en el servidor maestro Jenkins. Para la instalación, inicie sesión en el servidor maestro de Jenkins con SSH y ejecute los siguientes comandos:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Adición de un nombre de entidad de servicio de Azure a la credencial de Jenkins

Para ejecutar la CLI de Azure se necesita una credencial de Azure.

* En el panel de Jenkins, haga clic en **Credenciales -> System ->**(Credenciales -> Sistema). Haga clic en **Global credentials(unrestricted)**  [Credenciales (sin restricción) globales].
* Haga clic en **Add Credentials** (Agregar credenciales) para agregar una nueva [entidad de servicio de Microsoft Azure](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) rellenando los datos correspondientes a Subscription ID (Identificador de suscripción), Client ID (Identificador de cliente), Client Secret (Secreto de cliente) y OAuth 2.0 Token Endpoint (Punto de conexión de token OAuth 2.0). Proporcione un identificador para usarlo en el paso posterior.

![Adición de credenciales](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Creación de una instancia de Azure App Service para implementar la aplicación web de Java

Cree un plan de Azure App Service con el plan de tarifa **GRATIS** mediante el comando [az appservice plan create](/cli/azure/appservice/plan#create) de la CLI. Un plan de servicio de aplicaciones define los recursos físicos que se usan para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de servicio de aplicaciones comparten los recursos, lo que permite ahorrar costos al hospedar varias aplicaciones. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
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

 Use el comando de la CLI [az webapp create ](/cli/azure/appservice/web#create) para crear una definición de aplicación web en el plan de App Service `myAppServicePlan`. La definición de la aplicación web proporciona una dirección URL para acceder a la aplicación y configura varias opciones para implementar el código en Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
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

Establezca la configuración del sistema en tiempo de ejecución de Java que necesita la aplicación con el comando [az appservice web config update](/cli/azure/appservice/web/config#update).

El siguiente comando configura la aplicación web para que se ejecute en una versión reciente de Java 8 JDK y en [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Preparación de un repositorio de GitHub
Abra el repositorio de [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Aplicación web de Simple Java para Azure). Para bifurcar el repositorio en su propia cuenta de GitHub, haga clic en el botón **Fork** (Bifurcar) de la esquina superior derecha.

* En la interfaz de usuario web de GitHub, abra el archivo **Jenkinsfile**. Haga clic en el icono de lápiz para editar este archivo a fin de actualizar el grupo de recursos y el nombre de la aplicación web en las líneas 20 y 21, respectivamente.

```java
def resourceGroup = '<myResourceGroup>'

def webAppName = '<app_name>'

```
* Cambie la línea 23 para actualizar el identificador de la credencial en la instancia de Jenkins

```java
withCredentials([azureServicePrincipal('<azsrvprincipal>')]) {

```

## <a name="create-jenkins-pipeline"></a>Creación de una canalización de Jenkins
Abra Jenkins en un explorador web, haga clic en **New Item** (Nuevo elemento). 

* Proporcione un nombre para el trabajo y seleccione **Pipeline** (Canalización). Haga clic en **Aceptar**.
* Haga clic en la pestaña **Pipeline** (Canalización) a continuación. 
* En **Definition** (Definición), seleccione **Pipeline script from SCM** (Script de canalización del SCM).
* En **SCM**, seleccione **Git**.
* Escriba la dirección URL de GitHub para el repositorio bifurcado: https:\<su repositorio bifurcado\>.git
* Haga clic en **Guardar**

## <a name="test-your-pipeline"></a>Prueba de la canalización
* Vaya a la canalización que creó, haga clic en **Build Now** (Compilar ahora).
* En algunos segundos, debería crearse correctamente una compilación y puede ir a ella y hacer clic en **Console Output** (Salida de la consola) para ver los detalles

## <a name="verify-your-web-app"></a>Comprobación de la aplicación web
Para comprobar que el archivo WAR se ha implementado correctamente en la aplicación web: Abra un explorador web.

* Vaya a http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
Verá “**pong!**” como respuesta.

![Ping pong](./media/execute-cli-jenkins-pipeline/pingpong.png)

* Vaya a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (sustituya &lt;x> e &lt;y> con cualquier número) para obtener la suma de x e y

![Calculadora: suma](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha configurado una canalización de Jenkins que extrae del repositorio GitHub el código fuente. Se ejecuta Maven para generar un archivo war y, a continuación, se utiliza la CLI de Azure para implementarlo en Azure App Service. Ha aprendido a:

> [!div class="checklist"]
> * Crear una máquina virtual de Jenkins
> * Configuración de Jenkins
> * Creación de una aplicación web en Azure
> * Preparación de un repositorio de GitHub
> * Creación de una canalización de Jenkins
> * Ejecución de la canalización y comprobación de la aplicación web

