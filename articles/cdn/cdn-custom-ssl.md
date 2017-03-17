---
title: "Habilitación de HTTPS en un dominio personalizado de la red CDN de Azure | Microsoft Docs"
description: "Aprenda a habilitar HTTPS en un punto de conexión de la red CDN de Azure con un dominio personalizado."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b334ba6bbec1d0a7e23a514174bffae01c7fff05
ms.lasthandoff: 03/04/2017


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>Habilitación de HTTPS en un dominio personalizado de la red CDN de Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

La compatibilidad de HTTPS con los dominios personalizados de la red CDN de Azure permite entregar contenido seguro a través de SSL con su propio nombre de dominio para mejorar la seguridad de los datos mientras están en tránsito. El flujo de trabajo de un extremo a otro para habilitar HTTPS en un dominio personalizado se simplifica mediante la habilitación con un solo clic y la administración completa de certificados, y todo ello sin ningún costo adicional.

Es fundamental para garantizar tanto la privacidad como la integridad de todos los datos confidenciales de las aplicaciones web mientras están en tránsito. El uso del protocolo HTTPS garantiza que la información confidencial se cifra cuando se envía a través de Internet. Proporciona confianza y autenticación, y protege las aplicaciones web de posibles ataques. Actualmente, la red CDN de Azure admite HTTPS en un punto de conexión de la red CDN. Por ejemplo, si crea un punto de conexión de la red CDN desde la propia red CDN de Azure (p.ej., https://contoso.azureedge.net), HTTPS se habilita de manera predeterminada. Ahora, con HTTPS de dominio personalizado, también se puede habilitar la entrega segura para un dominio personalizado (p. ej., https://www.contoso.com). 

Algunos de los atributos clave de la característica de HTTPS son:

- Sin costo adicional: la adquisición o renovación de certificados no tiene costos y el tráfico HTTPS no supone un costo adicional. Solo es preciso pagar por los GB de salida de la red CDN.

- Habilitación simple: el aprovisionamiento en un solo clic está disponible desde [Azure Portal](https://portal.azure.com). También puede utilizar la API de REST u otras herramientas de desarrollo para habilitar la característica.

- Administración de certificados completa: toda la adquisición y administración de certificados se controla de manera automática. Los certificados se aprovisionan y renuevan automáticamente antes de su expiración. De esta forma desaparece completamente el riesgo de que se interrumpa el servicio como consecuencia de la expiración del certificado.

>[!NOTE] 
>Antes de habilitar la compatibilidad con HTTPS, se debe haber establecido un [dominio personalizado de la red CDN de Azure](./cdn-map-content-to-custom-domain.md).

## <a name="step-1-enabling-the-feature"></a>Paso 1: Habilitar la característica 

1. En [Azure Portal](https://portal.azure.com), vaya a su perfil de CDN estándar o Premium de Verizon.

2. En la lista de puntos de conexión, haga clic en el que contiene el dominio personalizado.

3. Haga clic en el dominio personalizado en el que desea habilitar HTTPS.

    ![Hoja de puntos de conexión](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Haga clic en **Activado** para habilitar HTTPS y guardar el cambio.

    ![Cuadro de diálogo Personalizar HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>Paso 2: Validación de dominio

>[!IMPORTANT] 
>Para que HTTPS pueda activarse en un dominio personalizado, antes es preciso completar la validación del dominio. Dispone de seis días laborables para aprobar el dominio. Si no se realiza la aprobación en 6 días laborables, la solicitud se cancelará.  

Después de habilitar HTTPS en un dominio personalizado, nuestro proveedor de certificados HTTPS, DigiCert, validará la propiedad del dominio, para lo que se pondrá en contacto con el inscrito del dominio, para lo que usará la información de WHOIS del inscrito, a través del correo electrónico (de forma predeterminada) o del teléfono. DigiCert también enviará el correo electrónico de comprobación a las siguientes direcciones. Si la información del inscrito de WHOIS es privada, asegúrese de que puede realizar las aprobaciones directamente desde una de estas direcciones.

>admin@<su-nombre-de-dominio.com> administrator@<su-nombre-de-dominio.com>  
>webmaster@<su-nombre-de-dominio.com>  
>hostmaster@<su-nombre-de-dominio.com>  
>postmaster@<su-nombre-de-dominio.com>


Al recibir el correo electrónico, tiene dos opciones de comprobación:

1. Puede aprobar todos los pedidos futuros realizados a través de la misma cuenta del mismo dominio de raíz, por ejemplo, consoto.com. Éste enfoque se recomienda si se planea agregar dominios personalizados en el futuro al mismo dominio raíz.
 
2. Se puede aprobar solo el nombre de host específico usado en esta solicitud. Para las solicitudes posteriores se requerirá una aprobación adicional.

    Correo electrónico de ejemplo:
    
    ![Cuadro de diálogo Personalizar HTTPS](./media/cdn-custom-ssl/domain-validation-email-example.png)

Tras la aprobación, DigiCert agregará el nombre de dominio personalizado al certificado de SAN. El certificado tendrá una validez de un año y se renovará automáticamente antes de expirar.

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>Paso 3: Esperar a la propagación y después empezar a usar la característica

Una vez que el nombre de dominio se valide, la característica HTTPS del dominio personalizado tardará entre 6 y 8 horas en estar activa. Una vez completado el proceso, el estado de "personalizar HTTPS" de Azure Portal se establecerá en "Habilitado". HTTPS con el dominio personalizado ya está listo para su uso.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

1. *¿Quién es el proveedor de certificados y qué tipo de certificado se utiliza?*

    Se usa el certificado SAN (nombres alternativos de firmantes) proporcionado por DigiCert. Un certificado SAN puede proteger varios nombres de dominio completo con un solo certificado.

2. *¿Puedo usar mi certificado dedicado?*
    
    Actualmente, no, pero está en nuestros planes.

3. *¿Qué debo hacer si no recibo el correo electrónico de comprobación de dominio de DigiCert?*

    Si no recibe un correo electrónico en 24 horas, póngase en contacto con Microsoft.

4. *¿Son menos seguros los certificados SAN que los certificados dedicados?*
    
    Los certificados SAN siguen los mismos estándares de cifrado y seguridad que los certificados dedicados. Todos los certificados SSL emitidos utilizan SHA-256 para mejorar la seguridad del servidor.

5. *¿Puedo usar HTTPS de dominio personalizado con la red CDN de Azure de Akamai?*

    Actualmente, esta característica solo está disponible en la red CDN de Azure de Verizon. Estamos trabajando para que esta característica con esté disponible en la red CDN de Azure CDN de Akamai.


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a configurar un [dominio personalizado en un punto de conexión de la red CDN de Azure](./cdn-map-content-to-custom-domain.md)



