<properties
  pageTitle="El conjunto de aplicaciones de IoT de Azure y aplicaciones lógicas | Microsoft Azure"
  description="Un tutorial sobre cómo enlazar aplicaciones lógicas con el conjunto de aplicaciones de IoT de Azure para el proceso empresarial."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/20/2016"
  ms.author="araguila"/>
  
# Tutorial: Conexión de una aplicación lógica a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT de Azure

solución preconfigurada de supervisión remota del [conjunto de aplicaciones de IoT de Azure][lnk-internetofthings] constituye una excelente forma de empezar a trabajar rápidamente con una serie de características de extremo a extremo que ejemplifica una solución de IoT. Este tutorial le mostrará cómo agregar una aplicación lógica a su solución preconfigurada de supervisión remota de dicho conjunto de aplicaciones. Aquí se muestra cómo puede aprovechar aún más la solución de IoT conectándola a un proceso empresarial.

_Si está buscando un tutorial sobre cómo aprovisionar una solución preconfigurada de supervisión remota, consulte [Tutorial: Introducción a las soluciones preconfiguradas][lnk-getstarted]._

Antes de comenzar este tutorial, además de aprovisionar la solución preconfigurada de supervisión remota en su suscripción de Azure, debe crear una cuenta de SendGrid para poder enviar un correo electrónico que desencadene el proceso empresarial. Puede registrarse para obtener una cuenta gratuita en [SendGrid](https://sendgrid.com/) haciendo clic en **Try for Free** (Probar gratis). Después de haberse registrado para crear una cuenta de evaluación gratuita, deberá generar una [clave de API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) en SendGrid que conceda permisos para enviar correo. Las necesitará más adelante en el tutorial.

Suponiendo que ya se haya aprovisionado la solución preconfigurada de supervisión remota, vaya al grupo de recursos correspondiente a esa solución en el [Portal de Azure][lnk-azureportal]. El grupo de recursos tiene el mismo nombre que la solución de supervisión remota aprovisionada. En el grupo de recursos, puede ver todos los recursos de Azure aprovisionados previamente para su solución (a excepción de la aplicación Azure Active Directory, que se puede encontrar en el Portal de Azure clásico). La captura de pantalla siguiente muestra una hoja de **grupo de recursos** de ejemplo para una solución preconfigurada de supervisión remota:

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

Para comenzar, configure la aplicación lógica que se usará con la solución preconfigurada.

## Configuración de la aplicación lógica

1. Haga clic en la opción __Agregar__ que verá en la parte superior de la hoja del grupo de recursos en el Portal de Azure.

2. Busque la __aplicación lógica__, selecciónela y, después, haga clic en **Crear**.

3. Especifique un valor en __Nombre__ y utilice los mismos parámetros **Suscripción**, **Grupo de recursos** y **Plan de servicio de aplicaciones** que usó al aprovisionar la solución de supervisión remota. Haga clic en __Crear__.

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. Cuando se complete la implementación, verá que la aplicación lógica aparece incluida en el grupo de recursos.

5. Haga clic en la aplicación lógica para desplazarse a la hoja de estudio. Con ello, se abrirá inmediatamente el **diseñador de aplicaciones lógicas**.

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. Seleccione __Manual – When an HTTP request is received__ (Manual: cuando se recibe una solicitud HTTP). Con ello, se especifica que una solicitud HTTP entrante con una carga específica con formato de JSON actúa como desencadenador.

7. Pegue lo siguiente en el esquema de JSON del cuerpo de la solicitud:

    ```
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
    
    Nota: Puede copiar la dirección URL de HTTP POST tras guardar la aplicación lógica, pero, primero, debe agregar una acción.

8. Haga clic en el signo __(+)__ que verá bajo el desencadenador manual. A continuación, haga clic en **Agregar una acción**.

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. Busque la opción **SendGrid - Send email** (SendGrid: enviar correo electrónico) y haga clic en ella.

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. Escriba un nombre para la conexión, como **SendGridConnection**, escriba la **clave de API de SendGrid** que creó al configurar la cuenta de SendGrid y haga clic en **Crear conexión**.

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. Agregue direcciones de correo electrónico que posea tanto en el campo **De** como en **Para**. Agregue **Remote monitoring alert [DeviceId]** (Alerta de supervisión remota [DeviceId]) al campo **Asunto**. En el campo **Email Body** (Cuerpo del correo electrónico), agregue **El dispositivo [DeviceId] ha informado de [measurementName] con el valor [measuredValue]**. Puede agregar **[DeviceId]**, **[measurementName]** y **[measuredValue]** haciendo clic en la sección **You can insert data from previous steps** (Puede insertar datos de pasos anteriores).

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. Haga clic en __Guardar__ en el menú superior.

13. Haga clic en el desencadenador **When an HTTP request is received** (Cuando se recibe una solicitud HTTP) y copie el valor __Http Post to this URL__ (HTTP POST a esta URL). Necesitará esta URL más adelante en el tutorial.

> [AZURE.NOTE] Las aplicaciones lógicas permiten ejecutar [muchos tipos diferentes de acciones][lnk-logic-apps-actions], incluidas las de Office 365.

## Configuración del trabajo web EventProcessor

En esta sección, se conectará la solución preconfigurada a la aplicación lógica creada mediante la adición de la dirección URL que desencadena la aplicación lógica a la acción que se lleva a cabo cuando un valor de sensor de dispositivo supera cierto umbral.

1. Use el cliente de Git para clonar la versión más reciente del [repositorio de GitHub azure-iot-remote-monitoring][lnk-rmgithub]. Por ejemplo:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. En Visual Studio, abra __RemoteMonitoring.sln__ de la copia local del repositorio.

3. Abra el archivo __ActionRepository.cs__ de la carpeta **Infrastructure\\Repository**.

4. Actualice el diccionario **actionIds** con el valor de __Http Post to this URL__ (HTTP POST a esta URL) que anotó de la aplicación lógica, como se muestra seguidamente:

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. Guarde los cambios de la solución y salga de Visual Studio.

## Implementación desde la línea de comandos

En esta sección, implementará la versión actualizada de la solución de supervisión remota para reemplazar la que se encuentre actualmente en ejecución en Azure.

1. Siga las instrucciones de [configuración del entorno de desarrollo][lnk-devsetup] con el fin de prepararlo para la implementación.

2.  Para efectuar la implementación localmente, siga las instrucciones de [implementación local][lnk-localdeploy].

3.  Para efectuar la implementación en la nube y actualizar la existente, siga las instrucciones de [implementación de nube][lnk-clouddeploy]. Utilice el nombre de la implementación original como el nombre de la implementación. Por ejemplo, si la original se denomina **demologicapp**, utilice el siguiente comando:

    ``
    build.cmd cloud release demologicapp
    ``
    
    Cuando se ejecute el script de compilación, asegúrese de usar la misma cuenta, suscripción, región e instancia de Active Directory de Azure que utilizó al aprovisionar la solución por primera vez.

## La aplicación lógica en acción

La solución preconfigurada de supervisión remota tiene dos reglas configuradas de forma predeterminada al aprovisionar una solución por primera vez. Ambas se encuentran en el dispositivo **SampleDevice001**:

* Temperature > 38.00 (Temperatura > 38,00)
* Humidity > 48.00 (Humedad > 48,00)

La regla de temperatura desencadena la acción **Raise Alarm** (Activar alarma), mientras que la de humedad, desencadena la acción **SendMessage**. Suponiendo que haya usado la misma dirección URL para ambas acciones en la clase **ActionRepository**, la aplicación lógica desencadenará alguna de las dos reglas y utilizará SendGrid para enviar un correo electrónico a la dirección de **Para** con detalles sobre la alerta.

> [AZURE.NOTE] La aplicación lógica se seguirá desencadenando cada vez que se alcance el umbral, por lo que, para evitar mensajes innecesarios, puede deshabilitar las reglas en el portal de la solución o deshabilitar la aplicación lógica en el [Portal de Azure][lnk-azureportal].

Además de recibir mensajes de correo electrónico, también puede ver cuándo se ejecuta la aplicación lógica en el portal:

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## Pasos siguientes

Ahora que ha utilizado una aplicación lógica para conectar la solución preconfigurada a un proceso empresarial, puede obtener más información sobre las opciones para personalizar las soluciones preconfiguradas:

- [Uso de telemetría dinámica con la solución de supervisión remota preconfigurada][lnk-dynamic]
- [Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

<!---HONumber=AcomDC_0727_2016-->