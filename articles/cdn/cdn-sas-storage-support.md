---
title: Uso de la red CDN de Azure con SAS | Microsoft Docs
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>Uso de la red CDN de Azure con SAS

Cuando suministra contenido proveniente del contenedor de almacenamiento de su cuenta de almacenamiento, es posible que quiera proteger la manera en que los usuarios pueden acceder a los archivos mediante la concesión de acceso privado al contenedor de almacenamiento. De lo contrario, cualquier persona que conozca la dirección URL de un contenedor de almacenamiento para el que concedió acceso público puede acceder a este. Para proteger una cuenta de almacenamiento a la que la red de entrega de contenido (CDN) puede acceder, puede usar la característica Firma de acceso compartido (SAS) desde Azure Storage para conceder acceso limitado a contenedores de almacenamiento privados.

Una SAS es un identificador URI que concede derechos de acceso restringido a los recursos de Azure Storage sin exponer la clave de cuenta. Puede proporcionar una SAS a los clientes que no son de confianza para entregarles la clave de cuenta de almacenamiento, pero a los que sí desea delegar acceso a ciertos recursos de la cuenta de almacenamiento. Mediante la distribución de un identificador URI de firma de acceso compartido a estos clientes, les concede acceso a un recurso durante un período de tiempo especificado.
 
SAS permite definir diversos parámetros de acceso a un blob, como horas de inicio y expiración, permisos (lectura o escritura) e intervalos IP. En este artículo se describe cómo usar SAS junto con la red CDN de Azure. Para más información sobre la característica SAS, incluido cómo crearla y sus opciones de parámetros, consulte [Uso de firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configuración de la red CDN de Azure para trabajar con la característica SAS de almacenamiento
Se recomiendan las tres opciones siguientes para usar SAS con la red CDN de Azure. En todas las opciones se presupone que ya creó una SAS en funcionamiento (consulte los requisitos previos). 
 
### <a name="prerequisites"></a>requisitos previos
Para comenzar, cree una cuenta de almacenamiento y, luego, genere una SAS para el recurso. Puede generar dos tipos de firmas de acceso compartido: una SAS de servicio o una SAS de cuenta. Para más información, consulte [Tipos de firmas de acceso compartido](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Una vez que genera una SAS, puede acceder al archivo de almacenamiento de blobs con una dirección URL con el formato siguiente: `https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
Por ejemplo: 
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para más información sobre cómo establecer los parámetros, consulte las [consideraciones sobre los parámetros de SAS](#sas-parameter-considerations) y los [parámetros de las firmas de acceso compartido](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Configuración de SAS de la red CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>Opción 1: Uso de SAS con paso a través al almacenamiento de blobs desde la red CDN

Esta opción es la más simple y solo usa un único token de SAS, que se pasa desde la red CDN al servidor de origen. Es compatible con la **red CDN de Azure de Verizon**, tanto para el perfil Estándar como para el perfil Premium, y la **red CDN de Azure de Akamai**. 
 
1. Seleccione un punto de conexión, haga clic en **Reglas de caché** y seleccione **Almacenar en caché todas las URL únicas** en la lista **Almacenamiento en caché de cadena de consulta**.

    ![Reglas de caché de la red CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Una vez que configura SAS en la cuenta de almacenamiento, use el token de SAS con la dirección URL de la red CDN para acceder al archivo. 
   
   La dirección URL resultante tiene el formato siguiente: `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   Por ejemplo:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ajuste la duración de la caché mediante las reglas de caché o al agregar los encabezados `Cache-Control` en el origen. Debido a que la red CDN trata al token de SAS como una cadena de consulta sin formato, el procedimiento recomendado que debe seguir es configurar una duración de la caché con la misma hora de expiración que la SAS o una anterior. De lo contrario, si un archivo se almacena en caché durante más tiempo que durante el cual la SAS está activa, es posible acceder al archivo desde el servidor de origen de la red CDN una vez que haya transcurrido la hora de expiración de la SAS. Si esto ocurre y desde que no se pueda acceder al archivo en caché, debe realizar una operación de purga en el archivo para eliminarlo de la memoria caché. Para información sobre cómo establecer la duración de la caché en la red CDN, consulte [Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con reglas de almacenamiento en caché](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>Opción 2: Token de seguridad de la red CDN oculto mediante la regla de reescritura
 
Con esta opción, puede proteger el almacenamiento de blobs de origen sin requerir un token de SAS para el usuario de la red CDN. Es posible que desee usar esta opción si no necesita restricciones de acceso específicas para el archivo, pero quiere evitar que los usuarios accedan al origen de almacenamiento de manera directa para mejorar los tiempos de descarga de la red CDN. Esta opción solo está disponible para los perfiles de la **red CDN Premium de Azure de Verizon**. 
 
1. Use el [motor de reglas](cdn-rules-engine.md) para crear una regla de reescritura de direcciones URL. Las reglas nuevas tardan unos 90 minutos en propagarse.

   ![Botón de administración de CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Botón del motor de reglas de CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Esta regla de reescritura de direcciones URL de ejemplo tiene los patrones siguientes:
   
   Origen:   
   `/test/demo.jpg`
   
   Destino:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regla de reescritura de direcciones URL de CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. Acceda al archivo en la red CDN sin el token de SAS con el formato siguiente: `https://<endpoint>.azureedge.net/<folder>/<file>`
 
   Por ejemplo:    
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   Tenga en cuenta que nadie, independientemente de si usa un token de SAS, puede acceder a un punto de conexión de CDN. 

3. Ajuste la duración de la caché mediante las reglas de caché o al agregar los encabezados `Cache-Control` en el origen. Debido a que la red CDN trata al token de SAS como una cadena de consulta sin formato, el procedimiento recomendado que debe seguir es configurar una duración de la caché con la misma hora de expiración que la SAS o una anterior. De lo contrario, si un archivo se almacena en caché durante más tiempo que durante el cual la SAS está activa, es posible acceder al archivo desde el servidor de origen de la red CDN una vez que haya transcurrido la hora de expiración de la SAS. Si esto ocurre y desde que no se pueda acceder al archivo en caché, debe realizar una operación de purga en el archivo para eliminarlo de la memoria caché. Para información sobre cómo establecer la duración de la caché en red CDN, consulte [Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con reglas de almacenamiento en caché](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opción 3: Uso de la autenticación de token de seguridad de red CDN con una regla de reescritura

Esta opción es la más segura y personalizable. Para usar la autenticación de token de seguridad de la red CDN, debe tener un perfil de **red CDN Premium de Azure de Verizon**. El acceso de cliente se basa en los parámetros de seguridad establecidos en el token de seguridad de red CDN. Sin embargo, si la SAS deja de ser válida, la red CDN no podrá volver a validar el contenido del servidor de origen.

1. [Cree un token de seguridad de red CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) y actívelo mediante el motor de reglas para el punto de conexión de red CN y la ruta de acceso donde los usuarios pueden acceder al archivo.

   Una dirección URL de SAS tiene el formato siguiente:   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   Por ejemplo:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Las opciones de parámetro de una autenticación de token de seguridad de red CDN son distintas a las opciones de parámetro para un token de SAS. Si elige usar una hora de expiración cuando crea un token de seguridad de red CDN, establézcalo en el mismo valor que la hora de expiración del token de SAS. De este modo se garantiza que la hora de expiración sea predecible. 
 
2. Use el [motor de reglas](cdn-rules-engine.md) para crear una regla de reescritura de direcciones URL a fin de habilitar el acceso de token a todos los blobs del contenedor. Las reglas nuevas tardan unos 90 minutos en propagarse.

   Esta regla de reescritura de direcciones URL de ejemplo tiene los patrones siguientes:
   
   Origen:   
   `/test/demo.jpg`
   
   Destino:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![Regla de reescritura de direcciones URL de CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. Cuando renueve la SAS, actualice la regla de reescritura de direcciones URL para usar el token de SAS nuevo. 

## <a name="sas-parameter-considerations"></a>Consideraciones sobre los parámetros de SAS

Como los parámetros de SAS no son visibles para la red CDN, esta no puede cambiar su comportamiento de entrega en función de dichos parámetros. Las restricciones de parámetro definidas solo se aplican en las solicitudes que la red CDN hace en el servidor de origen y no en las solicitudes del cliente a la red CDN. Resulta importante considerar esta distinción cuando se establecen los parámetros de SAS. Si se requieren estas funcionalidades avanzadas y se usa la [opción 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), establezca las restricciones adecuadas en el token de seguridad de CDN.

| Nombre de parámetro de SAS | DESCRIPCIÓN |
| --- | --- |
| Iniciar | Hora en que la red CDN puede comenzar a acceder al archivo de blob. Debido al desplazamiento del reloj (es decir, cuando una señal de reloj llega a distintas horas para componentes diferentes), elija un momento 15 minutos antes si desea que el recurso esté disponible de inmediato. |
| End | Tiempo después del cual la CDN ya no puede acceder al archivo de blob. Los archivos almacenados previamente en caché en la red CDN siguen siendo accesibles. Para controlar la hora de expiración de los archivos, establezca la hora de expiración correspondiente en el token de seguridad de red CDN o purgue el recurso. |
| Direcciones IP permitidas | Opcional. Si usa la **red CDN de Azure de Verizon**, puede establecer este parámetro en los intervalos que aparecen definidos en [Red CDN de Azure de intervalos IP del servidor perimetral de Verison](https://msdn.microsoft.com/library/mt757330.aspx). Si usa la **red CDN de Azure de Akamai**, no puede establecer el parámetro de intervalos IP porque las direcciones IP no son estáticas.|
| Protocolos admitidos | Protocolos que se permiten para una solicitud realizada con la SAS de cuenta. Se recomienda la configuración HTTPS.|

## <a name="see-also"></a>Otras referencias
- [Uso de Firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Firmas de acceso compartido, Parte 2: Creación y uso de una SAS con Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Protección de recursos de Azure Content Delivery Network con la autenticación por tokens](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
