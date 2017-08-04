---
title: "Use los agentes de máquina virtual de Azure para la integración continua con Jenkins."
description: "Agentes de máquina virtual de Azure como servidores subordinados de Jenkins."
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: es-es
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Use los agentes de máquina virtual de Azure para la integración continua con Jenkins.

Esta guía de inicio rápido muestra cómo usar el complemento de agentes de máquina virtual de Azure para Jenkins para crear un agente de Linux (Ubuntu) a petición en Azure.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido:

* Si no dispone de un servidor maestro de Jenkins, puede empezar con la [plantilla de solución](install-jenkins-solution-template.md) 
* Consulte [Creación de una entidad de servicio de Azure con la CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) si no dispone de una entidad de servicio de Azure.

## <a name="install-azure-vm-agents-plugin"></a>Instalación del complemento de agentes de máquina virtual de Azure

Si empieza a partir de la [plantilla de solución](install-jenkins-solution-template.md), el complemento de agentes de máquina virtual de Azure se instala en el servidor maestro de Jenkins.

En caso contrario, instale el complemento **Agentes de máquina virtual de Azure** desde dentro del panel de Jenkins.

## <a name="configure-the-plugin"></a>Configuración del complemento

* En el panel de Jenkins, haga clic en **Manage Jenkins (Administrar Jenkins) -> Configure System (Configurar sistema) ->**. Desplácese hasta la parte inferior de la página y busque la sección con la lista desplegable **Add new cloud** (Agregar una nube nueva). En el menú, seleccione **Microsoft Azure VM Agents** (Agentes de máquina virtual de Microsoft Azure)
* Seleccione una cuenta existente de la lista desplegable de credenciales de Azure.  Para agregar una nueva **entidad de servicio de Microsoft Azure,** escriba los siguientes valores: Id. de suscripción, Id. de cliente, secreto de cliente y punto de conexión de token de OAuth 2.0.

![Credenciales de Azure](./media/jenkins-azure-vm-agents/service-principal.png)

* Haga clic en **Verify configuration** (Comprobar configuración) para asegurarse de que la configuración del perfil es correcta.
* Guarde la configuración y continúe con el paso siguiente.

## <a name="template-configuration"></a>Configuración de plantilla

### <a name="general-configuration"></a>Configuración general
A continuación, configure una plantilla para utilizarla para definir a un agente de máquina virtual de Azure. 

* Haga clic en **Add** (Agregar) para agregar una plantilla. 
* Proporcione un nombre para la nueva plantilla. 
* Para la etiqueta, escriba "ubuntu". Esta etiqueta se usa durante la configuración del trabajo.
* Seleccione la región deseada en el control de cuadro combinado.
* Seleccione el tamaño de máquina virtual deseado.
* Especifique el nombre de cuenta de Azure Storage o déjelo en blanco y use el nombre predeterminado "jenkinsarmst".
* Especifique el tiempo de retención en minutos. Esta opción define el número de minutos que Jenkins debe esperar antes de eliminar automáticamente un agente inactivo. También puede especificar 0 si no desea que los agentes inactivos se eliminen automáticamente.

![Configuración general](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>Configuración de imagen

Para crear un agente de Linux (Ubuntu), seleccione **Image reference** (Referencia de imagen) y utilice la configuración siguiente como ejemplo. Vaya a [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) para obtener las imágenes más recientes compatibles con Azure.

* Image Publisher (Publicador de imagen): Canonical
* Image Offer (Oferta de imagen): UbuntuServer
* Image Sku (SKU de imagen): 14.04.5-LTS
* Image version (Versión de imagen): la más reciente
* OS Type (Tipo de sistema operativo): Linux
* Launch method (Método de inicio): SSH
* Proporcione unas credenciales de administrador.
* Para el script de inicialización de la máquina virtual, escriba:
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Configuración de imagen](./media/jenkins-azure-vm-agents/image-config.png)

* Haga clic en **Verify Template** (Comprobar plantilla) para comprobar la configuración.
* Haga clic en **Guardar**.

## <a name="create-a-job-in-jenkins"></a>Creación de un trabajo en Jenkins

* En el panel de Jenkins, haga clic en **New Item**(Nuevo elemento). 
* Escriba un nombre y seleccione **Freestyle project** (Proyecto de estilo libre) y haga clic en **OK** (Aceptar).
* En la pestaña **General**, seleccione "Restrict where project can be run" (Restringir donde se pueda ejecutar el proyecto) y escriba "ubuntu" en la expresión de etiqueta. Ahora verá "ubuntu" en la lista desplegable.
* Haga clic en **Guardar**.

![Configurar un proyecto](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>Compilación de un nuevo proyecto

* Vuelva al panel de Jenkins.
* Haga clic con el botón derecho en el nuevo trabajo que creó y, a continuación, haga clic en **Build now** (Compilar ahora). Se inicia una compilación. 
* Una vez completada la compilación, vaya a **Console output** (Salida de consola). Allí puede ver que la compilación se realizó de forma remota en Azure.

![Salida de consola](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>Referencia

* Vídeo de Azure Friday: [Continuous Integration with Jenkins using Azure VM agents](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents) (Integración continua con Jenkins mediante agentes de máquina virtual de Azure)
* Opciones de configuración e información de soporte técnico: [Azure VM Agent Jenkins Plugin Wiki](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) (Wiki del complemento Jenkins de agente de máquina virtual de Azure) 


