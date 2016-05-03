<properties
	pageTitle="Ejemplo de IoT de Azure MyDriving - Inicio rápido | Microsoft Azure"
	description="Empiece a trabajar con una aplicación que es una demostración completa de cómo diseñar un sistema IoT con Microsoft Azure, incluido Análisis de transmisiones, Aprendizaje automático y Centros de eventos."
	services=""
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="iot-suite"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="awills"/>

# Sistema IoT de MyDriving: inicio rápido

MyDriving es un sistema que muestra el diseño y la implementación de una solución de [Internet de las cosas](iot-suite-overview.md) (IoT) típica que recopila datos de telemetría de dispositivos, procesa dichos datos en la nube y aplica el aprendizaje automático para proporcionar una respuesta adaptable. La demostración registra datos sobre sus viajes en coche, mediante el uso de los datos de su teléfono móvil y un adaptador que recopila información del sistema de control de su automóvil. Estos datos se utilizan para proporcionar comentarios sobre su estilo de conducción en comparación con otros usuarios.

El objetivo real de MyDriving es animarle a crear su propia solución de IoT. Pero antes de eso, vamos a comenzar a hacerlo con la propia aplicación MyDriving, como si fuera un miembro de nuestro equipo de usuarios de prueba. Esto le proporciona una experiencia de la aplicación y del sistema subyacente como consumidor, antes de profundizar en la arquitectura. También le presenta HockeyApp, una forma útil de administrar las distribuciones alfa y beta de las aplicaciones para usuarios de prueba.

## Uso de la experiencia móvil

Puede utilizar la aplicación MyDriving si tiene un dispositivo Android, iOS o Windows 10.

### Instalación de Android y Windows 10 Mobile

En su dispositivo:

1.  Dé permiso a las aplicaciones de desarrollo:

    -   Android: en **Ajustes**, **Seguridad**, dé permiso a las aplicaciones de **orígenes desconocidos**.

    -   Windows 10: en **Configuración**, **Actualizaciones**, **Para desarrolladores**, establezca **Modo de programador**.

2.  Únase a nuestro equipo de prueba beta mediante el registro o el inicio de sesión en [HockeyApp](https://rink.hockeyapp.net). HockeyApp facilita la distribución de versiones anteriores de su aplicación a usuarios de prueba.

    Si está utilizando Windows 10, use el explorador Edge.

    Si es un asistente de Build 2016, inicie sesión con el mismo correo electrónico de la cuenta de Microsoft que ha registrado para la conferencia, mediante uno de los botones de Microsoft. Ya está registrado en HockeyApp.

    ![Pantalla de inicio de sesión de HockeyApp](./media/iot-solution-get-started/image1.png)

3.  Descargue e instale la aplicación desde aquí:

    -   [Android](http://rink.io/spMyDrivingAndroid)

    -   [Windows 10](http://rink.io/spMyDrivingUWP)

    Hay dos elementos. Instale el certificado en **Personas de confianza**. A continuación, instale la aplicación.

*¿Tiene problemas al iniciar la aplicación en Windows 10 Mobile?* Puede ser que el teléfono tenga pendientes una o dos actualizaciones. Asegúrese de que tiene las actualizaciones más recientes, o instale:

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 

 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx)

 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)


### Instalación para iOS

Si ha asistido a Build 2016, descargue la aplicación como miembro de nuestro equipo de prueba en HockeyApp:

1.  En el dispositivo iOS, inicie sesión en [HockeyApp](https://rink.hockeyapp.net). Use uno de los botones de inicio de sesión de Microsoft e inicie sesión con la misma cuenta de Microsoft con el que se ha registrado para la conferencia. (No utilice los campos de correo electrónico y contraseña).

    ![Pantalla de inicio de sesión de HockeyApp](./media/iot-solution-get-started/image1.png)

2.  En el panel de HockeyApp, seleccione MyDriving y descárguelo.

3.  Autorice la versión beta desde HockeyApp:

    a. Vaya a **Settings** (Configuración) > **General** > **Profiles and Device Management** (Perfiles y administración de dispositivos).

    b. Confíe en el certificado **Bit Stadium GmbH**.

Si no asistió a Build 2016, puede compilar e implementar la aplicación usted mismo:

1.   Descargue el código [desde GitHub].

2.   Realice la compilación e implementación [con Xamarin].

Encuentre más información en la [guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), en inglés.

## Obtención de un adaptador OBD (opcional)

Esta es la parte que lo convierte en un verdadero sistema de Internet de las cosas. Puede utilizar la aplicación sin uno de ellos, pero es más divertido usar uno auténtico y no son costosos.

El diagnóstico a bordo (OBD) es la característica de su automóvil que usa el taller para ajustar y diagnosticar ruidos extraños y luces de advertencia. A menos que su automóvil tenga muchos años, encontrará un socket en alguna parte del habitáculo, normalmente detrás de un saliente debajo del salpicadero. Con el conector adecuado, puede obtener métricas de rendimiento del motor y realizar algunos ajustes. Un conector de OBD se puede adquirir de manera económica en las ubicaciones habituales. Se conecta mediante Wi-Fi o Bluetooth a una aplicación en su teléfono.

Pero, en este caso, vamos a conectar su automóvil a la nube. La conexión directa desde el OBD es a su teléfono; pero nuestra aplicación funciona como una retransmisión. La telemetría de su automóvil se envía directamente en el Centro de IoT de MyDriving, donde se procesa para registrar sus viajes y evaluar su estilo de conducción.

Para conectar un dispositivo de OBD:

1.  Compruebe que su automóvil tenga un socket OBD.

2.  Obtenga un adaptador OBD.

    -   Si está usando un teléfono Android o Windows, necesita un adaptador OBD II compatible con Bluetooth. Usamos la [herramienta de análisis BAFX Products 34t5 Bluetooth OBDII].

    -   Si está usando un teléfono iOS, necesitará un adaptador OBD habilitado para Wi-Fi. Usamos el [detector de diagnóstico y adaptador OBD ScanTool OBDLink MX Wi-Fi].

3.  Siga las instrucciones incluidas en el adaptador OBD para conectarse a su teléfono. Tenga en cuenta lo siguiente:

    -   El adaptador Bluetooth debe estar emparejado con el teléfono, en la página de **configuración**.

    -   El adaptador Wi-Fi debe tener una dirección en el intervalo de 192.168.xxx.xxx.

4.  Si tiene varios automóviles, puede conseguir un adaptador independiente para cada uno de ellos (un máximo de tres).

Si no tiene un adaptador OBD, la aplicación todavía enviará datos de velocidad y la ubicación desde el receptor GPS del teléfono al back-end y le preguntará si desea simular un OBD.

Puede encontrar más información acerca de cómo la aplicación utiliza los datos desde el adaptador OBD y sobre las opciones para crear su propio dispositivo OBD en la sección 2.1 "IoT Devices" (Dispositivos IoT) de la [guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), en inglés.

## Uso de la aplicación

Inicie la aplicación. Hay un inicio rápido introductorio que le guiará a través de su funcionamiento.

### Realice el seguimiento de sus viajes.

Pulse el botón Grabar (el gran círculo rojo en la parte inferior de la pantalla) para iniciar un viaje y vuelva a pulsarlo para finalizar.

![Ilustración del botón Grabar para el seguimiento de viajes](./media/iot-solution-get-started/image2.png)

Cada vez que inicie un viaje y, si no hay ningún dispositivo OBD, se le preguntará si desea usar el simulador.

Al final de un viaje, pulse el botón Detener y obtendrá un resumen.

![Ejemplo de un resumen de viaje](./media/iot-solution-get-started/image3.png)

### Revise sus viajes.

![Ejemplo de un viaje anterior](./media/iot-solution-get-started/image4.png)

### Revise su perfil.

![Ejemplo de un perfil de conducción](./media/iot-solution-get-started/image5.png)

## Envíenos sus comentarios de prueba.

Como ya hemos creado MyDriving para ayudar a impulsar sus propios sistemas de IoT, queremos conocer su opinión acerca de cómo funciona. Queremos saber si:

- Se enfrenta a dificultades y desafíos.

- Hay un punto de extensión que puede resultarle más adecuado para su escenario.

- Busca una forma más eficaz de realizar ciertas necesidades.

- Tiene cualquier sugerencia para mejorar MyDriving o esta documentación.

Dentro de la misma aplicación MyDriving, puede usar el mecanismo de comentarios integrado de HockeyApp: en iOS y Android solo tiene que agitar el teléfono o utilizar el comando de menú de **comentarios**. Esta acción asociará automáticamente una captura de pantalla, por lo que ya sabremos de que está hablando. Y si se produjeran algunos bloqueos desafortunados, HockeyApp recopilará los registros de bloqueo para informarnos sobre ellos. También puede proporcionar comentarios a través del [portal de HockeyApp].

Asimismo puede registrar un [problema en GitHub] o dejar un comentario a continuación (edición en inglés).

Esperamos sus noticias.

## Pasos siguientes

-   Explore la [guía de referencia de MyDriving](http://aka.ms/mydrivingdocs) (en inglés) para entender cómo hemos diseñado y creado todo el sistema MyDriving.

-   [Cree e implemente un sistema por su cuenta](iot-solution-build-system.md) con nuestros scripts de Azure Resource Manager. La [guía de referencia de MyDriving](http://aka.ms/mydrivingdocs) también le guía por aquellas áreas en las que realizará la mayoría de las personalizaciones.

  [desde GitHub]: https://github.com/Azure-Samples/MyDriving
  [con Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [herramienta de análisis BAFX Products 34t5 Bluetooth OBDII]: http://www.amazon.com/gp/product/B005NLQAHS
  [detector de diagnóstico y adaptador OBD ScanTool OBDLink MX Wi-Fi]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [portal de HockeyApp]: https://rink.hockeyapp.org
  [problema en GitHub]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0420_2016-->