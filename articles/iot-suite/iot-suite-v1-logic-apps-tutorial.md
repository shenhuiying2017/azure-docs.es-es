---
title: Conjunto de aplicaciones de IoT de Azure y Logic Apps | Microsoft Docs
description: "Un tutorial sobre cómo enlazar aplicaciones lógicas con el conjunto de aplicaciones de IoT de Azure para el proceso empresarial."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Tutorial: Conexión de una aplicación lógica a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT de Azure
La solución preconfigurada de supervisión remota del [conjunto de aplicaciones de IoT de Microsoft Azure][lnk-internetofthings] constituye una excelente forma de empezar a trabajar rápidamente con una serie de características de extremo a extremo que ejemplifica una solución de IoT. Este tutorial le mostrará cómo agregar una aplicación lógica a su solución preconfigurada de supervisión remota de dicho conjunto de aplicaciones. Estos pasos muestran cómo puede aprovechar aún más la solución de IoT conectándola a un proceso empresarial.

*Si está buscando un tutorial sobre cómo aprovisionar una solución preconfigurada de supervisión remota, consulte [Tutorial: Introducción a las soluciones preconfiguradas][lnk-getstarted].*

Antes de comenzar este tutorial, debe:

* Aprovisionar la solución preconfigurada de supervisión remota en su suscripción de Azure.
* Crear una cuenta de SendGrid para poder enviar un correo electrónico que desencadene el proceso de negocio. Puede registrarse para obtener una cuenta de evaluación gratuita en [SendGrid](https://sendgrid.com/) haciendo clic en **Try for Free**(Probar gratis). Después de haberse registrado para crear una cuenta de evaluación gratuita, deberá generar una [clave de API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) en SendGrid que conceda permisos para enviar correo. La necesitará más adelante en el tutorial.

Para completar este tutorial, necesita Visual Studio 2015 o Visual Studio 2017 para modificar las acciones en el back-end de soluciones preconfiguradas.

Suponiendo que ya se haya aprovisionado la solución preconfigurada de supervisión remota, vaya al grupo de recursos correspondiente a esa solución en[ Azure Portal][lnk-azureportal]. El grupo de recursos tiene el mismo nombre que la solución de supervisión remota aprovisionada. En el grupo de recursos, puede ver todos los recursos de Azure aprovisionados para su solución. La captura de pantalla siguiente muestra una hoja de **grupo de recursos** de ejemplo para una solución preconfigurada de supervisión remota:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Para comenzar, configure la aplicación lógica que se usará con la solución preconfigurada.

## <a name="set-up-the-logic-app"></a>Configuración de la aplicación lógica
1. Haga clic en la opción **Agregar** que verá en la parte superior de la hoja del grupo de recursos en Azure Portal.
2. Busque la **aplicación lógica**, selecciónela y, después, haga clic en **Crear**.
3. Especifique un valor en **Nombre** y utilice los mismos valores de **Suscripción** y **Grupo de recursos** que usó al aprovisionar la solución de supervisión remota. Haga clic en **Crear**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. Cuando se complete la implementación, verá que la aplicación lógica aparece incluida en el grupo de recursos.
5. Haga clic en la aplicación lógica para desplazarse a la hoja de aplicación lógica y seleccione la plantilla **Aplicación lógica en blanco** para abrir el **Diseñador de aplicaciones lógicas**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Seleccione **Solicitud**. Esta acción especifica que una solicitud HTTP entrante con una carga específica con formato de JSON actúa como desencadenador.
7. Pegue el siguiente código en el esquema de JSON del cuerpo de la solicitud:
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > Puede copiar la dirección URL de HTTP POST tras guardar la aplicación lógica, pero, primero, debe agregar una acción.
   > 
   > 
8. Haga clic en **+ Nuevo paso** en el desencadenador manual. A continuación, haga clic en **Agregar una acción**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Busque la opción **SendGrid - Send email** (SendGrid: enviar correo electrónico) y haga clic en ella.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Escriba un nombre para la conexión, como **SendGridConnection**, escriba la **clave de API de SendGrid** que creó al configurar la cuenta de SendGrid y haga clic en **Crear**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Agregue direcciones de correo electrónico que posea tanto al campo **De** como a **Para**. Agregue **Alerta de supervisión remota [DeviceId]** al campo **Asunto**. En el campo **Cuerpo del correo electrónico**, agregue **. El dispositivo [DeviceId] ha informado de [measurementName] con el valor [measuredValue].** Puede agregar **[DeviceId]**, **[measurementName]** y **[measuredValue]** haciendo clic en la sección **Puede insertar datos de pasos anteriores**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Haga clic en **Guardar** en el menú superior.
13. Haga clic en el desencadenador **Solicitud** y copie el valor **HTTP POST a esta dirección URL**. Necesitará esta URL más adelante en el tutorial.

> [!NOTE]
> Logic Apps permite ejecutar [muchos tipos diferentes de acciones][lnk-logic-apps-actions], incluidas las de Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Configuración del trabajo web EventProcessor
En esta sección, se conectará la solución preconfigurada a la aplicación lógica creada. Para completar esta tarea, agregue la dirección URL para desencadenar la aplicación lógica con la acción que se activa cuando el valor del sensor de un dispositivo supera un umbral.

1. Use el cliente de Git para clonar la versión más reciente del [repositorio de GitHub azure-iot-remote-monitoring][lnk-rmgithub]. Por ejemplo:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. En Visual Studio, abra **RemoteMonitoring.sln** de la copia local del repositorio.
3. Abra el archivo **ActionRepository.cs** de la carpeta **Infrastructure\\Repository**.
4. Actualice el diccionario **actionIds** con el valor de **HTTP POST a esta dirección URL** que anotó de la aplicación lógica, como se indica a continuación:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Guarde los cambios de la solución y salga de Visual Studio.

## <a name="deploy-from-the-command-line"></a>Implementación desde la línea de comandos
En esta sección, implementará la versión actualizada de la solución de supervisión remota para reemplazar la que se encuentre actualmente en ejecución en Azure.

1. Siga las instrucciones de [configuración del entorno de desarrollo][lnk-devsetup] con el fin de prepararlo para la implementación.
2. Para efectuar la implementación localmente, siga las instrucciones de [implementación local][lnk-localdeploy].
3. Para efectuar la implementación en la nube y actualizar la existente, siga las instrucciones de [implementación de nube][lnk-clouddeploy]. Utilice el nombre de la implementación original como el nombre de la implementación. Por ejemplo, si la original se denomina **demologicapp**, utilice el siguiente comando:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   Cuando se ejecute el script de compilación, asegúrese de usar la misma cuenta, suscripción, región e instancia de Active Directory de Azure que utilizó al aprovisionar la solución.

## <a name="see-your-logic-app-in-action"></a>La aplicación lógica en acción
La solución preconfigurada de supervisión remota tiene dos reglas configuradas de forma predeterminada al aprovisionar una solución. Ambas se encuentran en el dispositivo **SampleDevice001** :

* Temperature > 38.00 (Temperatura > 38,00)
* Humidity > 48.00 (Humedad > 48,00)

La regla de temperatura desencadena la acción **Activar alarma**, mientras que la de humedad desencadena la acción **Enviar mensaje**. Suponiendo que utiliza la misma dirección URL para ambas acciones de la clase **ActionRepository** , la aplicación lógica se desencadenará con cualquiera de las dos reglas. Ambas reglas usan SendGrid para enviar un correo electrónico a la dirección **Para** con detalles de la alerta.

> [!NOTE]
> La aplicación lógica se sigue desencadenando cada vez que se alcance el umbral. Para evitar mensajes innecesarios, puede deshabilitar las reglas en el portal de la solución o deshabilitar la aplicación lógica en [Azure Portal][lnk-azureportal].
> 
> 

Además de recibir mensajes de correo electrónico, también puede ver cuándo se ejecuta la aplicación lógica en el portal:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha utilizado una aplicación lógica para conectar la solución preconfigurada a un proceso empresarial, puede obtener más información sobre las opciones para personalizar las soluciones preconfiguradas:

* [Uso de telemetría dinámica con la solución de supervisión remota preconfigurada][lnk-dynamic]
* [Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
