---
title: "Creación de una canalización de desarrollo en Azure con Jenkins | Microsoft Docs"
description: "Obtenga información sobre cómo crear una máquina virtual de Jenkins en Azure que se obtiene desde GitHub en cada confirmación de código y que crea un nuevo contenedor de Docker para ejecutar la aplicación"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 66dee639ddb1f59199af2905bcd7b1d87a62289c
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Creación de una infraestructura de desarrollo en una máquina virtual Linux en Azure con Jenkins, GitHub y Docker
Para automatizar las fases de compilación y prueba del desarrollo de la aplicación, puede utilizar una canalización de integración e implementación continua (CI/CD). En este tutorial, creará una canalización de CI/CD en una máquina virtual de Azure, y aprenderá los siguientes temas:

> [!div class="checklist"]
> * Crear una máquina virtual de Jenkins
> * Instalar y configurar Jenkins
> * Crear la integración de webhook entre GitHub y Jenkins
> * Crear y desencadenar trabajos de compilación de Jenkins desde confirmaciones de GitHub
> * Crear una imagen de Docker para la aplicación
> * Comprobar que GitHub confirma la imagen de Docker de nueva compilación y actualiza la aplicación en ejecución


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure de la versión 2.0.22 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-jenkins-instance"></a>Creación de la instancia de Jenkins
En un tutorial anterior sobre [cómo personalizar una máquina virtual Linux en el primer arranque](tutorial-automate-vm-deployment.md), aprendió a automatizar la personalización de máquinas virtuales con cloud-init. Este tutorial utiliza un archivo cloud-init para instalar Jenkins y Docker en una máquina virtual. Jenkins es un popular servidor de automatización de código abierto que se integra perfectamente con Azure para habilitar la integración continua (CI) y la entrega continua (CD). Para más tutoriales sobre cómo usar Jenkins, consulte el artículo sobre [Jenkins en el concentrador de Azure](https://docs.microsoft.com/azure/jenkins/).

En el shell actual, cree un archivo denominado *cloud-init-jenkins.txt* y pegue la siguiente configuración. Por ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Escriba `sensible-editor cloud-init-jenkins.txt` para crear el archivo y ver una lista de editores disponibles. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  - sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - touch /var/lib/jenkins/jenkins.install.InstallUtil.lastExecVersion
  - service jenkins restart
```

Antes de poder crear una máquina virtual, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado "*myResourceGroupJenkins*" en la ubicación *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Ahora cree una máquina virtual con el comando [az vm create](/cli/azure/vm#create). Use el parámetro `--custom-data` para pasar su archivo de configuración cloud-init. Proporcione la ruta de acceso completa a la configuración de *cloud-init-jenkins.txt* si guardó el archivo fuera de su directorio de trabajo actual.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

La máquina virtual tarda unos minutos en crearse y configurarse.

Con el objetivo de permitir que el tráfico web llegue a la máquina virtual, use [az vm open-port](/cli/azure/vm#open-port) para abrir el puerto *8080* para el tráfico de Jenkins, y el puerto *1337* para la aplicación Node.js que se usa para ejecutar una aplicación de ejemplo:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Configuración de Jenkins
Para acceder a la instancia de Jenkins, obtenga la dirección IP pública de la máquina virtual:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Por motivos de seguridad, debe escribir la contraseña de administrador inicial que se almacena en un archivo de texto de la máquina virtual para iniciar la instalación de Jenkins. Use la dirección IP pública que obtuvo en el paso anterior para acceder mediante SSH a la máquina virtual:

```bash
ssh azureuser@<publicIps>
```

Consulte el valor de `initialAdminPassword` de su instalación de Jenkins y cópielo:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Si el archivo aún no está disponible, espere unos minutos más a que cloud-init complete la instalación de Jenkins y Docker.

Ahora, abra un explorador web y vaya a `http://<publicIps>:8080`. Complete la instalación inicial de Jenkins como sigue:

- Escriba el nombre de usuario **admin** y proporcione el valor de *initialAdminPassword* que obtuvo de la máquina virtual en el paso anterior.
- Seleccione **Manage Jenkins** (Administrar Jenkins) y, luego, **Manage plugins** (Administrar complementos).
- Elija **Available** (Disponible) y busque *GitHub* en el cuadro de texto en la parte superior. Active la casilla de *GitHub plugin* (Complemento de GitHub) y seleccione **Download now and install after restart** (Descargar ahora e instalar después de reiniciar).
- Active la casilla de **Restart Jenkins when installation is complete and no jobs are running** (Restaurar Jenkins cuando se complete la instalación y no haya trabajos en ejecución) y espere hasta que finalice el proceso de instalación del complemento.


## <a name="create-github-webhook"></a>Creación de un webhook de GitHub
Para configurar la integración con GitHub, abra la [aplicación de ejemplo Hello World de Node.js](https://github.com/Azure-Samples/nodejs-docs-hello-world) desde el repositorio de ejemplos de Azure. Para bifurcar el repositorio en su propia cuenta de GitHub, seleccione el botón **Fork** (Bifurcar) de la esquina superior derecha.

Cree un webhook dentro de la bifurcación que ha creado:

- Seleccione **Settings** (Configuración) y, luego, **Integrations & services** (Integración y servicios) en el lado izquierdo.
- Elija **Agregar servicio** y, luego, escriba *Jenkins* en el cuadro de filtro.
- Seleccione *Jenkins (GitHub plugin)* Jenkins (complemento de GitHub).
- En **Jenkins hook URL** (URL de servicio de Jenkins), escriba `http://<publicIps>:8080/github-webhook/`. Asegúrese de incluir la barra diagonal final (/).
- Seleccione **Agregar servicio**.

![Incorporación del webhook de GitHub en el repositorio bifurcado](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Creación de trabajos de Jenkins
Para que Jenkins responda a un evento de GitHub como código de confirmación, cree un trabajo de Jenkins. 

En el sitio web de Jenkins, seleccione **Create new jobs** (Crear trabajos nuevos) desde la página principal:

- Escriba *HelloWorld* como nombre del trabajo. Seleccione **Freestyle project** (Proyecto de estilo libre) y elija **OK** (Aceptar).
- En la sección **General**, seleccione el proyecto de **GitHub** y escriba la dirección URL del repositorio bifurcado; por ejemplo, *https://github.com/iainfoulds/nodejs-docs-hello-world*
- En la sección **Source code management** (Administración de código fuente), seleccione **Git** y escriba la dirección URL *.git* del repositorio bifurcado; por ejemplo, *https://github.com/iainfoulds/nodejs-docs-hello-world.git*.
- En la sección **Build Triggers** (Compilar desencadenadores), seleccione **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para sondeo de GITScm).
- En la sección **Build** (Compilación), seleccione **Add build step** (Agregar el paso de compilación). Seleccione **Execute shell** (Ejecutar shell) y escriba `echo "Testing"` en la ventana de comandos.
- Seleccione **Save** (Guardar) en la parte inferior de la ventana de trabajos.


## <a name="test-github-integration"></a>Prueba de la integración de GitHub
Para probar la integración de GitHub con Jenkins, confirme un cambio en la bifurcación. 

En la interfaz de usuario web de GitHub, seleccione el repositorio bifurcado y, luego, seleccione el archivo **index.js**. Seleccione el icono de lápiz para editar este archivo para que la línea 6 tenga este aspecto:

```nodejs
response.end("Hello World!");
```

Para confirmar sus cambios, seleccione el botón **Confirmar cambios** situado en la parte inferior.

En Jenkins, se inicia una nueva compilación en la sección **Build history** (Historial de compilaciones) de la esquina inferior izquierda de la página de trabajos. Elija el vínculo del número de compilación y seleccione **Console output** (Resultado de la consola) en la izquierda. Puede ver los pasos que realiza Jenkins cuando el código se ha extraído de GitHub y la acción de compilación genera el mensaje `Testing` en la consola. Cada vez que se realiza una confirmación en GitHub, el webhook llega a Jenkins y desencadena una nueva compilación de este modo.


## <a name="define-docker-build-image"></a>Definición de la imagen de la compilación de Docker
Para ver la aplicación de Node.js en ejecución en función de las confirmaciones de GitHub, permita que se genere una imagen de Docker para ejecutar la aplicación. La imagen se crea a partir de un archivo Dockerfile que define cómo configurar el contenedor que ejecuta la aplicación. 

Desde la conexión SSH a la máquina virtual, cambie al directorio del área de trabajo de Jenkins denominado después del trabajo que creó en el paso anterior. En nuestro ejemplo, se llama "*HelloWorld*".

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Cree un archivo en este directorio de área de trabajo con `sudo sensible-editor Dockerfile` y pegue el contenido siguiente. Asegúrese de que todo el archivo Dockerfile se copia correctamente, especialmente la primera línea:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Este archivo Dockerfile utiliza la imagen base de Node.js con Linux Alpine, expone el puerto 1337 que ejecuta la aplicación Hello World y, luego, copia los archivos de aplicación y la inicializa.


## <a name="create-jenkins-build-rules"></a>Creación de reglas de generación de Jenkins
En un paso anterior, creó una regla de compilación de Jenkins básica que generó un mensaje en la consola. Permite crear el paso de compilación para usar nuestro Dockerfile y ejecutar la aplicación.

En la instancia Jenkins, seleccione el trabajo que creó en el paso anterior. Seleccione **Configure** (Configurar) en el lado izquierdo y desplácese hacia abajo hasta la sección **Build** (Compilación):

- Quite el paso de compilación `echo "Test"`. Seleccione la cruz roja de la esquina superior derecha de la casilla del paso de compilación existente.
- Elija **Add build step** (Agregar paso de compilación) y seleccione **Execute shell** (Ejecutar shell).
- En el cuadro **Command** (Comando), escriba los comandos siguientes de Docker y, luego, seleccione **Save** (Guardar):

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Los pasos de compilación de Docker crean una imagen y etiqueta con el número de compilación de Jenkins para que pueda mantener un historial de imágenes. Los contenedores existentes que ejecutan la aplicación se detendrán y, luego, se quitarán. Después, un nuevo contenedor se inicia con la imagen y ejecuta la aplicación de Node.js en función de las confirmaciones más recientes de GitHub.


## <a name="test-your-pipeline"></a>Prueba de la canalización
Para ver toda la canalización en acción, edite el archivo *index.js* en el nuevo repositorio de GitHub bifurcado y seleccione **Commit change** (Confirmar cambio). Un nuevo trabajo se inicia en Jenkins según el webhook de GitHub. Tarda unos segundos en crearse la imagen de Docker e iniciarse la aplicación en un nuevo contenedor.

Si es necesario, vuelva a obtener la dirección IP pública de la máquina virtual:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Abra un explorador web y escriba `http://<publicIps>:1337`. La aplicación de Node.js se muestra y refleja las confirmaciones más recientes en la bifurcación de GitHub como se indica a continuación:

![Ejecución de la aplicación Node.js](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Ahora, realice otra modificación en el archivo *index.js* de GitHub y confirme el cambio. Espere unos segundos para que el trabajo se complete en Jenkins; luego, actualice el explorador web para ver la versión actualizada de la aplicación que se ejecuta en un nuevo contenedor como sigue:

![Aplicación de Node.js en ejecución después de otra confirmación de GitHub](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha configurado GitHub para ejecutar un trabajo de compilación de Jenkins en cada confirmación de código y, luego, ha implementado un contenedor de Docker para probar la aplicación. Ha aprendido a:

> [!div class="checklist"]
> * Crear una máquina virtual de Jenkins
> * Instalar y configurar Jenkins
> * Crear la integración de webhook entre GitHub y Jenkins
> * Crear y desencadenar trabajos de compilación de Jenkins desde confirmaciones de GitHub
> * Crear una imagen de Docker para la aplicación
> * Comprobar que GitHub confirma la imagen de Docker de nueva compilación y actualiza la aplicación en ejecución

Siga con el próximo tutorial para obtener más información sobre cómo integrar Jenkins con Visual Studio Team Services.

> [!div class="nextstepaction"]
> [Implementación de aplicaciones con Jenkins y Team Services](tutorial-build-deploy-jenkins.md)
