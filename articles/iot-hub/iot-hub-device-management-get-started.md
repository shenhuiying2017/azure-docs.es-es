<properties
	pageTitle="Introducción a la administración de dispositivos del Centro de IoT | Microsoft Azure"
	description="Tutorial de introducción del Centro de IoT de Azure con C# sobre la administración de dispositivos. Use el Centro de IoT de Azure y C# con los SDK de IoT de Microsoft Azure para implementar la administración de dispositivos."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Introducción a la administración de dispositivos de Centro de IoT de Azure con C# (versión preliminar)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introducción
Para comenzar con la administración de dispositivos en un Centro de IoT de Azure, primero debe crear un Centro de IoT de Azure, abastecerlo de dispositivos, iniciar varios dispositivos simulados y ver estos dispositivos en la interfaz de usuario de ejemplo de administración de dispositivos. Este tutorial le guiará a través de estos pasos.

> [AZURE.NOTE]  Debe crear un nuevo Centro de IoT para habilitar las funcionalidades de administración de dispositivos, aunque ya disponga de un Centro de IoT existente, puesto que los Centros de IoT existentes aún no pueden administrar dispositivos. Una vez que la administración de dispositivos esté completamente operativa, todos los Centros de IoT existentes se actualizarán con las funcionalidades de administración de dispositivos.

## Requisitos previos

En este tutorial se asume que está utilizando un equipo de desarrollo de Windows.

Para llevar esto a cabo, necesita tener lo siguiente instalado:

- Microsoft Visual Studio 2015

- Git

- CMake (versión 2.8 o posterior). Instale CMake desde <https://cmake.org/download/>. Para un equipo con Windows, elija la opción Windows Installer (.msi). Asegúrese de marcar la casilla para agregar CMake a la variable PATH del usuario actual.

- Node.js 6.1.0 o superior. Instale Node.js para su plataforma desde <https://nodejs.org/>.

- Una suscripción de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

## Creación de un Centro de IoT con la administración de dispositivos habilitada

Debe crear un Centro de IoT con la administración de dispositivos habilitada para conectar los dispositivos simulados. En los siguientes pasos se muestra cómo completar esta tarea con el Portal de Azure.

1.  Inicie sesión en el [Portal de Azure].
2.  En la barra de accesos directos, haga clic en **Nuevo**. A continuación, haga clic en **Internet de las cosas** y en **Centro de IoT de Azure**.

	![][img-new-hub]

3.  En la hoja **Centro de IoT**, elija la configuración para su Centro de IoT.

	![][img-configure-hub]

  -   En el cuadro **Nombre**, escriba un nombre para identificar el Centro de IoT. Si el **Nombre** es válido y está disponible, aparecerá una marca de verificación verde en el cuadro **Nombre**.
  -   Seleccione un **Plan de tarifa y escalado**. Este tutorial no requiere ningún nivel determinado.
  -   En **Grupo de recursos**, cree un grupo de recursos o seleccione uno existente. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure].
  -   Active la casilla **Enable Device Management** (Habilitar la administración de dispositivos).
  -   En **Ubicación**, seleccione la ubicación para hospedar su Centro de IoT. La administración de dispositivos del Centro de IoT solo está disponible en el este de EE. UU., Europa del Norte y Asia Oriental. En el futuro, estará disponible en todas las regiones.

    > [AZURE.NOTE]  Si no activa la casilla **Enable Device Management** (Habilitar la administración de dispositivos), los ejemplos no funcionarán.

4.  Cuando haya elegido las opciones de configuración del Centro de IoT, haga clic en **Crear**. Azure puede tardar unos minutos en crear el Centro de IoT. Para comprobar el estado, puede supervisar el progreso en el **Panel de inicio** o en el panel de **notificaciones**.

	![][img-monitor]

5.  Cuando haya creado el Centro de IoT, abra la hoja del nuevo Centro de IoT, tome nota del **nombre de host** y, después, haga clic en el icono de **llave**.

	![][img-keys]

6.  Haga clic en la directiva **iothubowner** y copie y anote la cadena de conexión en la hoja **iothubowner**. Cópiela en una ubicación a la que pueda acceder después, ya que la necesitará para completar el tutorial.

 	> [AZURE.NOTE] En escenarios de producción, asegúrese de no usar las credenciales de **iothubowner**.

	![][img-connection]

Ya ha creado un Centro de IoT con la administración de dispositivos habilitada. Necesitará la cadena de conexión para completar el resto de este tutorial.

## Compilación de ejemplos y aprovisionamiento de dispositivos en el Centro de IoT

En esta sección ejecutará un script que genera el dispositivo simulado y los ejemplos, y proporciona un conjunto de nuevas identidades de dispositivo al registro de dispositivos del Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de dispositivos.

Para compilar los ejemplos y aprovisionar dispositivos en el Centro de IoT, siga estos pasos:

1.  Abra el **Símbolo del sistema para desarrolladores de VS2015**.

2.  Clone el repositorio GitHub. **Asegúrese de clonarlo en un directorio que sin espacios en blanco.**

	  ```
	  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
	  ```

3.  En la carpeta raíz donde clonó el repositorio **azure-iot-sdks**, navegue hasta la carpeta **\\azure-iot-sdks\\csharp\\service\\samples** y ejecute el archivo después de reemplazar el valor del marcador de posición por la cadena de conexión de la sección anterior:

	  ```
	  setup.bat <IoT Hub Connection String>
	  ```

El script hace lo siguiente:

1.  Ejecuta **cmake** para crear una solución de Visual Studio 2015 para el dispositivo simulado. Este archivo de proyecto es **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**. Observe que los archivos de código fuente se encuentran en la carpeta ***azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample**.

2.  Compila el proyecto **iotdm\_simple\_sample.vcxproj** para el dispositivo simulado.

3.  Compila los ejemplos **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln** de administración de dispositivos.

4.  Ejecuta **GenerateDevices.exe** para proporcionar las identidades de dispositivo al Centro de IoT. Los dispositivos se describen en **sampledevices.json** (ubicado en la carpeta **azure-iot-sdks\\node\\service\\samples**) y después se aprovisionan los dispositivos; las credenciales se almacenan en el archivo **devicecreds.txt** (ubicado en la carpeta **azure-iot-sdks\\csharp\\service\\samples\\bin**).

## Inicio de los dispositivos simulados

Ahora que los dispositivos se han agregado al registro de dispositivos, puede iniciar los dispositivos simulados. Para cada identidad de dispositivo proporcionada al Centro de IoT de Azure se inicia un dispositivo simulado.

Desde el símbolo del sistema para desarrolladores de la carpeta **\\azure-iot-sdks\\csharp\\service\\samples\\bin**, ejecute:

  ```
  simulate.bat
  ```

Este script ejecuta una instancia de **iotdm\_simple\_sample.exe** para cada dispositivo de los que se enumeran en el archivo **devicecreds.txt**. El dispositivo simulado seguirá ejecutándose hasta que cierre la ventana de comandos.

La aplicación de ejemplo **iotdm\_simple\_sample** se genera a partir de la biblioteca del cliente de administración de dispositivos del Centro de IoT de Azure para C, que permite la creación de dispositivos IoT que pueden administrarse mediante el Centro de IoT de Azure. Los fabricantes de los dispositivos pueden usar esta biblioteca para informar de propiedades de los dispositivos e implementar las acciones de ejecución que necesitan los trabajos de dispositivo. Esta biblioteca es un componente que se ofrece como parte del SDK de Centro de IoT de Azure de código abierto.

Al ejecutar **simulate.bat**, verá la transmisión de datos en la ventana de resultados. Esta salida muestra el tráfico entrante y saliente, así como las instrucciones de **printf** de las funciones de devolución de llamada específicas de la aplicación. Esto le permite ver el tráfico entrante y saliente junto, además de cómo la aplicación de ejemplo maneja los paquetes descodificados. Cuando el dispositivo se conecta al Centro de IoT, el servicio comienza automáticamente a observar los recursos del dispositivo. Después, la biblioteca del cliente de DM del Centro de IoT invoca las devoluciones de llamada del dispositivo para recuperar los valores más recientes del dispositivo.

A continuación se muestra la salida de la aplicación de prueba **iotdm\_simple\_sample**. En la parte superior, verá el mensaje **REGISTRADO**, que muestra el dispositivo con identificador **Device11 7ce4a850** conectándose al Centro de IoT.

> [AZURE.NOTE]  Para que el resultado sea menos detallado, genere y ejecute la configuración de venta directa.

![][img-output]

Asegúrese de dejar todos los dispositivos simulados en ejecución mientras completa las secciones siguientes.

## Ejecución de la interfaz de usuario de ejemplo de administración de dispositivos

Ahora que ha aprovisionado un Centro de IoT y tiene varios dispositivos simulados en ejecución y registrados para administración, puede implementar la interfaz de usuario de ejemplo de administración de dispositivos. La interfaz de usuario de ejemplo de administración de dispositivos proporciona un ejemplo práctico de cómo utilizar las API de administración de dispositivos para crear una experiencia interactiva de la interfaz de usuario. Para más información sobre la interfaz de usuario de ejemplo de administración de dispositivos, incluidos los [problemas conocidos](https://github.com/Azure/azure-iot-device-management#knownissues), consulte el repositorio de GitHub sobre la [interfaz de usuario de administración de dispositivos de IoT de Azure][lnk-dm-github].

Para recuperar, compilar y ejecutar la interfaz de usuario de ejemplo de administración de dispositivos, siga estos pasos:

1. Abra el **símbolo del sistema**.

2. Confirme que ha instalado Node.js 6.1.0, o una versión superior, según la sección de requisitos previos, escriba `node --version`.

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

6. Utilice un editor de texto para abrir el archivo user-config-json en la raíz de la carpeta clonada. Reemplace el texto "&lt;LA CADENA DE CONEXIÓN AQUÍ&gt;" por la cadena de conexión de Centro de IoT de la sección anterior y guarde el archivo.

7. En el símbolo del sistema, ejecute el siguiente comando para iniciar la aplicación de experiencia de usuario de administración de dispositivos:

	```
	npm run start
	```

8. Cuando el símbolo del sistema informa que los servicios se han iniciado, abra un explorador web (Edge/IE 11+/Safari/Chrome se admiten en este momento) y vaya a la aplicación de administración de dispositivos en la siguiente dirección URL para ver los dispositivos simulados: <http://127.0.0.1:3003>.

	![][img-dm-ui]

Deje ejecutándose los dispositivos simulados y la aplicación de administración de dispositivos mientras trabaja en el siguiente tutorial de administración de dispositivos.


## Pasos siguientes

Para continuar con la introducción a Centro de IoT, consulte la [introducción al SDK de puerta de enlace][lnk-gateway-SDK].

Para aprender más sobre las características de administración de dispositivos de Centro de IoT de Azure, consulte el tutorial [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Portal de Azure]: https://portal.azure.com/
[Uso de grupos de recursos para administrar los recursos de Azure]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

<!---HONumber=AcomDC_0713_2016-->