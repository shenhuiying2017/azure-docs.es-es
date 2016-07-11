<properties
	pageTitle="Administración de dispositivos con el SDK de puerta de enlace | Microsoft Azure"
	description="Tutorial del SDK de puerta de enlace del Centro de IoT de Azure que muestra cómo implementar la administración de dispositivos cuando se usa el SDK de puerta de enlace"
	services="iot-hub"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/15/2016"
     ms.author="dobett"/>


# SDK de puerta de enlace de IoT (beta): administración de dispositivos con el SDK de puerta de enlace

En este tutorial se muestra cómo utilizar las funcionalidades de [administración de dispositivos][lnk-device-management] del Centro de IoT junto con el [SDK de puerta de enlace del Centro de IoT de Azure][lnk-gateway-sdk]. Se utiliza un módulo de proceso de Intel Edison para hospedar una puerta de enlace con dispositivos simulados que envían telemetría a su centro de IoT. Utilizará las funcionalidades de administración de dispositivos del Centro de IoT para realizar una actualización remota del firmware en la placa Edison.

Esta tutorial abarca lo siguiente:

- **Arquitectura**: información importante de arquitectura sobre el ejemplo de administración de dispositivos.

- **Compilación y ejecución**: los pasos necesarios para compilar y ejecutar el ejemplo.

## Arquitectura

En este tutorial, se aprovisiona una placa Intel Edison para que funcione como una puerta de enlace de dispositivo IoT conectada al Centro de IoT. También configurará la placa Edison para que pueda administrarla mediante el Centro de IoT. En el siguiente diagrama se muestran los elementos clave de la solución que se crea en este tutorial:

![Arquitectura de puerta de enlace y de administración de dispositivos][img-architecture]

### Dispositivos

En esta solución, hay tres dispositivos IoT conectados al Centro de IoT:

- La placa Edison es un dispositivo denominado **GW-device**. En el tutorial se usarán las funcionalidades de administración de dispositivos del Centro de IoT para actualizar el firmware de este dispositivo físico.

- Dos dispositivos simulados (**GW-ble1-demo** y **GW-ble2-demo**). Estos dispositivos simulan dispositivos Bluetooth de baja energía que se conectan al Centro de IoT mediante la puerta de enlace y envían telemetría de temperatura a su centro.

### Software de puerta de enlace

El software de puerta de enlace se ejecuta como un servicio en la placa Edison. Dos dispositivos simulados generan telemetría de temperatura. El módulo de asignación asigna estos dispositivos simulados a dispositivos registrados en el Centro de IoT y el módulo HTTP administra la comunicación con el punto de conexión del Centro de IoT. En el artículo [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado][lnk-gateway-scenario] se describe este escenario con detalle.

### Cliente de administración de dispositivos

El [cliente de administración de dispositivos][lnk-device-management] se ejecuta como un servicio en la placa Edison. Esto le permite ejecutar trabajos remotos en la placa Edison, como [actualizaciones de firmware][lnk-dm-jobs], reinicios y actualizaciones de configuración, así como consultas de propiedades de dispositivo. En este tutorial, el cliente de administración de dispositivos recibe y procesa una solicitud para realizar una actualización de firmware en la placa Edison.

### Centro de IoT

El [Centro de IoT de Azure][lnk-iot-hub] es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos IoT y el back-end de una solución. En este tutorial, el Centro de IoT:

- Permite a los tres dispositivos conectarse a la nube.
- Recibe de la puerta de enlace los datos de telemetría generados por los dispositivos simulados.
- Le permite enviar una solicitud de actualización de firmware a la placa Edison.
- Supervisa el progreso del trabajo de actualización de firmware.

### Interfaz de usuario de ejemplo de administración de dispositivos

La [interfaz de usuario de ejemplo de administración de dispositivos][lnk-dm-sample-ui] es una aplicación web de ejemplo que le permite usar las funcionalidades de administración de dispositivos del Centro de IoT en una interfaz de usuario web interactiva. Por ejemplo, puede utilizar la interfaz de usuario de ejemplo para consultar los dispositivos conectados a su Centro de IoT y enviar trabajos de actualización de firmware a los dispositivos. En este tutorial, utilizará una interfaz de usuario de ejemplo para enviar un trabajo de actualización de firmware a la placa Edison y supervisar el progreso de ese trabajo hasta su finalización.

## Compilación y ejecución

Para ejecutar este ejemplo debe compilar una imagen personalizada de la placa Edison que incluya el cliente de administración de dispositivos y el software de puerta de enlace del Centro de IoT.

Antes de comenzar, asegúrese de que puede conectar la placa Edison a la red inalámbrica. Para configurar la placa Edison, debe conectarla a un equipo host. Más adelante, utilizará el equipo host para actualizar la placa Edison con la imagen personalizada que haya creado. Intel cuenta con un conjunto de guías de introducción que incluyen guías para los sistemas operativos siguientes:

- [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] (Introducción a la placa de desarrollo de Intel Edison en Windows de 64 bits).
- [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] (Introducción a la placa de desarrollo de Intel Edison en Windows de 32 bits).
- [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] (Introducción a la placa de Intel® Edison en Linux).

Para configurar la placa Edison y familiarizarse con ella, debe completar todos los pasos de estas guías de introducción, excepto:

- Actualización del firmware más reciente. Este paso lo haremos aquí, como parte de este tutorial, así que no tiene que realizarlo en este momento.
- El último paso, la selección de IDE, que no es necesario para el tutorial actual.

Cuando haya configurado la placa Edison e instalado los controladores necesarios en el equipo host, debe asegurarse de que puede conectarse a la placa Edison mediante un terminal serie. En la página [Setting up a serial terminal][lnk-serial-connection] (Configuración de un terminal serie) del sitio web de Intel, hay vínculos a las instrucciones de configuración de los sistemas operativos host, como Windows y Linux.

También deberá realizar estas tareas:

- [Crear un Centro de IoT][lnk-create-hub] en su suscripción de Azure. Necesitará el nombre de su centro para realizar este tutorial. Si aún no tiene una suscripción de Azure, puede obtener una [cuenta gratuita][lnk-free-trial].
- Agregar tres dispositivos (**GW-ble1-demo**, **GW-ble2-demo** y **GW-device**) a su centro de IoT y anotar sus id. y claves de dispositivo. Puede usar las herramientas [Device Explorer o iothub-explorer][lnk-explorer-tools] para agregar estos dispositivos al centro de IoT que ha creado en el paso anterior y recuperar sus claves. Dos de estos dispositivos (**GW-ble1-demo** y **GW-ble2-demo**) se usarán como dispositivos BLE simulados conectados a la puerta de enlace, y el tercero (**GW-device**) para identificar el dispositivo de puerta de enlace Edison como un cliente de administración de dispositivos que puede administrar desde su Centro de IoT.

### Preparación del entorno de compilación y comprobación de que puede crear una imagen personalizada

Para crear una imagen personalizada para la placa Edison, necesita un entorno de Linux. En estos pasos se da por supuesto que va a utilizar Ubuntu 14.04 que, en el momento de escribir, es la plataforma de uso recomendada. Debe tener al menos 40 GB de espacio libre en la partición principal.

> [AZURE.NOTE] El script que crea la imagen personalizada puede tardar hasta seis horas en ejecutarse en un equipo con una CPU de cuatro núcleos. Puede reducir este tiempo con un equipo más avanzado que tenga más núcleos de CPU.

Para los pasos de esta sección, se han consultado los siguientes artículos: [Intel Edison Board Support Package][lnk-inteledison-bsp] (Paquete de compatibilidad de la placa Intel Edison), [Manually Building Yocto Images for the Intel Edison Board from Source][lnk-hackgnar] (Creación manual de imágenes Yocto para la placa Intel Edison desde el origen) y [Creating a Custom Linux Kernel for the Edison (release 2.1)][lnk-shawnhymel] (Creación de un kernel personalizado para Edison [versión 2.1]).

1. Inicie sesión en el equipo Ubuntu 14.04 y ejecute el siguiente comando en la carpeta principal para descargar el paquete de origen de Edison:
    
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```

2. Descomprima el paquete de origen para crear una carpeta **edison-src** en la carpeta principal con el siguiente comando y vaya a la nueva carpeta **edison-src**:
    
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```

3. Instale los paquetes de requisitos previos:
    
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```

4. Cree los dos nuevos directorios en la carpeta **edison-src**:
    
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```

5. Ejecute el siguiente script para descargar el entorno de compilación. Si tiene una CPU con más de cuatro núcleos, establezca los argumentos de script **--parallel\_make** y **--bb\_number\_thread** en el número de núcleos disponibles:
    
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```

6. Actualice la ubicación del repositorio git de Paho. Edite el archivo **~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** y sustituya la dirección URL `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/` por `git://github.com/eclipse/paho.mqtt.c.git`.

7. Inicialice el entorno de compilación con los siguientes comandos:
    
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```

8. Use el siguiente comando para crear la imagen personalizada. La primera vez que ejecute este comando puede tardar hasta seis horas en ejecutarse en un equipo con una CPU de cuatro núcleos. Las posteriores recompilaciones después de haber agregado sus propias personalizaciones serán mucho más rápidas:
    
    ```
    bitbake edison-image
    ```

9. Termine la compilación mediante la ejecución de los siguientes comandos:
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Los archivos que necesita para actualizar la placa Edison están ahora en la carpeta **~/edison-src/out/linux64/build/toFlash/**.

### Adición del SDK de puerta de enlace a la imagen personalizada

Los pasos de esta sección le guiarán en el proceso de agregar el SDK de puerta de enlace a la imagen personalizada de modo que la placa Edison funcione como una puerta de enlace de IoT cuando arranque. En este tutorial, la puerta de enlace incluye un módulo que simula dos dispositivos Bluetooth de baja energía (BLE) que generan telemetría de temperatura que la puerta de enlace reenvía a su centro de IoT.

Complete los pasos siguientes en el mismo equipo Ubuntu 14.04 utilizado para crear una imagen Edison en la sección anterior.

1. Clone el SDK de puerta de enlace en la carpeta principal:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```

2. Configure la puerta de enlace para conectar con el Centro de IoT y simular dos dispositivos. Edite el archivo **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** y sustituya los marcadores de posición **IoTHubName**, **IoTHubSuffix**, **deviceID** y **deviceKey** por los valores anotados al configurar el Centro de IoT. Use los dispositivos **GW-ble1-demo** y **GW-ble2-demo** creados anteriormente.

3. Cree una carpeta que contenga la nueva receta:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```

4. Copie el archivo denominado **azure-iot-field-gateway-sdk.bb** de la carpeta **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** a la carpeta **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/** que acaba de crear. Edite el archivo para reemplazar las dos ocurrencias de `<<userName>>` por su nombre de usuario actual.

5. Edite **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** para agregar una entrada para la nueva receta. Agregue la siguiente línea al final del archivo:
    
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```

6. Ahora puede probar los cambios mediante la ejecución del comando **bitbake** que compila únicamente la nueva receta:
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### Adición del cliente de administración de dispositivos del Centro de IoT de Azure a la imagen personalizada

Los pasos de esta sección le guían en el proceso de agregar el cliente de administración de dispositivos del Centro de IoT a la imagen personalizada, de forma que pueda administrar el dispositivo de puerta de enlace Edison desde el Centro de IoT. En este tutorial, el cliente de administración de dispositivos incluye código de ejemplo para la realización de una actualización de firmware en el dispositivo de puerta de enlace Edison.

Lleve a cabo los pasos siguientes en el mismo equipo Ubuntu 14.04 que usó en la sección anterior para agregar la puerta de enlace a la imagen de Edison.

1. Clone la rama **dmpreview** del repositorio de los SDK del Centro de IoT en la carpeta principal:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```

2. Cree las siguientes carpetas para que contengan la nueva receta:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```

3. Copie el archivo **iotdm-edison-sample.bb** de la carpeta **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** a la carpeta **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample**.

4. Copie el archivo **iotdm\_edison\_sample.service** de la carpeta **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** a la carpeta **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files**.

5. Edite el archivo **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** para agregar una entrada para la nueva receta. Agregue la siguiente línea al final del archivo:
    
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```

6. Como el SDK de puerta de enlace y el cliente de administración de dispositivos comparten algunas bibliotecas, deberá editar el archivo **~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass**. Agregue las siguientes líneas al final de este archivo. Asegúrese de reemplazar `<your user>` por su nombre de usuario actual:
    
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```

7. Configure la conexión WiFi para que se inicie automáticamente en la placa Edison; para ello edite el archivo **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** y agregue las siguientes líneas al final. Asegúrese de reemplazar `<your wifi ssid>` y `<your wifi password>` por los valores correctos de su red WiFi:
    
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```

8. Ahora puede crear la imagen de la placa Edison que contiene el SDK de puerta de enlace y el cliente de administración de dispositivos. El comando **bitbake** se ejecutará mucho más rápido que antes porque solo hace falta compilar la nueva receta y agregarla a la imagen:
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```

9. Termine la compilación mediante la ejecución de los siguientes comandos:
  
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Los archivos que necesita para actualizar la placa Edison están ahora en la carpeta **~/edison-src/out/linux64/build/toFlash/**.

### Actualización de Intel Edison con la imagen personalizada

Ahora puede actualizar su placa Edison con la imagen personalizada que contiene el cliente de administración de dispositivos y el software de puerta de enlace del Centro de IoT.

Copie los archivos de la carpeta **toFlash** del equipo Ubuntu que utilizó para crear la imagen personalizada en el equipo conectado a la placa Edison mediante un cable USB.

Si usa un equipo Windows para conectarse a la placa Edison con un cable USB, debe ejecutar el script **flashall.bat** para actualizarla. Si usa un equipo Linux para conectarse a la placa Edison con un cable USB, debe ejecutar el script **flashall.bat** para actualizarla.

Cuando finalice el proceso de actualización, conéctese a la placa Edison mediante una [conexión serie][lnk-serial-connection] desde el equipo host e inicie sesión como **root**. Debe comprobar que la placa Edison ahora está conectada a su red WiFi.

### Ejecución del ejemplo

Para conectarse como el dispositivo **GW-device** al centro de IoT, debe configurar el cliente de administración de dispositivos en la placa Edison. Utilice un editor de texto (como **vi** o **nano**) para crear un archivo denominado **.cs** en la carpeta /home/root de la placa Edison. Este archivo debe contener solamente la cadena de conexión de **GW-device**. Si no anotó anteriormente esta cadena de conexión, puede usar las herramientas [Device Explorer o iothub-explorer][lnk-explorer-tools] para recuperar esta cadena de conexión de dispositivo del Registro de dispositivos del Centro de IoT.

Cuando haya creado el archivo **.cs**, reinicie la placa Edison mediante el siguiente comando:

```
reboot -h now
```

A continuación, compruebe que los servicios de puerta de enlace y de administración de dispositivos se inician con un estado **Correcto**:

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

Para solucionar los problemas con el servicio de puerta de enlace de IoT, examine las entradas del archivo **/deviceCloudUploadGatewaylog.log** en la placa Edison. También puede utilizar el siguiente comando para ver todos los resultados del servicio:

```
systemctl status simulated_device_cloud_upload.service
```

Para solucionar los problemas con el servicio de administración de dispositivos IoT, use el siguiente comando para ver todos los resultados:

```
systemctl status iotdm_edison_sample.service
```

### Inicio del trabajo de actualización del firmware

Una actualización de firmware en la placa Edison solicitada por el servicio de administración de dispositivos IoT normalmente descarga un archivo zip que contiene el firmware desde una dirección URL. Para simplificar este tutorial, copie manualmente el archivo zip en la placa Edison y utilice una dirección URL **file://** en lugar de una **http://** cuando solicite la actualización.

De nuevo, para simplificar el tutorial, la actualización de firmware vuelve a aplicar la misma imagen de firmware en lugar de utilizar una nueva. Podrá ver que esta imagen se aplica a la placa Edison.

Cree un archivo zip denominado **edison.zip** que contenga todos los archivos y subcarpetas de la carpeta **toFlash** en el equipo Ubuntu que usó para crear la imagen personalizada. Asegúrese de que los archivos de la carpeta **toFlash** se encuentran en la raíz del archivo zip. Utilice una herramienta como SCP (o PSCP si usa Putty) para copiar el archivo **edison.zip** en la carpeta /home/root de la placa Edison.

Para enviar el trabajo de actualización de firmware y supervisar su progreso, utilice la [interfaz de usuario de ejemplo de administración de dispositivos][lnk-dm-sample-ui] de Node.js. Puede ejecutar este ejemplo en Windows o Linux y se requiere [Node.js][lnk-nodejs] 6.1.0 o superior. Para recuperar, compilar y ejecutar la interfaz de usuario de ejemplo de administración de dispositivos, siga estos pasos:

1. Abra el **símbolo del sistema**.

2. Confirme que ha instalado Node.js 6.1.0 o superior, para ello, escriba `node --version`.

3. Clone el repositorio de GitHub de la interfaz de usuario de administración de dispositivos de IoT de Azure mediante la ejecución del siguiente comando:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. En la carpeta raíz de la copia clonada del repositorio de interfaz de usuario de administración de dispositivos de IoT de Azure, ejecute el comando siguiente para recuperar los paquetes dependientes:

    ```
    npm install
    ```

5. Cuando se haya completado el comando npm install, ejecute el comando siguiente para compilar el código:

    ```
    npm run build
    ```

6. Utilice un editor de texto para abrir el archivo user-config-json en la raíz de la carpeta clonada. Reemplace el texto "&lt;SU CADENA DE COINEXIÓN AQUÍ&gt;" por su cadena de conexión del Centro de IoT. Puede encontrar esta cadena de conexión en el [Portal de Azure][lnk-azure-portal].

7. En el símbolo del sistema, ejecute el siguiente comando para iniciar la aplicación de experiencia de usuario de administración de dispositivos:

    ```
    npm run start
    ```

8. Cuando el símbolo del sistema notifique que los servicios se han iniciado, abra un explorador web y vaya a la aplicación de administración de dispositivos en la siguiente dirección URL para ver los dispositivos: <http://127.0.0.1:3003>.

    ![Interfaz de usuario de administración de dispositivos][img-dm-ui]

9. Seleccione el dispositivo **GW-device**, en la lista desplegable **Device Jobs** (Trabajos de dispositivo), seleccione **Firmware Update** (Actualización de Firmware) y luego haga clic en **Iniciar**.

10. En el campo **Package URI** (URI del paquete), escriba **file:///home/root/edison.zip** para usar el archivo de imagen que copió anteriormente en la placa Edison. Haga clic en **Enviar**, en **Sí** y luego en el vínculo **Historial de trabajos** para ver los nuevos trabajos primarios y secundarios en ejecución:

    ![Vínculo de historial de trabajos][img-history-link]

11. Tras unos minutos, en el terminal serie conectado a la placa Edison, verá que Edison se reinicia y se aplican los cambios de firmware:

    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9

    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions

    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```

12. Cuando la placa Edison termine de reiniciarse, actualice la página en la interfaz de usuario de ejemplo de administración de dispositivos y verá que el estado del trabajo es ahora **completado** para los dos trabajos de actualización de firmware:

    ![Estado del trabajo completado][img-job-status]

Ahora ha finalizado el tutorial que muestra cómo utilizar el cliente de administración de dispositivos y el software de puerta de enlace del Centro de IoT en una placa Intel Edison. En este tutorial ha realizado las siguientes tareas:

- Ha creado una imagen de Intel Edison personalizada que incluye el cliente de administración de dispositivos del Centro de IoT y el software de puerta de enlace configurado para iniciarse cada vez que arranque la placa Edison.
- Ha configurado la placa Edison y el cliente de administración de dispositivos para la conexión al centro de IoT.
- Ha iniciado un trabajo de administración de dispositivos desde la interfaz de usuario de ejemplo que hace que la placa Edison se reinicie y aplique una nueva imagen de firmware.

## Pasos siguientes

Para más información sobre la administración de dispositivos con el Centro de IoT y la interfaz de usuario de ejemplo, consulte el artículo [Introducción a la administración de dispositivos del Centro de IoT de Azure][lnk-device-management].

Para más información sobre cómo conectar dispositivos físicos a su Centro de IoT, consulte el artículo [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo real a través de Linux][lnk-gateway-physical].



<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

<!---HONumber=AcomDC_0629_2016-->