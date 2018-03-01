---
title: "Escale las implementaciones de Jenkins con agentes de máquina virtual de Azure."
description: "Agregue una funcionalidad adicional a las canalizaciones de Jenkins mediante Azure Virtual Machines con el complemento de agente de máquina virtual de Azure."
services: multiple
documentationcenter: 
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 4d45ed14be499ed927f1433e134a029066146eea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Escalado de las implementaciones de Jenkins para satisfacer la demanda con agentes de máquina virtual de Azure

En este tutorial se muestra cómo utilizar el [complemento de agentes de máquina virtual de Azure](https://plugins.jenkins.io/azure-vm-agents) de Jenkins para agregar más funcionalidad a petición con máquinas virtuales de Linux que se ejecutan en Azure.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * Instalación del complemento de agentes de máquina virtual de Azure
> * Configuración del complemento para crear recursos en la suscripción de Azure
> * Establecimiento de los recursos de procesos disponibles en cada agente
> * Establecimiento del sistema operativo y las herramientas instaladas en cada agentes
> * Creación de un nuevo trabajo de Freestyle de Jenkins
> * Ejecución del trabajo en un agente de máquina virtual de Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure
* Un servidor maestro de Jenkins. Si no lo tiene, vea el [inicio rápido](install-jenkins-solution-template.md) para configurar uno en Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Instalación del complemento de agentes de máquina virtual de Azure

> [!TIP]
> Si ha implementado Jenkins en Azure mediante la [plantilla de solución](install-jenkins-solution-template.md), el complemento de agente de máquina virtual de Azure ya está instalado.

1. En el panel de Jenkins, seleccione **Manage Jenkins** (Administrar Jenkins) y después **Manage Plugins** (Administrar complementos).
2. Seleccione la pestaña **Available** (Disponible) y después busque **Azure VM Agents** (Agentes de máquina virtual de Azure). Active la casilla junto a la entrada del complemento y seleccione **Install without restart** (Instalar sin reiniciar) en la parte inferior del panel.

## <a name="configure-the-azure-vm-agents-plugin"></a>Configuración del complemento de agentes de máquina virtual de Azure

1. En el panel de Jenkins, seleccione **Manage Jenkins** (Administrar Jenkins) y luego **Configure System** (Configurar sistema).
2. Desplácese hasta la parte inferior de la página y busque la sección **Cloud** (Nube) con el menú desplegable **Add new cloud** (Agregar nueva nube) y seleccione **Microsoft Azure VM Agents** (Agentes de máquina virtual de Microsoft Azure).
3. Seleccione una entidad de servicio existente en el menú desplegable **Add** (Agregar) de la sección **Azure Credentials** (Credenciales de Azure). Si no hay ninguna, realice los pasos siguientes para [crear una entidad de servicio](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) para la cuenta de Azure y agréguela a la configuración de Jenkins:   

    a. Seleccione **Add** (Agregar) junto a **Azure Credentials** (Credenciales de Azure) y elija **Jenkins**.   
    b. En el cuadro de diálogo **Add Credentials** (Agregar credenciales), seleccione la opción **Microsoft Azure Service Principal** (Entidad de servicio de Microsoft Azure) del menú desplegable **Kind** (Variante).   
    c. Cree una entidad de servicio de Active Directory desde la CLI de Azure o [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Escriba las credenciales de la entidad de servicio en el cuadro de diálogo **Add credentials** (Agregar credenciales). Si no conoce el identificador de la suscripción de Azure, puede consultarlo desde la CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    La entidad de servicio completada debe usar el campo `id` para **Subscription ID** (Id. de suscripción), el valor `appId` para **Client ID** (Id. de cliente), `password` para **Client Secret** (Secreto de cliente) y `tenant` para **Tenant ID** (Identificador de inquilino). Seleccione **Add** (Agregar) para agregar la entidad de servicio y después configure el complemento para utilizar la credencial recién creada.

    ![Configuración de una entidad de servicio de Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. En la sección **Resource Group Name** (Nombre del grupo de recursos), deje seleccionada la opción **Create new** (Crear nuevo) y escriba `myJenkinsAgentGroup`.
5. Seleccione **Verify configuration** (Verificar configuración) para conectarse a Azure a fin de probar la configuración del perfil.
6. Seleccione **Apply** (Aplicar) para actualizar la configuración del complemento.

## <a name="configure-agent-resources"></a>Configuración de los recursos del agente

Configure una plantilla para utilizarla para definir un agente de máquina virtual de Azure. Esta plantilla define los recursos de procesos que cada agente tiene cuando se creó.

1. Seleccione **Add** (Agregar) junto a **Add Azure Virtual Machine Template** (Agregar plantilla de máquina virtual de Azure).
2. Escriba `defaulttemplate` en **Name** (Nombre).
3. Escriba `ubuntu` en **Label** (Etiqueta).
4. Seleccione la [región de Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) deseada en el cuadro combinado.
5. Seleccione un [tamaño de máquina virtual](/azure/virtual-machines/linux/sizes) en la lista desplegable de **Virtual Machine Size** (Tamaño de máquina virtual). Un tamaño `Standard_DS1_v2` de uso general es válido para este tutorial.   
6. Deje el campo **Retention time** (Tiempo de retención) en `60`. Esta opción define el número de minutos que Jenkins debe esperar antes de desasignar agentes inactivos. Especifique 0 si no desea que los agentes inactivos se eliminen automáticamente.

   ![Configuración general de máquina virtual](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Configuración de las herramientas y el sistema operativo del agente

En la sección **Image Configuration** (Configuración de la imagen) de la configuración del complemento, seleccione **Ubuntu 16.04 LTS**. Active las casillas junto a **Install Git (Latest)** [Instalar Git (última versión)], **Install Maven (V3.5.0)** [Instalar Maven (V3.5.0)] e **Install Docker** (Instalar Docker) para instalar estas herramientas en los agentes recién creados.

![Configuración de las herramientas y el sistema operativo de la máquina virtual](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Seleccione **Add** (Agregar) junto a **Admin Credentials** (Credenciales de administrador) y después seleccione **Jenkins**. Escriba un nombre de usuario y la contraseña que utiliza para iniciar sesión en los agentes, pero asegúrese de que cumplan la [directiva de nombre de usuario y contraseña](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) de las cuentas administrativas de Azure Virtual Machines.

Seleccione **Verify Template** (Verificar plantilla) para verificar la configuración y después seleccione **Save** (Guardar) para guardar los cambios y volver al panel de Jenkins.

## <a name="create-a-job-in-jenkins"></a>Creación de un trabajo en Jenkins

1. En el panel de Jenkins, haga clic en **New Item**(Nuevo elemento). 
2. Escriba `demoproject1` para el nombre, seleccione **Freestyle project** (Proyecto de Freestyle) y luego seleccione **OK** (Aceptar).
3. En la pestaña **General**, elija **Restrict where project can be run** (Restringir donde se pueda ejecutar el proyecto) y escriba `ubuntu` en **Label Expression** (Expresión de etiqueta). Aparece un mensaje que confirma que la etiqueta la proporciona la configuración de la nube creada en el paso anterior. 
   ![Configuración de un trabajo](./media/jenkins-azure-vm-agents/job-config.png)
4. En la pestaña **Source Code Management** (Administración del código fuente), seleccione **Git** y agregue la siguiente dirección URL en el campo **Repository URL** (Dirección URL del repositorio): `https://github.com/spring-projects/spring-petclinic.git`.
5. En la pestaña **Build** (Compilar), seleccione **Add build step** (Agregar paso de compilación) y elija **Invoke top-level Maven targets** (Invocar destinos de Maven de nivel superior). Escriba `package` en el campo **Goals** (Objetivos).
6. Seleccione **Save** (Guardar) para guardar la definición del trabajo.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Compilación del nuevo trabajo en un agente de máquina virtual de Azure

1. Vuelva al panel de Jenkins.
2. Seleccione el trabajo que creó en el paso anterior y haga clic en **Build now** (Compilar ahora). Una nueva compilación se pone en cola, pero no se inicia hasta que se crea una máquina virtual del agente en la suscripción de Azure.
3. Una vez completada la compilación, vaya a **Console output** (Salida de consola). Allí puede ver que la compilación se realizó de forma remota en un agente de Azure.

![Salida de consola](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [CI/CD en Azure App Service](java-deploy-webapp-tutorial.md)
