---
title: "Creación de DSVM y HDI como destinos de procesos en Azure Machine Learning"
description: "Creación de DSVM y el clúster de HDI Spark como destinos de procesos para la experimentación en Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 2560ca144f8ce4041aa592554f9945ed546cc49b
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>Creación de DSVM y el clúster de HDI Spark como destinos de procesos

Mediante la incorporación de destinos de procesos adicionales como DSVM (Data Science Virtual Machine) basado en Ubuntu y Apache Spark para el clúster de Azure HDInsight es fácil escalar vertical y horizontalmente el experimento de aprendizaje automático. Este artículo le guía por los pasos necesarios para crear dichos destinos de procesos en Azure. Para más información acerca de los destinos de procesos de Azure Machine Learning, consulte [Configuring Azure Machine Learning Experimentation Service](experimentation-service-configuration.md) (Configuración del servicio Experimentación de Azure Machine Learning).

>[!NOTE]
>Antes de empezar debe asegurarse de que tiene los permisos adecuados para crear recursos como clústeres HDI y máquinas virtuales en Azure. Tenga también en cuenta que ambos recursos pueden consumir muchos núcleos de proceso, en función de la configuración. Asegúrese de que su suscripción tiene suficiente capacidad para los núcleos de CPU virtuales. Siempre puede poner en contacto con el soporte técnico de Azure para aumentar el número máximo de núcleos que se permiten en su suscripción.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Creación de un DSVM Ubuntu en Azure Portal

En Azure Portal se puede crear un DSVM. 

1. Inicie sesión en Azure Portal desde https://portal.azure.com
2. Haga clic en el vínculo **+Nuevo** y busque "data science virtual machine for Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Elija **Data Science Virtual Machine for Linux (Ubuntu)** en la lista y siga las instrucciones en pantalla para crear la DSVM.

>[!IMPORTANT]
>Asegúrese de elegir **Contraseña** en _Tipo de autenticación_.

![usar contraseña](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Creación de un DSVM Ubuntu mediante azure-cli

Para implementar un DSVM también se puede usar una plantilla de administración de recursos de Azure.

>[!NOTE]
>Se asume que todos los comandos se emiten desde la carpeta raíz de un proyecto de Azure Machine Learning.

En primer lugar, cree un archivo `mydsvm.json` con el editor de texto que prefiera en la carpeta `docs` (si no tiene una carpeta `docs` en la carpeta raíz del proyecto, créela). Este archivo se usa para configurar algunos parámetros básicos de la plantilla de administración de recursos de Azure. 

Copie y pegue el siguiente fragmento de código JSON en el archivo `mydsvm.json` y rellene los valores adecuados:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Para el campo _vmSize_, puede usar cualquier tamaño de máquina virtual compatible que se indique en la [plantilla de administración de recursos de Azure de DSVM Ubuntu](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Es aconsejable usar uno de los siguiente tamaños como destinos de proceso de Azure Machine Learning. 


>[!TIP]
> Para [cargas de trabajo de aprendizaje profundo](how-to-use-gpu.md) puede realizar la implementación en máquinas virtuales con tecnología GPU.

- [Máquinas virtuales de propósito general](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [Máquinas virtuales con tecnología GPU](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

Más información acerca de estos [tamaños de máquinas virtuales Linux en Azure](../../virtual-machines/linux/sizes.md) y sus [precios](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Inicie la ventana de la CLI desde la aplicación Azure ML Workbench, para lo que debe hacer clic en el elemento de menú **Archivo** --> **Abrir símbolo del sistema** o **Abrir PowerShell**. 

>[!NOTE]
>También puede hacerlo en cualquier entorno de línea de comandos en los que az-cli esté instalado.

En la ventana de línea de comandos, escriba los siguiente comandos:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>Adjuntar un destino de procesos de DSVM
Una vez creado DSVM, se puede adjuntar a un proyecto de Azure Machine Learning.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Ya debería estar listo para realizar experimentos en DSVM.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Desasignación de DSVM y su posterior reinicio
Cuando haya terminado las tareas de proceso de Azure Machine Learning, puede desasignar DSVM. Esta acción apaga la máquina virtual, libera los recursos de proceso, pero conserva los discos virtuales. Cuando la máquina virtual se desasigne, no se cobrará el costo del proceso.

Para desasignar una máquina virtual:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

Para volver a iniciar la máquina virtual, use el comando `az ml start`:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Expansión del disco del SO de DSVM
Una máquina virtual Linux en Azure suele contener un disco de sistema operativo de 30 GB. Cuando se usa como destino de proceso en Azure Machine Learning, el motor Docker puede consumirla rápidamente al extraer imágenes de Docker y generar capas de sobre ella. Se recomienda expandir el disco del sistema operativo a un tamaño mayor (por ejemplo, 200 GB) para evitar que aparezca el error "disco lleno" en medio de una ejecución. Consulte [Expansión de discos duros virtuales en una máquina virtual Linux mediante la CLI de Azure](../../virtual-machines/linux/expand-disks.md) para aprender a hacerlo fácilmente desde azure-cli. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Creación de un clúster de Apache Spark para Azure HDInsight en Azure Portal

Para ejecutar trabajos de Spark de escalado horizontal, es preciso crear un clúster de Apache Spark para Azure HDInsight en Azure Portal.

1. Inicie sesión en Azure Portal desde https://portal.azure.com
2. Haga clic en el vínculo **+Nuevo** y busque "HDInsight".

    ![buscar hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Elija **HDInsight** en la lista y haga clic en el botón **Crear**.
4. En la pantalla de configuración **Conceptos básicos**, opción **Tipo de clúster**, asegúrese de elegir **Spark** en _Tipo de clúster_, **Linux** en _Sistema operativo_ y **Spark 2.1.0 (HDI 3.6)** en Versión.

    ![configurar hdi](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Observe que en la pantalla anterior el clúster tiene los campos _Nombre de usuario de inicio de sesión del clúster_ y _Nombre de usuario de Secure Shell (SSH)_. Son dos identidades de usuario distintas, aunque por comodidad puede especificar la misma contraseña para ambos inicios de sesión. _Nombre de usuario de inicio de sesión del clúster_ se usa para iniciar sesión en la interfaz de usuario web de administración. _Nombre de usuario de Secure Shell (SSH)_ se usa para iniciar sesión en el nodo principal del clúster, que es lo que hace falta para que Azure Machine Learning envíe trabajos de Spark.

5. Elegir el tamaño de clúster y el tamaño de nodo que necesita, y finalice al Asistente para creación. El clúster puede tardar un máximo de 30 minutos en finalizar el aprovisionamiento. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Adjuntar un destino de procesos de un clúster de HDI Spark

Una vez creado el cluster de Spark HDI, se puede adjuntar a un proyecto de Azure Machine Learning.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Ya debería estar listo para realizar experimentos en este clúster de Spark.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre:
- [Configuring Azure Machine Learning Experimentation Service](experimentation-service-configuration.md) (Configuración del servicio Experimentación de Azure Machine Learning)
- [Azure Machine Learning Workbench experimentation service configuration files](experimentation-service-configuration-reference.md) (Archivos de configuración del servicio Experimentación de Azure Machine Learning Workbench)
- [Apache Spark para HDInsight de Azure](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
