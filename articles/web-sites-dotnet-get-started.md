<properties linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Get started with Windows Azure" pageTitle="Get started with Windows Azure for .NET" metaKeywords="" description="This tutorial shows you how to deploy an ASP.NET web site to Windows Azure using Visual Studio 2013. In less than 15 minutes you'll have an app up-and-running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Windows Azure and ASP.NET" authors=""  solutions="" writer="tdykstra" manager="wpickett" editor="mollybos"  />

# Introducción a Windows Azure y ASP.NET

<div class="dev-center-tutorial-selector sublanding">
[Visual Studio 2013](/en-us/develop/net/tutorials/get-started/ "Visual
Studio 2013"){: .current}[Visual Studio
2012](/en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio
2012")
</div>

Este tutorial muestra cómo implementar una aplicación web ASP.NET en un
sitio web de Windows Azure utilizando el Asistente para publicación web
en Visual Studio 2013 o Visual Studio 2013 Express para Web. (Si
prefiere utilizar Visual Studio 2012, consulte [la versión anterior de
este tutorial](/en-us/develop/net/tutorials/get-started-vs2012/)).

Puede abrir una cuenta de Windows Azure de manera gratuita y, si todavía
no tiene Visual Studio 2013, el SDK instala automáticamente Visual
Studio 2013 Express para Web. De este modo, puede empezar a desarrollar
contenido para Windows Azure sin coste alguno.

En este tutorial se asume que no tiene ninguna experiencia previa con
Windows Azure. Cuando acabe, tendrá una aplicación web sencilla
ejecutándose en la nube.

Aprenderá a realizar los siguientes procedimientos:

* Habilitar su equipo para desarrollar contenido de Windows Azure
  mediante la instalación del SDK de Windows Azure.
* Crear un proyecto ASP.NET MVC 5 de Visual Studio y publicarlo en un
  sitio web de Windows Azure.

La siguiente ilustración muestra la aplicación completada:

![P&aacute;gina principal del sitio
web](./media/web-sites-dotnet-get-started-vs2013/GS13homepage.png)

<div class="dev-callout" markdown="1">
**Nota**
Para realizar este tutorial, necesita una cuenta de Windows Azure. Si
aún no la tiene, puede [activar los beneficios de suscripción a
MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F){:
data-morhtml="true" target="_blank"} o bien [regístrese para obtener una
evaluación gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F){:
data-morhtml="true" target="_blank"}.


</div>

### Secciones del tutorial

1.  [Configurar el entorno de desarrollo](#setupdevenv)
2.  [Crear una aplicación ASP.NET MVC5](#createapp)
3.  [Implementar la aplicación en Windows Azure](#deploytowindowsazure)
4.  [Pasos siguientes](#nextsteps)

\[WACOM.INCLUDE
[install-sdk-2013-only](../includes/install-sdk-2013-only.md)\]

<h2><a name="createapp" /><span class="short-header">Crear la aplicación</span>Crear una aplicación ASP.NET MVC 5</h2>


El primer paso consiste en crear un proyecto de aplicación web de Visual
Studio que luego publicará en Windows Azure.

### Crear el proyecto

1.  Abra Visual Studio 2013 o Visual Studio 2013 Express para Web.

2.  En el menú **Archivo**, haga clic en **Nuevo proyecto**.
    
    ![Nuevo proyecto en el men&uacute;
    Archivo](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3.  En el cuadro de diálogo **Nuevo proyecto**, expanda **C#** y
    seleccione **Web** debajo de **Plantillas instaladas**; a
    continuación, seleccione **Aplicación web ASP.NET**.

4.  Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de
    destino seleccionado.

5.  Póngale a la aplicación el nombre **Miejemplo** y haga clic en
    **Aceptar**.
    
    ![Cuadro de di&aacute;logo Nuevo
    proyecto](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  En el cuadro de diálogo **Nuevo proyecto ASP.NET**, seleccione la
    plantilla **MVC** y, a continuación, haga clic en **Cambiar
    autenticación**.
    
    ![Cuadro de di&aacute;logo Nuevo proyecto
    ASP.NET](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

7.  En el cuadro de diálogo **Cambiar autenticación**, haga clic en
    **Sin autenticación** y, a continuación, en **Aceptar**.
    
    ![Sin
    autenticaci&oacute;n](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    La aplicación de ejemplo que está creando no incluirá
    características que requieran que los usuarios inicien sesión. Para
    obtener información acerca de cómo implementar características de
    autenticación y autorización, consulte la sección [Pasos
    siguientes](#nextsteps) al final de este tutorial.

8.  En el cuadro de diálogo **Nuevo proyecto ASP.NET**, haga clic en
    **Aceptar**.
    
    ![Cuadro de di&aacute;logo Nuevo proyecto
    ASP.NET](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

### Ejecutar la aplicación de forma local

1.  Pulse CTRL+F5 para ejecutar la aplicación.
    
    Aparece la página principal de la aplicación en el explorador
    predeterminado.
    
    ![Sitio web ejecut&aacute;ndose de forma
    local](./media/web-sites-dotnet-get-started-vs2013/GS13homepage.png)
    
    La URL `http://localhost` indica que se está ejecutando en el equipo
    local. De manera predeterminada, se ejecuta en IIS Express, que es
    una versión ligera de IIS diseñada para el desarrollo de
    aplicaciones web.
    
    Esto es todo lo que necesita hacer para crear una aplicación
    sencilla que se pueda implementar en Windows Azure.

<h2><a name="deploytowindowsazure" /><span class="short-header">Implementar la aplicación</span>Implementar la aplicación en Windows Azure</h2>


1.  Cierre el explorador.

2.  En el **Explorador de soluciones** de Visual Studio, haga clic en el
    proyecto con el botón derecho y, a continuación, seleccione
    **Publicar** en el menú contextual.
    
    ![Opci&oacute;n Publicar del men&uacute; contextual del
    proyecto](./media/web-sites-dotnet-get-started-vs2013/GS13publish.png)
    
    Se abre el Asistente para **Publicación web**.

3.  En la pestaña **Perfil** del Asistente para **Publicación web**,
    haga clic en **Importar**.
    
    ![Importar configuraci&oacute;n de
    publicaci&oacute;n](./media/web-sites-dotnet-get-started-vs2013/ImportPublishSettings.png)
    
    Aparece el cuadro de diálogo **Importar perfil de publicación**.

4.  Utilice uno de los métodos siguientes para habilitar Visual Studio
    para que se conecte con su cuenta de Windows Azure.
    
    * Haga clic en **Iniciar sesión** y, a continuación, escriba las
      credenciales de su cuenta de Windows Azure.
      
      Este método es más rápido y sencillo pero, si lo utiliza, no podrá
      ver la base de datos SQL de Windows Azure ni los servicios móviles
      en la ventana **Explorador de servidores**.
    
    * Haga clic en **Administrar suscripciones** para instalar un
      certificado de administración que permita el acceso a su cuenta.
      
      En el cuadro de diálogo **Administrar suscripciones de Windows
      Azure**, haga clic en la pestaña **Certificados** y, a
      continuación, en **Importar**. Siga las indicaciones para
      descargar e importar un archivo de suscripción (también denominado
      archivo *.publishsettings*) para su cuenta de Windows Azure.
      
      > \[WACOM.NOTE\] Descargue el archivo de suscripción en una
      > carpeta ajena a los directorios de código fuente (por ejemplo,
      > en la carpeta Descargas) y elimínelo una vez que finalice la
      > importación. Si un usuario malintencionado obtuviera acceso al
      > archivo de suscripción, podría editar, crear y eliminar
      > servicios de Windows Azure.
      
      Para obtener más información, consulte [Administrar cuentas,
      suscripciones y roles administrativos][1].

5.  En el cuadro de diálogo **Importar configuración de publicación**,
    seleccione el botón de opción **Importar desde un sitio web de
    Windows Azure** y, a continuación, haga clic en **Nuevo**.
    
    ![Agregar un nuevo
    sitio](./media/web-sites-dotnet-get-started-vs2013/GS13NewSite.png)

6.  En el cuadro de diálogo **Crear un sitio en Windows Azure**, en
    **Nombre del sitio**, escriba el nombre que desee utilizar como URL
    única de la aplicación.
    
    La URL completa consistirá en el nombre que escriba más el sufijo
    que aparece junto al cuadro de texto. Si elige una URL ya utilizada
    por otro usuario, verá un signo de exclamación rojo a la derecha en
    lugar de una marca de verificación verde y deberá buscar un nombre
    diferente.

7.  En la lista desplegable **Ubicación**, elija la ubicación más
    cercana.
    
    Este valor especifica el centro de datos en el que se ejecutará el
    sitio web.

8.  No modifique los campos de la base de datos,
    
    ya que en este tutorial no se utiliza una base de datos. En la
    sección [Pasos siguientes](#nextsteps) al final de este documento
    encontrará un enlace a un tutorial que muestra cómo utilizar una
    base de datos.

9.  Haga clic en **Crear**.  
    
    
    ![Crear un nuevo sitio
    web](./media/web-sites-dotnet-get-started-vs2013/GS13createsite.png)
    
    En unos pocos segundos se crea el sitio web. Al volver al cuadro de
    diálogo **Importar configuración de publicación**, verá que el nuevo
    sitio está seleccionado en la lista desplegable.

10. Haga clic en **Aceptar**.
    
    ![Sitio web
    creado](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

11. En la pestaña **Conexión** del Asistente para **Publicación web**,
    haga clic en **Validar conexión** para asegurarse de que la
    configuración sea correcta.
    
    ![Validar
    conexi&oacute;n](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)
    
    Una vez que se valide la conexión, aparecerá una marca de
    verificación verde junto al botón **Validar conexión**.

12. Haga clic en **Siguiente**.
    
    ![Conexi&oacute;n validada
    correctamente](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

13. En la pestaña **Configuración**, haga clic en **Siguiente**.
    
    ![Pesta&ntilde;a
    Configuraci&oacute;n](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)
    
    Puede aceptar la configuración predeterminada de esta pestaña. Está
    implementando una configuración de compilación de versión y no es
    necesario eliminar archivos del servidor de destino, precompilar la
    aplicación ni excluir archivos de la carpeta App\_Data.

14. En la pestaña **Vista previa**, haga clic en **Iniciar vista
    previa**.
    
    ![Bot&oacute;n Iniciar vista previa de la pesta&ntilde;a Vista
    previa](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)
    
    En esta pestaña aparece la lista de archivos que se copiarán en el
    servidor. Para publicar la aplicación no es necesario mostrar la
    vista previa, pero se trata de una función útil que conviene
    conocer.

15. Haga clic en **Publicar**.
    
    ![Salida de archivo Iniciar vista
    previa](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)
    
    Visual Studio comienza el proceso de copiar los archivos en el
    servidor de Windows Azure.

16. La ventana **Resultado** muestra qué acciones de implementación se
    realizaron e informa de la correcta finalización de la
    implementación.
    
    ![Ventana Resultado que informa de que la implementaci&oacute;n se
    realiz&oacute;
    correctamente](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

17. Tras la correcta finalización de la implementación, el explorador
    predeterminado se dirige automáticamente a la URL del sitio web
    implementado.
    
    La aplicación creada ahora se ejecuta en la nube.
    
    ![Sitio web ejecut&aacute;ndose en Windows
    Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

<h2><a name="nextsteps" /><span class="short-header">Pasos siguientes</span>Pasos siguientes</h2>


En este tutorial, aprendió a implementar una aplicación web sencilla en
un sitio web de Windows Azure. Existen otros recursos disponibles para
aprender cómo administrar el sitio, escalarlo y solucionar sus
problemas; cómo agregar funcionalidad de base de datos, autenticación y
autorización; y cómo decidir si la aplicación debe ejecutarse en un
servicio en la nube de Windows Azure en lugar de en un sitio web de
Windows Azure.

<h3>Administrar un sitio web</h3>


El [Portal de administración de Windows Azure][2] es una interfaz web
que puede utilizar para administrar y supervisar todos los servicios de
Windows Azure.

Puede crear nuevos sitios web, servicios en la nube, máquinas virtuales
y bases de datos, entre otros. También puede crear aplicaciones de
código abierto desde la galería, o bien administrar los servicios que
creó. Por ejemplo, la siguiente captura de pantalla muestra los botones
**Detener**, **Reiniciar** y **Eliminar** para un sitio web de Windows
Azure de la pestaña **Panel** del portal de administración. El **Panel**
también muestra estadísticas de rendimiento, como el tiempo de CPU
utilizado, el número de solicitudes, los datos entrantes y salientes y
los errores que se produjeron.

![Pesta&ntilde;a Panel del portal de
administraci&oacute;n](./media/web-sites-dotnet-get-started-vs2013/MPStopStartDelete.png)

Puede cambiar otros muchos valores del sitio en la pestaña
**Configurar**. Para obtener más información, consulte [Administrar
sitios web](/en-us/manage/services/web-sites/how-to-manage-websites/).

También puede realizar algunas tareas de administración de sitios web
directamente desde el **Explorador de servidores** en Visual Studio.
Para obtener más información acerca de lo que puede hacer en el
**Explorador de servidores**, consulte [Solución de problemas de sitios
web de Windows Azure en Visual
Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

<h3>Escalar un sitio web</h3>


Si el sitio es público y empieza a tener más tráfico, es posible que los
tiempos de respuesta se alarguen. Para solucionarlo, puede agregar
fácilmente recursos de servidores en la pestaña **Escalar** del portal
de administración. Para obtener más información, consulte [Escalar un
sitio web](/en-us/manage/services/web-sites/how-to-scale-websites/).
(Agregar recursos de servidores para escalar un sitio web no es
gratuito).

<h3>Solucionar problemas de un sitio web</h3>


Es posible que desee consultar los resultados de seguimiento o los
registros para obtener ayuda con la solución de problemas. Visual Studio
ofrece herramientas integradas que facilitan la visualización de los
registros de Windows Azure cuando se generan, en tiempo real. También
puede ejecutar el sitio en modo de depuración de manera remota en
Windows Azure. Para obtener más información, consulte [Solución de
problemas de sitios web de Windows Azure en Visual
Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

<h3>Agregar funcionalidad de base de datos y autorización</h3>


La mayoría de los sitios web de producción utilizan una base de datos y
restringen algunas funciones del sitio a los usuarios autorizados. En el
siguiente enlace encontrará un tutorial introductorio acerca del acceso
a base de datos, la autenticación y la autorización: [Implementar una
aplicación ASP.NET MVC segura con suscripción, OAuth y base de datos SQL
en un sitio web de Windows
Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/).

<h3>Decidir si la aplicación debe ejecutarse en un servicio en la nube</h3>


En algunos casos, puede que prefiera ejecutar la aplicación en un
servicio en la nube de Windows Azure que en un sitio web de Windows
Azure. Para obtener más información, consulte [Modelos de ejecución de
Windows Azure](/en-us/develop/net/fundamentals/compute/) y [Sitios web,
servicios en la nube y MV de Windows Azure: cuándo usar cada uno][3]. En
el siguiente enlace encontrará una serie de tutoriales acerca de cómo
crear una aplicación web ASP.NET de niveles múltiples e implementarla en
un servicio en la nube: [Aplicación .NET de niveles múltiples utilizando
tablas, colas y blobs de
almacenamiento](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).



[1]: http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert
[2]: http://manage.windowsazure.com
[3]: http://www.windowsazure.com/en-us/manage/services/web-sites/choose-web-app-service/
