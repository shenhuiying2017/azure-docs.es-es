---
title: "Introducción a Azure CDN | Microsoft Docs"
description: "En este tema se muestra cómo habilitar Azure Content Delivery Network (CDN). El tutorial le guía a través de la creación de un perfil y un punto de conexión nuevos de la red CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: 81a88f6495ca9092ca3b55b8ffb3e41def3b4623
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="getting-started-with-azure-cdn"></a>Introducción a Azure CDN
En este artículo se describe cómo habilitar Azure Content Delivery Network (CDN) creando un perfil de red CDN y un punto de conexión nuevos.

> [!IMPORTANT]
> Para obtener una introducción a la red CDN y una lista de características, vea [Introducción a la red CDN](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Crear un nuevo perfil de CDN
Un perfil de red de entrega de contenido es una colección de puntos de conexión de red de entrega de contenido. Cada perfil puede contener uno o más de estos puntos de conexión de CDN. Puede usar varios perfiles para organizar sus puntos de conexión de la red CDN por dominio de Internet, aplicación web o cualquier otro criterio.

> [!NOTE]
> Una suscripción de Azure tiene límites predeterminados para los siguientes recursos:
> - El número de perfiles de red CDN que se pueden crear
> - El número de puntos de conexión que pueden crearse en un perfil de CDN 
> - El número de dominios personalizados que pueden asignarse a un punto de conexión
>
> Para más información acerca de los límites de suscripción de red CDN, vea [Límites de red CDN](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits).
>
> Los precios de red de entrega de contenido se aplican en el nivel de perfil de red de entrega de contenido. Por tanto, para utilizar una combinación de planes de tarifa de Azure CDN, deberá crear varios perfiles de red CDN.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Crear un nuevo extremo de CDN
**Para crear un nuevo punto de conexión de red CDN**

1. En [Azure Portal](https://portal.azure.com), vaya a su perfil de red CDN. Puede haberlo anclado al panel en el paso anterior. Si no es así, puede encontrarlo seleccionando **Todos los servicios** y, después, seleccionando **Perfiles de CDN**. En el panel **Perfiles de CDN**, seleccione el perfil al que planea agregar el punto de conexión. 
   
    Aparece el panel del perfil de CDN.
   
    ![Perfil de CDN][cdn-profile-settings]

2. Seleccione el **punto de conexión**.
   
    ![Botón Agregar punto de conexión][cdn-new-endpoint-button]
   
    Aparecerá el panel **Agregar un punto de conexión**.
   
    ![Agregar punto de conexión][cdn-add-endpoint]

3. En **Nombre**, escriba un nombre único para el nuevo punto de conexión de CDN. Este nombre se usa para obtener acceso a sus recursos almacenados en caché en el dominio `<endpointname>.azureedge.net`.

4. En **Tipo de origen**, seleccione un tipo de origen. Seleccione **Almacenamiento** para una cuenta de Azure Storage, **Servicio en la nube** para un servicio en la nube de Azure, **Aplic. web** para una aplic. web de Azure, o bien **Origen personalizado** para cualquier otro origen del servidor web públicamente accesible (hospedado en Azure o en otro lugar).
   
    ![Tipo de origen de la red CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. En **Nombre de host de origen**, seleccione o escriba el dominio de origen. En la lista desplegable se muestran todos los orígenes disponibles del tipo especificado en el paso 4. Si seleccionó **Origen personalizado** como su tipo de origen, escriba el dominio de su origen personalizado.
    
6. En **Ruta de acceso de origen**, escriba la ruta de acceso a los recursos que quiera almacenar en caché o déjela en blanco para permitir almacenar en caché cualquier recurso en el dominio especificado en el paso 5.
    
7. En el **Encabezado del host de origen**, escriba el encabezado de host que quiera que Azure CDN envíe con cada solicitud o deje el valor predeterminado.
   
   > [!WARNING]
   > Algunos tipos de orígenes, como Azure Storage y Aplicaciones web, requieren que el encabezado del host coincida con el dominio del origen. A menos que tenga un origen que requiera un encabezado de host diferente de su dominio, debe dejar el valor predeterminado.
   > 
    
8. Para **Protocolo** y **Puerto de origen**, especifique los protocolos y los puertos que se usan para tener acceso a sus recursos en el origen. Se debe seleccionar al menos un protocolo (HTTP o HTTPS). Para acceder al contenido HTTPS, use el dominio proporcionado por la red CDN (`<endpointname>.azureedge.net`). 
   
   > [!NOTE]
   > El valor de **Puerto de origen** solo determina el puerto que utiliza el punto de conexión para recuperar información del origen. El propio punto de conexión solo está disponible para los clientes finales en los puertos HTTP y HTTPS predeterminados (80 y 443), con independencia de cuál sea el **puerto de origen**.  
   > 
   > Los puntos de origen de los perfiles de **Azure CDN de Akamai** no permiten el intervalo completo de puertos TCP para los puertos de origen. Para obtener una lista de los puertos de origen que no se permiten, consulte [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx)(Puertos de origen permitidos de la red CDN de Azure de Akamai).  
   > 
   > Cuando accede a contenido de la red CDN mediante HTTPS, existen las siguientes restricciones:
   > 
   > * Use el certificado SSL proporcionado por la red CDN. No se admiten certificados de terceros.
   > * La compatibilidad con HTTPS para dominios personalizados de red CDN de Azure solo está disponible con los productos **Azure CDN de Verizon** (ediciones Standard y Premium). No lo estará en productos **CDN de Azure de Akamai**. Para más información, consulte [Configuración de HTTPS en un dominio personalizado de la red CDN de Azure](cdn-custom-ssl.md).
    
9. Seleccione **Agregar** para crear el nuevo punto de conexión.
   
   Una vez creado el punto de conexión, aparece en la lista de puntos de conexión del perfil.
    
   ![Punto de conexión de CDN][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Dado que se tarda un tiempo en que el registro se propague, el punto de conexión no estará disponible para su uso de forma inmediata. Para los perfiles de la **red CDN de Azure de Akamai** , la propagación normalmente se completa en un minuto. Para los perfiles de la **red CDN de Azure de Verizon**, la propagación normalmente se completa en 90 minutos, pero en algunos casos puede tardar más tiempo.
    > 
    > Si intenta usar el nombre de dominio de la red CDN antes de que la configuración del punto de conexión se haya propagado a los POP, puede recibir estados de respuesta HTTP 404. Si han pasado varias horas desde que creó el punto de conexión y aún recibe estados de respuesta 404, consulte [Solución de problemas de redes CDN que devuelven errores 404](cdn-troubleshoot-endpoint.md).
    > 
    > 

## <a name="see-also"></a>Otras referencias
* [Control del comportamiento del almacenamiento en caché de las solicitudes con cadenas de consulta](cdn-query-string.md)
* [Asignación del contenido de la red CDN a un dominio personalizado](cdn-map-content-to-custom-domain.md)
* [Carga previa de activos en un punto de conexión de CDN de Azure](cdn-preload-endpoint.md)
* [Depuración de un punto de conexión de red de entrega de contenido de Azure](cdn-purge-endpoint.md)
* [Solución de problemas de redes CDN que devuelven errores 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
