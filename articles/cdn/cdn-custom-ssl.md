---
title: Configurar HTTPS en un dominio personalizado de Azure Content Delivery Network | Microsoft Docs
description: "Aprenda a habilitar o deshabilitar HTTPS en un punto de conexión de la red CDN de Azure con un dominio personalizado."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: casoper
ms.openlocfilehash: 82de79cde208cdce1ed7cbd600f1e804ff1d45ff
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="configure-https-on-an-azure-content-delivery-network-custom-domain"></a>Configurar HTTPS en un dominio personalizado de Azure Content Delivery Network

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft admite el protocolo HTTPS para dominios personalizados en Azure Content Delivery Network (CDN). Gracias a la compatibilidad de los dominios personalizados con HTTPS, puede entregar contenido seguro a través de SSL mediante su propio nombre de dominio y así mejorar la seguridad de los datos mientras están en tránsito. El flujo de trabajo para habilitar HTTPS en su dominio personalizado se simplifica mediante la habilitación con un solo clic y la administración completa de certificados; todo ello sin ningún costo adicional.

Es fundamental garantizar tanto la privacidad como la integridad de datos de la información confidencial de las aplicaciones web mientras estén en tránsito. Gracias a este protocolo HTTPS, se asegurará de que la información confidencial se cifre cuando se envíe a través de Internet. Proporciona confianza, autenticación y protege las aplicaciones web de posibles ataques. De forma predeterminada, Azure CDN es compatible con HTTPS en un punto de conexión de la red CDN. Por ejemplo, si crea un punto de conexión de la red CDN desde la propia red CDN de Azure (como `https://contoso.azureedge.net`), HTTPS se habilita de manera predeterminada. Además, gracias a la compatibilidad con HTTPS en un dominio personalizado, también se puede habilitar la entrega segura en un dominio personalizado (por ejemplo, `https://www.contoso.com`). 

Algunos de los atributos clave de la característica de HTTPS son:

- Sin costo adicional: la adquisición o renovación de certificados no tiene costos y el tráfico HTTPS no supone un costo adicional. Solo es preciso pagar por los GB de salida de la red CDN.

- Habilitación simple: el aprovisionamiento en un solo clic está disponible desde [Azure Portal](https://portal.azure.com). También puede utilizar la API de REST u otras herramientas de desarrollo para habilitar la característica.

- Administración de certificados completa: toda la adquisición y administración de certificados se controla de manera automática. Los certificados se aprovisionan y se renuevan automáticamente antes de la expiración, lo que elimina los riesgos de interrupción del servicio debido a la expiración de un certificado.

>[!NOTE] 
>Antes de habilitar la compatibilidad con HTTPS, se debe haber establecido un [dominio personalizado de la red CDN de Azure](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Habilitación de HTTPS

Para habilitar HTTPS en un dominio personalizado, siga estos pasos:

### <a name="step-1-enable-the-feature"></a>Paso 1: Habilitación de la característica 

1. En [Azure Portal](https://portal.azure.com), vaya a su perfil de CDN estándar o Premium de Verizon.

2. En la lista de puntos de conexión, haga clic en el que contiene el dominio personalizado.

3. Haga clic en el dominio personalizado en el que desea habilitar HTTPS.

    ![Lista de dominios personalizados](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Haga clic en **Activar** para habilitar HTTPS y, a continuación, haga clic en **Aplicar**.

    ![Estado de HTTPS de dominio personalizado](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Paso 2: Validación del dominio

>[!IMPORTANT] 
>Para que HTTPS pueda activarse en un dominio personalizado, antes es preciso completar la validación del dominio. Dispone de seis días laborables para aprobar el dominio. Se cancelarán automáticamente las solicitudes que no estén aprobadas en seis días laborables. 

Después de habilitar HTTPS en un dominio personalizado, la entidad de certificación, DigiCert, validará la propiedad del dominio, para lo que se pondrá en contacto con el usuario inscrito, según la información de [WHOIS](http://whois.domaintools.com/) del dominio. El contacto se realiza a través de la dirección de correo electrónico (de forma predeterminada) o el número de teléfono que aparece en el registro WHOIS. 

>[!NOTE]
>Si tiene un registro de autorización de entidad de certificación (CAA) con el proveedor de DNS, debe incluir DigiCert como entidad de certificación válida. Un registro CAA permite que los propietarios de dominios especifiquen con sus proveedores de DNS qué entidades de certificación están autorizadas para emitir certificados para su dominio. Si una entidad de certificación recibe un pedido de un certificado para un dominio que posee un registro CAA y dicha entidad no figura como emisor autorizado, no se le permite emitir el certificado para ese dominio o subdominio. Para obtener información acerca de cómo administrar registros de CAA, consulte [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Administrar registros de CAA). Para obtener una herramienta de registros de CAA, consulte [CAA Record Helper](https://sslmate.com/caa/) (Aplicación auxiliar de registros de CAA).

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

- Puede aprobar todos los pedidos futuros realizados a través de la misma cuenta del mismo dominio raíz, por ejemplo, `contoso.com`. Se recomienda este método si piensa agregar más dominios personalizados al mismo dominio raíz.

- Puede aprobar solo el nombre de host específico usado en esta solicitud. Para las solicitudes posteriores se requiere una aprobación adicional.

Tras la aprobación, DigiCert agrega el nombre de dominio personalizado al certificado de nombres alternativos del firmante (SAN). El certificado tiene una validez de un año y se renovará automáticamente antes de expirar.

### <a name="step-3-wait-for-propagation"></a>Paso 3: Esperar a la propagación

Una vez que el nombre de dominio se valide, la característica HTTPS del dominio personalizado tarda entre 6 y 8 horas en estar activa. Una vez completado el proceso, el estado de HTTPS personalizado en Azure Portal se establece en **Habilitado** y los cuatro pasos de la operación en el cuadro de diálogo del dominio personalizado se marcan como completados. El dominio personalizado ahora está listo para usar HTTPS.

![Cuadro de diálogo Habilitar HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progreso de la operación

En la tabla siguiente se muestra el progreso de la operación que se produce cuando se habilita HTTPS. Después de habilitar HTTPS, los cuatro pasos de la operación aparecen en el cuadro de diálogo del dominio personalizado. A medida que cada paso se activa, aparecen detalles adicionales bajo el paso indicando su progreso. Después de que un paso se completa correctamente, aparece una marca de verificación verde junto a él. 

| Paso de la operación | Detalles del paso de la operación | 
| --- | --- |
| 1 Envío de la solicitud | Envío de la solicitud |
| | Se está enviando la solicitud HTTPS. |
| | Se ha enviado correctamente la solicitud HTTPS. |
| 2 Validación del dominio | Le hemos enviado un correo electrónico que le pide que validar la propiedad de dominio. Esperando su confirmación. |
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

## <a name="disabling-https"></a>Deshabilitación de HTTPS

Después de haber habilitado HTTPS en un dominio público, puede deshabilitarlo más adelante. Para deshabilitar HTTPS, siga estos pasos:

### <a name="step-1-disable-the-feature"></a>Paso 1: Deshabilitación de la característica 

1. En [Azure Portal](https://portal.azure.com), vaya a su perfil de CDN estándar o Premium de Verizon.

2. En la lista de puntos de conexión, haga clic en el que contiene el dominio personalizado.

3. Haga clic en el dominio personalizado en el que desea deshabilitar HTTPS.

    ![Lista de dominios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Haga clic en **Desactivar** para deshabilitar HTTPS y, a continuación, haga clic en **Aplicar**.

    ![Cuadro de diálogo Personalizar HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Paso 2: Esperar a la propagación

Cuando se deshabilita la característica HTTPS del dominio personalizado, puede tardar hasta 6 a 8 horas para que surta efecto. Una vez completado el proceso, el estado de HTTPS personalizado en Azure Portal se establece en **Deshabilitado** y los tres pasos de la operación en el cuadro de diálogo del dominio personalizado se marcan como completados. El dominio personalizado ya no usa HTTPS.

![Cuadro de diálogo Deshabilitar HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progreso de la operación

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

    Si no recibe un correo electrónico en 24 horas, póngase en contacto con el equipo de soporte técnico de Microsoft.

4. *¿Son menos seguros los certificados SAN que los certificados dedicados?*
    
    Los certificados SAN siguen los mismos estándares de cifrado y seguridad que los certificados dedicados. Todos los certificados SSL emitidos usan SHA-256 para mejorar la seguridad del servidor.

5. *¿Puedo usar HTTPS de dominio personalizado con la red CDN de Azure de Akamai?*

    Actualmente, esta característica solo está disponible en la red CDN de Azure de Verizon. Microsoft está trabajando para que esta característica esté disponible en la red CDN de Azure de Akamai en los próximos meses.

6. *¿Necesito un registro de autorización de entidad de certificación con mi proveedor de DNS?*
   No, actualmente no se requiere un registro de autorización de entidad de certificación. Pero si tiene uno, debe incluir DigiCert como entidad de certificación válida.


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a configurar un [dominio personalizado en un punto de conexión de la red CDN de Azure](./cdn-map-content-to-custom-domain.md)


