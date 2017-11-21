## <a name="configure-the-nodejs-simulated-device"></a>Configuración del dispositivo simulado de Node.js
1. En el panel de supervisión remota, haga clic en **+ Agregar un dispositivo** y agregue un *dispositivo personalizado*. Tome nota del nombre de host de IoT Hub, del identificador de dispositivo y de la clave de dispositivo. Los necesitará más adelante en este tutorial al preparar la aplicación cliente del dispositivo remote_monitoring.js.
2. Asegúrese de que tiene instalada la versión 0.12.x o posterior de Node.js en el equipo de desarrollo. Ejecute `node --version` en un símbolo del sistema o en un shell para comprobar la versión. Para obtener información sobre el uso de un administrador de paquetes para instalar Node.js en Linux, consulte [Installing Node.js via package manager][node-linux] (Instalación de Node.js con un administrador de paquetes).
3. Con Node.js instalado, clone la versión más reciente del repositorio [azure-iot-sdk-node][lnk-github-repo] en el equipo de desarrollo. Para ver la versión más reciente de las bibliotecas y los ejemplos siempre debe usar siempre la rama **principal** .
4. Desde su copia local del repositorio [azure-iot-sdk-node][lnk-github-repo], copie los siguientes dos archivos de la carpeta node/device/samples a una carpeta vacía de su equipo de desarrollo:
   
   * packages.json
   * remote_monitoring.js
5. Abra el archivo remote-monitoring.js y busque la siguiente definición de variable:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Reemplace **[IoT Hub device connection string]** con la cadena de conexión al dispositivo. Use los valores para el nombre de host de IoT Hub, el identificador de dispositivo y la clave de dispositivo que anotó en el paso 1. Una cadena de conexión de dispositivo tiene el siguiente formato:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Si su nombre de host de IoT Hub es **contoso** y el identificador del dispositivo es **mydevice**, su cadena de conexión tendrá este aspecto:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Guarde el archivo . Para instalar los paquetes necesarios, ejecute los siguientes comandos en un shell o símbolo del sistema de la carpeta que los contenga y ejecute la aplicación de ejemplo:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observación de la telemetría dinámica en acción
El panel muestra los datos de telemetría relativos a la temperatura y humedad de los dispositivos simulados existentes:

![Panel predeterminado][image1]

Si selecciona el dispositivo simulado de Node.js que se ejecutó en la sección anterior, verá los datos de telemetría relativos a la temperatura, la humedad y la temperatura externa:

![Temperatura exterior agregada al panel][image2]

La solución de supervisión remota detecta automáticamente el tipo de datos adicionales de telemetría relativos a la temperatura exterior y los agrega al gráfico del panel.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png