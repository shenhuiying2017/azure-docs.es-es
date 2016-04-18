<properties 
	pageTitle="Ejemplo de IoT de Azure MyDriving - Compilación | Microsoft Azure" 
	description="Compile una aplicación que es una demostración completa de cómo diseñar un sistema IoT con Microsoft Azure, incluido el análisis de transmisiones, el aprendizaje automático y los centros de eventos." 
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


# Compilación e implementación de la solución MyDriving en su entorno

MyDriving es una solución de Internet de las cosas (IoT) que recopila datos de su automóvil, los procesa mediante el aprendizaje automático y los presenta en el teléfono móvil. El back-end consta de una serie de servicios proporcionados por Microsoft Azure y los clientes pueden ser teléfonos Android, iOS o Windows 10.

La solución MyDriving se ha creado para proporcionar un punto de partida para su propio sistema de IoT. En el repositorio puede obtener scripts de Azure Resource Manager para implementar la arquitectura de back-end en su cuenta de Azure, tras lo cual puede reconfigurar los diferentes servicios, modificar las consultas para adaptarlas a sus propios datos, etc. En <https://github.com/Azure-Samples/MyDriving> encontrará, aparte de estos scripts, el código para la aplicación móvil o el proyecto de API de Servicio de aplicaciones de Azure.

Si todavía no ha probado la aplicación, eche un vistazo a la [Guía de introducción](iot-solution-get-started.md).

## ¿Qué es necesario crear?

En la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs) encontrará una descripción detallada de la arquitectura. En resumen, hay varias piezas que hemos instalado y que habrá que instalar para crear cualquier proyecto similar:

* **Aplicación cliente**: funciona en teléfonos Android, iOS y Windows 10. Se usa la plataforma de Xamarin porque permite compartir gran parte del código, el cual se almacena en GitHub en `src/MobileApp`. La aplicación realiza dos funciones distintas:
 * **Retransmisión de telemetría** desde el dispositivo OBD y desde su propio servicio de ubicación al back-end en la nube del sistema.
 * **Interfaz de usuario** que permite a los usuarios consultar sus desplazamientos registrados.
* **Servicio en la nube**: recopila los datos del viaje en tiempo real y los procesa. La labor principal al crear este servicio consiste en elegir, parametrizar y conectar una serie de servicios de Azure. Algunas de las partes requieren el uso de scripts para filtrar y procesar los datos entrantes. Para configurar todas las partes, usamos una plantilla **Azure Resource Management (ARM)**.
* **Aplicación de servicio móvil**. Se trata del servicio web que se encuentra tras la interfaz de usuario de la aplicación del dispositivo. Su función principal consiste en consultar la base de datos de los datos almacenados procesados. Su código se encuentra en GitHub en `src/MobileAppService`.
* **Visual Studio con Xamarin** es nuestro entorno de desarrollo. Xamarin está disponible como componente de Visual Studio y como IDE independiente, y se usa para compilar código para dispositivos de varias plataformas. Para poder compilar código de iOS, es necesario ejecutar una instancia de Xamarin en una máquina OSX, aunque también puede ejecutarse como agente administrado desde Visual Studio.
* **Pruebas unitarias**: estas pruebas de las aplicaciones de dispositivo se realizan en Xamarin Test Cloud.
* **GitHub**: es el repositorio donde se almacena todo el código, los scripts y las plantillas.
* **Visual Studio Team Services**: servicio en la nube que se usa para administrar la compilación continua y las pruebas del servicio web y las aplicaciones de dispositivo.
* **HockeyApp**: se usa para distribuir las versiones del código de dispositivo. Además de administrar la distribución, recopila informes de errores y de uso y comentarios de usuario.
* **Application Insights**: supervisa el servicio web móvil.


Ahora veamos cómo se configura todo esto. Tenga en cuenta que muchos de los pasos son opcionales.

## Registro de cuentas

-   [**Visual Studio Dev Essentials**](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx): este programa gratuito proporciona fácil acceso a muchos servicios y herramientas para desarrolladores, como Visual Studio, Visual Studio Team Services y Azure, y le proporciona un crédito mensual de 25 dólares al mes en Azure durante doce meses. También incluye suscripciones de Xamarin University y aprendizaje de Pluralsight. Puede también registrarse por separado en los niveles gratuitos de [Azure](https://azure.com) y [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), pero en este caso no se proporcionan créditos de Azure.

-   [**HockeyApp**](https://rink.hockeyapp.net/): (opcional) para administrar la distribución de las pruebas de aplicaciones móviles y recopilar datos de telemetría.

-   [**Xamarin**](https://xamarin.com/): (obligatorio) para compilar la aplicación móvil y realizar depuración y pruebas en [Xamarin Test Cloud](https://xamarin.com/test-cloud).

-   [**GitHub**](https://github.com/Azure-Samples/MyDriving/) (opcional): para crear repositorios públicos gratuitos para su propio código (los repositorios privados son de pago). Si lo prefiere, puede usar el plan básico de Visual Studio Team Services para repositorios privados.

-   [**Power BI**](https://powerbi.microsoft.com/) (opcional): para crear visualizaciones enriquecidas de datos en todo el sistema.

> [AZURE.NOTE] No es necesario tener una cuenta de GitHub para acceder al código de MyDriving en [https://github.com/Azure-Samples/MyDriving](https://github.com/Azure-Samples/MyDriving).

## Instalación de herramientas de desarrollo

La siguiente configuración está diseñada para desarrollar la solución en su totalidad: una aplicación multiplataforma para iOS, Android y Windows Phone 10, con back-end de Azure.

Si no trabaja con el back-end de Azure, también puede desarrollar las aplicaciones móviles con Xamarin Studio en Windows o Mac.

[Aquí](https://msdn.microsoft.com/library/mt613162.aspx) puede consultar una descripción más detallada.

### Máquina de desarrollo Windows

En Windows, Visual Studio es la herramienta central para trabajar con la aplicación MyDriving para Android y Windows, el proyecto de la API del Servicio de aplicaciones y las extensiones de microservicio.

Visual Studio incluye Xamarin, Git, emuladores y otros componentes útiles.

Instalación:

-   [**Visual Studio 2015 con Xamarin**](https://www.visualstudio.com/products/visual-studio-community-vs) (cualquier edición – Community es gratuita)

-   [**SQLite para Plataforma universal de Windows**](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936): necesario para compilar el código de Windows Phone 10.

-   [Azure SDK para VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409): proporciona el SDK para ejecutar aplicaciones en Azure y herramientas de línea de comandos para la administración de Azure.

-   [SDK Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric): Necesario para compilar la extensión [microservicio](../service-fabric/service-fabric-get-started.md).

Extensiones de Visual Studio: compruebe que en Herramientas aparece Android, iOS, Xamarin... De lo contrario, abra el **Panel de Control**, **Programas y características**, **Microsoft** **Visual Studio 2015**, **Modificar**. En **Desarrollo multiplataforma**, elija **C#/.Net (Xamarin).** Compruebe también que **GitHub** está instalado.

### Máquina de desarrollo Mac

Para el desarrollo de iOS, es necesario un equipo Mac (Yosemite o posterior). Aunque en Windows usamos Visual Studio con Xamarin para desarrollar y administrar todo el código, Xamarin usa un agente instalado en un equipo Mac con el fin de compilar y firmar el código de iOS.

![](./media/iot-solution-build-system/image1.png)

(Otra opción es usar Xamarin Studio directamente en el equipo Mac para desarrollar aplicaciones multiplataforma).

El Mac no es necesario si no se desea incluir iOS como plataforma de destino.

Instalación:

-   [**Xamarin Studio** para iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). También puede configurar Visual Studio y Xamarin en un equipo Mac que ejecute una máquina virtual de Windows. Consulte [Configuración, instalación y comprobaciones para usuarios de Mac](https://msdn.microsoft.com/library/mt488770.aspx) en MSDN.

-   [Herramientas de desarrollo de Azure](https://azure.microsoft.com/downloads/) (opcional).

Habilite el inicio de sesión remoto en el equipo Mac. Abra Preferencias del Sistema, Compartir y active Sesión remota.

Cuando abra un proyecto de iOS en Visual Studio en Windows, el complemento Xamarin le pedirá el identificador del Mac.

## Captura del repositorio de GitHub

Capture una copia local de <https://github.com/Azure-Samples/MyDriving> con el botón **Descargar ZIP** en GitHub, Visual Studio o en otro cliente de Git.

Descomprima el archivo en una carpeta con una ruta de acceso corta, como C:\\code.

Como alternativa, si desea mantenerse al día de nuestro código o contribuir a él, clone el repositorio del modo siguiente:

> git clone <https://github.com/Azure-Samples/MyDriving.git>

## Código de desarrollador de Mapas de Bing

[Regístrese para obtener una clave de API de Mapas de Bing](https://msdn.microsoft.com/library/ff428642.aspx).

Deberá reemplazar esto en la línea 22 en `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`



## Compilación de la aplicación de demostración

Abra estas soluciones en Visual Studio:

-   src\\MobileApps\\MyDriving.sln
-   src\\MobileAppService\\MyDrivingService.sln
-   src\\Extensions\\ServiceFabric\\VINLookUpApplication\\VINLookUpApplication.sln

Se le pedirá lo siguiente:

-   Confiar en algunos proyectos potencialmente no confiables. Elija abrirlos si desea continuar.

-   Establecer el modo de programador si está trabajando en una nueva máquina de Windows 10.

-   Proporcionar sus credenciales de Xamarin.

-   Conectarse al equipo Mac con Xamarin. Si no tiene un Mac, haga clic con el botón derecho en el proyecto iOS en Visual Studio y elija **Descargar el proyecto**.

Recompile la solución.

### ¿Problemas con la compilación?

Estas son algunas de las peculiaridades con las que nos hemos topado:

-   El proyecto VINLookupApplication no se carga: asegúrese de que ha instalado el [Azure SDK para VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   El proyecto de Service Fabric no se compila: compile primero los proyectos de las interfaces y asegúrese de que instaló el SDK de Service Fabric.

-   La aplicación Android no se compila:

    -   Abra Herramientas, Android, Android SDK Manager y asegúrese de que instaló la plataforma Android 6 (API 23)/SDK.

    -   Elimine este directorio y luego recompile:<br/> `%LocalAppData%\Xamarin\zips`

## Conocer el código

En la solución, encontrará:

-   Extensiones de Azure

    -   Service Fabric

-   HDInsight: scripts para procesar datos de ida y vuelta en Azure

-   Aplicaciones móviles: las aplicaciones móviles

-   MobileAppsService/MyDrivingService: el back-end web

-   Power BI: definición de informes de Power BI

-   Scripts

    -   ARM: plantillas para compilar los recursos de Azure

    -   PowerShell: scripts para ejecutar las plantillas de ARM

    -   SQLDatabase: bases de datos de depuración

-   SQLDatabase

    -   CreateTables: definiciones de esquema

-   StreamingAnalytics: consultas que transforman el flujo de datos entrantes

## Ejecutar las aplicaciones en modo de desarrollo

### Back-end

Establezca MyDrivingService como proyecto de inicio y presione F5 para ejecutar el servicio web de back-end. Se abrirá una vista del explorador con la lista de API.

### Clientes móviles

Las [aplicaciones móviles se desarrollan en Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).

-   [Depuración de Android en Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)

### iOS

-   [Depuración en iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)

### Windows Phone

-   [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)


## HockeyApp

HockeyApp administra la distribución de su dispositivo Android, iOS o Windows a los usuarios de prueba y les notifica la publicación de nuevas versiones. También recopila informes de errores, comentarios de usuarios con capturas de pantalla y métricas de uso.

[Empiece cargando](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) la aplicación compilada. A continuación, inicie sesión en HockeyApp (<https://rink.hockeyapp.net>) desde la máquina de desarrollo. En el panel del programador, haga clic en **Nueva aplicación** y arrastre los archivos compilados a la ventana. (Más adelante podrá automatizar el servicio de compilación para que lo haga).

Ahora se encuentra en el panel de la aplicación.

![](./media/iot-solution-build-system/image2.png)

Repita el proceso con cada plataforma en la que se ejecute la aplicación.

- Use el [identificador de la aplicación](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) del panel para enviar datos de errores y comentarios y datos de bloqueo desde su aplicación. En MyDriving, actualice los identificadores en src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs 
-   [**Invite a usuarios de prueba**](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Recibirá una dirección URL que puede usar para contratar usuarios evaluadores. Los usuarios podrán suscribirse a su equipo, descargar la aplicación y enviarle comentarios.

-   Si prefiere una versión beta más abierta, establezca la distribución como pública: haga clic en **Manage App, Distribution, Download = Public.** Ahora cualquier usuario puede descargar la aplicación, enviar comentarios y ver si se publica una nueva versión. Además, también pueden enviarle informes de errores. ![](./media/iot-solution-build-system/image3.png)

-   [**Vincule informes de errores a Visual Studio Team Services**](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs): haga clic en **Manage App, Visual Studio Team Services**. HockeyApp puede crear automáticamente elementos de trabajo en Team Services cuando se generan informes de errores o cuando se reciben comentarios.

> Más información en <https://hockeyapp.net>.

## Xamarin Test Cloud

[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatiza las pruebas de interfaz de usuario en dispositivos reales en la nube. Use el entorno NUnit para escribir pruebas que ejecutan su aplicación a través de la interfaz de usuario.

Para usar Xamarin, incorpore el SDK [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) a su aplicación. Este SDK viene incluido como paquete en NuGet. Lo encontrará en la aplicación de demostración y se incluye al crear nuevos proyectos de prueba con las plantillas de Xamarin.

![](./media/iot-solution-build-system/image4.png)

La aplicación incluye un proyecto de prueba de ejemplo en el repositorio: en [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService), mire en [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/

Si usa la compilación de Visual Studio Team Services, le resultará fácil escribir pruebas unitarias de interfaz de usuario de Xamarin y ejecutarlas como parte de la compilación.

## Implementar servicios de Azure

Consulte las instrucciones detalladas de **scripts/README.md** para realizar una implementación automática de servicios de Azure y servicios de Team Services Build.

### ¿Qué hace el script de implementación?

Microsoft Azure proporciona una gran cantidad de servicios diferentes que se pueden usar para crear aplicaciones de nube. Aunque muchos se pueden usar individualmente (por ejemplo, Servicio de aplicaciones/Aplicaciones web), lo mejor es interconectarlos para formar un sistema integrado como el que usamos en MyDriving.

Es posible crear e interconectar manualmente los servicios de Azure, pero es mucho más rápido y confiable usar plantillas de Azure Resource Manager (ARM). [ARM](../resource-group-overview.md) automatiza la implementación de los recursos de una solución y la creación de interconexiones entre ellos.

Encontrará la plantilla para el sistema de MyDriving en el repositorio de GitHub en [Scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Esta plantilla ofrece una vista muy completa y concisa de cómo están interconectados los distintos servicios de nuestra arquitectura. Todo esto se explica en detalle en la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), pero, simplemente leyendo la plantilla, se puede aprender muchísimo.

> [AZURE.NOTE] La mayoría de los servicios de Azure conllevan un costo según el plan de tarifa. Si no está familiarizado con Azure, puede [probarlo gratuitamente](https://azure.microsoft.com/free/). Sin embargo, si no tiene pensado usar determinados componentes del sistema MyDriving, asegúrese de quitarlos para evitar incurrir en costos. Consulte *Costos operativos estimados* a continuación para ver un resumen de los gastos de servicio habituales.

#### Edición de la plantilla

Para personalizar la implementación, quitar componentes innecesarios o agregar otros, realice primero un copia de scenario\_complete.params.json y scenario\_complete.json en donde se van a realizar cambios.

El archivo scenario\_complete.params.json permite invalidar varios valores predeterminados como el SKU de servicio o el tipo de replicación de almacenamiento, tal como se describe en la tabla siguiente. Los valores predeterminados seleccionan las opciones de costo más bajo.

| **Parámetro** | **Descripción** | **Valor predeterminado** |
|--------|---------|-------|
| SDK de Centro de IoT | Nivel de servicio de Centro de IoT de Azure | F1 |
| Tipo de cuenta de almacenamiento | Tipo de replicación de almacenamiento | LRS estándar |
| Objetivo del servicio SQL | Consumo de ranuras de simultaneidad | DW100 |
| SKU de plan de hospedaje | Plan del Servicio de aplicaciones | F1 |

En scenario\_complete.json:

-   Busque "baseName" y póngale el nombre que quiera.

-   Busque en "Create" (cada una de estas secciones crea un recurso).

-   Establezca sqlServerAdminLogin y sqlServerAdminPassword en los valores adecuados.

-   Antes de eliminar una sección que crea un recurso, busque su nombre en el resto del archivo para comprobar si tiene elementos dependientes. Observe que cada sección que crea un servicio incluye una sección *dependsOn* en la que se enumeran sus dependencias.

Esto es lo configura la plantilla. En la [Guía de referencia](http://aka.ms/mydrivingdocs) encontrará la información detallada:

| **Servicio** | **Descripción y detalles**  
|---|----
| Cuentas de almacenamiento | La plantilla crea tres cuentas:                                                                                                                                                                       
||- Una base de datos SQL que recibe datos de telemetría agregados de Análisis de transmisiones y sirve como almacén de respaldo para las tablas de Servicio de aplicaciones de Azure que exponen estos datos a través de extremos de API.                      
| | - Almacenamiento de blobs que acumula datos históricos de otro trabajo de Análisis de transmisiones, y que será procesado por HDInsight.                                                                                         
| | - Base de datos SQL que recibe los resultados procesados por HDInsight que se usarán con Power BI.                                                                                                                 
| Centro de IoT | Establece una conexión bidireccional con cada dispositivo conectado. En la solución MyDriving, la aplicación móvil actúa como una puerta de enlace de campo para enviar datos al Centro de IoT. Centro de IoT sirve como entrada a Análisis de transmisiones. |
| Centro de eventos | Una salida para un trabajo de Análisis de transmisiones que pone en cola la salida a extensiones creadas con Azure Service Fabric.                                                                                               
| Almacenamiento de datos SQL |                                                                                                                                                                                                            
| Trabajos de Análisis de transmisiones (ASA) | Conectar entradas y salidas con una consulta que se usa para agregar tanto datos en tiempo real como históricos para las API de Servicios de aplicaciones, Aprendizaje automático, extensiones y Power BI.                               
| Área de trabajo de Aprendizaje automático | Incluye experimentos, código R y servicio de API.                                                                                                                                                              
| Factoría de datos | Reciclaje programado de Aprendizaje automático.                                                                                                                                                                     
| Plan de hospedaje de Service Fabric | Para extensiones.                                                                                                                                                                                            
| Servicio de aplicaciones (“Aplicación móvil”) | Hospeda el proyecto de API de Aplicaciones móviles que proporciona los extremos para la aplicación móvil. El código de la API debe implementarse en el Servicio de aplicaciones desde Visual Studio.                                                         
| Las reglas de alertas | Se envía un correo electrónico si las respuestas de la aplicación indican fallos.                                                                                                                                            
| Application Insights | Para supervisar el rendimiento de las API en el Servicio de aplicaciones. Tendrá que configurar la conexión en Visual Studio.                                                                                          
| Almacén de claves | Para guardar el certificado de clúster del servicio web.                                                                                                                                                                

#### Ejecución de la plantilla

En **scripts/README.md encontrará instrucciones detalladas**.

Para aprovisionar todos estos servicios en su propia cuenta de Azure mediante el script, realice una de las siguientes acciones:

-   Con PowerShell:

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *location* es la [ubicación de Azure](https://azure.microsoft.com/regions/) como 'Europa del Norte' u 'Oeste de EE. UU.'. Use `Get-AzureLocation` para obtener una lista de las ubicaciones disponibles.

 -   *resourceGroupName* es el nombre que desea asignar al grupo al que pertenecerán todos los recursos. Cuando haya acabado de trabajar con los recursos, puede eliminar este grupo para eliminarlos todos juntos.

-   Ejecute DeploymentScripts/Bash/deploy.sh con Bash.

-   Abra y compile la solución de Visual Studio DeploymentScripts/VS/DeployARM.sln.

Tenga en cuenta que cada vez que se ejecuta la plantilla, se crea un nuevo conjunto de recursos con nombres nuevos. Para eliminar los recursos, vaya al portal y elimine el grupo de recursos.

Si el script falla por alguna razón, puede volver a ejecutarlo.

El script le da la opción de configurar la integración continua en Visual Studio Team Services. Si ha configurado un proyecto Team Services, tendrá una dirección URL https://yourAccountName.visualstudio.com. Escriba la dirección URL completa cuando se le pida. Puede darle un nombre nuevo o uno existente para un proyecto Team Services.



## Visual Studio Team Services

En este proyecto usamos Team Services principalmente por las características de compilación y prueba que ofrece. Además, proporciona una excelente compatibilidad de colaboración, como la administración de tareas con los tableros Kanban, revisión de código integrada con tareas y control de código fuente, compilaciones con protección controlada, etc. Se integra bien con otras herramientas como GitHub, Xamarin, HockeyApp y, por supuesto, Visual Studio. El acceso se realiza a través de la interfaz web o de Visual Studio, lo que sea más cómodo en ese momento.

Para las definiciones de compilación y versión se usan diversos servicios de complemento que están disponibles en el [Marketplace](https://marketplace.visualstudio.com/VSTS) de Team Services. Además de las utilidades básicas para la ejecución de líneas de comandos o la copia de archivos, hay servicios que invocan compilaciones de Xamarin, Android y otros proveedores, y que se interrelacionan con HockeyApp.

![](./media/iot-solution-build-system/image5.png)

### Definiciones de compilación

Tenemos definiciones de compilación para cada uno de los destinos principales, así como variaciones de características y pruebas de regresión. Esto es lo que nos proporciona:

-   MyDriving.Services (la aplicación web de back-end para la aplicación móvil)

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android-Feature

    -   MyDriving.Xamarin.Android-Regression

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS-Feature

    -   MyDriving.Xamarin.iOS-Regression

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP-Feature

    -   MyDriving.Xamarin.UWP-Regression

Si desea ver todos los detalles de la configuración, consulte la sección 4.7 de la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), *Configuración de compilación y versión*. Siguen el mismo modelo general:

1.  Restauración del paquete NuGet. No conservamos el código compilado en el repositorio, por lo que los primeros pasos de cada compilación consisten en restaurar los paquetes de NuGet necesarios.

2.  Activación de licencias. La compilación se lleva a cabo en la nube, por lo que si es necesario un certificado —en concreto, para el servicio de compilación Xamarin—, tendremos que activar la licencia en la máquina de compilación actual. Inmediatamente después lo desactivamos para que pueda usarse en otra máquina.

3.  Compilación con el servicio apropiado. Usamos compilaciones de Xamarin para las aplicaciones móviles y una compilación de Visual Studio para el servicio web de back-end.

4.  Compilación de pruebas.

5.  Ejecución de pruebas. Ejecutamos las pruebas de la aplicación móvil en Xamarin Test Cloud.

6.  Publicación del resultado de la compilación en la ubicación de destino.

El desencadenador para las compilaciones principales se establece en Integración continua, es decir, la compilación se ejecuta cada vez que el código se inserta en el repositorio de la rama principal.

![](./media/iot-solution-build-system/image6.png)

### Definiciones de versión

Las definiciones de versión se configuran de modo muy parecido.

Para el servicio web, establecemos la implementación como una aplicación web de Azure:

![](./media/iot-solution-build-system/image7.png)

Y establezca el desencadenador de versión en implementación continua; es decir, cada inserción en el repositorio seguida de una compilación correcta provoca que la aplicación web se actualice:

![](./media/iot-solution-build-system/image8.png)

Para las aplicaciones móviles, implementamos en HockeyApp:
![](./media/iot-solution-build-system/image9.png)

## Application Insights

[Application Insights](../application-insights/app-insights-overview.md) recopila datos de telemetría sobre el rendimiento y el uso de los servicios web. El SDK de Application Insights envía datos de telemetría desde el servicio al recurso de Application Insights en Azure.

Busque el recurso de Application Insights que se configuró mediante la plantilla. Ahí podrá explorar gráficos del rendimiento de su [proyecto de Servicio de aplicaciones móviles](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Se muestran las solicitudes del servidor, los tiempos de respuesta, los errores y la cantidad de excepciones. También hay gráficos de tiempos de respuesta de dependencias, es decir, las llamadas a la base de datos y a las API de REST, como el Aprendizaje automático. Si hay algún problema de rendimiento, podrá ver qué parte de su sistema lo está provocando.

![Gráfico de rendimiento de ejemplo](./media/iot-solution-build-system/image11.png)

Si tiene un servicio web configurado a mano, también puede obtener los mismos gráficos:

![En la hoja de servicio web, elija Herramientas, Extensiones, Agregar. Seleccione Application Insights.](./media/iot-solution-build-system/image12.png)

La característica funciona mediante la instrumentación de la aplicación con el SDK de Application Insights.

Puede agregar datos de telemetría personalizados (o instrumentar una aplicación que se ejecute fuera de Azure) si [agrega el SDK de Application Insights](../application-insights/app-insights-asp-net.md) en tiempo de desarrollo. Esto es útil para registrar métricas que dependen de la aplicación, como la duración promedio del viaje de los usuarios o el kilometraje total.

![En Visual Studio, haga clic con el botón derecho en el proyecto y seleccione Agregar Application Insights.](./media/iot-solution-build-system/image10.png)

Application Insights enviará mensajes de alerta si observa una cantidad inusual de respuestas con error, pero también es posible configurar alertas personalizadas sobre varias métricas, como los tiempos de respuesta.

Para asegurarse de que el servicio web está siempre activo y en ejecución, puede configurar [pruebas de disponibilidad](../application-insights/app-insights-monitor-web-app-availability.md) que hagan ping en el sitio desde varias ubicaciones en todo el mundo cada quince minutos. De nuevo, recibirá un correo electrónico en el caso de que haya algún problema.



## Costos operativos estimados.

Es muy económico ejecutar una aplicación como esta a pequeña escala. Muchos de los servicios tienen niveles de entrada gratuitos, por lo que el desarrollo y el funcionamiento a pequeña escala cuestan muy poco. Y, por supuesto, las aplicaciones no tienen por qué usar todas las características mostradas en MyDriving.

A continuación se incluye una estimación aproximada de los costos de la configuración de desarrollo en MyDriving.

Asimismo, apuntamos algunas alternativas que *no* usamos. #

Condiciones:

-   Equipo no superior a cinco (+ partes interesadas observadoras).

-   Ejecución durante un mes.

-   100 usuarios con 4 viajes por día.

>[AZURE.NOTE] Si no está familiarizado con Azure, puede probar una [cuenta de prueba gratuita](https://azure.microsoft.com/free/).

| **Servicio/Componente** | **Notas** | **Costo/Mes** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) con [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>Entorno de desarrollo multiplataforma .| Visual Studio Community. <br/>(# [VS Pro](https://www.visualstudio.com/vs-2015-product-editions) para [Xamarin.Forms](https://xamarin.com/forms) necesario para diseño multiplataforma desde una sola base de código). | 0 $ |
| [Centro de IoT de Azure](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Conexión de datos bidireccional con dispositivos. | 8000 mensajes + 0,5 KB/mensaje gratis | 0 $ |
| [Análisis de transmisiones](https://azure.microsoft.com/pricing/details/stream-analytics/) <br/> Procesamiento de datos de flujos de gran volumen. | Cargo de 0,031 $ por unidad de streaming por hora mientras esté habilitada. Usted selecciona el número de unidades de streaming que desee; para escalar verticalmente se necesita un mayor número. | 23 $ |
| [Aprendizaje automático de Azure](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Respuestas adaptables. | 10 $/puesto/mes <br/> + 3 h de experimento * 1 $/hora de experimento <br/> + 3,5 h CPU de API * 2 $/hora de CPU de producción <br/> El tiempo de CPU de la API supone 5 minutos por día de reciclaje, aunque esto aumentaría con más datos de entrada; <br/> + 2 min/día de puntuación para procesar 400 viajes por día. | 20 $ |
| [Servicio de aplicaciones de Azure](https://azure.microsoft.com/pricing/details/app-service/) <br/> Back-end móvil de host. | Nivel B1: aplicaciones web de producción. | 56 $ |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/) <br/> Compilación, pruebas unitarias y administración de versiones. Administración de tareas. | Agentes privados, 5 usuarios.| 0 $ |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Supervisión del rendimiento y el uso de sitios y servicios web.| Nivel Gratis | 0 $ |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Distribución de aplicaciones beta, recopilación de comentarios, datos de uso y errores. | Dos aplicaciones gratuitas para nuevos usuarios.<br/> Después, 30 $/mes. | 0 $ |
| [Xamarin](https://store.xamarin.com/)<br/> Código en una plataforma uniforme para varios dispositivos. | Prueba gratuita. <br/>Después, 25 $/mes.| 0 $ |
| [Instancia de SQL](https://azure.microsoft.com/pricing/details/sql-database/) para el Servicio de aplicaciones de Azure| Nivel básico; modelo de base de datos única. | 5 $ |
| [Service Fabric](../service-fabric/services/service-fabric.md) (opcional) | Ejecutar un clúster local. | 0 $ |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Visualizaciones versátiles e investigación de datos transmitidos y estáticos.| Nivel gratis: 1 GB, 10.000 filas/hora, actualización diaria <br/> # 10 $/usuario/mes para [aumentar los límites de](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), más opciones de conexión y colaboración. | 0 $ |
| [Almacenamiento](https://azure.microsoft.com/pricing/details/storage/) | L (con redundancia local) &lt; 100 G 0,024 $/GB | 3 $ |
| [Factoría de datos](https://azure.microsoft.com/pricing/details/data-factory/) | 0,60 $ por actividad * (8 - 5 FOC)| 2 $ |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) (clúster a petición) <br/> Para reciclaje de formación diario. | 3 nodos A3 a 0,32 $/h para 1 hora diaria X 31 días | 30 $ |
| [Centro de eventos](https://azure.microsoft.com/pricing/details/event-hubs/) | Básico con unidad de procesamiento de 11 $/mes + entrada 0,028 $ | 11 $ |
| Llave OBD || 12 $ |
| **Total**| | **157 $** |

Consulte también:

-   [Resumen de cuotas y límites del servicio de Azure](../azure-subscription-service-limits/#iot-hub-limits)

-   [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/)



## Guía de referencia

Se recomienda la lectura de la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), donde se describe de manera detallada el diseño del sistema y sus componentes.


## Comentarios 

Hemos creado MyDriving para ayudar a impulsar sus propios sistemas de IoT y, por ello, queremos conocer su opinión acerca de cómo funciona. Díganos si experimenta dificultades, si considera que una extensión podría hacerlo más adecuado para su escenario, si encuentra una forma más eficaz de llevar a cabo ciertas tareas o si tiene cualquier sugerencia para mejorar MyDriving o esta documentación.

Si lo desea, puede registrar un [problema en GitHub] o dejar un comentario a continuación (edición en inglés).

Esperamos sus noticias.

<!----HONumber=AcomDC_0406_2016-->