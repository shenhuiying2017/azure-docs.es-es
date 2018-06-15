---
title: Configuración de un dispositivo para el servicio Azure IoT Hub Device Provisioning
description: Configurar el dispositivo para el aprovisionamiento mediante el servicio IoT Hub Device Provisioning durante el proceso de fabricación del dispositivo
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1e4e93c276fe62caae17c85bf9ac92282dfdfb88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631275"
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configuración del dispositivo para el aprovisionamiento mediante el servicio Azure IoT Hub Device Provisioning

En el tutorial anterior, aprendió a configurar el servicio Azure IoT Hub Device Provisioning para aprovisionar automáticamente los dispositivos en su instancia de IoT Hub. Este tutorial muestra cómo configurar el dispositivo durante el proceso de fabricación, lo que permite aprovisionarlo automáticamente con IoT Hub. El dispositivo se aprovisiona en función de su [mecanismo de atestación](concepts-device.md#attestation-mechanism), tras el primer arranque y su conexión al servicio de aprovisionamiento. Este tutorial describe los procesos para:

> [!div class="checklist"]
> * Compilar un SDK de cliente de los servicios Device Provisioning específico de la plataforma
> * Extracción de los artefactos de seguridad
> * Crear el software de registro del dispositivo

## <a name="prerequisites"></a>requisitos previos

Antes de continuar, cree la instancia del servicio Device Provisioning y una instancia de IoT Hub, para lo que debe seguir las instrucciones del tutorial [1 - Configuración de los recursos de la nube](./tutorial-set-up-cloud.md).

En este tutorial se usan los [SDK y las bibliotecas de Azure IoT para el repositorio de C](https://github.com/Azure/azure-iot-sdk-c), que contiene el SDK de cliente del servicio Device Provisioning. El SDK proporciona actualmente compatibilidad con TPM y X.509 para los dispositivos que se ejecutan en implementaciones de Windows o Ubuntu. Este tutorial se basa en el uso de un cliente de desarrollo de Windows, que también da por supuesto un conocimiento básico de Visual Studio 2017. 

Si no está familiarizado con el proceso de aprovisionamiento automático, no olvide revisar los [conceptos sobre aprovisionamiento automático](concepts-auto-provisioning.md) antes de continuar. 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Compilación de una versión específica de la plataforma del SDK

El SDK de cliente del servicio Device Provisioning le ayuda a implementar el software de registro del dispositivo. Pero para poder usarlo, es preciso compilar una versión del SDK específico de su plataforma de cliente de desarrollo y mecanismo de plataforma. En este tutorial, se compila un SDK que usa Visual Studio 2017 en una plataforma de desarrollo de Windows para un tipo de atestación compatible:

1. Instale las herramientas requeridas y clone el repositorio de GitHub que contiene el SDK de cliente del servicio de aprovisionamiento para C:

   a. Asegúrese de tener instalados Visual Studio 2015 o [Visual Studio 2017 ](https://www.visualstudio.com/vs/) en la máquina. Debe tener la carga de trabajo ["Desarrollo para el escritorio con C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada para la instalación de Visual Studio.

   b. Descargue e instale el [sistema de compilación CMake](https://cmake.org/download/). Es importante que en la máquina esté instalado Visual Studio con la carga de trabajo "Desarrollo para el escritorio con C++" **antes de** instalar CMake.

   c. Asegúrese de que `git` está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. En [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (herramientas de cliente GIT de Software Freedom Conservancy), encontrará las herramientas `git` más recientes, entre las que se incluyen **Git Bash**, una shell Bash de línea de comandos para interactuar con el repositorio Git local. 

   d. Abra Git Bash y clone el repositorio "SDK y bibliotecas de IoT de Azure para C". El comando de clonación puede tardar varios minutos en completarse, ya que también descarga varios submodulos dependientes:
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. Crear un nuevo subdirectorio `cmake` en el subdirectorio del repositorio recién creado:

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. Desde el símbolo del sistema de Git Bash, cambie al subdirectorio `cmake` del repositorio azure-iot-sdk-c:

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. Compile el SDK para la plataforma de desarrollo y uno de los mecanismos de atestación admitidos, para lo que debe utilizar uno de los siguientes comandos (fíjese en los dos caracteres de punto finales). Al finalizar, CMake crea el subdirectorio `/cmake` con contenido específico para su dispositivo:
    - Para dispositivos que usan un TPM/HSM físico o un certificado X.509 simulado para la atestación:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - Para dispositivos que usan el simulador de TPM para la atestación:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

Ya está listo para usar el SDK para compilar el código de registro del dispositivo. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extracción de los artefactos de seguridad 

El siguiente paso es extraer los artefactos de seguridad del mecanismo de atestación que usa el dispositivo. 

### <a name="physical-device"></a>Dispositivo físico 

Si ha compilado el SDK para usar la atestación desde un HSM/TPM físico:

- En el caso de un dispositivo con TPM, es preciso que determine la **clave de aprobación** asociada a este con la ayuda del fabricante del chip TPM. Puede derivar un único **identificador del registro** para el dispositivo TPM aplicando un algoritmo hash a la clave de aprobación.  

- En el caso de un dispositivo con X.509, debe obtener los certificados emitidos para los dispositivos: certificados de entidad final para la inscripción en dispositivos individuales, y certificados raíz para las inscripciones de grupos de dispositivos. 

### <a name="simulated-device"></a>Dispositivo simulado

Si ha compilado el SDK para usar la atestación de un certificado TPM o X.509 simulado:

- Para un dispositivo con TPM simulado:
   1. En un símbolo del sistema nuevo o independiente, vaya al subdirectorio `azure-iot-sdk-c` y ejecute el simulador de TPM. Atiende en un socket en los puertos 2321 y 2322. No cierre esta ventana de comandos; este simulador debe estar en ejecución hasta el final de la siguiente guía de inicio rápido. 

      En el directorio `azure-iot-sdk-c`, ejecute el siguiente comando para iniciar el simulador:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. Con Visual Studio, abra la solución generada en la carpeta *cmake* denominada `azure_iot_sdks.sln` y compílela mediante el comando "Compilar solución" del menú "Compilar".

   3. En el panel *Explorador de soluciones* de Visual Studio, vaya a la carpeta **Provision\_Tools**. Haga clic con el botón derecho en el proyecto **tpm_device_provision** y seleccione **Establecer como proyecto de inicio**. 

   4. Ejecute la solución con cualquiera de los comandos de "Inicio" del menú "Depurar". En la ventana de salida se muestra el **_identificador de registro_** y la **_clave de aprobación_** del simulador de TPM, que necesarios para la inscripción y el registro del dispositivo. Copie estos valores para usarlos más adelante. Puede cerrar esta ventana (con el identificador de registro y la clave de aprobación), pero deje que se ejecute la ventana del simulador de TPM que inició en el paso 1.

- Para un dispositivo con X.509 simulado:
  1. Con Visual Studio, abra la solución generada en la carpeta *cmake* denominada `azure_iot_sdks.sln` y compílela mediante el comando "Compilar solución" del menú "Compilar".

  2. En el panel *Explorador de soluciones* de Visual Studio, vaya a la carpeta **Provision\_Tools**. Haga clic con el botón derecho en el proyecto **dice\_device\_enrollment** y seleccione **Establecer como proyecto de inicio**. 
  
  3. Ejecute la solución con cualquiera de los comandos de "Inicio" del menú "Depurar". En la ventana de salida, escriba **i** para la inscripción individual cuando se le solicite. La ventana de salida muestra un certificado X.509 generado localmente para el dispositivo simulado. Copie en el portapapeles la salida desde *---BEGIN CERTIFICATE---* hasta *-----END CERTIFICATE-----* y asegúrese de incluir ambas líneas. Tenga en cuenta que solo necesita el primer certificado de la ventana de salida.
 
  4. Cree un archivo llamado **_X509testcert.pem_**, ábralo en el editor de texto que desee y copie el contenido del portapapeles en este archivo. Guarde el archivo tal como lo usará más adelante para la inscripción de dispositivo. Cuando se ejecuta el software de registro, utiliza el mismo certificado durante el aprovisionamiento automático.    

Estos artefactos de seguridad se requieren durante la inscripción del dispositivo al servicio Device Provisioning. El servicio de aprovisionamiento espera a que el dispositivo arranque y se conecte con él en cualquier momento posterior en el tiempo. La primera vez que arranca el dispositivo, la lógica del SDK de cliente interactúa con el chip (o simulador) para extraer los artefactos de seguridad del dispositivo y comprueba el registro con el servicio Device Provisioning. 

## <a name="create-the-device-registration-software"></a>Crear el software de registro del dispositivo

El último paso es escribir una aplicación de registro que use el SDK de cliente del servicio Device Provisioning para registrar el dispositivo con el servicio IoT Hub. 

> [!NOTE]
> Para este paso supondremos que se usa un dispositivo simulado, lo que se realiza mediante la ejecución de una aplicación de registro de ejemplo de SDK desde la estación de trabajo. Sin embargo, se aplican los mismos conceptos si se compila una aplicación de registro para su implementación en un dispositivo físico. 

1. En Azure Portal, seleccione la hoja **Información general** del servicio Device Provisioning y copie el valor de **_Ámbito de id_**. El servicio genera el *ámbito del identificador* y este garantiza la exclusividad. Es inmutable y se utiliza para identificar de forma exclusiva los identificadores de registro.

    ![Extracción de información del punto de conexión DPS de la hoja del portal](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. En el *Explorador de soluciones* de Visual Studio de su máquina, vaya a la carpeta **Provision\_Samples**. Seleccione el proyecto de ejemplo denominado **prov\_dev\_client\_sample** y abra el archivo de origen **prov\_dev\_client\_sample.c**.

3. Asigne el valor de _Ámbito de id._ que obtuvo en el paso 1 a la variable `id_scope` (quitando los corchetes izquierdo /`[` y derecho /`]`): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Como referencia, la variable `global_prov_uri`, que permite que la API de registro de cliente de IoT Hub `IoTHubClient_LL_CreateFromDeviceAuth` se conecte con la instancia del servicio Device Provisioning designada.

4. En la función **main()** del mismo archivo, agregue o quite el comentario de la variable `hsm_type` que coincida con el mecanismo de atestación que use el software de registro de su dispositivo (TPM o X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Guarde los cambios y vuelva a compilar el ejemplo **prov\_dev\_client\_sample**, para lo que debe seleccionar "Compilar solución" en el menú "Compilar". 

6. Haga clic en el proyecto **prov\_dev\_client\_sample** de la carpeta **Provision\_Samples** y seleccione **Establecer como proyecto de inicio**. NO ejecute aún la aplicación de ejemplo.

> [!IMPORTANT]
> No ejecute o inicie el dispositivo aún. Es preciso que finalice el proceso, para lo que debe inscribir el dispositivo en el servicio antes de iniciarlo. La sección Pasos siguientes le guiará al siguiente artículo.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>API de SDK usadas en el registro (solo para referencia)

Como referencia, el SDK proporciona las siguientes API para que su aplicación las use en el registro. Estas API ayudan a su dispositivo a conectarse y registrarse en el servicio Device Provisioning al arrancar. A cambio, el dispositivo recibe la información que necesita para establecer una conexión con una instancia de IoT Hub:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

También puede darse cuenta de que necesita refinar la aplicación de registro de cliente del servicio Device Provisioning, para lo que debe usar al principio un dispositivo simulado, y una configuración del servicio de prueba. Una vez que la aplicación está funcionando en el entorno de prueba, puede compilarla para el dispositivo concreto y copiar el archivo ejecutable en la imagen del dispositivo. 

## <a name="clean-up-resources"></a>Limpieza de recursos

En este momento, los servicios Device Provisioning e IoT Hub se ejecutan en el portal. Si desea abandonar la configuración del aprovisionamiento del dispositivo o retrasar la finalización de esta serie de tutoriales, es aconsejable apagarlos para no incurrir en costos innecesarios.

1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  
2. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Compilar un SDK de cliente del servicio Device Provisioning específico de la plataforma
> * Extracción de los artefactos de seguridad
> * Crear el software de registro del dispositivo

Avance hasta el siguiente tutorial para aprender a aprovisionar el dispositivo en su instancia de IoT Hub inscribiéndolo en el servicio Azure IoT Hub Device Provisioning para su aprovisionamiento automático.

> [!div class="nextstepaction"]
> [Aprovisionar el dispositivo en la instancia de IoT Hub](tutorial-provision-device-to-hub.md)

