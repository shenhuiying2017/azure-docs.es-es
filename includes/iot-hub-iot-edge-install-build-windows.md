## <a name="install-the-prerequisites"></a>Instalación de los requisitos previos

1. Instale [Visual Studio 2015 o 2017](https://www.visualstudio.com). Puede usar la versión gratuita Community Edition si cumple los requisitos de concesión de licencias. No olvide incluir Visual C++ y el Administrador de paquetes NuGet.

1. Instale [git](http://www.git-scm.com) y asegúrese de que puede ejecutar git.exe desde la línea de comandos.

1. Instale [CMake](https://cmake.org/download/) y asegúrese de que puede ejecutar cmake.exe desde la línea de comandos. Se recomienda usar CMake versión 3.7.2 o posterior. El instalador **.msi** es la opción más sencilla en Windows. Agregue CMake a PATH al menos para el usuario actual cuando el instalador se lo solicite.

1. Instale [Python 2.7](https://www.python.org/downloads/release/python-27). Asegúrese de que agrega Python a su variable de entorno `PATH` en el **Panel de control -> Sistema -> Configuración avanzada del sistema -> Variables de entorno**.

1. En un símbolo del sistema, ejecute el siguiente comando para clonar el repositorio de GitHub de Azure IoT Edge en la máquina local:

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>Compilación del ejemplo

Ya puede compilar el runtime de IoT Edge y los ejemplos en la máquina local:

1. Abra un **símbolo del sistema para desarrolladores de VS2015** o un **símbolo del sistema para desarrolladores de VS2017**.

1. Vaya a la carpeta raíz en la copia local del repositorio **iot-edge**.

1. Ejecute el script de compilación como sigue:

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

Este script crea un archivo de solución de Visual Studio y compila la solución. Puede encontrar la solución de Visual Studio en la carpeta **build** de su copia local del repositorio **iot-edge**. Si desea compilar y ejecutar las pruebas unitarias, agregue el parámetro `--run-unittests`. Si desea compilar y ejecutar pruebas de extremo a extremo, agregue `--run-e2e-tests`.

> [!NOTE]
> Cada vez que ejecute el script **build.cmd**, elimine y luego vuelva a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **iot-edge**.