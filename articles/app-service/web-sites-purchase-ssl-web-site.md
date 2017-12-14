---
title: "Compra y configuración de un certificado SSL para Azure App Service | Microsoft Docs"
description: "Aprenda a comprar un certificado de App Service y a enlazarlo a esta aplicación"
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 6c0125bf0bd22912a21372b5a7da6846e924e6cd
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Compra y configuración de un certificado SSL para Azure App Service

Este tutorial muestra cómo proteger su aplicación web comprando un certificado SSL para su instancia de  **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** almacenándolo de forma segura en [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)y asociándolo a un dominio personalizado.

## <a name="step-1---log-in-to-azure"></a>Paso 1: Inicio de sesión en Azure

Inicie sesión en Azure Portal: http://portal.azure.com.

## <a name="step-2---place-an-ssl-certificate-order"></a>Paso 2: Realización de un pedido de certificado SSL

Puede hacer un pedido de certificado SSL creando un [certificado de App Service](https://portal.azure.com/#create/Microsoft.SSL) en **Azure Portal**.

![Creación de certificados](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Escriba un **nombre** descriptivo para su certificado SSL y especifique el **nombre de dominio**.

> [!NOTE]
> Este paso es una de las partes más importantes del proceso de compra. Asegúrese de especificar el nombre de host correcto (dominio personalizado) que desea proteger con este certificado. **NO** anexe el nombre de host con WWW. 
>

Seleccione la **suscripción**, el **grupo de recursos** y la **SKU de certificado**.

> [!WARNING]
> Los certificados de App Services solo pueden utilizarlas otras instancias de App Service en la misma suscripción.  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Paso 3: Almacenamiento del certificado en Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) es un servicio de Azure que ayuda a proteger claves criptográficas y secretos que emplean servicios y aplicaciones en la nube.
>

Una vez completada la compra del certificado SSL, debe abrir la página [Certificados de App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders).

![insertar imagen de listo para almacenar en KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

El estado del certificado es "**Emisión pendiente**", ya que hay algunos pasos más que debe completar antes de poder empezar a usar este certificado.

Haga clic en "**Configuración del certificado**" dentro de la página Propiedades del certificado y haga clic en "**Paso 1: Almacenamiento**" para almacenar este certificado en Azure Key Vault.

En la página **Estado de Key Vault**, haga clic en **Repositorio de Key Vault** para elegir un almacén de claves existente para almacenar este certificado. **También puede hacer clic en Crear nuevo almacén de claves** para generar el nuevo almacén de claves dentro de la misma suscripción y del mismo grupo de recursos.

> [!NOTE]
> Azure Key Vault tiene gastos mínimos por almacenar este certificado.
> Para obtener más información, consulte **[Detalles de precios de Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Una vez que haya seleccionado el repositorio de Key Vault donde almacenar este certificado, la opción **Almacenar** debería mostrar el mensaje "correcto".

![insert image of store success in KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Paso 4: Comprobación de la propiedad del dominio

En la página **Configuración del certificado** que usó en el paso 3, haga clic en **Paso 2: Comprobación**.

Elija el método de comprobación de dominio preferido. 

Hay cuatro tipos de comprobación de dominio que admiten los certificados de App Service: App Service, Comprobación de dominio, Comprobación de correo electrónico y Comprobación manual. Estos tipos de comprobación se explican con más detalle en la sección [Avanzado](#advanced).

> [!NOTE]
> **Comprobación de App Service** es la opción más conveniente cuando el dominio que desea comprobar ya está asignado a una aplicación App Service de la misma suscripción. Se aprovecha de que la aplicación App Service ya ha comprobado la propiedad del dominio.
>

Haga clic en el botón **Comprobar** para completar este paso.

![insert image of domain verification](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Después de hacer clic en **Comprobar**, use el botón **Actualizar** hasta que la opción **Comprobar** muestre el mensaje "correcto".

![insert image of verify success in KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Paso 5: Asignación del certificado a la aplicación de App Service

> [!NOTE]
> Antes de realizar los pasos de esta sección, debe haber asociado un nombre de dominio personalizado a la aplicación. Para más información, consulte **[Configuración de un nombre de dominio personalizado para una aplicación web](app-service-web-tutorial-custom-domain.md)**
>

En **[Azure Portal](https://portal.azure.com/)**, haga clic en la opción **App Service** del lado izquierdo de la página.

Haga clic en el nombre de la aplicación a la que desea asignar este certificado.

En **Configuración**, haga clic en **Certificados SSL**.

Haga clic en **Importar certificado de App Service** y seleccione el certificado que acaba de comprar.

![insertar imagen de Importar certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

En la sección **Enlaces SSL**, haga clic en **Agregar enlaces** y use las listas desplegables para seleccionar el nombre de dominio que va a proteger con SSL, así como el certificado que pretende utilizar. Es posible que también desee seleccionar el uso de **[Indicación de nombre de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** (SNI) o SSL basada en IP.

![insertar imagen de enlaces de SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Haga clic en **Agregar enlace** para guardar los cambios y habilitar SSL.

> [!NOTE]
> Si ha seleccionado **SSL basada en IP** y su dominio personalizado se ha configurado con un registro D, debe realizar los siguientes pasos adicionales. Estos se explican con más detalle en la sección [Avanzado](#Advanced).

Llegados a este punto, debería ser capaz de visitar su aplicación con `HTTPS://` en lugar de `HTTP://`, para comprobar que el certificado se ha configurado correctamente.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Paso 6: Tareas de administración

### <a name="azure-cli"></a>CLI de Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Avanzado

### <a name="verifying-domain-ownership"></a>Comprobación de la propiedad del dominio

Hay dos tipos más de comprobación de dominio que admiten los certificados de App Service: Comprobación de correo electrónico y Comprobación manual.

#### <a name="mail-verification"></a>Comprobación de correo electrónico

El correo electrónico de comprobación ya se envió a las direcciones de correo electrónico asociadas a este dominio personalizado.
Para completar el paso de comprobación de correo electrónico, abra el mensaje y haga clic en el vínculo de comprobación.

![insert image of email verification](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Si necesita reenviar el correo electrónico de comprobación, haga clic en el botón **Reenviar correo electrónico**.

#### <a name="domain-verification"></a>Comprobación de dominio

Elija esta opción solo para [un dominio de App Service que haya adquirido a través de Azure](custom-dns-web-site-buydomains-web-app.md). Azure agrega automáticamente el registro TXT de comprobación en su lugar y completa el proceso.

#### <a name="manual-verification"></a>Comprobación manual

> [!IMPORTANT]
> Comprobación de página web HTML (solo funciona con la SKU de certificados estándar)
>

1. Creación de un archivo HTML denominado "**starfield.html**"

1. El contenido de este archivo debe ser exactamente el mismo nombre del token de comprobación de dominio (puede copiar el token de la página Estado de comprobación de dominio).

1. Cargue este archivo en la raíz del servidor web que hospeda el dominio `/.well-known/pki-validation/starfield.html`.

1. Haga clic en **Actualizar** para poner al día el estado del certificado después de completar la comprobación. La comprobación podría tardar unos minutos en completarse.

> [!TIP]
> Realice el proceso en un terminal usando `curl -G http://<domain>/.well-known/pki-validation/starfield.html`; la respuesta debería contener `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Comprobación del registro TXT de DNS

1. Mediante el administrador de DNS, cree un registro TXT en el subdominio `@` con valor igual al token de comprobación de dominio.
1. Haga clic en **Actualizar** para poner al día el estado del certificado después de completar la comprobación.

> [!TIP]
> Debe crear un registro TXT en `@.<domain>` con valor `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Asignación del certificado a la aplicación de App Service

Si ha seleccionado **SSL basada en IP** y su dominio personalizado se ha configurado con un registro D, debe realizar los siguientes pasos adicionales:

Después de haber configurado un enlace SSL basado en IP, se asigna una dirección IP dedicada a la aplicación. Podrá encontrar esta dirección IP en la página **Dominio personalizado** de la configuración de su aplicación, justo arriba de la sección **Nombre de host**. Figurará como **Dirección IP externa**.

![insertar imagen de SSL de IP](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Esta dirección IP será distinta de la dirección IP virtual que ha usado previamente para configurar el registro D de su dominio. Si lo ha configurado para usar una SSL basada en SNI, o bien no lo ha configurado para usar SSL, esta entrada no contendrá ninguna dirección.

Mediante las herramientas proporcionadas por su registrador de nombres de dominio, modifique el registro D de su nombre de dominio personalizado para que apunte a la dirección IP del paso anterior.

## <a name="rekey-and-sync-the-certificate"></a>Regeneración de la clave del certificado y sincronización de este

Si necesita regenerar la clave del certificado, seleccione la opción **Regenerar clave y sincronizar** en la página **Propiedades del certificado**.

Haga clic en el botón **Regenerar clave** para iniciar el proceso. Este proceso puede tardar de 1 a 10 minutos en completarse.

![insertar imagen de SSL de regeneración de claves](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

La regeneración de claves del certificado renovará el certificado con un nuevo certificado emitido desde la entidad de certificación.

<a name="notrenewed"></a>
## <a name="why-is-my-ssl-certificate-not-auto-renewed"></a>¿Por qué mi certificado SSL no se renueva automáticamente?

Si el certificado SSL está configurado para la renovación automática, pero no se renueva automáticamente, puede tener una comprobación del dominio pendiente. Tenga en cuenta lo siguiente: 

- GoDaddy, que genera certificados de App Service, requiere la comprobación del dominio una vez cada tres años. El administrador del dominio recibe un correo electrónico una vez cada tres años para comprobar el dominio. Un error al comprobar el correo electrónico o comprobar el dominio impide que el certificado de App Service se renueve automáticamente. 
- Todos los certificados de App Service emitidos antes del 31 de marzo de 2017 requieren la comprobación de nuevo del dominio en el momento de la renovación siguiente (incluso si la renovación automática está habilitada para el certificado). Esto es resultado del cambio en la directiva de GoDaddy. Compruebe su correo electrónico y complete esta comprobación de dominio de un solo uso para continuar con la renovación automática del certificado de App Service. 

## <a name="more-resources"></a>Más recursos

* [Uso de un certificado SSL en el código de aplicación de Azure App Service](app-service-web-ssl-cert-load.md)
* [Preguntas frecuentes: Certificados de App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)