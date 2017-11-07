## <a name="build-iot-edge"></a>Compilación de IoT Edge

Este tutorial usa módulos personalizados de IoT Edge para comunicarse con la solución preconfigurada de supervisión remota. Por tanto, es necesario compilar los módulos de IoT Edge desde el código fuente personalizado. Las secciones siguientes describen cómo instalar IoT Edge y compilar el módulo personalizado de IoT Edge.

### <a name="install-iot-edge"></a>Instalación de IoT Edge

Los pasos siguientes describen cómo instalar el software precompilado de IoT Edge en un Intel NUC:

1. Configure los repositorios necesarios de Smart Package mediante la ejecución de los comandos siguientes en el Intel NUC:

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Escriba `y` cuando el comando le pregunte **Include this channel?** (¿Incluir este canal?).

1. Actualice Smart Package Manager ejecutando el comando siguiente:

    ```bash
    smart update
    ```

1. Instale el paquete de Azure IoT Edge ejecutando el comando siguiente:

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Compruebe la instalación ejecutando el ejemplo "Hola mundo". Este ejemplo escribe el mensaje Hola mundo en el archivo log.txt cada cinco segundos. Los comandos siguientes ejecutan el ejemplo "Hola mundo":

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    Ignore cualquier mensaje de **argumento no válido** que se produzca al detener el ejemplo.

    Use el comando siguiente para ver el contenido del archivo de registro:

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Solución de problemas

Reinicie el dispositivo Intel NUC si ve el siguiente error: "no package provides util-linux-dev" (ningún paquete proporciona util-linux-dev).
