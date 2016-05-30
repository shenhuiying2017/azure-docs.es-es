<properties
	pageTitle="Ejemplo de IoT de Azure MyDriving: compilación | Microsoft Azure"
	description="Compile una aplicación que es una demostración completa de cómo diseñar un sistema IoT con Microsoft Azure, incluido Análisis de transmisiones, Aprendizaje automático y Centros de eventos."
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

MyDriving es una solución de Internet de las cosas (IoT) que recopila datos de su automóvil, los procesa mediante el aprendizaje automático y los presenta en el teléfono móvil. El back-end consta de una variedad de servicios proporcionados por Microsoft Azure. Los clientes pueden ser teléfonos Android, iOS o Windows 10.

La solución MyDriving se ha creado para proporcionar un punto de partida para su propio sistema de IoT. En el [repositorio de MyDriving en GitHub](https://github.com/Azure-Samples/MyDriving) puede obtener los scripts de Azure Resource Manager para implementar la arquitectura de back-end en su propia cuenta de Azure. Desde ese punto, puede volver a configurar los distintos servicios, modificar las consultas para adaptarlas a sus propios datos, etc. En el repositorio de MyDriving encontrará, además de estos scripts, el código para la aplicación móvil o el proyecto de API de Servicio de aplicaciones de Azure, entre otros elementos.

Si todavía no ha probado la aplicación, eche un vistazo a la [guía de introducción](iot-solution-get-started.md).

En la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs) encontrará una descripción detallada de la arquitectura. En resumen, hay varios componentes que hemos configurado y que habrá que configurar para crear cualquier proyecto similar:

* Una **aplicación cliente** funciona en teléfonos Android, iOS y Windows 10. Se usa la plataforma de Xamarin porque permite compartir gran parte del código, el cual se almacena en GitHub en `src/MobileApp`. La aplicación realiza dos funciones distintas:
 * Retransmite telemetría desde el dispositivo de diagnóstico a bordo (DAB) y desde su propio servicio de ubicación al back-end en la nube del sistema.
 * Es una interfaz de usuario en la que los usuarios pueden consultar sus desplazamientos registrados.
* Un **servicio en la nube** recopila los datos del viaje en tiempo real y los procesa. La labor principal al crear este servicio consiste en elegir, parametrizar y conectar una serie de servicios de Azure. Algunas de las partes requieren el uso de scripts para filtrar y procesar los datos entrantes. Para configurar todas las partes, usamos una plantilla de Azure Resource Manager.
* Una **aplicación de servicio móvil** es el servicio web que se encuentra tras la interfaz de usuario de la aplicación del dispositivo. Su función principal consiste en consultar la base de datos de los datos almacenados procesados. Su código se encuentra en GitHub en `src/MobileAppService`.
* **Visual Studio con Xamarin** es nuestro entorno de desarrollo. Xamarin, que está disponible como componente de Visual Studio y como entorno de desarrollo integrado (IDE) independiente, se usa para compilar código para dispositivos de varias plataformas. Para compilar el código de iOS, es necesario disponer de una instancia de Xamarin que se ejecute en una máquina de OS X. Si es necesario, se puede ejecutar como un agente administrado desde Visual Studio.
* Las **pruebas unitarias** de las aplicaciones de dispositivo se realizan en Xamarin Test Cloud.
* **GitHub** es el repositorio donde se almacena todo el código, los scripts y las plantillas.
* **Visual Studio Team Services** es un servicio en la nube que se usa para administrar la compilación continua y las pruebas del servicio web y las aplicaciones de dispositivo.
* **HockeyApp** se usa para distribuir las versiones del código de dispositivo. También recopila comentarios de usuario y los informes de uso y bloqueo.
* **Visual Studio Application Insights** supervisa el servicio web móvil.

Ahora veamos cómo se configura todo esto. Tenga en cuenta que muchos de los pasos son opcionales.

## Registro de cuentas

-   [Visual Studio Dev Essentials](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Este programa gratuito proporciona fácil acceso a muchos servicios y herramientas para desarrolladores, como Visual Studio, Visual Studio Team Services y Azure. Proporciona un crédito mensual de 25 dólares al mes en Azure durante 12 meses. También incluye suscripciones de Xamarin University y aprendizaje de Pluralsight. También puede registrarse por separado en los niveles gratuitos de [Azure](https://azure.com) y [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), pero en este caso no se proporcionan créditos de Azure.

-   [HockeyApp](https://rink.hockeyapp.net/): (opcional) para administrar la distribución de las pruebas de aplicaciones móviles y recopilar datos de telemetría.

-   [Xamarin](https://xamarin.com/): (obligatorio) para compilar la aplicación móvil y realizar depuración y pruebas en [Xamarin Test Cloud](https://xamarin.com/test-cloud).

-   [GitHub](https://github.com/Azure-Samples/MyDriving/): (opcional) para crear repositorios públicos gratuitos para su propio código (los repositorios privados son de pago). Si lo prefiere, puede usar el plan básico de Visual Studio Team Services para repositorios privados.

-   [Power BI](https://powerbi.microsoft.com/): (opcional) para crear exhaustivas visualizaciones de datos en todo el sistema.

> [AZURE.NOTE] No es necesario tener una cuenta de GitHub para acceder al código de MyDriving en [el repositorio de MyDriving en GitHub](https://github.com/Azure-Samples/MyDriving).

## Instalación de herramientas de desarrollo

La siguiente configuración está diseñada para desarrollar la solución en su totalidad: una aplicación multiplataforma para iOS, Android y Windows 10 Mobile, con back-end de Azure.

Si no trabaja con el back-end de Azure, también puede desarrollar las aplicaciones móviles con Xamarin Studio en Windows o Mac.

[Aquí](https://msdn.microsoft.com/library/mt613162.aspx) puede consultar una descripción más detallada.

### Máquina de desarrollo Windows

En Windows, Visual Studio es la herramienta central para trabajar con la aplicación MyDriving para Android y Windows, el proyecto de la API del Servicio de aplicaciones y las extensiones de microservicio.

Visual Studio incluye Xamarin, Git, emuladores y otros componentes útiles.

Instalación:

-   [Visual Studio 2015 con Xamarin](https://www.visualstudio.com/products/visual-studio-community-vs) (cualquier edición – Community es gratuita)

-   [SQLite para Plataforma universal de Windows](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Necesario para compilar el código de Windows 10 Mobile.

-   [SDK de Azure para Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409). Proporciona el SDK para ejecutar aplicaciones en Azure y herramientas de línea de comandos para la administración de Azure.

-   [SDK de Azure Service Fabric](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Necesario para compilar la extensión de [microservicio](../service-fabric/service-fabric-get-started.md).

Además, asegúrese de que tiene el derecho de extensiones de Visual Studio. Compruebe que en **Herramientas**, aparece **Android, iOS, Xamarin...**. De lo contrario, abra el Panel de Control, seleccione **Programas y características** > **Microsoft** **Visual Studio 2015** > **Modificar**. En **Desarrollo multiplataforma**, elija **C#/.Net (Xamarin)**. Compruebe también que **GitHub** está instalado.

### Máquina de desarrollo Mac

Para el desarrollo de iOS, es necesario un equipo Mac (Yosemite o posterior). Aunque en Windows usamos Visual Studio con Xamarin para desarrollar y administrar todo el código, Xamarin usa un agente instalado en un equipo Mac con el fin de compilar y firmar el código de iOS.

![Desarrollo en Windows y compilación en Mac](./media/iot-solution-build-system/image1.png)

(Otra opción es usar Xamarin Studio directamente en el equipo Mac para desarrollar aplicaciones multiplataforma).

El Mac no es necesario si no se desea incluir iOS como plataforma de destino.

Instalación:

-   [Xamarin Studio para iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). También puede configurar Visual Studio y Xamarin en un equipo Mac que ejecute una máquina virtual de Windows. Consulte [Configuración, instalación y comprobaciones para usuarios de Mac](https://msdn.microsoft.com/library/mt488770.aspx) en MSDN.

-   [Herramientas de desarrollo de Azure](https://azure.microsoft.com/downloads/) (opcional).

Habilite el inicio de sesión remoto en el equipo Mac. Abra **Preferencias del sistema**, **Compartir** y seleccione **Inicio de sesión remoto**.

Cuando abra un proyecto de iOS en Visual Studio en Windows, el complemento Xamarin le pedirá el identificador del Mac.

## Recuperación del repositorio de GitHub

Recupere una copia local del [repositorio de MyDriving en GitHub](https://github.com/Azure-Samples/MyDriving) mediante el botón **Download ZIP** (Descargar ZIP) en GitHub, Visual Studio u otro cliente de Git.

Descomprima el archivo en una carpeta con una ruta de acceso corta, como C:\\code.

Como alternativa, si desea mantenerse al día de nuestro código o contribuir a él, clone el repositorio del modo siguiente:

****git clone https://github.com/Azure-Samples/MyDriving.git**

## Obtención de la clave de API de mapas de Bing

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

Si tiene problemas al compilar, pruebe las soluciones para las diferentes peculiaridades que hemos encontrado:

-   *El proyecto VINLookupApplication no se carga*: asegúrese de que ha instalado el [Azure SDK para Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   *El proyecto de Service Fabric no se compila*: compile primero los proyectos de las interfaces y asegúrese de que instaló el SDK de Service Fabric.

-   *La aplicación Android no se compila*:

    -   Abra **Herramientas** > ** Android** > **Android SDK Manager** (Administrador de SKD de Android) y asegúrese de que instaló la plataforma Android 6 (API 23)/SDK.

    -   Elimine este directorio y luego recompile:<br/> `%LocalAppData%\Xamarin\zips`

## Conocer el código

En la solución, encontrará:

-   Extensiones de Azure: Service Fabric.

-   HDInsight de Azure: scripts para procesar los datos de los traslados en Azure.

-   Aplicaciones móviles: las aplicaciones móviles.

-   MobileAppsService/MyDrivingService: el back-end web.

-   Power BI: la definición de informe.

-   Scripts:

    -   Resource Manager: plantillas para compilar los recursos de Azure.

    -   PowerShell: scripts para ejecutar las plantillas de Resource Manager.

    -   Base de datos SQL de Azure: depuración de bases de datos.

-   Base de datos SQL: CreateTables: definiciones de esquema.

-   Análisis de transmisiones de Azure: consultas que transforman el flujo de datos entrantes.

## Ejecutar las aplicaciones en modo de desarrollo

Tome medidas para ejecutar las aplicaciones, según el dispositivo que esté usando:

-  Back-end: establezca MyDrivingService como proyecto de inicio y presione F5 para ejecutar el servicio web de back-end. Se abrirá una vista del explorador con la lista de API.

-  Clientes móviles: las [aplicaciones móviles se desarrollan en Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).
 -  Android: para obtener más información, consulte [Debugging Android in Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/) (Depuración de Android en Xamarin).

 -  iOS: para obtener más información, consulte [Debugging in iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/) (Depuración en iOS).

 -  Windows Phone: para obtener más información, consulte[Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/).

## Carga de la aplicación móvil en HockeyApp

HockeyApp administra la distribución de su aplicación Android, iOS o Windows a los usuarios de prueba y les notifica la publicación de nuevas versiones. También recopila útiles informes de errores, comentarios de usuarios con capturas de pantalla y métricas de uso.

[Empiece cargando](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) la aplicación compilada. A continuación, inicie sesión en [HockeyApp](https://rink.hockeyapp.net) desde la máquina de desarrollo. En el panel del desarrollador, haga clic en **New App** (Nueva aplicación) y arrastre los archivos compilados a la ventana. (Más adelante podrá automatizar el servicio de compilación para que lo haga).

Ahora se encuentra en el panel de la aplicación.

![Pestaña de información general en el panel de la aplicación](./media/iot-solution-build-system/image2.png)

Repita el proceso con cada plataforma en la que se ejecute la aplicación. A continuación, puede hacer lo siguiente:

-  Use el [identificador de la aplicación](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) del panel para enviar datos de errores y comentarios y datos de bloqueo desde su aplicación. En MyDriving, actualice los identificadores en src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs.

-  [Invite a usuarios de prueba](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Recibirá una dirección URL que puede usar para contratar usuarios evaluadores. Los usuarios podrán suscribirse a su equipo, descargar la aplicación y enviarle comentarios.

-  Si prefiere una versión beta más abierta, establezca la distribución en modo público. Haga clic en **Manage App** (Administrar aplicación) > **Distribution** (Distribución) > **Download = Public** (Descargar = público). Ahora cualquier usuario puede descargar la aplicación, enviar comentarios y ver si se publica una nueva versión. También pueden enviarle informes de errores.

    ![Equipos en el panel](./media/iot-solution-build-system/image3.png)

-  [Vincular informes de errores a Visual Studio Team Services](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). Haga clic en **Manage App** (Administrar aplicación) > **Visual Studio Team Services**. HockeyApp puede crear automáticamente elementos de trabajo en Team Services cuando se generan informes de errores o cuando se reciben comentarios.

Obtenga más información en el [sitio de HockeyApp](https://hockeyapp.net).

## Prueba de la aplicación móvil en Xamarin Test Cloud

[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatiza las pruebas de interfaz de usuario en dispositivos reales en la nube. Use el entorno NUnit para escribir pruebas que ejecutan su aplicación a través de la interfaz de usuario.

Para usar Xamarin, incorpore el SDK [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) a su aplicación. Este SDK viene incluido como paquete de NuGet. Lo encontrará en la aplicación de demostración y se incluye al crear nuevos proyectos de prueba con las plantillas de Xamarin.

![Dónde encontrar el SDK multiplataforma en la interfaz](./media/iot-solution-build-system/image4.png)

Se incluye un proyecto de prueba de ejemplo con la aplicación en el repositorio. En [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService), mire en [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/.

Si usa la compilación de Visual Studio Team Services, le resultará fácil escribir pruebas unitarias de interfaz de usuario de Xamarin y ejecutarlas como parte de la compilación.

## Implementar servicios de Azure

Consulte las instrucciones detalladas de **scripts/README.md** para realizar una implementación automática de servicios de Azure y servicios de compilación de Team Services.

Microsoft Azure proporciona una gran cantidad de servicios diferentes que se pueden usar para crear aplicaciones de nube. Aunque muchos se pueden usar individualmente (por ejemplo, Servicio de aplicaciones/Aplicaciones web), lo mejor es interconectarlos para formar un sistema integrado como el que usamos en MyDriving.

Es posible crear e interconectar manualmente los servicios de Azure, pero es mucho más rápido y confiable usar plantillas de Azure Resource Manager. [Resource Manager](../resource-group-overview.md) automatiza la implementación de los recursos de una solución y la creación de interconexiones entre ellos.

Encontrará la plantilla para el sistema de MyDriving en el repositorio de GitHub en [Scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Esta plantilla ofrece una vista completa y concisa de cómo están interconectados los distintos servicios de nuestra arquitectura. Todo esto se explica en detalle en la [guía de referencia de MyDriving](http://aka.ms/mydrivingdocs); sin embargo, se puede aprender muchísimo simplemente leyendo la plantilla.

> [AZURE.NOTE] La mayoría de los servicios de Azure conllevan un costo según el plan de tarifa. Si no está familiarizado con Azure, puede [probarlo gratuitamente](https://azure.microsoft.com/free/). No obstante, si no tiene pensado usar determinados componentes del sistema MyDriving, asegúrese de quitarlos para evitar incurrir en costos. La sección "Estimación de costos operativos" que se presenta más adelante en este artículo proporciona un resumen de los gastos de servicio habituales.

### Edición de la plantilla

Para personalizar la implementación, quitar componentes innecesarios o agregar otros, realice primero un copia de scenario\_complete.params.json y scenario\_complete.json en donde se van a realizar cambios.

Puede utilizar el archivo scenario\_complete.params.json para invalidar varios valores predeterminados. como el SKU de servicio o el tipo de replicación de almacenamiento, tal como se describe en la tabla siguiente. Los valores predeterminados seleccionan las opciones de costo más bajo.

| **Parámetro** | **Descripción** | **Valor predeterminado** |
|--------|---------|-------|
| SDK de Centro de IoT | Nivel de servicio de Centro de IoT de Azure | F1 |
| Tipo de cuenta de almacenamiento | Tipo de replicación de almacenamiento | LRS estándar |
| Objetivo del servicio SQL | Consumo de ranuras de simultaneidad | DW100 |
| SKU de plan de hospedaje | Plan del Servicio de aplicaciones | F1 |

En scenario\_complete.json:

-   Busque "baseName" y póngale el nombre que prefiera.

-   Busque "Create". Cada una de estas secciones crea un recurso.

-   Establezca sqlServerAdminLogin y sqlServerAdminPassword en los valores adecuados.

-   Antes de eliminar una sección que crea un recurso, busque su nombre en el resto del archivo para comprobar si tiene elementos dependientes. Observe que cada sección que crea un servicio incluye una sección *dependsOn* en la que se enumeran sus dependencias.

Esto es lo que configura la plantilla. Puede encontrar más detalles en la [guía de referencia](http://aka.ms/mydrivingdocs).

| **Servicio** | **Descripción y detalles**  
|---|----
| Cuentas de almacenamiento | La plantilla crea tres cuentas:                                                                                                                                                                       
|| - Una base de datos SQL que recibe datos de telemetría agregados de Análisis de transmisiones y sirve como almacén de respaldo para las tablas de Servicio de aplicaciones de Azure que exponen estos datos a través de extremos de API.                      
|| - Almacenamiento de blobs que acumula datos históricos de otro trabajo de Análisis de transmisiones, y que será procesado por HDInsight.                                                                                         
|| - Base de datos SQL que recibe los resultados procesados por HDInsight que se usarán con Power BI.                                                                                                                 
| Centro de IoT de Azure | Establece una conexión bidireccional con cada dispositivo conectado. En la solución MyDriving, la aplicación móvil actúa como una puerta de enlace de campo para enviar datos al Centro de IoT de Azure. El Centro de IoT de Azure sirve como entrada a Análisis de transmisiones. |
| Centros de eventos de Azure | Una salida para un trabajo de Análisis de transmisiones que pone en cola la salida a extensiones creadas con Azure Service Fabric.                                                                                               
| Almacenamiento de datos SQL de Azure |                                                                                                                                                                                                            
| Trabajos de Análisis de transmisiones | Conectar entradas y salidas con una consulta que se usa para agregar datos tanto en tiempo real como históricos para las API de Servicio de aplicaciones, Aprendizaje automático de Azure, extensiones y Power BI.                               
| Área de trabajo de Aprendizaje automático | Incluye experimentos, código R y servicio de API.                                                                                                                                                              
| Factoría de datos de Azure | Reciclaje programado de Aprendizaje automático.                                                                                                                                                                     
| Plan de hospedaje de Service Fabric | Para extensiones.                                                                                                                                                                                            
| Servicio de aplicaciones (“Aplicación móvil”) | Hospeda el proyecto de API de Aplicaciones móviles que proporciona los extremos para la aplicación móvil. El código de la API debe implementarse en Servicio de aplicaciones desde Visual Studio.                                                         
| Las reglas de alertas | Envía un correo electrónico si las respuestas de la aplicación indican errores.                                                                                                                                            
| Application Insights | Para supervisar el rendimiento de las API en el Servicio de aplicaciones. Tendrá que configurar la conexión en Visual Studio.                                                                                          
| Almacén de claves de Azure | Para guardar el certificado de clúster del servicio web.                                                                                                                                                                

### Ejecución de la plantilla

En **scripts/README.md** hay instrucciones detalladas para ejecutar la plantilla.

Para aprovisionar todos estos servicios en su propia cuenta de Azure mediante el script, realice una de las siguientes acciones:

-   Use PowerShell.

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *location* es la [ubicación de Azure](https://azure.microsoft.com/regions/), como `North Europe` o `West US`. Use `Get-AzureLocation` para obtener una lista de las ubicaciones disponibles.

 -   *resourceGroupName* es el nombre que desea asignar al grupo al que pertenecerán todos los recursos. Cuando haya acabado de trabajar con los recursos, puede eliminar este grupo para eliminarlos todos juntos.

-   Ejecute DeploymentScripts/Bash/deploy.sh con Bash.

-   Abra y compile la solución de Visual Studio DeploymentScripts/VS/DeployARM.sln.

Tenga en cuenta que cada vez que se ejecuta la plantilla, se crea un nuevo conjunto de recursos con nombres nuevos. Para eliminar los recursos, vaya al portal y elimine el grupo de recursos.

Si el script falla por alguna razón, puede volver a ejecutarlo.

El script le da la opción de configurar la integración continua en Visual Studio Team Services. Si ha configurado un proyecto Team Services, tendrá una dirección URL: https://yourAccountName.visualstudio.com. Escriba la dirección URL completa cuando se le pida. Puede darle un nombre nuevo o uno existente para un proyecto Team Services.

## Configuración de las definiciones de compilación y pruebas en Visual Studio Team Services

En este proyecto usamos Team Services principalmente por las características de compilación y prueba que ofrece. Además, proporciona una excelente compatibilidad de colaboración, como la administración de tareas con los tableros Kanban, la revisión de código integrada con tareas y control de código fuente y las compilaciones controladas. Se integra bien con otras herramientas como GitHub, Xamarin, HockeyApp y, por supuesto, Visual Studio. El acceso se realiza a través de la interfaz web o de Visual Studio, lo que sea más cómodo en ese momento.

Para las definiciones de compilación y versión se usan diversos servicios de complemento que están disponibles en el [Marketplace](https://marketplace.visualstudio.com/VSTS) de Team Services. Además de las utilidades básicas para la ejecución de líneas de comandos o la copia de archivos, hay servicios que invocan compilaciones de Xamarin, Android y otros proveedores, y que conectan con HockeyApp.

![Opciones de compilación en Team Services](./media/iot-solution-build-system/image5.png)

### Definiciones de compilación

Tenemos las definiciones de compilación para cada uno de los objetivos principales. También contamos con variaciones para pruebas de características y regresión. Esto es lo que nos proporciona:

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

Si desea ver todos los detalles de la configuración, consulte la sección 4.7 de la [guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), "Build and Release Configuration" (Configuración de compilación y versión). Siguen el mismo modelo general: El script:

1.  Restaura el paquete NuGet. No conservamos el código compilado en el repositorio, por lo que los primeros pasos de cada compilación consisten en restaurar los paquetes de NuGet necesarios.

2.  Activa la licencia. La compilación se lleva a cabo en la nube, por lo que si es necesario un certificado —en concreto, para el servicio de compilación Xamarin—, tendremos que activar la licencia en la máquina de compilación actual. Inmediatamente después la desactivamos para que pueda usarse en otra máquina.

3.  Compila utilizando el servicio apropiado. Usamos compilaciones de Xamarin para las aplicaciones móviles, y compilaciones de Visual Studio para el servicio web de back-end.

4.  Compila pruebas.

5.  Ejecuta pruebas. Ejecutamos las pruebas de la aplicación móvil en Xamarin Test Cloud.

6.  Publica el resultado de la compilación en la ubicación de destino.

El desencadenador para las compilaciones principales se establece en la integración continua. Es decir, la compilación se ejecuta cada vez que se inserte código en la rama principal.

![Interfaz donde el desencadenador está establecido en integración continua](./media/iot-solution-build-system/image6.png)

### Definiciones de versión

Las definiciones de versión se configuran de modo muy parecido.

Para el servicio web, establecemos la implementación como una aplicación web de Azure:

![Interfaz para configurar la implementación como una aplicación web de Azure](./media/iot-solution-build-system/image7.png)

Y establecemos el desencadenador de versión para la implementación continua. Es decir, cada inserción seguida de una compilación correcta da como resultado una actualización para la aplicación web.

![Interfaz para configurar el desencadenador de versión para la implementación continua](./media/iot-solution-build-system/image8.png)

Para las aplicaciones móviles, implementamos en HockeyApp:

![Interfaz para implementar una aplicación móvil en HockeyApp](./media/iot-solution-build-system/image9.png)

## Exploración de los datos de telemetría mediante Application Insights

[Application Insights](../application-insights/app-insights-overview.md) recopila datos de telemetría sobre el rendimiento y el uso de los servicios web. El SDK de Application Insights envía datos de telemetría desde el servicio al recurso de Application Insights en Azure.

Busque el recurso de Application Insights que se configuró mediante la plantilla. Ahí podrá explorar gráficos del rendimiento de su [proyecto de Servicio de aplicaciones móviles](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). Se muestran las solicitudes del servidor, los tiempos de respuesta, los errores y la cantidad de excepciones. También hay gráficos de tiempos de respuesta de dependencias; es decir, las llamadas a la base de datos y a las API de REST, como el Aprendizaje automático. Si hay algún problema de rendimiento, podrá ver qué parte de su sistema lo está provocando.

![Gráfico de rendimiento de ejemplo](./media/iot-solution-build-system/image11.png)

Si tiene un servicio web configurado a mano, también puede obtener los mismos gráficos: En la hoja de servicio web, elija **Herramientas** > **Extensiones** > **Agregar**. Seleccione **Application Insights**.

![Interfaz para seleccionar Application Insights para obtener los gráficos](./media/iot-solution-build-system/image12.png)

La característica funciona mediante la instrumentación de la aplicación con el SDK de Application Insights.

Puede agregar datos de telemetría personalizados (o instrumentar una aplicación que se ejecute fuera de Azure) si [agrega el SDK de Application Insights](../application-insights/app-insights-asp-net.md) en tiempo de desarrollo. Esto es útil para registrar métricas que dependen de la aplicación, como la duración promedio del viaje de los usuarios o el kilometraje total. En Visual Studio, haga clic con el botón derecho en el proyecto y luego seleccione **Agregar Application Insights**.

![Interfaz para seleccionar Agregar Application Insights para agregar datos de telemetría personalizados](./media/iot-solution-build-system/image10.png)

Application Insights enviará correos electrónicos de alerta si detecta números inusuales de respuestas de error. También puede configurar sus propias alertas en varias métricas, como tiempos de respuesta.

Para asegurarse de que el servicio web estará siempre en activo y en ejecución, puede configurar [pruebas de disponibilidad](../application-insights/app-insights-monitor-web-app-availability.md). Estas pruebas hacen ping a su sitio desde varias ubicaciones de todo el mundo cada 15 minutos. De nuevo, recibirá un correo electrónico en el caso de que haya algún problema.

## Estimación de costos operativos

Es muy económico ejecutar una aplicación como esta a pequeña escala. Muchos de los servicios tienen niveles de entrada gratuitos, por lo que el desarrollo y el funcionamiento a pequeña escala cuestan muy poco. Y, por supuesto, las aplicaciones no tienen por qué usar todas las características mostradas en MyDriving.

A continuación se incluye una estimación aproximada de los costos de la configuración de desarrollo para MyDriving. Asimismo, apuntamos algunas alternativas que *no* usamos. Esta información puede resultar útil a la hora de estimar sus propios costos.

Condiciones:

-   Equipo no superior a cinco integrantes (+ partes interesadas observadoras).

-   Ejecución durante un mes.

-   100 usuarios con cuatro viajes al día.

>[AZURE.NOTE] Si no está familiarizado con Azure, puede probar una [cuenta gratuita](https://azure.microsoft.com/free/).

| **Servicio/Componente** | **Notas** | **Costo/Mes** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) con [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>Entorno de desarrollo multiplataforma| Visual Studio Community. ([Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions) para [Xamarin.Forms](https://xamarin.com/forms) es necesario para el diseño multiplataforma desde una sola base de código). | 0 $ |
| [Centro de IoT de Azure](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Conexión de datos bidireccional con dispositivos. | 8000 mensajes + 0,5 KB/mensaje gratis | 0 $ |
| [Análisis de transmisiones](https://azure.microsoft.com/pricing/details/stream-analytics/) <br/> Procesamiento de datos de flujos de gran volumen. | Cargo de 0,031 $ por unidad de streaming por hora mientras esté habilitado. Usted selecciona el número de unidades de streaming que desee; para escalar verticalmente se necesita un mayor número. | 23 $ |
| [Aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Respuestas adaptables. | 10 $/puesto/mes. <br/> + 3 horas experimento * 1 $/hora de experimento. <br/> + 3,5 horas de CPU de la API * 2 $/hora de CPU de producción. <br/>El tiempo de CPU de la API asume 5 minutos al día de nuevo entrenamiento, aunque esto aumentaría con más datos de entrada. <br/> + 2 min/día de puntuación para procesar 400 viajes/día. | 20 $ |
| [Servicio de aplicaciones](https://azure.microsoft.com/pricing/details/app-service/) <br/> Host para el back-end móvil | Nivel B1: aplicaciones web de producción. | 56 $ |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/) <br/> Compilación, pruebas unitarias y administración de versiones; administración de tareas | Agentes privados, cinco usuarios.| 0 $ |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Supervisión de rendimiento y uso de sitios y servicios web| Nivel Gratis. | 0 $ |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Distribución de aplicaciones beta, más recopilación de comentarios e información de uso y bloqueo | Dos aplicaciones gratuitas para nuevos usuarios.<br/> Después, 30 $/mes. | 0 $ |
| [Xamarin](https://store.xamarin.com/)<br/> Código en una plataforma uniforme para varios dispositivos | Evaluación gratuita. <br/>Después, 25 $/mes.| 0 $ |
| [Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/) para el Servicio de aplicaciones de Azure| Nivel básico; modelo de base de datos única. | 5 $ |
| [Service Fabric](../service-fabric/services/service-fabric.md) (opcional) | Ejecutar un clúster local. | 0 $ |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Visualizaciones versátiles e investigación de datos transmitidos y estáticos| Nivel gratis: 1 GB, 10 000 filas/hora, actualización diaria <br/> # 10 $/usuario/mes para [aumentar los límites](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), más opciones de conexión y colaboración. | 0 $ |
| [Almacenamiento](https://azure.microsoft.com/pricing/details/storage/) | L (con redundancia local) &lt; 100 G $0,024/GB. | 3 $ |
| [Factoría de datos](https://azure.microsoft.com/pricing/details/data-factory/) | $0,60 por actividad * (8 - 5 FOC)| 2 $ |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/> Clúster a petición para nuevo entrenamiento diario | Tres nodos de A3 a 0,32 $/hora por 1 hora diaria * 31 días. | 30 $ |
| [Centros de eventos](https://azure.microsoft.com/pricing/details/event-hubs/) | Básico con unidad de procesamiento de 11 $/mes + entrada 0,028 $ | 11 $ |
| Llave OBD || 12 $ |
| **Total**| | **157 $** |

Para más información, consulte:

-   Resumen de [cuotas y límites del servicio de Azure](../azure-subscription-service-limits/#iot-hub-limits)

-   [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/) de Azure

## Envíenos sus comentarios

Como ya hemos creado MyDriving para ayudar a impulsar sus propios sistemas de IoT, queremos conocer su opinión acerca de cómo funciona. Queremos saber si:

-  Se enfrenta a dificultades y desafíos.

-  Hay un punto de extensión que puede resultarle más adecuado para su escenario.

-  Busca una forma más eficaz de realizar ciertas necesidades.

-  Tiene cualquier sugerencia para mejorar MyDriving o esta documentación.

Si lo desea, puede registrar un [problema en GitHub] o dejar un comentario a continuación (edición en inglés).

Esperamos sus noticias.

## Pasos siguientes

Se recomienda la lectura de la [guía de referencia de MyDriving](http://aka.ms/mydrivingdocs), donde se describe de manera detallada el diseño del sistema y sus componentes.

<!---HONumber=AcomDC_0518_2016-->