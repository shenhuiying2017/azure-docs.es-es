---
title: "Personalizar clústeres de HDInsight mediante acciones de script - Azure | Microsoft Docs"
description: "Agregue componentes personalizados a clústeres de HDInsight basados en Linux mediante acciones de script. Las acciones de script son scripts de Bash que se pueden usar para personalizar la configuración del clúster o para agregar servicios adicionales y utilidades como Hue, Solr o R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ad2ba8688761a7fa2541906fe04987dfb1997a81
ms.contentlocale: es-es
ms.lasthandoff: 07/08/2017


---
# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)

HDInsight proporciona una opción de configuración llamada **Acción de script** que invoca scripts personalizados que personalizan el clúster. Estos scripts se usan para instalar componentes adicionales y para cambiar los valores de configuración. Las acciones de script pueden usarse durante la creación del clúster o después.

> [!IMPORTANT]
> La posibilidad de usar acciones de script en un clúster ya en ejecución solo está disponible para clústeres de HDInsight basados en Linux.
>
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Las acciones de script también pueden publicarse en Azure Marketplace como una aplicación de HDInsight. Algunos de los ejemplos de este documento muestran cómo puede instalar una aplicación de HDInsight mediante comandos de acción de script de PowerShell y el SDK. NET. Para más información sobre aplicaciones de HDInsight, consulte [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>permisos

Si está usando un clúster de HDInsight unido a un dominio, necesita dos permisos de Ambari para usar acciones de script con el clúster:

* **AMBARI.RUN\_CUSTOM\_COMMAND**: el rol de administrador de Ambari tiene este permiso de forma predeterminada.
* **CLUSTER.RUN\_CUSTOM\_COMMAND**: el administrador de clústeres de HDInsight y el administrador de Ambari tienen este permiso de forma predeterminada.

Para más información sobre cómo trabajar con permisos con clústeres de HDInsight unidos a un dominio, consulte [Administrar clústeres de HDInsight unidos a un dominio](hdinsight-domain-joined-manage.md).

## <a name="access-control"></a>Control de acceso

Si no es el administrador o propietario de la suscripción de Azure, la cuenta debe tener al menos acceso de **Colaborador** al grupo de recursos que contiene el clúster de HDInsight.

Además, si va a crear un clúster de HDInsight, un usuario con al menos acceso de **Colaborador** a la suscripción de Azure debe haber registrado previamente el proveedor para HDInsight. El registro del proveedor se produce cuando un usuario con acceso de Colaborador a la suscripción crea un recurso por primera vez en la suscripción. También puede realizarse sin crear ningún recurso mediante el [registro de un proveedor con REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para más información sobre cómo trabajar con la administración de acceso, consulte los documentos siguientes:

* [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md)
* [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>Descripción de las acciones de script

Una acción de script es simplemente un script de Bash al que proporciona un URI y para el que proporciona parámetros. El script se ejecuta en nodos del clúster de HDInsight. Estas son algunas características de las acciones de script.

* Deben almacenarse en un URI accesible desde el clúster de HDInsight. A continuación, se proponen varias ubicaciones de almacenamiento posibles:

    * Una **cuenta de Azure Data Lake Store** accesible para el clúster de HDInsight. Para más información sobre el uso de Azure Data Lake Store con HDInsight, consulte [Creación de un clúster de HDInsight con Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Cuando se usa un script almacenado en Data Lake Store, el formato del URI es `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > La entidad de servicio que HDInsight usa para acceder a Data Lake Store debe tener acceso de lectura al script.

    * Un blob de una **cuenta de Azure Storage** que sea la cuenta de almacenamiento principal o adicional del clúster de HDInsight. Durante la creación del clúster se concede acceso a HDInsight a estos dos tipos de cuentas de almacenamiento.

    * Un servicio público de uso compartido de archivos, como Azure Blob, GitHub, OneDrive, Dropbox, etc.

        Para obtener URI de ejemplo, vea la sección [Ejemplo de scripts de acción de script](#example-script-action-scripts).

        > [!WARNING]
        > HDInsight solo admite cuentas de Azure Storage de __uso general__. No admite actualmente el tipo de cuenta de __Blob Storage__.

* Su **ejecución se puede limitar a determinados tipos de nodos**, por ejemplo, nodos principales o de trabajo.

  > [!NOTE]
  > Cuando se utiliza con HDInsight Premium, puede especificar que el script se tenga que usar en el nodo perimetral.

* Puede ser **persistente** o **ad hoc**.

    Los scripts **persistentes** se aplican a los nodos de trabajo agregados al clúster después de la ejecución del script. Por ejemplo, al escalar el clúster.

    Un script persistente también puede aplicar cambios a otro tipo de nodo, como uno principal.

  > [!IMPORTANT]
  > Las acciones de scripts persistentes deben tener un nombre único.

    Los scripts **ad hoc** no son persistentes. No se aplican a los nodos de trabajo agregados al clúster después de la ejecución del script. Un script ad hoc se puede promover a persistente y el nivel de un script persistente se puede disminuir a ad hoc.

  > [!IMPORTANT]
  > Las acciones de script usadas durante la creación de un clúster se guardan automáticamente.
  >
  > Los scripts que dan error no se guardan, aunque indique específicamente que lo hagan.

* Pueden aceptar **parámetros** que usa el script durante la ejecución.
* Se ejecutan con **privilegios de nivel raíz** en los nodos del clúster.
* Se pueden usar mediante **Azure Portal**, **Azure PowerShell**, la **CLI de Azure** o el **SDK de .NET para HDInsight**.

El clúster conserva un historial de todos los scripts que se han ejecutado. El historial resulta útil cuando se necesita buscar el identificador de un script para las operaciones de promoción o disminución de nivel.

> [!IMPORTANT]
> No hay ninguna forma automática de deshacer los cambios realizados por una acción de script. Los cambios se revierten de forma manual o se proporciona un script que lo hace.


### <a name="script-action-in-the-cluster-creation-process"></a>Acción de script en el proceso de aprovisionamiento de clústeres

Las acciones de script usadas durante la creación de un clúster son algo diferentes de las ejecutadas en un clúster existente:

* El script se **guarda automáticamente como persistente**.
* Un **error** del script puede impedir que el proceso de creación del clúster se efectúe correctamente.

El siguiente diagrama ilustra el momento en que acción de script se ejecuta durante el proceso de aprovisionamiento:

![Fases y personalización de clústeres de HDInsight durante la creación de clústeres][img-hdi-cluster-states]

El script se ejecuta mientras se configura HDInsight. En esta fase, el script se ejecuta de forma paralela en todos los nodos especificados del clúster con privilegios raíz en los nodos.

> [!NOTE]
> Como el script se ejecuta con privilegios raíz en los nodos del clúster, puede realizar operaciones como la detención y el inicio de servicios, incluidos los servicios de Hadoop. Si detiene los servicios, tiene que asegurarse de que los servicios de Ambari y los demás servicios de Hadoop estén en funcionamiento antes de que finalice la ejecución del script. Estos servicios son necesarios para determinar correctamente el estado del clúster mientras se crea.


Durante la creación del clúster, puede usar varias acciones de script a la vez. Estos scripts se invocan en el orden en el que se hayan especificado.

> [!IMPORTANT]
> Las acciones de script se tienen que completar dentro de un periodo de 60 minutos o se superará el tiempo de espera. Durante el aprovisionamiento del clúster, el script se ejecuta a la vez con otros procesos de instalación y configuración. La competición por los recursos, como el ancho de banda de red o el tiempo de CPU puede ocasionar que el script tarde más en terminar que en el entorno de desarrollo.
>
> Para minimizar el tiempo necesario para ejecutar el script, evite tareas tales como descargar y compilar aplicaciones desde el origen. Compile las aplicaciones previamente y almacene el archivo binario en Azure Storage. Esto permite que el script descargue rápidamente la aplicación en el clúster.


### <a name="script-action-on-a-running-cluster"></a>Acción de script en un clúster en ejecución

A diferencia de las acciones de script usadas durante la creación de un clúster, un error en un script ejecutado en un clúster ya en ejecución no hace que el clúster cambie automáticamente a un estado de error. Cuando un script finaliza, el clúster debe volver a un estado "en ejecución".

> [!IMPORTANT]
> Esto no significa que el clúster en ejecución sea inmune a los scripts que no funcionan correctamente. Por ejemplo, un script podría eliminar archivos necesarios para el clúster.
>
> Las acciones de script se ejecutan con privilegios raíz, así que debe asegurarse de que comprende lo que hace un script antes de aplicarlo a un clúster.

Al aplicar un script a un clúster, el estado de este cambia de **En ejecución** a **Aceptado**, luego a **Configuración de HDInsight** y, finalmente, de nuevo a **En ejecución** en el caso de los scripts que funcionan correctamente. El estado del script se registra en el historial de acciones de script; esta información se puede usar para determinar si el script ha funcionado correctamente o no. Por ejemplo, el cmdlet `Get-AzureRmHDInsightScriptActionHistory` de PowerShell se puede usar para ver el estado de un script. Devuelve información similar al texto siguiente:

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Si ha cambiado la contraseña de usuario (admin) del clúster tras la creación de este, se puede producir un error en las acciones de script ejecutadas en este clúster. Si tiene alguna acción de script persistente cuyo destino sea algún nodo de trabajo, se puede producir un error en este script al escalar el clúster.

## <a name="example-script-action-scripts"></a>Ejemplo de scripts de acción de script

Los scripts de acciones de script se pueden usar con las utilidades siguientes:

* Portal de Azure
* Azure PowerShell
* Azure CLI
* SDK .NET de HDInsight

HDInsight proporciona scripts para instalar los siguientes componentes en clústeres de HDInsight:

| Nombre | Script |
| --- | --- |
| **Agregar una cuenta de Azure Storage** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Vea el artículo [Adición de almacenamiento adicional a un clúster HDInsight](hdinsight-hadoop-add-storage.md). |
| **Instalación de Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Consulte [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md) |
| **Instalar Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Vea [Install and use Presto on HDInsight clusters](hdinsight-hadoop-install-presto.md) (Instalación y uso de Presto en clústeres de HDInsight). |
| **Instalar Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Vea [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| **Instalación de Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Vea [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| **Carga previa de las bibliotecas de Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Vea [Add Hive libraries on HDInsight clusters](hdinsight-hadoop-add-hive-libraries.md) (Adición de bibliotecas de Hive en clústeres de HDInsight). |
| **Instalación o actualización de Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Vea [Instalación o actualización de Mono en HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Uso de una acción de script durante la creación de un clúster

En esta sección se proporcionan ejemplos sobre las distintas maneras de usar acciones de script al crear un clúster de HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Uso de una acción de script durante la creación de un clúster desde Azure Portal

1. Comience a crear un clúster, tal y como se describe en [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Deténgase al llegar a la hoja __Resumen del clúster__.

2. En la hoja __Resumen del clúster__, seleccione el vínculo __editar__ de __Configuración avanzada__.

    ![Vínculo Configuración avanzada](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. En la hoja __Configuración avanzada__, seleccione __Acciones de script__. En la hoja __Acciones de script__, seleccione __+ Enviar Nuevo__

    ![Enviar una nueva acción de script](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Use la entrada __Seleccione un script__ para seleccionar un script prefabricado. Para usar un script personalizado, seleccione __Personalizado__ y luego proporcione el __Nombre__ y el __URI de script de Bash__ del script.

    ![Agregar un script en el formulario de selección de script](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    En la siguiente tabla se describen los elementos del formulario:

    | Propiedad | Valor |
    | --- | --- |
    | Seleccione un script | Seleccione uno de los scripts prefabricados o __Personalizado__ para usar su propio script. |
    | Nombre |Especifique un nombre para la acción de script. |
    | URI de script de Bash |Especifique el identificador URI al script que se ha invocado para personalizar el clúster. |
    | Principal, Trabajo o Zookeeper |Especifique los nodos (**Principal**, **Trabajo** o **Zookeeper**) en los que se ejecuta el script de personalización. |
    | Parámetros |Especifique los parámetros, si lo requiere el script. |

    Use la entrada __Continuar con esta acción de script__ para asegurarse de que el script se aplica a los nodos de trabajo al escalar el clúster.

5. Seleccione __Crear__ para guardar el script. Luego puede usar __+ Enviar Nuevo__ para agregar otro script.

    ![Varias acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Cuando haya terminado de agregar scripts, use el botón __Seleccionar__ y luego el botón __Siguiente__ para volver a la hoja __Resumen del clúster__.

3. Para crear el clúster, seleccione __Crear__ en la hoja __Resumen del clúster__.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Use una acción de script de las plantillas de Azure Resource Manager

Los ejemplos de esta sección muestran cómo usar acciones de script con plantillas de Azure Resource Manager al crear un clúster de HDInsight.

#### <a name="before-you-begin"></a>Antes de empezar

* Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
* Para obtener instrucciones sobre cómo crear plantillas, consulte [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Si todavía no ha utilizado Azure PowerShell con el Administrador de recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../azure-resource-manager/powershell-azure-resource-manager.md)

#### <a name="create-clusters-using-script-action"></a>Creación de clústeres mediante acciones de script

1. Copie la plantilla siguiente en una ubicación en el equipo. Esta plantilla instala Giraph en el nodo principal y en los nodos de trabajo del clúster. También puede comprobar si la plantilla JSON es válida. Pegue el contenido de la plantilla en [JSONLint](http://jsonlint.com/), herramienta de validación JSON en línea.

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

4. Si no tiene un grupo de recursos existente, puede crear uno. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del grupo de recursos nuevo.

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

5. Para crear una implementación del grupo de recursos, ejecute el comando **New-AzureRmResourceGroupDeployment** y especifique los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre del grupo de recursos y la ruta de acceso o dirección URL a la plantilla que creó. Si la plantilla requiere parámetros, tiene que pasar también esos parámetros. En este caso, la acción de script para instalar R en el clúster no requiere ningún parámetro.

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>

    Se le pedirá que proporcione valores para los parámetros definidos en la plantilla.

1. Una vez implementado el grupo de recursos, se muestra un resumen de la implementación.

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/17/2015 7:00:27 PM
          Mode              : Incremental
          ...

2. Si se produce un error en la implementación, puede usar los cmdlets siguientes para obtener información sobre los errores.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Uso de una acción de script durante la creación de un clúster desde Azure PowerShell

En esta sección, usamos el cmdlet [Add-AzureRMHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) para invocar scripts mediante la acción de script con el fin de personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para información sobre cómo configurar una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

El script siguiente muestra cómo aplicar una acción de script al crear un clúster con PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

El clúster puede tardar varios minutos en crearse.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Uso de una acción de script durante la creación de un clúster desde el SDK de .NET para HDInsight

El .NET SDK de HDInsight proporciona bibliotecas de cliente que facilitan el trabajo con HDInsight desde una aplicación .NET. Para ver un ejemplo de código, consulte [Crear clústeres basados en Linux en HDInsight con el SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Aplicación de una acción de script a un clúster en ejecución

En esta sección, aprenda cómo aplicar acciones de script a un clúster en ejecución.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicación de una acción de script a un clúster en ejecución desde Azure Portal

1. En el [Portal de Azure](https://portal.azure.com), seleccione el clúster de HDInsight.

2. En la hoja del clúster de HDInsight, seleccione el icono **Acciones de script**.

    ![Icono Acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > También puede seleccionar **Toda la configuración** y, luego, **Acciones de script** en la hoja Configuración.

3. En la parte superior de la hoja Acciones de script, seleccione **Enviar nuevo**.

    ![Agregar un script a un clúster en ejecución](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Use la entrada __Seleccione un script__ para seleccionar un script prefabricado. Para usar un script personalizado, seleccione __Personalizado__ y luego proporcione el __Nombre__ y el __URI de script de Bash__ del script.

    ![Agregar un script en el formulario de selección de script](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    En la siguiente tabla se describen los elementos del formulario:

    | Propiedad | Valor |
    | --- | --- |
    | Seleccione un script | Seleccione uno de los scripts prefabricados o __Personalizado__ para usar su propio script. |
    | Nombre |Especifique un nombre para la acción de script. |
    | URI de script de Bash |Especifique el identificador URI al script que se ha invocado para personalizar el clúster. |
    | Principal, Trabajo o Zookeeper |Especifique los nodos (**Principal**, **Trabajo** o **Zookeeper**) en los que se ejecuta el script de personalización. |
    | Parámetros |Especifique los parámetros, si lo requiere el script. |

    Use la entrada __Continuar con esta acción de script__ para asegurarse de que el script se aplica a los nodos de trabajo al escalar el clúster.

5. Por último, use el botón **Crear** para aplicar el script al clúster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicación de una acción de script a un clúster en ejecución desde Azure PowerShell

Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para información sobre cómo configurar una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

El ejemplo siguiente muestra cómo aplicar una acción de script a un clúster en ejecución:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Una vez terminada la operación, aparece información similar a la siguiente:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicación de una acción de script a un clúster en ejecución desde la CLI de Azure

Antes de continuar, asegúrese de que ha instalado y configurado la CLI de Azure. Para más información, consulte [Instalación de la CLI de Azure](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Abra una sesión del shell, terminal, símbolo del sistema u otra línea de comandos para el sistema y use el comando siguiente para cambiar al modo de Azure Resource Manager.

        azure config mode arm

2. Use lo siguiente para autenticarse en su suscripción de Azure.

        azure login

3. Use el comando siguiente para aplicar una acción de script a un clúster en ejecución.

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Si omite los parámetros de este comando, se le solicitan. Si el script que especifica con `-u` acepta parámetros, puede especificarlos mediante el parámetro `-p`.

    Los tipos de nodo válidos son `headnode`, `workernode` y `zookeeper`. Si el script se debe aplicar a varios tipos de nodo, especifique los tipos separados por punto y coma (;). Por ejemplo: `-n headnode;workernode`.

    Para guardar el script como persistente, agregue `--persistOnSuccess`. También puede guardar el script como persistente más adelante mediante `azure hdinsight script-action persisted set`.

    Una vez terminado el trabajo, aparece un resultado similar al siguiente texto:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Aplicación de una acción de script a un clúster en ejecución mediante la API de REST

Consulte [Run Script Actions on a running cluster](https://msdn.microsoft.com/library/azure/mt668441.aspx)(Ejecución de acciones de script en un clúster en ejecución).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicación de una acción de script a un clúster en ejecución desde el SDK de .NET para HDInsight

Si desea ver un ejemplo de uso del SDK de .NET para aplicar scripts a un clúster, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Visualización del historial, promover y disminuir de nivel acciones de script

### <a name="using-the-azure-portal"></a>Uso del portal de Azure

1. En el [Portal de Azure](https://portal.azure.com), seleccione el clúster de HDInsight.

2. En la hoja del clúster de HDInsight, seleccione el icono **Acciones de script**.

    ![Icono Acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > También puede seleccionar **Toda la configuración** y, luego, **Acciones de script** en la hoja Configuración.

4. Aparece un historial de scripts de este clúster en la hoja Acciones de script. Esta información incluye una lista de scripts persistentes. En la siguiente captura de pantalla puede ver que el script Solr se ha ejecutado en este clúster. La captura de pantalla no muestra ningún script persistente.

    ![Hoja Acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Al seleccionar un script del historial se muestra la hoja Propiedades de ese script. En la parte superior de la hoja, puede volver a ejecutar el script o subirlo de nivel.

    ![Hoja de propiedades de acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. También puede usar **...** a la derecha de las entradas de la hoja Acciones de script para realizar acciones.

    ![Acciones de script... uso](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Uso de Azure PowerShell

| Use el código siguiente... | Para ... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Recuperar información sobre acciones de script persistentes |
| Get-AzureRmHDInsightScriptActionHistory |Recuperar un historial de acciones de script aplicadas al clúster o detalles de un script específico |
| Set-AzureRmHDInsightPersistedScriptAction |Promover una acción de script ad hoc a una acción de script persistente |
| Remove-AzureRmHDInsightPersistedScriptAction |Disminuir de nivel una acción de script persistente a una acción ad hoc |

> [!IMPORTANT]
> El empleo de `Remove-AzureRmHDInsightPersistedScriptAction` no deshace las acciones realizadas por un script. Este cmdlet solo quita la marca persistente.

El siguiente script de ejemplo muestra cómo utilizar los cmdlets para promover y luego disminuir de nivel un script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

| Use el código siguiente... | Para ... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Recuperar una lista de las acciones de script persistentes |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Recuperar información sobre una acción de script persistente específica |
| `azure hdinsight script-action history list <clustername>` |Recuperar un historial de acciones de script aplicadas al clúster |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Recuperar información sobre una acción de script específica |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promover una acción de script ad hoc a una acción de script persistente |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Disminuir de nivel una acción de script persistente a una acción ad hoc |

> [!IMPORTANT]
> El empleo de `azure hdinsight script-action persisted delete` no deshace las acciones realizadas por un script. Este cmdlet solo quita la marca persistente.

### <a name="using-the-hdinsight-net-sdk"></a>Uso del SDK de .NET de HDInsight

Si desea ver un ejemplo de uso del SDK de .NET para recuperar el historial de scripts de un clúster, o bien promover scripts o disminuir el nivel de estos, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> En este ejemplo también muestra cómo instalar una aplicación de HDInsight mediante el SDK. de NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Soporte técnico para el software de código abierto utilizado en clústeres de HDInsight

El servicio Microsoft Azure HDInsight usa un ecosistema de tecnologías de código abierto formado en torno a Hadoop. Microsoft Azure proporciona un nivel general de soporte técnico para las tecnologías de código abierto. Para más información, vea la sección de **ámbito de soporte técnico** del [sitio web Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). Además, el servicio de HDInsight ofrece un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Hay dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

* **Componentes integrados** : estos componentes están instalados previamente en clústeres de HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, el administrador de recursos de YARN, el lenguaje de consulta Hive (HiveQL) y la biblioteca Mahout pertenecen a esta categoría. Hay una lista completa de componentes del clúster disponible en [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md).
* **Componentes personalizados** : el usuario del clúster puede instalar o usar en la carga de trabajo cualquier componente que esté disponible en la comunidad o que haya creado personalmente.

> [!WARNING]
> Los componentes proporcionados con el clúster de HDInsight son totalmente compatibles. Soporte técnico de Microsoft ayuda a aislar y a solucionar problemas relacionados con estos componentes.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Los proyectos de Apache también tienen sitios de proyecto en [http://apache.org](http://apache.org) (por ejemplo, [Hadoop](http://hadoop.apache.org/)).

El servicio HDInsight proporciona varias maneras de utilizar los componentes personalizados. Se aplica el mismo nivel de soporte técnico independientemente de cómo se use un componente o cómo se instale en el clúster. La siguiente lista describe las formas más comunes de usar los componentes personalizados en clústeres de HDInsight:

1. Envío de trabajos: se pueden enviar al clúster trabajos de Hadoop o de otros tipos que ejecuten o usen componentes personalizados.

2. Personalización del clúster: durante la creación del clúster puede especificar una configuración adicional y componentes personalizados que se instalan en los nodos del clúster.

3. Muestras: para los componentes personalizados más populares, Microsoft y otros pueden proporcionar muestras de cómo estos componentes se pueden utilizar en los clústeres de HDInsight. Estas muestras se proporcionan sin soporte técnico.

## <a name="troubleshooting"></a>Solución de problemas

Puede usar la interfaz de usuario web de Ambari para ver la información registrada por las acciones de script. Si se produce un error en el script durante la creación del clúster, los registros también están disponibles en la cuenta de almacenamiento predeterminada asociada al clúster. Esta sección proporciona información sobre cómo recuperar los registros mediante ambas opciones.

### <a name="using-the-ambari-web-ui"></a>Uso de la interfaz de usuario web de Ambari

1. Abra https://CLUSTERNAME.azurehdinsight.net en el explorador. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.

    Cuando se le solicite, escriba el nombre de cuenta de administrador (admin) y la contraseña de administrador para el clúster. Es posible que tenga que volver a escribir las credenciales de administrador en un formulario web.

2. En la barra de la parte superior de la página, seleccione la entrada **ops**. Se muestra una lista de las operaciones actuales y anteriores realizadas en el clúster mediante Ambari.

    ![Barra de interfaz de usuario web de Ambari con ops seleccionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Busque las entradas que tienen **run\_customscriptaction** en la columna **Operaciones**. Estas entradas se crean al ejecutarse las acciones de script.

    ![Captura de pantalla de operaciones](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Para ver la salida STDOUT y STDERR, seleccione la entrada run\customscriptaction y profundice mediante los vínculos. Esta salida se genera cuando se ejecuta el script y puede contener información útil.

### <a name="access-logs-from-the-default-storage-account"></a>Acceso a los registros desde la cuenta de almacenamiento predeterminada

Si se produce un error en la creación del clúster debido a un error de acción de script, es posible acceder a los registros desde la cuenta de almacenamiento predeterminada.

* Los registros de almacenamiento están disponibles en `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Captura de pantalla de operaciones](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    En este directorio, los registros se organizan por separado para el nodo principal, el nodo de trabajo y el nodo de Zookeeper. A continuación, se indican algunos ejemplos:

    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Nodo de trabajo** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Nodo Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos los stdout y stderr del host correspondiente se cargan en la cuenta de almacenamiento. Hay un archivo **output-\*.txt** and **errors-\*.txt** para cada acción de script. El archivo de output-*.txt contiene información sobre el identificador URI del script que se ejecuta en el host. Por ejemplo:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Es posible crear repetidamente un clúster de la acción de script con el mismo nombre. En tal caso, puede distinguir los registros relevantes según el nombre de la carpeta de fecha. Por ejemplo, la estructura de carpetas de un clúster (mycluster) creado en diferentes fechas es similar a las siguientes entradas de registro:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Si crea un clúster de acción de script con el mismo nombre en el mismo día, puede usar el prefijo único para identificar los archivos de registro correspondientes.

* Si crea un clúster al final del día, es posible que los archivos de registro abarquen dos días. En tales casos, ve dos carpetas de fecha diferentes para el mismo clúster.

* La carga de los archivos de registro en el contenedor predeterminado puede tardar hasta 5 minutos, especialmente para grandes clústeres. Por lo tanto, si desea tener acceso a los registros, no debe eliminar inmediatamente el clúster si se produce un error en una acción de script.

### <a name="ambari-watchdog"></a>Guardián Ambari

> [!WARNING]
> No cambie la contraseña del guardián Ambari (hdinsightwatchdog) en el clúster de HDInsight basado en Linux. El cambio de la contraseña de esta cuenta impide ejecutar nuevas acciones de script en el clúster de HDInsight.

### <a name="cannot-import-name-blobservice"></a>No se puede importar el nombre BlobService

__Síntomas__: se produce un error en la acción de script y se muestra un error similar al siguiente ejemplo al ver la operación en Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__: este error se produce si actualiza el cliente de Azure Storage de Python que se incluye con el clúster de HDInsight. HDInsight espera el cliente de Azure Storage 0.20.0.

__Resolución__: para resolver este error, conéctese manualmente a cada nodo de clúster con `ssh` y use el comando siguiente para volver a instalar la versión correcta del cliente de almacenamiento:

```
sudo pip install azure-storage==0.20.0
```

Para obtener información sobre cómo conectarse al clúster mediante SSH, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>El historial no muestra los scripts usados durante la creación del clúster

Si el clúster se creó antes del 15 de marzo de 2016, puede que no vea una entrada en el historial de acciones de script. Si cambia el tamaño del clúster después del 15 de marzo de 2016, los scripts usados durante la creación del clúster aparecen en el historial, ya que se aplican a los nuevos nodos del clúster como parte de la operación de cambio de tamaño.

Hay dos excepciones:

* Si el clúster se creó antes del 1 de septiembre de 2015. Esta es la fecha en que se presentaron las acciones de script. Cualquier clúster creado con anterioridad a esta fecha no ha podido usar acciones de script para su creación.

* Si ha usado varias acciones de script durante la creación del clúster y ha usado el mismo nombre para varios scripts, o el mismo nombre y el mismo identificador URI, pero diferentes parámetros para varios scripts. En estos casos, aparece el siguiente error:

    No se puede ejecutar ninguna nueva acción de script en este clúster debido al conflicto con los nombres de los scripts existentes. Los nombres de script proporcionados en la creación del clúster deben ser únicos. Los scripts existentes se siguen ejecutando a pesar del cambio de tamaño.

## <a name="next-steps"></a>Pasos siguientes

* [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Adición de almacenamiento adicional a un clúster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fases durante la creación del clúster"

