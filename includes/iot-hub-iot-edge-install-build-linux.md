## <a name="install-the-prerequisites"></a>Instalación de los requisitos previos

En los pasos de este tutorial se supone que está ejecutando Ubuntu Linux.

Abra un shell y ejecute los comandos siguientes para instalar los paquetes de requisitos previos:

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

En el shell, ejecute el siguiente comando para clonar el repositorio de GitHub de Azure IoT Edge en la máquina local:

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo

Ya puede compilar el runtime de IoT Edge y los ejemplos en la máquina local:

1. Abra un shell.

1. Vaya a la carpeta raíz en la copia local del repositorio **iot-edge**.

1. Ejecute el script de compilación como sigue:

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **iot-edge** y generar un archivo Make. A continuación, el script compila la solución, omitiendo las pruebas unitarias y las pruebas completas. Si desea compilar y ejecutar las pruebas unitarias, agregue el parámetro `--run-unittests`. Si desea compilar y ejecutar pruebas de extremo a extremo, agregue `--run-e2e-tests`.

> [!NOTE]
> Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **iot-edge**.