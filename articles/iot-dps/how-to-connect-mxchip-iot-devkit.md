---
title: Uso de MXChip IoT DevKit para conectarse al servicio Azure IoT Hub Device Provisioning | Microsoft Docs
description: Uso de MXChip IoT DevKit para conectarse al servicio Azure IoT Hub Device Provisioning
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>Conexión de MXChip IoT DevKit al servicio Azure IoT Hub Device Provisioning

En este artículo se describe cómo configurar el DevKit para que se registre automáticamente en IoT Hub mediante el servicio Device Provisioning. En este tutorial, aprenderá a:

* Configurar un punto de conexión global de DPS en el dispositivo
* Usar el secreto único de dispositivo (UDS) para generar el certificado X.509
* Inscribir un dispositivo individual
* Comprobar que el dispositivo está registrado

[MXChip IoT DevKit](https://aka.ms/iot-devkit) es una placa integral compatible de Arduino con periféricos y sensores varios. Se puede desarrollar para ella con la [extensión de Visual Studio Code para Arduino](https://aka.ms/arduino). Incluye un [catálogo de proyectos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) cada vez mayor que sirve de guía para crear prototipos de soluciones de Internet de las cosas (IoT) que aprovechen los servicios de Microsoft Azure.

## <a name="before-you-begin"></a>Antes de empezar

Para completar este tutorial, necesitará lo siguiente:

* Prepare el DevKit con la [guía de introducción](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Actualice al firmware más reciente (> = 1.3.0) con el [tutorial de actualización](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Cree y vincule IoT Hub con la instancia del servicio Device Provisioning con la [configuración del aprovisionamiento automático](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Establecer la configuración del servicio Device Provisioning en el dispositivo

Para habilitar el DevKit para conectarse a la instancia de servicio Device Provisioning que ha creado:

1. En Azure Portal, seleccione la hoja **Información general** del servicio Device Provisioning y anote los valores de **Punto de conexión global del dispositivo** y de **Ámbito de Id**.
  ![Punto de conexión global y el ámbito de identificador de DPS](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Asegúrese de que `git` está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente.

3. Abra el símbolo del sistema. Clone el repositorio de GitHub para el código de ejemplo de DPS:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Inicie VS Code y conecte el DevKit al equipo; después, abra la carpeta que contiene el código que ha clonado.

5. Abra **DevKitDPS.ino**, busque y reemplace `[Global Device Endpoint]` y `[ID Scope]` por los valores que acaba de anotar.
  ![Punto de conexión de DPS](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) Puede dejar el valor de **registrationId** en blanco, pues la aplicación generará un valor basado en la versión de firmware y en la dirección MAC. Si desea personalizarlo, el identificador de registro tiene que utilizar combinaciones alfanuméricas, de minúsculas y guiones solo con un máximo de 128 caracteres. Para más información, consulte [Administración de inscripciones de dispositivos con Azure Portal](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Use **Quick Open** en VS Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) y escriba **task device-upload** para crear y cargar el código en el DevKit.

7. Observe que la tarea se ha realizado correctamente en la ventana de salida.

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>Guardado del secreto único de dispositivo en el chip de seguridad de STSAFE

El servicio Device Provisioning puede configurarse en el dispositivo en función de su [módulo de seguridad de hardware (HSM)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). DevKit utiliza la tecnología [Device Identity Composition Engine (DICE)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) de [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). Se usa un **secreto único de dispositivo (UDS)** guardado en el chip de seguridad de STSAFE en el DevKit para generar el certificado [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) único del dispositivo. El certificado se puede utilizar más adelante en el proceso de inscripción en el servicio Device Provisioning.

Un **secreto único de dispositivo (UDS)** típico consiste en una cadena larga de 64 caracteres. Este es un ejemplo de un UDS:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Cada uno de estos dos caracteres se utiliza como valor hexadecimal en el cálculo de seguridad. Por tanto los UDS de ejemplo anteriores se resuelven en: "`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`".

Para guardar el secreto único de dispositivo en el DevKit:

1. Abra el monitor serie con una herramienta como Putty, consulte [Use Configuration Mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) (Uso del modo de configuración) para más información.

2. Con DevKit conectado al equipo, mantenga presionado el botón A, después presione y suelte el botón de restablecimiento para acceder al modo de configuración. La pantalla debe mostrar el identificador de DevKit y **'Configuration'** (Configuración).

3. Tome la cadena de UDS larga del ejemplo anterior y cambie uno o varios caracteres por otros valores entre `0` y `f`. Esto se utiliza como su propio UDS.

4. En la ventana del monitor serie, escriba **set_dps_uds [su_propio_valor_UDS]** y presione la tecla Entrar para guardarlo.
  > [!NOTE]
  > Por ejemplo, si establece su propia UDS cambiando los dos últimos caracteres a `f`, tiene que especificar el comando como **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**.

5. Sin cerrar la ventana del monitor serie, presione el botón Restablecer en el DevKit.

6. Anote el valor de **dirección MAC de DevKit** y la **versión de firmware de DevKit**.
  ![Versión de firmware](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>Generación de certificado X.509

### <a name="windows"></a>Windows

1. Abra el explorador de archivos y vaya a la carpeta que contiene el código de ejemplo de DSP clonado. En carpeta **.build** que verá, busque y copie **DPS.ino.bin** y **DPS.ino.map** en ella.
  ![Archivos generados](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Si ha cambiado la configuración de `built.path` para Arduino a otra carpeta. Necesita buscar esos archivos en la carpeta que haya configurado.

2. Pegue estos dos archivos en la carpeta **tools** en el mismo nivel que la carpeta **.build**.

3. Ejecute **dps_cert_gen.exe**, siga las instrucciones para escribir su **UDS**, la **dirección MAC** para DevKit y la **versión de firmware** para generar el certificado X.509.
  ![Ejecución de dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Observe que la generación se ha realizado correctamente porque en la misma carpeta se ha guardado el certificado **.pem**.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Creación de una entrada de inscripción de dispositivos en el servicio Device Provisioning

1. En Azure Portal, vaya al servicio de aprovisionamiento. Haga clic en **Administrar inscripciones** y seleccione la pestaña **Inscripciones individuales**. ![Inscripciones individuales](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Haga clic en **Agregar**.

3. En **Mecanismo**, elija **X.509**.
  ![Carga del certificado](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. En **Archivo .pem o .cer de certificado.**, cargue el certificado **.pem** que acaba de obtener.

5. Deje el resto tal como está y haga clic en **Guardar**.

## <a name="start-the-devkit"></a>Inicio del DevKit

1. Ejecute VS Code y abra el monitor serie.

2. Presione el botón **Restablecer** en DevKit.

Debería ver cómo el DevKit inicia el registro con el servicio Device Provisioning.

![Salida de VS Code](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>Compruebe que el DevKit esté registrado en IoT Hub

Cuando arranca el dispositivo, se deben realizar las siguientes acciones:

1. El dispositivo envía una solicitud de registro al servicio Device Provisioning.
2. El servicio devuelve un desafío de registro al que responde el dispositivo.
3. Una vez que el registro se ha realizado correctamente, el servicio Device Provisioning envía el identificador URI del centro de IoT, el del dispositivo y la clave cifrada nuevamente al dispositivo.
4. Luego, la aplicación cliente de IoT Hub del dispositivo se conecta al centro.
5. Después de una conexión correcta, el dispositivo aparecerá la instancia de Device Explorer del centro de IoT.
  ![Dispositivo registrado](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>Cambio del identificador del dispositivo

El identificador de dispositivo predeterminado registrado en Azure IoT Hub es **AZ3166**. Si desea modificarlo, siga [estas instrucciones](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemas y comentarios

Si tiene algún problema, acuda a [P+F](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), o también puede ponerse en contacto con nosotros a través de los siguientes canales:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>pasos siguientes

Ahora ha aprendido a preparar el DevKit para inscribir un dispositivo de forma segura en DPS mediante DICE, por tanto, ya lo puede registrar de manera automática en IoT Hub sin interacción. En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configurar un punto de conexión global de DPS en el dispositivo
> * Usar el secreto único de dispositivo (UDS) para generar el certificado X.509
> * Inscribir un dispositivo individual
> * Comprobar que el dispositivo está registrado

Consulte los otros tutoriales para obtener información sobre:

> [!div class="nextstepaction"]
> [Creación y aprovisionamiento de un dispositivo simulado](./quick-create-simulated-device.md)

