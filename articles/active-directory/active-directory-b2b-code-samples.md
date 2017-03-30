---
title: "Ejemplos de código y PowerShell para la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Ejemplos de código y PowerShell para la colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 51c0f98e3d3ad09e3e6675b2692bc2a8888db9a7
ms.lasthandoff: 03/17/2017


---


# <a name="azure-active-directory-b2b-collaboration-code-and-powershell-samples"></a>Ejemplos de código y PowerShell para la colaboración B2B de Azure Active Directory

## <a name="code-sample"></a>Código de ejemplo
En este ejemplo se muestra cómo llamar a la API de invitación, en el modo de solo de aplicación, para obtener la URL de pago del recurso al que va a invitar al usuario B2B. El objetivo consiste en enviar un correo electrónico de invitación personalizado. Podrá ver cómo se puede redactar el correo electrónico con un cliente HTTP para que pueda personalizar su aspecto y enviarlo a través de la API Graph.

```
namespace SampleInviteApp
{
    using System.Globalization;
    using System;
    using System.Net;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.Graph;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    class Program
    {
        /// <summary>
        ///  Authentication endpoint to get token.
        /// </summary>
        static readonly string EstsLoginEndpoint = "https://login.microsoftonline.com";
        /// <summary>
        /// Initialize this to Tenant you want to invite users to.
        /// </summary>
        private static readonly string TenantID = "";
        /// <summary>
        /// Client id of the app.
        /// </summary>
        private static readonly string TestAppClientId = "";
        /// <summary>
        /// Client secret of the app.
        /// </summary>
        private static readonly string TestAppClientSecret = @"";
        /// <summary>
        /// Admin user principal name
        /// </summary>
        private static readonly string InviterUserPrincipalName = "";
        static void Main(string[] args)
        {
            CreateInvitation();
        }
        private static void CreateInvitation()
        {
            string accessToken = null;
            string GraphResource = string.Format("", "graph.microsoft.com");
            // Get the token for our app to talk to graph.
            try
            {
                AuthenticationContext testAuthContext = new AuthenticationContext(string.Format("{0}/{1}", EstsLoginEndpoint, TenantID));
                AuthenticationResult testAuthResult = testAuthContext.AcquireToken(
                    GraphResource,
                    new ClientCredential(TestAppClientId, TestAppClientSecret));;
                accessToken = testAuthResult.AccessToken;
            }
            catch (AdalException ex)
            {
                Console.WriteLine("An exception was thrown: {0}.", ex);
                throw;
            }
            // setup http client.
            HttpClient httpClient = new HttpClient();
            httpClient.Timeout = TimeSpan.FromSeconds(300);
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
            httpClient.DefaultRequestHeaders.Add("client-request-id", Guid.NewGuid().ToString());
            Console.WriteLine("CorrelationID for the request: {0}", httpClient.DefaultRequestHeaders.GetValues("client-request-id").Single());
            var inviteEndPoint = string.Format("https://graph.microsoft.com/beta/invitations");
            // Set the invitation object.
            Invitation invitation = new Invitation();
            invitation.InvitedUserDisplayName = "displayName";
            invitation.InvitedUserEmailAddress = "Give the invitee email";
            invitation.InviteRedirectUrl = "https://www.microsoft.com";
            invitation.SendInvitationMessage = false;
            // Make the invite call. Your app needs to have User.ReadWrite.All or Directory.ReadWrite.All scope to invite
            HttpContent content = new StringContent(JsonConvert.SerializeObject(invitation));
            content.Headers.Add("ContentType", "application/json");
            var postResponse = httpClient.PostAsync(inviteEndPoint, content).Result;
            string serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
            // Build the mail message. Here we are including the server response from inviation request, but you can customize it.
            // To send the email you need Mail.Send scope.
            var emailEndPoint = string.Format("https://graph.microsoft.com/beta/users/{0}/sendMail", InviterUserPrincipalName);
            Email mail = new Email();
            mail.message = new Message();
            mail.message.Subject = "Inviation Email";
            mail.message.Body = new ItemBody();
            mail.message.Body.Content = serverResponse;
            Recipient recipient = new Recipient();
            recipient.EmailAddress = new EmailAddress();
            recipient.EmailAddress.Address = invitation.InvitedUserEmailAddress;
            mail.recipients = new List<Recipient>();
            mail.recipients.Add(recipient);
            // Make the call to send email.
            content = new StringContent(JsonConvert.SerializeObject(mail));
            Console.WriteLine(content);
            content.Headers.Add("ContentType", "application/json");
            postResponse = httpClient.PostAsync(emailEndPoint, content).Result;
            serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
        }
        /// <summary>
        /// Invitation class.
        /// </summary>
        public class Invitation
        {
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserDisplayName { get; set; }
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserEmailAddress { get; set; }
            /// <summary>
            /// Gets or sets a value indicating whether Invitation Manager should send the email to InvitedUser.
            /// </summary>
            public bool SendInvitationMessage { get; set; }
            /// <summary>
            /// Gets or sets invitation redirect URL
            /// </summary>
            public string InviteRedirectUrl { get; set; }
        }
        /// <summary>
        /// The email message.
        /// </summary>
        public class Email
        {
            public Message message;
            public List<Recipient> recipients;
        }
    }
}
```

## <a name="powershell-example"></a>Ejemplo de PowerShell
Puede invitar de forma masiva a usuarios externos a una organización desde direcciones de correo electrónico que ha almacenado en un archivo CSV.

1. Prepare el archivo: cree un archivo CSV y asígnele el nombre invitations.csv. En este ejemplo, el archivo se guarda en C:\data. El archivo CSV podría ser similar al siguiente:

  ```
    InvitedUserEmailAddress
    user1@contoso.com
    user2@outlook.com
    user3@gmail.com
    user4@yahoo.com
    ```

2. Obtenga la última versión de Azure AD PowerShell para usar los nuevos cmdlets: debe instalar el módulo de Azure AD PowerShell actualizado, que puede descargar de la [página de la versión del módulo de Powershell](https://www.powershellgallery.com/packages/AzureADPreview).

3. Inicie sesión en el espacio.

    ```
    Connect-AzureAd and login
    ```

4. Ejecución del cmdlet de PowerShell

    ```
    $Invitations = import-csv C:\data\invitations.csv
    foreach ($email in $invitations) {New-AzureADMSInvitation -InvitedUserEmailAddress $email.InvitedUserEmailAddress -InviteRedirectUrl http://microsoft.com -SendInvitationMessage $true}
  ```

Este cmdlet enviará una invitación a la dirección de correo electrónico de invitations.csv. Algunas características adicionales de este cmdlet son las siguientes:
- Texto personalizado en el mensaje de correo electrónico
- Inclusión de un nombre para mostrar de los usuarios invitados
- Envío de mensajes en copias o supresión de mensajes de correo electrónico por completo

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Configuración de aplicaciones de SaaS para la colaboración B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)

