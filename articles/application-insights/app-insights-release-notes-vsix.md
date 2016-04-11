<properties
	pageTitle="Notas de la versión de la extensión de Visual Studio para Developer Analytics"
	description="Las últimas novedades sobre las herramientas de Visual Studio para Developer Analytics."
	services="application-insights"
    documentationCenter=""
	authors="aruna"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="acearun"/>

# Notas de la versión: herramientas de Developer Analytics
##### Análisis de Application Insights y HockeyApp en Visual Studio
## Versión 5.2
Nos complace anunciar la presentación de los escenarios de HockeyApp en Visual Studio. La primera integración que hemos habilitado es la distribución beta de las aplicaciones universales de Windows y Windows Forms dentro de VS.

La distribución beta le permite cargar las primeras versiones de las aplicaciones a HockeyApp para su distribución a un subconjunto determinado de clientes o evaluadores. La distribución beta, combinada con las características de comentarios del usuario y recopilación de bloqueo de HockeyApp, puede proporcionar información importante acerca de su aplicación antes de su lanzamiento general. Puede usar esta información para abordar los problemas de su aplicación antes de que llegue a ser un problema importante (calificaciones bajas, comentarios negativos, etc.).

Compruebe lo sencillo que es cargar compilaciones para la distribución beta desde VS...
### Aplicaciones universales de Windows
El menú contextual de un nodo de proyecto para UWP incluye ahora una opción para cargar la compilación en HockeyApp.

![Menú contextual del proyecto para las aplicaciones universales](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Elija el elemento y consulte el cuadro de diálogo de carga de HockeyApp. Necesitará una cuenta de HockeyApp para cargar la compilación. No se preocupe si es un usuario nuevo, crear una cuenta es un proceso sencillo.

Una vez conectado, verá el formulario de carga en el cuadro de diálogo.

![Cuadro de diálogo de carga de las aplicaciones universales](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Seleccione el contenido para cargar (appxbundle o appx) y elija las opciones de la versión en el asistente. Opcionalmente, puede agregar notas de la versión en la página siguiente. Haga clic en "Finalizar" para comenzar la carga.

Una vez finalizada la carga, verá una notificación del sistema de HockeyApp con la confirmación y un vínculo a la aplicación en el portal de HockeyApp.

![Notificación del sistema de carga completa](./media/app-insights-release-notes-vsix/UploadComplete.png)

Eso es todo. Acaba de cargar una compilación para la distribución beta con tan solo unos clics.

El portal de HockeyApp le permite administrar la aplicación de varias maneras (invitar a usuarios, ver informes de bloqueo y comentarios, cambiar detalles, etc.).

![Portal de HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Si desea más información sobre la administración de la aplicación, esta se encuentra disponible en la [Knowledge Base de HockeyApp](http://support.hockeyapp.net/kb/app-management-2).

### Aplicaciones de Windows Forms
El menú contextual de un nodo de proyecto de Windows Forms incluye una opción para cargar la compilación en HockeyApp.

![Menú contextual del proyecto para las aplicaciones de Windows Forms](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Se abrirá el cuadro de diálogo de carga de HockeyApp que es similar al de las aplicaciones universales.

![Cuadro de diálogo de carga de las aplicaciones de Windows Forms](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

Tenga en cuenta un campo adicional en este asistente,para especificar la versión de la aplicación. Para las aplicaciones universales, la información se rellena del manifiesto; lamentablemente Win Forms no tiene un equivalente y, por lo tanto, es necesario especificarlo de forma manual.

El resto del flujo es similar al de las aplicaciones universales: elegir la compilación, revelar las opciones, agregar notas de la versión, cargar y administrar en el portal de HockeyApp.

Es así de sencillo. Pruébelo y díganos qué le parece.
## Versión 4.3
### Búsqueda de telemetría desde sesiones de depuración local
Con esta versión, introducimos la capacidad para buscar la telemetría de Application Insights generada en la sesión de depuración de Visual Studio. Anteriormente, la búsqueda solo era posible si había registrado la aplicación con Application Insights. Con esta versión, la aplicación únicamente necesita que el SDK de Application Insights esté instalado para buscar la telemetría local.

#### Si tiene una aplicación ASP.NET con el SDK de Application Insights

- Depure la aplicación.
- Abra Búsqueda de Application Insights de una de estas formas.
	- Menú Ver -> Otras ventanas -> Búsqueda de Application Insights
	- Haga clic en el botón de la barra de herramientas de Application Insights.
	- En el Explorador de soluciones, expanda ApplicationInsights.config -> Busque la telemetría de la sesión de depuración.
- Si aún no se ha registrado con Application Insights, se abrirá la ventana de búsqueda en el modo de telemetría de la sesión de depuración.
- Haga clic en el icono de búsqueda para ver los datos de telemetría local.

![Carga completa](./media/app-insights-release-notes-vsix/LocalSearch.png)



##Versión 4.2
En esta versión hemos agregado características para facilitar la búsqueda de datos en el contexto de eventos, la capacidad de saltar al código desde más eventos de datos y una experiencia para enviar sin esfuerzo los datos de registro a Application Insights. Esta extensión se actualiza mensualmente; si tiene comentarios o solicitudes de características, envíelos a aidevtools@microsoft.com.
###- Experiencia de registro sin un solo clic
Si ya está usando el seguimiento de NLog, Log4Net o System.Diagnostics, no tendrá que preocuparse acerca de cómo mover todos los seguimientos a AI, ya que estamos integrando los adaptadores de registro de Application Insights con la experiencia de configuración normal. Si ya tiene uno de estos marcos de registro configurados, esto es lo que debe hacer:
####Si ya ha agregado Application Insights
- Haga clic con el botón derecho en el nodo de proyecto y elija Application Insights, Configurar Application Insights. Asegúrese de que ve el la opción de agregar el adaptador correcto en la ventana de configuración.
- O bien, cuando compile la solución, observe la ventana emergente que aparece en la parte superior derecha de la pantalla y haga clic en Configurar. ![Aviso de inicio de sesión](./media/app-insights-release-notes-vsix/LoggingToast.png)

Una vez instalado el adaptador de registro, puede ejecutar la aplicación y asegurarse de que ve los datos en la pestaña Herramientas de diagnóstico de la forma siguiente: ![Seguimientos](./media/app-insights-release-notes-vsix/Traces.png)
###- El usuario puede saltar al código o buscarlo donde se genera la propiedad de evento de telemetría
Con la nueva versión, el usuario puede hacer clic en cualquier valor en el detalle de eventos para buscar una cadena coincidente en la solución abierta en ese momento. Se mostrarán resultados en la lista "Resultados de la búsqueda" de Visual Studio tal como se muestra a continuación: ![Buscar coincidencia](./media/app-insights-release-notes-vsix/FindMatch.png)
###- Nueva pantalla para el usuario que no ha iniciado sesión en la ventana de búsqueda
Hemos mejorado la apariencia de la ventana de búsqueda para guiar a los usuarios en su búsqueda de los datos de producción. ![Ventana de búsqueda](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- El usuario puede ver todos los eventos de telemetría asociados al evento
Se agregó una nueva pestaña situada junto a los detalles del evento que contiene consultas predefinidas para ver todos los datos relacionados con el evento de telemetría que está buscando el usuario. Por ejemplo: una solicitud tiene un campo denominado identificador de operación y todos los eventos asociados a esta solicitud tendrán el mismo identificador de operación; por tanto, si se produjera una excepción al procesar la solicitud, se obtendría el mismo identificador de operación de la solicitud para facilitar su búsqueda, y así sucesivamente. De esta forma, el usuario que vea una solicitud ahora, podrá hacer clic en "Toda la telemetría para esta operación" para abrir una nueva pestaña con los nuevos resultados de búsqueda. ![Elementos relacionados](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - Agregar historial hacia delante y hacia atrás en Búsqueda
El usuario ahora puede ir hacia delante y hacia atrás entre los resultados de la búsqueda. ![Volver](./media/app-insights-release-notes-vsix/GoBAck.png)

##Versión 4.1
Esta versión incluye una serie de nuevas características y mejoras de las existentes. Para obtener esta versión, debe haber instalado Actualización 1 en el equipo.

### Salto de una excepción al método en el código fuente
Ahora los usuarios que vean excepciones procedentes de sus aplicaciones de producción en la ventana Búsqueda de Application Insights pueden saltar al método del código donde se haya producido la excepción. Basta con tener cargado el proyecto adecuado; nosotros nos encargamos del resto. (Para más información sobre la ventana Búsqueda, consulte las notas de la versión 4.0 a continuación).

#### ¿Cómo funciona?

Cuando no hay ninguna solución abierta, se puede usar Búsqueda de Application Insights sin tener que abrir una. En ese caso, el área de seguimiento de la pila mostrará un mensaje informativo y muchos de los elementos en ella aparecerán atenuados.


Si había información disponible sobre el archivo, es posible que algunos elementos sean vínculos, pero el elemento de información de la solución seguirá estando visible.

Al hacer clic en el hipervínculo, le llevará al lugar donde se encuentra el método seleccionado en el código. Puede que exista una diferencia en el número de versión, pero la característica para saltar a la versión correcta del código se incluirá en versiones posteriores.

![Al hacer clic en la excepción](./media/app-insights-release-notes-vsix/jumptocode.png)

###Nuevos puntos de entrada a la experiencia de búsqueda en el Explorador de soluciones

![Punto de entrada en el Explorador de soluciones](./media/app-insights-release-notes-vsix/searchentry.png)


###Notificación emergente del sistema cuando se completa la publicación
Aparecerá un elemento emergente una vez que se publique el proyecto en línea, para que vea los datos de Application Insights en producción.

![Notificación emergente](./media/app-insights-release-notes-vsix/publishtoast.png)

## Versión 4.0

###Datos de Búsqueda de Application Insights en Visual Studio
Al igual que Búsqueda en el portal de Application Insights, puede filtrar y hacer búsquedas por tipos de eventos, valores de propiedades y texto, además de inspeccionar eventos individuales.

![Ventana de búsqueda](./media/app-insights-release-notes-vsix/search.png)

###Visualización de datos procedentes de su cuadro local en la ventana Herramientas de diagnóstico

La telemetría también aparecerá junto con otros datos de depuración en el concentrador de diagnósticos de Visual Studio. Esto solo admite ASP.NET 4.5; la compatibilidad con ASP.NET 5 se incluirá en próximas versiones.

![Ventana del concentrador de diagnósticos](./media/app-insights-release-notes-vsix/diagtools.png)

###Incorporación del SDK a su proyecto sin tener que iniciar sesión en Azure

Ya no tendrá que iniciar sesión en Azure para agregar paquetes de Application Insights al proyecto, ya sea en el cuadro de diálogo Nuevo proyecto o en el menú contextual del proyecto. Si inicia sesión, se instalará y configurará el SDK para enviar telemetría al portal, como antes. Si no inicia sesión, se agregará el SDK al proyecto y se generará telemetría para el concentrador de diagnósticos; podrá configurarlo más adelante si se desea.

![Cuadro de diálogo Nuevo proyecto](./media/app-insights-release-notes-vsix/newproject.png)

###Compatibilidad con dispositivos

En *Connect();* 2015 [anunciamos](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que nuestra experiencia de DevOps para móviles con dispositivos es HockeyApp. HockeyApp ayuda a distribuir compilaciones beta a los evaluadores, recopilar y analizar todos los bloqueos de la aplicación y recopilar comentarios directamente de los clientes. HockeyApp es compatible con cualquier plataforma en la que compile la aplicación móvil, ya sea iOS, Android, Windows o una solución multiplataforma como Xamarin, Cordova o Unity.

En versiones futuras de la extensión Application Insights, agregaremos nuevas funcionalidades para hacer posible una experiencia más integrada entre HockeyApp y Visual Studio. Por ahora, para empezar a trabajar con HockeyApp, basta con agregar la referencia de NuGet: consulte la [documentación](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) para obtener más información.

<!---HONumber=AcomDC_0330_2016-->