---
title: "Incorporación de un certificado SSL a la aplicación Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo agregar un certificado SSL a la aplicación App Service."
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo;aelnably
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 00e252e249dbd1a38a4649e435071685860722e4
ms.lasthandoff: 04/07/2017


---

# <a name="add-an-ssl-certificate-to-your-app-service-app"></a>Incorporación de un certificado SSL a la aplicación App Service
> [!div class="op_single_selector"]
> * [Compra de un certificado SSL en Azure](web-sites-purchase-ssl-web-site.md)
> * [Uso de un certificado SSL desde cualquier otro lugar](web-sites-configure-ssl-certificate.md)
> 
> 

De manera predeterminada, [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) habilita HTTPS para la aplicación web con un certificado comodín para el dominio \*.azurewebsites.net. No tiene pensado configurar un dominio personalizado, puede aprovechar el certificado HTTPS predeterminado. Sin embargo, al igual que todos los [dominios comodín](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), el certificado comodín de Azure no es tan seguro como usar un dominio personalizado con su propio certificado.

App Service brinda una forma simplificada de comprar y administrar un certificado SSL en Azure Portal. 

En este artículo se explica cómo comprar y configurar un certificado SSL para la aplicación [App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

> [!NOTE]
> No puede usar certificados SSL para nombres de dominio personalizados para aplicaciones que se hospedan en un plan de App Service Gratis o Compartido. Para usar los certificados SSL, la aplicación web se debe hospedar en un plan de App Service Básico, Estándar o Premium. Si cambia el tipo de suscripción, puede cambiar la forma en que se le factura según la suscripción. Para más información, consulte [Precios de App Service](https://azure.microsoft.com/pricing/details/web-sites/).
> 
> 

> [!WARNING]
> No intente comprar un certificado SSL con una suscripción para la que no adjuntó una tarjeta de crédito válida. Esto podría provocar que se deshabilite la suscripción. 
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Para habilitar HTTPS para un dominio personalizado, comience por [asignar un nombre de dominio personalizado a la aplicación de Azure](web-sites-custom-domain-name.md).

Antes de solicitar un certificado SSL, primero determina los nombres de dominio que protegerá con el certificado. Esto determina el tipo de certificado que necesita. Si desea proteger un solo nombre de dominio, como contoso.com *o* www.contoso.com, puede usar un certificado Estándar (básico). Si necesita proteger varios nombres de dominio, como contoso.com, www.contoso.com *y* mail.contoso.com, puede obtener un [certificado comodín](http://en.wikipedia.org/wiki/Wildcard_certificate).

## <a name="bkmk_purchasecert"></a>Compra de un certificado SSL

1. En [Azure Portal](https://portal.azure.com/), seleccione **Examinar** en el menú. En el cuadro **Búsqueda**, escriba **App Service Certificate**. En los resultados de la búsqueda, seleccione **App Service Certificates**. 

   ![Creación mediante Examinar](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
2. En la página **App Service Certificates**, seleccione **Agregar**. 

   ![Incorporación de un certificado](./media/app-service-web-purchase-ssl-web-site/add.jpg)

3. Escriba un **nombre** para el certificado SSL.
4. Escriba el **nombre de host**.
   
   > [!WARNING]
   > Esta es una de las partes más importantes del proceso de compra. Asegúrese de escribir el nombre de host correcto (nombre de dominio personalizado) que desea proteger con este certificado. *No* agregue "www" al comienzo del nombre de host. Por ejemplo, si el nombre de dominio personalizado es www.contoso.com, escriba **contoso.com** en el cuadro **Nombre de host**. El certificado protegerá los dominios raíz y www. 
   > 

5. Seleccione su **suscripción**. 
   
   Si tiene varias suscripciones, cree el certificado SSL en la misma suscripción que usa para la aplicación web o el dominio personalizado.

6. Seleccione o cree un **grupo de recursos**.
   
   Puede usar grupos de recursos para administrar los recursos relacionados de Azure como una unidad. Los grupos de recursos son útiles cuando desea establecer reglas de control de acceso basado en rol (RBAC) para las aplicaciones. Para más información, consulte la administración de los recursos de Azure.

7. Seleccione la **SKU de certificado**. 
   
   Seleccione la SKU de certificado que se adapte a sus necesidades y, luego, seleccione **Crear**. 
   
   Puede elegir entre dos SKU en App Service:
   * **S1**: certificado estándar con un año de validez y renovación automática  
   * **W1**: certificado comodín con un año de validez y renovación automática       
  
    ![SKU de certificado](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

    Para más información, consulte [Precios de App Service](https://azure.microsoft.com/pricing/details/web-sites/).

> [!NOTE]
> Crear un certificado SSL puede tardar hasta 10 minutos. Este proceso incluye varios plazos que se realizan en segundo plano.  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>Almacenamiento del certificado en Azure Key Vault

1. Cuando complete la compra del certificado SSL, en Azure Portal, vaya a la hoja **App Service Certificates**.

   ![Certificado listo para almacenamiento en Key Vault](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   Observe que el estado del certificado es **Emisión pendiente**. Debe completar algunos pasos antes de que pueda usar este certificado.

2. En la hoja **Propiedades del certificado**, seleccione **Configuración del certificado**. Para almacenar este certificado en Key Vault, seleccione **Paso 1: Almacenamiento**.
3. En la hoja **Estado de Key Vault**, seleccione **Repositorio de Key Vault** para seleccionar un almacén de claves donde almacenar este certificado.  Para crear un almacén de claves en la misma suscripción y grupo de recursos, seleccione **Crear nuevo almacén de claves**.

   ![Creación de almacén de claves](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > Azure Key Vault ofrece cargos mínimos para almacenar el certificado. Para más información, consulte [Precios de Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
   > 
   > 

4. Una vez que seleccione el repositorio de Key Vault para almacenar el certificado, en la parte superior de la hoja **Estado de Key Vault**, seleccione el botón **Almacenar**.  
   
Para comprobar la selección, puede hacer clic en el botón de actualización del explorador. Una marca de verificación verde muestra que se terminó este paso.

## <a name="bkmk_VerifyOwnership"></a>Comprobación de la propiedad del dominio

1. En la hoja **Configuración del certificado**, seleccione **Paso 2: Comprobación**.
2. Use la siguiente información para seleccionar las opciones de comprobación. 

Los certificados App Service Certificate admiten tres tipos de comprobación de dominio:

   * Comprobación de dominio
   * Comprobación de correo
   * Comprobación manual

### <a name="domain-verification"></a>Comprobación de dominio 
     
La comprobación de dominio es el proceso más conveniente, pero *solo* si [adquirió el dominio personalizado en Azure App Service](custom-dns-web-site-buydomains-web-app.md).

1. Para completar este paso, seleccione **Comprobar**.
2. Para actualizar el estado del certificado una vez que se completa la comprobación, seleccione **Actualizar**. La comprobación podría tardar unos minutos en completarse.

### <a name="mail-verification"></a>Comprobación de correo
     
Con un dominio personalizado, se envía un correo electrónico de comprobación a la dirección de correo asociada con el dominio. 

1. Para completar el paso de comprobación de correo electrónico, abra el mensaje y haga clic en el vínculo de comprobación. 
2. Si necesita reenviar el correo electrónico de comprobación, haga clic en el botón **Reenviar correo electrónico**.

### <a name="manual-verification"></a>Comprobación manual    
     
**Comprobación de página web HTML (solo funciona con una SKU de certificados Estándar)**

1. Cree un archivo HTML llamado starfield.html. El contenido de este archivo debe ser exactamente el mismo nombre del token de comprobación de dominio. (Puede copiar el token de la hoja **Estado de comprobación de dominio**).
2. Cargue este archivo a la raíz del servidor web donde hospeda el dominio. Por ejemplo, /.well-known/pki-validation/starfield.html.
3.  Cuando finalice la comprobación, seleccione **Actualizar** para actualizar el estado del certificado. La comprobación podría tardar unos minutos en finalizar.

    Por ejemplo, si va a comprar un certificado estándar de **contosocertdemo.com** con un token de comprobación de dominio **tgjgthq8d11ttaeah97s3fr2sh**, una solicitud web realizada a **http://contosocertdemo.com/.well-known/pki-validation/starfield.html** debe devolver **tgjgthq8d11ttaeah97s3fr2sh**.

**Comprobación del registro TXT de DNS**
        
1. Mediante el administrador de DNS, cree un registro TXT en el subdominio **@** con un valor igual al **token de comprobación de dominio**.
2. Para actualizar el estado del certificado cuando finalice la comprobación, seleccione **Actualizar**. La comprobación podría tardar unos minutos en finalizar.
 
   Por ejemplo, para realizar la validación de un certificado comodín con el nombre de host **\*.contosocertdemo.com** o **\*.subdomain.contosocertdemo.com** y el token de comprobación de dominio **tgjgthq8d11ttaeah97s3fr2sh**, cree un registro TXT en **contosocertdemo.com** con el valor **tgjgthq8d11ttaeah97s3fr2sh**.     

## <a name="bkmk_AssignCertificate"></a>Asignación del certificado a una aplicación de App Service

> [!NOTE]
> Antes de completar los pasos de esta sección, debe asociar un nombre de dominio personalizado con la aplicación. Para más información, consulte [Configuración de un nombre de dominio personalizado para una aplicación web](web-sites-custom-domain-name.md).
> 
> 

1. En el menú de [Azure Portal](https://portal.azure.com/), seleccione **App Service**.
2. Seleccione el nombre de la aplicación a la que se desea asignar este certificado. 
3. Vaya a **Configuración** > **Certificados SSL** > **Importar App Service Certificate** y seleccione el certificado.

   ![Importación de certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

4. En la sección **Enlaces SSL**, seleccione **Agregar enlaces**.
5. En la hoja **Agregar enlace SSL**, seleccione el nombre de dominio que desea proteger con el certificado SSL. Seleccione el certificado que desea usar. Es posible que también desee seleccionar el uso de [Indicación de nombre de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication) o SSL basada en IP.

   ![Enlaces SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
    * Para asociar un certificado con un nombre de dominio, SSL basada en IP asigna la dirección IP pública dedicada del servidor al nombre de dominio. Cuando usa SSL basada en IP, cada nombre de dominio (por ejemplo, contoso.com o fabricam.com) asociado con el servicio debe tener una dirección IP dedicada. Este es el método tradicional para asociar un certificado SSL con un servidor web.
    * SSL basada en SNI es una extensión de SSL y [Seguridad de la capa de transporte](http://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS). Cuando usa SSL basada en SNI, varios dominios pueden compartir la misma dirección IP. Cada dominio tiene un certificado de seguridad independiente. Los exploradores más modernos, como Internet Explorer, Chrome, Firefox y Opera, admiten SNI. Es posible que los exploradores más antiguos no sean compatibles con SNI. Para más información sobre SNI, consulte [Indicación de nombre de servidor](http://en.wikipedia.org/wiki/Server_Name_Indication) en Wikipedia.

6. Seleccione **Agregar enlace** para guardar los cambios y habilitar SSL.

Si selecciona **SSL basada en IP** y el dominio personalizado se configura con un registro D, debe completar los siguientes pasos adicionales.

1.  Una vez que configura un enlace SSL basado en IP, se asigna una dirección IP dedicada a la aplicación. Para encontrar la dirección IP, vaya a **Configuración** > **Dominio personalizado**. Justo arriba de la sección **Nombres de host**, la dirección IP aparece como **Dirección IP externa**.

   ![SSL basada en IP](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
    
  Tenga en cuenta que esta dirección IP es distinta de la dirección IP virtual que usó anteriormente para configurar el registro D del dominio. Si la aplicación está configurada para usar SSL basada en SNI o si no está configurada para usar SSL, aquí no aparecerá ninguna dirección IP.

2.  Use las herramientas que proporciona el registrador de nombres de dominio para modificar el registro D del nombre de dominio personalizado de manera que apunte a la dirección IP que usó en el paso anterior.

3.  Para comprobar que se configuró correctamente el certificado, vaya a la aplicación con HTTPS:// en lugar de HTTP://.

## <a name="bkmk_Export"></a>Exportación del certificado App Service Certificate
Puede crear una copia local PFX de un certificado App Service Certificate. Cuando tiene una copia local, puede usar el certificado con otros servicios de Azure. Para más información, consulte nuestra entrada de blog [Create a local PFX copy of your App Service certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/) (Creación de una copia local PFX del certificado App Service Certificate).

## <a name="bkmk_Renew"></a>Renovación automática del certificado App Service Certificate
Para establecer la configuración de renovación automática del certificado o renovarlo manualmente, en la hoja **Propiedades del certificado**, seleccione **Configuración de renovación automática**. 

![Configuración de renovación automática](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

Para renovar automáticamente el certificado antes de que expire, establezca **Renovación automática** en **Activada**. Esta es la opción predeterminada. Si la renovación automática está activada, intentamos renovar el certificado a partir del día 90 antes de su expiración. Si creó enlaces SSL en las aplicaciones de App Service en Azure Portal, también se actualizan los enlaces cuando el certificado nuevo está listo (como en escenarios de regeneración de claves y sincronización). 

Si desea controlar manualmente las renovaciones, establezca **Renovación automática** en **Desactivada**. Puede renovar manualmente un certificado App Service Certificate solo si quedan menos de 90 días para la fecha de expiración.

## <a name="bkmk_Rekey"></a>Regeneración de la clave del certificado y sincronización de este

1. Si alguna vez necesita regenerar la clave del certificado (por motivos de seguridad), en la hoja **Propiedades del certificado**, seleccione **Regenerar claves y sincronizar**. 
2. Seleccione **Regenerar claves**. El proceso puede tardar hasta 10 minutos en finalizar. 

   ![Regenerar claves de SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

A continuación se proporciona información adicional sobre la regeneración de claves:

* Regenerar la clave del certificado reemplaza el certificado por otro. La entidad de certificación emite el certificado nuevo.
* Mientras el certificado esté vigente, no se le cobrará por regenerar las claves. 
* Regenerar las claves del certificado hace que este tenga el estado **Emisión pendiente**. 
* Cuando el certificado está listo, asegúrese de sincronizar los recursos mediante el certificado para evitar la interrupción del servicio.
* La opción de sincronización no está disponible para los certificados que todavía no se asignan a una aplicación web. 

## <a name="next-steps"></a>Pasos siguientes

* [Protección del dominio personalizado de la aplicación con HTTPS](web-sites-configure-ssl-certificate.md)
* [Comprar y configurar un nombre de dominio personalizado en Azure App Service](custom-dns-web-site-buydomains-web-app.md)
* [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/en-us/support/trust-center/)

> [!NOTE]
> Para comenzar a usar Azure App Service antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de App Service](https://azure.microsoft.com/try/app-service/). Puede crear una aplicación web de inicio de corta duración en App Service. No necesita ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

