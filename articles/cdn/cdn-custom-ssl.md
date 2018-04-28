---
title: 'Tutorial: Configuración de HTTPS en un dominio personalizado de Azure CDN | Microsoft Docs'
description: En este tutorial, aprenderá a habilitar y deshabilitar HTTPS en su dominio personalizado de punto de conexión de Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/12/2018
ms.author: rli
ms.custom: mvc
ms.openlocfilehash: a8f2da5a68552c35a55a7bbb764afc7b36af6962
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Configuración de HTTPS en un dominio personalizado de Azure CDN

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

En este tutorial se muestra cómo habilitar el protocolo HTTP en un dominio personalizado que está asociado a un punto de conexión de Azure Content Delivery Network (CDN). Mediante el protocolo HTTPS en el dominio personalizado (por ejemplo, https:\//www.contoso.com), se garantiza que los datos confidenciales se entregan de manera segura a través del cifrado SSL cuando se envían por Internet. HTTPS ofrece confianza, autenticación y protección de las aplicaciones web de posibles ataques. El flujo de trabajo para habilitar HTTPS se simplifica mediante la habilitación con un solo clic y la administración completa de certificados; todo ello sin ningún costo adicional.

Azure CDN admite HTTPS en un punto de conexión de CDN de manera predeterminada. Por ejemplo, si crea un punto de conexión de CDN (como https:\//contoso.azureedge.net), HTTPS se habilita de manera automática.  

Algunos de los atributos clave de la característica de HTTPS son:

- Sin costo adicional: la adquisición o renovación de certificados no tiene costos y el tráfico HTTPS no supone un costo adicional. Solo es preciso pagar por los GB de salida de la red CDN.

- Habilitación simple: el aprovisionamiento en un solo clic está disponible desde [Azure Portal](https://portal.azure.com). También puede utilizar la API de REST u otras herramientas de desarrollo para habilitar la característica.

- Administración de certificados completa: toda la adquisición y administración de certificados se controla de manera automática. Los certificados se aprovisionan y se renuevan automáticamente antes de la expiración, lo que elimina los riesgos de interrupción del servicio debido a la expiración de un certificado.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> - Habilitar el protocolo HTTPS en su dominio personalizado
> - Validar el dominio
> - Deshabilitar el protocolo HTTPS en su dominio personalizado

## <a name="prerequisites"></a>requisitos previos

Para poder completar los pasos de este tutorial, primero es preciso crear un perfil de CDN y al menos un punto de conexión de CDN. Para más información, consulte [Guía de inicio rápido: Creación de un perfil y un punto de conexión de Azure CDN](cdn-create-new-endpoint.md).

Además, debe asociar un dominio personalizado de Azure CDN en el punto de conexión de CDN. Para más información, consulte el [Tutorial: Adición de un dominio personalizado a un punto de conexión de Azure CDN](cdn-map-content-to-custom-domain.md)

## <a name="enable-the-https-feature"></a>Habilitación de la característica HTTPS

Para habilitar HTTPS en un dominio personalizado, siga estos pasos:

1. En el [Azure Portal](https://portal.azure.com), vaya a su perfil de CDN **Azure CDN estándar de Verizon** o **Azure CDN premium de Verizon**.

2. En la lista de puntos de conexión de CDN, haga clic en el que contiene el dominio personalizado.

    ![Lista de puntos de conexión](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Aparece la página **Punto de conexión**.

3. En la lista de dominios personalizados, seleccione el dominio personalizado para el que desea habilitar HTTPS.

    ![Lista de dominios personalizados](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Aparece la página **Dominio personalizado**.

4. Seleccione **Activado** para habilitar HTTPS y, luego, seleccione **Aplicar**.

    ![Estado de HTTPS de dominio personalizado](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="validate-the-domain"></a>Validar el dominio

Si ya tiene un dominio personalizado en uso que está asignado a su punto de conexión personalizado con un registro CNAME, continúe con  
[El dominio personalizado está asignado al punto de conexión de CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). En caso contrario, si la entrada de registro CNAME para el punto de conexión ya no existe o no contiene el subdominio cdnverify, continúe con [El dominio personalizado no está asignado al punto de conexión de CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>El dominio personalizado está asignado al punto de conexión de CDN mediante un registro CNAME

Cuando agregó un dominio personalizado a su punto de conexión, creó un registro CNAME en la tabla DNS de su registrador de dominios para asignarlo a su nombre de host del punto de conexión de CDN. Si ese registro CNAME todavía existe, pero no contiene el subdominio cdnverify, la entidad de certificación (CA) DigiCert lo usa para validar la titularidad de su dominio personalizado. 

El registro CNAME debe tener el formato siguiente, donde *Nombre* es el nombre de dominio personalizado y *Valor* es el nombre de host del punto de conexión de red CDN:

| NOMBRE            | type  | Valor                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Para obtener más información sobre los registros CNAME, consulte [Creación de los registros DNS de CNAME](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#create-the-cname-dns-records).

Si el registro CNAME tiene el formato correcto, DigiCert automáticamente comprueba su nombre de dominio personalizado y lo agrega al certificado de nombres alternativos del firmante (SAN). DigitCert no enviará un correo electrónico de comprobación, y usted no tendrá que aprobar la solicitud. El certificado tiene una validez de un año y se renovará automáticamente antes de expirar. Continúe con [Esperar a la propagación](#wait-for-propagation). 

La validación automática suele tarda unos minutos. Si no ve su dominio validado dentro de una hora, abra una incidencia de soporte técnico.

>[!NOTE]
>Si tiene un registro de autorización de entidad de certificación (CAA) con el proveedor de DNS, debe incluir DigiCert como entidad de certificación válida. Un registro CAA permite que los propietarios de dominios especifiquen con sus proveedores de DNS qué entidades de certificación están autorizadas para emitir certificados para su dominio. Si una entidad de certificación recibe un pedido de un certificado para un dominio que posee un registro CAA y dicha entidad no figura como emisor autorizado, no se le permite emitir el certificado para ese dominio o subdominio. Para obtener información acerca de cómo administrar registros de CAA, consulte [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Administrar registros de CAA). Para obtener una herramienta de registros de CAA, consulte [CAA Record Helper](https://sslmate.com/caa/) (Aplicación auxiliar de registros de CAA).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>El dominio personalizado no está asignado al punto de conexión de CDN

Si la entrada del registro CNAME para el punto de conexión ya no existe o contiene el subdominio cdnverify, siga el resto de las instrucciones que aparecen en este paso.

Después de habilitar HTTPS en un dominio personalizado, la entidad de certificación, DigiCert, validará la propiedad del dominio, para lo que se pondrá en contacto con el usuario inscrito, según la información de [WHOIS](http://whois.domaintools.com/) del dominio. El contacto se realiza a través de la dirección de correo electrónico (de forma predeterminada) o el número de teléfono que aparece en el registro WHOIS. Para que HTTPS pueda activarse en un dominio personalizado, antes es preciso completar la validación del dominio. Dispone de seis días laborables para aprobar el dominio. Se cancelarán automáticamente las solicitudes que no estén aprobadas en seis días laborables. 

![Registro WHOIS](./media/cdn-custom-ssl/whois-record.png)

DigiCert también envía un mensaje de verificación a direcciones de correo electrónico adicionales. Si la información del usuario inscrito de WHOIS es privada, asegúrese de que puede realizar las aprobaciones directamente desde una de las direcciones siguientes:

admin@&lt;su-nombre-de-dominio.com&gt;  
administrator@&lt;su-nombre-de-dominio.com&gt;  
webmaster@&lt;su-nombre-de-dominio.com&gt;  
hostmaster@&lt;su-nombre-de-dominio.com&gt;  
postmaster@&lt;su-nombre-de-dominio.com&gt;  

Debería recibir un correo electrónico en unos minutos, de forma similar al ejemplo siguiente, solicitando que apruebe la solicitud. Si utiliza un filtro de correo no deseado, agregue admin@digicert.com a la lista de permitidos. Si no recibe un correo electrónico en 24 horas, póngase en contacto con el equipo de soporte técnico de Microsoft.
    
![Correo electrónico de validación del dominio](./media/cdn-custom-ssl/domain-validation-email.png)

Al hacer clic en el vínculo de aprobación, obtendrá acceso al siguiente formulario de aprobación en línea: 
    
![Formulario de validación del dominio](./media/cdn-custom-ssl/domain-validation-form.png)

Siga las instrucciones que aparecen en el formulario; tiene dos opciones de comprobación:

- Puede aprobar todos los pedidos futuros realizados a través de la misma cuenta del mismo dominio raíz, por ejemplo, contoso.com. Se recomienda este método si piensa agregar más dominios personalizados al mismo dominio raíz.

- Puede aprobar solo el nombre de host específico usado en esta solicitud. Para las solicitudes posteriores se requiere una aprobación adicional.

Tras la aprobación, DigiCert agrega el nombre de dominio personalizado al certificado de SAN. El certificado tiene una validez de un año y se renovará automáticamente antes de expirar.

## <a name="wait-for-propagation"></a>Esperar a la propagación

Una vez que el nombre de dominio se valide, la característica HTTPS del dominio personalizado tarda entre 6 y 8 horas en estar activa. Una vez completado el proceso, el estado de HTTPS personalizado en Azure Portal se establece en **Habilitado** y los cuatro pasos de la operación en el cuadro de diálogo del dominio personalizado se marcan como completados. El dominio personalizado ahora está listo para usar HTTPS.

![Cuadro de diálogo Habilitar HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progreso de la operación

En la tabla siguiente se muestra el progreso de la operación que se produce cuando se habilita HTTPS. Después de habilitar HTTPS, los cuatro pasos de la operación aparecen en el cuadro de diálogo del dominio personalizado. A medida que cada paso se activa, aparecen detalles de subpasos adicionales bajo el paso. No se producirán todos estos subpasos. Después de que un paso se completa correctamente, aparece una marca de verificación verde junto a él. 

| Paso de la operación | Detalles del subpaso de la operación | 
| --- | --- |
| 1 Envío de la solicitud | Envío de la solicitud |
| | Se está enviando la solicitud HTTPS. |
| | Se ha enviado correctamente la solicitud HTTPS. |
| 2 Validación del dominio | El dominio se valida automáticamente si está asignado por CNAME al punto de conexión de CDN. En caso contrario, se enviará una solicitud de verificación al correo electrónico que aparece en el registro de su dominio (registrante WHOIS). Compruebe el dominio lo antes posible. |
| | La propiedad del dominio se ha validado correctamente. |
| | La solicitud de validación de la propiedad del dominio ha expirado (el cliente probablemente no respondió dentro del plazo de 6 días). HTTPS no se habilitará en el dominio. * |
| | El cliente rechazó la solicitud de validación de la propiedad del dominio. HTTPS no se habilitará en el dominio. * |
| 3 Aprovisionamiento del certificado | La entidad de certificación está emitiendo actualmente el certificado necesario para habilitar HTTPS en el dominio. |
| | El certificado se ha emitido y actualmente se está implementando en la red CDN. Esto puede tardar hasta 6 horas. |
| | El certificado se ha implementado correctamente en la red CDN. |
| 4 Finalizado | Se habilitó correctamente HTTPS en el dominio. |

\* Este mensaje no aparecerá a menos que se haya producido un error. 

Si se produce un error antes de que se envíe la solicitud, verá el mensaje de error siguiente:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="clean-up-resources---disable-https"></a>Clean up resources - disable HTTPS (Limpiar recursos: deshabilitar HTTPS)

En los pasos anteriores, habilitó el protocolo HTTPS en su dominio personalizado. Si ya no desea usar su dominio personalizado con HTTPS, puede deshabilitar HTTPS mediante estos pasos:

### <a name="disable-the-https-feature"></a>Deshabilitación de la característica HTTPS 

1. En el [Azure Portal](https://portal.azure.com), vaya a su perfil de CDN **Azure CDN estándar de Verizon** o **Azure CDN premium de Verizon**.

2. En la lista de puntos de conexión, haga clic en el que contiene el dominio personalizado.

3. Haga clic en el dominio personalizado en el que desea deshabilitar HTTPS.

    ![Lista de dominios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Haga clic en **Desactivar** para deshabilitar HTTPS y, a continuación, haga clic en **Aplicar**.

    ![Cuadro de diálogo Personalizar HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Esperar a la propagación

Cuando se deshabilita la característica HTTPS del dominio personalizado, puede tardar hasta 6 a 8 horas para que surta efecto. Una vez completado el proceso, el estado de HTTPS personalizado en Azure Portal se establece en **Deshabilitado** y los tres pasos de la operación en el cuadro de diálogo del dominio personalizado se marcan como completados. El dominio personalizado ya no usa HTTPS.

![Cuadro de diálogo Deshabilitar HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Progreso de la operación

En la tabla siguiente se muestra el progreso de la operación que se produce cuando se deshabilita HTTPS. Después de deshabilitar HTTPS, los tres pasos de la operación aparecen en el cuadro de diálogo del dominio personalizado. A medida que cada paso se activa, aparecen detalles adicionales bajo el paso. Después de que un paso se completa correctamente, aparece una marca de verificación verde junto a él. 

| Progreso de la operación | Detalles de la operación | 
| --- | --- |
| 1 Envío de la solicitud | Enviando la solicitud |
| 2 Desaprovisionamiento del certificado | Eliminando certificado |
| 3 Finalizado | Certificado eliminado |

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

1. *¿Quién es el proveedor de certificados y qué tipo de certificado se utiliza?*

    Microsoft usa un certificado de nombres alternativos del firmante (SAN) proporcionado por DigiCert. Un certificado SAN puede proteger varios nombres de dominio completos con un solo certificado.

2. *¿Puedo usar mi certificado dedicado?*
    
    Actualmente, no, pero está en nuestros planes.

3. *¿Qué debo hacer si no recibo el correo electrónico de comprobación de dominio de DigiCert?*

    Si tiene una entrada CNAME para el dominio personalizado, que apunte directamente a su nombre de host del punto de conexión (y no utiliza el nombre del subdominio cdnverify), no recibirá un correo electrónico de comprobación de dominio. La validación se produce automáticamente. En caso contrario, si no tiene una entrada CNAME y no ha recibido un correo electrónico en 24 horas, póngase en contacto con el servicio de soporte técnico de Microsoft.

4. *¿Son menos seguros los certificados SAN que los certificados dedicados?*
    
    Los certificados SAN siguen los mismos estándares de cifrado y seguridad que los certificados dedicados. Todos los certificados SSL emitidos usan SHA-256 para mejorar la seguridad del servidor.

5. *¿Puedo usar HTTPS de dominio personalizado con la red CDN de Azure de Akamai?*

    Actualmente, esta característica solo está disponible en la red CDN de Azure de Verizon. Microsoft está trabajando para que esta característica esté disponible en la red CDN de Azure de Akamai en los próximos meses.

6. *¿Necesito un registro de autorización de entidad de certificación con mi proveedor de DNS?*

    No, actualmente no se requiere un registro de autorización de entidad de certificación. Pero si tiene uno, debe incluir DigiCert como entidad de certificación válida.


## <a name="next-steps"></a>Pasos siguientes

Ha aprendido a:

> [!div class="checklist"]
> - Habilitar el protocolo HTTPS en su dominio personalizado
> - Validar el dominio
> - Deshabilitar el protocolo HTTPS en su dominio personalizado

Avance al siguiente tutorial para aprender a configurar el almacenamiento en caché en el punto de conexión de CDN.

> [!div class="nextstepaction"]
> [Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de caché](cdn-caching-rules.md)

