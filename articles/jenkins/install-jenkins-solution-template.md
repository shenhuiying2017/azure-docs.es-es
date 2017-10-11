---
title: "Creación de un servidor de Jenkins en Azure"
description: "Instale Jenkins en una máquina virtual Linux de Azure a partir de la plantilla de solución de Jenkins y genere una aplicación Java de ejemplo."
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Creación de un servidor de Jenkins en una máquina virtual Linux de Azure desde Azure Portal

Este tutorial rápido muestra cómo instalar [Jenkins](https://jenkins.io) en una máquina virtual Linux de Ubuntu con las herramientas y complementos configurados para que funcionen con Azure. Cuando haya terminado, dispondrá de un servidor de Jenkins en ejecución en Azure que permitirá compilar una aplicación Java de ejemplo desde [GitHub](https://github.com).

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure
* Acceso a SSH en la línea de comandos del equipo (por ejemplo, el shell de Bash o [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Creación de la máquina virtual de Jenkins a partir de la plantilla de solución

Abra la [imagen de Marketplace para Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) en su explorador web y seleccione **OBTENERLA AHORA** en el lado izquierdo de la página. Revise los detalles de precios y seleccione **Continuar** y, después, seleccione **Crear** para configurar el servidor de Jenkins en Azure Portal. 
   
![Cuadro de diálogo de Azure Portal](./media/install-jenkins-solution-template/ap-create.png)

En la pestaña **Configurar opciones básicas**, rellene los campos siguientes:

![Configuración básica](./media/install-jenkins-solution-template/ap-basic.png)

* Use **Jenkins** en **Nombre**.
* Escriba un **nombre de usuario**. El nombre de usuario debe cumplir [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
* Seleccione **Contraseña** como **tipo de autenticación** y escriba una contraseña. La contraseña debe contener un carácter en mayúscula, un número y un carácter especial.
* Use **myJenkinsResourceGroup** en **Grupo de recursos**.
* Elija **Este de EE. UU.** como [región de Azure](https://azure.microsoft.com/regions/) en la lista desplegable **Ubicación**.

Seleccione **Aceptar** para pasar a la pestaña **Configurar opciones adicionales**. Escriba un nombre de dominio único para identificar el servidor Jenkins y seleccione **Aceptar**.

![Configurar opciones adicionales](./media/install-jenkins-solution-template/ap-addtional.png)  

 Una vez aprobada la validación, seleccione **Aceptar** de nuevo desde la pestaña **Resumen**. Por último, seleccione **Adquirir** para crear la máquina virtual de Jenkins. Cuando el servidor esté listo, recibirá una notificación en Azure Portal:   

![Notificación de que Jenkins está listo](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Conexión a Jenkins

Vaya a la máquina virtual (por ejemplo, http://jenkins2517454.eastus.cloudapp.azure.com/) en el explorador web. No se puede acceder a la consola de Jenkins a través de una HTTP no segura. Por ello, se proporcionan instrucciones en la página para acceder a la consola de forma segura desde su equipo mediante un túnel SSH.

![Desbloquear jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Configure el túnel mediante el comando `ssh` en la página de la línea de comandos, reemplazando `username` por el nombre del usuario administrador de la máquina virtual elegido anteriormente al configurar la máquina virtual desde la plantilla de solución.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Después de haber iniciado el túnel, vaya a http://localhost:8080/ en la máquina local. 

Obtenga la contraseña inicial mediante la ejecución del comando siguiente en la línea de comandos mientras está conectado a través de SSH a la máquina virtual de Jenkins.

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
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
