---
title: "Uso de un certificado SSL cargado en el código de aplicación de Azure App Service | Microsoft Docs"
description: 
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Uso de un certificado SSL en el código de aplicación de Azure App Service

Esta guía de procedimientos muestra cómo usar uno de los certificados SSL que ha cargado o importado en la aplicación App Service en el código de aplicación. Un ejemplo de caso de uso se produce cuando la aplicación accede a un servicio externo que requiere la autenticación de certificados. 

Este enfoque para el uso de certificados SSL en el código usa la funcionalidad SSL de App Service, que requiere que la aplicación tenga el nivel **Básico** u otro superior. Una alternativa consiste en incluir el archivo de certificado en el directorio de la aplicación y cargarlo directamente (consulte [Método alternativo: Carga del certificado como un archivo](#file)). Sin embargo, este método alternativo no le permite ocultar la clave privada del certificado del código de la aplicación ni del desarrollador. Además, si el código de la aplicación está en un repositorio de código abierto, guardar un certificado con una clave privada en este repositorio no es una opción.

Cuando permite que App Service administre los certificados SSL, puede mantener por separado los certificados y el código de la aplicación y proteger así la información confidencial.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de procedimientos:

- [Crear una aplicación de App Service](/azure/app-service/)
- [Asignar un nombre DNS personalizado a la aplicación web](app-service-web-tutorial-custom-domain.md)
- [Cargar un certificado SSL](app-service-web-tutorial-custom-ssl.md) o [importar un certificado de App Service](web-sites-purchase-ssl-web-site.md) en su aplicación web


## <a name="load-your-certificates"></a>Carga de certificados

Para usar un certificado que se haya cargado o importado en App Service, lo primero es hacer que este sea accesible al código de la aplicación. Esto lo puede hacer mediante la configuración de la aplicación `WEBSITE_LOAD_CERTIFICATES`.

En <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, abra la página de la aplicación web.

En el panel izquierdo, haga clic en **Certificados SSL**.

![Certificado cargado](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Aquí se muestran todos los certificados SSL cargados e importados para esta aplicación web con sus huellas digitales. Copie la huella digital del certificado que desea utilizar.

En el panel izquierdo, haga clic en **Configuración de la aplicación**.

Agregue una configuración de la aplicación llamada `WEBSITE_LOAD_CERTIFICATES` y establezca su valor en la huella digital del certificado. Para hacer que varios certificados sean accesibles, use valores de huellas digitales separados por comas. Para que todos los certificados sean accesibles, establezca el valor en `*`. 

![Configurar las opciones de la aplicación](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Cuando termine, haga clic en **Guardar**.

Ahora, el código ya puede usar el certificado configurado.

## <a name="use-certificate-in-c-code"></a>Uso del certificado en el código C#

Una vez que el certificado es accesible, puede acceder a él con código C# mediante la huella digital del certificado. El código siguiente carga un certificado con la huella digital `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>Método alternativo: Carga del certificado como un archivo

En esta sección se muestra cómo cargar un archivo de certificado que se encuentra en el directorio de la aplicación. 

El ejemplo de C# siguiente carga un certificado denominado `mycert.pfx` desde el directorio `certs` del repositorio de la aplicación.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

