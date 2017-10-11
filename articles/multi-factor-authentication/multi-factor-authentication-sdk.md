---
title: Kit de desarrollo de software MFA para aplicaciones personalizadas | Microsoft Docs
description: "En este artículo se muestra cómo descargar y usar el SDK de Azure MFA para comprobar en dos pasos sus aplicaciones personalizadas."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1c152f67-be02-42a5-a0c7-246fb6b34377
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.openlocfilehash: 281f9c61a30a20027f69808600373aa272255ef6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Creación de Multi-Factor Authentication en aplicaciones personalizadas (SDK)

El Kit de desarrollo de Software (SDK) de Multi-Factor Authentication de Azure le permite generar una comprobación en dos pasos directamente en los procesos de inicio de sesión o transacción de las aplicaciones de su inquilino de Azure AD.

El SDK de Multi-Factor Authentication está disponible para C#, Visual Basic (.NET), Java, Perl, PHP y Ruby. El SDK proporciona un contenedor fino en torno a la comprobación en dos pasos. Incluye todo lo que necesita para escribir su código, incluidos los archivos de código fuente comentados, los archivos de ejemplo y un archivo Léame detallado. Cada SDK también incluye un certificado y una clave privada para cifrar transacciones que son únicos para su proveedor de Multi-Factor Authentication. Siempre y cuando tenga un proveedor, puede descargar el SDK en tantos idiomas y formatos como necesite.

La estructura de las API del SDK de Multi-Factor Authentication es sencilla. Cree una llamada de función única a una API con los parámetros de la opción de varios factores (como el modo de comprobación) y los datos de usuario (como el número de teléfono al que llamar o el número PIN que validar). Las API convierten la llamada de función en solicitudes de servicios web al servicio Azure Multi-Factor Authentication basado en la nube. Todas las llamadas deben incluir una referencia al certificado privado que se incluye en todos los SDK.

Dado que las API no tienen acceso a los usuarios registrados en Azure Active Directory, debe proporcionar información de usuario en un archivo o una base de datos. Además, las API no proporcionan funciones de administración de inscripciones o usuarios, por lo que necesitará crear estos procesos en la aplicación.

> [!IMPORTANT]
> Para descargar el SDK, debe crear un proveedor de Multi-Factor Auth de Azure, incluso si tiene licencias de Azure MFA, AAD Premium o EMS. Si crea un proveedor de Multi-Factor Auth de Azure para este propósito y ya tiene licencias, asegúrese de crear el proveedor con el modelo **Por usuario habilitado**. A continuación, vincule el proveedor al directorio que contiene las licencias de Azure MFA, Azure AD Premium o EMS. Gracias a esta configuración, se asegura de que no se le cobrará, salvo que tenga más usuarios únicos mediante el SDK que el número de licencias que posee.


## <a name="download-the-sdk"></a>Descarga del SDK
Para descargar el SDK de Azure Multi-Factor, se requiere un [proveedor de autenticación multifactor](multi-factor-authentication-get-started-auth-provider.md).  Para ello, se necesita una suscripción de Azure completa, aunque se cuenten con licencias de Azure MFA, Azure AD Premium o Enterprise Mobility Suite.  Para descargar el SDK, vaya al Portal de administración de Multi-Factor. Puede tener acceso al portal administrando directamente el proveedor de Multi-Factor Auth o haciendo clic en el vínculo **"Ir al portal"** de la página de configuración del servicio MFA.

### <a name="download-from-the-azure-classic-portal"></a>Descarga desde el Portal de Azure clásico
1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, seleccione **Active Directory**.
3. En la parte superior de la página Active Directory, seleccione **Proveedores de Multi-Factor Auth**
4. En la parte inferior, seleccione **Administrar**. Se abrirá una nueva página.
5. A la izquierda, en la parte inferior, haga clic en **SDK**.
   <center>![Descargar](./media/multi-factor-authentication-sdk/download.png)</center>
6. Seleccione el idioma que desee y haga clic en uno de los vínculos de descarga asociados.
7. Guarde el archivo descargado.

### <a name="download-from-the-service-settings"></a>Descarga desde la configuración del servicio
1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, seleccione **Active Directory**.
3. Haga doble clic en la instancia de Azure AD.
4. En la parte superior, haga clic en **Configurar**
5. En Multi-Factor Authentication, seleccione **Administrar configuración del servicio**
   ![Descargar](./media/multi-factor-authentication-sdk/download2.png)
6. En la parte inferior de la página de configuración de servicios, haga clic en **Ir al portal**. Se abrirá una nueva página.
   ![Descargar](./media/multi-factor-authentication-sdk/download3a.png)
7. A la izquierda, en la parte inferior, haga clic en **SDK**.
8. Seleccione el idioma que desee y haga clic en uno de los vínculos de descarga asociados.
9. Guarde el archivo descargado.

## <a name="whats-in-the-sdk"></a>Qué contiene el SDK
El SDK incluye los siguientes elementos:

* **LÉAME**. Explica cómo usar las API de Multi-Factor Authentication en una aplicación nueva o existente.
* **Archivos de origen** para Multi-Factor Authentication
* **Certificado de cliente** que se usa para comunicarse con el servicio Multi-Factor Authentication
* **Clave privada** para el certificado
* **Resultados de la llamada.** Una lista de códigos de resultado de la llamada. Para abrir este archivo, use una aplicación con formato de texto, como WordPad. Use los códigos de resultado de la llamada para probar y solucionar problemas de la implementación de Multi-Factor Authentication en su aplicación. No son códigos de estado de autenticación.
* **Ejemplos.** Código de ejemplo para una implementación básica de funcionamiento de Multi-Factor Authentication.

> [!WARNING]
> El certificado de cliente es un certificado privado único que se generó especialmente para usted. No comparta ni pierda este archivo. Es su clave para garantizar la seguridad de las comunicaciones con el servicio Multi-Factor Authentication.

## <a name="code-sample"></a>Código de ejemplo
Este ejemplo de código muestra cómo usar las API en el SDK de Azure Multi-Factor Authentication para agregar comprobación de llamadas de voz de modo estándar a la aplicación. El modo estándar es una llamada de teléfono a la que el usuario responde presionando la tecla #.

Este ejemplo usa el SDK de Multi-Factor Authentication C# .NET 2.0 en una aplicación ASP.NET básica con lógica de servidor de C#, pero el proceso es similar en otros idiomas. Dado que el SDK incluye archivos de origen, no archivos ejecutables, puede generar los archivos y hacer referencia a ellos o incluirlos directamente en la aplicación.

> [!NOTE]
> Al implementar Multi-Factor Authentication, use métodos adicionales (llamada de teléfono o mensaje de texto) como la comprobación secundaria o terciaria para complementar el método de autenticación principal (nombre de usuario y contraseña). Estos métodos no están diseñados como métodos de autenticación principal.

### <a name="code-sample-overview"></a>Información general del ejemplo de código
Este código de ejemplo para una aplicación de demostración web sencilla usa una llamada telefónica con una respuesta de clave # para comprobar la autenticación del usuario. Este factor de llamada de teléfono se conoce en la Multi-Factor Authentication como modo estándar.

El código de cliente no incluye ningún elemento específico de Multi-Factor Authentication. Dado que los factores de autenticación adicionales son independientes de la autenticación principal, puede agregarlos sin cambiar la interfaz de inicio de sesión existente. Las API del SDK multifactor le permiten personalizar la experiencia del usuario, pero es posible que no tenga que cambiar nada en absoluto.

El código de servidor agrega autenticación de modo estándar en el paso 2. Crea un objeto PfAuthParams con los parámetros necesarios para la comprobación de modo estándar: nombre de usuario, número de teléfono y modo, y la ruta de acceso al certificado de cliente (CertFilePath), que es necesario en cada llamada. Para obtener una demostración de todos los parámetros en PfAuthParams, consulte el archivo de ejemplo en el SDK.

A continuación, el código pasa el objeto PfAuthParams a la función pf_authenticate(). El valor devuelto indica el éxito o fracaso de la autenticación. Los parámetros de salida, callStatus y errorID, contienen información adicional del resultado de la llamada. Los códigos de resultado de la llamada se documentan en el archivo de resultados de la llamada en el SDK.

Esta implementación mínima puede escribirse en unas pocas líneas. Sin embargo, en el código de producción, incluiría un tratamiento de errores más sofisticado, código de la base de datos adicional y una experiencia de usuario mejorada.

### <a name="web-client-code"></a>Código de cliente web
A continuación se facilita código de cliente web para una página de demostración.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Código de servidor
En el siguiente código de servidor, Multi-Factor Authentication se configura y ejecuta en el paso 2. El modo estándar (MODE_STANDARD) es una llamada de teléfono a la que el usuario responde presionando la tecla #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
