---
title: "Implementación de un clúster de contenedores de Docker en Azure | Microsoft Docs"
description: "Implementación de un clúster de Azure Container Service mediante Azure Portal o la plantilla de Azure Resource Manager."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>Implementación de un clúster del servicio Contenedor de Azure



El servicio Contenedor de Azure proporciona una rápida implementación de agrupación en clústeres de contenedor de código abierto y soluciones de orquestación populares. Este documento es una guía paso a paso para implementar un clúster de Azure Container Service mediante Azure Portal o una plantilla de inicio rápido de Azure Resource Manager. 

> [!NOTE]
> La compatibilidad con Kubernetes en Azure Container Service está actualmente en versión preliminar.

También se puede implementar un clúster de Azure Container Service mediante la [CLI de Azure 2.0 (versión preliminar)](container-service-create-acs-cluster-cli.md) o las API de Azure Container Service.



## <a name="prerequisites"></a>Requisitos previos

* **Suscripción a Azure:** si no tiene una, suscríbase para una obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

* **Clave pública SSH**: si la implementación se realiza a través del portal o de una de las plantillas de inicio rápido de Azure, es preciso especificar la clave pública para la autenticación con máquinas virtuales de Azure Container Service. Para crear claves SSH (Secure Shell), consulte las instrucciones de [OS X y Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md), o de [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **Identificador y secreto de cliente de entidad de servicio** (solo Kubernetes): para más información e instrucciones para crear una entidad de servicio, consulte [About the Azure Active Directory service principal for a Kubernetes cluster in Azure Container Service](container-service-kubernetes-service-principal.md) (Acerca de la entidad de servicio de Azure Active Directory para un clúster de Kubernetes de Azure Container Service).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Creación de un clúster mediante Azure Portal
1. Inicie sesión en Azure Portal, seleccione **Nuevo**, y busque **Azure Container Service** en Azure Marketplace.

    ![Azure Container Service en Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Seleccione **Azure Container Service** y haga clic en **Crear**.

    ![Crear un servicio de contenedor](media/container-service-deployment/acs-portal2.png)  <br />

3. Escriba la siguiente información:

    * **Nombre de usuario**: el nombre de usuario de una cuenta en cada una de las máquinas virtuales y los conjuntos de escalado de máquinas virtuales del clúster de Azure Container Service.
    * **Suscripción**: seleccione una suscripción de Azure.
    * **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo.
    * **Ubicación**: seleccione una región de Azure para la implementación del servicio Contenedor de Azure.
    * **Clave pública SSH**: agregue la clave pública que se va a usar para la autenticación en las máquinas virtuales de Azure Container Service. Es importante que esta clave no tenga saltos de línea y que incluya el prefijo `ssh-rsa`. El sufijo `username@domain` es opcional. La clave debería tener un formato similar al siguiente: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 

4. Haga clic en **Aceptar** cuando esté listo para continuar.

    ![Configuración básica](media/container-service-deployment/acs-portal3.png)  <br />

5. Seleccione un tipo de orquestación. Las opciones son:

  * **DC/OS**: implementa un clúster de DC/OS.
  * **Swarm**: implementa un clúster de Docker Swarm.
  * **Kubernetes**: implementa un clúster de Kubernetes


6. Haga clic en **Aceptar** cuando esté listo para continuar.

    ![Elegir un orquestador](media/container-service-deployment/acs-portal4-new.png)  <br />

7. Si se selecciona **Kubernetes** en la lista desplegable, será preciso escribir tanto el identificador como el secreto del cliente de la entidad de servicio. Para más información, consulte [About the Azure Active Directory service principal for a Kubernetes cluster in Azure Container Service](container-service-kubernetes-service-principal.md) (Acerca de la entidad de servicio de Azure Active Directory para un clúster de Kubernetes de Azure Container Service).

    ![Escriba una entidad de servicio para Kubernetes](media/container-service-deployment/acs-portal10.png)  <br />

7. En la hoja de configuración de **Azure Container Service**, escriba la siguiente información:

    * **Master count**(Número de patrones): el número de patrones en el clúster. Si se selecciona Kubernetes, el valor predeterminado del número de patrones se establece en 1.
    * **Número de agentes**: en el caso de Docker Swarm y Kubernetes, este valor es el número inicial de agentes del conjunto de escalado de agentes. En el caso de DC/OS, se trata del número inicial de agentes de un conjunto de escalado privado. Además, se crea un conjunto de escalado público para DC/OS, que contiene un número predeterminado de agentes. El número de agentes de este conjunto de escalado público lo determina el número de patrones que se han creado en el clúster: un agente público para un patrón y dos agentes públicos para tres o cinco patrones.
    * **Agent virtual machine size**(Tamaño de máquina virtual de agente): el tamaño de las máquinas virtuales de los agentes.
    * **DNS prefix**(Prefijo de DNS): un nombre único en el mundo que se utiliza para prefijar las partes clave de los nombres de dominio completos del servicio.
    * **VM diagnostics** (Diagnóstico de máquina virtual): en el caso de algunos orquestadores, puede elegir habilitar los diagnósticos de máquina virtual.

8. Haga clic en **Aceptar** cuando esté listo para continuar.

    ![Configuración de Container Service](media/container-service-deployment/acs-portal5.png)  <br />

9. Haga clic en **Aceptar** una vez finalizada la validación del servicio.

    ![Validación](media/container-service-deployment/acs-portal6.png)  <br />

10. Revise los términos. Para iniciar el proceso de implementación, haga clic en **Purchase** (Comprar).

    ![Purchase](media/container-service-deployment/acs-portal7.png)  <br />

    Si ha elegido anclar la implementación al Portal de Azure, se verá el estado de la implementación.

    ![Estado de implementación](media/container-service-deployment/acs-portal8.png)  <br />

La implementación tarda varios minutos en completarse. Después, el clúster de Azure Container Service estará listo para su uso.



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Creación de un clúster mediante una plantilla de inicio rápido
Las plantillas de inicio rápido de Azure permiten implementar un clúster en Azure Container Service. Las plantillas de inicio rápido que se proporcionan se pueden modificar para que incluyan una configuración de Azure adicional o avanzada. Para crear un clúster de Azure Container Service mediante una plantilla de inicio rápido de Azure, se necesita una suscripción a Azure. Si no dispone de ninguna, suscríbase para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Siga estos pasos para implementar un clúster mediante una plantilla y la CLI de Azure 2.0 (versión preliminar) (consulte las [instrucciones de instalación y configuración ](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> En un sistema Windows, puede utilizar pasos similares para implementar una plantilla mediante Azure PowerShell. En esta misma sección encontrará los pasos necesarios para hacerlo. Las plantillas también se pueden implementar a través del [portal](../azure-resource-manager/resource-group-template-deploy-portal.md) o mediante otros métodos.

1. Seleccione una de las siguientes plantillas de GitHub para implementar un clúster de DC/OS, Docker Swarm o Kubernetes. Tenga en cuenta que las plantillas de DC/OS y Swarm son iguales, a excepción de la selección del orquestador predeterminado.

    * [Plantilla de DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Plantilla Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Plantilla de Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Inicie sesión en su cuenta de Azure (`az login`) y asegúrese de que la CLI de Azure está conectada a su suscripción a Azure. Para ver la suscripción predeterminada, use el siguiente comando:

    ```azurecli
    az account show
    ```
    
    Si tiene más de una suscripción y necesita establecer otra suscripción predeterminada, ejecute `az account set --subscription` y especifique el nombre o identificador de la suscripción.

3. Como procedimiento recomendado, utilice un grupo de recursos nuevo para la implementación. Para crear un grupo de recursos nuevo, use el comando `az group create` y especifique el nombre y la ubicación del grupo de recursos: 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Cree un archivo JSON que contenga los parámetros de plantilla necesarios. Descargue el archivo de parámetros denominado `azuredeploy.parameters.json` que acompaña a la plantilla de Azure Container Service `azuredeploy.json` en GitHub. Especifique los valores de parámetro necesarios para el clúster. 

    Por ejemplo, para usar la [plantilla de DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos), especifique los valores de parámetro para `dnsNamePrefix` y `sshRSAPublicKey`. Vea las descripciones de `azuredeploy.json` y opciones para otros parámetros.  
 

5. Para crear un clúster de Container Service, use el archivo de parámetros de implementación con el comando siguiente, donde:

    * **RESOURCE_GROUP** es el nombre del grupo de recursos que creó en el paso anterior.
    * **DEPLOYMENT_NAME** (opcional) es el nombre que se asigna a la implementación.
    * **TEMPLATE_URI** es la ubicación del archivo de implementación `azuredeploy.json`. Este identificador URI tiene que ser el archivo sin formato, no un puntero a la interfaz de usuario de GitHub. Para buscar el identificador URI, seleccione el archivo `azuredeploy.json` en GitHub y haga clic en el botón **Raw** (Sin formato).  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    Los parámetros también se pueden especificar en forma de cadena con formato JSON en la línea de comandos. Use un comando similar al siguiente:

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > La implementación tarda varios minutos en completarse.
    > 

### <a name="equivalent-powershell-commands"></a>Comandos de PowerShell equivalentes
Las plantillas de clúster de Azure Container Service también se pueden implementar con PowerShell. Este documento se basa en la versión 1.0 del [módulo de Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

1. Seleccione una de las siguientes plantillas para implementar un clúster de DC/OS, Docker Swarm o Kubernetes. Tenga en cuenta que las plantillas de DC/OS y Swarm son iguales, a excepción de la selección del orquestador predeterminado.

    * [Plantilla de DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Plantilla Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Plantilla de Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Antes de crear un clúster en su suscripción de Azure, compruebe que la sesión de PowerShell se ha iniciado en Azure. Para ello, puede usar el comando `Get-AzureRMSubscription` :

    ```powershell
    Get-AzureRmSubscription
    ```

3. Si tiene que iniciar sesión en Azure, use el comando `Login-AzureRMAccount` :

    ```powershell
    Login-AzureRmAccount
    ```

4. Como procedimiento recomendado, utilice un grupo de recursos nuevo para la implementación. Para crear un nuevo grupo de recursos, use el comando `New-AzureRmResourceGroup` y especifique el nombre y la región de destino de dicho grupo:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Después de crear un grupo de recursos, puede crear el clúster con el comando siguiente. El identificador URI de la plantilla deseada se especificará para el parámetro `-TemplateUri` . Al ejecutar este comando, PowerShell solicita los valores de los parámetros de implementación.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Suministro de los parámetros de plantilla
Si está familiarizado con PowerShell, sabe que puede recorrer los parámetros disponibles para un cmdlet; basta con que escriba un signo menos (-) y luego presione la tecla TAB. Esta misma funcionalidad también sirve para los parámetros que se definen en la plantilla. En cuanto escribe el nombre de la plantilla, el cmdlet captura la plantilla, analiza los parámetros y agrega los parámetros de la plantilla al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla. Además, si olvida el valor de algún parámetro necesario, PowerShell le pide dicho valor.

A continuación se muestra el comando completo con parámetros incluidos. Puede proporcionar sus propios valores para los nombres de los recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene un clúster funcionando, consulte los siguientes documentos para obtener información detallada sobre conexión y administración:

* [Conexión a un clúster del servicio Contenedor de Azure](container-service-connect.md)
* [Administración de contenedores con la API de REST](container-service-mesos-marathon-rest.md)
* [Administración de contenedores con Docker Swarm](container-service-docker-swarm.md)
* [Trabajo con Azure Container Service y Kubernetes](container-service-kubernetes-walkthrough.md)



<!--HONumber=Feb17_HO1-->


