---
title: Conectar un dispositivo con C en mbed | Microsoft Docs
description: "Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en un dispositivo de mbed."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.contentlocale: es-es
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Conectar el dispositivo a la solución preconfigurada de supervisión remota (mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Compilación y ejecución de la solución de ejemplo de C

Las instrucciones siguientes describen los pasos para conectar un dispositivo [Freescale FRDM-K64F habilitado para mbed][lnk-mbed-home] a la solución de supervisión remota.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Conexión del dispositivo mbed a la máquina de escritorio y a la red

1. Conecte el dispositivo mbed a la red mediante un cable Ethernet. Este paso es necesario porque la aplicación de ejemplo requiere acceso a Internet.

1. Vea [Introducción a mbed][lnk-mbed-getstarted] para conectar el dispositivo de mbed al equipo de escritorio.

1. Si el equipo de escritorio está ejecutando Windows, vea [Configuración del equipo][lnk-mbed-pcconnect] para configurar el acceso al puerto serie para el dispositivo mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Crear un proyecto de mbed e importar el código de ejemplo

Siga estos pasos para agregar algún código de ejemplo a un proyecto de mbed. Importará el proyecto de inicio de supervisión remota y luego cambiará el proyecto para usar el protocolo MQTT en lugar del protocolo AMQP. Actualmente, debe emplear el protocolo MQTT para usar las características de administración de dispositivos de IoT Hub.

1. En el explorador web, vaya al [sitio para desarrolladores](https://developer.mbed.org/)mbed.org. Si aún no se ha registrado, verá una opción para crear una cuenta (es gratuita). De lo contrario, inicie sesión con las credenciales de su cuenta. Luego haga clic en **Compilador** en la esquina superior derecha de la página. Al realizar esta acción, se mostrará la interfaz *Área de trabajo*.

1. Asegúrese de que la plataforma de hardware que está usando aparezca en la esquina superior derecha de la ventana, o bien haga clic en el icono de la esquina derecha para seleccionar la plataforma de hardware.

1. Haga clic en **Importar** en el menú principal. A continuación, haga clic en **Click here to import from URL** (Haga clic aquí para importar desde la dirección URL).
   
    ![Inicio de la importación al área de trabajo de mbed][6]

1. En la ventana emergente, escriba el vínculo al código de ejemplo https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ y, después, haga clic en **Importar**.
   
    ![Importación del código de ejemplo al área de trabajo de mbed][7]

1. Puede ver en la ventana del compilador de mbed que se importaron varias bibliotecas durante la importación de este proyecto. El equipo de IoT de Azure ofrece y mantiene algunas bibliotecas ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), mientras que otras son bibliotecas de terceros que están disponibles en el catálogo de bibliotecas de mbed.
   
    ![Visualización del proyecto de mbed][8]

1. En el **área de trabajo de programa**, haga clic con el botón derecho en la biblioteca **iothub\_amqp\_transport**, haga clic en **Delete** (Eliminar) y luego en **OK** (Aceptar) para confirmar.

1. En el **área de trabajo de programa**, haga clic con el botón derecho en la biblioteca **azure\_amqp\_c**, haga clic en **Delete** (Eliminar) y luego en **OK** (Aceptar) para confirmar.

1. Haga clic con el botón derecho en el proyecto **remote_monitoring** en el **área de trabajo de programa**, seleccione **Import Library** (Importar biblioteca) y luego seleccione **From URL** (Desde dirección URL).
   
    ![Inicio de la importación de la biblioteca al área de trabajo de mbed][6]

1. En la ventana emergente, especifique el vínculo de la biblioteca de transporte de MQTT https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/ y luego haga clic en **Import** (Importar).
   
    ![Importación de la biblioteca al área de trabajo de mbed][12]

1. Repita el paso anterior para agregar la biblioteca MQTT desde https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/.

1. El área de trabajo se parece ahora a esta:

    ![Visualización de área de trabajo de mbed][13]

1. Abra el archivo remote\_monitoring\remote_monitoring.c y sustituya las instrucciones `#include` existentes por el código siguiente:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. Elimine el resto de código del archivo remote\_monitoring\remote\_monitoring.c.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo

Agregue código para invocar la función **remote\_monitoring\_run** y luego compile y ejecute la aplicación del dispositivo.

1. Agregue una función **main** con el código siguiente al final del archivo remote\_monitoring.c para invocar la función **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Haga clic en **Compilar** para compilar el programa. Puede omitir con seguridad las advertencias, pero si la compilación genera errores, corríjalos antes de continuar.

1. Si la compilación es correcta, el sitio web del compilador de mbed genera un archivo .bin con el nombre del proyecto y lo descarga en el equipo local. Copie el archivo .bin en el dispositivo. Si guarda el archivo .bin en el dispositivo, este último se reiniciará y ejecutará el programa incluido en el archivo .bin. Puede reiniciar manualmente el programa en cualquier momento presionando el botón Restablecer en el dispositivo mbed.

1. Conecte con el dispositivo mediante una aplicación cliente de SSH, como PuTTY. Puede determinar el puerto serie que usa el dispositivo comprobando el Administrador de dispositivos de Windows.
   
    ![][11]

1. En PuTTY, haga clic en el tipo de conexión **serie** . Como el dispositivo normalmente se conecta a 9600 baudios, especifique 9600 en el cuadro **Velocidad** . A continuación, haga clic en **Abrir**.

1. El programa comienza a ejecutarse. Si el programa no se inicia automáticamente al conectarse, puede que tenga que restablecer la placa (presione CTRL+Pausa o pulse el botón de reinicio de la placa).
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

