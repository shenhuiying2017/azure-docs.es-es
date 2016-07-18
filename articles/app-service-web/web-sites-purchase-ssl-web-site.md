<properties
	pageTitle="Compra y configuración de un certificado SSL para el Servicio de aplicaciones de Azure"
	description="Aprenda a compra y configurar un certificado SSL para el Servicio de aplicaciones de Azure"
	services="app-service"
	documentationCenter=".net"
	authors="apurvajo"
	manager="stefsch"
	editor="cephalin"
	tags="buy-ssl-certificates"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="apurvajo"/>

#Compra y configuración de un certificado SSL para el Servicio de aplicaciones de Azure

> [AZURE.SELECTOR]
- [Compra de un certificado SSL para Aplicaciones web](web-sites-purchase-ssl-web-site.md)
- [Habilitación de SSL en un dominio personalizado](web-sites-configure-ssl-certificate.md)

De forma predeterminada, **[Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** ya habilita HTTPS para la aplicación web con un certificado comodín para el dominio *.azurewebsites.net. Si no tiene pensado configurar un dominio personalizado, puede aprovechar el certificado HTTPS predeterminado. Sin embargo, al igual que *[todos los dominios comodín](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), no es tan seguro como usar un dominio personalizado con su propio certificado. Servicio de aplicaciones de Azure ahora proporciona una manera realmente simplificada de adquirir y administrar un certificado SSL desde el Portal de Azure sin tener que salir de dicho portal. Este artículo explica cómo comprar y configurar un certificado SSL para su **[Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)** en 3 pasos sencillos.

> [AZURE.NOTE]
Los certificados SSL para nombres de dominio personalizado no se pueden usar con una aplicación web en el modo Gratis o Compartido. Debe configurar la aplicación web para los modos Básico, Estándar o Premium, ya que el uso de un modo u otro puede cambiar el precio de la suscripción. Para obtener más información, consulte **[Detalles de aplicaciones web](https://azure.microsoft.com/pricing/details/web-sites/)**.



##<a name="bkmk_Overview"></a>Información general
> [AZURE.NOTE]
No intente adquirir un certificado SSL mediante una suscripción que no tenga una tarjeta de crédito activa asociada. Esto podría provocar que se deshabilite la suscripción.

##<a> Compra, almacenamiento y asignación de un certificado SSL para el dominio personalizado </a>
Para habilitar HTTPS para un dominio personalizado, como contoso.com, primero debe **[configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure.](web-sites-custom-domain-name.md)**

Antes de solicitar un certificado SSL, primero debe determinar los nombres de dominio que contarán con la protección del certificado. De este modo, se determinará el tipo de certificado que debe obtener. Si necesita proteger un solo nombre de dominio, como contoso.com o www.contoso.com, será suficiente con un certificado de tipo Estándar (básico). Si necesita proteger varios nombres de dominio, como contoso.com, www.contoso.com y mail.contoso.com, puede obtener un **[certificado comodín](http://en.wikipedia.org/wiki/Wildcard_certificate)**.

##<a name="bkmk_purchasecert"></a>Paso 0: Hacer un pedido de certificado SSL

En este paso, aprenderá a realizar un pedido de un certificado SSL de su elección.

1.	En el **[Portal de Azure](https://portal.azure.com/)**, haga clic en Examinar, escriba Certificados del Servicio de aplicaciones en la barra de búsqueda, seleccione Certificados del Servicio de aplicaciones en el resultado y haga clic en Agregar.

    ![insertar imagen de creación mediante examinar](./media/app-service-web-purchase-ssl-web-site/browse.jpg)

    ![insertar imagen de creación mediante examinar](./media/app-service-web-purchase-ssl-web-site/add.jpg)

2.	Escriba un **nombre descriptivo** para el certificado SSL.

3.	Escriba un nombre en **Nombre de host**.
> [AZURE.NOTE]
    Esta es una de las partes más importantes del proceso de compra. Asegúrese de especificar el nombre de host correcto (dominio personalizado) que desea proteger con este certificado. **NO** anexe el nombre de host con WWW. Por ejemplo, si el nombre de dominio personalizado es www.contoso.com, entonces simplemente escriba contoso.com en el campo Nombre de Host. El certificado en cuestión protegerá los dominios raíz y www.
    
4.	Seleccione su **suscripción**.

    Si tiene varias suscripciones, asegúrese de crear un certificado SSL en la misma suscripción que ha usado para su dominio personalizado o la aplicación web en cuestión.
       
5.	Seleccione o cree un **grupo de recursos**.

    Los grupos de recursos le permiten administrar los recursos de Azure relacionados como una unidad y resultan útiles al establecer las reglas del control de acceso basado en rol (RBAC) para las aplicaciones. Para más información, consulte la administración de los recursos de Azure.
     
6.	Seleccione la **SKU de certificado**.

    Por último, seleccione el SKU de certificado que se adapte a sus necesidades y haga clic en Crear. Hoy en día, el Servicio de aplicaciones de Azure permite comprar dos SKU diferentes • S1 – certificado estándar de 1 año validez y auto renovación • W1: Certificado comodín con 1 año de validez y auto renovación. Consulte **[ Servicio de aplicaciones Precios](https://azure.microsoft.com/pricing/details/web-sites/)** para obtener más información.

![insertar imagen de SKU de certificado](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [AZURE.NOTE]
La creación del certificado SSL tardará entre 1 y 10 minutos. Este proceso realiza varios pasos en segundo plano que, de lo contrario, son muy complicados de llevar a cabo manualmente.

##<a name="bkmk_StoreKeyVault"></a>Paso 1: Almacenar el certificado en el Almacén de claves de Azure

En este paso, aprenderá a almacenar un certificado SSL que adquirió en el Almacén de claves de Azure de su elección.

1.	Una vez completada la compra del certificado SSL debe abrir manualmente la hoja de recursos **Certificados del Servicio de aplicaciones** examinándola de nuevo (consulte el paso 1 anterior).

    ![insertar imagen de listo para almacenar en KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)

    Observará que el estado del certificado es **Emisión pendiente**, ya que hay algunos pasos más que debe completar antes de poder empezar a usar este certificado.
 
2. Haga clic en **Configuración del certificado** dentro de la hoja Propiedades del certificado y haga clic en **Paso 1: Almacenar** para almacenar este certificado en el Almacén de claves de Azure.

3.	En la hoja **Estado del almacén de claves**, haga clic en **Repositorio del almacén de claves** para elegir un almacén de claves existente para almacenar este certificado. **También puede hacer clic en Crear nuevo almacén de claves** para generar el nuevo almacén de claves dentro de la misma suscripción y del mismo grupo de recursos.
 
    ![insertar imagen de creación de nuevo KV](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
 
    > [AZURE.NOTE]
    Almacén de claves de Azure tiene gastos mínimos para almacenar este certificado. Para obtener más información, consulte **[ Almacén de claves Precios ](https://azure.microsoft.com/pricing/details/key-vault/)**.

4. Una vez haya seleccionado el repositorio del almacén de claves para almacenar este certificado, continúe y almacénelo haciendo clic en el botón **Almacenar** situado en la parte superior de la hoja **Estado del almacén de claves**.

    Esto debe completar el paso para almacenar el certificado adquirido con el Almacén de claves de Azure de su elección. Al actualizar la hoja, también debe ver marca de verificación verde marcar en este paso.
    
##<a name="bkmk_VerifyOwnership"></a>Paso 2: Comprobar la propiedad del dominio

En este paso, aprenderá a comprobar la verificación de la propiedad del dominio para un certificado SSL para el que acaba de hacer un pedido.

1.	Haga clic en **Paso 2: Comprobar** desde la hoja **Configuración del certificado**. Los certificados del Servicio de aplicaciones admiten 4 tipos de comprobación de dominio.

    * **Comprobación del Servicio de aplicaciones**
    
        * Este es el proceso más conveniente si ya tiene **el dominio personalizado asignado a las aplicaciones del Servicio de aplicaciones**. Este método mostrará una lista con todas las aplicaciones del Servicio de aplicaciones que cumplen este criterio. Por ejemplo, en este caso, **contosocertdemo.com** es un dominio personalizado que se asigna a la aplicación del Servicio de aplicaciones llamado **ContosoCertDemo**, por lo que es la única aplicación del Servicio de aplicaciones enumerada aquí. Si hubiera una implementación en varias regiones, la enumeraría en todas las regiones.
        
           El método de verificación SOLO está disponible para compras de certificado de tipo Estándar (Básico). Para los certificados comodín, omita esta opción y pase a la opción B, C o D siguiente.
        * Haga clic en el botón **“Comprobar”** para completar este paso.
        * Haga clic en **“Actualizar”** para poner al día el estado del certificado después de completar la comprobación. La comprobación podría tardar unos minutos en completarse.
        
        ![insertar imagen de comprobación del Servicio de aplicaciones](./media/app-service-web-purchase-ssl-web-site/AppVerify.jpg)

    * **Comprobación del dominio**

        * Este es el proceso más conveniente **SOLO SI** ha **[adquirido el dominio personalizado desde el Servicio de aplicaciones de Azure.](custom-dns-web-site-buydomains-web-app.md)**
        
        * Haga clic en el botón **“Comprobar”** para completar este paso.
        
        * Haga clic en **“Actualizar”** para poner al día el estado del certificado después de completar la comprobación. La comprobación podría tardar unos minutos en completarse.

    * **Comprobación del correo**
        
        * El correo electrónico de comprobación ya se envió a las direcciones de correo electrónico asociadas a este dominio personalizado.
         
        * Abra el correo electrónico y haga clic en el vínculo de comprobación para completar el paso de comprobación del correo electrónico.
        
        * Si necesita volver a enviar el correo electrónico de comprobación, haga clic en el botón **"Reenviar correo electrónico"**.
         
    * **Comprobación manual**
                 
        1. **Comprobación de página web HTML**
        
            * Cree un archivo HTML llamado **{Token de comprobación de dominio}**.html (puede copiar el token desde la hoja de estado de comprobación de dominio).
            
            * El contenido de este archivo debe ser exactamente el mismo nombre de **Token de comprobación de dominio**.
            
            * Cargue este archivo en la raíz del servidor web que hospeda el dominio.
            
            * Haga clic en **“Actualizar”** para poner al día el estado del certificado después de completar la comprobación. La comprobación podría tardar unos minutos en completarse.
            
            Por ejemplo, si va a comprar un certificado estándar de contosocertdemo.com con el Token de comprobación de dominio **'cAGgQrKc'**, entonces una solicitud web hecha a **‘http://contosocertdemo.com/cAGgQrKc.html’** debe devolver **cAGgQrKc.**
        2. **Comprobación del registro TXT de DNS**

            * Mediante el administrador de DNS, cree un registro TXT en el subdominio **'DZC'** con valor igual al **token de comprobación de dominio.**
            
            * Haga clic en **“Actualizar”** para poner al día el estado del certificado después de completar la comprobación. La comprobación podría tardar unos minutos en completarse.
                              
            Por ejemplo, para realizar la validación de un certificado comodín con el nombre de host ***.contosocertdemo.com** o ***.subdomain.contosocertdemo.com** y el token de comprobación de dominio **cAGgQrKc**, deberá crear un registro TXT en dzc.contosocertdemo.com con el valor **cAGgQrKc.**


##<a name="bkmk_AssignCertificate"></a>Paso 3: Asignar el certificado a la aplicación del Servicio de aplicaciones

En este paso, aprenderá a asignar este certificado recién adquirido a las aplicaciones del Servicio de aplicaciones.

> [AZURE.NOTE]
Antes de realizar los pasos de esta sección, debe haber asociado un nombre de dominio personalizado a la aplicación. Para más información, consulte **[Configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](web-sites-custom-domain-name.md)**.

1.	En el explorador, abra el **[Portal de Azure](https://portal.azure.com/)**.
2.	Haga clic en la opción **Servicio de aplicaciones** del lado izquierdo de la página.
3.	Haga clic en el nombre de la aplicación a la que desea asignar este certificado.
4.	Haga clic en **Configuración** y, después, en **Dominios personalizados y SSL**.
5.	En la **sección de certificados**, haga clic en **Importar certificado** y seleccione el certificado que acaba de comprar.

    ![insertar imagen de Importar certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.jpg)

6. En la sección **Enlaces SSL** de la pestaña **Configuración de SSL**, use las listas desplegables para seleccionar el nombre de dominio que desea proteger con SSL y el certificado que va a usar. Es posible que también desee seleccionar el uso de **[Indicación de nombre de servido (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** (SNI) o SSL basada en IP.

    ![insertar imagen de enlaces de SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.jpg)

       • SSL basada en IP asocia un certificado a un nombre de dominio mediante la asignación de una dirección IP pública dedicada del servidor al nombre de dominio. En este caso es necesario que cada nombre de dominio (contoso.com, fabrikam.com, etc.) asociado a un servicio tenga una dirección IP dedicada. Este es el método tradicional de asociación de certificados SSL a un servidor web. • SSL basada en SNI es una extensión de SSL y **[Seguridad de la capa de transporte](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) que permite que varios dominios compartan la misma dirección IP con certificados de seguridad independientes para cada dominio. Los exploradores más modernos (entre los que se incluyen Internet Explorer, Chrome, Firefox y Opera) son compatibles con la extensión SNI; sin embargo, es posible que los exploradores más antiguos no sean compatibles con la extensión SNI. Para más información sobre la extensión SNI, consulte el artículo **[Indicación de nombre de servidor](http://en.wikipedia.org/wiki/Server_Name_Indication)** en Wikipedia.
       
7. Haga clic en *Guardar* para guardar los cambios y habilitar SSL.



Si ha seleccionado **SSL basada en IP** y su dominio personalizado se ha configurado con un registro D, debe realizar los siguientes pasos adicionales:

* Después de haber configurado un enlace SSL basado en IP, se asigna una dirección IP dedicada a la aplicación. Esta dirección IP se puede encontrar en la página **Panel** de la aplicación, en la sección **vista rápida**. Se mostrará como **Dirección IP virtual**:
    
    ![insertar imagen de SSL de IP](./media/app-service-web-purchase-ssl-web-site/IPSSL.jpg)

    Tenga en cuenta que esta dirección IP será distinta de la dirección IP virtual que ha usado previamente para configurar el registro D de su dominio. Si lo ha configurado para usar un cifrado SSL basado en SNI, o no lo ha configurado para usar SSL, esta entrada no contendrá ninguna dirección.
    
2. Mediante las herramientas proporcionadas por su registrador de nombres de dominio, modifique el registro D de su nombre de dominio personalizado para que apunte a la dirección IP del paso anterior. Llegados a este punto, debería ser capaz de visitar su aplicación con HTTPS://, en lugar de HTTP://, para comprobar que el certificado se ha configurado correctamente.


##<a name="bkmk_Rekey"></a>Regeneración de la clave del certificado y sincronización de este

1. Por motivos de seguridad, si necesita regenerar la clave del certificado, simplemente seleccione la opción **"Regenerar clave y sincronizar"** en la hoja **"Propiedades del certificado"**.

2. Haga clic en el botón **"Regenerar clave"** para iniciar el proceso. Este proceso puede tardar de 1 a 10 minutos en completarse.

    ![insertar imagen de SSL de regeneración de claves](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

3. La regeneración de claves del certificado renovará el certificado con un nuevo certificado emitido desde la entidad de certificación.
4. Mientras el certificado esté vigente no se le cobrará por la regeneración de claves.
5. La regeneración de claves del certificado pasará a través del estado Emisión pendiente.
6. Una vez que el certificado esté listo, asegúrese de que sincronizar los recursos con este certificado para evitar la interrupción del servicio.
7. La opción de sincronización no está disponible para los certificados que todavía no están asignados a la aplicación web.

## Más recursos ##
- [Habilitación de HTTPS para una aplicación en el servicio de aplicaciones de Azure](web-sites-configure-ssl-certificate.md)
- [Comprar y configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](custom-dns-web-site-buydomains-web-app.md)
- [Centro de confianza de Microsoft Azure](/support/trust-center/security/)
- [Opciones de configuración desbloqueadas en Sitios web Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Portal de administración de Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<!---HONumber=AcomDC_0706_2016-->