<properties pageTitle="Get Started with the Azure WebJobs SDK" metaKeywords="Azure tutorial, Azure WebJobs tutorial, Azure multi-tier tutorial, MVC tutorial, Azure blobs tutorial, Azure queues tutorial, Azure storage tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The frontend runs in a website, and the backend runs as a WebJob. The app uses Entity Framework, SQL Database, and Azure storage queues and blobs." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Get Started with the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Introducción al SDK de WebJobs de Azure

En este tutorial se presenta el SDK de WebJobs y se muestra cómo crear una aplicación MVC ASP.NET de niveles múltiples que use el SDK de WebJobs en un [sitio web de Azure][sitio web de Azure]. La aplicación usa [Base de datos SQL de Azure][Base de datos SQL de Azure], el [servicio Blob de Azure][servicio Blob de Azure] y el [servicio Cola de Azure][servicio Cola de Azure].

La aplicación de ejemplo es un tablón de anuncios publicitario. Los usuarios crean un anuncio introduciendo texto y cargando una imagen. Pueden ver una lista de anuncios con imágenes en miniatura y ver la imagen a tamaño completo cuando seleccionan un anuncio para ver los detalles. A continuación se muestra una captura de pantalla:

![Ad list][Ad list]

Puede [descargar el proyecto de Visual Studio][descargar el proyecto de Visual Studio] desde la Galería de código de MSDN.

## Tabla de contenido

El tutorial incluye las siguientes secciones:

-   [Requisitos previos][Requisitos previos]
-   [Temas que se abordarán][Temas que se abordarán]
-   [El SDK de WebJobs][El SDK de WebJobs]
-   [Arquitectura de la aplicación][Arquitectura de la aplicación]
-   [Configuración del entorno de desarrollo][Configuración del entorno de desarrollo]
-   [Generación, ejecución e implementación de la aplicación][Generación, ejecución e implementación de la aplicación]
-   [Creación de la aplicación desde cero][Creación de la aplicación desde cero]
-   [Revisión del código de la aplicación][Revisión del código de la aplicación]
-   [Solución de problemas][Solución de problemas]
-   [Pasos siguientes][Pasos siguientes]

## <span id="prerequisites"></span></a>Requisitos previos

En el tutorial se asume que sabe cómo trabajar con proyectos [ASP.NET MVC][ASP.NET MVC] o [Web Forms][Web Forms] en Visual Studio. La aplicación de ejemplo usa MVC, pero la mayoría del tutorial también se aplica a Web Forms.

Las instrucciones del tutorial funcionan con cualquiera de los productos siguientes:

-   Visual Studio 2013
-   Visual Studio 2013 Express para Web

Si no tiene uno de estos productos, Visual Studio 2013 Express para Web se instalará automáticamente al instalar el SDK de Azure.

[WACOM.INCLUDE [free-trial-note][free-trial-note]]

## <span id="learn"></span></a>Temas que se abordarán

En el tutorial se muestra cómo realizar las siguientes tareas:

-   Habilitar su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure.
-   Crear un proyecto de aplicación de consola que se implemente automáticamente como un WebJob de Azure cuando implemente el proyecto web asociado.
-   Probar un back-end del SDK de WebJobs en modo local en el equipo de desarrollo.
-   Publicar una aplicación con el back-end de WebJobs en un sitio web de Azure.
-   Cargar archivos y almacenarlos en el servicio Blob de Azure.
-   Usar el SDK de WebJobs de Azure para trabajar con colas y blobs de Almacenamiento de Azure.

## <span id="webjobssdk"></span></a>Introducción al SDK de WebJobs

[WebJobs][WebJobs] es una característica de Sitios web de Azure que permite ejecutar un programa o script en el mismo contexto que un sitio web. No es necesario usar el SDK de WebJobs para ejecutar un programa como un WebJob. El objetivo del SDK de WebJobs consiste en simplificar la tarea de escribir el código que funciona con las colas, blobs y tablas de Almacenamiento de Azure y con las colas del bus de servicio.

El SDK de WebJobs incluye los siguientes componentes:

-   **Paquetes NuGet**. Los paquetes NuGet que se agregan a un proyecto de Visual Studio proporcionan un marco de trabajo que usa el código para funcionar con el servicio de Almacenamiento de Azure o con las colas del bus de servicio.
-   **Panel**. Parte del SDK de WebJobs se incluye en Sitios web de Azure y ofrece una supervisión y un diagnóstico completos para los programas que escriba usando los paquetes NuGet. No es necesario escribir código para usar estas características de supervisión y diagnóstico.

Puede ejecutar un programa que use paquetes NuGet del SDK de WebJobs en cualquier lugar, no tiene por qué ejecutarse como un WebJob de Azure. No obstante, el panel solo está disponible como una extensión de un sitio web de Azure. Por ejemplo, puede ejecutar un programa que use el SDK de WebJobs en modo local en el equipo de desarrollo o en un rol de trabajo de Servicio en la nube de Azure. Posteriormente, puede obtener la información de supervisión mediante la configuración del panel del SDK de WebJobs de un sitio web de Azure a fin de usar la misma cuenta de almacenamiento que usa su programa del SDK de WebJobs.

En el momento en que se elaboró este tutorial, el SDK de WebJobs está disponible en la versión beta. No está listo para el uso de producción.

### Escenarios típicos

Estos son algunos de los escenarios típicos que puede controlar más fácilmente con el SDK de Azure WebJobs:

-   Procesamiento de imágenes u otros trabajos de uso intensivo de CPU. Una característica común de los sitios web es la capacidad para cargar imágenes y vídeos. A menudo, necesitamos manipular el contenido después de cargarlo, pero no queremos que el usuario esté esperando mientras realizamos este proceso.
-   Procesamiento de colas. Una de las formas más comunes de establecer comunicación entre un front-end web y un servicio back-end consiste en usar colas. Cuando el sitio web necesita que se realicen tareas en él, inserta un mensaje en una cola. Un servicio back-end extrae los mensajes de la cola y realiza el trabajo. Por ejemplo, puede usar colas con procesamiento de imágenes: después de que el usuario cargue una serie de archivos, coloque los nombres de archivo en un mensaje de cola para que el back-end los recoja y los procese. También puede usar las colas para mejorar la capacidad de respuesta del sitio. Por ejemplo, en lugar de escribir directamente en una base de datos SQL, escriba en una cola, indique al usuario que ha finalizado y deje que el servicio back-end controle el trabajo de bases de datos relacionales de alta latencia.
-   Agregación de fuentes RSS. Si tiene un sitio que mantiene una lista de fuentes RSS, puede extraer todos los artículos de las fuentes en un proceso en segundo plano.
-   Mantenimiento de archivos, como el agregado o limpieza de archivos de registro. Puede tener archivos de registro creados a través de varios sitios o para intervalos de tiempo diferentes que desee combinar con el fin de ejecutar trabajos de análisis en ellos. O puede que desee programar una tarea para limpiar archivos de registro antiguos que se ejecute semanalmente.
-   Otras tareas con una ejecución prolongada que desee ejecutar en un subproceso en segundo plano, como el envío de correos electrónicos.

Para evitar que un sitio web entre en modo de suspensión después de un período de inactividad, lo que interrumpiría una tarea con una ejecución prolongada en segundo plano, puede usar la característica [AlwaysOn][AlwaysOn] de Sitios web de Azure.

### Ejemplos de código

El código para controlar las tareas comunes que se realizan con Almacenamiento de Azure es muy sencillo. En una aplicación de consola, escriba los métodos para las tareas en segundo plano que desee ejecutar y represéntelas con atributos del SDK de WebJobs. El método `Main` permite crear un objeto `JobHost` que coordina las llamadas a los métodos que escriba. El marco de trabajo del SDK de WebJobs sabrá cuándo llamar a los métodos en función de los atributos del SDK de WebJobs que use en ellos. Por ejemplo:

        static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

El objeto `JobHost` es un contenedor de un conjunto de funciones en segundo plano. El objeto `JobHost` supervisa las funciones, inspecciona los eventos que las desencadenan y ejecuta las funciones cuando se producen los efectos desencadenantes. La llamada a un método `JobHost` permite indicar si desea ejecutar el proceso del contenedor en el subproceso actual o en un subproceso de segundo plano. En el ejemplo, el método `RunAndBlock` ejecuta el proceso de forma continua en el subproceso actual.

Dado que el método `ProcessQueueMessage` de este ejemplo contiene un atributo `QueueTrigger`, el desencadenador de esa función es la recepción de un nuevo mensaje de cola. El objeto `JobHost` inspecciona los mensajes de cola nuevos de la cola especificada ("colatrabajosweb" en este ejemplo) y cuando encuentra uno, llama a `ProcessQueueMessage`. El atributo `QueueTrigger` también notifica el marco de trabajo para enlazar el parámetro `inputText` al valor del mensaje de cola:

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;colatrabajosweb&quot;)]] string inputText, 
    [Blob(&quot;nombrecontenedor/nombreblob&quot;)]TextWriter writer)
</pre>

El marco de trabajo también enlaza un objeto `TextWriter` a un blob llamado "nombreblob" en un contenedor llamado "nombrecontenedor":

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;colatrabajosweb&quot;)]] string inputText, 
    [Blob(&quot;nombrecontenedor/nombreblob&quot;)]TextWriter writer)
</pre>

A continuación, la función usa estos parámetros para escribir el valor del mensaje de cola en el blob:

        writer.WriteLine(inputText);

Como puede observar, las características de desencadenamiento y enlace del SDK de WebJobs simplifican enormemente el código que se debe escribir para trabajar con objetos de Almacenamiento de Azure. El marco de trabajo del SDK de WebJobs se encarga de escribir el código de bajo nivel necesario para controlar el procesamiento de colas y de blobs, es decir, el marco de trabajo crea colas que no existen aún, abre la cola, lee sus mensajes, los elimina cuando se ha completado el procesamiento, crea contenedores de blobs que no existen aún, escribe en los blobs, etc.

El SDK de WebJobs ofrece diversas formas de trabajar con Almacenamiento de Azure. Por ejemplo, si el parámetro que representa con el atributo `QueueTrigger` es una matriz de bytes o un tipo personalizado, se deserializa automáticamente desde el JSON. Además, puede usar un atributo `BlobTrigger` para desencadenar un proceso cada vez que se cree un blob nuevo en su cuenta de Almacenamiento de Azure. (Tenga en cuenta que mientras que `QueueTrigger` encuentra nuevos mensajes de cola en tan solo unos segundos, `BlobTrigger` puede tardar hasta 20 minutos en detectar un blob nuevo. `BlobTrigger` busca blobs siempre que se inicia `JobHost` y, después, comprueba periódicamente los registros de Almacenamiento de Azure para detectar blobs nuevos.)

## <span id="contosoads"></span></a>Arquitectura de la aplicación

La aplicación de ejemplo usa el [patrón de trabajo centrado en colas][servicio Cola de Azure] para descargar el trabajo de uso intensivo de CPU de creación de miniaturas y pasarlo a un proceso back-end.

La aplicación almacena anuncios en una base de datos SQL, usando Entity Framework Code First para crear las tablas y acceder a los datos. Por cada anuncio, la base de datos almacena dos direcciones URL, una para la imagen a tamaño completo y otra para la miniatura.

![Ad table][Ad table]

Cuando un usuario carga una imagen, el sitio web front-end almacena dicha imagen en un [blob de Azure][servicio Blob de Azure] y la información del anuncio en la base de datos con una dirección URL que apunta al blob. Al mismo tiempo, escribe mensaje en una cola de Azure. Los procesos back-end que se ejecutan como un WebJob de Azure usan el SDK de WebJobs para sondear la cola en busca de mensajes nuevos. Cuando aparece un mensaje nuevo, el WebJob crea una miniatura para la imagen y actualiza el campo de base de datos de la dirección URL de la miniatura para ese anuncio. A continuación puede ver un diagrama que muestra cómo interactúan las partes de la aplicación:

![Contoso Ads architecture][Contoso Ads architecture]

### Arquitectura alternativa

Los WebJobs se ejecutan en el contexto de un sitio web y no se pueden escalar por separado. Por ejemplo, si tiene una instancia de sitio web estándar, solo puede haber una instancia del proceso en segundo plano en ejecución. Esta instancia usa algunos de los recursos del servidor (CPU, memoria, etc.) que, de lo contrario, estarían disponibles para proporcionar contenido web.

Si el tráfico varía en función de la hora o el día de la semana, y si el procesamiento de back-end que deba realizar puede esperar, es posible programar los WebJobs para que se ejecuten en horas de menos tráfico. Si la carga sigue siendo demasiado elevada para esa solución, considere otros entornos para el programa de back-end, como los siguientes:

-   Ejecute el programa como un WebJob en un sitio web independiente destinado a ese fin. A continuación, puede escalar el sitio web back-end con independencia del sitio web front-end.
-   Ejecute el programa en un rol de trabajo de Servicio en la nube de Azure. Si elige esta opción, puede ejecutar el front-end en un rol de trabajo de Servicio en una nube o en un sitio web.

En este tutorial se muestra cómo ejecutar el front-end en un sitio web y el back-end como un WebJob en el mismo sitio web. Para obtener información sobre cómo elegir el mejor entorno para su escenario, consulte [Comparación entre sitios web, servicios en la nube y máquinas virtuales de Azure][Comparación entre sitios web, servicios en la nube y máquinas virtuales de Azure].

[WACOM.INCLUDE [install-sdk-2013-only][install-sdk-2013-only]]

## <span id="storage"></span></a>Creación de una cuenta de Almacenamiento de Azure

Una cuenta de almacenamiento de Azure proporciona recursos para almacenar datos de cola y blob en la nube. El SDK de WebJobs también usa esta cuenta para almacenar datos para el panel.

En una aplicación real, normalmente crea cuentas independientes para los datos de aplicación frente a los datos de registro, y cuentas diferentes para datos de prueba frente a datos de producción. Para este tutorial, usará solamente una cuenta.

1.  En el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Nuevo** - **Servicios de datos** - **Almacenamiento** - **Creación rápida**.

2.  En el cuadro **URL**, escriba un prefijo de dirección URL.

    Este prefijo y el texto que verá debajo de la casilla formarán la dirección URL exclusiva para la cuenta de almacenamiento. Si el prefijo escrito ya lo usa otra persona, elija un prefijo diferente.

    En la imagen situada al final de esta sección, se crea una cuenta de almacenamiento con la dirección URL `contosoads.core.windows.net`.

3.  Establezca la opción de la lista desplegable **Región** en la región más cercana.

    Esta configuración especifica el centro de datos de Azure que va a almacenar la cuenta de almacenamiento. En este tutorial, la opción que elija no tendrá mucha repercusión, pero en un entorno de producción se recomienda que el servidor web y la cuenta de almacenamiento estén en la misma región a fin de minimizar la latencia y los cargos por concepto de salida de los datos. El sitio web (que creará posteriormente) debe estar lo más próximo posible a los exploradores que obtengan acceso a su sitio con el fin de minimizar la latencia.

4.  Establezca la lista desplegable **Replicación** en **Localmente redundante**.

    Cuando se habilita la replicación geográfica para una cuenta de almacenamiento, el contenido almacenado se replica en un centro de datos secundario para habilitar la conmutación por error en caso de que se produzca un desastre importante en la ubicación principal. La replicación geográfica puede suponer costes adicionales. Lo normal es que no quiera pagar por el servicio de replicación geográfica para las cuentas de prueba y desarrollo. Para obtener más información, consulte [Administración de cuentas de almacenamiento][Administración de cuentas de almacenamiento].

5.  Haga clic en **Crear cuenta de almacenamiento**.

    ![New storage account][New storage account]

## <span id="download"></span></a>Descarga de la aplicación

1.  Descargue y descomprima la [solución completa][descargar el proyecto de Visual Studio] (información en inglés).

2.  Inicie Visual Studio.

3.  En el menú **Archivo**, seleccione **Abrir** \> **Proyecto/Solución**, diríjase a la ubicación donde descargó la solución y, a continuación, abra el archivo de la solución.

4.  Presione CTRL+MAYÚS+B para compilar la solución.

    De forma predeterminada, Visual Studio restaura automáticamente el contenido del paquete NuGet, que no se incluyó en el archivo *.zip*. Si los paquetes no se restauran, instálelos manualmente mediante el cuadro de diálogo **Administrar paquetes NuGet para la solución** y haciendo clic en el botón **Restaurar** situado en la parte superior derecha.

5.  En el **Explorador de soluciones**, asegúrese de que **ContosoAdsWeb** se encuentra seleccionado como proyecto de inicio.

## <span id="configurestorage"></span></a>Configuración de la aplicación para usar una cuenta de almacenamiento

1.  Abra el archivo *Web.config* de la aplicación en el proyecto ContosoAdsWeb.

    El archivo contiene una cadena de conexión SQL y una cadena de conexión de almacenamiento de Azure para trabajar con blobs y colas.

    La cadena de conexión SQL apunta a una base de datos [SQL Server Express LocalDB][SQL Server Express LocalDB]

    La cadena de conexión de almacenamiento incluye marcadores donde se inserta el nombre de la cuenta de almacenamiento y la clave de acceso en los pasos siguientes.

    ``` prettyprint
    <connectionStrings>
      <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
      <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[nombrecuenta];AccountKey=[claveacceso]"/>
    </connectionStrings>
    ```

    La cadena de conexión de almacenamiento se denomina AzureWebJobsStorage porque ese es el nombre que el SDK de WebJobs usa de forma predeterminada. Aquí se usa el mismo nombre, de modo que solo tiene que establecer el valor de una cadena de conexión en el entorno de Azure.

2.  En el [Portal de administración de Azure][1], seleccione la cuenta de almacenamiento y haga clic en **Administrar claves de acceso** en la parte inferior de la página.

    ![Botón Administrar claves de acceso][Botón Administrar claves de acceso]

    ![Cuadro de diálogo Administrar claves de acceso][Cuadro de diálogo Administrar claves de acceso]

3.  Reemplace *[nombrecuenta]* en la cadena de conexión por el valor del cuadro **Nombre de cuenta de almacenamiento**.

4.  Reemplace *[claveacceso]* en la cadena de conexión por el valor del cuadro **Clave de acceso primaria**.

5.  Abra el archivo *App.config* del proyecto ContosoAdsWebJob.

    Este archivo tiene dos cadenas de conexión de almacenamiento, una para los datos de aplicación y otra para registro. Para este tutorial, usará la misma cuenta para ambas. Las cadenas de conexión tienen los mismos marcadores que explicamos anteriormente.

    ``` prettyprint
    <configuration><connectionStrings> <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[nombrecuenta];AccountKey=[claveacceso]"/> <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[nombrecuenta];AccountKey=[claveacceso]"/> <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/></connectionStrings> <startup> <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" /></startup></configuration>
    ```

    </p>
    De forma predeterminada, el SDK de WebJobs busca cadenas de conexión llamadas AzureWebJobsStorage y AzureWebJobsDashboard. Como opción alternativa, puede almacenar la cadena de conexión que desee y pasarla de forma explícita al objeto `JobHost`.

6.  Reemplace todas las apariciones de *[nombrecuenta]* y *[claveacceso]* por los valores de la cuenta de almacenamiento, al igual que hizo con el proyecto web. (También puede copiar la cadena de conexión completada del archivo *Web.config* a ambas cadenas de conexión del archivo *App.config*.)

7.  Guarde los cambios.

## <span id="run"></span></a>Ejecución de la aplicación de forma local

1.  Para iniciar el front-end web de la aplicación, presione CTRL+F5.

    El explorador predeterminado se abre en la página principal. (El proyecto web se ejecuta porque lo ha creado en el proyecto de inicio.)

    ![Página principal de Contoso Ads][Página principal de Contoso Ads]

2.  Para iniciar el back-end del WebJob de la aplicación, haga clic con el botón secundario en el proyecto ContosoAdsWebJob en el **Explorador de soluciones** y, después, haga clic en **Depurar** \> **Iniciar nueva instancia**.

    Se abre una ventana de la aplicación de consola que muestra un mensaje indicando que el host del trabajo se ha iniciado y se está ejecutando.

    ![Ventana de la aplicación de consola con el back-end en ejecución][Ventana de la aplicación de consola con el back-end en ejecución]

3.  En el explorador, haga clic en la opción para **crear un anuncio**.

4.  Escriba algunos datos de prueba y seleccione una imagen para cargar. Después haga clic en **Create**.

    ![Create page][Create page]

    La aplicación irá a la página Index, pero no mostrará una miniatura para el nuevo anuncio porque ese procesamiento todavía no se ha llevado a cabo.

    Transcurridos unos instantes, aparece un mensaje de registro en la ventana de la aplicación de consola que muestra que se ha recibido y se ha procesado un mensaje de cola.

    ![Ventana de la aplicación de consola que muestra que se ha procesado un mensaje de cola][Ventana de la aplicación de consola que muestra que se ha procesado un mensaje de cola]

5.  Después de que aparezcan los mensajes de registro en la ventana de la aplicación de consola, actualice la página del índice para ver la miniatura.

    ![Página de índice][Ad list]

6.  Haga clic en el vínculo **Details** correspondiente a su anuncio para ver la imagen a tamaño completo.

    ![Details page][Details page]

Aunque haya ejecutado la aplicación en el equipo local y use una base de datos SQL Server ubicada en su equipo, la aplicación trabaja con colas y blobs en la nube. En la siguiente sección ejecutará la aplicación en la nube mediante una base de datos en la nube, así como blobs y colas en la nube.

## <span id="runincloud"></span></a>Ejecución de la aplicación en la nube

Llevará a cabo los pasos siguientes para ejecutar la aplicación en la nube:

-   Implementación en un sitio web de Azure. Visual Studio creará automáticamente una instancia de Base de datos SQL y un sitio web de Azure nuevos.
-   Configuración del sitio web para que use una cuenta de almacenamiento y una base de datos SQL de Azure.

Después de crear algunos anuncios mientras ejecuta la aplicación en la nube, consulte el panel del SDK de WebJobs para ver las completas características de supervisión que ofrece.

### Implementación en un sitio web de Azure

1.  Cierre el explorador y la ventana de la aplicación de consola.

2.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto ContosoAdsWeb y, a continuación, en **Publicar**.

3.  En el paso **Perfil** del asistente para **publicación web**, haga clic en **Sitios web de Microsoft Azure**.

    ![Seleccionar el destino de aplicación del sitio web de Azure][Seleccionar el destino de aplicación del sitio web de Azure]

4.  En el cuadro **Seleccionar sitio web existente**, haga clic en **Iniciar sesión**.

    ![Haga clic en Iniciar sesión][Haga clic en Iniciar sesión]

5.  Después de iniciar sesión, haga clic en Nuevo.

    ![Haga clic en Nuevo][Haga clic en Nuevo]

6.  En el cuadro de diálogo **Crear sitio en Microsoft Azure**, especifique un nombre único en el cuadro **Nombre del sitio**.

    La dirección URL completa consta de lo que escriba aquí más .azurewebsites.net (como se muestra junto al cuadro de texto **Nombre del sitio**). Por ejemplo, si el nombre del sitio es ContosoAds, la URL será ContosoAds.azurewebsites.net.

7.  En la lista desplegable **Región**, elija la misma región que seleccionó para la cuenta de almacenamiento.

    Esta configuración especifica el centro de datos de Azure en el que se ejecutará el sitio web. Al mantener el sitio web y la cuenta de almacenamiento en el mismo centro de datos, se minimiza la latencia y los cargos por concepto de salida de los datos.

8.  En la lista desplegable **Base de datos**, elija **Crear nuevo servidor**.

    Asimismo, si su suscripción ya tiene un servidor, puede seleccionarlo en la lista desplegable.

9.  Complete los campos **Nombre de usuario de base de datos** y **Contraseña de base de datos** con los datos de un administrador.

    Si seleccionó **New SQL Database server**, no debe escribir un nombre y una contraseña existentes en estos campos, sino definir unos nuevos que volverá a utilizar más adelante para obtener acceso a la base de datos. Si seleccionó un servidor creado anteriormente, se le pedirá la contraseña de la cuenta de usuario administrativa que ya creó.

10. Haga clic en **Crear**.

    ![Cuadro de diálogo Crear sitio en Microsoft Azure][Cuadro de diálogo Crear sitio en Microsoft Azure]

    Visual Studio crea la solución, el proyecto web, el sitio web de Azure y la instancia de Base de datos SQL de Azure.

11. En el paso **Conexión** del asistente para **publicación web**, haga clic en **Siguiente**.

    ![Paso Conexión][Paso Conexión]

12. En el paso **Configuración**, desactive la opción **Usar esta cadena de conexión en tiempo de ejecución** y haga clic en **Siguiente**.

    ![Settings step][Settings step]

    No es necesario usar el cuadro de diálogo de publicación para establecer la cadena de conexión SQL porque puede hacerlo posteriormente en el entorno de Azure.

    Puede omitir las advertencias de esta página.

    -   Generalmente, la cuenta de almacenamiento que se usa cuando se ejecuta en Azure es distinta a la que se usa en el modo de ejecución local, pero para este tutorial usaremos la misma en ambos entornos. Así pues, no es necesario cambiar la cadena de conexión AzureWebJobsStorage. Aunque deseara usar una cuenta de almacenamiento diferente en la nube, no es necesario transformar la cadena de conexión porque la aplicación usará una configuración del entorno de Azure cuando se ejecute en Azure. Veremos este procedimiento posteriormente en el tutorial.

    -   En este tutorial, no se realizarán cambios en el modelo de datos usado para la base de datosContosoAdsContext, por lo que no es necesario usar migraciones de Entity Framework Code First para la implementación. Code First creará automáticamente una base de datos nueva la primera vez que la aplicación intente tener acceso a datos SQL.

    Para este tutorial, los valores predeterminados de las opciones de **Opciones de publicación de archivos** son correctos.

13. En el paso **Vista previa**, haga clic en **Iniciar vista previa**.

    ![Haga clic en Iniciar vista previa][Haga clic en Iniciar vista previa]

    Puede omitir la advertencia acerca de que no se van a publicar bases de datos. Entity Framework Code First creará la base de datos, no es necesario publicarla.

    En la ventana de vista previa se muestran los archivos binarios y de configuración del proyecto WebJob que se copiarán en la carpeta *app\_data\\jobs\\continuous* del sitio web.

    ![Archivos de WebJobs en la ventana de vista previa][Archivos de WebJobs en la ventana de vista previa]

14. Haga clic en **Publicar**.

    Visual Studio implementa la aplicación y abre la URL de la página principal en el explorador.

    No podrá usar el sitio hasta que establezca las cadenas de conexión en el entorno de Azure en la siguiente sección. Aparecerá una página de error o la página principal, en función de las opciones de creación del sitio y de la base de datos elegidas anteriormente.

### Configuración del sitio web para que use una cuenta de almacenamiento y una base de datos SQL de Azure.

Un procedimiento recomendado de seguridad consiste en [evitar insertar información confidencial como cadenas de conexión en archivos que se almacenen en repositorios de código fuente][evitar insertar información confidencial como cadenas de conexión en archivos que se almacenen en repositorios de código fuente]. Azure proporciona una forma de hacer esto: puede establecer la cadena de conexión y otros valores de configuración en el entorno de Azure para que las API de configuración de ASP.NET recojan esos valores automáticamente cuando la aplicación se ejecute en Azure. En esta sección, configurará valores de cadenas de conexión en Azure.

1.  En el explorador, vaya al portal de administración de Azure y seleccione el sitio web en el que implementó la aplicación Contoso Ads.

2.  Haga clic en la pestaña **Configurar** y desplácese hasta la sección **Cadenas de conexión**.

3.  Cambie el nombre de la cadena de conexión DefaultConnection a ContosoAdsContext.

    Azure creó esta cadena de conexión automáticamente cuando creó el sitio con una base de datos asociada, por lo que ya tiene el valor de cadena de conexión correcto. Solo cambiará el nombre de lo que el código está buscando.

4.  Agregue dos cadenas de conexión nuevas, llamadas AzureWebJobsStorage y AzureWebJobsDashboard. Establezca el tipo en Custom y establezca el valor de cadena de conexión en el mismo valor que usó anteriormente para los archivos *Web.config* y *App.config*. (Asegúrese de incluir la cadena de conexión completa, no solo la clave de acceso, sin comillas.)

    El SDK de WebJobs usa estas cadenas de conexión, una para los datos de la aplicación y otra para el registro. Como vimos anteriormente, el código del front-end web también usa la cadena de datos de la aplicación.

5.  Haga clic en **Save**.

    ![Cadenas de conexión en el portal de administración][Cadenas de conexión en el portal de administración]

6.  Haga clic en la pestaña **Panel** y, a continuación, en **Reiniciar**.

    El WebJob se inicia automáticamente al publicar, pero se detiene cuando se realiza un cambio en la configuración. Para reiniciarlo, puede reiniciar el sitio o el WebJob. Generalmente, se recomienda reiniciar el sitio después de cambiar la configuración.

7.  Actualice la ventana del explorador que contiene la URL del sitio en la barra de direcciones.

    Se abre la página principal.

8.  Cree un anuncio, tal y como hizo cuando ejecutó la aplicación de forma local.

    Se muestra la página del índice sin ninguna miniatura al principio.

9.  Actualice la página después de unos segundos y la miniatura aparece.

    Si la miniatura no aparece, puede que el WebJob no se haya iniciado automáticamente. En ese caso, vaya a la pestaña WebJobs

### Visualización del panel del SDK de WebJobs

1.  En el portal de administración de Azure, seleccione su sitio web.

2.  Haga clic en la pestaña **WebJobs**.

3.  Haga clic en la URL de la columna de registros correspondiente al WebJob.

    ![Pestaña WebJobs][Pestaña WebJobs]
    Se abre una nueva pestaña de explorador con el panel del SDK de WebJobs. El panel indica que el WebJob se está ejecutando y muestra una lista de funciones en el código que el SDK de WebJobs ha desencadenado.

4.  Haga clic en una de las funciones para ver más detalles sobre su ejecución

    ![Panel del SDK de WebJobs][Panel del SDK de WebJobs]

    ![Panel del SDK de WebJobs][2]

    El botón de **función de reproducción** de esta página sirve para que el marco de trabajo del SDK de WebJobs llame de nuevo a la función, y le permite cambiar los datos que se pasan primero a la función.

> [WACOM.NOTE] Cuando finalice las pruebas, elimine el sitio web y la instancia de Base de datos SQL. El sitio web es gratuito, pero la instancia de Base de datos SQL y la cuenta de almacenamiento tienen un coste (mínimo dado su pequeño tamaño). Asimismo, si deja el sitio ejecutándose, cualquiera que encuentre su dirección URL puede crear y ver anuncios. En el Portal de administración de Azure, vaya a la pestaña **Panel** correspondiente a su servicio en la nube y haga clic en el botón **Eliminar** que se encuentra en la parte inferior de la página. A continuación, puede activar una casilla para eliminar la instancia de la Base de datos SQL al mismo tiempo. Si lo que desea es evitar temporalmente que otros accedan al sitio, haga clic en **Detener**. En ese caso, se seguirán acumulando cargos para la cuenta de almacenamiento y la base de datos SQL. Puede seguir un procedimiento similar para eliminar la base de datos SQL y la cuenta de almacenamiento cuando ya no las necesite.

## <span id="create"></span></a>Creación de la aplicación desde cero

En esta sección realizará las siguientes tareas:

-   Crear una solución de Visual Studio con un proyecto web.
-   Agregar un proyecto de biblioteca de clases para la capa de acceso a datos que comparten el front-end y el back-end.
-   Agregar un proyecto de aplicación de consola al back-end con la implementación de WebJobs habilitada.
-   Agregar paquetes NuGet.
-   Establecer preferencias del proyecto.
-   Copiar los archivos de configuración y de código de la aplicación desde la aplicación descargada con la que trabajó en la sección anterior de este tutorial.
-   Revisar las partes del código que funcionan con blobs y colas de Azure y el SDK de WebJobs.

### Crear una solución de Visual Studio con un proyecto web

1.  En Visual Studio, elija **Nuevo** \> **Proyecto** en el menú **Archivo**.

2.  En el cuadro de diálogo **Nuevo proyecto**, elija **C#** \> **Web** \> **Aplicación web ASP.NET**.

3.  Asigne el nombre ContosoAdsWeb al proyecto, llame ContosoAdsWebJobsSDK a la solución (cambie el nombre de la solución si lo coloca en la misma carpeta que la solución descargada) y, a continuación, haga clic en **Aceptar**.

    ![Nuevo proyecto][Nuevo proyecto]

4.  En el cuadro de diálogo **Nuevo proyecto ASP.NET**, elija la plantilla MVC y desactive la casilla **Host en la nube** en **Microsoft Azure**.

    Al seleccionar **Host en la nube** se permite que Visual Studio cree automáticamente un nuevo sitio web de Azure y Base de datos SQL. Como ya los creó anteriormente, no es necesario hacerlo mientras crea el proyecto. Si desea crearlos, active la casilla de verificación. A continuación, puede configurar el nuevo sitio web y base de datos SQL al igual que lo hizo anteriormente cuando implementó la aplicación.

5.  Haga clic en **Cambiar autenticación**.

    ![Change Authentication][Change Authentication]

6.  En el cuadro de diálogo **Cambiar autenticación**, elija **Sin autenticación** y haga clic en **Aceptar**.

    ![Sin autenticación][Sin autenticación]

7.  En el cuadro de diálogo **Nuevo proyecto ASP.NET**, haga clic en **Aceptar**.

    Visual Studio crea la solución y el proyecto web.

8.  En el **Explorador de soluciones**, haga clic con el botón secundario en la solución (no en el proyecto) y elija **Agregar** \> **Nuevo proyecto**.

9.  En el cuadro de diálogo **Agregar nuevo proyecto**, elija **Visual C#** \> **Escritorio de Windows** \> plantilla de **biblioteca de clases**.

10. Asigne un nombre al proyecto *ContosoAdsCommon* y haga clic en **Aceptar**.

    Este proyecto incluirá el contexto de Entity Framework y el modelo de datos que usarán el front-end y el back-end. Como alternativa, podría definir las clases relacionadas con EF en el proyecto web y hacer referencia a ese proyecto desde el proyecto WebJob. Pero en ese caso, el proyecto WebJob tendría que hacer referencia a ensamblados web que no necesita.

### Agregar un proyecto de aplicación de consola con la implementación de WebJobs habilitada

1.  Haga clic con el botón secundario en el proyecto web (no en la solución ni en el proyecto de biblioteca de clases) y, a continuación, haga clic en **Agregar** \> **Nuevo proyecto WebJob de Azure**.

    ![Selección del menú Nuevo proyecto WebJob de Azure][Selección del menú Nuevo proyecto WebJob de Azure]

2.  En el cuadro de diálogo **Agregar WebJob de Azure**, especifique ContosoAdsWebJob como **Nombre de proyecto** y **Nombre de WebJob**. Deje la opción **Modo de ejecución de WebJob** establecida en **Ejecutar continuamente**.

3.  Haga clic en **OK**.

    Visual Studio crea una aplicación de consola que se configura para implementarse como un WebJob cada vez que implemente el proyecto web. Para ello, realiza las siguientes tareas después de crear el proyecto:

    -   Agrega un archivo *webjob-publish-settings.json* a la carpeta de propiedades del proyecto WebJob.
    -   Agrega un archivo *webjobs-list.json* a la carpeta de propiedades del proyecto web.
    -   Instala el paquete NuGet Microsoft.Web.WebJobs.Publish en el proyecto WebJob.

    Para obtener más información acerca de estos cambios, consulte [Cómo implementar WebJobs mediante Visual Studio][Cómo implementar WebJobs mediante Visual Studio].

### Agregar paquetes NuGet

En primer lugar, instale el SDK de WebJobs en el proyecto de WebJob.

1.  Abra el cuadro de diálogo **Administrar paquetes de NuGet** correspondiente a la solución.

2.  En el panel izquierdo, seleccione **En línea**.

3.  Cambie **Solo estable** a **Incluir versión preliminar**.

4.  Busque el paquete NuGet *Microsoft.Azure.WebJobs* e instálelo en el proyecto ContosoAdsWebJob.

    ![Buscar el paquete del SDK de WebJobs][Buscar el paquete del SDK de WebJobs]

    ![Instalar el paquete del SDK de WebJobs solo en el proyecto de WebJob][Instalar el paquete del SDK de WebJobs solo en el proyecto de WebJob]

    De este modo también se instalan los paquetes dependientes, incluido otro paquete del SDK de WebJobs: *Microsoft.Jobs.Core*. (El otro paquete del SDK de WebJobs se usa por separado solo al crear funciones de usuario en un archivo DLL independiente; en este tutorial, el código se escribe en la aplicación de la consola.) Necesita la biblioteca del cliente de almacenamiento (SCL) de Azure para trabajar con colas y blobs en el proyecto web y en el proyecto WebJob.

El paquete NuGet de la biblioteca del cliente de almacenamiento (SCL) de Azure se instala automáticamente en el proyecto WebJob como una dependencia del SDK de WebJobs. También se requiere para trabajar con blobs y colas en el proyecto web.

1.  En el panel izquierdo, seleccione **Paquetes instalados**.

2.  Busque el paquete de *almacenamiento de Azure* y haga clic en **Administrar**.

3.  En el cuadro **Seleccionar proyectos**, active la casilla **ContosoAdsWeb** y, a continuación, haga clic en **Aceptar**.

Los tres proyectos usan Entity Framework para trabajar con los datos de Base de datos SQL.

1.  En el panel izquierdo, seleccione **En línea**.

2.  Cambie **Incluir versión preliminar** a **Solo estable**.

3.  Busque el paquete de NuGet *EntityFramework* e instálelo en los otros tres proyectos.

### Establecer preferencias del proyecto

Los proyectos web y WebJob funcionan con la base de datos SQL, por lo que ambos hacen referencia al proyecto ContosoAdsCommon.

1.  En el proyecto ContosoAdsWeb, establezca una referencia al proyecto ContosoAdsCommon. (Haga clic con el botón secundario en el proyecto ContosoAdsWeb y después en **Agregar** \> **Referencia**. En el cuadro de diálogo **Administrador de referencias**, seleccione **Solución** \> **Proyectos** \> **ContosoAdsCommon** y, después, haga clic en **Aceptar**.)

2.  En el proyecto ContosoAdsWebJob, establezca una referencia al proyecto ContosAdsCommon.

El proyecto WebJob necesita referencias para trabajar con imágenes y para tener acceso a las cadenas de conexión.

1.  En el proyecto ContosoAdsWebJob, establezca una referencia a `System.Drawing` y a `System.Configuration`.

### Agregar archivos de configuración y de código

En este tutorial no se explica cómo [crear controladores y vistas MVC usando scaffolding][ASP.NET MVC], cómo [escribir código Entity Framework que funcione con bases de datos de SQL Server][escribir código Entity Framework que funcione con bases de datos de SQL Server] ni los [fundamentos de la programación asincrónica en ASP.NET 4.5][fundamentos de la programación asincrónica en ASP.NET 4.5]. Por ello, lo único que queda pendiente consiste en copiar archivos de configuración y de código desde la solución descargada a la nueva. Después de realizar este proceso, se muestran y explican las partes clave del código en las siguientes secciones.

Para agregar archivos a un proyecto o carpeta, haga clic con el botón secundario en dicho proyecto o carpeta y después en **Agregar** \> **Elemento existente**. Seleccione los archivos que desee y haga clic en **Agregar**. Si se le pregunta si desea reemplazar los archivos existentes, haga clic en **Sí**.

1.  En el proyecto ContosoAdsCommon, elimine el archivo *Class1.cs* y agregue en su lugar los siguientes archivos desde el proyecto descargado.

    -   *Ad.cs*
    -   *ContosoAdscontext.cs*
    -   *BlobInformation.cs*

2.  En el proyecto ContosoAdsWeb, agregue los siguientes archivos desde el proyecto descargado.

    -   *Web.config*
    -   *Global.asax.cs*
    -   En la carpeta *Controllers*: *AdController.cs*
    -   En la carpeta *Views\\Shared*: archivo *\_Layout.cshtml*.
    -   En la carpeta *Views\\Home*: *Index.cshtml*.
    -   En la carpeta *Views\\Ad* (cree la carpeta primero): cinco archivos *.cshtml*.

3.  En el proyecto ContosoAdsWebJob, agregue los siguientes archivos desde el proyecto descargado.

    -   *App.config* (cambie el filtro de tipo de archivo a **Todos los archivos**)
    -   *Program.cs*

Ahora puede generar, ejecutar e implementar la aplicación como se indicó anteriormente en el tutorial. No obstante, antes de hacerlo, detenga el WebJob que esté aún ejecutándose en el primer sitio web en el que lo implementó. De lo contrario, ese WebJob procesará los mensajes de cola creados localmente o mediante la aplicación que se ejecute en un sitio web nuevo, ya que todos usan la misma cuenta de almacenamiento.

## <span id="code"></span></a>Revisión del código de la aplicación

En la siguiente sección se explica el código relacionado para trabajar con el SDK de WebJobs y los blobs y las colas de Azure. Para consultar el código específico del SDK de WebJobs, consulte la [sección Program.cs][sección Program.cs].

### ContosoAdsCommon - Ad.cs

El archivo Ad.cs define una enumeración para categorías de anuncios y una clase de entidad POCO para información de anuncios.

        public enum Category
        {
            Cars,
            [Display(Name="Real Estate")]
            RealEstate,
            [Display(Name = "Free Stuff")]
            FreeStuff
        }

        public class Ad
        {
            public int AdId { get; set; }

            [StringLength(100)]
            public string Title { get; set; }

            public int Price { get; set; }

            [StringLength(1000)]
            [DataType(DataType.MultilineText)]
            public string Description { get; set; }

            [StringLength(1000)]
            [DisplayName("Full-size Image")]
            public string ImageURL { get; set; }

            [StringLength(1000)]
            [DisplayName("Thumbnail")]
            public string ThumbnailURL { get; set; }

            [DataType(DataType.Date)]
            [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
            public DateTime PostedDate { get; set; }

            public Category? Category { get; set; }
            [StringLength(12)]
            public string Phone { get; set; }
        }

### ContosoAdsCommon - ContosoAdsContext.cs

La clase ContosoAdsContext especifica que la clase Ad se usa en una colección DbSet, cuyo Entity Framework se almacenará en una base de datos SQL.

        public class ContosoAdsContext : DbContext
        {
            public ContosoAdsContext() : base("name=ContosoAdsContext")
            {
            }
            public ContosoAdsContext(string connString)
                : base(connString)
            {
            }
            public System.Data.Entity.DbSet<Ad> Ads { get; set; }
        }

La clase tiene dos constructores. El primero de ellos lo usa el proyecto web y especifica el nombre de una cadena de conexión que se almacena en el archivo Web.config o en el entorno en tiempo de ejecución de Azure. El segundo constructor le permite pasar la cadena de conexión real. El proyecto WebJob necesita este elemento porque no tiene ningún archivo Web.config. Anteriormente vio dónde se almacenaba esta cadena de conexión y posteriormente verá cómo el código recupera dicha cadena de conexión cuando crea instancias de la clase DbContext.

### ContosoAdsCommon - BlobInformation.cs

La clase `BlobInformation` se usa para almacenar información de almacenamiento acerca de un blob de imagen en un mensaje de cola.

        public class BlobInformation
        {
            public Uri BlobUri { get; set; }
            
            public string BlobName
            {
                get
                {
                    return BlobUri.Segments[BlobUri.Segments.Length - 1];
                }
            }
            public string BlobNameWithoutExtension
            {
                get
                {
                    return Path.GetFileNameWithoutExtension(BlobName);
                }
            }
            public int AdId { get; set; }
        }

### ContosoAdsWeb - Global.asax.cs

El código llamado desde el método `Application_Start` crea un contenedor de blob *images* y una cola *images* si todavía no existen. Esto garantiza que siempre que comience a usar una cuenta de almacenamiento nueva, la cola y el contenedor del blob necesarios se crearán automáticamente.

El código obtiene acceso a la cuenta de almacenamiento mediante la cadena de conexión de almacenamiento del archivo *Web.config* o el entorno en tiempo de ejecución de Azure.

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

Después obtiene una referencia al contenedor de blobs *images*, crea el contenedor si todavía no existe y establece permisos de acceso en el nuevo contenedor. De forma predeterminada, los nuevos contenedores solamente permiten a los clientes con credenciales de cuenta de almacenamiento acceder a blobs. El sitio web necesita que los blobs sean públicos para que pueda mostrar imágenes usando direcciones URL que apunten a los blobs de imagen.

        var blobClient = storageAccount.CreateCloudBlobClient();
        var imagesBlobContainer = blobClient.GetContainerReference("images");
        if (imagesBlobContainer.CreateIfNotExists())
        {
            imagesBlobContainer.SetPermissions(
                new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
        }

El código similar obtiene una referencia a la cola *blobnamerequest* y crea una nueva cola. En este caso no es necesario cambios de permiso. En la sección [ResolveBlobName][ResolveBlobName] que se incluye más adelante en el tutorial se explica por qué la cola que escribe la aplicación web se usa solo para obtener los nombres de blob y no para generar miniaturas.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
        imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - \_Layout.cshtml

El archivo *_Layout.cshtml* establece el nombre de aplicación en el encabezado y pie de página y crea una entrada de menú "Ads".

### ContosoAdsWeb - Views\\Home\\Index.cshtml

El archivo *Views\\Home\\Index.cshtml* muestra vínculos de categoría en la página de inicio. Los vínculos pasan el valor entero de la enumeración `Category` en una variable de cadena de consulta a la página Ads Index.

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

En el archivo *AdController.cs*, el constructor llama al método `InitializeStorage` para crear objetos de biblioteca de cliente de almacenamiento de Azure que proporcionan una API para trabajar con blobs y colas.

Después, el código obtiene una referencia al contenedor de blobs *images* tal y como vio anteriormente en *Global.asax.cs*. Mientras hace eso, establece una [directiva de reintentos][directiva de reintentos] apropiada para una aplicación web. La directiva de reintentos de retroceso exponencial predeterminada podría bloquear la aplicación web durante más de un minuto en reintentos repetitivos para un error transitorio. La directiva de intentos especificada aquí espera 3 segundos después de cada reintento hasta 3 reintentos.

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

El código similar obtiene una referencia a la cola *images*.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

La mayoría del código del controlador es típico para trabajar con un modelo de datos de usando Entity Framework usando una clase DbContext. Una excepción es el método `Create` HttpPost, que carga un archivo y lo guarda en el almacenamiento de blobs. El enlazador de modelos proporciona un objeto [HttpPostedFileBase][HttpPostedFileBase] al método.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(
            [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
            HttpPostedFileBase imageFile)

Si el usuario seleccionó un archivo para cargar, el código carga el archivo, lo guarda en un blob y actualiza el registro de base de datos Ad con una dirección URL que adjunta al blob.

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            blob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = blob.Uri.ToString();
        }

El código que no se carga se encuentra en el método `UploadAndSaveBlobAsync`. Crea un nombre GUID para el blob, carga y guarda el archivo y devuelve una referencia al blob guardado.

        private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
        {
            string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
            CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
            using (var fileStream = imageFile.InputStream)
            {
                await imageBlob.UploadFromStreamAsync(fileStream);
            }
            return imageBlob;
        }

Después de que el método HttpPost `Create` carga un blob y actualiza la base de datos, crea un mensaje de cola para informar a ese proceso back-end que una imagen está preparada para su conversión en una miniatura.

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

El código para el método HttpPost `Edit` es similar con la excepción de que si el usuario selecciona un archivo de imagen nuevo, cualquier blob que ya exista para este anuncio se debe eliminar.

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            await DeleteAdBlobsAsync(ad);
            imageBlob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = imageBlob.Uri.ToString();
        }

A continuación se muestra el código que elimina blobs cuando elimina un anuncio:

        private async Task DeleteAdBlobsAsync(Ad ad)
        {
            if (!string.IsNullOrWhiteSpace(ad.ImageURL))
            {
                Uri blobUri = new Uri(ad.ImageURL);
                await DeleteAdBlobAsync(blobUri);
            }
            if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
            {
                Uri blobUri = new Uri(ad.ThumbnailURL);
                await DeleteAdBlobAsync(blobUri);
            }
        }
        private static async Task DeleteAdBlobAsync(Uri blobUri)
        {
            string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
            CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
            await blobToDelete.DeleteAsync();
        }

### ContosoAdsWeb - Views\\Ad\\Index.cshtml y Details.cshtml

El archivo *Index.cshtml* muestra miniaturas con otros datos de anuncio:

        <img  src="@Html.Raw(item.ThumbnailURL)" />

El archivo *Details.cshtml* muestra la imagen a tamaño completo:

        <img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\\Ad\\Create.cshtml y Edit.cshtml

Los archivos *Create.cshtml* y *Edit.cshtml* especifican codificación de formularios que permite al controlador obtener el objeto `HttpPostedFileBase`.

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Un elemento `<input>` indica al explorador que proporcione un cuadro de dialogo de selección.

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <span id="programcs"></span></a>ContosoAdsWebJob - Program.cs - Métodos Main e Initialize

Cuando el WebJob se inicia, el método `Main` llama a `Initialize` para crear una instancia del contexto de base de datos de Entity Framework. A continuación, llama al método `JobHost.RunAndBlock` del SDK de WebJobs para iniciar la ejecución de subproceso único de las funciones desencadenadas en el subproceso actual.

        static void Main(string[] args)
        {
            Initialize();
        
            JobHost host = new JobHost();
            host.RunAndBlock();
        }
        
        private static void Initialize()
        {
            db = new ContosoAdsContext();
        }

### <span id="generatethumbnail"></span></a>ContosoAdsWebJob - Program.cs - Método GenerateThumbnail

El SDK de WebJobs llama a este método cuando se recibe un mensaje de cola. El método crea una miniatura y coloca la URL de la miniatura en la base de datos.

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }
        
            var id = blobInfo.AdId;
            Ad ad = db.Ads.Find(id);
            if (ad == null)
            {
                throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
            }
            ad.ThumbnailURL = outputBlob.Uri.ToString();
            db.SaveChanges();
        }

-   El atributo `QueueTrigger` ordena al SDK de WebJobs que llame a este método cuando se recibe un mensaje nuevo en la cola thumbnailrequest.

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    El objeto `BlobInformation` del mensaje de cola se deserializa automáticamente en el parámetro `blobInfo`. Cuando el método se completa, el mensaje de cola se elimina. Si se produce un error en el método antes de completarse, el mensaje de cola no se elimina, sino que expira transcurridos 10 minutos y el mensaje se libera para que se pueda recoger y procesarse de nuevo. Esta secuencia no se repite de manera indefinida cuando un mensaje provoca siempre una excepción. Después de cinco intentos erróneos al procesar un mensaje, este se mueve a una cola llamada {nombrecola}-poison. Se puede configurar el número máximo de intentos.

-   Los dos atributos `Blob` proporcionan objetos que están enlazados a blobs: uno al blob de imagen existente y otro al blob de la miniatura nueva que el método crea.

        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    Los nombres de blob proceden de las propiedades del objeto `BlobInformation` recibido en el mensaje de cola (`BlobName` y `BlobNameWithoutExtension`). Para obtener todas las funcionalidades de la biblioteca del cliente de almacenamiento, use la clase `CloudBlockBlob` para trabajar con blobs. Si desea reutilizar el código que escribió para trabajar con objetos `Stream`, use la clase `Stream`.

> [WACOM.NOTE]
>
> -   Si su sitio web se ejecuta en varias máquinas virtuales, el programa se ejecutará en cada una de las máquinas y estas, a su vez, esperarán a los desencadenadores e intentarán ejecutar funciones. En algunos escenarios puede ocurrir que algunas funciones procesen los mismos datos dos veces, por lo que las funciones deben ser idempotentes (escritas de tal forma que al llamarlas repetidamente con los mismos datos de entrada no se generen resultados duplicados).
> -   Para obtener más información acerca de cómo implementar un cierre estable, consulte el [lanzamiento de la versión beta del SDK de WebJobs 0.3.0][lanzamiento de la versión beta del SDK de WebJobs 0.3.0].
> -   El código del método `ConvertImageToThumbnailJPG` (no se muestra) usa clases del espacio de nombres `System.Drawing` por simplicidad. Sin embargo, las clases de este espacio de nombres se diseñaron para usarse con Windows Forms. No se admiten para usarse en un servicio de Windows o ASP.NET.

### Comparación entre WebJobs y un rol de trabajo de Servicio en la nube

Si compara la cantidad de código en el método `GenerateThumbnails` de esta aplicación de ejemplo con el código del rol de trabajo de la [versión de Servicio en la nube de la aplicación][versión de Servicio en la nube de la aplicación], observará el volumen de trabajo que el SDK de WebJobs realiza por usted. Las ventajas son mucho mayores de lo que parece, porque el código de aplicación de ejemplo de Servicio en la nube no realiza todas las acciones (como el control de mensajes dudosos) que se ejecutarían en una aplicación de producción, pero con el SDK de WebJobs sí es posible.

La versión del Servicio en la nube de esta aplicación tiene un método `ProcessQueueMessage` que realiza las siguientes tareas:

-   Obtener el identificador de registro de base de datos del mensaje de cola.
-   Leer la base de datos para obtener la URL del blob.
-   Convertir una imagen en una miniatura y guardarla en un blob nuevo.
-   Actualizar el registro de bases de datos mediante la incorporación de la URL del blob de miniatura.

En la versión del Servicio en la nube de la aplicación, el identificador de registro es la única información del mensaje de cola, y el proceso en segundo plano obtiene la URL de imagen de la base de datos. En la versión del SDK de WebJobs de la aplicación, el mensaje de cola incluye la URL de la imagen para que se pueda proporcionar a los atributos `Blob`. Si el mensaje de cola no incluye la URL del blob, tendría que escribir código en el método para obtenerla y, a continuación, escribir código para enlazar objetos a los blobs de entrada y de salida en lugar de que el SDK de WebJobs haga ese trabajo por usted.

### Uso del SDK de WebJobs en un rol de trabajo

Según hemos visto, no es necesario que un programa que use el SDK de WebJobs se ejecute en Azure en un WebJob. Se puede ejecutar de forma local o en un rol de trabajo. No obstante, solo puede tener acceso al panel del SDK de WebJobs a través de un sitio web de Azure. Para usar el panel, debe conectar el sitio web a la cuenta de almacenamiento que esté usando. Para ello, establezca la cadena de conexión AzureWebJobsDashboard en la pestaña Configurar del portal de administración. A continuación, podrá consultar el panel mediante el uso de la URL https://{nombresitioweb}.scm.azurewebsites.net/azurejobs/\#/functions. Para obtener más información, consulte [cómo obtener un panel para desarrollo local con el SDK de WebJobs][cómo obtener un panel para desarrollo local con el SDK de WebJobs], pero tenga en cuenta que muestra un nombre de cadena de conexión antiguo.

## Pasos siguientes

En este tutorial, ha visto una aplicación sencilla de niveles múltiples que usa el SDK de WebJobs para el procesamiento de back-end. La aplicación se ha diseñado de forma que resulte sencilla intencionadamente para un tutorial de inicio. Por ejemplo, no implementa la [inserción de dependencias][inserción de dependencias] o el [repositorio y la unidad de patrones de trabajo][repositorio y la unidad de patrones de trabajo], no [usa una interfaz para registro][usa una interfaz para registro], no usa [migraciones de EF Code First][migraciones de EF Code First] para administrar cambios de modelos de datos o la [resistencia de conexiones EF][resistencia de conexiones EF] para administrar errores de red transitorios, etc.

Para obtener más ejemplos que muestran cómo usar el SDK de WebJobs en otros escenarios, consulte la carpeta [AzureWebJobs][AzureWebJobs] del proyecto CodePlex de ASP.NET.

Cuando un WebJob se ejecuta en un sitio web, puede solucionar los problemas mediante la ejecución en modo de depuración de forma remota para los sitios web. Para obtener más información, consulte [Solución de problemas de Sitios web Azure en Visual Studio][Solución de problemas de Sitios web Azure en Visual Studio]. Debe asociar manualmente al proceso del WebJob; consulte las instrucciones de Visual Studio 2012 del tutorial para obtener información sobre cómo asociar a un proceso.

Para obtener más información, consulte [Recursos recomendados de trabajos web de Azure][Recursos recomendados de trabajos web de Azure].

  [sitio web de Azure]: /es-es/documentation/articles/fundamentals-application-models/#WebSites
  [Base de datos SQL de Azure]: http://msdn.microsoft.com/library/azure/ee336279
  [servicio Blob de Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [servicio Cola de Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Ad list]: ./media/websites-dotnet-webjobs-sdk-get-started/list.png
  [descargar el proyecto de Visual Studio]: http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb
  [Requisitos previos]: #prerequisites
  [Temas que se abordarán]: #learn
  [El SDK de WebJobs]: #webjobssdk
  [Arquitectura de la aplicación]: #contosoads
  [Configuración del entorno de desarrollo]: #setupdevenv
  [Generación, ejecución e implementación de la aplicación]: #storage
  [Creación de la aplicación desde cero]: #create
  [Revisión del código de la aplicación]: #code
  [Solución de problemas]: #troubleshoot
  [Pasos siguientes]: #next-steps
  [ASP.NET MVC]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Web Forms]: http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview
  [free-trial-note]: ../includes/free-trial-note.md
  [WebJobs]: /es-es/documentation/articles/web-sites-create-web-jobs/
  [AlwaysOn]: http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx
  [Ad table]: ./media/websites-dotnet-webjobs-sdk-get-started/adtable.png
  [Contoso Ads architecture]: ./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png
  [Comparación entre sitios web, servicios en la nube y máquinas virtuales de Azure]: /es-es/documentation/articles/choose-web-site-cloud-service-vm/
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Administración de cuentas de almacenamiento]: /es-es/documentation/articles/storage-manage-storage-account/
  [New storage account]: ./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png
  [SQL Server Express LocalDB]: http://msdn.microsoft.com/es-es/library/hh510202.aspx
  [1]: http://manage.windowsazure.com/
  [Botón Administrar claves de acceso]: ./media/websites-dotnet-webjobs-sdk-get-started/mak.png
  [Cuadro de diálogo Administrar claves de acceso]: ./media/websites-dotnet-webjobs-sdk-get-started/cpak.png
  [Página principal de Contoso Ads]: ./media/websites-dotnet-webjobs-sdk-get-started/home.png
  [Ventana de la aplicación de consola con el back-end en ejecución]: ./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png
  [Create page]: ./media/websites-dotnet-webjobs-sdk-get-started/create.png
  [Ventana de la aplicación de consola que muestra que se ha procesado un mensaje de cola]: ./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png
  [Details page]: ./media/websites-dotnet-webjobs-sdk-get-started/details.png
  [Seleccionar el destino de aplicación del sitio web de Azure]: ./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png
  [Haga clic en Iniciar sesión]: ./media/websites-dotnet-webjobs-sdk-get-started/signin.png
  [Haga clic en Nuevo]: ./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png
  [Cuadro de diálogo Crear sitio en Microsoft Azure]: ./media/websites-dotnet-webjobs-sdk-get-started/newdb.png
  [Paso Conexión]: ./media/websites-dotnet-webjobs-sdk-get-started/connstep.png
  [Settings step]: ./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png
  [Haga clic en Iniciar vista previa]: ./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png
  [Archivos de WebJobs en la ventana de vista previa]: ./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png
  [evitar insertar información confidencial como cadenas de conexión en archivos que se almacenen en repositorios de código fuente]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets
  [Cadenas de conexión en el portal de administración]: ./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png
  [Pestaña WebJobs]: ./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png
  [Panel del SDK de WebJobs]: ./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png
  [2]: ./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png
  [Nuevo proyecto]: ./media/websites-dotnet-webjobs-sdk-get-started/newproject.png
  [Change Authentication]: ./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png
  [Sin autenticación]: ./media/websites-dotnet-webjobs-sdk-get-started/noauth.png
  [Selección del menú Nuevo proyecto WebJob de Azure]: ./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png
  [Cómo implementar WebJobs mediante Visual Studio]: /es-es/documentation/articles/websites-dotnet-deploy-webjobs/
  [Buscar el paquete del SDK de WebJobs]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg.png
  [Instalar el paquete del SDK de WebJobs solo en el proyecto de WebJob]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg2.png
  [escribir código Entity Framework que funcione con bases de datos de SQL Server]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc
  [fundamentos de la programación asincrónica en ASP.NET 4.5]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [sección Program.cs]: #programcs
  [ResolveBlobName]: #resolveblobname
  [directiva de reintentos]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling
  [HttpPostedFileBase]: http://msdn.microsoft.com/es-es/library/system.web.httppostedfilebase.aspx
  [lanzamiento de la versión beta del SDK de WebJobs 0.3.0]: http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/
  [versión de Servicio en la nube de la aplicación]: /es-es/documentation/articles/cloud-services-dotnet-get-started/
  [https://{nombresitioweb}.scm.azurewebsites.net/azurejobs/\#/functions]: https://{websitename}.scm.azurewebsites.net/azurejobs/#/functions
  [cómo obtener un panel para desarrollo local con el SDK de WebJobs]: http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx
  [inserción de dependencias]: http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection
  [repositorio y la unidad de patrones de trabajo]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo
  [usa una interfaz para registro]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log
  [migraciones de EF Code First]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
  [resistencia de conexiones EF]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
  [AzureWebJobs]: http://aspnet.codeplex.com/SourceControl/latest#Samples/AzureWebJobs/ReadMe.txt
  [Solución de problemas de Sitios web Azure en Visual Studio]: /es-es/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
  [Recursos recomendados de trabajos web de Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
