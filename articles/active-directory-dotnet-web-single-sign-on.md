<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Web Single Sign-On with .NET and Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# Inicio de sesión web único con .NET y Azure Active Directory

## <a name="introduction"></a>Introducción

Este tutorial le mostrará cómo utilizar Azure Active Directory para permitir el inicio de sesión único para usuarios de clientes de Office 365. Aprenderá a:

-   Aprovisionar la aplicación web en el inquilino de un cliente
-   Proteger la aplicación con WS-Federation

### Requisitos previos

Para este tutorial se necesitan los siguientes requisitos previos para el entorno de desarrollo:

-   Visual Studio 2010 SP1
-   Microsoft .NET Framework 4.0
-   [ASP.NET MVC 3][ASP.NET MVC 3]
-   [Tiempo de ejecución de Windows Identity Foundation 1.0][Tiempo de ejecución de Windows Identity Foundation 1.0]
-   [SDK de Windows Identity Foundation 3.5][SDK de Windows Identity Foundation 3.5]
-   Internet Information Services (IIS) 7.5 con SSL habilitado
-   Windows PowerShell
-   [Instalar y configurar Windows PowerShell para el inicio de sesión único][Instalar y configurar Windows PowerShell para el inicio de sesión único]

### Tabla de contenido

-   [Introducción][Introducción]
-   [Paso 1: Crear una aplicación ASP.NET MVC][Paso 1: Crear una aplicación ASP.NET MVC]
-   [Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa][Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa]
-   [Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado][Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado]
-   [Resumen][Resumen]

## <a name="createapp"></a>Paso 1: Crear una aplicación ASP.NET MVC

Este paso describe cómo crear una aplicación ASP.NET MVC 3 sencilla que representará un recurso protegido. El acceso a este recurso se otorgará a través de la autenticación federada administrada por el STS de la empresa, que se describe más adelante en este tutorial.

1.  Abra Visual Studio como administrador.
2.  En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3.  En el menú de plantillas que aparece a la izquierda, seleccione **Web** y, a continuación, haga clic en **Aplicación web de ASP.NET MVC 3**. Asigne al proyecto el nombre *OrgIdFederationSample*.
4.  En el cuadro de diálogo **New ASP.NET MVC 3 Project**, seleccione la plantilla **Empty** y, a continuación, haga clic en **Aceptar**.
5.  Una vez que Visual Studio haya generado el proyecto nuevo, haga clic con el botón secundario en la carpeta **Controllers** en el **Explorador de soluciones**, haga clic en **Add** y, a continuación, en **Controller**.
6.  En el cuadro de diálogo **Add Controller**, asigne al controlador nuevo el nombre *HomeController.cs* y, a continuación, haga clic en **Add**.
7.  Se creará y abrirá el archivo **HomeController.cs**. En el archivo de código, haga clic con el botón secundario en el método ***Index()*** y luego haga clic en **Add View...**.
8.  En el cuadro de diálogo **Add View**, haga clic en **Aceptar**. Se creará un archivo **Index.cshtml** en una carpeta nueva llamada **Home**.
9.  Haga clic con el botón secundario en su **OrgIdFederationSample** en el **Explorador de soluciones** y, a continuación, haga clic en **Properties**.
10. En la ventana de propiedades, haga clic en **Web** en el menú que aparece a la izquierda y, a continuación, seleccione **Use Local IIS Web server**. Puede que aparezca un cuadro de diálogo que le pida crear un directorio virtual para el proyecto; haga clic en **Sí**.
11. Compile y ejecute la aplicación. Aparecerá la página de índice de su controlador Home.

## <a name="provisionapp"></a>Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa

Este paso describe cómo un administrador de un cliente de Azure Active Directory aprovisiona la aplicación MVC en su inquilino y configura el inicio de sesión único. Una vez que se completa este paso, los empleados de la empresa se pueden autenticar en la aplicación web mediante sus cuentas de Office 365.

El proceso de aprovisionamiento comienza con la creación de una nueva entidad de servicio para la aplicación. Azure Active Directory utiliza las entidades de servicio para registrar y autenticar las aplicaciones en el directorio.

1.  Si todavía no lo ha hecho, descargue e instale los commandlets de PowerShell para Office 365.
2.  En el menú **Inicio**, ejecute la consola **Módulo Microsoft Online Services para Windows PowerShell**. Esta consola proporciona un entorno de línea de comandos para configurar atributos acerca de su inquilino de Office 365, tales como la creación y la modificación de entidades de servicio.
3.  Para importar el módulo **MSOnlineExtended** requerido, escriba el siguiente comando y presione Entrar:

        Import-Module MSOnlineExtended -Force

4.  Para conectarse a su directorio de Office 365, necesitará proporcionar las credenciales de administrador de la empresa. Escriba el siguiente comando y presione Entrar y, a continuación, escriba las credenciales en el símbolo del sistema:

        Connect-MsolService

5.  Ahora creará una nueva entidad de servicio para la aplicación. Escriba el siguiente comando y presione Entrar:

        New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Este paso dará como resultado información similar a la siguiente:

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Website
        ServicePrincipalNames : {OrgIdFederationSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    <div class="dev-callout"><strong>Nota:</strong><p>debe guardar esta salida, en especial la clave sim&eacute;trica generada. La clave solo se revelar&aacute; durante la creaci&oacute;n de la entidad de servicio y no podr&aacute; recuperarla en el futuro. Los otros valores son necesarios para utilizar la API Graph para leer y escribir informaci&oacute;n en el directorio.</p></div>

6.  El paso final define la URL de respuesta para su aplicación. La URL de respuesta es donde se envían las respuestas después de los intentos de autenticación. Escriba los siguientes comandos y presione Entrar:

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

La aplicación web ahora se aprovisionó en el directorio y los empleados de la empresa la pueden utilizar para el inicio de sesión web único.

## <a name="protectapp"></a>Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado

Este paso le muestra cómo agregar compatibilidad con el inicio de sesión federado con Windows Identity Foundation (WIF). También agregará una página de inicio de sesión y configurará la confianza entre la aplicación y el inquilino de directorio.

1.  En Visual Studio, haga clic con el botón secundario en el proyecto **OrgIdFederationSample** y seleccione **"Add STS reference..."**. La opción del menú contextual se agregó cuando instaló el SDK de WIF.
2.  En el cuadro de diálogo **Federation Utility** en **Application URI**, deberá escribir el URI en el siguiente formato:

        spn:<Your AppPrincipalId>@<Your Directory Domain>

    **spn** especifica que el URI es el nombre de una entidad de servicio, **Your AppPrincpalId** es el valor de GUID de **AppPrincipalId** que se generó cuando creó una entidad de servicio y **Your Directory Domain** es el dominio \*.onmicrosoft.com para el inquilino de directorio. Para esta aplicación de ejemplo, se especifica un valor de URI de aplicación completo tal como se muestra a continuación:

        spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

    Una vez que haya escrito el URI de la aplicación, haga clic en **Next**.

3.  Aparecerá un cuadro de diálogo con el mensaje "The application is not hosted on a secure https connection". Esto ocurre porque la utilidad de federación no reconoce el formato **spn:**, pero de todos modos la aplicación seguirá utilizando una conexión https segura. Haga clic en **Sí** para continuar.

4.  En la página siguiente de la utilidad de federación, seleccione **Use an existing STS** y en **STS WS-Federation metadata document location**, escriba la dirección URL del documento de metadatos de WS-Federation. Esta URL se especifica en el siguiente formato:

        https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

    Para esta aplicación, la ubicación de los metadatos de WS-Federation se especifica de la siguiente manera:

        https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

    Una vez que haya escrito la ubicación de los metadatos, haga clic en **Next**.

5.  En la página siguiente de la utilidad de federación, seleccione **Disable certificate chain validation** y, a continuación, haga clic en **Next**.

6.  En la página siguiente de la utilidad de federación, seleccione **No encryption** y, a continuación, haga clic en **Next**.

7.  La página siguiente de la utilidad de federación muestra las notificaciones que proporciona el STS. Revise las notificaciones y haga clic en **Next**.

8.  A continuación, configurará Internet Information Services (IIS) para admitir SSL en su entorno de desarrollo. Para abrir el Administrador de IIS, escriba *inetmgr* en el símbolo del sistema **Ejecutar**.

9.  En el Administrador de IIS, expanda la carpeta **Sitios** en el menú de la izquierda y, a continuación, haga clic en **Página principal de sitio web predeterminado**. En el menú **Actions** que aparece a la derecha, haga clic en **Bindings...**.

10. En el cuadro de diálogo **Site Bindings**, haga clic en **Add**. En el cuadro de diálogo **Add Site Binding**, cambie la lista desplegable **Type** por ***https*** y, a continuación, en **SSL certificate**, seleccione **IIS Express Development Certificate**. Haga clic en **OK**.

11. En el Administrador de IIS, haga clic en **Application Pools** en el menú de la izquierda y luego seleccione la entrada **ASP.NET v4.0** de la lista y, a continuación, haga clic en **Advanced Settings** en el menú **Actions** de la derecha.

12. En el cuadro de diálogo **Advanced Settings**, defina la propiedad **Load User Profile** en **true**. Haga clic en **OK**.

13. En Visual Studio, abra el archivo **Web.config** desde el **Explorador de soluciones** en la raíz del proyecto.

14. En el archivo **Web.config**, ubique la sección **wsFederation** y agregue un atributo **reply** con el mismo valor que la variable **$replyUrl** que especificó cuando creó la entidad de servicio. Por ejemplo:

        <wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. Agregue un nodo **httpRuntime** dentro de la sección **system.web** con el atributo **requestValidationMode** definido en **2.0**. Por ejemplo:

        <system.web>
            <httpRuntime requestValidationMode="2.0" /> 
            ...

    Guarde el archivo **Web.config**.

16. Ahora que ha habilitado la autenticación de la aplicación web, se debe modificar la página de **índice** para que presente las notificaciones de un usuario autenticado. Abra el archivo **Index.cshtml** ubicado en la carpeta **Home** de la carpeta **Views**.

17. Agregue el siguiente fragmento de código al archivo **Index.cshtml** y guárdelo:

        <p>
            @if (User.Identity.IsAuthenticated)
            {
            <ul>
                @foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
                {
                    <li>@claim</li>
                }
            </ul>
            }
        </p> 

18. Una vez que haya guardado los cambios del archivo **Index.cshtml**, presione **F5** para ejecutar la aplicación. Se le redirigirá automáticamente a la página del proveedor de identidades de Office 365, donde puede iniciar sesión con sus credenciales de inquilino de directorio. Por ejemplo, *john.doe@awesomecomputers.onmicrosoft.com*.

19. Una vez que haya iniciado sesión con sus credenciales, se le redirigirá a la página de índice de su controlador Home, donde aparecen las notificaciones de su cuenta. Con esto se demuestra que un usuario se autenticó correctamente en la aplicación a través del inicio de sesión único que proporciona Azure Active Directory.

## <a name="summary"></a>Resumen

Este tutorial le ha mostrado cómo crear y configurar una aplicación de un solo inquilino que utiliza las capacidades de inicio de sesión único de Azure Active Directory. Lea el siguiente tutorial para saber cómo crear aplicaciones multiempresa para Azure Active Directory: [Desarrollo de aplicaciones multiempresa en la nube con Azure Active Directory][Desarrollo de aplicaciones multiempresa en la nube con Azure Active Directory].

  [ASP.NET MVC 3]: http://www.microsoft.com/es-es/download/details.aspx?id=4211
  [Tiempo de ejecución de Windows Identity Foundation 1.0]: http://www.microsoft.com/es-es/download/details.aspx?id=17331
  [SDK de Windows Identity Foundation 3.5]: http://www.microsoft.com/es-es/download/details.aspx?id=4451
  [Instalar y configurar Windows PowerShell para el inicio de sesión único]: http://onlinehelp.microsoft.com/es-es/office365-enterprises/ff652560.aspx
  [Introducción]: #introduction
  [Paso 1: Crear una aplicación ASP.NET MVC]: #createapp
  [Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa]: #provisionapp
  [Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado]: #protectapp
  [Resumen]: #summary
  [Desarrollo de aplicaciones multiempresa en la nube con Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
