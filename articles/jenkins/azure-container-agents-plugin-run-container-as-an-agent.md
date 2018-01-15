---
title: Compilar un proyecto en Azure con Jenkins y Azure Container Instances
description: "Obtenga información acerca de cómo usar el complemento de Azure Container Agent para Jenkins para compilar un proyecto en Azure con Azure Container Instances"
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 04a60bf021ec6e265a3880264386ad32ec7e8177
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Compilar un proyecto en Azure con Jenkins y Azure Container Instances

Con Azure Container Instances es muy fácil ponerse en funcionamiento sin tener que aprovisionar máquinas virtuales ni recurrir a un servicio de nivel superior. Azure Container Instances proporciona facturación por segundo en función de la capacidad que necesita; una opción atractiva para las cargas de trabajo transitorias, como la realización de una compilación.

Aprenderá a:
> [!div class="checklist"]
> * Instalar y configurar un servidor de Jenkins en Azure
> * Instalar y configurar el complemento de Azure Container Agents para Jenkins
> * Usar Azure Container Instances para compilar la [aplicación de ejemplo Spring PetClinic](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de Azure**: para obtener información acerca de las opciones de compra de Azure, consulte [Instrucciones para contratar Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

- **CLI de Azure 2.0 o Azure Cloud Shell**: instale uno de los siguientes productos para introducir los comandos de Azure:

    - [CLI de Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest): le permite ejecutar comandos de Azure desde una ventana Comandos o de terminal.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md): experiencia de shell basada en explorador. Cloud Shell permite el acceso a una experiencia de línea de comandos basada en explorador compilada con las tareas de administración de Azure en mente.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Instalar un servidor de Jenkins en Azure con la imagen de Marketplace para Jenkins

Jenkins admite un modelo en el cual el servidor de Jenkins delega el trabajo a uno o más agentes para permitir que una única instalación de Jenkins pueda hospedar un gran número de proyectos o proporcionar diferentes entornos necesarios para realizar compilaciones o pruebas. Los pasos descritos en esta sección le ayudarán a instalar y configurar un servidor de Jenkins en Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Conectarse al servidor de Jenkins que se ejecuta en Azure

Cuando haya instalado Jenkins en Azure, deberá conectarse a Jenkins. Los siguientes pasos le guiarán para configurar una conexión SSH a la máquina virtual de Jenkins que se ejecuta en Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Actualizar el DNS de Jenkins

Jenkins necesita conocer su propia dirección URL a la hora de crear vínculos que apuntan a sí mismo. Por ejemplo, la dirección URL debe utilizarse cuando Jenkins envía mensajes de correo electrónico con vínculos directos a resultados de la compilación. 

En esta sección se ofrece información detallada acerca de cómo configurar la URL de Jenkins.

1. En el explorador, navegue al panel de Jenkins en `http://localhost:8080`.

1. Seleccione **Manage Jenkins** (Administración de Jenkins).

    ![Administrar las opciones de Jenkins en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Seleccione **Configurar el sistema**.

    ![Administrar las opciones de los complementos de Jenkins en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. En **Jenkins Location** (Ubicación de Jenkins), escriba la dirección URL del servidor de Jenkins.

1. Seleccione **Guardar**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Actualizar Jenkins para permitir el protocolo Java Network Launch Protocol (JNLP)

El agente Jenkins se conecta con el servidor de Jenkins a través del protocolo Java Network Launch Protocol (JNLP). En esta sección se explica cómo especificar un puerto para los agentes JNLP que se usará para comunicarse con el servidor de Jenkins.

1. En el panel de Jenkins, seleccione **Manage Jenkins**(Administrar Jenkins).

    ![Administrar las opciones de Jenkins en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Seleccione **Configure Global Security** (Configurar seguridad global).

    ![Configurar la seguridad global en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. En **Agentes**, seleccione **Fijo** y escriba un puerto. En la captura de pantalla se muestra el valor de puerto 12345 como ejemplo. Debe especificar un puerto que tenga sentido en su entorno.

    ![Actualizar la configuración de seguridad global de Jenkins para permitir el protocolo JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Seleccione **Guardar**.

1. Si usa la CLI de Azure 2.0 o Cloud Shell, escriba el comando siguiente para crear una regla de entrada para el grupo de seguridad de red de Jenkins:

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Crear y agregar una entidad de servicio de Azure a las credenciales de Jenkins

Se necesita una entidad de servicio de Azure para implementar en Azure. Los pasos siguientes le ayudarán a crear una entidad de servicio (si todavía no tiene una) y a actualizar Jenkins con dicha entidad de servicio.

1. Si ya tiene una entidad de servicio (y conoce su identificador de suscripción, el inquilino, el identificador de aplicación y la contraseña), puede omitir este paso. Si necesita crear una entidad de seguridad, consulte el artículo [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) (Crear una entidad de servicio con la CLI de Azure 2.0). Al crear la entidad de seguridad, tome nota de los valores para el identificador de suscripción, el suscriptor, el identificador de aplicación y la contraseña.

1. Seleccione **Credenciales**.

    ![Administrar la opción de credenciales en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. En **Credenciales**, seleccione **Sistema**.

    ![Administrar la opción de credenciales del sistema en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Seleccione **Global credentials(unrestricted)** (Credenciales globales (sin restricción)).

    ![Administrar la opción de credenciales del sistema global en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Seleccione **Adding some credentials** (Agregar algunas credenciales).

    ![Agregar credenciales en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. En la lista desplegable **Tipo**, seleccione **Entidad de servicio de Microsoft Azure** para que la página muestre los campos específicos para agregar una entidad de servicio. A continuación, proporcione los valores solicitados como sigue:

    - **Ámbito**: seleccione una opción en **Global (Jenkins, nodes, items, all child items, etc.)** (Global (Jenkins, nodos, elementos, todos los elementos secundarios, etc.)).
    - **Id. de suscripción**: use el identificador de suscripción de Azure que especificó al ejecutar `az account set`.
    - **Id. de cliente**: use el valor `appId` que devolvió `az ad sp create-for-rbac`.
    - **Secreto de cliente**: use el valor `password` que devolvió `az ad sp create-for-rbac`.
    - **Id. de inquilino**: use el valor `tenant` que devolvió `az ad sp create-for-rbac`.
    - **Entorno de Azure**: seleccione `Azure`.
    - **ID**: escriba `myTestSp`. Este valor se volverá a usar más adelante en este tutorial.
    - **Descripción**: (opcional) escriba un valor de descripción para esta entidad de seguridad.

    ![Especificar propiedades nuevas de la entidad de servicio en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Cuando haya terminado de introducir la información que define la entidad de servicio, también puede seleccionar **Verify Service Principal** (Comprobar entidad de servicio) para asegurarse de que todo funciona correctamente. Si la entidad de servicio se definió correctamente, verá un mensaje que indica que la entidad de servicio de Microsoft Azure se comprobó correctamente en el campo **Descripción**.

1. Cuando haya terminado, seleccione **Aceptar** para agregar la entidad de servicio a Jenkins. El panel de Jenkins muestra la entidad de servicio que acaba de agregarse en la página **Credenciales globales**.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Crear un grupo de recursos de Azure para Azure Container Instances

Azure Container Instances se debe colocar en un grupo de recursos de Azure. Un grupo de recursos de Azure es un contenedor que almacena los recursos relacionados con una solución de Azure.

Si usa la CLI de Azure 2.0 o Cloud Shell, escriba el siguiente comando para crear un grupo de recursos denominado `JenkinsAciResourceGroup` en la ubicación `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

Cuando termine, el comando `az group create` muestra resultados similares al ejemplo siguiente:

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Instalar el complemento de Azure Container Agents para Jenkins

Si ya ha instalado el complemento de Azure Container Agents, puede omitir esta sección.

Una vez creado el grupo de recursos de Azure para el agente Jenkins, los pasos siguientes muestran cómo instalar el complemento de Azure Container Agents:

1. En el panel de Jenkins, seleccione **Manage Jenkins**(Administrar Jenkins).

    ![Administrar las opciones de Jenkins en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Seleccione **Administrar complementos**.

    ![Administrar las opciones de los complementos de Jenkins en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Seleccione **Disponible**.

    ![Ver las opciones de los complementos de Jenkins disponibles en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Escriba `Azure Container Agents` en el cuadro de texto **Filtro**. (La lista se filtra a medida que escribe el texto.)

    ![Filtrar los complementos de Jenkins disponibles en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Seleccione la casilla de verificación al lado del complemento de **Azure Container Agents** y una de las opciones de instalación. Para propósitos de esta demostración, se ha seleccionado la opción **Install without restart** (Instalar sin reiniciar).

    ![Instalar los complementos de Azure Container Agents desde el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  Después de seleccionar la opción para instalar los complementos deseados, el panel de Jenkins muestra una página que detalla el estado de lo que se está instalando.

    ![Estado de la instalación de los complementos de Azure Container Agents desde el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Para volver a la página principal del panel de Jenkins, seleccione **Go back to the top page** (Volver a la primera página).

## <a name="configure-the-azure-container-agents-plugin"></a>Configuración del complemento de Azure Container Agents

Una vez instalado el complemento de Azure Container Agents, esta sección le guía a través de la configuración del mismo en el panel de Jenkins.

1. En el panel de Jenkins, seleccione **Manage Jenkins**(Administrar Jenkins).

    ![Administrar las opciones de Jenkins en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Seleccione **Configurar el sistema**.

    ![Administrar las opciones de los complementos de Jenkins en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Busque la sección **Nube** en la parte inferior de la página y, en la lista desplegable **Add a new cloud** (Agregar una nube nueva), seleccione **Azure Container Instance**.

    ![Agregar un nuevo proveedor de nube desde el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. En la sección **Azure Container Instance**, especifique los valores siguientes:

    - **Nombre de nube**: (opcional ya que se genera un nombre automáticamente como valor predeterminado.) Especifique un nombre para esta instancia. 
    - **Credencial de Azure**: seleccione la flecha desplegable y, después, seleccione la entrada `myTestSp` que identifica la entidad de servicio de Azure que creó anteriormente.
    - **Grupo de recursos**: seleccione la flecha desplegable y, después, seleccione la entrada `JenkinsAciResourceGroup` que identifica el grupo de recursos de Azure Container Instance que creó anteriormente.

    ![Definir las propiedades de Azure Container Instance](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Seleccione la flecha desplegable **Add Container Template** (Agregar plantilla de contenedor) y, después, seleccione **Aci Container Template** (Plantilla del contenedor de ACI).

1. En la sección **Aci container Template** (Plantilla del contenedor de ACI), especifique los valores siguientes:

    - **Nombre**: escriba `ACI-container`.
    - **Etiquetas**: escriba `ACI-container`.
    - **Imagen de Docker**: escriba `cloudbees/jnlp-slave-with-java-build-tools`.

    ![Definir las propiedades de la imagen de Azure Container Instance](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Seleccione **Advanced** (Avanzadas).

1. Seleccione la flecha desplegable **Retention Strategy** (Estrategia de retención) y, después, seleccione **Container Idle Retention Strategy** (Estrategia de retención inactiva del contenedor). Al seleccionar esta opción, Jenkins mantiene el agente activo hasta que no se ejecute ningún trabajo nuevo y haya transcurrido el tiempo de inactividad especificado.

    ![Definir la estrategia de retención de Azure Container Instance](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Seleccione **Guardar**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Crear el trabajo de la aplicación Spring PetClinic en Jenkins

Los pasos siguientes le ayudarán a crear un trabajo de Jenkins, como un proyecto de estilo libre, para compilar la aplicación Spring PetClinic.

1. En el panel de Jenkins, haga clic en **New Item**(Nuevo elemento).

    ![Nueva opción del menú de elementos en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Escriba `myPetClinicProject` para el nombre del elemento, seleccione **Freestyle project** (Proyecto de estilo libre).

    ![Nuevo proyecto de estilo libre en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Seleccione **Aceptar**.

1. En la pestaña **General**, especifique los valores siguientes:

    - **Restrict where project can be run** (Restringir donde puede ejecutarse este proyecto): seleccione esta opción.
    - **Expresión de etiqueta**: escriba `ACI-container`. Al salir del campo, se muestra un mensaje que confirma que la etiqueta la proporciona la configuración de la nube creada en el paso anterior.

    ![Configuración general para un nuevo proyecto de estilo libre en el panel de Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Seleccione la pestaña **Administración de código fuente** y especifique los valores siguientes:

    - **Administración de código fuente**: seleccione **Git**.
    - **Dirección URL del repositorio**: escriba la siguiente dirección URL para el repositorio de GitHub de la aplicación de ejemplo Spring PetClinic: `https://github.com/spring-projects/spring-petclinic.git`.

1. Seleccione la pestaña **Compilar** y realice las tareas siguientes:

    - Seleccione la flecha desplegable **Agregar paso de compilación** y seleccione **Invoke top-level Maven targets** (Invocar destinos de Maven de nivel superior).

    - En **Objetivos**, escriba `package`.

1. Seleccione **Guardar** para guardar la definición del nuevo proyecto.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Compilar el trabajo de la aplicación Spring PetClinic en Jenkins

Es el momento de compilar su proyecto. En esta sección se explica cómo compilar un proyecto desde el panel de Jenkins.

1. En el panel de Jenkins, seleccione `myPetClinicProject`.

    ![Seleccione el proyecto que quiere compilar en el panel de Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Seleccione **Compilar ahora**. 

    ![Compile el proyecto desde el panel de Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Cuando se inicia una compilación en Jenkins, esta se pone en cola. En el caso de Azure Container Agent, la compilación no se puede ejecutar hasta que se inicia Azure Container Agent y se pone en línea. Hasta entonces, verá un mensaje que indica el nombre del agente y el hecho de que la compilación está pendiente. (Este proceso tarda aproximadamente cinco minutos, pero solo es necesario realizarlo la primera vez que se usa el agente para una compilación. Las compilaciones posteriores son mucho más rápidas porque el agente ya estará en línea).

    ![La compilación se pone en cola hasta que el agente se crea y se pone en línea.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    Una vez que se inicia la compilación, una barra de progreso en forma de poste de barbero indica que la compilación se está ejecutando:

    ![La compilación se estará ejecutando cuando vea la barra de progreso en forma de poste de barbero.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Cuando desaparezca la barra de progreso en forma de poste de barbero, seleccione la flecha situada junto al número de compilación. En el menú contextual, seleccione **Salida de la consola**.

    ![Haga clic en el elemento de menú Registro de consola para ver la información de compilación.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Información del registro de consola que emite el proceso de compilación. Para ver toda la información de la compilación (incluida información acerca de la compilación que se realiza de forma remota en el agente de Azure que creó), seleccione **Registro completo**.

    ![Haga clic en el vínculo de Registro completo para ver información más detallada acerca de la compilación.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    El registro completo muestra más información detallada de la compilación:

    ![El registro completo muestra información detallada de la compilación.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Para ver la disposición de la compilación, desplácese hasta el final del registro.

    ![La disposición de la compilación se muestra en la parte inferior del registro de compilación.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Limpieza de los recursos de Azure

En este tutorial, creó recursos dentro de dos grupos de recursos de Azure: 
    - `JenkinsResourceGroup`: contiene los recursos de Azure para el servidor de Jenkins.
    - `JenkinsAciResourceGroup`: contiene los recursos de Azure para el agente Jenkins.
    
Si ya no necesita usar cualquiera de los recursos de un grupo de recursos de Azure, puede eliminar el grupo de recursos con el comando `az group delete` como se indica a continuación (reemplazando el marcador de posición &lt;resourceGroup> por el nombre del grupo de recursos que quiere eliminar):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>pasos siguientes
> [!div class="nextstepaction"]
> [Visite el concentrador Jenkins en Azure para ver los últimos artículos y ejemplos](https://docs.microsoft.com/azure/jenkins/)