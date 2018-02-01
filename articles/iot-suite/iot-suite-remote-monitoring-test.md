---
title: "Simulación de dispositivos en la solución de supervisión remota: Azure | Microsoft Docs"
description: "En este tutorial se muestra cómo usar el simulador de dispositivos con la solución preconfigurada de la supervisión remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0bf1cff4058bfe46b54f3f0b6836ede3e04ed5dd
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="test-your-solution-with-simulated-devices"></a>Prueba de la solución con dispositivos simulados

En este tutorial se muestra cómo personalizar el microservicio del simulador de dispositivos en la solución preconfigurada de la supervisión remota. Para mostrar las funcionalidades del simulador de dispositivos, en este tutorial se usan dos escenarios en la aplicación IoT de Contoso.

En el primer escenario, Contoso desea probar un nuevo dispositivo de bombilla inteligente. Para realizar las pruebas, crea un dispositivo simulado nuevo con las características siguientes:

*Propiedades*

| NOMBRE                     | Valores                      |
| ------------------------ | --------------------------- |
| Color                    | Blanco, rojo, azul            |
| Brillo               | De 0 a 100                    |
| Vida restante estimada | Cuenta atrás desde 10 000 horas |

*Telemetría*

En la tabla siguiente se muestran los datos de los que la bombilla informa a la nube como flujo de datos:

| NOMBRE   | Valores      |
| ------ | ----------- |
| Status | "on", "off" |
| Temperatura | Grados F |
| online (en línea) | true, false |

> [!NOTE]
> El valor de telemetría **online** (en línea) es obligatorio para todos los tipos simulados.

*Métodos*

La tabla siguiente muestra las acciones que admite el dispositivo nuevo:

| NOMBRE        |
| ----------- |
| Encender   |
| Apagar  |

*Estado inicial*

La tabla siguiente muestra el estado inicial del dispositivo:

| NOMBRE                     | Valores |
| ------------------------ | -------|
| Color inicial            | Blanco  |
| Brillo inicial       | 75     |
| Vida restante inicial   | 10.000 |
| Estado de telemetría inicial | "on"   |
| Temperatura de telemetría inicial | 200   |

En el segundo escenario, agrega un tipo de telemetría nuevo al dispositivo **Refrigerador** existente de Contoso.

En este tutorial se muestra cómo usar el simulador de dispositivos con la solución preconfigurada de la supervisión remota:

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Crear un tipo de dispositivo nuevo
> * Simular el comportamiento de un dispositivo personalizado
> * Agregar un tipo de dispositivo nuevo al panel
> * Enviar telemetría personalizada desde un tipo de dispositivo existente

## <a name="prerequisites"></a>requisitos previos

Para utilizar este tutorial, necesitará:

* Una instancia implementada de la solución de supervisión remota en la suscripción de Azure. Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-deploy.md).

* Visual Studio 2017. Si aún no tiene Visual Studio 2017 instalado, puede descargar y usar la edición gratis de [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/).

* Una extensión de Visual Studio de [Cloud Explorer para Visual Studio de 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview).

* Una cuenta en [Docker Hub](https://hub.docker.com/). Puede registrarse de forma gratuita para empezar a trabajar.

* Tener [GIT](https://git-scm.com/downloads) instalado en la máquina de escritorio.

## <a name="prepare-your-development-environment"></a>Preparación del entorno de desarrollo

Complete las siguientes tareas para preparar el entorno de desarrollo y así poder agregar un nuevo dispositivo simulado a la solución de supervisión remota:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Configurar el acceso SSH para la máquina virtual de la solución en Azure

Cuando cree la solución de supervisión remota en [www.azureiotsuite.com](https://www.azureiotsuite.com), elija un nombre para la solución. El nombre de la solución se convierte en el nombre del grupo de recursos de Azure que contiene los distintos recursos implementados que utiliza la solución. Los siguientes comandos usan un grupo de recursos denominado **Contoso-01**, así que debe reemplazar **Contoso-01** con el nombre de su grupo de recursos.

Los siguientes comandos usan el comando `az` de la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Puede instalar la CLI de Azure 2.0 en la máquina de desarrollo, o usar [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) en [Azure Portal](http://portal.azure.com). La CLI de Azure 2.0 viene preinstalada en Cloud Shell.

1. Para comprobar el nombre del grupo de recursos que contiene los recursos de supervisión remotos, ejecute el siguiente comando:

    ```sh
    az group list | grep "name"
    ```

    Gracias a este comando puede obtener una lista de todos los grupos de recursos de la suscripción. La lista debe incluir un grupo de recursos con el mismo nombre que la solución de supervisión remota.

1. Para que el grupo de recursos sea el grupo predeterminado de los comandos siguientes, ejecute el siguiente comando con el nombre del grupo de recursos en lugar de con **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Para enumerar los recursos del grupo de recursos, ejecute el siguiente comando:

    ```sh
    az resource list -o table
    ```

    Tome nota de los nombres de la máquina virtual y el grupo de seguridad de red. Utilizará estos valores en pasos posteriores.

1. Para habilitar el acceso SSH en la máquina virtual, ejecute el comando siguiente mediante el nombre del grupo de seguridad de red que obtuvo en el paso anterior:

    ```sh
    az network nsg rule create --name SSH --nsg-name your-network-security-group --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Para ver la lista de reglas entrantes de la red, ejecute el siguiente comando:

    ```sh
    az network nsg rule list --nsg-name Contoso-01-nsg -o table
    ```

1. Para cambiar la contraseña de la máquina virtual y establecer una contraseña que ya conozca, ejecute el siguiente comando. Utilice el nombre de la máquina virtual que anotó en el paso anterior y escriba una contraseña de su elección:

    ```sh
    az vm user update --name your-vm-name --username azureuser --password your-password
    ```
1. Para buscar la dirección IP de la máquina virtual, use el comando siguiente y tome nota de la dirección IP pública:

    ```sh
    az vm list-ip-addresses --name your-vm-name
    ```

1. Puede elegir cualquier herramienta SSH para conectarse a la máquina virtual. El comando `ssh` viene preinstalado en Cloud Shell. Use la dirección IP pública del paso anterior y, cuando se le solicite, escriba la contraseña configurada para la máquina virtual:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Ahora tiene acceso al shell de la máquina virtual que ejecuta los contenedores de Docker en la solución de supervisión remota. Para ver los contenedores en ejecución, utilice el siguiente comando:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Buscar las cadenas de conexión de servicio

En el tutorial, trabajará con la solución de Visual Studio que se conecta a los servicios Cosmos DB e IoT Hub de la solución. Los pasos siguientes le mostrarán la manera de encontrar los valores de la cadena de conexión que necesite:

1. Para buscar la cadena de conexión de Cosmos DB, ejecute el siguiente comando en la sesión SSH que está conectada a la máquina virtual:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Anote el nombre de la cadena de conexión. Usará este valor más adelante en el tutorial.

1. Para buscar la cadena de conexión de IoT Hub, ejecute el siguiente comando en la sesión SSH que está conectada a la máquina virtual:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Anote el nombre de la cadena de conexión. Usará este valor más adelante en el tutorial.

> [!NOTE]
> Puede encontrar estas cadenas de conexión en Azure Portal o mediante el comando `az`.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Detener el servicio de simulación de dispositivos en la máquina virtual

Cuando se modifica el servicio de simulación de dispositivos, puede ejecutarlo localmente para probar los cambios. Antes de ejecutar el servicio de simulación de dispositivos de forma local, debe detener la instancia que se esté ejecutando en la máquina virtual tal como se indica a continuación:

1. Para buscar el **ID. DEL CONTENEDOR** del servicio **device-simulation**, ejecute el siguiente comando en la sesión SSH que está conectada a la máquina virtual:

    ```sh
    docker ps
    ```

    Anote el id. del contenedor del servicio **device-simulation**.

1. Para detener el contenedor **device-simulation**, ejecute el siguiente comando:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Clonar los repositorios GitHub

En este tutorial, trabajará con los proyectos de Visual Studio **device-simulation** y **storage-adapter**. Puede clonar los repositorios de código fuente desde GitHub. Realice este paso en la máquina de desarrollo local donde tenga instalado Visual Studio:

1. Abra el símbolo del sistema y desplácese a la carpeta donde desea guardar la copia de los repositorios de GitHub **device-simulation** y **storage-adapter**.

1. Para clonar la versión de .NET del repositorio **device-simulation**, ejecute el siguiente comando:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    El servicio de simulación de dispositivos de la solución de supervisión remota le permite hacer cambios en los tipos integrados de dispositivos simulados y crear nuevos tipos de dispositivos simulados. Puede usar los tipos de dispositivos personalizados para probar el comportamiento de la solución de supervisión remota antes de conectar los dispositivos físicos.

1. Para clonar la versión de .NET del repositorio **storage-adapter**, ejecute el siguiente comando:

    ```cmd
    git clone https://github.com/Azure/storage-adapter.git
    ```

    El servicio de simulación de dispositivos usa el servicio del adaptador de almacenamiento para conectar con el servicio de Cosmos DB en Azure. La solución de supervisión remota almacena los datos de configuración del dispositivo simulado en una base de datos de Cosmos DB.

### <a name="run-the-storage-adapter-service-locally"></a>Ejecutar el servicio del adaptador de almacenamiento de manera local

El servicio de simulación de dispositivos usa el servicio del adaptador de almacenamiento para conectar con la base de datos de Cosmos DB de la solución. Si ejecuta el servicio de simulación de dispositivos de forma local, también debe ejecutar de manera local el servicio del adaptador de almacenamiento. En los pasos siguientes se muestra cómo ejecutar el servicio del adaptador de almacenamiento desde Visual Studio:

1. En Visual Studio, abra el archivo **pcs-storage-adapter.sln** de la solución en el clon local del repositorio **storage-adapter**.

1. En el Explorador de soluciones, haga clic en el proyecto **WebService**, elija **Propiedades** y, a continuación, **Depurar**.

1. En la sección **Variables de entorno**, edite el valor de la variable **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** para que sea la cadena de conexión de Cosmos DB que anotó anteriormente. A continuación, guarde los cambios.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **WebService**, elija **Depurar** y, a continuación, **Iniciar nueva instancia**.

1. El servicio comienza a ejecutarse de manera local y abre `http://localhost:9022/v1/status` en el explorador predeterminado. Compruebe que el valor del **estado** es "Activo y correcto".

1. Deje que el servicio del adaptador de almacenamiento siga ejecutándose de forma local hasta que haya completado el tutorial.

Ya tiene todo en su lugar y está listo para comenzar a agregar nuevos tipos de dispositivos simulados en la solución de supervisión remota.

## <a name="create-a-simulated-device-type"></a>Creación de un tipo de dispositivo simulado

La forma más sencilla de crear un tipo de dispositivo nuevo en el servicio de simulación de dispositivos es copiar y modificar un tipo existente. Los pasos siguientes muestran cómo copiar el dispositivo **Refrigerador** integrado para crear un dispositivo **Bombilla** nuevo:

1. En Visual Studio, abra el archivo **device-simulation.sln** de la solución en el clon local del repositorio **device-simulation**.

1. En el Explorador de soluciones, haga clic en el proyecto **SimulationAgent**, elija **Propiedades** y, a continuación, **Depurar**.

1. En la sección **Variables de entorno**, edite el valor de la variable **PCS\_IOTHUB\_CONNSTRING** para que sea la cadena de conexión de IoT Hub que anotó anteriormente. A continuación, guarde los cambios.

1. En el Explorador de soluciones, haga clic con el botón derecho en la solución **device-simulations** y seleccione **Establecer proyectos de inicio**. Elija **Proyecto de inicio único** y seleccione **SimulationAgent**. A continuación, haga clic en **Aceptar**.

1. Cada tipo de dispositivo tiene un archivo de modelo JSON y los scripts asociados en la carpeta **Services/data/devicemodels**. En el Explorador de soluciones, copie los archivos de **Refrigerador** para crear los archivos de **Bombilla** tal como se muestra en la tabla siguiente:

    | Origen                      | Destino                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definición de las características del tipo de dispositivo nuevo

El archivo **lightbulb-01.json** define las características del tipo, como la telemetría que genera y los métodos que admite. En los pasos siguientes se actualiza el archivo **lightbulb-01.json** para definir el dispositivo **Bombilla**:

1. En el archivo **lightbulb-01.json**, actualice los metadatos de dispositivo tal como se muestra en el fragmento de código siguiente:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. En el archivo **lightbulb-01.json**, actualice la definición de la simulación tal como se muestra en el fragmento de código siguiente:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. En el archivo **lightbulb-01.json**, actualice las propiedades del tipo de dispositivo tal como se muestra en el fragmento de código siguiente:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. En el archivo **lightbulb-01.json**, actualice las definiciones de telemetría del tipo de dispositivo tal como se muestra en el fragmento de código siguiente:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. En el archivo **lightbulb-01.json**, actualice los métodos del tipo de dispositivo tal como se muestra en el fragmento de código siguiente:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Guarde el archivo **lightbulb-01.json**.

### <a name="simulate-custom-device-behavior"></a>Simular el comportamiento de un dispositivo personalizado

El archivo **scripts/lightbulb-01-state.js** define el comportamiento de la simulación del tipo **Bombilla**. En los pasos siguientes se actualiza el archivo **scripts/lightbulb-01-state.js** para definir el comportamiento del dispositivo **Bombilla**:

1. Edite la definición de estado en el archivo **scripts/lightbulb-01-state.js**, tal como se muestra en el fragmento de código siguiente:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Agregue una función de tipo **flip** después de la función **vary** con la siguiente definición:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Edite la función **main** para implementar el comportamiento, tal como se muestra en el fragmento de código siguiente:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Guarde el archivo **scripts/lightbulb-01-state.js**.

El archivo **scripts/SwitchOn-method.js** implementa el método **Encender** en un dispositivo **Bombilla**. En los siguientes pasos se actualiza el archivo **scripts/SwitchOn-method.js**:

1. Edite la definición de estado en el archivo **scripts/SwitchOn-method.js**, tal como se muestra en el fragmento de código siguiente:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Para encender la bombilla, edite la función **main** de la siguiente manera:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Guarde el archivo **scripts/SwitchOn-method.js**.

1. Haga una copia del archivo **scripts/SwitchOn-method.js** denominado **scripts/SwitchOff-method.js**.

1. Para desactivar la bombilla, edite la función **main** en el archivo **scripts/SwitchOff-method.js** tal como se muestra a continuación:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Guarde el archivo **scripts/SwitchOff-method.js**.

1. En el Explorador de soluciones, seleccione los cuatro archivos nuevos uno por uno. En la ventana **Propiedades** de cada archivo, asegúrese de que la opción **Copiar en el directorio de salida** está establecida en **Copiar si es posterior**.

### <a name="configure-the-device-simulation-service"></a>Configurar la solución de simulación de dispositivos

Para limitar el número de dispositivos simulados que se conectan a la solución durante las pruebas, configure el servicio para que ejecute un refrigerador y un dispositivo de bombilla únicos. Los datos de configuración se almacenan en la instancia de Cosmos DB en el grupo de recursos de la solución. Para editar los datos de configuración, use la vista de **Cloud Explorer** en Visual Studio:

1. Para abrir la vista de **Cloud Explorer** en Visual Studio, elija **Ver** y, a continuación, **Cloud Explorer**.

1. Para buscar el documento de configuración de la simulación, vaya a **Buscar recursos** y escriba **simulations.1**.

1. Haga doble clic en el documento **simulations.1** para abrirlo y editarlo.

1. En el valor **Datos**, busque la matriz **DeviceModels** que es similar al siguiente fragmento de código:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Para definir un refrigerador y un dispositivo simulado de bombilla únicos, reemplace la matriz **DeviceModels** con el código siguiente:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Guarde el cambio en el documento **simulations.1**.

> [!NOTE]
> También puede usar el Explorador de datos de Cosmos DB en Azure Portal para editar el documento **simulations.1**.

### <a name="test-the-lightbulb-device-type-locally"></a>Probar el tipo de dispositivo Bombilla de forma local

Ya está listo para probar el nuevo tipo de bombilla simulada; para ello, ejecute el proyecto de simulación de dispositivos de forma local.

1. En el Explorador de soluciones, haga clic con el botón derecho en **SimulationAgent**, elija **Depurar** y, a continuación, **Iniciar nueva instancia**.

1. Para comprobar que los dos dispositivos simulados están conectados a IoT Hub, abra Azure Portal en el explorador.

1. Vaya a IoT Hub en el grupo de recursos que contiene la solución de supervisión remota.

1. Seleccione **Métricas** en la sección **Supervisión**. A continuación, compruebe que el número de **dispositivos conectados** sea dos:

    ![Número de dispositivos conectados](media/iot-suite-remote-monitoring-test/connecteddevices.png)

1. En el explorador, navegue hasta el **panel** de la solución de supervisión remota. En el panel de telemetría del **Panel** principal, seleccione **temperatura**. La temperatura de los dos dispositivos simulados se muestra en el gráfico:

    ![Telemetría de temperatura](media/iot-suite-remote-monitoring-test/telemetry.png)

Ahora la simulación del dispositivo de bombilla se ejecuta de forma local. El siguiente paso es implementar el código del simulador actualizado en la máquina virtual que ejecuta los microservicios de supervisión remota en Azure.

Antes de continuar, puede detener la depuración de los proyectos de la simulación de dispositivos y del adaptador de almacenamiento en Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Implementar el simulador actualizado en la nube

Los microservicios de la solución de supervisión remota se ejecutan en contenedores de Docker. Los contenedores se hospedan en la máquina virtual de la solución en Azure. En esta sección:

* Puede crear una nueva imagen de Docker de la simulación del dispositivo.
* Puede cargar la imagen en el repositorio de Docker Hub.
* Puede importar la imagen en la máquina virtual de la solución.

En los siguientes pasos se supone que tiene un repositorio denominado **Bombilla** en la cuenta de Docker Hub.

1. En Visual Studio, en el proyecto denominado **device-simulation**, abra el archivo **solution\scripts\docker\build.cmd**.

1. Modifique la línea que establece la variable de entorno **DOCKER_IMAGE** en el nombre del repositorio de Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Guarde el cambio.

1. En Visual Studio, en el proyecto denominado **device-simulation**, abra el archivo **solution\scripts\docker\publish.cmd**.

1. Modifique la línea que establece la variable de entorno **DOCKER_IMAGE** en el nombre del repositorio de Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Guarde el cambio.

1. Abra un símbolo del sistema como administrador. A continuación, navegue hasta la carpeta **scripts\docker** en el clon del repositorio de GitHub denominado **device-simulation**.

1. Para compilar la imagen de Docker, ejecute el siguiente comando:

    ```cmd
    build.cmd
    ```

1. Para iniciar sesión en la cuenta de Docker Hub, ejecute el siguiente comando:

    ```cmd
    docker login
    ```

1. Para cargar la nueva imagen en la cuenta de Docker Hub, ejecute el siguiente comando:

    ```cmd
    publish.cmd
    ```

1. Para comprobar la carga, vaya a [https://hub.docker.com/](https://hub.docker.com/). Busque el repositorio **Bombilla** y seleccione **Detalles**. A continuación, seleccione **Etiquetas**:

    ![Docker Hub](media/iot-suite-remote-monitoring-test/dockerhub.png)

    Los scripts agregaron la etiqueta **Pruebas** a la imagen.

1. Use SSH para conectarse a la máquina virtual de la solución en Azure. A continuación, navegue hasta la carpeta **Aplicación** y edite el archivo **docker-compose.yaml**:

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Edite la entrada del servicio de simulación de dispositivos para usar la imagen de Docker:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Guarde los cambios.

1. Para reiniciar todos los servicios con la nueva configuración, ejecute el siguiente comando:

    ```sh
    sudo ./start.sh
    ```

1. Para comprobar el archivo de registro del nuevo contenedor de simulación de dispositivos, ejecute el siguiente comando para buscar el id. del contenedor:

    ```sh
    docker ps
    ```

    A continuación, ejecute el siguiente comando mediante el id. del contenedor:

    ```sh
    docker logs {container ID}
    ```

Ya ha completado los pasos para implementar una versión actualizada del servicio de simulación de dispositivos en la solución de supervisión remota.

En el explorador, navegue hasta el **panel** de la solución de supervisión remota. En el panel de telemetría del **Panel** principal, seleccione **Temperatura**. La temperatura de los dos dispositivos simulados se muestra en el gráfico:

![Telemetría de temperatura](media/iot-suite-remote-monitoring-test/telemetry.png)

En la página **Dispositivos**, puede aprovisionar instancias del tipo nuevo:

![Vista de la lista de simulaciones disponibles](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Puede ver la telemetría desde el dispositivo simulado:

![Vista de la telemetría de bombilla](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Puede llamar a los métodos **SwitchOn** y **SwitchOff** en el dispositivo:

![Llamada a los métodos de bombilla](media/iot-suite-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Incorporación de un tipo de telemetría nuevo

En esta sección se describe cómo modificar un tipo de dispositivo simulado existente para admitir un tipo de telemetría nuevo.

### <a name="locate-the-chiller-device-type-files"></a>Ubicación de los archivos del tipo de dispositivo Refrigerador

En los pasos siguientes se muestra cómo encontrar los archivos que definen el dispositivo **Refrigerador** integrado:

1. Si todavía no lo ha hecho, use el comando siguiente para clonar el repositorio **device-simulation** de GitHub en la máquina local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Cada tipo de dispositivo tiene un archivo de modelo JSON y los scripts asociados en la carpeta `data/devicemodels`. Los archivos que define el tipo de dispositivo **Refrigerador** simulado son:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Especificación de un tipo de telemetría nuevo

Los pasos siguientes muestran cómo agregar un tipo nuevo de **Temperatura interna** al tipo de dispositivo **Refrigerador**:

1. Abra el archivo **chiller-01.json**.

1. Actualice el valor **SchemaVersion** de la siguiente manera:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. En la sección **InitialState**, agregue las dos definiciones siguientes:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. En la matriz **Telemetría**, agregue la definición siguiente:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Guarde el archivo **chiller-01.json**.

1. Abra el archivo **scripts/chiller-01-state.js**.

1. Agregue los campos siguientes a la variable **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Agregue la línea siguiente a la función **main**:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Guarde el archivo **scripts/chiller-01-state.js**.

### <a name="test-the-chiller-device-type"></a>Prueba del tipo de dispositivo Refrigerador

Para probar el tipo de dispositivo **Refrigerador** actualizado, puede ejecutar primero una copia local del servicio **device-simulation** para comprobar que el tipo de dispositivo se comporta según lo esperado. Cuando haya probado y depurado localmente el tipo de dispositivo actualizado, puede recompilar el contenedor y volver a implementar el servicio **device-simulation** en Azure.

Cuando ejecuta localmente el servicio **device-simulation**, este envía la telemetría a la solución de supervisión remota. En la página **Dispositivos**, puede aprovisionar instancias del tipo actualizado.

Para probar y depurar localmente los cambios, consulte la sección anterior [Probar el tipo de dispositivo Bombilla de forma local](#test-the-lightbulb-device-type-locally).

Para implementar el servicio de simulación de dispositivos actualizado en la máquina virtual de la solución en Azure, consulte la sección anterior [Implementar el simulador actualizado en la nube](#deploy-the-updated-simulator-to-the-cloud).

En la página **Dispositivos**, puede aprovisionar instancias del tipo actualizado:

![Incorporación del refrigerador actualizado](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Puede ver la telemetría **Temperatura interna** nueva desde el dispositivo simulado.

## <a name="next-steps"></a>pasos siguientes

Este tutorial le ha mostrado cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Crear un tipo de dispositivo nuevo
> * Simular el comportamiento de un dispositivo personalizado
> * Agregar un tipo de dispositivo nuevo al panel
> * Enviar telemetría personalizada desde un tipo de dispositivo existente

Ya ha aprendido a personalizar el servicio de simulación de dispositivos. A continuación, el paso siguiente que se sugiere es aprender a [conectar un dispositivo físico a la solución de supervisión remota](iot-suite-connecting-devices-node.md).

Para más información sobre la solución de supervisión remota para los desarrolladores, consulte:

* [Guía de referencia para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guía de solución de problemas para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->