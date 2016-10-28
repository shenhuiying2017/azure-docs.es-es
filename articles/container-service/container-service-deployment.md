<properties
   pageTitle="Implementación de un clúster del servicio Contenedor de Azure | Microsoft Azure"
   description="Implementación de un clúster del servicio Contenedor de Azure mediante el Portal de Azure, la CLI de Azure o PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# Implementación de un clúster del servicio Contenedor de Azure

El servicio Contenedor de Azure proporciona una rápida implementación de agrupación en clústeres de contenedor de código abierto y soluciones de orquestación populares. El servicio Contenedor de Azure permite implementar clústeres de DC/OS y Docker Swarm con plantillas de Azure Resource Manager o con el Portal de Azure. Estos clústeres se implementan mediante Conjuntos de escala de máquinas virtuales de Azure y sacan provecho de las posibilidades que ofrecen las funciones de red y almacenamiento de Azure. Para acceder a Azure Container Service, se necesita una suscripción de Azure. Si no tiene una, puede registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento le guiará por la implementación de un clúster del servicio Contenedor de Azure mediante el [Portal de Azure](#creating-a-service-using-the-azure-portal), la [interfaz de línea de comandos (CLI) de Azure](#creating-a-service-using-the-azure-cli) y el [módulo Azure PowerShell](#creating-a-service-using-powershell).

## Creación de un servicio con el Portal de Azure

Inicie sesión en el Portal de Azure, seleccione **Nuevo** y busque en Azure Marketplace el **servicio Contenedor de Azure**.

![Creación de una implementación 1](media/acs-portal1.png) <br />

Seleccione **Servicio Contenedor de Azure** y haga clic en **Crear**.

![Creación de una implementación 2](media/acs-portal2.png) <br />

Escriba la siguiente información:

- **Nombre de usuario**: este es el nombre de usuario que se utilizará para una cuenta en cada una de las máquinas virtuales y los conjuntos de escalado de máquinas virtuales en el clúster del servicio Contenedor de Azure.
- **Suscripción**: seleccione una suscripción de Azure.
- **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo.
- **Ubicación**: seleccione una región de Azure para la implementación del servicio Contenedor de Azure.
- **Clave pública SSH**: agregue la clave pública que se utilizará para la autenticación en las máquinas virtuales del servicio Contenedor de Azure. Es importante que la clave no tenga saltos de línea y que incluya el prefijo 'ssh-rsa' y el sufijo 'username@domain'. Debería ser similar a lo siguiente: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. Para obtener instrucciones acerca de cómo crear claves de Secure Shell (SSH), consulte los artículos sobre [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) y [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/).

Haga clic en **Aceptar** cuando esté listo para continuar.

![Creación de una implementación 3](media/acs-portal3.png) <br />

Seleccione un tipo de orquestación. Las opciones son:

- **DC/OS**: implementa un clúster de DC/OS.
- **Swarm**: implementa un clúster de Docker Swarm.

Haga clic en **Aceptar** cuando esté listo para continuar.

![Creación de una implementación 4](media/acs-portal4.png) <br />

Escriba la siguiente información:

- **Master count** (Número de patrones): el número de patrones en el clúster.
- **Agent count** (Número de agentes): en el caso de Docker Swarm, será el número inicial de agentes en el conjunto de escalas del agente. En el caso de DC/OS, se trata del número inicial de agentes en un conjunto de escalas privadas. Además, se crea un conjunto de escalas públicas que contiene un número predeterminado de agentes. El número de agentes en este conjunto de escalas públicas se determina por el número de patrones que se han creado en el clúster, un agente público para un patrón y dos agentes públicos para tres o cinco patrones.
- **Agent virtual machine size** (Tamaño de máquina virtual de agente): el tamaño de las máquinas virtuales de los agentes.
- **DNS prefix** (Prefijo de DNS): un nombre único en el mundo que se utilizará para prefijar las partes clave de los nombres de dominio completos del servicio.

Haga clic en **Aceptar** cuando esté listo para continuar.

![Creación de una implementación 5](media/acs-portal5.png) <br />

Haga clic en **Aceptar** una vez finalizada la validación del servicio.

![Creación de una implementación 6](media/acs-portal6.png) <br />

Haga clic en **Crear** para iniciar el proceso de implementación.

![Creación de una implementación 7](media/acs-portal7.png) <br />

Si ha elegido anclar la implementación al Portal de Azure, se verá el estado de la implementación.

![Creación de una implementación 8](media/acs-portal8.png) <br />

Una vez finalizada la implementación, el clúster del servicio Contenedor de Azure estará listo para su uso.

## Creación de un servicio con la CLI de Azure

Para crear una instancia de Azure Container Service mediante la línea de comandos, necesita una suscripción de Azure. Si no tiene ninguna, puede registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). También es preciso que la CLI de Azure esté [instalada](../xplat-cli-install.md) y [configurada](../xplat-cli-connect.md).

Seleccione una de las siguientes plantillas de GitHub para implementar un clúster de DC/OS o Docker Swarm. Observe que ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.

* [Plantilla de DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Plantilla Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

A continuación, asegúrese de que CLI de Azure se ha conectado a una suscripción de Azure. Para ello, puede usar el siguiente comando:

```bash
azure account show
```
Si no se devuelve una cuenta de Azure, use el comando siguiente para iniciar la sesión de CLI en Azure.

```bash
azure login -u user@domain.com
```

A continuación, configure las herramientas de la CLI de Azure para usar Azure Resource Manager.

```bash
azure config mode arm
```

Cree un clúster del grupo de recursos de Azure y el servicio Contenedor con el comando siguiente, en el que:

- **RESOURCE\_GROUP** es el nombre del grupo de recursos que desea usar para este servicio.
- **LOCATION** es la región de Azure en la que se creará la implementación del grupo de recursos y de Azure Container Service.
- **TEMPLATE\_URI** es la ubicación del archivo de implementación. Tenga en cuenta que este tiene que ser el archivo Raw, no un puntero a la interfaz de usuario de GitHub. Para encontrar esta dirección URL seleccione el archivo azuredeploy.json en GitHub y haga clic en el botón **Raw** (Sin formato).

> [AZURE.NOTE] Al ejecutar este comando, el shell solicitará los valores de los parámetros de implementación.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### Suministro de los parámetros de plantilla

Esta versión del comando requiere que el usuario defina los parámetros de forma interactiva. Si desea proporcionar parámetros, como una cadena con formato JSON, puede hacerlo con el modificador `-p`. Por ejemplo:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

También puede proporcionar un archivo de parámetros con formato JSON mediante el modificador `-e`:

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Para ver un archivo de parámetros de ejemplo llamado `azuredeploy.parameters.json`, búsquelo con las plantillas de Azure Container Service en GitHub.

## Creación de un servicio con PowerShell

También puede implementar un clúster del servicio Contenedor de Azure con PowerShell. Este documento se basa en la versión 1.0 del [módulo de Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Seleccione una de las siguientes plantillas para implementar un clúster de DC/OS o Docker Swarm. Observe que ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.

* [Plantilla de DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Plantilla Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de crear un clúster en su suscripción de Azure, compruebe que la sesión de PowerShell se ha iniciado en Azure. Para ello, puede usar el comando `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Si tiene que iniciar sesión en Azure, use el comando `Login-AzureRMAccount`:

```powershell
Login-AzureRmAccount
```

Si va a implementar en un nuevo grupo de recursos, primero tiene que crear el grupo de recursos. Para crear un nuevo grupo de recursos, use el comando `New-AzureRmResourceGroup` y especifique un nombre y una región de destino para dicho grupo:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Después de crear un grupo de recursos, puede crear el clúster con el comando siguiente. El identificador URI de la plantilla deseada se especificará para el parámetro `-TemplateUri`. Al ejecutar este comando, PowerShell solicitará los valores de los parámetros de implementación.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### Suministro de los parámetros de plantilla

Si está familiarizado con PowerShell, sabe que puede recorrer los parámetros disponibles para un cmdlet; basta con que escriba un signo menos (-) y luego presione la tecla TAB. Esta misma funcionalidad también sirve para los parámetros que se definen en la plantilla. En cuanto escribe el nombre de la plantilla, el cmdlet captura la plantilla, analiza los parámetros y agrega los parámetros de la plantilla al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla. Además, si olvida el valor de algún parámetro necesario, PowerShell le pide dicho valor.

A continuación se muestra el comando completo con parámetros incluidos. Puede proporcionar sus propios valores para los nombres de los recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Pasos siguientes

Ahora que tiene un clúster funcionando, consulte los siguientes documentos para obtener información detallada sobre conexión y administración:

- [Conexión a un clúster del servicio Contenedor de Azure](container-service-connect.md)
- [Administración de contenedores con la API de REST](container-service-mesos-marathon-rest.md)
- [Administración de contenedores con Docker Swarm](container-service-docker-swarm.md)

<!---HONumber=AcomDC_0914_2016-->