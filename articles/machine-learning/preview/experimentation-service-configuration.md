---
title: "Configuración del servicio Experimentación de Azure Machine Learning | Microsoft Docs"
description: "En este artículo se proporciona una descripción general de alto nivel del servicio Experimentación de Azure Machine Learning con instrucciones acerca de cómo configurarlo."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: bd152cc79c08124a1acab2aefc8652c7d162ea2c
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Configuración del servicio Experimentación de Azure Machine Learning

## <a name="overview"></a>Información general
El servicio Experimentación de Azure Machine Learning permite a los científicos de datos ejecutar sus experimentos mediante las funcionalidades de ejecución y administración de ejecuciones de Azure Machine Learning. Proporciona un marco para la experimentación ágil con iteraciones rápidas. Azure Machine Learning Workbench le permite iniciar las ejecuciones locales en su equipo y le ofrece una ruta de acceso sencilla para realizar un escalado vertical y horizontal a otros entornos, como las máquinas virtuales remotas de ciencia de datos o los clústeres de HDInsight que ejecutan Spark.

El servicio Experimentación se ha creado para proporcionar ejecuciones aisladas, reproducibles y coherentes de sus experimentos. Le ayuda a administrar los destinos de proceso, los entornos de ejecución y las configuraciones de ejecución. Mediante el uso de las funcionalidades de ejecución y administración de ejecuciones de Azure Machine Learning Workbench es posible moverse fácilmente entre los distintos entornos. 

Los scripts de Python o PySpark se pueden ejecutar en un proyecto de Workbench localmente o a escala en la nube. 

Puede ejecutar los scripts en: 

* Un entorno de Python (3.5.2) del equipo local instalado por Workbench.
* Entorno de conda en Python dentro de un contenedor de Docker en el equipo local.
* Entorno de conda en Python dentro de un contenedor de Docker en una máquina remota Linux. Por ejemplo, una [DSVM basada en Ubuntu en Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).
* [HDInsight para Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) en Azure

>[!IMPORTANT]
>Actualmente, el servicio Experimentación de Machine Learning admite Python 3.5.2 y Spark 2.1.11 como versiones de runtime de Python y Spark, respectivamente. 


### <a name="key-concepts-in-experimentation-service"></a>Conceptos clave del servicio Experimentación
Es importante conocer los conceptos siguientes en la ejecución de experimentos de Azure Machine Learning. En las secciones siguientes, se describe cómo usar estos conceptos en detalle. 

#### <a name="compute-target"></a>Destino de proceso
Un _destino de proceso_ especifica dónde se va a ejecutar su programa, como por ejemplo en su escritorio, en Docker remoto en una máquina virtual o en un clúster. Un destino de proceso debe ser direccionable y accesible para el usuario. Workbench le ofrece la capacidad de crear destinos de proceso y administrarlos mediante la aplicación Workbench y la CLI. 

El comando _az ml computetarget attach_ en CLI le permite crear un destino de proceso que puede usar en las ejecuciones.

Los destinos de proceso admitidos son:
* Un entorno de Python (3.5.2) local de su equipo instalado por Workbench.
* Docker local en el equipo.
* Docker remoto en máquinas virtuales de Ubuntu en Linux. Por ejemplo, una [DSVM basada en Ubuntu en Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).
* [HDInsight para clúster Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) en Azure

El servicio Experimentación admite actualmente Python 3.5.2 y Spark 2.1.11 como versiones de runtime de Python y Spark, respectivamente. 

>[!IMPORTANT]
> Las máquinas virtuales de Windows que ejecutan Docker **no** se admiten como destinos de proceso remotos.

#### <a name="execution-environment"></a>Entorno de ejecución
El _entorno de ejecución_ define la configuración de runtime y las dependencias necesarias para ejecutar el programa en Workbench.

El entorno de ejecución local lo administra mediante sus herramientas y administradores de paquetes favoritos si se ejecuta en el runtime predeterminado de Workbench. 

Conda se utiliza para administrar ejecuciones locales y remotas de Docker, así como ejecuciones basadas en HDInsight. En el caso de estos destinos de proceso, la configuración del entorno de ejecución se administra a través de los archivos **Conda_dependencies.yml** y **Spark_dependencies.yml**. Estos archivos se encuentran en la carpeta **aml_config** del proyecto.

**Los runtimes compatibles para entornos de ejecución son:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Configuración de ejecución
Además del destino de proceso y del entorno de ejecución, Azure Machine Learning proporciona un marco para definir y cambiar las *configuraciones de ejecución*. Las distintas ejecuciones de su experimento pueden requerir una configuración diferente como parte de la experimentación iterativa. Puede abarcar distintos intervalos de parámetros, usar diferentes orígenes de datos y ajustar parámetros de Spark. El servicio Experimentación proporciona un marco para administrar configuraciones de ejecución.

Ejecutar el comando _az ml computetarget attach_ crea dos archivos en la carpeta **aml_config** del proyecto: uno .compute y uno .runconfig que siguen esta convención: _<nombre_destinoproceso>.compute_ y _<nombre_destinoproceso>.runconfig_. Para su comodidad, el archivo .runconfig se crea automáticamente cuando crea un destino de proceso. Puede crear y administrar otras configuraciones de ejecución mediante el comando _az ml runconfigurations_ de CLI. También puede crearlas y editarlas en el sistema de archivos.

La configuración de ejecución de Workbench también le permite especificar variables de entorno. Puede especificar variables de entorno y usarlas en el código si agrega la sección siguiente al archivo .runconfig. 

```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Puede accederse a estas variables de entorno en el código. Por ejemplo, este fragmento de código de phyton imprime la variable de entorno denominada “EXAMPLE_ENV_VAR1”.
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**En la siguiente ilustración se muestra el flujo de alto nivel para ejecutar el experimento inicial.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Escenarios de ejecución de experimentos
En esta sección, se profundiza en los escenarios de ejecución y se proporciona información acerca de la forma en que Azure Machine Learning ejecuta los experimentos; en concreto, la ejecución de un experimento localmente, en una máquina virtual remota y en un clúster de HDInsight. Esta sección es un tutorial que va de la creación de un destino de proceso a la ejecución de experimentos.

>[!NOTE]
>Durante el resto de este artículo, se usarán los comandos de CLI (interfaz de línea de comandos) para mostrar los conceptos y las funcionalidades. Las funcionalidades que se describen aquí también pueden utilizarse desde Workbench.

## <a name="launching-the-cli"></a>Inicio de la CLI
Una manera fácil de iniciar la CLI es abrir un proyecto Workbench y navegar a **Archivo-->Abrir símbolo del sistema**.

![](media/experimentation-service-configuration/opening-cli.png)

Este comando inicia una ventana de terminal en la que puede escribir comandos para ejecutar scripts en la carpeta del proyecto actual. Esta ventana de terminal está configurada con el entorno de Python 3.5.2, que se instala mediante Workbench.

>[!NOTE]
> Al ejecutar cualquier comando _az ml_ desde la ventana de comandos, debe autenticarse en Azure. La CLI utiliza una caché de autenticación independiente y luego la aplicación de escritorio, por lo que iniciar sesión en Workbench no significa que está autenticado en el entorno de CLI. Para autenticarse, siga los pasos a continuación. El token de autenticación se almacena en caché localmente durante un período para que solo tenga que repetir estos pasos cuando expire el token. Cuando el token expira o si recibe errores de autenticación, ejecute los comandos siguientes:

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Al ejecutar el comando _az ml_ en una carpeta del proyecto, asegúrese de que el proyecto pertenece a una cuenta de Experimentación de Machine Learning en la suscripción _actual_ de Azure. En caso contrario, podría experimentar errores de ejecución.


## <a name="running-scripts-and-experiments"></a>Ejecución de scripts y experimentos
Con Workbench, puede ejecutar scripts de Python y PySpark en distintos destinos de proceso mediante el comando _az ml experiment submit_. Este comando requiere una definición de configuración de ejecución. 

Workbench crea un archivo .runconfig correspondiente cuando crea un destino de proceso, pero puede crear configuraciones de ejecución adicionales mediante el comando _az ml runconfiguration create_. También puede editar manualmente los archivos de configuración de ejecución.

Las configuraciones de ejecución se muestran como parte de la experiencia de ejecución de experimentos en Workbench. 

>[!NOTE]
>Para aprender más sobre el archivo de configuración de ejecución, consulte la sección de [Referencia sobre configuración de ejecución de experimentos](experimentation-service-configuration-reference.md).

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Ejecución local de un script en un runtime instalado en Workbench
Workbench le permite ejecutar scripts directamente en el runtime de Python 3.5.2 instalado en Workbench. Este runtime predeterminado se instala en momento de la configuración de Workbench e incluye las dependencias y bibliotecas de Azure Machine Learning. Los resultados de ejecución y los artefactos para ejecuciones locales se siguen guardando en el servicio de historial de ejecución en la nube.

A diferencia de las ejecuciones basadas en Docker, Conda _no_ administra esta configuración. Debe aprovisionar manualmente las dependencias de los paquetes en su entorno local de Python de Workbench.

Puede usar el siguiente comando para ejecutar el script localmente en el entorno de Python instalado en Workbench. 

```
$az ml experiment submit -c local myscript.py
```

Para encontrar la ruta al entorno de Python predeterminado, puede escribir el siguiente comando en la ventana de la CLI de Workbench.
```
$ conda env list
```

>[!NOTE]
>Actualmente **no** se admite la ejecución local de PySpark directamente frente a entornos locales de Spark. Workbench es compatible con los scripts de PySpark que se ejecutan en Docker localmente. La imagen de Docker básica de Azure Machine Learning viene con Spark 2.1.11 preinstalado. 

_**Información general de ejecución local para un script de Python:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Ejecución de un script en Docker local
También puede ejecutar los proyectos en un contenedor de Docker en el equipo local mediante el servicio Experimentación. Workbench proporciona una imagen de Docker básica que incluye las bibliotecas de Azure Machine Learning, así como el runtime de Spark 2.1.11 para facilitar las ejecuciones locales de Spark. Es necesario que Docker ya se esté ejecutando en el equipo local.

Para ejecutar el script de Python o PySpark en Docker local, puede ejecutar los siguientes comandos en CLI.

```
$az ml experiment submit -c docker myscript.py
```
o
```
az ml experiment submit --run-configuration docker myscript.py
```

El entorno de ejecución en Docker local se prepara mediante la imagen de Docker básica de Azure Machine Learning. Workbench descarga esta imagen la primera vez que se ejecuta por primera vez y la superpone a los paquetes especificados en su archivo conda_dependencies.yml. Esta operación hace que la ejecución inicial sea más lenta, pero las ejecuciones posteriores son considerablemente más rápidas gracias a que Workbench reutiliza capas almacenadas en caché. 

>[!IMPORTANT]
>Debe ejecutar el comando _az ml experiment prepare -c docker_ primero para preparar la imagen de Docker para la primera ejecución. También puede establecer el parámetro **PrepareEnvironment** en true en el archivo docker.runconfig. Esta acción prepara automáticamente el entorno como parte de la ejecución.  

>[!NOTE]
>Si ejecuta un script de PySpark en Spark, spark_dependencies.yml también se utiliza, además de conda_dependencies.yml.

Ejecutar los scripts en una imagen de Docker ofrece las siguientes ventajas:

1. Se asegura de que se puede ejecutar el script de forma confiable en otros entornos de ejecución. Ejecutar en un contenedor de Docker ayuda a detectar y evitar cualquier referencia local que pueda afectar a la portabilidad. 

2. Permite probar rápidamente el código en runtimes y marcos que son difíciles de instalar y configurar, como Apache Spark, sin tener que instalarlos usted mismo.


_**Información general de ejecución de Docker local para un script de Python:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Ejecución de un script en Docker remoto
En algunos casos, puede que los recursos disponibles en su equipo local no sean suficientes para entrenar el modelo deseado. En esta situación, el servicio Experimentación permite una manera sencilla de ejecutar los scripts de Python o PySpark en máquinas virtuales más potentes mediante la ejecución en Docker remoto. 

La máquina virtual remota debe cumplir los requisitos siguientes:
* La máquina virtual remota debe ejecutar Ubuntu en Linux y debe ser accesible a través de SSH. 
* La máquina virtual remota debe tener Docker en ejecución.

>[!IMPORTANT]
> Las máquinas virtuales de Windows que ejecutan Docker **no** se admiten como destinos de proceso remotos.


Puede utilizar el siguiente comando para crear tanto en la definición del destino de proceso como en la configuración de ejecución para las ejecuciones basadas en Docker remoto.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

Una vez configurado el destino de proceso, puede usar el siguiente comando para ejecutar el script.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Tenga en cuenta que el entorno de ejecución está configurado con las especificaciones de conda_dependencies.yml. spark_dependencies.yml también se usa si se especifica el marco de PySpark en el archivo .runconfig. 

El proceso de construcción de Docker para máquinas virtuales remotas es exactamente el mismo que el proceso para ejecuciones de Docker local, por lo que debe esperar una experiencia de ejecución similar.

>[!TIP]
>Si prefiere evitar la latencia que genera la creación de la imagen de Docker para la primera ejecución, puede usar el comando siguiente para preparar el destino de proceso antes de ejecutar el script. az ml experiment prepare -c remotedocker


_**Información general de ejecución de máquina virtual remota para un script de Python:**_
![](media/experimentation-service-configuration/remote-vm-run.png)


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Ejecución de un script en un clúster de HDInsight
HDInsight es una plataforma popular para el análisis de macrodatos compatibles con Apache Spark. Workbench permite la experimentación con macrodatos mediante clústeres de Spark en HDInsight. 

>[!NOTE]
>El clúster de HDInsight debe usar Azure Blob como almacenamiento principal. Aún no se admite el almacenamiento de Azure Data Lake.

Puede crear un destino de proceso y configuración de ejecución para un clúster de Spark en HDInsight con el comando siguiente:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Si usa el FQDN en lugar de una dirección IP y el clúster Spark de HDI se denomina _foo_, el punto de conexión SSH se encuentra en el nodo de controlador denominado _foo-ssh.azurehdinsight.net_. No olvide el posfijo **-ssh** en el nombre del servidor cuando use el FQDN para el parámetro _--address_.


Una vez que tenga el contexto de proceso, puede ejecutar el siguiente comando para ejecutar el script de PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench prepara y administra el entorno de ejecución en un clúster de HDInsight mediante Conda. La configuración se administra mediante los archivos de configuración _conda_dependencies.yml_ y _spark_dependencies.yml_. 

Para ejecutar experimentos de este modo, necesita acceso de SSH al clúster de HDInsight. 

>[!NOTE]
>La configuración admitida son clústeres de Spark en HDInsight que se ejecutan en Linux (Ubuntu con Python/PySpark 3.5.2 y Spark 2.1.11).

_**Información general de ejecución basada en HDInsight para un script de PySpark**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Ejecución de un script en la GPU
Para ejecutar los scripts en la GPU, puede seguir las instrucciones de este artículo:[Uso de GPU en Azure Machine Learning](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Uso de la autenticación basada en claves SSH para crear y utilizar destinos de proceso
Azure Machine Learning Workbench le permite crear y usar destinos de proceso mediante autenticación basada en claves SSH además del esquema basado en nombre de usuario y contraseña. Puede usar esta funcionalidad al utilizar remotedocker o cluster como destino de proceso. Cuando se usa este esquema, Workbench crea un par de claves pública y privada e informa de la clave pública. La clave pública se anexa a los archivos ~/.ssh/authorized_keys para su nombre de usuario. Azure Machine Learning Workbench usa a continuación la autenticación basada en claves SSH para acceder a este destino de proceso y ejecutarlo. Puesto que la clave privada para el destino de proceso se guarda en el almacén de claves para el área de trabajo, otros usuarios del área de trabajo pueden usar el destino de proceso del mismo modo proporcionando el nombre de usuario especificado para crear el destino de proceso.  

Siga estos pasos para utilizar esta funcionalidad. 

- Cree un destino de proceso con uno de los siguientes comandos.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
o
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Anexe la clave pública generada por Workbench al archivo ~/.ssh/authorized_keys del destino de proceso adjunto. 

>[!IMPORTANT]
>Deberá iniciar sesión en el destino de proceso con el mismo nombre de usuario utilizado para crear el destino de proceso. 

- Ahora puede preparar y usar el destino de proceso mediante autenticación basada en claves SSH.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>Pasos siguientes
* [Crear e instalar Azure Machine Learning](quickstart-installation.md)
* [Administración de modelos](model-management-overview.md)
