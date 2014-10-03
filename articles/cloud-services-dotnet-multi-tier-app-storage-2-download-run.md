<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra"></tags>

# Configuración e implementación de la aplicación Servicio de correo electrónico de Azure: tutorial 2 de 5

Este es el segundo tutorial de la serie de cinco que muestra cómo crear e implementar la aplicación de ejemplo de Servicio de correo electrónico de Azure. Para obtener información sobre la serie de tutoriales y la aplicación, consulte el [primer tutorial de la serie][].

En este tutorial, aprenderá a realizar las siguientes tareas:

-   Configuración de su equipo para desarrollar contenido de Azure mediante la instalación del SDK de Azure
-   Configuración y prueba de la aplicación Servicio de correo electrónico de Azure en su máquina local
-   Publicación de la aplicación en Azure
-   Visualización y edición de tablas, colas y blobs de Azure mediante el Explorador de servidores de Visual Studio.
-   Configuración del seguimiento y visualización de los datos de seguimiento
-   Escalado de la aplicación aumentando el número de instancias de rol de trabajo

### Secciones del tutorial

-   [Configuración del entorno de desarrollo][]
-   [Descarga y ejecución de la solución completa][]
-   [Visualización del almacenamiento del desarrollo en Visual Studio](#StorageExpVS)
-   [Creación de una cuenta de almacenamiento de Azure][]
-   [Creación de un servicio en la nube][]
-   [Configuración de la aplicación para el almacenamiento de Azure][]
-   [Configuración de la aplicación para usar SendGrid][]
-   [Implementación de la aplicación en Azure][]
-   [Aumento de nivel de la aplicación de ensayo a producción][]
-   [Configuración y visualización de datos de seguimiento][]
-   [Incorporación de otra instancia de rol de trabajo para gestionar una carga aumentada][]

[WACOM.INCLUDE [install-sdk-2013-only][]]

## <a name="downloadcnfg"></a><span class="short-header">Descarga y ejecución</span>Descarga y ejecución de la solución completa

1.  Descargue y descomprima la [solución completa][] (información en inglés).

2.  Inicie Visual Studio.

3.  En el menú **Archivo**, seleccione **Abrir proyecto**, diríjase a la ubicación donde descargó la solución y, a continuación, abra el archivo de la solución.

4.  Presione CTRL+MAYÚS+B para compilar la solución.

    De forma predeterminada, Visual Studio restaura automáticamente el contenido del paquete NuGet, que no se incluyó en el archivo *.zip*. Si los paquetes no se restauran, instálelos manualmente mediante el cuadro de diálogo **Administrar paquetes NuGet para la solución** y haciendo clic en el botón **Restaurar** situado en la parte superior derecha.

5.  En el **Explorador de soluciones**, asegúrese de que **AzureEmailService** se encuentra seleccionado como proyecto de inicio.

6.  Presione CTRL+F5 para ejecutar la aplicación.

    Aparecerá la página principal de la aplicación en el explorador.

7.  Haga clic en **Listas de correo** en la barra de menú.

    (Las capturas de pantalla muestran el estilo de página web de las plantillas de proyecto de Visual Studio 2012 pero el contenido es el mismo para Visual Studio 2013.)

    ![Ejecución de la aplicación][]

8.  Haga clic en **Crear nuevo**.

9.  Especifique algunos datos de prueba y, a continuación, haga clic en **Crear**.

    ![Ejecución de la aplicación][1]

10. Cree un par de entradas de lista de correo más.

    ![Página de índice de listas de correo][]

11. Haga clic en **Suscriptores** y, a continuación, agregue algunos suscriptores. Establezca **Comprobado** en `true`.

    ![Página de índice de suscriptores][]

12. Prepárese para agregar mensajes creando un archivo *.txt* que contenga el cuerpo de un correo electrónico que desee enviar. A continuación, cree un archivo *.htm* que contenga el mismo texto pero con contenido HTML (por ejemplo, ponga algunas palabras del mensaje en negrita o cursiva). Usará estos archivos en el paso siguiente.

13. Haga clic en **Mensajes** y, a continuación, agregue algunos mensajes. Seleccione los archivos que creó en el paso anterior. No cambie la fecha programada, que es de forma predeterminada de una semana en el futuro. La aplicación no puede enviar mensajes hasta que configure SendGrid.

    ![página de creación de mensajes][]

    ![página de índice de mensajes][]

    Los datos que ha especificado y visualizado son administrados por el emulador de almacenamiento de Azure. El emulador de almacenamiento usa una base de datos LocalDB de SQL Server Express para emular la forma en la que Almacenamiento de Azure funciona en la nube. La aplicación usa el emulador de almacenamiento porque es lo que se decidió en la configuración del proyecto en el momento de su descarga. Esta configuración se almacena en los archivos *.cscfg* en el proyecto **AzureEmailService**. El archivo *ServiceConfiguration.Local.cscfg* determina lo que se usa cuando se ejecuta la aplicación localmente en Visual Studio y el archivo *ServiceConfiguration.Cloud.cscfg* determina lo que se usa cuando implementa la aplicación en la nube. Más adelante verá cómo configurar la aplicación para usar la cuenta de Almacenamiento de Azure.

14. Cierre el explorador.

## <a name="StorageExpVS"></a>Visualización del almacenamiento de desarrollo en Visual Studio

El explorador **Almacenamiento de Azure** en el **Explorador de servidores** proporciona una forma práctica de trabajar directamente con recursos de Almacenamiento de Azure.

1.  En el menú **View** en Visual Studio, seleccione **Explorador de servidores**.

2.  Expanda el nodo **Azure**, luego el nodo **Almacenamiento** y, por último, el nodo **(Desarrollo)** que se encuentra bajo el nodo **Almacenamiento de Azure**.

    Si todavía no ha iniciado sesión en Azure en Visual Studio, se le pedirán las credenciales de Azure. Escriba las credenciales de la cuenta que tiene la suscripción en la que desea ejecutar el servicio en la nube.

3.  Expanda **Tablas** para ver las tablas que creó en los pasos anteriores.

    ![Explorador de servidores][]

4.  Haga doble clic en la tabla **MailingList**.

    ![Explorador de almacenamiento de VS][]

    Observe cómo la ventana muestra los distintos esquemas de la tabla. Las entidades `MailingList` tienen la propiedad `Description` y `FromEmailAddress`, y las entidades `Subscriber` tienen la propiedad `Verified` (más `SubscriberGUID` que no se muestra porque la imagen no es lo suficientemente ancha). La tabla cuenta con columnas para todas las propiedades y, si una fila de la tabla determinada es para una entidad que no dispone de una propiedad determinada, la celda aparece en blanco.

Otra herramienta que puede usar para trabajar con recursos de Almacenamiento de Azure es el [Explorador de almacenamiento de Azure][].

## <a name="createWASA"></a>Creación de una cuenta de Almacenamiento de Azure

Cuando ejecuta la aplicación de ejemplo en Visual Studio, puede obtener acceso a tablas, colas y blobs en el emulador de almacenamiento de Azure o en una cuenta de Almacenamiento de Azure en la nube. En esta sección del tutorial, creará la cuenta de almacenamiento de Azure que configurará en Visual Studio para usarla más tarde en el tutorial.

1.  En el explorador, abra el [Portal de administración de Azure][].

2.  En el [Portal de administración de Azure][], haga clic en **Almacenamiento** y, a continuación, en **Nuevo**.

	![Nuevo almacenamiento][]

1.  Haga clic en **Quick Create**.

	![Creación rápida][]

1.  En el cuadro de entrada de la dirección URL, especifique un prefijo de la dirección URL.

    Este prefijo y el texto que verá debajo de la casilla formarán la dirección URL exclusiva para la cuenta de almacenamiento. Si alguien ya ha usado el prefijo establecido, verá un mensaje que le indica que ya está en uso ("El nombre de almacenamiento ya está en uso") sobre el cuadro de texto y tendrá que seleccionar un prefijo distinto.

2.  Establezca la región en el área en el que desee implementar la aplicación.

3.  Establezca el cuadro desplegable **Replicación** en **Localmente redindante**.

    Cuando se habilita la replicación geográfica para una cuenta de almacenamiento, el contenido almacenado se replica en una ubicación secundaria para habilitar la conmutación por error en caso de un desastre importante en la ubicación principal. La replicación geográfica puede suponer costes adicionales. Lo normal es que no quiera pagar por el servicio de replicación geográfica para las cuentas de prueba y desarrollo. Para obtener más información, consulte [Administración de cuentas de almacenamiento][].

4.  Haga clic en **Crear cuenta de almacenamiento**.

    En la imagen, se crea una cuenta de almacenamiento con la dirección URL `aestest3.core.windows.net`.

    ![creación de almacenamiento con prefijo de la dirección URL][]

    Este paso puede tardar varios minutos en completarse. Mientras espera, puede repetir estos pasos y crear una cuenta de almacenamiento de producción. A veces es recomendable disponer de una cuenta de almacenamiento de prueba para el desarrollo local y otra para el proceso de prueba en Azure, y una cuenta de almacenamiento de producción.

5.  Haga clic en la cuenta de prueba que creó en el paso anterior y, a continuación, haga clic en el icono **Administrar claves de acceso**.

    ![Administrar claves][]

    ![GUID de las claves][]

    Visual Studio configurará cadenas de conexión automáticamente con una de estas claves cuando se seleccione la cuenta de almacenamiento. También puede actualizar las cadenas de conexión manualmente.

    Existen dos claves, por lo que puede cambiar periódicamente la clave que usa sin que se interrumpa el servicio de una aplicación activa. Vuelva a generar la clave que no esté usando y, a continuación, podrá cambiar la cadena de conexión en la aplicación para usar la clave regenerada. Si existe solo una clave, la aplicación perderá la conectividad con la cuenta de almacenamiento cuando vuelva a generar la clave. Las claves que se muestran en la imagen ya no son válidas porque se volvieron a generar después de la captura de la imagen.

## <a name="createcloudsvc"></a><span class="short-header">Creación de un servicio en la nube</span>Creación de un servicio en la nube

1.  En el [Portal de administración de Azure][], haga clic en **Servicios en la nube** y, a continuación, en **Nuevo**.

    ![Nube rápida][]

2.  Haga clic en **Quick Create**.

3.  En el cuadro de entrada de la dirección URL, especifique un prefijo de la dirección URL.

    Como ocurre con la dirección URL de almacenamiento, esta dirección URL tiene que ser exclusiva y se mostrará un mensaje de error si alguien ya está usando el prefijo que seleccionó.

4.  Establezca la región en el área en el que desee implementar la aplicación.

    Debe crear el servicio en la nube en la misma región en la que creó la cuenta de almacenamiento. Cuando la cuenta de almacenamiento y el servicio en la nube se encuentren en centros de datos diferentes (distintas regiones), la latencia aumentará y se le cobrará el ancho de banda no perteneciente al centro de datos. El ancho de banda del centro de datos es gratuito.

    Los grupos de afinidad de Azure proporcionan un mecanismo para minimizar la distancia entre los recursos de un centro de datos, lo que puede reducir la latencia. Este tutorial no usa grupos de afinidad. Para obtener información, consulte [Crear un grupo de afinidad en el Portal de administración][].

5.  Haga clic en **Crear servicio en la nube**.

    En la siguiente imagen, se crea un servicio en la nube con la dirección URL aescloud.cloudapp.net.

    ![creación de almacenamiento con prefijo de la dirección URL][2]

## <a name="conf4azureStorage"></a><span class="short-header">Uso de la cuenta de almacenamiento</span>Configuración de la aplicación para usar la cuenta de almacenamiento de Azure

A continuación, verá cómo configurar la aplicación de manera que use la cuenta de almacenamiento de Azure cuando se ejecute en Visual Studio en lugar del almacenamiento de desarrollo.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **MvcWebRole** debajo de **Roles** en el proyecto **AzureEmailService** y, a continuación, haga clic en **Propiedades**.

    ![Clic con el botón secundario en Propiedades][]

2.  En la ventana **MvcWebRole [rol]**, haga clic en la pestaña **Configuración**.

3.  En el cuadro desplegable **Configuración de servicio**, seleccione **Local**.

4.  Seleccione la entrada **StorageConnectionString** y verá un botón con unos puntos suspensivos (**...**) en el extremo derecho de la línea. Haga clic en el botón con los puntos suspensivos para abrir el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

    ![Clic con el botón secundario en Propiedades][3]

5.  En el cuadro de diálogo **Crear cadena de conexión de almacenamiento**, haga clic en **Su suscripción** y elija los datos que desee en **Suscripción** y **Nombre de cuenta**.

    ![Clic con el botón secundario en Propiedades][4]

6.  Siga el mismo procedimiento que usó para la cadena de conexión `StorageConnectionString` para establecer la cadena de conexión `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

7.  Siga el mismo procedimiento que usó para las dos cadenas de conexión para el rol MvcWebRole para configurar las cadenas de conexión para los roles WorkerRoleA y WorkerRoleB.

8.  Abra el archivo **ServiceConfiguration.Local.cscfg** que se encuentra en el proyecto **AzureEmailService**.

    En el elemento `Role` de `MvcWebRole`, verá un elemento `ConfigurationSettings` que tiene la configuración que actualizó usando la UI de Visual Studio.

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    En los elementos `Role` para los dos roles de trabajo verá las dos mismas cadenas de conexión.

    Puede editar estos archivos directamente en lugar de usar la ventana **[Rol]** de Visual Studio. Para obtener más información sobre los archivos de configuración, consulte [Configurar un proyecto de Azure][].

### Prueba de la aplicación configurada para usar la cuenta de almacenamiento

1.  Presione CTRL+F5 para ejecutar la aplicación. Especifique algún dato haciendo clic en los vínculos **Listas de correo**, **Suscriptores** y **Mensajes** como hizo anteriormente en este tutorial.

2.  En Visual Studio, abra el **Explorador de servidores**.

3.  Expanda el nodo **Almacenamiento** bajo el nodo **Azure** y después expanda el nodo de la cuenta de almacenamiento que configuró para usar la aplicación.

4.  Expanda **Tablas** y haga doble clic en la tabla `MailingList` para ver los datos introducidos en las páginas **Listas de correo** y **Subcriptor** de la aplicación.

### Pasos opcionales para deshabilitar el inicio automático del emulador de almacenamiento de Azure

Si no usa el emulador de almacenamiento, puede disminuir el tiempo de inicio del proyecto y usar menos recursos locales deshabilitando el inicio automático para el emulador de almacenamiento de Azure.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto en la nube **AzureEmailService** y seleccione **Propiedades**.

    ![Selección de las propiedades del proyecto en la nube][]

2.  Seleccione la pestaña **Desarrollo**.

3.  Establezca **Iniciar emulador de almacenamiento de Azure** en **False**.

    ![Deshabilitación del inicio automático del emulador de almacenamiento][]

    **Nota**: solo debe establecer la opción en false si no va a usar el emulador de almacenamiento.

    Esta ventana también proporciona una forma de cambiar el archivo **Configuración de servicio** que se usó cuando ejecutó localmente la aplicación desde **Local** a **Cloud** (desde *ServiceConfiguration.Local.cscfg* a *ServiceConfiguration.Cloud.cscfg*).

4.  En la bandeja del sistema de Windows, haga clic con el botón secundario en el icono del emulador de proceso y haga clic en **Apagar emulador de almacenamiento**.

    ![ASE][]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>Configuración de la aplicación para usar SendGrid

La aplicación de ejemplo usa SendGrid para enviar correos electrónicos. Para enviar correos electrónicos mediante SendGrid, tiene que configurar una cuenta de SendGrid y, a continuación, actualizar un archivo de configuración con las credenciales de SendGrid.

<div class="note"><p><strong>Nota:</strong> si no quiere o no puede usar SendGrid, puede sustituirlo f&aacute;cilmente por su propio servicio de correo electr&oacute;nico. El c&oacute;digo que usa SendGrid se a&iacute;sla en dos m&eacute;todos en el rol de trabajo B. [Tutorial 5][tut5] explica lo que tiene que cambiar para implementar un m&eacute;todo distinto de env&iacute;o de correos electr&oacute;nicos. Si desea hacerlo, puede omitir este procedimiento y continuar con este tutorial; todo lo dem&aacute;s en la aplicaci&oacute;n funcionar&aacute; (las p&aacute;ginas web, la programaci&oacute;n de correo electr&oacute;nico, etc.) excepto para el env&iacute;o real de correos electr&oacute;nicos.</p></div>

### Creación de una cuenta de SendGrid

1.  Siga las instrucciones en [Envío de correos electrónicos con SendGrid y Azure][] para registrarse y conseguir una cuenta gratuita.

### Actualización de las credenciales de SendGrid en las propiedades de rol de trabajo

Anteriormente en el tutorial, cuando estableció las credenciales de la cuenta de almacenamiento para el rol web y los dos roles de trabajo, es posible que se diera cuenta de que el rol de trabajo B tenía tres valores que no aparecían en el rol web o en el rol de trabajo A. Puede usar la misma interfaz de usuario ahora para configurar esos tres valores (seleccione **Cloud** en la lista desplegable **Configuración de servicio**).

Los siguientes pasos muestran un método alternativo para el establecimiento de propiedades mediante la edición del archivo de configuración.

1.  Edite el archivo *ServiceConfiguration.Cloud.cscfg* en el proyecto `AzureEmailService` e introduzca los valores del nombre de usuario y la contraseña SendGrid que obtuvo en el paso anterior en el elemento `WorkerRoleB` que tiene esta configuración. El siguiente código muestra el elemento WorkerRoleB.

    ![SendGridSettings][]

2.  También existe una configuración de AzureMailServiceURL. Establezca este valor en la dirección URL que seleccionó cuando creó el servicio en la nube de Azure, por ejemplo: "<http://aescloud.cloudapp.net>".

Si actualiza el archivo de configuración en la nube, está configurando los valores que se usarán cuando se ejecute la aplicación en la nube. Si quería que la aplicación enviara correos electrónicos mientras se ejecutaba localmente, también tendría que actualizar el archivo *ServiceConfiguration.Local.cscfg*.

## <a name="deployAz"></a><span class="short-header">Implementación de Azure</span>Implementación de la aplicación en Azure

Para implementar la aplicación, puede crear un paquete en Visual Studio y cargarlo usando el Portal de administración de Azure o realizar la publicación directamente desde Visual Studio. En este tutorial usará el método de publicación.

Publicará primero la aplicación en el entorno de ensayo y después aumentará su nivel pasando a producción.

### Implementación de restricciones de IP

Cuando realice la implementación de ensayo, la aplicación será accesible públicamente para cualquier persona que conozca la dirección URL. Por lo tanto, el primer paso es implementar las restricciones de IP para garantizar que no se permita el uso a personas no autorizadas. En una aplicación de producción habría que implementar un mecanismo de autenticación y autorización, como la identidad de ASP.NET, pero estas funciones se han omitido en la aplicación de ejemplo para que siga siendo fácil de configurar, implementar y probar.

1.  Abra el archivo *Web.Release.config* que se encuentra en la carpeta raíz del proyecto `MvcWebRole` y reemplace el valor 127.0.0.1 del atributo **ipAddress** por su dirección IP. Para ver el archivo **Web.Release.config** en **Explorador de soluciones** tiene que expandir el archivo *Web.config*.

    Puede buscar la dirección IP utilizando la ecuación de búsqueda "Find my IP" en [Bing][] o en otro motor de búsqueda.

    Cuando se publica la aplicación, se aplican las transformaciones especificadas en el archivo *Web.release.config* y se actualizan los elementos de restricción de IP en el archivo *web.config* que se implementa en la nube. Puede ver el archivo *web.config* transformado en la carpeta *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* una vez que se ha creado el paquete.

### Configuración de la aplicación para usar la cuenta de almacenamiento cuando se ejecuta en la nube

Anteriormente en el tutorial, cuando estableció las credenciales de la cuenta de almacenamiento para el rol web y los dos roles de trabajo, estableció las credenciales que se deben usar al ejecutar la aplicación localmente. Ahora necesita establecer las credenciales de la cuenta de almacenamiento que se usarán cuando ejecute la aplicación en la nube.

Para esta ejecución de prueba, usará las mismas credenciales para la nube que utilizó para la ejecución local. Si ha implementado una aplicación de producción, normalmente usará una cuenta distinta para la producción que la que usa para la prueba. También es recomendable para la producción usar una cuenta distinta para la cadena de conexión de diagnóstico que para la cadena de conexión de almacenamiento, pero para esta ejecución de prueba usará la misma cuenta.

Puede usar la misma interfaz de usuario para configurar las cadenas de conexión (asegúrese de que selecciona **Cloud** en la lista desplegable **Configuración de servicio**). También puede editar el archivo de configuración como se explica en los pasos siguientes.

1.  Abra el archivo *ServiceConfiguration.Local.cscfg* en el proyecto **AzureEmailService** y copie los elementos `Setting` para `StorageConnectionString` y `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Abra el archivo *ServiceConfiguration.Cloud.cscfg* en el proyecto **AzureEmailService** y pegue los elementos copiados en el elemento `Configuration Settings` para `MvcWebRole`, `WorkerRoleA` y `WorkerRoleB`, reemplazando los elementos `Setting` que ya están allí.

3.  Compruebe que el rol web y los dos elementos de rol de trabajo definen las mismas cadenas de conexión.

### Publicación de la aplicación

1.  Si aún no se ha abierto, inicie Visual Studio y abra la solución **AzureEmailService**.

2.  Haga clic con el botón secundario en el proyecto en la nube **AzureEmailService** y seleccione **Publicar**.

    ![Paquete][]

    Aparecerá el cuadro de diálogo **Publicar aplicación de Azure**.

    ![Paquete en la nube][]

3.  Si ha usado el método automático para la importación de las credenciales de la cuenta de almacenamiento anteriormente, la suscripción a Azure se encuentra en la lista desplegable y puede seleccionarla y, a continuación, hacer clic en **Siguiente**. De lo contrario, haga clic en **Iniciar sesión para descargar credenciales** y siga las instrucciones en [Configuración de la aplicación para usar la cuenta de almacenamiento de Azure][Configuración de la aplicación para el almacenamiento de Azure] para descargar e importar la configuración de publicación.

4.  En la pestaña **Configuración común**, compruebe el servicio en la nube está seleccionado en la lista desplegable **Servicio en la nube**.

5.  En el cuadro desplegable **Entorno**, cambie **Producción** por **Ensayo**.

    ![Panel][]

6.  Mantenga el valor predeterminado de **Release** para **Configuración de compilación** y **Nube** para **Configuración de servicio**.

    La configuración predeterminada de la pestaña **Advanced** es correcta para este tutorial. En la pestaña **Advanced** existen un par de valores que son útiles para el desarrollo y la realización de pruebas. Para obtener más información sobre la pestaña Advanced, consulte [Publish Azure Application Wizard][].

7.  Haga clic en **Next**.

8.  En el paso **Resumen** del asistente, haga clic en el icono para **guardar** (el icono del disquete que aparece a la derecha de la lista desplegable del perfil de destino) para guardar la configuración de publicación.

    La próxima vez que publique la aplicación, se usará la configuración guardada y no tendrá que utilizar el asistente de publicación de nuevo.

9.  Revise la configuración y, a continuación, haga clic en **Publicar**.

    ![pub][]

	Se abrirá la ventana **Registro de actividad de Azure** en Visual Studio.

1.  Haga clic en el icono de la flecha derecha para expandir la información de implementación.

    ![pub][5]
	<br/><br/>
    ![pub][6]

    La implementación puede tardar 5 minutos o más en completarse.

2.  Cuando se haya completado el estado de implementación, haga clic en la **URL de sitio web** para iniciar la aplicación.

    ![Panel][7]

3.  Especifique algún dato en las páginas web de **Mailing List**, **Subscriber** y **Message** para probar la aplicación.

    **Nota**: elimine la aplicación una vez que haya terminado de probarla para evitar pagar por recursos que no está usando. Si usa una [cuenta de evaluación gratuita de Azure][], los tres roles implementados se usarán hasta el límite mensual en un par de semanas. Para eliminar una implementación mediante el Portal de administración de Azure, seleccione el servicio en la nube y haga clic en **DELETE** en la parte inferior de la página y, a continuación, seleccione la implementación de ensayo o producción.

    ![pub][8]

4.  En Registro de actividad de Azure en Visual Studio, seleccione **Abrir en el Explorador de servidores**.

    En **Proceso de Azure** en **Explorador de servidores** puede supervisar la implementación. Si seleccionó **Enable Remote Desktop for all roles** en el **asistente para publicar aplicación de Azure**, puede hacer clic con el botón secundario en una instancia de rol y seleccionar **Connect using Remote Desktop**.

    ![pub][9]

## <a name="swap"></a>Aumento de nivel de la aplicación de ensayo a producción

1.  En el [Portal de administración de Azure][], haga clic en el icono **Servicios en la nube** del panel izquierdo y, a continuación, seleccione su servicio en la nube y haga clic en la pestaña **Panel**.

2.  Haga clic en **Swap**.

3.  Haga clic en **Yes** para completar el intercambio VIP (IP virtual). Este paso puede tardar varios minutos en completarse.

    ![Panel][10]

4.  Cuando el intercambio se haya completado, desplácese a la pestaña **Panel** para la implementación **Producción** en la sección **vista rápida** situada en la parte inferior derecha de la página. Tenga en cuenta que **Site URL** ha cambiado de un prefijo del GUID al nombre del servicio en la nube.

    ![Panel][11]

5.  Haga clic en el vínculo en **Site URL** o cópielo y péguelo en un explorador para probar la aplicación en producción.

    Si no ha cambiado la configuración de la cuenta de almacenamiento, los datos que especificó mientras probaba la versión de ensayo de la aplicación se muestran cuando ejecuta la aplicación en la nube.

## <a name="trace"></a>Configuración y visualización de los datos de seguimiento

El seguimiento es una herramienta inestimable para la depuración de una aplicación en la nube. En esta sección del tutorial, verá cómo visualizar datos de seguimiento.

1.  Compruebe que la cadena de conexión de diagnóstico esté configurada para usar su cuenta de almacenamiento de Azure y no el almacenamiento de desarrollo.

    Si ha seguido las instrucciones anteriores del tutorial, serán las mismas. Puede comprobar que son las mismas usando la interfaz de usuario de Visual Studio (la pestaña **Settings** en **Propiedades** para los roles) o visualizando los archivos *ServiceConfiguration.\*.cscfg*.

    **Nota:** es recomendable usar una cuenta de almacenamiento diferente para el seguimiento de datos de la cuenta de almacenamiento usada para los datos de producción, pero para simplificar este tutorial, se ha configurado la misma cuenta para el seguimiento.

1.  En Visual Studio, abra *WorkerRoleA.cs* en el proyecto **WorkerRoleA**, busque `ConfigureDiagnostics` y examine el método `ConfigureDiagnostics`.

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

    En este código, `DiagnosticMonitor` se configura para almacenar hasta 500 MB de información de seguimiento (después de 500 MB, los datos más antiguos se sobrescribirán) y para almacenar todos los mensajes de seguimiento (LogLevel.Verbose). `ScheduledTransferPeriod` transfiere los datos de seguimiento al almacenamiento cada minuto. Debe establecer `ScheduledTransferPeriod` para guardar datos de seguimiento.

    El método `ConfigureDiagnostics` de cada uno de los roles web y de trabajo configura la escucha de seguimiento para registrar datos cuando llame a la API de seguimiento. Para obtener más información, consulte [Uso del seguimiento en aplicaciones en la nube de Windows Azure][].

2.  En **Explorador de servidores**, haga doble clic en **WADLogsTable** (expanda **Azure**/**Almacenamiento**/**yourstorageaccountname**/**Tablas**) para la cuenta de almacenamiento que agregó anteriormente. Puede especificar un [filtro para los servicios de datos de WCF][] para limitar las entidades mostradas. En la siguiente imagen, solo se muestran los mensajes de advertencia y error.

    ![Panel][12]

## <a name="addRole"></a>Incorporación de otra instancia de rol de trabajo para gestionar una carga aumentada

Existen dos enfoques para realizar la escalación de recursos informáticos en los roles de Azure: la especificación del [tamaño de la máquina virtual][] o la especificación del recuento de instancias de las máquinas virtuales en ejecución.

El tamaño de la máquina virtual (VM) se especifica en el atributo `vmsize` del elemento `WebRole` o `WorkerRole` en el archivo *ServiceDefinition.csdef*. La configuración predeterminada es `Small`, que le ofrece un núcleo y 1,75 GB de RAM Para aplicaciones multiproceso y que usen una gran cantidad de memoria, disco y ancho de banda, puede aumentar el tamaño de la VM para que el rendimiento sea mayor. Por ejemplo, una VM `ExtraLarge` cuenta con 8 núcleos de CPU y 14 GB de RAM. El aumento de memoria, núcleos de CPU, disco y ancho de banda en una máquina única se conoce como *escalación vertical*. Entre los candidatos a los que mejor se puede aplicar la escalación vertical se encuentran las aplicaciones web ASP.NET que usan [métodos asincrónicos][] (información en inglés). Consulte [Configurar los tamaños de los Servicios en la nube][tamaño de la máquina virtual] para obtener una descripción de los recursos proporcionados por cada tamaño de la VM.

El rol de trabajo B en esta aplicación es el componente de limitación en una carga alta, ya que realiza el trabajo del envío de correos electrónicos. El rol de trabajo A solo crea mensajes de cola, lo que no consume una gran cantidad de recursos. Puesto que el rol de trabajo B no es multiproceso y no tiene un gran impacto en la memoria, no es un buen candidato para la escalación vertical. El rol de trabajo B puede escalarse linealmente (es decir, prácticamente duplicar el rendimiento cuando se duplican las instancias) mediante el aumento del recuento de instancias. El aumento del número de instancias de proceso se denomina *escalación horizontal*. Existe un coste para cada instancia, por lo que debe escalar horizontalmente solo cuando la aplicación lo requiera.

Escale horizontalmente un rol de trabajo o web actualizando la configuración en la interfaz de usuario de Visual Studio mediante la edición directa de los archivos *ServiceConfiguration.\*.cscfg*. El recuento de instancias se especifica en la pestaña **Configuración** de la ventana **Propiedades** y en el elemento `Instances` de los archivos *.cscfg* . Cuando actualice la configuración, tendrá que implementar el archivo de configuración actualizado para que el cambio surta efecto. Alternativamente, para el aumento transitorio de la carga, también puede cambiar el número de instancias de rol manualmente o configurar Azure para cambiar automáticamente el número de instancias basándose en criterios que usted mismo especifique. Para obtener más información sobre la autoescala, consulte [el último tutorial de esta serie][].

En esta sección del tutorial, realizará la escalación horizontal del rol de trabajo B mediante el portal de administración, pero primero verá cómo se realiza en Visual Studio.

Para realizar este procedimiento en Visual Studio, haga clic con el botón secundario en el rol en **Roles** en el proyecto en la nube y seleccione **Propiedades**.

![Clic con el botón secundario en Propiedades][]

A continuación, seleccione la pestaña **Configuration** de la izquierda y seleccione **Cloud** en el menú desplegable **Configuración de servicio**.

![Recuento de instancias][]

Tenga en cuenta que también puede configurar el tamaño de la VM en esta pestaña.

En los siguientes pasos se explica cómo escalar horizontalmente mediante el Portal de administración de Azure.

1.  En el Portal de administración de Azure, seleccione el servicio en la nube y, a continuación, haga clic en **Scale**.

2.  Aumente el número de instancias del rol de trabajo B y, a continuación, haga clic en **Save**.

    ![Aumento de instancias][]

    El aprovisionamiento de las nuevas VM puede tardar algunos minutos.

3.  Seleccione la pestaña **Instancias** para ver cada instancia de rol en su aplicación.

    ![Visualización de instancias][]

## <a name="nextsteps"></a>Pasos siguientes

Ya ha visto cómo configurar, implementar y escalar la aplicación completa. Los siguientes tutoriales muestran cómo crear la aplicación desde el principio. En el [próximo tutorial][], creará un rol web.

Podrá encontrar vínculos a recursos adicionales para trabajar con tablas, colas y blobs de Almacenamiento de Azure en el [último tutorial de la serie][].

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">Tutorial 3</a></div>

  [primer tutorial de la serie]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Configuración del entorno de desarrollo]: #setupdevenv
  [Descarga y ejecución de la solución completa]: #downloadcnfg
  [Creación de una cuenta de almacenamiento de Azure]: #createWASA
  [Creación de un servicio en la nube]: #createcloudsvc
  [Configuración de la aplicación para el almacenamiento de Azure]: #conf4azureStorage
  [Configuración de la aplicación para usar SendGrid]: #sendGrid
  [Implementación de la aplicación en Azure]: #deployAz
  [Aumento de nivel de la aplicación de ensayo a producción]: #swap
  [Configuración y visualización de datos de seguimiento]: #trace
  [Incorporación de otra instancia de rol de trabajo para gestionar una carga aumentada]: #addRole
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [solución completa]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Ejecución de la aplicación]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [Página de índice de listas de correo]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [Página de índice de suscriptores]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [página de creación de mensajes]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [página de índice de mensajes]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [Explorador de servidores]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [Explorador de almacenamiento de VS]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Explorador de almacenamiento de Azure]: http://azurestorageexplorer.codeplex.com/
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Nuevo almacenamiento]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [Creación rápida]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [Administración de cuentas de almacenamiento]: /en-us/manage/services/storage/how-to-manage-a-storage-account/
  [creación de almacenamiento con prefijo de la dirección URL]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [Administrar claves]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [GUID de las claves]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [Nube rápida]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [Crear un grupo de afinidad en el Portal de administración]: http://msdn.microsoft.com/en-us/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [Clic con el botón secundario en Propiedades]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [Configurar un proyecto de Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx
  [Selección de las propiedades del proyecto en la nube]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [Deshabilitación del inicio automático del emulador de almacenamiento]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [Envío de correos electrónicos con SendGrid y Azure]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "find my IP"
  [Paquete]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [Paquete en la nube]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [Panel]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [Publish Azure Application Wizard]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "pub wiz"
  [pub]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [cuenta de evaluación gratuita de Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/ "cuenta de evaluación gratuita"
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [Uso del seguimiento en aplicaciones en la nube de Windows Azure]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Uso del seguimiento en Windows Azure"
  [filtro para los servicios de datos de WCF]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "Filtro WCF"
  [12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [tamaño de la máquina virtual]: http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "Tamaños de máquinas virtuales"
  [métodos asincrónicos]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC"
  [el último tutorial de esta serie]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [Recuento de instancias]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [Aumento de instancias]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [Visualización de instancias]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [próximo tutorial]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [último tutorial de la serie]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
