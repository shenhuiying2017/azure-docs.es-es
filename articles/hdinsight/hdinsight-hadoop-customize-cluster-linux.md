---
title: Personalización de los clústeres de HDInsight mediante acciones de script | Microsoft Docs
description: Obtenga información sobre cómo agregar componentes personalizados en clústeres de HDInsight basados en Linux mediante acciones de script. Las acciones de script son scripts de prueba que se ejecutan en los nodos del clúster y se pueden usar para personalizar la configuración del clúster o para agregar servicios adicionales y utilidades como Hue, Solr o R.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: larryfr

---
# Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)
HDInsight proporciona una opción de configuración llamada **Acción de script** que invoca scripts personalizados que personalizan el clúster. Estos scripts pueden utilizarse durante la creación del clúster o en un clúster ya en ejecución para instalar componentes adicionales o cambiar los valores de configuración.

> [!NOTE]
> La posibilidad de usar acciones de script en un clúster ya en ejecución solo está disponible para clústeres de HDInsight basados en Linux. Para más información sobre el uso de las acciones de script con clústeres basados en Windows, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md).
> 
> 

Las acciones de script también pueden publicarse en Azure Marketplace como una aplicación de HDInsight. Algunos de los ejemplos de este documento muestran cómo puede instalar una aplicación de HDInsight mediante comandos de acción de script de PowerShell y el SDK. NET. Para más información sobre aplicaciones de HDInsight, consulte [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md).

## Descripción de las acciones de script
Una acción de script es un script de Bash al que se proporciona una dirección URL y para el que se proporcionan parámetros, que luego se ejecuta en los nodos del clúster de HDInsight. Estas son algunas características de las acciones de script.

* Deben almacenarse en un URI accesible desde el clúster de HDInsight. A continuación, se proponen varias ubicaciones de almacenamiento posibles:
  
  * Una cuenta de almacenamiento de blobs que sea la cuenta de almacenamiento principal o una adicional para el clúster de HDInsight. Puesto que, durante la creación del clúster, se concede acceso a HDInsight a estos dos tipos de cuentas de almacenamiento, estas ofrecen una forma de usar una acción de script no público.
  * Un URI público legible, como un blob de Azure, GitHub, OneDrive, Dropbox, etc.
    
    Para obtener ejemplos de URI de scripts que se almacenan en un contenedor de blobs (con legibilidad pública), consulte la sección [Ejemplo de scripts de acción de script](#example-script-action-scripts).
* Su **ejecución se puede limitar a determinados tipos de nodos**, como a nodos principales o de trabajo.
  
  > [!NOTE]
  > Cuando se utiliza con HDInsight Premium, puede especificar que el script se tenga que usar en el nodo perimetral.
  > 
  > 
* Esto pueden ser **persistentes** o **ad hoc**.
  
    Los scripts **persistentes** son aquellos que se aplican a nodos de trabajo y se ejecutan automáticamente en los nuevos nodos creados al escalar un clúster verticalmente.
  
    Un script persistente podría también aplicar cambios a otro tipo de nodo, por ejemplo, a un nodo principal, pero desde una perspectiva de la funcionalidad, la única razón para guardar un script es que se aplica a los nuevos nodos de trabajo creados cuando un clúster se escala horizontalmente.
  
  > [!IMPORTANT]
  > Las acciones de scripts persistentes deben tener un nombre único.
  > 
  > 
  
    Los scripts **ad hoc** no son persistentes; sin embargo, es posible promoverlos posteriormente a scripts persistentes o bien disminuir de nivel los scripts persistentes a scripts ad hoc.
  
  > [!IMPORTANT]
  > Las acciones de script usadas durante la creación de un clúster se guardan automáticamente.
  > 
  > Los scripts que dan error no se guardan, aunque indique específicamente que lo hagan.
  > 
  > 
* Pueden aceptar **parámetros** que usa el script durante la ejecución.
* Se ejecutan con **privilegios de nivel raíz** en los nodos del clúster.
* Se pueden usar mediante **Azure Portal**, **Azure PowerShell**, la **CLI de Azure** o el **SDK de .NET para HDInsight**
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

Para ayudar a comprender qué scripts se han aplicado a un clúster, y a determinar los id. de scripts para promoción o disminución de nivel, el clúster mantiene un historial de todos los scripts que se han ejecutado.

> [!IMPORTANT]
> No hay ninguna forma automática de deshacer los cambios realizados por una acción de script. Si necesita revertir los efectos de un script, debe comprender qué cambios se realizaron y revertirlos manualmente (o proporcionar una acción de script que los revierta).
> 
> 

### Acción de script en el proceso de aprovisionamiento de clústeres
Las acciones de script usadas durante la creación de un clúster son algo diferentes de las ejecutadas en un clúster existente:

* El script se **guarda automáticamente como persistente**.
* Un **error** del script puede impedir que el proceso de creación del clúster se efectúe correctamente.

El siguiente diagrama ilustra el momento en que acción de script se ejecuta durante el proceso de aprovisionamiento:

![Fases y personalización de clústeres de HDInsight durante la creación de clústeres][img-hdi-cluster-states]

Se ejecuta el script durante la configuración de HDInsight. En esta fase, el script se ejecuta de forma paralela en todos los nodos especificados del clúster con privilegios raíz en los nodos.

> [!NOTE]
> Como el script se ejecuta con privilegios raíz en los nodos del clúster, puede realizar operaciones como la detención y el inicio de servicios, incluidos los servicios de Hadoop. Si detiene los servicios, tiene que asegurarse de que los servicios de Ambari y los demás servicios de Hadoop estén en funcionamiento antes de que finalice la ejecución del script. Estos servicios son necesarios para determinar correctamente el estado del clúster mientras se crea.
> 
> 

Durante este proceso, puede especificar que se invoquen varias acciones de script en el orden en el que se especificaron.

> [!IMPORTANT]
> Las acciones de script se tienen que completar dentro de un periodo de 60 minutos o superarán el tiempo de espera. Durante el aprovisionamiento del clúster, el script se ejecuta a la vez con otros procesos de instalación y configuración. La competición por los recursos, como el ancho de banda de red o el tiempo de CPU puede ocasionar que el script tarde más en terminar que en el entorno de desarrollo.
> 
> Para minimizar el tiempo necesario para ejecutar el script, evite tareas tales como descargar y compilar aplicaciones desde el origen. En su lugar, compile previamente la aplicación y almacene la versión binaria en el almacenamiento de blobs de Azure para que se pueda descargar rápidamente en el clúster.
> 
> 

### Acción de script en un clúster en ejecución
A diferencia de las acciones de script usadas durante la creación de un clúster, un error en un script ejecutado en un clúster ya en ejecución no hace que el clúster cambie automáticamente a un estado de error. Cuando un script finaliza, el clúster debe volver a un estado "en ejecución".

> [!IMPORTANT]
> Esto no significa que el clúster en ejecución sea inmune a los scripts que no funcionan correctamente. Por ejemplo, un script podría eliminar archivos que necesita un clúster, cambiar la configuración y hacer que los servicios se ejecutaran con errores, etc.
> 
> Las acciones de script se ejecutan con privilegios raíz, así que debe asegurarse de que comprende lo que hace un script antes de aplicarlo a un clúster.
> 
> 

Al aplicar un script a un clúster, el estado de este cambiará de **En ejecución** a **Aceptado**, luego a **Configuración de HDInsight** y, finalmente, de nuevo a **En ejecución** en el caso de los scripts que funcionan correctamente. El estado del script se registra en el historial de acciones de script, y puede usar este historial para determinar si el script funcionó correctamente o no. Por ejemplo, el cmdlet `Get-AzureRmHDInsightScriptActionHistory` de PowerShell se puede usar para ver el estado de un script. La información devuelta con este cmdlet será parecida a la siguiente:

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Si ha cambiado la contraseña del usuario (admin) del clúster tras la creación del mismo, puede provocar el error de las acciones de script ejecutadas en este clúster. Si tiene cualquier acciones de script persistente cuyo destino son nodos de trabajo, pueden producir un error al agregar nodos al clúster a través de operaciones de cambio de tamaño.
> 
> 

## Ejemplo de scripts de acción de script
Los scripts de acción de script pueden usarse desde Azure Portal, Azure PowerShell, la CLI de Azure o el SDK para .NET de HDInsight. HDInsight proporciona scripts para instalar los siguientes componentes en clústeres de HDInsight:

| Nombre | Script |
| --- | --- |
| **Agregar una cuenta de Azure Storage** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Consulte [Aplicación de una acción de script a un clúster en ejecución](#apply-a-script-action-to-a-running-cluster). |
| **Instalación de Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Consulte [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md) |
| **Instalar R** |https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consulte [Instalación y uso de R en clústeres de HDInsight](hdinsight-hadoop-r-scripts-linux.md). |
| **Instalar Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consulte [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| **Instalación de Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Vea [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| **Carga previa de las bibliotecas de Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Consulte [Incorporación de bibliotecas de Hive durante la creación de clústeres de HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## Uso de una acción de script durante la creación de un clúster
En esta sección se proporcionan ejemplos sobre las diferentes maneras en que puede usar acciones de script al crear un clúster de HDInsight: desde Azure Portal, mediante una plantilla de Azure Resource Manager, usando cmdlets de PowerShell y con el SDK. de NET.

### Uso de una acción de script durante la creación de un clúster desde Azure Portal
1. Comience a crear un clúster, tal como se describe en [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md#portal).
2. En **Configuración opcional**, de la hoja **Acciones de script**, haga clic en **Agregar acción de script** para proporcionar detalles sobre la acción de script, tal como se muestra a continuación:
   
    ![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)
   
   | Propiedad | Valor |
   | --- | --- |
   | Nombre |Especifique un nombre para la acción de script. |
   | Identificador URI de script |Especifique el identificador URI al script que se ha invocado para personalizar el clúster. |
   | Head/Worker |Especifique los nodos (**Principal** o **Trabajo** o **Zookeeper**) en los que se ejecuta el script de personalización. |
   | Parámetros |Especifique los parámetros, si lo requiere el script. |
   
    Presione ENTRAR para agregar más de una acción de script para instalar varios componentes en el clúster.
3. Haga clic en **Seleccionar** para guardar la configuración y continuar con la creación del clúster.

### Use una acción de script de las plantillas de Azure Resource Manager
En esta sección, usamos plantillas de Azure Resource Manager para crear un clúster de HDInsight y usar una acción de script para instalar componentes personalizados (en este ejemplo, R) en el clúster. En esta sección se proporciona una plantilla de ejemplo para crear un clúster mediante la acción de script.

> [!NOTE]
> Los pasos de esta sección muestran cómo crear un clúster mediante una acción de script. Para obtener un ejemplo de creación de un clúster desde una plantilla mediante una aplicación de HDInsight, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).
> 
> 

#### Antes de empezar
* Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
* Para obtener instrucciones sobre cómo crear plantillas, consulte [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md).
* Si todavía no ha utilizado Azure PowerShell con el Administrador de recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)

#### Creación de clústeres mediante acciones de script
1. Copie la plantilla siguiente en una ubicación en el equipo. Esta plantilla instala Giraph en el nodo principal, así como en los nodos de trabajo en el clúster. También puede comprobar si la plantilla JSON es válida. Pegue el contenido de la plantilla en [JSONLint](http://jsonlint.com/), herramienta de validación JSON en línea.
   
            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installGiraph",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }
2. Inicie Azure PowerShell e inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.
   
        Add-AzureRmAccount
   
        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...
3. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.
   
        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>
   
   > [!NOTE]
   > Puede usar `Get-AzureRmSubscription` para obtener una lista de todas las suscripciones asociadas a su cuenta, lo que incluye el id. de suscripción de cada una.
   > 
   > 
4. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del grupo de recursos nuevo.
   
        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"
   
        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup
5. Para crear otra implementación del grupo de recursos, ejecute el comando **New-AzureRmResourceGroupDeployment** y especifique los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos y la ruta de acceso o dirección URL a la plantilla que creó. Si la plantilla requiere parámetros, tiene que pasar también esos parámetros. En este caso, la acción de script para instalar R en el clúster no requiere ningún parámetro.

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


    Se le pedirá que proporcione valores para los parámetros definidos en la plantilla.

1. Una vez implementado el grupo de recursos, verá un resumen de la implementación.
   
          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/17/2015 7:00:27 PM
          Mode              : Incremental
          ...
2. Si se produce un error en la implementación, puede usar los cmdlets siguientes para obtener información sobre los errores.
   
        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### Uso de una acción de script durante la creación de un clúster desde Azure PowerShell
En esta sección, usamos el cmdlet [Add-AzureRMHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) para invocar scripts mediante la acción de script con el fin de personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para información sobre cómo configurar una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Lleve a cabo los siguiente pasos:

1. Abra la consola de Azure PowerShell y use el siguiente código para iniciar sesión en su suscripción de Azure y declarar algunas variables de PowerShell:
   
        # LOGIN TO ZURE
        Login-AzureRmAccount
   
        # PROVIDE VALUES FOR THESE VARIABLES
        $subscriptionId = "<SubscriptionId>"        # ID of the Azure subscription
        $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"                # Location of the HDInsight cluster. It must be in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNumbers>            # The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in
2. Especifique los valores de configuración (como nodos en el clúster) y el almacenamiento predeterminado que se va a usar.
   
        # SPECIFY THE CONFIGURATION OPTIONS
        Select-AzureRmSubscription -SubscriptionId $subscriptionId
        $config = New-AzureRmHDInsightClusterConfig
        $config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccountKey=$storageAccountKey
3. Use el cmdlet **Add-AzureRmHDInsightScriptAction** para invocar el script. En el ejemplo siguiente se usa el script para instalar Giraph en el clúster:
   
        # INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
   
    El cmdlet **Add-AzureRmHDInsightScriptAction** toma los siguientes parámetros:
   
   | Parámetro | Definición |
   | --- | --- |
   | Configuración |Objeto de configuración al que se agrega la información de la acción de script. |
   | Nombre |Nombre de la acción de script. |
   | NodeType |Especifica el nodo en el que se ejecuta el script de personalización. Los valores válidos son **HeadNode** (para instalar en el nodo principal), **WorkerNode** (para instalar en todos los nodos de datos) o **ZookeeperNode** (para instalar en el nodo zookeeper). |
   | Parámetros |Parámetros requeridos por el script. |
   | Identificador URI |Especifica el identificador URI para el script que se ejecuta. |
4. Establecimiento del usuario admin/HTTPS para el clúster:
   
        $httpCreds = get-credential
   
    Cuando se le solicite, escriba el nombre 'admin' e indique una contraseña.
5. Establecimiento de las credenciales SSH:
   
        $sshCreds = get-credential
   
    Cuando se le solicite, escriba el nombre de usuario SSH y la contraseña. Si desea proteger la cuenta SSH con un certificado en lugar de una contraseña, use una contraseña en blanco y establezca `$sshPublicKey` en el contenido de la clave pública del certificado que quiera usar. Por ejemplo:
   
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
6. Por último, cree el clúster:
   
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
   
    Si usa una clave pública para proteger la cuenta SSH, debe especificar también `-SshPublicKey $sshPublicKey` como parámetro.

El clúster puede tardar varios minutos en crearse.

### Uso de una acción de script durante la creación de un clúster desde el SDK de .NET para HDInsight
El .NET SDK de HDInsight proporciona bibliotecas de cliente que facilitan el trabajo con HDInsight desde una aplicación .NET. Para ver un ejemplo de código, consulte [Crear clústeres basados en Linux en HDInsight con el SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## Aplicación de una acción de script a un clúster en ejecución
En esta sección se proporcionan ejemplos sobre las diferentes maneras en que puede aplicar acciones de script a un clúster en ejecución de HDInsight: mediante Azure Portal, cmdlets de PowerShell, la CLI de Azure multiplataforma y el SDK. de NET. La acción de script persistente usada en esta sección permite agregar una cuenta de Azure Storage existente a un clúster en ejecución. También puede usar otras acciones de script; para ello consulte [Ejemplo de scripts de acción de script](#example-script-action-scripts).

### Aplicación de una acción de script a un clúster en ejecución desde Azure Portal
1. En el [Portal de Azure](https://portal.azure.com), seleccione el clúster de HDInsight.
2. En la hoja del clúster de HDInsight, seleccione el icono **Acciones de script**.
   
    ![Icono Acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)
   
   > [!NOTE]
   > También puede seleccionar **Toda la configuración** y, luego, **Acciones de script** en la hoja Configuración.
   > 
   > 
3. En la parte superior de la hoja Acciones de script, seleccione **Enviar nuevo**.
   
    ![Icono Enviar nuevo](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)
4. En la hoja Agregar acción de script, escriba la siguiente información.
   
   * **Nombre**: nombre descriptivo que se usará para esta acción de script. En este ejemplo, `Add Storage account`.
   * **URI DE SCRIPT**: el identificador URI del script. En este ejemplo: `https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`
   * **Encabezado**, **Trabajo** y **Zookeeper**: active los nodos a los que se debe aplicar este script. En este ejemplo se marcan Principal, Trabajo y Zookeeper.
   * **PARÁMETROS**: si el script acepta parámetros, especifíquelos aquí. En este ejemplo, escriba el nombre y la clave de la cuenta de almacenamiento:
     
       ![hdinsight acción de script persistente cuenta de almacenamiento en clústeres en ejecución](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-persisted-script-action-add-storage-account.png)
     
       En la captura de pantalla, `contosodata` es una cuenta de Azure Storage existente y la segunda línea es la clave de la cuenta.
   * **PERSISTENTE**: active esta entrada si desea guardar un script como persistente para que se aplique a nuevos nodos de trabajo cuando escale el clúster verticalmente.
5. Por último, use el botón **Crear** para aplicar el script al clúster.

### Aplicación de una acción de script a un clúster en ejecución desde Azure PowerShell
Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para información sobre cómo configurar una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

1. Abra la consola de Azure PowerShell y use el siguiente código para iniciar sesión en su suscripción de Azure y declarar algunas variables de PowerShell:
   
        # LOGIN TO ZURE
        Login-AzureRmAccount
   
        # PROVIDE VALUES FOR THESE VARIABLES
        $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"
   
   > [!NOTE]
   > Si utiliza un clúster de HDInsight Premium, puede utilizar un nodetype de `"edgenode"` para ejecutar el script en el nodo perimetral.
   > 
   > 
2. Utilice el siguiente comando para aplicar el script al clúster:
   
        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess
   
    Una vez completado el trabajo, debería recibir información similar a la siguiente:
   
        OperationState  : Succeeded
        ErrorMessage    :
        Name            : Giraph
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### Aplicación de una acción de script a un clúster en ejecución desde la CLI de Azure
Antes de continuar, asegúrese de que ha instalado y configurado la CLI de Azure. Para más información, consulte [Instalación de la CLI de Azure](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

1. Abra una sesión del shell, terminal, símbolo del sistema u otra línea de comandos para el sistema y use el comando siguiente para cambiar al modo de Azure Resource Manager.
   
        azure config mode arm
2. Use lo siguiente para autenticarse en su suscripción de Azure.
   
        azure login
3. Use el comando siguiente para aplicar una acción de script a un clúster en ejecución.
   
        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
   
    Si omite los parámetros de este comando, se le solicitarán. Si el script que especifica con `-u` acepta parámetros, puede especificarlos mediante el parámetro `-p`.
   
    Los valores de **nodetypes** válidos son **headnode**, **workernode** y **zookeeper**. Si el script se debe aplicar a varios tipos de nodo, especifique los tipos separados por punto y coma (;). Por ejemplo: `-n headnode;workernode`.
   
    Para guardar el script como persistente, agregue `--persistOnSuccess`. También puede guardar el script como persistente con posterioridad mediante `azure hdinsight script-action persisted set`.
   
    Cuando se complete el trabajo, obtendrá un resultado similar al siguiente.
   
        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### Aplicación de una acción de script a un clúster en ejecución mediante la API de REST
Consulte [Run Script Actions on a running cluster](https://msdn.microsoft.com/library/azure/mt668441.aspx) (Ejecución de acciones de script en un clúster en ejecución).

### Aplicación de una acción de script a un clúster en ejecución desde el SDK de .NET para HDInsight
Si desea ver un ejemplo de uso del SDK de .NET para aplicar scripts a un clúster, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## Visualización del historial, promover y disminuir de nivel acciones de script
### Uso del portal de Azure
1. En el [Portal de Azure](https://portal.azure.com), seleccione el clúster de HDInsight.
2. En la hoja del clúster de HDInsight, seleccione **Configuración**.
   
    ![Icono Configuración](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)
3. En la hoja Configuración, seleccione **Acciones de script**.
   
    ![Vínculo a acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)
4. En la hoja Acciones de script se muestra una lista de los scripts persistentes, así como un historial de los scripts aplicados al clúster. En la siguiente captura de pantalla, puede ver que el script Solr se ha ejecutado en este clúster, pero que no se ha guardado ninguna acción de script.
   
    ![Hoja Acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)
5. Al seleccionar un script del historial se muestra la hoja Propiedades de ese script. En la parte superior de la hoja, puede volver a ejecutar el script o subirlo de nivel.
   
    ![Hoja de propiedades de acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)
6. También puede usar los puntos suspensivos (**...**) a la derecha de las entradas de la hoja Acciones de script para realizar acciones como volver a ejecutar el elemento correspondiente, guardarlo como persistente o eliminarlo (en el caso de las acciones guardadas como persistentes).
   
    ![Acciones de script... uso](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### Uso de Azure PowerShell
| Use el código siguiente... | Para ... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Recuperar información sobre acciones de script persistentes |
| Get-AzureRmHDInsightScriptActionHistory |Recuperar un historial de acciones de script aplicadas al clúster o detalles de un script específico |
| Set-AzureRmHDInsightPersistedScriptAction |Promover una acción de script ad hoc a una acción de script persistente |
| Remove-AzureRmHDInsightPersistedScriptAction |Disminuir de nivel una acción de script persistente a una acción ad hoc |

> [!IMPORTANT]
> El uso de `Remove-AzureRmHDInsightPersistedScriptAction` no deshace las acciones que realiza un script, sino que solo elimina la marca de persistencia para que el script no se ejecute en los nuevos nodos de trabajo agregados al clúster.
> 
> 

El siguiente script de ejemplo muestra cómo utilizar los cmdlets para promover y luego disminuir de nivel un script.

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you will receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### Uso de la CLI de Azure
| Use el código siguiente... | Para ... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Recuperar una lista de las acciones de script persistentes |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Recuperar información sobre una acción de script persistente específica |
| `azure hdinsight script-action history list <clustername>` |Recuperar un historial de acciones de script aplicadas al clúster |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Recuperar información sobre una acción de script específica |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promover una acción de script ad hoc a una acción de script persistente |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Disminuir de nivel una acción de script persistente a una acción ad hoc |

> [!IMPORTANT]
> El uso de `azure hdinsight script-action persisted delete` no deshace las acciones que realiza un script, sino que solo elimina la marca de persistencia para que el script no se ejecute en los nuevos nodos de trabajo agregados al clúster.
> 
> 

### Uso del SDK de .NET de HDInsight
Si desea ver un ejemplo de uso del SDK de .NET para recuperar el historial de scripts de un clúster, o bien promover scripts o disminuir el nivel de estos, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> En este ejemplo también muestra cómo instalar una aplicación de HDInsight mediante el SDK. de NET.
> 
> 

## Solución de problemas
Puede usar la interfaz de usuario web de Ambari para ver la información registrada por las acciones de script. Si el script se ha utilizado durante la creación del clúster y este proceso no se realizó debido a un error en el script, los registros también están disponibles en la cuenta de almacenamiento predeterminada asociada con el clúster. Esta sección proporciona información sobre cómo recuperar los registros mediante ambas opciones.

### Uso de la interfaz de usuario web de Ambari
1. Abra el explorador y vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.
   
    Cuando se le solicite, escriba el nombre de cuenta de administrador (admin) y la contraseña de administrador para el clúster. Tendrá que volver a escribir las credenciales de administrador en un formulario web.
2. En los vínculos de la parte superior de la página, seleccione la entrada **ops**. Esto mostrará una lista de las operaciones actuales y anteriores realizadas en el clúster a través de Ambari.
   
    ![Barra de interfaz de usuario web de Ambari con ops seleccionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)
3. Busque las entradas que tienen **run\_customscriptaction** en la columna **Operaciones**. Estas se crean al ejecutarse las acciones de script.
   
    ![Captura de pantalla de operaciones](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)
   
    Seleccione esta entrada y profundice a través de los vínculos para ver el resultado STDOUT y STDERR que se genera cuando el script se ejecutó en el clúster.

### Acceso a los registros desde la cuenta de almacenamiento predeterminada
Si se produce un error en la creación del clúster debido a un error en la acción de script, los registros de la acción de script todavía pueden tener acceso directamente desde la cuenta de almacenamiento predeterminado asociada al clúster.

* Los registros de almacenamiento están disponibles en `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.
  
    ![Captura de pantalla de operaciones](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)
  
    En este caso, los registros se organizan por separado para el nodo principal, el nodo de trabajo y el nodo de Zookeeper. A continuación, se indican algunos ejemplos:
  
  * **Nodo principal**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
  * **Nodo de trabajo**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
  * **Nodo de Zookeeper**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`
* Todos los stdout y stderr del host correspondiente se cargan en la cuenta de almacenamiento. Hay un archivo **output-*.txt** y **errors-*.txt** para cada acción de script. El archivo de output-*.txt contiene información sobre el identificador URI del script que se ejecuta en el host. Por ejemplo:
  
        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
* Es posible crear repetidamente un clúster de la acción de script con el mismo nombre. En tal caso, puede distinguir los registros relevantes según el nombre de la carpeta de fecha. Por ejemplo, la estructura de carpetas para un clúster (mycluster) creado en diferentes fechas será la siguiente:
  
  * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
  * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`
* Si crea un clúster de acción de script con el mismo nombre en el mismo día, puede usar el prefijo único para identificar los archivos de registro correspondientes.
* Si crea un clúster al final del día, es posible que los archivos de registro abarquen dos días. En tales casos, verá dos carpetas de fecha diferentes para el mismo clúster.
* La carga de los archivos de registro en el contenedor predeterminado puede tardar hasta 5 minutos, especialmente para grandes clústeres. Por lo tanto, si desea tener acceso a los registros, no debe eliminar inmediatamente el clúster si se produce un error en una acción de script.

## Soporte técnico para el software de código abierto utilizado en clústeres de HDInsight
El servicio HDInsight de Microsoft Azure es una plataforma flexible que permite compilar aplicaciones con grandes volúmenes de datos en la nube mediante el ecosistema de tecnologías de código abierto formadas en torno a Hadoop. Microsoft Azure ofrece un nivel general de soporte técnico para las tecnologías de código abierto, tal como se describe en la sección **Ámbito de soporte técnico** del [sitio web de Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). Además, el servicio de HDInsight ofrece un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Hay dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

* **Componentes integrados**: estos componentes están instalados previamente en clústeres de HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, el administrador de recursos de YARN, el lenguaje de consulta Hive (HiveQL) y la biblioteca Mahout pertenecen a esta categoría. Una lista completa de los componentes del clúster está disponible en [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md).
* **Componentes personalizados**: el usuario del clúster puede instalar o usar en la carga de trabajo cualquier componente que esté disponible en la comunidad o que haya creado personalmente.

> [!WARNING]
> Los componentes ofrecidos con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
> 
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Los proyectos de Apache también tienen sitios de proyecto en [http://apache.org](http://apache.org) (por ejemplo, [Hadoop](http://hadoop.apache.org/)).
> 
> 

El servicio HDInsight proporciona varias maneras de utilizar los componentes personalizados. Independientemente de cómo se use el componente o cómo se instale en el clúster, se aplica el mismo nivel de soporte técnico. A continuación, se muestra una lista de las maneras más comunes que se pueden usar los componentes personalizados en los clústeres de HDInsight:

1. Envío de trabajos: se pueden enviar al clúster trabajos de Hadoop o de otros tipos que ejecuten o usen componentes personalizados.
2. Personalización del clúster: durante la creación del clúster puede especificar una configuración adicional y componentes personalizados que se instalarán en los nodos del clúster.
3. Muestras: para los componentes personalizados más populares, Microsoft y otros pueden proporcionar muestras de cómo estos componentes se pueden utilizar en los clústeres de HDInsight. Estas muestras se proporcionan sin soporte técnico.

## Solución de problemas
### El historial no muestra los scripts usados durante la creación del clúster
Si el clúster se creó antes del 15 de marzo de 2016, puede que no vea una entrada en el historial de acciones de script relativa a los scripts usados durante la creación del clúster. Sin embargo, si ha cambiado el tamaño del clúster después del 15 de marzo de 2016, los scripts usados durante la creación del clúster aparecerán en el historial ya que se aplican a los nuevos nodos del clúster como parte de la operación de cambio de tamaño.

Hay dos excepciones:

* Si el clúster se creó antes del 1 de septiembre de 2015. Es cuando se introdujeron las acciones de script, así que ningún clúster creado con anterioridad a esta fecha ha podido usar acciones de script para su creación.
* Si ha usado varias acciones de script durante la creación del clúster y ha usado el mismo nombre para varios scripts, o el mismo nombre y el mismo identificador URI, pero diferentes parámetros para varios scripts. En estos casos, recibirá el siguiente error:
  
    No se puede ejecutar ninguna nueva acción de script en este clúster debido al conflicto con los nombres de los scripts existentes. Los nombres de script proporcionados en la creación del clúster deben ser únicos. Los scripts existentes se siguen ejecutando a pesar del cambio de tamaño.

## Pasos siguientes
Consulte la siguiente información y ejemplos sobre la creación y uso de scripts para personalizar un clúster:

* [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fases durante la creación del clúster"

<!---HONumber=AcomDC_0914_2016-->