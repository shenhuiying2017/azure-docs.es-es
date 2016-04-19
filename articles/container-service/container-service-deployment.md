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
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Implementación de un clúster del servicio Contenedor de Azure

El servicio Contenedor de Azure proporciona una rápida implementación de agrupación en clústeres de contenedor de código abierto y soluciones de orquestación populares. El servicio Contenedor de Azure permite implementar clústeres de Marathon Mesos y Docker Swarm con plantillas de Azure Resource Manager o con el Portal de Azure. Estos clústeres se implementan mediante conjuntos de escalado de máquinas virtuales de Azure, y aprovechan las posibilidades que ofrecen las funciones de red y almacenamiento de Azure. Para obtener acceso al servicio Contenedor de Azure, necesita una suscripción de Azure. Si no tiene una, puede registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento le guiará por la implementación de un clúster del servicio Contenedor de Azure mediante el [Portal de Azure](#creating-a-service-using-the-azure-portal), la [interfaz de línea de comandos (CLI) de Azure](#creating-a-service-using-the-azure-cli) y el [módulo Azure PowerShell](#creating-a-service-using-powershell).

## Creación de un servicio con el Portal de Azure

Seleccione una de las siguientes plantillas de GitHub para implementar un clúster de Mesos o Docker Swarm. Observe que ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.

* [Plantilla Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Plantilla Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Al seleccionar el botón **Implementar en Azure** en cualquiera de las plantillas, se abre el Portal de Azure con un formulario de aspecto similar al siguiente:<br />

![Creación de una implementación mediante el formulario](media/create-mesos-params.png) <br />

Complete el formulario usando estas instrucciones y seleccione **Aceptar** cuando haya terminado. <br />

Campo | Descripción
----------------|-----------
DNSNAMEPREFIX | Debe ser un valor único a nivel universal. Se utilizará para crear nombres DNS para cada una de las partes claves del servicio. Consulte más información en este artículo.
AGENTCOUNT | Este es el número de máquinas virtuales que se creará en el conjunto de escalado del agente del servicio Contenedor de Azure.
AGENTVMSIZE | Es el tamaño de las máquinas virtuales del agente. Tenga cuidado y seleccione un tamaño que proporcione suficientes recursos para alojar los contenedores más grandes.
ADMINUSERNAME | Este es el nombre de usuario que se utilizará para una cuenta en cada una de las máquinas virtuales y los conjuntos de escalado de máquinas virtuales en el clúster del servicio Contenedor de Azure.
ORCHESTRATORTYPE| Este es el tipo de orquestador que usará en su clúster del servicio Contenedor de Azure.
MASTERCOUNT | Este es el número de máquinas virtuales para configurar como patrones para el clúster. Puede seleccionar 1, pero esto no proporcionará resistencia en el clúster y solo se recomienda para la realización de pruebas. Se recomienda 3 o 5 para un clúster de producción.
SSHRSAPUBLICKEY | Debe usar Shell seguro (SSH) para la autenticación en las máquinas virtuales. Es aquí donde agrega la clave pública. Es muy importante tener cuidado al pegar el valor de la clave en este cuadro. Algunos editores insertan saltos de línea en el contenido, y esto divide la clave. Compruebe que la clave no tiene saltos de línea y que incluye el prefijo 'ssh-rsa' y el sufijo 'username@domain'. Debería ser algo similar a ' ssh rsa AAAAB3Nz... SNIPPEDCONTENT... UcyupgH azureuser@linuxvm'. Si necesita crear una clave SSH, puede encontrar instrucciones para [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) y [Linux](../virtual-machines/virtual-machines-linux-ssh-from-linux.md) en el sitio de documentación de Azure.

Una vez que establecidos los valores adecuados para los parámetros, seleccione **Aceptar**. A continuación, proporcione un nombre de grupo de recursos, seleccione una región y revise y acepte los términos legales.

> [AZURE.NOTE] Durante la versión preliminar, no se realiza ningún cargo por el servicio Contenedor de Azure. Solo se realizan cargos por los procesos estándar, por ejemplo, cargos por máquinas virtuales, almacenamiento y red.

![Selección de grupo de recursos](media/resourcegroup.png)

Por último, seleccione **Crear**. Vuelva al panel. Si no desactivó la casilla **Anclar al panel** en la hoja de implementación, verá un icono animado que tendrá un aspecto similar al siguiente:

![Icono de plantilla de implementación](media/deploy.png)

Ahora relájese mientras se crea el clúster. Una vez creado el clúster, verá algunas hojas que muestran los recursos que componen el clúster del servicio Contenedor de Azure.

![Terminado](media/final.png)

## Creación de un servicio con la CLI de Azure

Para crear una instancia del servicio Contenedor de Azure mediante la línea de comandos, necesita una suscripción de Azure. Si no tiene una, puede registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). También tiene que haber instalado y configurado la CLI de Azure.

Seleccione una de las siguientes plantillas de GitHub para implementar un clúster de Mesos o Docker Swarm. Observe que ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.

* [Plantilla Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Plantilla Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

A continuación, asegúrese de que CLI de Azure se ha conectado a una suscripción de Azure. Para ello, puede usar el siguiente comando:

```bash
Azure account show
```
Si no se devuelve una cuenta de Azure, use el comando siguiente para iniciar la sesión de CLI en Azure.

```bash
azure login -u user@domain.com
```

A continuación, configure las herramientas de la CLI de Azure para usar Azure Resource Manager.

```bash
azure config mode arm
```

Si desea crear el clúster en un nuevo grupo de recursos, primero tiene que crear el grupo de recursos. Use el siguiente comando, donde `GROUP_NAME` es el nombre del grupo de recursos que desea crear, y `REGION` es la región en la que desea crear el grupo de recursos:

```bash
azure group create GROUP_NAME REGION
```

Una vez creado un grupo de recursos, puede crear el clúster con este comando, donde:

- **RESOURCE\_GROUP**: es el nombre del grupo de recursos que quiere usar para este servicio.
- **DEPLOYMENT\_NAME** es el nombre de esta implementación.
- **TEMPLATE\_URI** es la ubicación del archivo de implementación. Tenga en cuenta que este tiene que ser el archivo RAW, *no* un puntero a la interfaz de usuario de GitHub. Para encontrar esta dirección URL, seleccione el archivo azuredeploy.json en GitHub y seleccione el botón **RAW**.

> [AZURE.NOTE] Al ejecutar este comando, el shell solicitará los valores de los parámetros de implementación.

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```

### Suministro de los parámetros de plantilla

Esta versión del comando requiere que el usuario defina los parámetros de forma interactiva. Si desea proporcionar parámetros, tales como una cadena con el formato json, puede hacerlo con el modificador `-p`. Por ejemplo:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

También puede proporcionar un archivo de parámetros con formato JSON mediante el modificador `-e`:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Para ver un archivo de parámetros de ejemplo llamado `azuredeploy.parameters.json`, búsquelo con las plantillas del servicio Contenedor de Azure en GitHub.

## Creación de un servicio con PowerShell

También puede implementar un clúster del servicio Contenedor de Azure con PowerShell. Este documento se basa en la versión 1.0 y versiones posteriores del [módulo Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Seleccione una de las siguientes plantillas para implementar un clúster de Mesos o Docker Swarm. Observe que ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.

* [Plantilla Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Plantilla Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de crear un clúster en su suscripción de Azure, compruebe que la sesión de PowerShell se ha iniciado en Azure. Puede hacer esto con el comando `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Si tiene que iniciar sesión en Azure, use el comando `Login-AzureRMAccount`:

```powershell
Login-AzureRmAccount
```

Si va a implementar en un nuevo grupo de recursos, primero tiene que crear el grupo de recursos. Para crear un nuevo grupo de recursos, use el comando `New-AzureRmResourceGroup`, especificando un nombre y una región de destino para el grupo de recursos.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Después de crear un grupo de recursos, puede crear el clúster con el comando siguiente. El URI de la plantilla que desee se especificará para el parámetro `-TemplateUri`. Al ejecutar este comando, PowerShell solicitará los valores de los parámetros de implementación.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### Suministro de los parámetros de plantilla

Si está familiarizado con PowerShell, sabe que puede recorrer los parámetros disponibles para un cmdlet; basta con que escriba un signo menos (-) y luego presione la tecla TAB. Esta misma funcionalidad también sirve para los parámetros que se definen en la plantilla. En cuanto escribe el nombre de la plantilla, el cmdlet captura la plantilla, analiza los parámetros y agrega los parámetros de la plantilla al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla. Además, si olvida el valor de algún parámetro necesario, PowerShell le pide dicho valor.

A continuación se muestra el comando completo con parámetros incluidos. Puede proporcionar sus propios valores para los nombres de los recursos.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Pasos siguientes

Ahora que tiene un clúster funcionando, consulte estos artículos para obtener información detallada sobre conexión y administración.

- [Conexión a un clúster del servicio Contenedor de Azure](./container-service-connect.md)
- [Administración de contenedores con la API de REST](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0413_2016-->