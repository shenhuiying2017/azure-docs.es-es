<properties linkid="manage-services-how-to-monitor-websites" urlDisplayName="How to monitor" pageTitle="How to monitor web sites - Azure service management" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="Learn how to monitor Azure web sites by using the Monitor page in the Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Web Sites" authors="" solutions="" manager="" editor="" />

<a name="howtomonitor"></a>Supervisión de sitios web
=========================

Los sitios web proporcionan la funcionalidad de supervisión a través de la página de administración de supervisión. La página de administración de supervisión brinda estadísticas del rendimiento de un sitio web, tal como se describe a continuación.

Tabla de contenido
------------------

-   [Incorporación de métricas de sitio web](#websitemetrics)
-   [Recepción de alertas desde métricas de sitio web](#howtoreceivealerts)
-   [Visualización de cuotas de uso para un sitio web](#howtoviewusage)
-   [Reducción del uso de recursos](#resourceusage)
-   [¿Qué sucede cuando se supera una cuota de uso de recursos?](#exceeded)
-   [Configuración de registros de diagnóstico y descarga para un sitio web](#howtoconfigdiagnostics)
-   [Supervisión del estado de extremo web](#webendpointstatus)

<a name="websitemetrics"></a>Incorporación de métricas de sitio web
--------------------------------------

1.  En el [Portal de administración de Azure](http://manage.windowsazure.com/), en las páginas de administración del sitio web, haga clic en la pestaña **Monitor** para mostrar la página de administración **Monitor**. De manera predeterminada, el gráfico de la pantalla **Monitor** muestra las mismas métricas que el gráfico de la página **Panel**.

2.  Si desea ver métricas adicionales para el sitio web, haga clic en **Add Metrics** en la parte inferior de la página para mostrar el cuadro de diálogo **Choose Metrics**.

3.  Haga clic para seleccionar métricas adicionales que mostrar en la página **Monitor**.

4.  Después de seleccionar las métricas que desea agregar a la página **Monitor**, haga clic en **Ok**.

5.  Después de agregar métricas en la página **Monitor**, haga clic para habilitar/deshabilitar la casilla de opción junto a cada métrica para agregar/quitar la métrica del gráfico en la parte superior de la página.

6.  Para quitar métricas de la página **Monitor**, seleccione la métrica que desea quitar y, a continuación, haga clic en el icono **Delete Metric** en la parte inferior de la página.

La siguiente lista describe las métricas que puede ver en el gráfico de la página **Monitor**:

-   **CPUTime**: Una medida del uso de CPU del sitio web.
-   **Requests**: Un recuento de las solicitudes de cliente al sitio web.
-   **Data Out**: Una medida de los datos que el sitio web envía a los clientes.
-   **Data In**: Una medida de los datos que el sitio web recibe de los clientes.
-   **Http Client Errors**: Cantidad de mensajes "4xx Client Error" http enviados.
-   **Http Server Errors**: Cantidad de mensajes "5xx Server Error" http enviados.
-   **Http Successes**: Cantidad de mensajes "2xx Success" http enviados.
-   **Http Redirects**: Cantidad de mensajes "3xx Redirection" http enviados.
-   **Http 401 errors**: Cantidad de mensajes "401 Unauthorized" http enviados.
-   **Http 403 errors**: Cantidad de mensajes http "403 Forbidden" http enviados.
-   **Http 404 errors**: Cantidad de mensajes http "404 Not Found" http enviados.
-   **Http 406 errors**: Cantidad de mensajes http "406 Not Acceptable" http enviados.

<a name="howtoreceivealerts"></a>Recepción de alertas desde métricas de sitio web
------------------------------------------------

En el modo de sitio web **estándar**, puede recibir alertas según las métricas de supervisión de su sitio web. La característica de alerta requiere primero configurar un extremo web para supervisión, lo que puede hacer en la sección **Monitoring** de la página **Configure**. En la página **Settings** del Portal de administración de Azure, puede crear una regla para desencadenar una alerta cuando la métrica seleccionada alcance el valor que haya especificado. Puede también elegir que se envíe un correo electrónico cuando se desencadene la alerta. Para obtener más información, consulte [Recibir notificaciones de alerta y administrar reglas de alerta en Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

<a name="howtoviewusage"></a>Visualización de cuotas de uso para un sitio web
------------------------------------------------

Los sitios web pueden estar configurados para ejecutarse en modo de sitio web **compartido** o **estándar** desde la página de administración **Scale** del sitio web. Cada suscripción a Azure tiene acceso a un conjunto de recursos que tienen como finalidad la ejecución de hasta 100 sitios web por región en el modo de sitio web **compartido**. El grupo de recursos disponibles para cada suscripción de Sitio web con este fin es compartido por otros sitios web en la misma región geográfica que estén configurados para ejecutarse en modo **compartido**. Como estos recursos son compartidos para su uso de parte de otros sitios web, todas las suscripciones tienen límites en el uso que hacen de estos recursos. Los límites que se aplican al uso que una suscripción hace de estos recursos se expresan como cuotas de uso enumeradas bajo la sección de información general de uso en la página de administración de **Panel** de cada sitio web.

**Nota:**   
 cuando un sitio web está configurado para ejecutarse en modo **estándar**, se le asignan recursos dedicados equivalentes a los tamaños **Pequeño** (valor predeterminado), **Mediano** o **Grande** de máquina virtual en la tabla de [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://go.microsoft.com/fwlink/?LinkID=309169). No hay límites en los recursos que puede utilizar una suscripción para ejecutar sitios web en modo **estándar**. Sin embargo, la cantidad de sitios web en modo **estándar** que se pueden crear por región es 500.

### Visualización de cuotas de uso para sitios web configurados para el modo de sitio web compartido

Para determinar el alcance del impacto de un sitio web en las cuotas de uso de los recursos, siga estos pasos:

1.  Abra la página de administración **Panel** del sitio web.
2.  En la sección de **información general del uso**, aparecen las cuotas de uso para **Data Out**, **CPU Time** y **File System Storage**. La barra verde que aparece para cada recurso indica qué cantidad de la cuota de uso de recursos de una suscripción consume el sitio web actual, mientras que la barra gris que aparece para cada recurso indica qué cantidad de la cuota de uso de recursos de una suscripción consumen todos los demás sitios web de modo compartido asociados con su suscripción a Sitio web.

Las cuotas de uso de recursos ayudan a evitar el uso excesivo de los siguientes recursos:

-   **Data Out**: Una medida de la cantidad de datos enviados desde sitios web que se ejecutan en modo **compartido** a sus clientes en el intervalo de cuota actual (24 horas).
-   **CPU Time**: La cantidad de tiempo de CPU que usan los sitios web que se ejecutan en modo **compartido** para el intervalo de cuota actual.
-   **File System Storage**: La cantidad de almacenamiento de sistema de archivos que usan los sitios web que se ejecutan en modo **compartido**.

Cuando se exceden las cuotas de uso de una suscripción, Azure actúa para detener el uso excesivo de los recursos. Esto se hace para evitar que algún suscriptor agote los recursos en perjuicio de otros suscriptores.

<a name="resourceusage"></a>Reducción del uso de recursos
-----------------------------

Debido a que Azure calcula las cuotas de uso de recursos al medir los recursos que utilizan los sitios web en modo compartido de una suscripción durante un intervalo de cuota de 24 horas, considere lo siguiente:

-   Dado que la cantidad de sitios web configurados para ejecutarse en modo compartido aumenta, también aumenta la probabilidad de exceder las cuotas de uso de recursos en modo compartido. Considere reducir la cantidad de sitios web que están configurados para ejecutarse en modo compartido si se exceden las cuotas de uso de recursos.
-   De manera similar, dado que la cantidad de instancias de cualquier sitio web en ejecución en modo compartido aumenta, también aumenta la probabilidad de exceder las cuotas de uso de recursos en modo compartido. Considere volver a escalar instancias adicionales de sitios web en modo compartido si se están excediendo las cuotas de uso.

<a name="exceeded"></a>¿Qué sucede cuando se supera una cuota de uso de recursos?
----------------------------------------------------------

Azure lleva a cabo las siguientes acciones si se superan las cuotas de uso de recursos de una suscripción en un intervalo de cuota (24 horas):

-   **Data Out**: Cuando se excede esta cuota, Azure detiene todos los sitios web de una suscripción que estén configurados para ejecutarse en modo **compartido** por el resto del intervalo de cuota actual. Azure iniciará los sitios web al comienzo del siguiente intervalo de cuota.

-   **CPU Time**: Cuando se excede esta cuota, Azure detiene todos los sitios web de una suscripción que estén configurados para ejecutarse en modo **compartido** por el resto del intervalo de cuota actual. Azure iniciará los sitios web al comienzo del siguiente intervalo de cuota.

-   **File System Storage**: Azure evita la implementación de cualquier sitio web para una suscripción que esté configurado para ejecutarse en modo compartido si la implementación hará que se exceda la cuota de uso del almacenamiento de sistema de archivos. Cuando el recurso de almacenamiento de sistema de archivos llega al tamaño máximo permitido por su cuota, el almacenamiento de sistema de archivos queda accesible para las operaciones de lectura, pero se bloquean todas las operaciones de escritura, incluidas las necesarias para la actividad normal de un sitio web. Cuando esto ocurre, puede configurar uno o más sitios web en ejecución en modo compartido de sitio web para que se ejecuten en modo estándar de sitio web, o bien, reducir el uso de almacenamiento del sistema de archivos por debajo de la cuota de uso del almacenamiento del sistema de archivos.

<a name="howtoconfigdiagnostics"></a>Configuración de registros de diagnóstico y descarga para un sitio web
----------------------------------------------------------------------

Los diagnósticos se habilitan en la página de administración **Configure** del sitio web. Existen dos tipos de diagnóstico: **diagnósticos de la aplicación** y **diagnósticos del sitio**.

#### Diagnósticos de la aplicación

La sección de **diagnósticos de la aplicación** en la página de administración **Configure** controla el registro de la información que genera la aplicación, que resulta útil cuando se registran eventos que se producen dentro de una aplicación. Por ejemplo, cuando se produce un error en su aplicación, es posible que desee presentar al usuario un error fácil de comprender mientras que se escribe una información más detallada del error en el registro para un análisis posterior.

Puede habilitar o deshabilitar los siguientes diagnósticos de aplicación:

-   **Application Logging (File System)**: Activa el registro de la información que genera la aplicación. El campo **Logging Level** determina si se registra información en el nivel de error, advertencia o información. También puede seleccionar Verbose, que registrará toda la información generada por la aplicación.

    Los registros que se generan a partir de esta configuración se almacenan en el sistema de archivos del sitio web y se pueden descargar al seguir los pasos de la sección **Descarga de archivos de registro para un sitio web** que aparece a continuación.

-   **Application Logging (Table Storage)**: Activa el registro de la información que genera la aplicación, de manera similar a la opción Application Logging (File System). Sin embargo, la información de registro se almacena en una cuenta de almacenamiento de Azure en una tabla.

    Para especificar la tabla y la cuenta de almacenamiento de Azure, elija **On**, seleccione el **Logging Level** y, a continuación, **Manage Table Storage**. Especifique la tabla y la cuenta de almacenamiento que se utilizará o cree una tabla nueva.

    Puede tener acceso a la información de registro almacenada en la tabla a través de un cliente de almacenamiento de Azure.

-   **Application Logging (Blob storage)**: Activa el registro de la información que genera la aplicación, de manera similar a la opción Application Logging (Table Storage). Sin embargo, la información de registro se almacena en una cuenta de almacenamiento de Azure en un blob.

    Para especificar el blob y la cuenta de almacenamiento de Azure, elija **On**, seleccione el **Logging Level** y, a continuación, **Manage Blob Storage**. Especifique la cuenta de almacenamiento, el contenedor de blobs y el nombre de blob que se utilizará o cree un contenedor y un blob nuevos.

Para obtener más información acerca de las cuentas de almacenamiento de Azure, consulte [Administración de cuentas de almacenamiento](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/).
**Nota:**

El registro de aplicaciones en almacenamiento de tablas o blobs solo es compatible con aplicaciones .NET.

Como el registro de aplicaciones en almacenamiento requiere utilizar un cliente de almacenamiento para ver los datos de registro, resulta más útil cuando planea utilizar un servicio o aplicación que comprenda cómo leer y procesar los datos directamente desde el almacenamiento de tablas o blobs de Azure. El registro en el sistema de archivos genera archivos que se pueden descargar en el equipo local a través de FTP u otras utilidades, según se describe más adelante en esta sección.
**Nota:**

**Application diagnostics (file system)**, **Application diagnostics (table storage)** y **Application diagnostics (blob storage)** se pueden habilitar al mismo tiempo y pueden tener configuraciones individuales en el nivel de registro. Por ejemplo, es posible que desee registrar errores y advertencias en el almacenamiento como una solución de registro a largo plazo, mientras que se habilita el registro de sistema de archivos con un nivel de detalle después de instrumentar el código de la aplicación para solucionar un problema.

**Nota:**

Los diagnósticos también se pueden habilitar desde Azure PowerShell con el cmdlet **Set-AzureWebsite**.

Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/).

**Nota:**

El registro de aplicaciones se basa en la información de registro que genera su aplicación. El método usado para generar información de registro, así como también el formato de la información, es específico para el lenguaje en que está escrita la aplicación. Si desea información específica para el lenguaje sobre el uso del registro de aplicaciones, consulte los siguientes artículos:

-   **.NET**: [Habilitación del registro de diagnóstico para Sitios web Azure](/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
-   **Node.js**: [Depuración de una aplicación Node.js en Sitios web Azure](/en-us/develop/nodejs/how-to-guides/Debug-Website/)

El registro de aplicaciones en almacenamiento de tablas o blobs solo es compatible con aplicaciones .NET.

#### Diagnósticos de sitios

La sección de **diagnósticos de sitios** de la página de administración **Configure** controla el registro que lleva a cabo el servidor web, como el registro de solicitudes web, los errores al atender páginas y el tiempo que se tardó en atender una página. Puede habilitar o deshabilitar las siguientes opciones:

-   **Web Server Logging**: Active el registro de servidores web para guardar registros de sitios web que utilicen formato de archivo de registro W3C extendido. El registro de servidores web genera un registro de todas las solicitudes entrantes a su sitio web, que contiene información como la dirección IP del cliente, el URI solicitado, el código de estado HTTP de la respuesta y la cadena de agente de usuario del cliente. Puede guardar los registros en una cuenta de almacenamiento de Azure o en el sistema de archivos.

 Para guardar los registros de servidores web en una cuenta de almacenamiento de Azure, elija **Almacenamiento** y, a continuación, **manage storage** para especificar un contenedor de blobs de Azure donde se conservarán los registros. Para obtener más información acerca de las cuentas de almacenamiento de Azure, consulte [Administración de cuentas de almacenamiento](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/).

 Para guardar registros de servidores web en el sistema de archivos, elija **File System**. Con esto se habilitará la casilla **Quota**, en la que podrá definir la cantidad máxima de espacio en disco para los archivos de registro. El tamaño mínimo es de 25 MB, mientras que el máximo es de 100 MB. El tamaño predeterminado es de 35 MB.

 De manera predeterminada, nunca se eliminan los registros de servidores web. Para especificar un período después del cual los registros se eliminarán automáticamente, seleccione **Set Retention** y escriba el número de días durante los cuales conservar los registros en el cuadro **Retention Period**. Esta configuración está disponible para las opciones de almacenamiento de Azure y del sistema de archivos.

-   **Detailed Error Messages**: Active un registro detallado de los errores para registrar información adicional acerca de los errores HTTP (códigos de estado mayores que 400).

-   **Failed Request Tracing**: Active el seguimiento de solicitudes con error para capturar información para solicitudes de clientes con error, como un código de estado HTTP de la serie 400. El seguimiento de solicitudes con error genera un documento XML que contiene un seguimiento de los módulos por los que la solicitud ha pasado en IIS, los detalles devueltos por el módulo y la hora en que se invocó el módulo. Esta información se puede utilizar para aislar el componente en que se produjo el error.

Después de habilitar los diagnósticos para un sitio web, haga clic en el icono **Save** en la parte inferior de la página de administración **Configure** para así aplicar las opciones que definió.
**Importante**

El registro y el seguimiento plantean exigencias importantes sobre un sitio web. Recomendamos desactivar el registro y el seguimiento una vez que haya reproducido los problemas que se están solucionando.

### Configuración avanzada

Es posible seguir modificando los diagnósticos si agrega pares clave/valor a la sección **app settings** de la página de administración **Configure**. Los siguientes ajustes se pueden configurar desde **app settings**:

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

-   La ubicación en que se guardarán los registros de aplicaciones, en relación con la raíz web.

-   Valor predeterminado: ..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

-   El tamaño máximo de búfer que se utilizará cuando se capturen registros de aplicación. La información inicialmente se escribe en el búfer antes de vaciarla al archivo o el almacenamiento. Si se escribe información nueva en el búfer antes de poder vaciarla, es posible que pierda la información anteriormente registrada. Si la aplicación genera grandes ráfagas de información de registro, considere aumentar el tamaño del búfer.

-   Valor predeterminado: 10 MB

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

-   El tamaño máximo de la carpeta **Application** en la que se almacenan los diagnósticos de aplicaciones escritos en el archivo.

-   Valor predeterminado: 1 MB

### Descarga de archivos de registro para un sitio web

Los archivos de registro se pueden descargar mediante el uso de FTP, Azure PowerShell o las herramientas de línea de comandos de Azure.

**FTP**

1.  Abra la página de administración **Panel** del sitio web y tome nota del sitio FTP que aparece en **Diagnostics Logs** y la cuenta que aparece en **Deployment User**. El sitio FTP es donde se ubican los archivos de registro y la cuenta que aparece bajo Deployment User se utiliza para autenticarse en el sitio FTP.
2.  Si todavía no ha creado credenciales de implementación, la cuenta que aparece en **Deployment User** se muestra como **Not set**. En este caso, debe crear credenciales de implementación tal como se describen en la sección Reset Deployment Credentials del Panel, porque estas credenciales se deben usar para autenticarse en el sitio de FTP donde se almacenan los archivos de registro. Azure no es compatible con la autenticación en este sitio FTP con las credenciales de Live ID.
3.  Considere utilizar un cliente FTP, como [FileZilla](http://go.microsoft.com/fwlink/?LinkId=247914), para conectarse al sitio FTP. Con un cliente FTP es más fácil especificar las credenciales y visualizar las carpetas en un sitio FTP de lo que normalmente sería posible con un explorador.
4.  Copie los archivos de registro desde el sitio FTP a su equipo local.

**Azure PowerShell**

1.  En la **pantalla Inicio** o en el **menú Inicio**, busque **Azure PowerShell**. Haga clic con el botón secundario en la entrada **Azure PowerShell** y seleccione **Ejecutar como administrador**.

    **Nota:**

    Si **Azure PowerShell** no está instalado, consulte [Introducción a los cmdlets de Azure PowerShell](http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx) para obtener información sobre la instalación y configuración.

2.  Desde el símbolo del sistema de Azure PowerShell, utilice el siguiente comando para descargar los archivos de registro:

         Save-AzureWebSiteLog -Name websitename

    Con esto se descargarán los archivos de registro para el sitio web especificado en el **websitename** y se guardarán en un archivo **log.zip** en el directorio actual.

    También puede ver una secuencia en vivo de eventos de registro a través del siguiente comando:

         Get-AzureWebSiteLog -Name websitename -Tail

    Con esto aparecerá información de registros en el símbolo del sistema de Azure PowerShell a medida que se produzcan.

**Herramientas de línea de comandos de Azure**

Abra una nueva sesión de Terminal, sesión Bash, PowerShell o símbolo del sistema y utilice el siguiente comando para descargar los archivos de registro:

    azure site log download websitename

Con esto se descargarán los archivos de registro para el sitio web especificado en el **websitename** y se guardarán en un archivo **log.zip** en el directorio actual.

También puede ver una secuencia en vivo de eventos de registro a través del siguiente comando:

    azure site log tail websitename

Con esto aparecerá información de registros en la sesión de Terminal, sesión Bash, PowerShell o símbolo del sistema desde donde se ejecuta el comando.
**Nota:**

Si el comando **azure** no está instalado, consulte [Uso de las herramientas de línea de comandos](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/) para obtener información sobre la instalación y la configuración.

### Lectura de archivos de registro

Los archivos de registro que se generan después de habilitar el registro o el seguimiento de un sitio web varían según el nivel de registro/seguimiento definido en la página de administración Configure para el sitio web. A continuación aparece la ubicación de los archivos de registro y la manera en que se pueden analizar:

**Tipo de archivo de registro: Registro de aplicaciones**

-   Ubicación /LogFiles/Application/. Esta carpeta contiene uno o más archivos de texto que contienen información generada por el registro de aplicaciones. La información registrada incluye la fecha y la hora, el identificador del proceso (PID) de la aplicación y el valor que produce la instrumentación de la aplicación.

-   Lea los archivos con: Un editor de texto o un analizador que comprenda los valores que genera su aplicación

**Tipo de archivo de registro: Seguimiento de solicitudes con error**

-   Ubicación: /LogFiles/W3SVC########\#/. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio de los archivos XML, porque el archivo XSL proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML cuando se visualizan en Internet Explorer.

-   Lea los archivos con: Internet Explorer

**Tipo de archivo de registro: Registro detallado de errores**

-   Ubicación: /LogFiles/DetailedErrors/. La carpeta /LogFiles/DetailedErrors/ contiene uno o más archivos .htm que proporcionan una amplia información para cualquier error HTTP que se haya generado.

-   Lea los archivos con: Explorador web

Los archivos .htm incluyen las siguientes opciones:

-   **Detailed Error Information:** Incluye información acerca del error, como *Module*, *Handler*, *Error Code* y *Requested URL*.

-   **Most likely causes:** Enumera varias causas posibles del error.

-   **Things you can try:** Enumera posibles soluciones para resolver el problema indicado por el error.

-   **Links and More Information**: Proporciona información resumida adicional acerca del error y también puede incluir vínculos a otros recursos, como artículos de Microsoft Knowledge Base.

**Tipo de archivo de registro: Registro del servidor web**

-   Ubicación: /LogFiles/http/RawLogs. El formato de la información almacenada en los archivos se aplica con [W3C extended log format](http://go.microsoft.com/fwlink/?LinkID=90561). Sitios web Azure no utiliza los campos s-computername, s-ip y cs-version.

-   Lea los archivos con: Analizador de registro. Se usa para analizar y consultar archivos de registro de IIS. El Analizador de registro 2.2 está disponible en el Centro de descarga de Microsoft en <http://go.microsoft.com/fwlink/?LinkId=246619>.

<a name="webendpointstatus"></a>Supervisión de estado de extremo web
------------------------------------

Esta característica, disponible en modo **estándar**, le permite supervisar hasta dos extremos desde hasta tres ubicaciones geográficas.

La supervisión de extremo configura pruebas web desde ubicaciones distribuidas geográficamente que prueban el tiempo de respuesta y el tiempo activo de direcciones URL web. La prueba realiza una operación HTTP Get en la dirección URL web para determinar el tiempo de respuesta y el tiempo activo desde cada ubicación. Cada ubicación configurada ejecuta una prueba cada cinco minutos.

El tiempo activo se supervisa a través de códigos de respuesta de HTTP y el tiempo de respuesta se mide en milisegundos. El tiempo activo se considera en un 100 % cuando el tiempo de respuesta es menor que 30 segundos y el código de estado HTTP es menor que 400. El tiempo activo es de 0 % cuando el tiempo de respuesta es mayor que 30 segundos o el código de estado HTTP es mayor que 400.

Después de configurar la supervisión de extremo, puede obtener detalles sobre los extremos individuales para ver el estado del tiempo activo y el tiempo de respuesta sobre el intervalo de supervisión desde cada una de las ubicaciones de prueba.

**Para configurar la supervisión de extremo:**

1.  Abra **Sitios web**. Haga clic en el nombre del sitio web que desea configurar.
2.  Haga clic en la pestaña **Configure**.
3.  Vaya a la sección **Monitoring** para especificar la configuración de extremo.
4.  Escriba un nombre para el extremo.
5.  Escriba la dirección URL del servicio que desea supervisar. Por ejemplo, <http://contoso.cloudapp.net>.
6.  Seleccione una o más ubicaciones geográficas en la lista.
7.  De manera opcional, repita los pasos anteriores para crear un segundo extremo.
8.  Haga clic en **Save**. Es posible que los datos de supervisión del extremo web tarden cierto tiempo en estar disponibles en las pestañas **Panel** y **Monitor**.

Vea el siguiente vídeo para obtener más información sobre la supervisión de extremos de sitio web:

-   [Scott Guthrie introduces Azure Web Sites and sets up Endpoint Monitoring](http://www.windowsazure.com/en-us/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

-   [Keeping Azure Web Sites up plus Endpoint Monitoring - with Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)


[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169
