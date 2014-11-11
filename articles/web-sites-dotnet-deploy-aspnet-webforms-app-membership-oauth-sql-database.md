<properties linkid="Deploy_a_Secure_ASP.NET_Web_Forms_App_with_Membership_OAuth_and_SQL_Database_to_an_Azure_Web_Site" pageTitle="Deploy a Secure ASP.NET Web Forms App with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure WebForms Hello World Tutorial, Azure Getting Started with WebForms Tutorial, Azure C# WebForms Hello World Tutorial, SQL Azure C# WebForms Tutorial" description="This tutorial shows you how to build a secure ASP.NET 4.5 Web Forms web app that incorporates a SQL Database and deploy the application to Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy a secure ASP.NET 4.5 Web Forms app to an Azure Website" authors="erikre"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="erikre" />

# Implementación de una aplicación de ASP.NET Web Forms segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure

Este tutorial muestra cómo compilar una aplicación de ASP.NET Web Forms 4.5 segura que incorpora una base de datos SQL e implementar la aplicación en Azure.

> [WACOM.NOTE]
> Para obtener una versión para MVC de este tutorial, consulte [Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos de SQL en un sitio web de Azure][Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos de SQL en un sitio web de Azure].

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. Puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se supone que no tiene ninguna experiencia previa con Microsoft Azure. Al terminar el tutorial, tendrá una aplicación web ejecutándose en la nube que usa una base de datos en la nube.

Aprenderá a realizar los siguientes procedimientos:

-   Crear un proyecto de ASP.NET 4.5 Web Forms y publicarlo en un sitio web de Azure
-   Usar OAuth y las suscripciones a ASP.NET para proteger la aplicación.
-   Usar una sola base de datos para datos de suscripción y de aplicación.
-   Usar Web Forms Scaffolding para crear páginas web que le permitan modificar datos.
-   Utilizar la nueva API de suscripción para agregar usuarios y roles
-   Utilizar una Base de datos SQL para almacenar datos en Azure

Va a desarrollar una aplicación web de lista de contactos sencilla basada en ASP.NET 4.5 Web Forms que utiliza Entity Framework para el acceso a la base de datos. La imagen siguiente muestra la página de formularios Web Forms que permiten acceso de lectura y escritura a la base de datos:

![Página Contactos - Editar][Página Contactos - Editar]

> [WACOM.NOTE]
> Necesita una cuenta de Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a MSDN][activar los beneficios de suscripción a MSDN], o bien [regístrese para obtener una evaluación gratuita][regístrese para obtener una evaluación gratuita].

Este tutorial contiene las siguientes secciones:

-   [Configuración del entorno de desarrollo][Configuración del entorno de desarrollo]
-   [Configuración del entorno de Azure][Configuración del entorno de Azure]
-   [Creación de una aplicación de ASP.NET Web Forms][Creación de una aplicación de ASP.NET Web Forms]
-   [Incorporación de una base de datos a la aplicación][Incorporación de una base de datos a la aplicación]
-   [Habilitación de SSL para el proyecto][Habilitación de SSL para el proyecto]
-   [Incorporación de un proveedor de OAuth 2.0][Incorporación de un proveedor de OAuth 2.0]
-   [Uso de la API de suscripción para restringir el acceso][Uso de la API de suscripción para restringir el acceso]
-   [Implementación de la aplicación con la base de datos de Azure][Implementación de la aplicación con la base de datos de Azure]
-   [Revisión de la base de datos][Revisión de la base de datos]
-   [Pasos siguientes][Pasos siguientes]

## Configuración del entorno de desarrollo

Para comenzar, configure el entorno de desarrollo con la instalación de Visual Studio 2013 y el SDK de Azure para .NET.

1.  Instale [Visual Studio 2013][Visual Studio 2013], si aún no lo tiene instalado.
2.  Instale el [SDK de Azure para Visual Studio 2013][SDK de Azure para Visual Studio 2013]. Este tutorial requiere Visual Studio 2013 antes de instalar el SDK de Azure para Visual Studio 2013.

    > [WACOM.NOTE]
    >  Según la cantidad de dependencias de SDK que tenga ya en la máquina, la instalación del SDK puede tardar bastante tiempo, desde unos minutos a media hora o más.

3.  Si el sistema le pregunta si desea ejecutar o guardar el archivo ejecutable de instalación, haga clic en **Ejecutar**.
4.  En la ventana **Instalador de plataforma web**, haga clic en **Instalar** y proceda con la instalación.
    ![Web Platform Installer][Web Platform Installer]

    > [WACOM.NOTE]
    > Si ya tiene el SDK instalado, no habrá ningún elemento para instalar. El número de elementos para instalar se puede ver en la zona inferior izquierda de la ventana **Instalador de plataforma web**.

5.  Si aún no tiene **Visual Studio Update 2**, descargue e instale **[Visual Studio 2013 Update 2][Visual Studio 2013 Update 2]** o posterior.

    > [WACOM.NOTE]
    > Debe instalar Visual Studio 2013 Update 2 o posterior para usar Goggle OAuth 2.0 y SSL en modo local sin advertencias. Asimismo, necesita la versión Update 2 para usar Web Forms Scaffolding.

Cuando la instalación se complete, dispondrá de todo lo necesario para iniciar el desarrollo.

## Configuración del entorno de Azure

En esta sección, va a configurar el entorno de Azure con la creación de un sitio web y una base de datos SQL de Azure.

### Creación de un sitio web y una base de datos SQL en Azure

En este tutorial, su sitio web de Azure se ejecutará en un entorno de hospedaje compartido, es decir, se ejecutará en máquinas virtuales (VM) que se comparten con otros sitios web de Azure. Los entornos de hospedaje compartidos ofrecen un coste reducido a los nuevos usuarios de servicios en la nube. Más adelante, si el tráfico web aumenta, es posible escalar la aplicación para atender la demanda ejecutándola en máquinas virtuales dedicadas. Si necesita una arquitectura más compleja, puede migrar la aplicación a un Servicio en la nube de Azure. Los servicios en la nube se ejecutan en MV dedicadas que se pueden configurar según sus necesidades.

La Base de datos SQL de Azure es un servicio de base de datos relacional en la nube que se basa en la tecnología de SQL Server. Las herramientas y aplicaciones que funcionan con SQL Server también funcionan con la Base de datos SQL.

1.  En el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Sitios web** en el panel izquierdo y elija **Nuevo**.  
    ![Instalador de plataforma web][Instalador de plataforma web]
2.  Haga clic en **Sitio web** y elija **Creación personalizada**.  
    ![Creación personalizada][Creación personalizada]  
    Se abre el asistente **Nuevo sitio web - Creación personalizada**.

3.  En el paso **Crear sitio web** del asistente, escriba una cadena en el cuadro **URL** para usarla como dirección URL única de la aplicación. La URL completa consistirá en el valor que escriba más el sufijo que aparece junto al cuadro de texto. La ilustración muestra una dirección URL que probablemente ya está en uso, por lo que **debe elegir otra dirección URL**.  
    ![Contactos - Crear nuevo sitio web][Contactos - Crear nuevo sitio web]
4.  En la lista desplegable Plan de hospedaje web, elija la región más cercana a su ubicación. Este valor especifica el centro de datos en el que se ejecutará la máquina virtual.
5.  En la lista desplegable **Base de datos**, elija **Crear una base de datos SQL de 20 MB gratuita**.
6.  En el cuadro **Nombre de la cadena de conexión de BD**, no modifique el valor predeterminado *DefaultConnection*.
7.  Haga clic en la flecha de la parte inferior del cuadro.
    El asistente avanza hasta el paso **Especificar configuración de base de datos**.
8.  En el cuadro **Nombre**, escriba *`ContactDB`*.  
    ![Database Settings][Database Settings]
9.  En el cuadro **Servidor**, seleccione **Nuevo servidor de bases de datos SQL**.
    Si ya había creado una base de datos SQL, puede seleccionar también ese servidor SQL en el control desplegable.
10. Establezca la opción **Región** en la misma área en la que creó el sitio web.
11. Proporcione un **Nombre de inicio de sesión** y una **Contraseña** de administrador.
    Si seleccionó**Nuevo servidor de bases de datos SQL** no proporcionará aquí un nombre y una contraseña existentes, sino un nombre y una contraseña nuevos que va a definir ahora para usarlos más adelante cuando acceda a la base de datos. Si seleccionó un SQL Server creado anteriormente, se le pedirá la contraseña de la cuenta de SQL Server que abrió para crearlo. Para este tutorial, no active la casilla **Avanzadas**.
12. Haga clic en la marca de verificación que aparece en la esquina inferior derecha del cuadro para indicar que finalizó la configuración.

El **Portal de administración de Azure** vuelve a la página **Sitios web** y la columna **Estado** muestra que se está creando el sitio. Poco después (normalmente, menos de un minuto), la columna **Estado** muestra que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece junto al icono **Sitios web** y el número de bases de datos aparece junto al icono **Bases de datos SQL** .

## Creación de una aplicación de ASP.NET Web Forms

Ya ha creado un sitio web de Azure, pero todavía no hay contenido en él. El siguiente paso consiste en crear la aplicación web de Visual Studio que luego publicará en Azure.

### Creación del proyecto

1.  Seleccione **Nuevo proyecto** en el menú **Archivo** en Visual Studio.  
    ![File Menu - New Project][File Menu - New Project]  
2.  A la izquierda, seleccione el grupo **Plantillas** -\> **Visual C#** -\> **Web**.
3.  Elija la plantilla **Aplicación web ASP.NET** en la columna central.
4.  Dele al proyecto el nombre *ContactManager* y haga clic en **Aceptar**.  
    ![Cuadro de diálogo Nuevo proyecto][Cuadro de diálogo Nuevo proyecto]

    > [WACOM.NOTE]
    > El nombre del proyecto en esta serie de tutoriales es **ContactManager**. Se recomienda utilizar este nombre de proyecto tal cual para que el código proporcionado en el tutorial funcione correctamente.

5.  En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **Aplicación de Internet**. Desactive la casilla **Host en la nube** si está seleccionada y haga clic en **Aceptar**.  
    ![Cuadro de diálogo Nuevo proyecto de ASP.NET][Cuadro de diálogo Nuevo proyecto de ASP.NET]  
    Se crea la aplicación de formularios Web Forms.

### Actualización de la página maestra

En ASP.NET Web Forms, las páginas maestro permiten crear un diseño coherente de las páginas en la aplicación. Una sola página maestro define la apariencia y el comportamiento estándar que desea para todas las páginas (o un grupo de páginas) en su aplicación. Después, puede crear páginas de contenido individuales con el contenido que desee mostrar. Cuando los usuarios soliciten las páginas de contenido, ASP.NET las combina con la página maestro para producir una salida que combina el diseño de la página maestro con el contenido de la página de contenido.
El nuevo sitio necesita el nombre de la aplicación y un vínculo actualizado. Este vínculo apuntará a una página que mostrará los datos de contacto. Para realizar estos cambios, debe modificar el código HTML en la página maestro.

6.  En el **Explorador de soluciones**, busque y abra la página *Site.Master*.
7.  En la página, en la vista **Diseño**, cambie a la vista **Código fuente**.
8.  Actualice la página maestro modificando y agregando el marcado resaltado en amarillo:

``` prettyprint
<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

<!DOCTYPE html>

<html lang="en">
<head runat="server">
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title><%: Page.Title %> - Contact Manager</title>

    <asp:PlaceHolder runat="server">
        <%: Scripts.Render("~/bundles/modernizr") %>
    </asp:PlaceHolder>
    <webopt:bundlereference runat="server" path="~/Content/css" />
    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

</head>
<body>
    <form runat="server">
        <asp:ScriptManager runat="server">
            <Scripts>
                <%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                <%--Framework Scripts--%>
                <asp:ScriptReference Name="MsAjaxBundle" />
                <asp:ScriptReference Name="jquery" />
                <asp:ScriptReference Name="bootstrap" />
                <asp:ScriptReference Name="respond" />
                <asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                <asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                <asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                <asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                <asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                <asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                <asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                <asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                <asp:ScriptReference Name="WebFormsBundle" />
                <%--Site Scripts--%>
            </Scripts>
        </asp:ScriptManager>

        <div class="navbar navbar-inverse navbar-fixed-top">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" runat="server" id="ContactDemoLink" href="~/Contacts/Default.aspx">Contact Demo</a>
                </div>
                <div class="navbar-collapse collapse">
                    <ul class="nav navbar-nav">
                        <li><a runat="server" href="~/">Home</a></li>
                        <li><a runat="server" href="~/About">About</a></li>
                        <li><a runat="server" href="~/Contact">Contact</a></li>
                    </ul>
                    <asp:LoginView runat="server" ViewStateMode="Disabled">
                        <AnonymousTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Register">Register</a></li>
                                <li><a runat="server" href="~/Account/Login">Log in</a></li>
                            </ul>
                        </AnonymousTemplate>
                        <LoggedInTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Manage" title="Manage your account">Hello, <%: Context.User.Identity.GetUserName()  %> !</a></li>
                                <li>
                                    <asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                </li>
                            </ul>
                        </LoggedInTemplate>
                    </asp:LoginView>
                </div>
            </div>
        </div>
        <div class="container body-content">
            <asp:ContentPlaceHolder ID="MainContent" runat="server">
            </asp:ContentPlaceHolder>
            <hr />
            <footer>
                <p>© <%: DateTime.Now.Year %> - Contact Manager</p>
            </footer>
        </div>
    </form>
</body>
</html>
```

Más adelante en este tutorial, agregará funcionalidad de scaffolding para formularios Web Forms. La función de scaffolding creará la página a la que hace referencia el vínculo “Contact Demo” anterior.

### Ejecución de la aplicación de forma local

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la página *Default.aspx* y seleccione **Establecer como página de inicio**.
2.  A continuación, presione **CTRL + F5** para ejecutar la aplicación.
    Aparece la página predeterminada de la aplicación en la ventana del explorador predeterminado.  
    ![Contactos - Crear nuevo sitio web][1]

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure. Más adelante, agregará funcionalidad de base de datos, así como las páginas necesarias para mostrar y editar datos de contacto.

### Implementación de la aplicación en Azure

Ahora que ya ha creado y ejecutado la aplicación de forma local, es el momento de implementarla en Azure.

1.  En Visual Studio, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.  
    ![Seleccione Publicar][Seleccione Publicar]  
    Se muestra el cuadro de diálogo **Publicación web**.

2.  En la pestaña **Perfil** del cuadro de diálogo **Publicación web**, haga clic en **Sitios web Azure**.  
    ![Cuadro de diálogo Publicación web][Cuadro de diálogo Publicación web]  
3.  Si aún no ha iniciado sesión, haga clic en el botón **Iniciar sesión** en el cuadro de diálogo **Seleccionar sitio web existente**. Cuando haya iniciado sesión, seleccione el sitio web que creó en la primera parte de este tutorial. Haga clic en **Aceptar** para continuar.
    ![Cuadro de diálogo Seleccionar el sitio web existente][Cuadro de diálogo Seleccionar el sitio web existente]  
    Visual Studio descargará su configuración de publicación.
4.  En el cuadro de diálogo **Publicación Web**, haga clic en **Publicar**.  
    ![Cuadro de diálogo Publicación web][2]  
    Verá el estado global de la publicación en la ventana **Actividad de publicación web** en Visual Studio:  
    ![Actividad de publicación web][Actividad de publicación web]

La aplicación creada ahora se ejecuta en la nube. La próxima vez que implemente la aplicación desde Visual Studio, solo se implementarán los archivos modificados o nuevos.  
 ![Aplicación en el explorador][Aplicación en el explorador]

> [WACOM.NOTE]
> Si se produce un error durante la publicación en un sitio web ya establecido, puede borrar la ubicación antes de agregar los archivos nuevos.
> 
> Publique de nuevo la aplicación, pero, en el cuadro de diálogo **Publicación web**, seleccione la pestaña **Configuración**. A continuación, establezca la configuración en **Depurar** y seleccione la opción **Quitar archivos adicionales en destino**. Seleccione **Publicar** para implementar de nuevo la aplicación.  
>  ![Cuadro de diálogo Publicación web][3]

## Incorporación de una base de datos a la aplicación

Ahora va a actualizar la aplicación de formularios Web Forms para agregarle funcionalidad para mostrar y actualizar contactos, así como almacenar datos en la base de datos predeterminada. Cuando creó el proyecto de formularios Web Forms, la base de datos se creó también de forma predeterminada. La aplicación utilizará Entity Framework para acceder a la base de datos y leer y actualizar los datos que contiene.

### Incorporación de una clase de modelo de datos

Comience por crear un modelo de datos sencillo con código. Este modelo de datos se incluirá en una clase llamada `Contacts`. El nombre de clase `Contacts` se eligió para evitar un conflicto de nombres de clase con la clase `Contact` contenida en el archivo class Contact.aspx.cs creado por la plantilla de formularios Web Forms.

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Models* y seleccione **Agregar** -\> **Clase**.  
    ![Seleccione Clase][Seleccione Clase]  
    Se muestra el cuadro de diálogo **Agregar nuevo elemento**.

2.  Dele el nombre *Contacts.cs* a la clase nueva.  
    ![Cuadro de diálogo Agregar nuevo elemento][Cuadro de diálogo Agregar nuevo elemento]  
3.  Reemplace el código predeterminado por el siguiente:
	<pre class="prettyprint">
	using System.ComponentModel.DataAnnotations;
	using System.Globalization;
	
	namespace ContactManager.Models
	{
	    public class Contacts
	    {
	        [ScaffoldColumn(false)]
	        [Key]
	        public int ContactId { get; set; }
	        public string Name { get; set; }
	        public string Address { get; set; }
	        public string City { get; set; }
	        public string State { get; set; }
	        public string Zip { get; set; }
	        [DataType(DataType.EmailAddress)]
	        public string Email { get; set; }
	    }
	}
	</pre>

La clase **Contacts** define qué datos de los contactos va a almacenar, además de una clave primaria (`ContactID`) necesaria para la base de datos. La clase **Contactos** representa los datos del contacto que se mostrarán. Cada instancia de un objeto Contacts corresponde a una fila de una tabla de la base de datos relacional y cada propiedad de la clase Contacts se asigna a una columna de esa tabla. Más adelante en este tutorial, revisará los datos del contacto que contiene la base de datos.

### Web Forms Scaffolding

En la sección anterior, ha creado la clase de modelos **Contacts**. Ahora puede usar el generador de IU (scaffolder) de formularios Web Forms para generar las páginas *List*, *Insert*, *Edit* y *Delete* que se usan cuando se trabaja con estos datos. El generador de IU de formularios Web Forms utiliza datos de Entity Framework, de arranque y dinámicos. De forma predeterminada, el generador de IU (scaffolder) de formularios Web Forms se instala como una extensión del proyecto, como parte de la plantilla del proyecto cuando se usa Visual Studio 2013.

Los pasos siguientes permiten usar el generador de IU (scaffolder) de formularios Web Forms.

1.  En Visual Studio, en la barra de menús, seleccione **Herramientas** -\> **Extensiones y actualizaciones**.
    Se abre el cuadro de diálogo **Extensiones y actualizaciones**.
2.  En el panel izquierdo del cuadro de diálogo, seleccione **En línea** -\> **Galería de Visual Studio** -\> **Herramientas** -\> **Scaffolding**.
3.  Si no ve 'Web Forms Scaffolding' en la lista, escriba 'Web Forms Scaffolding' en el cuadro de búsqueda de la derecha en el cuadro de diálogo.
4.  Si el generador de IU (scaffolder) de formularios Web Forms no está instalado, seleccione **Descargar** para descargar e instalar 'Web Forms Scaffolding'. Reinicie Visual Studio si es necesario. Asegúrese de guardar los cambios del proyecto cuando el sistema le pregunte.
    ![Cuadro de diálogo Extensiones y actualizaciones][Cuadro de diálogo Extensiones y actualizaciones]
5.  Compile el proyecto (**Ctrl + Mayús + B**).
    Debe compilar el proyecto antes de usar el mecanismo de scaffolding.
6.  En el **Explorador de soluciones**, haga clic con el botón secundario en *proyecto* y seleccione **Agregar** -\> **New Scaffolded Item**.
    Se muestra el cuadro de diálogo **Add Scaffold**.
7.  Seleccione **Web Forms** en el panel izquierdo y elija **Web Forms Pages using Entity Framework** en el panel central. Haga clic en **Add**.  
    ![Cuadro de diálogo Add Scaffold][Cuadro de diálogo Add Scaffold]  
    Se muestra el cuadro de diálogo **Add Web Forms Pages**.

8.  En el cuadro de diálogo **Add Web Forms Pages**, establezca **Clase de modelo** en `Contact (ContactManager.Models)`. Establezca **Clase de contexto de datos** en `ApplicationDbContext (ContactManager.Models)`. Haga clic en **Agregar**.
    ![Cuadro de diálogo Add Web Forms Pages][Cuadro de diálogo Add Web Forms Pages]

El generador de IU (scaffolder) de formularios Web Forms agrega una nueva carpeta que contiene las páginas *Default.aspx*, *Delete.aspx*, *Edit.aspx* e *Insert.aspx*. El generador de IU (scaffolder) de formularios Web Forms crea también una carpeta *DynamicData* que contiene una carpeta *EntityTemplates* y una carpeta *FieldTemplates*. `ApplicationDbContext` se usará tanto para la base de datos de suscripciones como para los datos de contactos.

### Configuración de la aplicación para que use el modelo de datos

La siguiente tarea consiste en habilitar la función Migraciones de Code First para crear la base de datos a partir del modelo de datos que ha creado. También agregará datos de ejemplo y un inicializador de datos.

1.  En el menú **Herramientas**, seleccione **Administrar paquetes NuGet** y **Package Manager Console**.  
    ![Cuadro de diálogo Add Web Forms Pages][4]
2.  En la ventana Package Manager Console, escriba el siguiente comando:

    ``` prettyprint
    enable-migrations
    ```

    El comando enable-migrations crea una carpeta *Migrations* y pone en ella un archivo *Configuration.cs* que puede editar para inicializar la base de datos y configurar migraciones de datos.

3.  En la ventana **Package Manager Console**, escriba el siguiente comando:

    ``` prettyprint
    add-migration Initial
    ```

    El comando `add-migration Initial` genera un archivo llamado <date_stamp>Initial en la carpeta *Migrations* que crea la base de datos. El primer parámetro (Initial) es arbitrario y se utiliza para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**. En la clase `Initial`, el método `Up` crea la tabla `Contact` y el método `Down` (que se utiliza cuando se desea volver al estado anterior) la anula.

4.  Abra el archivo *Migrations\\Configuration.cs*.
5.  Agregue el siguiente espacio de nombres:

    ``` prettyprint
    using ContactManager.Models;
    ```

6.  Reemplace el método `Seed` por el código siguiente:
	<pre class="prettyprint">
	protected override void Seed(ContactManager.Models.ApplicationDbContext context)
	{
	    context.Contacts.AddOrUpdate(p => p.Name,
	       new Contacts
	       {
	           ContactId = 1,
	           Name = "Ivan Irons",
	           Address = "One Microsoft Way",
	           City = "Redmond",
	           State = "WA",
	           Zip = "10999",
	           Email = "ivani@wideworldimporters.com",
	       },
	       new Contacts
	        {
	            ContactId = 2,
	            Name = "Brent Scholl",
	            Address = "5678 1st Ave W",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "brents@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 3,
	            Name = "Terrell Bettis",
	            Address = "9012 State St",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "terrellb@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 4,
	            Name = "Jo Cooper",
	            Address = "3456 Maple St",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "joc@wideworldimporters.com",
	        },
	        new Contacts
	        {
	            ContactId = 5,
	            Name = "Ines Burnett",
	            Address = "7890 2nd Ave E",
	            City = "Redmond",
	            State = "WA",
	            Zip = "10999",
	            Email = "inesb@wideworldimporters.com",
	        }
	        );
	}
	</pre>

    Este código inicializa la base de datos con la información de contacto. Para obtener más información acerca de la inicialización de la base de datos, consulte [Inicialización y depuración de bases de datos de Entity Framework (EF)][Inicialización y depuración de bases de datos de Entity Framework (EF)].

7.  En la consola **Package Manager Console** escriba el comando:

    ``` prettyprint
    update-database
    ```

    `update-database` ejecuta la primera migración, que crea la base de datos. De forma predeterminada, la base de datos se crea como base de datos LocalDB de SQL Server Express.
    ![Package Manager Console.][Package Manager Console.]

### Ejecución de la aplicación en modo local y visualización de los datos

Ejecute ahora la aplicación para ver cómo puede visualizar los contactos.

1.  En primer lugar, compile el proyecto (**Ctrl + Mayús + B**).
2.  Presione **Ctrl + F5** para ejecutar la aplicación.
    El explorador se abre y muestra la página *Default.aspx*.
3.  Seleccione el vínculo **Contact Demo** en la parte superior de la página para mostrar la página *Lista de contactos*.  
    ![Página Lista de contactos][Página Lista de contactos]

## Habilitación de SSL para el proyecto

Capa de sockets seguros (SSL) es un protocolo definido para permitir a los servidores y clientes web comunicarse de forma más segura mediante el uso de cifrado. Cuando no se usa SSL, cualquiera que tenga acceso físico a la red puede abrir los datos que se envían entre el cliente y el servidor para examinar el paquete. Además, varios esquemas de autenticación habituales no son seguros en HTTP plano. En particular, la autenticación básica y la autenticación mediante formularios envían credenciales no cifradas. Para ser seguros, estos esquemas de autenticación deben usar SSL.

1.  En el **Explorador de soluciones**, haga clic en el proyecto **ContactManager** y presione **F4** para mostrar la ventana **Propiedades**.
2.  Cambie **SSL habilitado** a `true`.
3.  Copie la **Dirección URL de SSL** para usarla más adelante.
    Esta dirección será `https://localhost:44300/` a menos que haya creado antes sitios web con SSL (como se muestra a continuación).
    ![Project Properties][Project Properties]
4.  En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **Contact Manager** y elija **Propiedades**.
5.  En el panel izquierdo, haga clic en **Web**.
6.  Cambie la dirección **URL del proyecto** por la **URL de SSL** que guardó antes.  
    ![Propiedades web del proyecto][Propiedades web del proyecto]
7.  Presione **CTRL + S** para guardar la página.
8.  Presione **Ctrl + F5** para ejecutar la aplicación.
    Visual Studio muestra una opción que permite evitar las advertencias sobre SSL.
9.  Haga clic en **Sí** para confiar en el certificado SSL de IIS Express y continuar.  
    ![Detalles de certificado SSL de IIS Express][Detalles de certificado SSL de IIS Express]  
    Se muestra una advertencia de seguridad.

10. Haga clic en **Sí** para instalar el certificado en el localhost.  
    ![Cuadro de diálogo Advertencia de seguridad][Cuadro de diálogo Advertencia de seguridad]  
    Se muestra la ventana del explorador.

Puede probar fácilmente la aplicación web de forma local usando SSL.

## Incorporación de un proveedor de OAuth 2.0

ASP.NET Web Forms proporciona opciones mejoradas para suscripciones y autenticación. Estas mejoras incluyen OAuth. OAuth es un protocolo abierto que ofrece autorización segura a través de un método estándar sencillo para aplicaciones web, móviles y de escritorio. La plantilla de Internet ASP.NET MVC utiliza OAuth para ofrecer Facebook, Twitter, Google y Microsoft como proveedores de autenticación. Aunque este tutorial solo utiliza Google como proveedor de autenticación, puede modificar fácilmente el código para utilizar cualquiera de los proveedores. Los pasos que hay que seguir para implementar otros proveedores son muy similares a los que verá en este tutorial.

Además de la autenticación, este tutorial también utiliza roles para implementar la autorización. Únicamente los usuarios que agregue al rol `canEdit` podrán cambiar datos (crear, editar o eliminar contactos).

Los pasos siguientes permiten agregar un proveedor de autenticación de Google.

1.  Abra el archivo *App\_Start\\Startup.Auth.cs*.
2.  Quite los caracteres de comentario del método `app.UseGoogleAuthentication()` para que tenga el siguiente aspecto:

	<pre class="prettyprint">
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "",
	                ClientSecret = ""
	            });
	</pre>

3.  Navegue a la [consola de Google Developers][consola de Google Developers]. Debe iniciar sesión también con su cuenta de correo electrónico de desarrollador de Google (gmail.com). Si no tiene una cuenta de Google, seleccione el vínculo **Crear una cuenta**.

    A continuación, verá la **consola de Google Developers**.
    ![Consola de Google Developers][Consola de Google Developers]

4.  Haga clic en el botón **Crear proyecto** y escriba un nombre de proyecto y un identificador (puede usar los valores predeterminados). Después haga clic en la **casilla del acuerdo** y en el botón **Crear**.  
    ![Google - Nuevo proyecto][Google - Nuevo proyecto]  
    En unos segundos se creará el nuevo proyecto y el explorador mostrará la página del nuevo proyecto.
5.  En el panel izquierdo, haga clic en **APIs y autenticación** y después en **Credenciales**.
6.  Haga clic en **Crear ID de cliente nuevo** en **OAuth**.
    Se muestra el cuadro de diálogo **Crear ID de cliente**.
    ![Google - Crear ID de cliente][Google - Crear ID de cliente]
7.  En el cuadro de diálogo **Crear ID de cliente**, no modifique el valor predeterminado **Aplicación web** para el tipo de aplicación.
8.  Establezca los **Orígenes de JavaScript autorizados** en la dirección URL de SSL que usó antes en este tutorial (**https://localhost:44300/** a menos que haya creado otros proyectos SSL).

    Esta dirección URL es el origen de la aplicación. Para este ejemplo, solo proporcionará la dirección URL de prueba del localhost. Sin embargo, puede especificar varias direcciones URL para localhost y producción.

9.  Establezca el **URI de redireccionamiento autorizado** en lo siguiente:

    ``` prettyprint
    https://localhost:44300/signin-google
    ```

    Este valor es el URI que utiliza ASP.NET OAuth para comunicarse con el servidor OAuth de Google. Recuerde utilizar la dirección URL de SSL que usó antes (**https://localhost:44300/** a menos que haya creado otros proyectos con SSL).

10. Haga clic en el botón **Crear ID de cliente**.
11. En Visual Studio, actualice el método `UseGoogleAuthentication` de la página *Startup.Auth.cs*. Para ello, copie y pegue la información de **Id. de cliente** y de **Secreto de la aplicación** en el método. Los valores **ClientId** y **ClientSecret** que se muestran a continuación son ejemplos y no funcionarán.
	<pre class="prettyprint">  
	using System;
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	using Microsoft.AspNet.Identity.Owin;
	using Microsoft.Owin;
	using Microsoft.Owin.Security.Cookies;
	using Microsoft.Owin.Security.DataProtection;
	using Microsoft.Owin.Security.Google;
	using Owin;
	using ContactManager.Models;
	
	namespace ContactManager
	{
	    public partial class Startup {
	
	        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
	        public void ConfigureAuth(IAppBuilder app)
	        {
	            // Configure the db context and user manager to use a single instance per request
	            app.CreatePerOwinContext(ApplicationDbContext.Create);
	            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);
	
	            // Enable the application to use a cookie to store information for the signed in user
	            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
	            // Configure the sign in cookie
	            app.UseCookieAuthentication(new CookieAuthenticationOptions
	            {
	                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
	                LoginPath = new PathString("/Account/Login"),
	                Provider = new CookieAuthenticationProvider
	                {
	                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
	                        validateInterval: TimeSpan.FromMinutes(20),
	                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
	                }
	            });
	            // Use a cookie to temporarily store information about a user logging in with a third party login provider
	            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
	
	            // Uncomment the following lines to enable logging in with third party login providers
	            //app.UseMicrosoftAccountAuthentication(
	            //    clientId: "",
	            //    clientSecret: "");
	
	            //app.UseTwitterAuthentication(
	            //   consumerKey: "",
	            //   consumerSecret: "");
	
	            //app.UseFacebookAuthentication(
	            //   appId: "",
	            //   appSecret: "");
	
	            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
	            {
	                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
	                ClientSecret = "<mark>00000000000</mark>"
	            });
	        }
	    }
	}
	</pre>  
12. Presione **CTRL + F5** para compilar y ejecutar la aplicación. Haga clic en el vínculo **Iniciar sesión**.
13. Debajo de **Usar otro servicio para iniciar sesión**, haga clic en **Google**.  
    ![Iniciar sesión][Iniciar sesión]
14. Si tiene que proporcionar credenciales, se le redirigirá al sitio de Google donde podrá especificarlas.  
    ![Google - Iniciar sesión][Google - Iniciar sesión]
15. Una vez que haya proporcionado sus credenciales, el sistema le pedirá que le dé permisos a la aplicación web que acaba de crear:  
    ![Cuenta de servicio predeterminada del proyecto][Cuenta de servicio predeterminada del proyecto]
16. Haga clic en **Accept**. Ahora se le redirigirá de nuevo a la página **Registro** de la aplicación **ContactManager** donde puede registrar su cuenta de Google.
    ![Registro con su cuenta de Google][Registro con su cuenta de Google]
17. Tiene la opción de cambiar el nombre de registro de correo electrónico local que usa para su cuenta de Gmail, pero suele ser más práctico mantener el mismo alias de correo electrónico predeterminado (es decir, el que usó para la autenticación). Haga clic en **Iniciar sesión**.

## Uso de la API de suscripción para restringir el acceso

ASP.NET Identity es el sistema de suscripción utilizado para autenticación cuando se compila una aplicación web ASP.NET. Facilita la integración de los datos de perfil específicos del usuario con los datos de aplicación. Además, ASP.NET Identity permite elegir el modelo de persistencia para perfiles de usuario en la aplicación. Puede almacenar los datos en una base de datos de SQL Server o en otro almacén de datos, incluidos almacenes de datos *NoSQL* como las tablas de almacenamiento de Azure.

Al usar la plantilla predeterminada de ASP.NET Web Forms, dispone de funcionalidad de suscripción integrada que puede usar de forma inmediata cuando se ejecuta la aplicación. Deberá usar ASP.NET Identity para agregar un rol de administrador y asignar un usuario a ese rol. A continuación, verá cómo restringir el acceso a la carpeta de administración y a las páginas de esa carpeta que se usan para modificar los datos de contactos.

### Incorporación de un administrador

Con ASP.NET Identity, puede agregar un rol de administrador y asignar un usuario a ese rol usando código.

1.  En el **Explorador de soluciones**, abra el archivo *Configuration.cs* de la carpeta *Migrations*.
2.  Agregue las siguientes instrucciones `using` dentro del espacio de nombres `ContactManger.Migrations`:

    ``` prettyprint
    using Microsoft.AspNet.Identity;using Microsoft.AspNet.Identity.EntityFramework;
    ```

3.  Agregue el siguiente método `AddUserAndRole` a la clase `Configuration` después del método `Seed`:
	<pre class="prettyprint">
        public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
        {
            IdentityResult IdRoleResult;
            IdentityResult IdUserResult;

            var roleStore = new RoleStore&lt;IdentityRole&gt;(context);
            var roleMgr = new RoleManager&lt;IdentityRole&gt;(roleStore);

            if (!roleMgr.RoleExists(&quot;canEdit&quot;))
            {
                IdRoleResult = roleMgr.Create(new IdentityRole { Name = &quot;canEdit&quot; });
            }

            //var userStore = new UserStore&lt;ApplicationUser&gt;(context);
            //var userMgr = new UserManager&lt;ApplicationUser&gt;(userStore);
            var userMgr = new UserManager&lt;ApplicationUser&gt;(new UserStore&lt;ApplicationUser&gt;(context));

            var appUser = new ApplicationUser
            {
                UserName = &quot;canEditUser@wideworldimporters.com&quot;,
                Email = &quot;canEditUser@wideworldimporters.com&quot;
            };
            IdUserResult = userMgr.Create(appUser, &quot;Pa$$word1&quot;);

            if (!userMgr.IsInRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;))
            {
              //  IdUserResult = userMgr.AddToRole(appUser.Id, &quot;canEdit&quot;);
                IdUserResult = userMgr.AddToRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;);
            }
        }
	</pre>
4.  Agregue una llamada al método `AddUserAndRole` desde el principio del método `Seed`. Tenga en cuenta que solo se muestra el principio del método `Seed`.

    ``` prettyprint
     protected override void Seed(ContactManager.Models.ApplicationDbContext context) { AddUserAndRole(context);
    ```

5.  Tras guardar todos los cambios, ejecute el siguiente comando en la ventana **Package Manager Console**:

    ``` prettyprint
    Update-Database
    ```

    Este código crea un nuevo rol denominado `canEdit` y crea un nuevo usuario local con el correo electrónico <canEditUser@wideworldimporters.com>. A continuación, el código agrega <canEditUser@wideworldimporters.com> al rol `canEdit`. Para obtener más información, consulte la página de recursos de [ASP.NET Identity][ASP.NET Identity].

    </p>

### Restricción del acceso a la carpeta de administración

La aplicación de ejemplo **ContactManager** permite ver los contactos tanto a usuarios anónimos como a usuarios que han iniciado sesión. Sin embargo, cuando complete esta sección, los usuarios que hayan iniciado sesión y que tengan asignado el rol "canEdit" serán los únicos que podrán modificar los contactos.

Creará una carpeta denominada *Admin* a la que solo tienen acceso los usuarios que tienen asignado el rol "canEdit".

1.  En el **Explorador de soluciones**, agregue una subcarpeta a la carpeta *Contacts* y dele el nombre *Admin*.
2.  Mueva los archivos siguientes de la carpeta *Contacts* a la carpeta *Contacts/Admin*:
	- *Delete.aspx* y\* Delete.aspx.cs\*
	- *Edit.aspx* y\* Edit.aspx.cs\*
	- *Insert.aspx* y\* Insert.aspx.cs\*
3.  Actualice las referencias a vínculos en *Contacts/Default.aspx* agregando "Admin/" delante de las referencias a páginas que vinculen con *Insert.aspx*, *Edit.aspx* y *Delete.aspx*:
	<pre class="prettyprint">
	&lt;%@ Page Title=&quot;ContactsList&quot; Language=&quot;C#&quot; MasterPageFile=&quot;~/Site.Master&quot; CodeBehind=&quot;Default.aspx.cs&quot; Inherits=&quot;ContactManager.Contacts.Default&quot; ViewStateMode=&quot;Disabled&quot; %&gt;
	&lt;%@ Register TagPrefix=&quot;FriendlyUrls&quot; Namespace=&quot;Microsoft.AspNet.FriendlyUrls&quot; %&gt;
	
	&lt;asp:Content runat=&quot;server&quot; ContentPlaceHolderID=&quot;MainContent&quot;&gt;
	    &lt;h2&gt;Contacts List&lt;/h2&gt;
	    &lt;p&gt;
	        &lt;asp:HyperLink runat=&quot;server&quot; NavigateUrl=&quot;<mark>Admin/</mark>Insert.aspx&quot; Text=&quot;Create new&quot; /&gt;
	    &lt;/p&gt;
	    &lt;div&gt;
	        &lt;asp:ListView runat=&quot;server&quot;
	            DataKeyNames=&quot;ContactId&quot; ItemType=&quot;ContactManager.Models.Contacts&quot;
	            AutoGenerateColumns=&quot;false&quot;
	            AllowPaging=&quot;true&quot; AllowSorting=&quot;true&quot;
	            SelectMethod=&quot;GetData&quot;&gt;
	            &lt;EmptyDataTemplate&gt;
	                There are no entries found for Contacts
	            &lt;/EmptyDataTemplate&gt;
	            &lt;LayoutTemplate&gt;
	                &lt;table class=&quot;table&quot;&gt;
	                    &lt;thead&gt;
	                        &lt;tr&gt;
	                            &lt;th&gt;Name&lt;/th&gt;
	                            &lt;th&gt;Address&lt;/th&gt;
	                            &lt;th&gt;City&lt;/th&gt;
	                            &lt;th&gt;State&lt;/th&gt;
	                            &lt;th&gt;Zip&lt;/th&gt;
	                            &lt;th&gt;Email&lt;/th&gt;
	                            &lt;th&gt;&amp;nbsp;&lt;/th&gt;
	                        &lt;/tr&gt;
	                    &lt;/thead&gt;
	                    &lt;tbody&gt;
	                        &lt;tr runat=&quot;server&quot; id=&quot;itemPlaceholder&quot; /&gt;
	                    &lt;/tbody&gt;
	                &lt;/table&gt;
	            &lt;/LayoutTemplate&gt;
	            &lt;ItemTemplate&gt;
	                &lt;tr&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Name&quot; ID=&quot;Name&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Address&quot; ID=&quot;Address&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;City&quot; ID=&quot;City&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;State&quot; ID=&quot;State&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Zip&quot; ID=&quot;Zip&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Email&quot; ID=&quot;Email&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;a href=&quot;<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Edit&lt;/a&gt; | 
	                        &lt;a href=&quot;<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Delete&lt;/a&gt;
	                    &lt;/td&gt;
	                &lt;/tr&gt;
	            &lt;/ItemTemplate&gt;
	        &lt;/asp:ListView&gt;
	    &lt;/div&gt;
	&lt;/asp:Content&gt;
	</pre>
4.  Actualice las seis referencias del código `Response.Redirect("Default.aspx")` a `Response.Redirect("~/Contacts/Default.aspx")` para los tres archivos siguientes:

    -   *Delete.aspx.cs*
    -   *Edit.aspx.cs*
    -   *Insert.aspx.cs*

    Ahora los vínculos funcionarán todos correctamente cuando muestre y actualice los datos de contactos.

5.  Para restringir el acceso a la carpeta *Admin*, en el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Admin* y seleccione **Agregar nuevo elemento**.
6.  En la lista de plantillas web de Visual C#, seleccione **Archivo de configuración web** en la lista central, acepte el nombre predeterminado *Web.config* y seleccione **Agregar**.
7.  Reemplace el contenido XML del archivo *Web.config* por el siguiente:

    ``` prettyprint
    <?xml version="1.0"?><configuration> <system.web> <authorization> <allow roles="canEdit"/> <deny users="*"/> </authorization> </system.web></configuration>
    ```

8.  Guarde el archivo *Web.config*.
    El archivo *Web.config* especifica que solo los usuarios que tienen asignado el rol "canEdit" puede obtener acceso a las páginas que contienen la carpeta *Admin*.

Cuando un usuario que no forma parte del rol "canEdit" intenta modificar los datos, se le redirige a la página *Iniciar sesión*.

## Implementación de la aplicación con la base de datos de Azure

Ahora que la aplicación web está completa, puede publicarla en Azure.

### Publicación de la aplicación

1.  En Visual Studio, compile el proyecto (**Ctrl + Mayús + B**).
2.  Haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y elija **Publicar**.

    ![Opción de menú Publicar][Opción de menú Publicar]  

    Se muestra el cuadro de diálogo **Publicación web**.  
    ![Cuadro de diálogo Publicación web][5]  
3.  En la pestaña **Perfil**, seleccione **Sitios web Azure** como destino de publicación si no está ya seleccionado.  

    ![Cuadro de diálogo Publicación web][6]  

4.  Haga clic en **Iniciar sesión** si no ha iniciado sesión aún.
5.  Seleccione el sitio web que creó anteriormente en este tutorial en el cuadro de lista desplegable **Sitios web existentes** y haga clic en el botón **Aceptar**.  

    ![Cuadro de diálogo Seleccionar sitio web existente][Cuadro de diálogo Seleccionar sitio web existente]  

    Si el sistema le pregunta si desea guardar los cambios del perfil, seleccione **Sí**.
6.  Haga clic en la pestaña **Configuración**.  

    ![Cuadro de diálogo Seleccionar sitio web existente][7]  

7.  Establezca el cuadro de lista desplegable de **Configuración** en **Depurar**.
8.  Haga clic en el icono de **flecha abajo** que aparece junto a **ApplicationDbContext** y establézcalo en **ContactDB**.
9.  Active la casilla **Ejecutar Migraciones de Code First**.

    > [WACOM.NOTE]
    > 
    > En este ejemplo, debe activar esta casilla solo la primera vez que publique la aplicación. De este modo, solo se llamará una vez al método *Seed* del archivo *Configuration.cs*.

10. Después haga clic en **Publicar**.
    La aplicación se publicará en Azure.

    > [WACOM.NOTE]
    > 
    > Si cerró y volvió a abrir Visual Studio después de crear el perfil de publicación, es posible que no vea la cadena de conexión en la lista desplegable. En ese caso, en lugar de editar el perfil de publicación que había creado, cree otro nuevo de igual modo que hizo antes y siga estos pasos en la pestaña **Configuración**.

### Revisión de la aplicación en Azure

1.  En el explorador, haga clic en el vínculo **Contact Demo**.  
    Se muestra la lista de contactos.
    ![Lista de contactos en el explorador][Lista de contactos en el explorador]

2.  Seleccione **Crear nuevo** en la página **Lista de contactos**.
    ![Lista de contactos en el explorador][8]
    Se le redirige a la página **Iniciar sesión** porque no ha iniciado sesión con una cuenta que pueda modificar contactos.
3.  Cuando haya proporcionado la dirección de correo electrónico y la contraseña que se indican a continuación, haga clic en el botón **Iniciar sesión**.  
    **Correo electrónico**: `canEditUser@wideworldimporters.com`  
    **Contraseña**: `Pa$$word1`  
    ![Página Iniciar sesión][Página Iniciar sesión]

4.  Escriba datos nuevos para cada campo y presione el botón **Insertar**.  
    ![Add New Contact Page][Add New Contact Page]  
    The *EditContactList.aspx* page is displayed with the new record.  
    ![Add New Contact Page][9]

5.  Haga clic en el vínculo **Cerrar sesión**.

### Detención de la aplicación

Con el fin de impedir que otras personas se registren y usen su aplicación de ejemplo, debe detener el sitio web.

1.  En Visual Studio, en el menú **Ver**, seleccione **Explorador de servidores**.
2.  En el **Explorador de servidores**, navegue a **Sitios web**.
3.  Haga clic con el botón secundario en cada instancia del sitio web y seleccione **Detener sitio web**.  
    ![Elemento de menú Detener sitio web][Elemento de menú Detener sitio web]

    También tiene la opción de seleccionar el sitio web en el Portal de administración de Microsoft Azure y hacer clic en el icono **Detener** situado en la parte inferior de la página.  
    ![Página Agregar nuevo contacto][Página Agregar nuevo contacto]

## Revisión de la base de datos

Es importante saber cómo ver y modificar la base de datos directamente. Saber cómo trabajar directamente con la base de datos le permite confirmar los datos de la base de datos y comprender cómo se almacenan en cada tabla.

### Examen de la base de datos SQL de Azure

1.  En Visual Studio, abra el **Explorador de servidores** y navegue a **ContactDB**.
2.  Haga clic con el botón secundario en **ContactDB** y seleccione **Abrir en Explorador de objetos de SQL Server**.

    ![Elemento de menú Abrir en Explorador de objetos de SQL Server][Elemento de menú Abrir en Explorador de objetos de SQL Server]

3.  Si se muestra el cuadro de diálogo **Agregar regla de firewall** seleccione **Agregar regla de firewall**.

    > [WACOM.NOTE]
    > 
    > Si no puede expandir **Bases de datos SQL** y no puede ver **ContactDB** en Visual Studio, siga estas instrucciones para abrir un puerto o intervalo de puertos de firewall. Para ello, siga las instrucciones que se proporcionan en **Configuración de reglas de firewall de Azure** casi al final del [Tutorial de MVC][Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos de SQL en un sitio web de Azure]. También puede revisar los datos de la base de datos local compilando, ejecutando y agregando datos a la aplicación en modo local (**CTRL + F5** desde Visual Studio).

4.  Si se muestra el cuadro de diálogo **Conectar con el servidor**, escriba la **contraseña** que creó al principio de este tutorial y presione el botón **Conectar**.

    > [WACOM.NOTE]
    > 
    > Si no recuerda la contraseña, puede buscarla en el archivo de proyecto local. En el **Explorador de soluciones**, expanda las carpetas *Properties* y *PublishProfiles*. Abra el archivo *contactmanager.pubxml* (su archivo puede tener otro nombre). Busque su contraseña de publicación en el archivo.

5.  Expanda la base de datos **contactDB** y después **Tablas**.
6.  Haga clic con el botón secundario en la tabla **dbo.AspNetUsers** y seleccione **Ver datos**.  
    ![Elemento de menú Ver datos][Elemento de menú Ver datos]  
    Puede ver los datos asociados con el usuario <canEditUser@contoso.com>.  
    ![Ventana ContactManager][Ventana ContactManager]

### Incorporación de un usuario al rol Admin editando la base de datos

Anteriormente en este tutorial, utilizamos código para agregar usuarios al rol canEdit. Existe un método alternativo que consiste en manipular directamente los datos de las tablas de suscripciones. Los pasos siguientes muestran cómo utilizar este método alternativo para agregar un usuario a un rol.

1.  En el **Explorador de objetos de SQL Server**, haga clic con el botón secundario en **dbo.AspNetUserRoles** y seleccione **Ver datos**.
    ![Datos de AspNetUserRoles][Datos de AspNetUserRoles]
2.  Copie el *RoleId* y péguelo en la fila vacía (nueva).  
    ![Datos de AspNetUserRoles][10]
3.  En la tabla **dbo.AspNetUsers**, busque el usuario que desea agregar al rol y copie su *Id*.
4.  Pegue el *Id* copiado en el campo **UserId** de la nueva fila en la tabla **AspNetUserRoles**.

> [WACOM.NOTE]
> 
> Estamos trabajando en una herramienta que facilitará mucho la tarea de administrar los usuarios y roles.

## Pasos siguientes

Para obtener más información acerca de ASP.NET Web Forms, vea [ASP.NET Web Forms][ASP.NET Web Forms] en el sitio web de ASP.NET y en [Tutoriales y guías de Microsoft Azure][Tutoriales y guías de Microsoft Azure].

Este tutorial está basado en el tutorial para MVC[Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos SQL en un Sitio web Azure][Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos de SQL en un sitio web de Azure] escrito por Rick Anderson (Twitter [@RickAndMSFT][@RickAndMSFT]) con ayuda de Tom Dykstra y Barry Dorrans (Twitter [@blowdart][@blowdart]).

Es importante que haga comentarios acerca de lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. También puede solicitar y votar nuevos temas en [Show Me How With Code][Show Me How With Code].

  [Implementación de una aplicación ASP.NET MVC 5 segura con suscripción, OAuth y Base de datos de SQL en un sitio web de Azure]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Página Contactos - Editar]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png
  [activar los beneficios de suscripción a MSDN]: /es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [regístrese para obtener una evaluación gratuita]: /es-es/pricing/free-trial/?WT.mc_id=A261C142F
  [Configuración del entorno de desarrollo]: #set-up-the-development-environment
  [Configuración del entorno de Azure]: #Set-up-the-Azure-environment
  [Creación de una aplicación de ASP.NET Web Forms]: #Create-an-ASP.NET-Web-Forms-Application
  [Incorporación de una base de datos a la aplicación]: #Add-a-Database-to-the-Application
  [Habilitación de SSL para el proyecto]: #Enable-SSL-for-the-Project
  [Incorporación de un proveedor de OAuth 2.0]: #Add-an-OAuth-2.0-Provider
  [Uso de la API de suscripción para restringir el acceso]: #Use-the-Membership-API-to-Restrict-Access
  [Implementación de la aplicación con la base de datos de Azure]: #Deploy-the-Application-with-the-Database-to-Azure
  [Revisión de la base de datos]: #Review-the-Database
  [Pasos siguientes]: #Next-Steps
  [Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=306566
  [SDK de Azure para Visual Studio 2013]: http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409
  [Web Platform Installer]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png
  [Visual Studio 2013 Update 2]: http://www.microsoft.com/download/details.aspx?id=42666
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Instalador de plataforma web]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png
  [Creación personalizada]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png
  [Contactos - Crear nuevo sitio web]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png
  [Database Settings]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png
  [File Menu - New Project]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png
  [Cuadro de diálogo Nuevo proyecto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png
  [Cuadro de diálogo Nuevo proyecto de ASP.NET]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png
  [1]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png
  [Seleccione Publicar]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png
  [Cuadro de diálogo Publicación web]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png
  [Cuadro de diálogo Seleccionar el sitio web existente]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png
  [2]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png
  [Actividad de publicación web]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png
  [Aplicación en el explorador]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png
  [3]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png
  [Seleccione Clase]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png
  [Cuadro de diálogo Agregar nuevo elemento]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png
  [Cuadro de diálogo Extensiones y actualizaciones]: ./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png
  [Cuadro de diálogo Add Scaffold]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png
  [Cuadro de diálogo Add Web Forms Pages]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png
  [4]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png
  [Package Manager Console.]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png
  [Página Lista de contactos]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png
  [Project Properties]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png
  [Propiedades web del proyecto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png
  [Detalles de certificado SSL de IIS Express]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png
  [Cuadro de diálogo Advertencia de seguridad]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png
  [consola de Google Developers]: https://console.developers.google.com/
  [Consola de Google Developers]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png
  [Google - Nuevo proyecto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png
  [Google - Crear ID de cliente]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png
  [Iniciar sesión]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png
  [Google - Iniciar sesión]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png
  [Cuenta de servicio predeterminada del proyecto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png
  [Registro con su cuenta de Google]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png
  [ASP.NET Identity]: http://www.asp.net/identity
  [Opción de menú Publicar]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png
  [5]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png
  [6]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png
  [Cuadro de diálogo Seleccionar sitio web existente]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png
  [7]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png
  [Lista de contactos en el explorador]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png
  [8]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png
  [Página Iniciar sesión]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png
  [Add New Contact Page]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png
  [9]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png
  [Elemento de menú Detener sitio web]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png
  [Página Agregar nuevo contacto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png
  [Elemento de menú Abrir en Explorador de objetos de SQL Server]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png
  [Elemento de menú Ver datos]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png
  [Ventana ContactManager]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png
  [Datos de AspNetUserRoles]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png
  [10]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png
  [ASP.NET Web Forms]: http://www.asp.net/web-forms
  [Tutoriales y guías de Microsoft Azure]: http://azure.microsoft.com/es-es/documentation/services/web-sites/#net
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Show Me How With Code]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
