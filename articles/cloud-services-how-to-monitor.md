<properties urlDisplayName="How to monitor" pageTitle="Supervisi&oacute;n de un servicio en la nube - Azure" metaKeywords="Azure monitoring cloud services, Azure Management Portal cloud services" description="Vea c&oacute;mo supervisar servicios en la nube usando el Portal de administraci&oacute;n de Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Supervisi&oacute;n de servicios en la nube" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Supervisión de servicios en la nube

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Puede supervisar las métricas de rendimiento principales de sus servicios en la nube en el Portal de administración de Azure. También es posible configurar el nivel de supervisión a mínimo o detallado para cada rol del servicio, así como personalizar la visualización de la supervisión. Los datos de la supervisión detallada se almacenan en una cuenta de almacenamiento, a la que puede obtener acceso fuera del portal.

En el Portal de administración, puede configurar muchos parámetros de las visualizaciones de la supervisión. Puede elegir las métricas que desee supervisar en la lista de métricas, en la página **Supervisión**, así como las métricas que desea mostrar en los gráficos de métricas de la página **Supervisión** y del panel.

## Tabla de contenido

-   [Conceptos][Conceptos]
-   [Direccionamiento del la supervisión para los servicios en la nube][Direccionamiento del la supervisión para los servicios en la nube]
-   [Direccionamiento del alertas de las métricas de los servicios en la nube][Direccionamiento del alertas de las métricas de los servicios en la nube]
-   [Direccionamiento del métricas en la tabla de métricas][Direccionamiento del métricas en la tabla de métricas]
-   [Direccionamiento del gráfico de métricas][Direccionamiento del gráfico de métricas]
-   [Direccionamiento del los datos de supervisión detallada fuera del Portal de administración][Direccionamiento del los datos de supervisión detallada fuera del Portal de administración]

## <span id="concepts"></span></a>Conceptos

De forma predeterminada, para un servicio en la nube nuevo, se proporciona la supervisión mínima con contadores de rendimiento recopilados del sistema operativo host para las instancias de los roles (máquinas virtuales). Las métricas mínimas se limitan a porcentaje de CPU, datos de entrada, datos de salida, rendimiento de lectura de disco y rendimiento de escritura de disco. Al configurar la supervisión detallada, puede recibir métricas adicionales en función de los datos de rendimiento de las máquinas virtuales (instancias de rol). Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones.

> [WACOM.NOTE]
> Si utiliza supervisión detallada, puede agregar más contadores de rendimiento en el inicio de la instancia de rol, a través de un archivo de configuración de diagnóstico. Para poder supervisar estas métricas en el Portal de administración, debe agregar los contadores de rendimiento antes de configurar la supervisión detallada. Para obtener más información, consulte [Habilitación de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure][Habilitación de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure].

De forma predeterminada, los datos del contador de rendimiento de las instancias de los roles se muestrean y transfieren desde la instancia de rol en intervalos de 3 minutos. Al activar la supervisión detallada, los datos del contador de rendimiento se agregan para cada instancia de rol y entre las instancias de rol de cada rol en intervalos de 5 minutos, 1 hora y 12 horas. Los datos agregados se eliminan después de 10 días.

Después de activar la supervisión detallada, los datos de supervisión agregados se almacenan en tablas en su cuenta de almacenamiento. Para activar la supervisión detallada de un rol, debe configurar una cadena de conexión de diagnósticos que lo vincule a la cuenta de almacenamiento. Puede utilizar cuentas de almacenamiento diferentes para roles diferentes.

Tenga en cuenta que la activación de la supervisión detallada aumentará los costes de almacenamiento relacionados con el almacenamiento de datos, la transferencia de datos y las transacciones de almacenamiento. La supervisión mínima no requiere una cuenta de almacenamiento. Los datos de las métricas que se exponen a un nivel mínimo de supervisión no se almacenan en su cuenta de almacenamiento, incluso si configura la supervisión en un nivel detallado.

## <span id="verbose"></span></a>Direccionamiento del la supervisión para los servicios en la nube

Utilice los siguientes procedimientos para configurar la supervisión detallada o mínima en el Portal de administración. No puede activar la supervisión detallada hasta que active Diagnósticos de Azure y configure las cadenas de conexión de diagnósticos para permitir que Diagnósticos de Azure obtenga acceso a las cuentas de almacenamiento para almacenar los datos de supervisión detallados.

### Antes de empezar

-   Cree una cuenta de almacenamiento para almacenar los datos de supervisión. Puede utilizar cuentas de almacenamiento diferentes para roles diferentes. Para obtener más información, consulte la ayuda de **Cuentas de almacenamiento** o consulte [Creación de una cuenta de almacenamiento][Creación de una cuenta de almacenamiento].

-   Active Diagnósticos de Azure en sus roles de servicios en la nube.
    Para obtener más información, consulte [Habilitación de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure][1].

En el Portal de administración, puede agregar o modificar las cadenas de conexión de diagnósticos que Diagnósticos de Azure utiliza para tener acceso a las cuentas de almacenamiento que almacenan datos de supervisión detallada, y puede configurar el nivel de supervisión a detallado o mínimo. Dado que la supervisión detallada almacena los datos en una cuenta de almacenamiento, debe configurar las cadenas de conexión de diagnósticos antes de configurar la supervisión en un nivel detallado.

### Para configurar las cadenas de conexiones de diagnósticos de la supervisión detallada

1.  Copie una clave de acceso de almacenamiento para la cuenta de almacenamiento que va a utilizar para almacenar los datos de la supervisión detallada. En el [Portal de administración de Azure][Portal de administración de Azure], puede usar **Manage Keys** en la página **Cuentas de almacenamiento**. Para obtener más información, consulte [Administración de servicios en la nube][Administración de servicios en la nube] o la ayuda de la página **Cuentas de almacenamiento**.

2.  Abra **Cloud Services**. A continuación, para abrir el panel, haga clic en el nombre del servicio en la nube que desee configurar.

3.  Haga clic en **Production** o **Staging** para visualizar la implementación que desea configurar.

4.  Haga clic en **Configure**.

    Editará los parámetros de **supervisión** en la parte superior de la página **Configure**, que se muestra a continuación. Si no ha activado Diagnósticos de Azure para el servicio en la nube, la opción **Level** no está disponible. No puede cambiar la directiva de retención de datos. Los datos de la supervisión detallada de un servicio en la nube se mantienen almacenados durante 10 días.

    ![Opciones de supervisión][Opciones de supervisión]

5.  En **Diagnostics Connection Strings**, complete la cadena de conexión de diagnósticos para cada rol en el que desee tener una supervisión detallada.

    Las cadenas de conexión tienen el siguiente formato. (La muestra es para un servicio en la nube que utilice extremos predeterminados). Para actualizar una cadena de conexión, escriba un nombre de cuenta de almacenamiento y una clave de acceso de almacenamiento válidos para la cuenta de almacenamiento que desea utilizar.

    DefaultEndpointsProtocol=https;AccountName=StorageAccountName;AccountKey=StorageAccountKey

6.  Haga clic en **Save**.

Si va a activar la supervisión detallada, realice el siguiente procedimiento después de configurar las cadenas de conexión de diagnósticos en los roles del servicio.

### Para cambiar el nivel de supervisión a detallado o mínimo

1.  En el [Portal de administración][Portal de administración de Azure], abra la página **Configure** de la implementación del servicio en la nube.

2.  En **Level**, haga clic en **Verbose** o **Minimal**.

3.  Haga clic en **Save**.

Después de activar la supervisión detallada, debería empezar a ver los datos de supervisión en el Portal de administración en menos de una hora.

Los datos del contador de rendimiento y los datos de supervisión agregados se almacenan en la cuenta de almacenamiento, en tablas, en función del identificador de implementación de los roles.

## <span id="receivealerts"></span></a>Direccionamiento del alertas de las métricas de los servicios en la nube

Puede recibir alertas basadas en las métricas de supervisión de los servicios en la nube. En la página de **servicios de administración** del Portal de administración de Azure, puede crear una regla para desencadenar una alerta cuando la métrica seleccionada alcance el valor que haya especificado. Puede también elegir que se envíe un correo electrónico cuando se desencadene la alerta. Para obtener más información, consulte [Inserción de notificaciones de alerta y administración de reglas de alerta en Azure][Inserción de notificaciones de alerta y administración de reglas de alerta en Azure].

## <span id="addmetrics"></span></a>Direccionamiento del métricas en la tabla de métricas

1.  En el [Portal de administración][Portal de administración], abra la página de **supervisión** del servicio en la nube.

    La tabla de métricas muestra de forma predeterminada un subconjunto de las métricas disponibles. La ilustración muestra las métricas detalladas predeterminadas de un servicio en la nube, que están limitadas al contador de rendimiento Memoria/MBytes disponibles, con datos agregados en el nivel del rol. Utilice **Add Metrics** para seleccionar las métricas agregadas y de nivel del rol adicionales que desee supervisar en el Portal de administración.

    ![Visualización detallada][Visualización detallada]

2.  Para agregar métricas a la tabla de métricas:

    a. Haga clic en **Agregar métricas** para abrir **Elegir métricas**, como se muestra a continuación.
    La primera métrica disponible se expande para mostrar las opciones que hay disponibles. En cada métrica, la opción superior muestra los datos de supervisión agregados de todos los roles. Además, puede elegir los roles individuales de los que desee visualizar los datos.

    ![Agregar métricas][Agregar métricas]

    b. Para seleccionar las métricas que desea visualizar:

    -   Haga clic en la flecha abajo sobre la métrica para expandir las opciones de supervisión.
    -   Active la casilla de verificación de cada opción de supervisión que desee visualizar.

    Puede visualizar hasta 50 métricas en la tabla de métricas.

    <div class="dev-callout">

    **Consejo**
    En la supervisión detallada, la lista de métricas puede contener muchas métricas. Para mostrar una barra de desplazamiento, desplace el ratón sobre el lado derecho del cuadro de diálogo. Para filtrar la lista, haga clic en el icono de búsqueda y escriba en texto en el cuadro de búsqueda como se muestra a continuación.

    </div>

    ![Búsqueda de Agregar métricas][Búsqueda de Agregar métricas]

3.  Después de haber seleccionado las métricas, haga clic en la marca de verificación.

    Las métricas seleccionadas se agregan a la tabla de métricas como se muestra a continuación.

    ![supervisar métricas][supervisar métricas]

4.  Para eliminar una métrica de la tabla de métricas, haga clic en la métrica para seleccionarla y, a continuación, haga clic en **Delete Metric**. (Solo verá **Delete Metric** si ha seleccionado una métrica).

## <span id="customizechart"></span></a>Direccionamiento del gráfico de métricas

1.  En la tabla de métricas, seleccione las métricas que desee mostrar en el gráfico de métricas (un máximo de 6). Para seleccionar una métrica, haga clic en la casilla de verificación del lado izquierdo. Para quitar una métrica del gráfico de métricas, desmarque la casilla de verificación en la tabla de métricas.

    A medida que seleccione métricas en la tabla de métricas, estas se agregarán al gráfico de métricas. En un modo de visualización reducido, una lista desplegable **n more** contiene los encabezados de las métricas que no caben en la pantalla.

2.  Para alternar entre los valores relativos de visualización (solo el valor final de cada métrica) y los valores absolutos (se muestra el eje Y), seleccione Relative o Absolute en la parte superior del gráfico.

    ![Relative o Absolute][Relative o Absolute]

3.  Para cambiar el intervalo de tiempo que se muestra en el gráfico de métricas, seleccione 1 hora, 24 horas o 7 días en la parte superior del gráfico.

    ![Periodo de la pantalla de supervisión][Periodo de la pantalla de supervisión]

    En el panel del gráfico de métricas, el método de visualización de métricas es diferente. Hay un conjunto de métricas estándar disponible, y las métricas se agregan o se borran seleccionando el encabezado de la métrica.

### Para personalizar el gráfico de métricas en el panel

1.  Abra el panel del servicio en la nube.

2.  Agregue o borre las métricas del gráfico:

    -   Para mostrar una nueva métrica, active la casilla de verificación de la métrica en los encabezados del gráfico. En un modo de visualización reducido, haga clic en la flecha inferior sobre ***n*??metrics** para mostrar una métrica que el encabezado del gráfico no puede mostrar.

    -   Para eliminar una métrica que se muestra en el gráfico, desmarque la casilla de verificación en su encabezado.

3.  Alterne entre las pantallas **Relative** y **Absolute**.

4.  Elija 1 hora, 24 horas o 7 días para visualizar los datos correspondientes.

## <span id="accessverbose"></span></a>Direccionamiento del los datos de supervisión detallada fuera del Portal de administración

Los datos de la supervisión detallada se almacenan en tablas en las cuentas de almacenamiento que ha especificado para cada rol. Para cada implementación de servicios en la nube, se crean seis tablas para el rol. Se crean dos tablas en cada intervalo (5 minutos, 1 hora y 12 horas). Una de estas tablas almacena los agregados a nivel de rol y, la otra, los agregados de las instancias de rol.

Los nombres de tabla tienen el siguiente formato:

    WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

donde:

-   *deploymentID* es el GUID asignado a la implementación del servicio en la nube.

-   *aggregation\_interval* = 5M, 1H o 12H

-   agregados a nivel de rol = R

-   agregados de las instancias de rol = RI

Por ejemplo, las tablas siguientes almacenarían datos de supervisión detallados agregados en intervalos de 1 hora:

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)

  [Conceptos]: #concepts
  [Direccionamiento del la supervisión para los servicios en la nube]: #verbose
  [Direccionamiento del alertas de las métricas de los servicios en la nube]: #receivealerts
  [Direccionamiento del métricas en la tabla de métricas]: #addmetrics
  [Direccionamiento del gráfico de métricas]: #customizechart
  [Direccionamiento del los datos de supervisión detallada fuera del Portal de administración]: #accessverbose
  [Habilitación de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure]: http://azure.microsoft.com/es-es/documentation/articles/cloud-services-dotnet-diagnostics/
  [Creación de una cuenta de almacenamiento]: /es-es/manage/services/storage/how-to-create-a-storage-account/
  [1]: /es-es/documentation/articles/cloud-services-dotnet-diagnostics/
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Administración de servicios en la nube]: /es-es/documentation/articles/cloud-services-how-to-manage/
  [Opciones de supervisión]: ./media/cloud-services-how-to-monitor/CloudServices_MonitoringOptions.png
  [Inserción de notificaciones de alerta y administración de reglas de alerta en Azure]: http://go.microsoft.com/fwlink/?LinkId=309356
  [Portal de administración]: http://manage.windowsazure.com/
  [Visualización detallada]: ./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png
  [Agregar métricas]: ./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png
  [Búsqueda de Agregar métricas]: ./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png
  [supervisar métricas]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png
  [Relative o Absolute]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png
  [Periodo de la pantalla de supervisión]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png
