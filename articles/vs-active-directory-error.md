<properties title="Error durante la detecci&oacute;n de autenticaci&oacute;n" pageTitle="Error durante la detecci&oacute;n de autenticaci&oacute;n" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Error durante la detección de autenticación

Al detectar el código de autenticación anterior, el asistente detectó un tipo de autenticación incompatible.

##### ¿Qué se está comprobando?

El asistente trata de detectar versiones de código de autenticación que se hayan configurado con versiones anteriores de Visual Studio. Si recibió este error, significa el proyecto contiene un tipo de autenticación incompatible. El asistente detecta los siguientes tipos de autenticación de las versiones anteriores de Visual Studio:

-   Autenticación de Windows
-   Cuentas de usuario individuales
-   Cuentas organizativas

Para detectar la autenticación de Windows en un proyecto MVC, el asistente busca el elemento `authentication` en el archivo **web.config**.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Para detectar la autenticación de Windows en un proyecto Web API, el asistente busca el elemento `IISExpressWindowsAuthentication` en el archivo **.csproj** del proyecto.

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

Para detectar la autenticación de cuentas de usuario individuales, el asistente busca el elemento de paquete en el archivo **Packages.config**.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

Para detectar una forma anterior de autenticación con la cuenta de una organización, el asistente busca el siguiente elemento en **web.config**:

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Para cambiar el tipo de autenticación, quite el tipo de autenticación incompatible y ejecute de nuevo el asistente.

Para obtener más información, consulte [Escenarios de autenticación en Azure AD][Escenarios de autenticación en Azure AD].

  [Escenarios de autenticación en Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
