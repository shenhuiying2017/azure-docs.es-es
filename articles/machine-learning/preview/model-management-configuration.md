---
title: "Instalación y configuración de Administración de modelos de Azure Machine Learning| Microsoft Docs"
description: "Este documento describe los pasos y conceptos implicados en la instalación y configuración de Administración de modelos de Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 151e7c2dc808a8fa117a0d7a1950185abe9e3152
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="model-management-setup"></a>Instalación de Administración de modelos

## <a name="overview"></a>Información general
Este documento ofrece una introducción al uso de Administración de modelos de Azure Machine Learning para implementar y administrar sus modelos de aprendizaje automático como servicios web. 

Con Administración de modelos de Azure Machine Learning, puede implementar y administrar de forma eficiente modelos de aprendizaje automático que se han compilado utilizando un número de plataformas entre las que se incluyen SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit o Python. 

Al final de este documento, debe tener el entorno de administración de modelo configurado y listo para implementar los modelos de aprendizaje automático.

## <a name="what-you-need-to-get-started"></a>Qué necesita para empezar
Para obtener el máximo partido de esta guía, debe tener acceso de propietario a una suscripción de Azure en la que pueda implementar los modelos.
La CLI viene preinstalada en Azure Machine Learning Workbench y en las instancias de [Azure DSVM](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Uso de la CLI
Para usar las interfaces de línea de comandos (CLI) desde Workbench, haga clic en **Archivo** -] **Abrir interfaz de la línea de comandos**. 

En una instancia de Data Science Virtual Machine, conecte y abra el símbolo del sistema. Escriba `az ml -h` para ver las opciones. Para más información sobre los comandos, utilice la marca --help.

En todos los demás sistemas, deberá instalar las CLI.

### <a name="installing-or-updating-on-windows"></a>Instalación (o actualización) en Windows

Instale Python desde https://www.python.org/. Asegúrese de que ha seleccionado instalar pip.

Abra un símbolo del sistema utilizando Ejecutar como administrador y luego ejecute los siguientes comandos:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Si tiene una versión anterior, desinstálela primero mediante el comando siguiente:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Instalación (o actualización) en Linux
Ejecute el siguiente comando desde la línea de comando y siga las indicaciones:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Una vez completada la instalación, ejecute el siguiente comando:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Cierre la sesión y vuelva a iniciar sesión en la sesión SSH para que los cambios surtan efecto.
>Puede usar los comandos anteriores para actualizar una versión anterior de la CLI en la instancia de DSVM.
>

## <a name="deploying-your-model"></a>Implementación del modelo
Use la CLI para implementar modelos como servicios web. Los servicios web pueden implementarse de forma local o en un clúster.

Comience con una implementación local, valide el funcionamiento del modelo y el código y, a continuación, implemente en un clúster para su uso en la escala de producción.

Para empezar, tiene que configurar el entorno de implementación. La configuración del entorno es una tarea que se realiza una sola vez. Una vez completada la instalación, puede volver a usar el entorno para las implementaciones posteriores. Consulte la siguiente sección para más información.

Cuando realice la configuración del entorno:
- Se le pide que inicie sesión en Azure. Para iniciar sesión use un explorador web para abrir la página https://aka.ms/devicelogin y escriba el código que se le ha proporcionado para autenticarse.
- Durante el proceso de autenticación, el sistema solicita una cuenta con la que autenticar. Importante: seleccione una cuenta que tenga una suscripción válida de Azure y los permisos necesarios para crear recursos en la cuenta. Cuando se haya realizado el inicio de sesión, se presenta la información de suscripción y se le pregunta si desea continuar con la cuenta seleccionada.

### <a name="environment-setup"></a>Configuración del entorno
Para iniciar el proceso de configuración, tiene que registrar al proveedor de entorno escribiendo el comando siguiente:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Implementación local
Para implementar y probar el servicio web en el equipo local, configure un entorno local con el comando siguiente:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>La implementación del servicio web local requiere que instale Docker en la máquina local. 
>

El comando de instalación de entorno local crea los siguientes recursos en su suscripción:
- Un grupo de recursos (si no se ha proporcionado)
- Una cuenta de almacenamiento
- Una instancia de Azure Container Registry (ACR)
- Application Insights

Cuando el programa de instalación finalice correctamente, establezca el entorno que se usará con el comando siguiente:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Implementación del clúster
Utilice la implementación de clúster para escenarios de producción de gran escala. Configura un clúster de ACS con Kubernetes como el orquestador. El clúster de ACS se puede escalar horizontalmente para controlar un mayor rendimiento para las llamadas de servicio web.

Para implementar el servicio web en un entorno de producción, primero configure el entorno con el comando siguiente:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

El comando de instalación de entorno del clúster crea los siguientes recursos en su suscripción:
- Un grupo de recursos (si no se ha proporcionado)
- Una cuenta de almacenamiento
- Una instancia de Azure Container Registry (ACR)
- Una implementación de Kubernetes en un clúster de Azure Container Service (ACS)
- Application Insights

El grupo de recursos, la cuenta de almacenamiento y el ACR se crean rápidamente. La implementación de ACS puede tardar hasta 20 minutos. 

Una vez completada la instalación, tiene que establecer el entorno que se usará para esta implementación. Use el comando siguiente:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> Una vez creado el entorno, para las implementaciones posteriores, bastará con usar el comando anterior para volver a usarlo.
>

>[!NOTE] 
>Para crear un punto de conexión HTTPS, especifique un certificado SSL cuando cree un clúster mediante las opciones --cert-name y --cert-pem en la configuración de az ml env. Esto configura el clúster para atender solicitudes en https, protegidas mediante el certificado proporcionado. Una vez completada la instalación, cree un registro CNAME DNS que apunte al FQDN del clúster.

### <a name="create-an-account"></a>Creación de una cuenta
Una cuenta es necesaria para la implementación de modelos. Tiene que hacer esto una vez por cada cuenta y puede volver a usar la misma cuenta en varias implementaciones.

Para crear una nueva cuenta utilice el comando siguiente:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Para usar una cuenta existente utilice el comando siguiente:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Implementación del modelo
Ahora está listo para implementar el modelo guardado como un servicio web. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Pasos siguientes
Pruebe uno de los muchos ejemplos de la galería.
