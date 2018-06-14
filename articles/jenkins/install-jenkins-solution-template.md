---
title: Creación de un servidor de Jenkins en Azure
description: Instale Jenkins en una máquina virtual Linux de Azure a partir de la plantilla de solución de Jenkins y genere una aplicación Java de ejemplo.
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: c9f86ab2536d3c598bb8c7084524395b41f18db0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936584"
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Creación de un servidor de Jenkins en una máquina virtual Linux de Azure desde Azure Portal

Este tutorial rápido muestra cómo instalar [Jenkins](https://jenkins.io) en una máquina virtual Linux de Ubuntu con las herramientas y complementos configurados para que funcionen con Azure. Cuando haya terminado, dispondrá de un servidor de Jenkins en ejecución en Azure que permitirá compilar una aplicación Java de ejemplo desde [GitHub](https://github.com).

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure
* Acceso a SSH en la línea de comandos del equipo (por ejemplo, el shell de Bash o [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Creación de la máquina virtual de Jenkins a partir de la plantilla de solución
Jenkins admite un modelo en el cual el servidor de Jenkins delega el trabajo a uno o más agentes para permitir que una única instalación de Jenkins pueda hospedar un gran número de proyectos o proporcionar diferentes entornos necesarios para realizar compilaciones o pruebas. Los pasos descritos en esta sección le ayudarán a instalar y configurar un servidor de Jenkins en Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Conexión a Jenkins

Navegue a la máquina virtual (por ejemplo, http://jenkins2517454.eastus.cloudapp.azure.com/) en el explorador web. No se puede acceder a la consola de Jenkins a través de una HTTP no segura. Por ello, se proporcionan instrucciones en la página para acceder a la consola de forma segura desde su equipo mediante un túnel SSH.

![Desbloquear jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Configure el túnel mediante el comando `ssh` en la página de la línea de comandos, reemplazando `username` por el nombre del usuario administrador de la máquina virtual elegido anteriormente al configurar la máquina virtual desde la plantilla de solución.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Después de haber iniciado el túnel, vaya a http://localhost:8080/ en la máquina local. 

Obtenga la contraseña inicial mediante la ejecución del comando siguiente en la línea de comandos mientras está conectado a través de SSH a la máquina virtual de Jenkins.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Desbloquee el panel de Jenkins por primera vez con la contraseña inicial.

![Desbloquear jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Seleccione **Instalar los complementos sugeridos** en la siguiente página y, a continuación, cree un usuario administrador de Jenkins que se pueda utilizar para acceder al panel de este.

![Jenkins ya está listo.](./media/install-jenkins-solution-template/jenkins-welcome.png)

El servidor Jenkins ya está preparado para compilar código.

## <a name="create-your-first-job"></a>Creación del primer trabajo

Seleccione **Create new jobs** (Crear nuevos trabajos) en la consola de Jenkins, a continuación, asígnele el nombre **mySampleApp** y seleccione **Freestyle project** (Proyecto de estilo libre) y, finalmente, seleccione **Aceptar**.

![Crear un nuevo trabajo](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Seleccione la pestaña **Source Code Management** (Administración del código fuente), habilite **Git** y escriba la siguiente dirección URL en el campo **Repository URL** (URL del repositorio): `https://github.com/spring-guides/gs-spring-boot.git`

![Definir el repositorio Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Seleccione la pestaña **Build** (Compilación), después, seleccione **Add build step** (Agregar el paso de compilación) y seleccione la opción **Invoke Gradle script** (Invocar script de Gradle). Seleccione **Use Gradle Wrapper** (Usar contenedor de Gradle). A continuación, escriba `complete` en **Wrapper location** (Ubicación del contenedor) y `build` en **Tasks** (Tareas).

![Usar el contenedor de Gradle para compilar](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Seleccione **Advanced** (Avanzadas) y, a continuación, escriba `complete` en el campo **Root Build script** (Script de compilación raíz). Seleccione **Guardar**.

![Establecer configuraciones avanzadas en el paso de compilación del contenedor de Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Compilación del código

Seleccione **Build Now** (Compilar ahora) para compilar el código y empaquetar la aplicación de ejemplo. Cuando se complete la compilación, seleccione el vínculo del **área de trabajo** del proyecto.

![Vaya al área de trabajo para obtener el archivo JAR de la compilación](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Vaya a `complete/build/libs` y asegúrese de que `gs-spring-boot-0.1.0.jar` está allí para comprobar que la compilación se realizó correctamente. El servidor de Jenkins ya está listo para compilar sus propios proyectos de Azure.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Agregar máquinas virtuales de Azure como agentes de Jenkins](jenkins-azure-vm-agents.md)
