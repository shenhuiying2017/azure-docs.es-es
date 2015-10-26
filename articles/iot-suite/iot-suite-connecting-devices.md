<properties
   pageTitle="Conectar un dispositivo a una solución preconfigurada | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante un ejemplo que usa datos de temperatura y humedad."
   services=""
   documentationCenter="na"
   authors="hegate"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="nana"
   ms.date="09/29/2015"
   ms.author="hegate"/>


# Conexión del dispositivo a la solución de supervisión remota del conjunto de aplicaciones de IoT de Azure


## Información general de escenario

En este ejemplo, tenemos tres orígenes de datos simulados: temperatura exterior, temperatura interior y humedad. Por motivos de simplicidad no vamos a usar sensores reales (los datos se generan automáticamente en el código de cliente), pero le animamos en un paso siguiente a conectar su sensor favorito y enviar datos reales. Visite los recursos vinculados de la sección Ampliación de la solución para obtener más información.

## Requisitos previos

### Aprovisionar el conjunto de aplicaciones de IoT

Si aún no aprovisionó la solución preconfigurada de supervisión remota, puede hacerlo [aquí](http://www.microsoft.com/es-ES/server-cloud/internet-of-things/getting-started.aspx).


### Aprovisionar el dispositivo en la solución de supervisión remota
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
Para conectar el dispositivo a la solución preconfigurada, debe obtener las credenciales del dispositivo desde el panel. Estas se usarán en la aplicación cliente para que se pueda identificar el dispositivo. Siga estos pasos:

1.  En la esquina inferior izquierda del panel, haga clic en "Agregar un dispositivo". ![][1]
2.  En "Dispositivo personalizado", haga clic en el botón "Agregar nuevo".

    ![][2]
3.  Elija su propio id. de dispositivo (puede escribir un nombre como realdevice1), y haga clic en "Comprobar id." para asegurarse de que ese nombre no se usó aún. ![][3]

5. Copie las credenciales proporcionadas (identificador de dispositivo, nombre de host del Centro de IoT y clave de dispositivo). Las necesitará más adelante en la aplicación cliente para conectar el dispositivo a la solución. ![][4]
6. Asegúrese de que el dispositivo se muestra correctamente en la sección de dispositivos. El estado será "Pendiente". Se espera hasta que se establezca la conexión del dispositivo a la nube.

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

Elija ahora el lenguaje que le gustaría usar para continuar el ejemplo. En este tutorial, hemos creado código de ejemplo para C y node.js, pero también se puede implementar en C Sharp y Java.

## Envío de datos de dispositivo a la solución de supervisión remota mediante C


### Ejecución del dispositivo en Linux

1. Configure el entorno: si nunca usó antes nuestro SDK de dispositivos, obtenga información sobre cómo configurar el entorno en Linux [aquí](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux).

1. Abra el archivo **c/serializer/samples/serializer/remote\_monitoring.c** en un editor de texto.

2. Busque el siguiente código en el archivo: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Reemplace "[Device Id]" y "[Device Key] por los datos de su dispositivo.

4. Use los datos de dispositivo del nombre de host del Centro de IoT para rellenar el nombre y el sufijo de IoTHub. Para ello, debe dividirlo en IoTHub + IoTHubSuffix. Por ejemplo, si el nombre de host del Centro de IoT es "Contoso.azure devices.net", "Contoso" será el nombre de IoTHub y el resto el sufijo. Debería ser parecido a este:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. Guarde los cambios y compile los ejemplos. Para compilar el ejemplo, puede ejecutar el script build.sh en el directorio **build\_all/c/linux**.

6. Ejecute la aplicación de ejemplo **serializador/c/samples/linux/remote\_monitoring/remote\_monitoring**.

Visualizar el dispositivo registrado y los datos

7. Vuelva al panel de la solución de supervisión remota. Verá que el estado del dispositivo cambió a En ejecución en la lista de dispositivos. ![][18]

8. Haga clic en el panel para ver los datos entrantes. El ejemplo está configurado para enviar 50 unidades de temperatura interior, 55 unidades de temperatura exterior y 50 de humedad. Tenga en cuenta que, de forma predeterminada, en el panel solo se muestran la temperatura y la humedad.

8. Ahora, vaya a la sección [Comando y control](#command) para obtener información sobre cómo cambiar la temperatura en el dispositivo desde la solución de supervisión remota.


### Ejecución del dispositivo en Windows


1. Configure el entorno: si nunca usó antes nuestro SDK de dispositivos, obtenga información sobre cómo configurar el entorno en Windows [aquí](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows).

1. Inicie una nueva instancia de Visual Studio de 2015. Abra la solución **remote\_monitoring.sln** en la carpeta **c\\serializer\\build\\windows** de la copia local del repositorio.

2. En Visual Studio, en el **Explorador de soluciones**, vaya a la carpeta de ejemplos. En el proyecto **remote\_monitoring**, abra el archivo **remote\_monitoring.c**.

2. Busque el siguiente código en el archivo: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Reemplace "[Device Id]" y "[Device Key] por los datos de su dispositivo.

4. Use los datos de dispositivo del nombre de host del Centro de IoT para rellenar el nombre y el sufijo de IoTHub. Para ello, debe dividirlo de la forma siguiente:

    Si el nombre de host del Centro de IoT es "Contoso.azure devices.net", "Contoso" será el nombre de IoTHub y el resto el sufijo. Debería ser parecido a este:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **remote\_monitoring**, haga clic en **Depurar** y, luego, haga clic en **Iniciar nueva instancia** para compilar y ejecutar el ejemplo. En la consola se muestran los mensajes a medida que la aplicación envía mensajes de dispositivo a nube al Centro de IoT.

Visualizar el dispositivo registrado y los datos

7. Vuelva al panel de la solución de supervisión remota. Verá que el estado del dispositivo cambió a En ejecución en la lista de dispositivos. ![][18]

8. Haga clic en el panel para ver los datos entrantes. El ejemplo está configurado para enviar 50 unidades de temperatura interior, 55 unidades de temperatura exterior y 50 de humedad. Tenga en cuenta que, de forma predeterminada, en el panel solo se muestran la temperatura y la humedad.

8. Ahora, vaya a la sección [Comando y control](#command) para obtener información sobre cómo cambiar la temperatura en el dispositivo desde la solución de supervisión remota.


8. Ahora, vaya a la sección Comando y control para obtener información sobre cómo cambiar la temperatura en el dispositivo desde la solución de supervisión remota.

### Ejecución del dispositivo en mbed

Las instrucciones siguientes describen los pasos para conectar un dispositivo [Freescale FRDM-K64F habilitado para mbed](https://developer.mbed.org/platforms/FRDM-K64F/) al Centro de IoT de Azure.


Requisitos

- Hardware necesario: [Freescale K64F habilitado para mbed](https://developer.mbed.org/platforms/FRDM-K64F/) o similar.

Conectar el dispositivo

- Conecte la placa a la red mediante un cable Ethernet. Este paso es necesario, ya que el ejemplo depende del acceso a Internet.

- Conecte el dispositivo al equipo mediante un cable micro USB. Asegúrese de conectar el cable al puerto USB correcto del dispositivo, como se ilustra [aquí](https://developer.mbed.org/platforms/frdm-k64f/), en la sección "Introducción".

- Siga las [instrucciones de la guía de mbed](https://developer.mbed.org/handbook/SerialPC) para configurar la conexión serie con el dispositivo desde el equipo de desarrollo. Si está en Windows, instale los controladores del puerto serie de Windows ubicados [aquí](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port).

Crear el proyecto de mbed e importar el código de ejemplo

- En el explorador web, vaya al sitio para desarrolladores [mbed.org](https://developer.mbed.org/). Si no se registró, verá una opción para crear una nueva cuenta (es gratis). De lo contrario, inicie sesión con las credenciales de su cuenta. A continuación, haga clic en **Compilador** en la esquina superior derecha de la página. Esto debería llevarle a la interfaz de administración del área de trabajo.

- Asegúrese de que la plataforma de hardware que está usando aparezca en la esquina superior derecha de la ventana, o bien haga clic en el icono de la esquina derecha para seleccionar la plataforma de hardware.

- Haga clic en **Importar** en el menú principal. A continuación, haga clic en **Haga clic aquí** para importar desde el vínculo de dirección URL situado junto al logotipo de globo terráqueo de mbed.

	![][6]

- En la ventana emergente, especifique el vínculo para el código de ejemplo https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

	![][7]

- Puede ver en el compilador de mbed que en la importación de este proyecto se importaron varias bibliotecas. El equipo de IoT de Azure proporciona y mantiene algunas ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), mientras que otras son bibliotecas de terceros que están disponibles en el catálogo de bibliotecas de mbed.

	![][8]

- Abra remote\_monitoring\\remote\_monitoring.c y busque el código siguiente en el archivo: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Reemplace "[Device Id]" y "[Device Key] por los datos de su dispositivo.

4. Use los datos de dispositivo del nombre de host del Centro de IoT para rellenar el nombre y el sufijo de IoTHub. Para ello, debe dividirlo de la forma siguiente:

    Si el nombre de host del Centro de IoT es "Contoso.azure devices.net", "Contoso" será el nombre de IoTHub y el resto el sufijo. Debería ser parecido a este:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
    ![][9]

Compilar y ejecutar el programa

- Haga clic en **Compilar** para compilar el programa. Puede omitir con seguridad las advertencias, pero si la compilación genera errores, corríjalos antes de continuar.

- Si la compilación se realiza correctamente, se genera un archivo .bin con el nombre del proyecto. Copie el archivo .bin en el dispositivo. Al guardar el archivo .bin en el dispositivo se restablece la sesión de terminal actual con el dispositivo. Tras reconectar, restablezca de nuevo el terminal manualmente o inicie un nuevo terminal. De esta forma el dispositivo de mbed se restablece y comienza a ejecutarse el programa.

- Conecte con el dispositivo mediante una aplicación cliente de SSH, como PuTTY. Puede determinar el puerto serie que el dispositivo usa comprobando el Administrador de dispositivos de Windows:


- En PuTTY, haga clic en el tipo de conexión **serie**. Lo más probable es que el dispositivo se conecte en 115200, así que escriba ese valor en el cuadro **Speed** (Velocidad). Haga clic en **Open** (Abrir).

	![][11]

El programa comienza a ejecutarse. Puede que tenga que restablecer la placa (presione CTRL+pausa o el botón de reinicio de la placa) si el programa no se inicia automáticamente cuando se conecta.

Visualice el dispositivo registrado y los datos. Vuelva al panel de la solución de supervisión remota. Verá que el estado del dispositivo cambió a En ejecución en la lista de dispositivos. ![][18]

8. Haga clic en el panel para ver los datos entrantes. El ejemplo está configurado para enviar 50 unidades de temperatura interior, 55 unidades de temperatura exterior y 50 de humedad. Tenga en cuenta que, de forma predeterminada, en el panel solo se muestran la temperatura y la humedad.

8. Ahora, vaya a la sección [Comando y control](#command) para obtener información sobre cómo cambiar la temperatura en el dispositivo desde la solución de supervisión remota.



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

Para aprender a enviar comandos y controlar, vaya a esa sección a continuación en este tutorial.

## Envío de datos de dispositivo a la solución de supervisión remota mediante node.js



-   En nuestro repositorio azure-iot-sdks, busque los siguientes archivos: packages.json (under /node/common) y remote\_monitoring.js under node/device/samples/). Cópielos en el dispositivo y colóquelos en la misma carpeta.

- Abra el archivo remote-monitoring.js y busque las siguientes variables:


   ```
   static const char* deviceId = "[Device Id]";
   static const char* deviceKey = "[Device Key]";
   static const char* hubName = "[IoTHub Name]";
   static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
   ```

-  Reemplace "[Device Id]" y "[Device Key] por los datos de su dispositivo.

-  Use los datos de dispositivo del nombre de host del Centro de IoT para rellenar el nombre y el sufijo de IoTHub. Para ello, debe dividirlo de la forma siguiente:

   Si el nombre de host del Centro de IoT es "Contoso.azure devices.net", "Contoso" será el nombre de IoTHub y el resto el sufijo. Debería ser parecido a este:


   ```
     static const char* deviceId = "mydevice";
   static const char* deviceKey = "mykey";
   static const char* hubName = "Contoso";
   static const char* hubSuffix = "azure-devices.net";
   ```


- Guarde el archivo . Ejecute el siguiente comando en la carpeta de destino:

```
npm install
node .
```

3.  Sustituya cada una de las variables por la información recopilada en el paso anterior. Guarde los cambios.


4. Abra un shell (Linux) o un símbolo del sistema de Node.js (Windows) y vaya a la carpeta **node\\samples**. A continuación, ejecute la aplicación de ejemplo usando el comando siguiente:

    ```
    node simple_sample_remotemonitoring.js
    ```

Visualizar el dispositivo y los datos entrantes

6. En el portal de soluciones preconfiguradas, haga clic en la sección de dispositivos para asegurarse de que el estado del dispositivo cambió a "En ejecución" y que puede ver todos los datos del fabricante.

7. Haga clic en el panel y seleccione su dispositivo en "Vista de dispositivo". Ahora debería ver que sus datos de telemetría se supervisan en la solución de administración remota.


## <a name="command"></a>Enviar comandos al dispositivo y controlarlo desde el panel

Ahora que el dispositivo está conectado y envía datos de temperatura generados automáticamente, puede enviar comandos al dispositivo y controlarlo de forma remota desde el Centro de IoT. Puede implementar varios tipos de comandos según su aplicación empresarial. En este caso, hemos implementado un cambio de temperatura, como si hubiera una necesidad de controlarlo desde la solución. Para enviar el comando, debe:

-  Hacer clic en el identificador del dispositivo en la lista de dispositivos (puede encontrar la sección de dispositivos en el menú de la izquierda).

	![][13]

- En el menú de la derecha donde se muestran los detalles del dispositivo, haga clic en "Enviar comando".


- Seleccione el comando que quiere ejecutar; en este caso, elegimos "Set temperature", puesto que queremos cambiar la temperatura en la que está establecido el dispositivo. Seleccione ese comando y elija el valor de temperatura. Haga clic en "Enviar comando" y la temperatura nueva se insertará en el dispositivo. Nota: verá que en el historial de comandos el resultado del comando es "Pendiente". Esto se debe a que, por motivos de simplicidad, en este ejemplo no se implementó ninguna lógica en el dispositivo para responder al Centro de IoT. Para ello, es necesario ampliar la solución.

	![][14]
- Vuelva al panel y asegúrese de que los datos se actualizaron. Verá que se muestran estadísticas actualizadas sobre la temperatura y los nuevos datos en el historial de telemetría.




[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## Pasos siguientes

Existen varias maneras de ampliar la funcionalidad de este ejemplo: conectar el sensor real al dispositivo para enviar la fecha real, implementar la funcionalidad de comando y control, etc. Use nuestra [guía](articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md) sobre cómo ampliar la solución de supervisión remota para obtener más información al respecto.

<!---HONumber=Oct15_HO3-->