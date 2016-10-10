<properties
	pageTitle="Introducción al SDK de puerta de enlace del Centro de IoT | Microsoft Azure"
	description="Tutorial del SDK de puerta de enlace del Centro de IoT de Azure usando Windows para ilustrar los conceptos clave que debe comprender cuando utilice este SDK."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# SDK de puerta de enlace de IoT (beta): introducción mediante Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Compilación del ejemplo

Antes de comenzar, debe [configurar el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Windows.

1. Abra un **símbolo del sistema para desarrolladores para VS2015**.
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk**.
3. Ejecute el script **tools\\build.cmd**. Este script crea un archivo de solución de Visual Studio, genera la solución y ejecuta las pruebas. Puede encontrar la solución de Visual Studio en la carpeta **build** de su copia local del repositorio **azure-iot-gateway-sdk**¡.

## Ejecución del ejemplo

1. El script **build.cmd** crea una carpeta llamada **build** en la copia local del repositorio. Esta carpeta contiene los dos módulos utilizados en este ejemplo.

    El script de compilación coloca **logger\_hl.dll** en la carpeta **build\\modules\\logger\\Debug** y **hello\_world\_hl.dll** en la carpeta **build\\modules\\hello\_world\\Debug**. Utilice estas rutas de acceso para el valor de **ruta de acceso del módulo**, tal como se muestra en el archivo de configuración JSON siguiente.

2. El archivo **hello\_world\_win.json** de la carpeta **samples\\hello\_world\\src** es un ejemplo de archivo de configuración JSON para Windows que puede usar para ejecutar el ejemplo. En el ejemplo de configuración JSON que se muestra a continuación se da por hecho que ha clonado el repositorio del SDK de puerta de enlace en la raíz de la unidad **C:**. Si lo ha descargado en otra ubicación, debe ajustar los valores de **ruta de acceso del módulo** en el archivo **samples\\hello\_world\\src\\hello\_world\_win.json** según corresponda.

3. Para el módulo **logger\_hl**, de la sección **args**, establezca el valor de **nombre de archivo** en el nombre y la ruta de acceso del archivo que contendrá los datos de registro.

    Este es un ejemplo de un archivo de configuración JSON para Windows que se escribirá en el archivo **log.txt** en la raíz de la unidad **C:**.

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. En un símbolo del sistema, vaya a la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.
4. Ejecute el siguiente comando:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_0928_2016-->