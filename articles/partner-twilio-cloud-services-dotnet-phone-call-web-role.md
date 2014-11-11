<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="MicrosoftHelp@twilio.com; larryf" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf" />

# Realización de una llamada telefónica con Twilio en un rol web en Azure

En esta guía se describe cómo usar Twilio para realizar una llamada desde una página web hospedada en Azure. La aplicación resultante pide al usuario los valores de la llamada telefónica, como se muestra en la siguiente captura de pantalla.

![Formulario de llamada de Azure con Twilio y ASP.NET][Formulario de llamada de Azure con Twilio y ASP.NET]

## Tabla de contenido

-   [Requisitos previos][Requisitos previos]
-   [Direccionamiento de un formulario web para hacer una llamada][Direccionamiento de un formulario web para hacer una llamada]
-   [Direccionamiento del código para realizar la llamada][Direccionamiento del código para realizar la llamada]
-   [Pasos siguientes][Pasos siguientes]
-   [Otras referencias][Otras referencias]

## <a name="twilio-prereqs"></a>Requisitos previos

Tendrá que hacer lo siguiente para usar el código de este tema:

1.  Adquiera una cuenta de Twilio y un token de autenticación. Para empezar con Twilio, inscríbase en [][]<https://www.twilio.com/try-twilio></a>. Evalúe los precios en [][1]<http://www.twilio.com/pricing></a>. Para obtener información acerca de la API proporcionada por Twilio, consulte [][2]<http://www.twilio.com/voice/api></a>.
2.  Compruebe el número de teléfono con Twilio. Para obtener información acerca de cómo comprobar su número de teléfono, consulte [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Como alternativa a utilizar un número existente, puede comprar un número de teléfono de Twilio.
    A los efectos de este ejemplo, usará el número de teléfono de Sandbox de Twilio para enviar un mensaje al número de teléfono confirmado. Solo se puede usar el número de teléfono de Sandbox para el envío a números de teléfono confirmados.
3.  Agregue la biblioteca .NET de Twilio al rol web. Consulte el apartado "Para agregar las bibliotecas de Twilio al proyecto de rol web", más adelante en este tema.

Debe estar familiarizado con la creación de un rol web básico en Azure.

## <a name="howtocreateform"></a>Direccionamiento del Creación de un formulario web para hacer una llamada

### <span id="use_nuget"></span></a>Para agregar las bibliotecas de Twilio al proyecto de rol web:

1.  Abra su solución en Visual Studio.
2.  Haga clic con el botón secundario en **Referencias**.
3.  Haga clic en **Administrar paquetes de NuGet**.
4.  Haga clic en **En línea**.
5.  En el cuadro de búsqueda en línea, escriba *twilio*.
6.  Haga clic en **Instalar** en el paquete de Twilio.

El siguiente código muestra cómo crear un formulario web para recuperar datos de usuario para hacer una llamada. En este ejemplo, se crea un rol web de ASP.NET denominado **TwilioCloud**.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <span id="howtocreatecode"></span></a>Direccionamiento del Creación del código para realizar la llamada

El siguiente código, al que se llama cuando el usuario completa el formulario, crea el mensaje de llamada y genera la llamada. En este ejemplo, el código se ejecuta en el controlador de eventos onclick del botón en el formulario. (Use la cuenta de Twilio y el token de autenticación en lugar de los valores de marcador de posición asignados a **accountSID** y **authToken** en el código a continuación).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

Se realiza la llamada y se muestran el extremo de Twilio, la versión de la API y el estado de la llamada. En la captura de pantalla siguiente se muestra el resultado de una ejecución de muestra.

![Respuesta de llamada de Azure con Twilio y ASP.NET][Respuesta de llamada de Azure con Twilio y ASP.NET]

Para obtener más información sobre TwiML, consulte [][4][https://www.twilio.com/docs/api/twiml][4]</a>. Puede encontrar más información sobre Say y otros verbos de Twilio en [][5]<http://www.twilio.com/docs/api/twiml/say></a>.

## <span id="nextsteps"></span></a>Pasos siguientes

Este código se proporciona para mostrar la funcionalidad básica del uso de Twilio en un rol web de ASP.NET en Azure. Antes de implementarlo en Azure en producción, es posible que desee agregar más controles de errores u otras características. Por ejemplo:

-   En lugar de usar un formulario web, puede usar el almacenamiento de blobs de Azure o una instancia de Base de datos SQL de Azure para almacenar los números de teléfono y el texto de llamada. Para obtener información acerca de cómo usar los blobs en Azure, consulte [Uso del servicio de almacenamiento de blobs de Azure en .NET][Uso del servicio de almacenamiento de blobs de Azure en .NET]. Para obtener información acerca de cómo usar la base de datos SQL, consulte [Uso de Base de datos SQL de Azure en aplicaciones .NET][Uso de Base de datos SQL de Azure en aplicaciones .NET].
-   Puede usar RoleEnvironment.getConfigurationSettings para recuperar el identificador de la cuenta de Twilio y el token de autenticación desde los ajustes de configuración de su implementación, en vez de codificar de forma rígida los valores en el formulario. Para obtener información acerca de la clase RoleEnvironment, consulte [Microsoft.WindowsAzure.ServiceRuntime (Espacio de nombres)][Microsoft.WindowsAzure.ServiceRuntime (Espacio de nombres)].
-   Lea las directrices de seguridad de Twilio en [][6]<https://www.twilio.com/docs/security></a>.
-   Obtenga más información acerca de Twilio en [][7]<https://www.twilio.com/docs></a>.

## <a name="seealso"></a>Otras referencias

-   [Uso de Twilio para funciones de voz y SMS desde Azure][Uso de Twilio para funciones de voz y SMS desde Azure]

  [Formulario de llamada de Azure con Twilio y ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [Requisitos previos]: #twilio-prereqs
  [Direccionamiento del código para realizar la llamada]: #howtocreatecode
  [Pasos siguientes]: #nextsteps
  [Otras referencias]: #seealso
  []: http://www.twilio.com/try-twilio
  [1]: http://www.twilio.com/pricing
  [2]: http://www.twilio.com/voice/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Respuesta de llamada de Azure con Twilio y ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [4]: http://www.twilio.com/docs/api/twiml
  [5]: http://www.twilio.com/docs/api/twiml/say
  [Uso del servicio de almacenamiento de blobs de Azure en .NET]: https://www.windowsazure.com/es-es/develop/net/how-to-guides/blob-storage/
  [Uso de Base de datos SQL de Azure en aplicaciones .NET]: https://www.windowsazure.com/es-es/develop/net/how-to-guides/sql-database/
  [6]: http://www.twilio.com/docs/security
  [7]: http://www.twilio.com/docs
  [Uso de Twilio para funciones de voz y SMS desde Azure]: ../twilio-dotnet-how-to-use-for-voice-sms/
