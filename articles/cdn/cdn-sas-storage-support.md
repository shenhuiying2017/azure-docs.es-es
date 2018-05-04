---
title: Uso de la red Azure CDN con SAS | Microsoft Docs
description: La Red CDN de Azure admite el uso de firma de acceso compartido (SAS) para conceder acceso limitado a los contenedores de almacenamiento privado.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: v-deasim
ms.openlocfilehash: 8b609beb67cfb0873bf9926ca648f0ad5568ad2e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="using-azure-cdn-with-sas"></a>Uso de la red Azure CDN con SAS

Al configurar una cuenta de almacenamiento para la Red de entrega de contenido (CDN) de Azure para usar al contenido de la memoria caché, cualquier usuario que conozca las direcciones URL de los contenedores de almacenamiento tiene acceso de manera predeterminada a los archivos que se hayan cargado. Si quiere proteger los archivos de la cuenta de almacenamiento, puede cambiar el acceso de los contenedores de almacenamiento de público a privado. Pero si lo hace, nadie podrá tener acceso a los archivos. 

Si quiere conceder acceso limitado a los contenedores de almacenamiento privado, puede usar la característica de firma de acceso compartido (SAS) de la cuenta de Azure Storage. Una SAS es un identificador URI que concede derechos de acceso restringido a los recursos de Azure Storage sin exponer la clave de cuenta. Puede proporcionar una SAS a los clientes que no son de confianza para entregarles la clave de la cuenta de almacenamiento, pero a los que sí desea delegar acceso a ciertos recursos de la cuenta de almacenamiento. Mediante la distribución de un identificador URI de firma de acceso compartido a estos clientes, les concede acceso a un recurso durante un período de tiempo especificado.
 
Con una SAS, puede definir diversos parámetros de acceso a un blob, como horas de inicio y expiración, permisos (lectura o escritura) e intervalos IP. En este artículo se describe cómo usar SAS junto con la red Azure CDN. Para más información sobre la característica SAS, incluido cómo crearla y sus opciones de parámetros, consulte [Uso de firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configuración de Azure CDN para trabajar con la característica SAS de almacenamiento
Se recomiendan las tres opciones siguientes para usar SAS con la red Azure CDN. En todas las opciones se presupone que ya creó una SAS en funcionamiento (consulte los requisitos previos). 
 
### <a name="prerequisites"></a>requisitos previos
Para comenzar, cree una cuenta de almacenamiento y, luego, genere una SAS para el recurso. Puede generar dos tipos de firmas de acceso compartido: una SAS de servicio o una SAS de cuenta. Para más información, consulte [Tipos de firmas de acceso compartido](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Cuando haya generado un token de SAS, tiene acceso a su archivo de Blob Storage mediante la anexión de `?sv=<SAS token>` a la dirección URL. Esta dirección URL tiene el formato siguiente: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Por ejemplo: 
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Para más información sobre cómo establecer los parámetros, consulte [Consideraciones sobre los parámetros de SAS](#sas-parameter-considerations) y [Parámetros de la firma de acceso compartido](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Configuración de SAS de la red CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opción 1: Uso de SAS con paso a través a Blob Storage desde la red CDN de Azure

Esta opción es la más simple y solo usa un único token de SAS, que se pasa desde la red CDN de Azure al servidor de origen. Es compatible con la red **CDN de Azure de Verizon** y la red **CDN de Azure de Akamai**. 
 
1. Elija un punto de conexión, seleccione **Reglas de caché** y luego, en la lista **Almacenamiento en caché de cadenas de consulta**, elija **Almacenar en caché todas las URL únicas**.

    ![Reglas de caché de la red CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Después de configurar SAS en la cuenta de almacenamiento, use el token de SAS con la dirección URL de la red CDN de Azure para tener acceso al archivo. 
   
   La dirección URL resultante tiene el formato siguiente: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Por ejemplo:    
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Ajuste la duración de la caché mediante las reglas de caché o al agregar los encabezados `Cache-Control` en el servidor de origen. Debido a que la red CDN de Azure trata el token de SAS como cadena de consulta sin formato, el procedimiento recomendado que debe seguirse es configurar una duración de la caché con la misma hora de expiración que la SAS o una anterior. De lo contrario, si un archivo se almacena en caché más tiempo que el que está activa la SAS, es posible acceder al archivo desde el servidor de origen de la red CDN de Azure una vez que haya transcurrido la hora de expiración de la SAS. Si esto ocurre y quiere que el archivo en caché no sea accesible, debe realizar una operación de purga en el archivo para eliminarlo de la memoria caché. Para obtener información sobre cómo establecer la duración de la caché en la red CDN de Azure, vea [Control del comportamiento del almacenamiento en caché de la red CDN de Azure con reglas de caché](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-security-token-using-a-rewrite-rule"></a>Opción 2: Token de seguridad de la red CDN oculto mediante una regla de reescritura
 
Con esta opción, puede proteger el almacenamiento de blobs de origen sin exigir que un usuario de la red CDN de Azure utilice un token de SAS en la dirección URL. Puede que quiera usar esta opción si no necesita restricciones de acceso específicas para el archivo, pero quiere evitar que los usuarios tengan acceso directo al origen de almacenamiento para mejorar los tiempos de descarga de la red CDN de Azure. Esta opción solo está disponible para los perfiles de **Azure CDN Premium de Verizon**. 
 
1. Use el [motor de reglas](cdn-rules-engine.md) para crear una regla de reescritura de direcciones URL. Las reglas nuevas tardan unos 90 minutos en propagarse.

   ![Botón de administración de CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Botón del motor de reglas de CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   La siguiente regla de reescritura de direcciones URL de ejemplo usa un patrón de expresión regular con un grupo de captura y un punto de conexión de nombre *storagedemo*:
   
   Origen:   
   `(/test/*.)`
   
   Destino:   
   ```
   $1?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![Regla de reescritura de direcciones URL de CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-2.png)

2. Cuando la nueva regla esté activa, tiene acceso al archivo en la red CDN de Azure sin usar un token de SAS en la dirección URL, en el formato siguiente: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Por ejemplo:    
   `https://demoendpoint.azureedge.net/container1/demo.jpg`
       
   Tenga en cuenta que nadie, independientemente de que use un token de SAS, tiene acceso a un punto de conexión en la red CDN. 

3. Ajuste la duración de la caché mediante las reglas de caché o al agregar los encabezados `Cache-Control` en el servidor de origen. Debido a que la red CDN de Azure trata el token de SAS como cadena de consulta sin formato, el procedimiento recomendado que debe seguirse es configurar una duración de la caché con la misma hora de expiración que la SAS o una anterior. De lo contrario, si un archivo se almacena en caché más tiempo que el que está activa la SAS, es posible acceder al archivo desde el servidor de origen de la red CDN de Azure una vez que haya transcurrido la hora de expiración de la SAS. Si esto ocurre y quiere que el archivo en caché no sea accesible, debe realizar una operación de purga en el archivo para eliminarlo de la memoria caché. Para obtener información sobre cómo establecer la duración de la caché en la red CDN de Azure, vea [Control del comportamiento del almacenamiento en caché de la red CDN de Azure con reglas de caché](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opción 3: Uso de la autenticación de token de seguridad de red CDN con una regla de reescritura

Esta opción es la más segura y personalizable. Para usar la autenticación de token de seguridad de la red CDN de Azure, debe tener un perfil de **red CDN premium de Azure de Verizon**. El acceso de cliente se basa en los parámetros de seguridad que se establezcan en el token de seguridad. Aunque, si posteriormente la SAS deja de ser válida, la red CDN de Azure no podrá volver a validar el contenido del servidor de origen.

1. [Cree un token de seguridad de red CDN de Azure](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) y actívelo mediante el motor de reglas para el punto de conexión de red CDN y la ruta de acceso donde los usuarios pueden acceder al archivo.

   Una dirección URL de token de seguridad tiene el formato siguiente:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Por ejemplo:    
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Las opciones de parámetro de una autenticación de token de seguridad son distintas a las opciones de parámetro de un token de SAS. Si al crear un token de seguridad, decide usar una hora de expiración, establézcala en el mismo valor que la hora de expiración del token de SAS. De este modo se garantiza que la hora de expiración sea predecible. 
 
2. Use el [motor de reglas](cdn-rules-engine.md) para crear una regla de reescritura de direcciones URL a fin de habilitar el acceso del token de SAS a todos los blobs del contenedor. Las reglas nuevas tardan unos 90 minutos en propagarse.

   La siguiente regla de reescritura de direcciones URL de ejemplo usa un patrón de expresión regular con un grupo de captura y un punto de conexión de nombre *storagedemo*:
   
   Origen:   
   `(/test/*.)`
   
   Destino:   
   ```
   $1&sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![Regla de reescritura de direcciones URL de CDN](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-3.png)

3. Si renueva la SAS, actualice la regla de reescritura de direcciones URL con el nuevo token de SAS. 

## <a name="sas-parameter-considerations"></a>Consideraciones sobre los parámetros de SAS

Como los parámetros de SAS no son visibles para la red CDN de Azure, esta no puede cambiar su comportamiento de entrega en función de dichos parámetros. Las restricciones de parámetro definidas solo se aplican en las solicitudes que la red CDN de Azure hace en el servidor de origen y no en las solicitudes del cliente a la red CDN de Azure. Resulta importante tener en cuenta esta distinción cuando se establecen parámetros de SAS. Si se requieren estas funcionalidades avanzadas y se usa la [opción 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), establezca las restricciones adecuadas en el token de seguridad de la red CDN de Azure.

| Nombre de parámetro de SAS | DESCRIPCIÓN |
| --- | --- |
| Iniciar | Hora en que la red CDN de Azure puede comenzar a acceder al archivo de blob. Debido al desplazamiento del reloj (es decir, cuando la señal de un reloj llega a distintas horas para componentes diferentes), elija una hora 15 minutos antes si quiere que el recurso esté disponible de inmediato. |
| End | Hora tras la cual la red CDN de Azure ya no tiene acceso al archivo de blob. Los archivos almacenados previamente en caché en la red CDN de Azure siguen siendo accesibles. Para controlar la hora de expiración de los archivos, establezca la hora de expiración correspondiente en el token de seguridad de la red CDN de Azure o purgue el recurso. |
| Direcciones IP permitidas | Opcional. Si usa la **red Azure CDN de Verizon**, puede establecer este parámetro en los intervalos que aparecen definidos en [Azure CDN from Verizon Edge Server IP Ranges](https://msdn.microsoft.com/library/mt757330.aspx) (Azure CDN de intervalos IP del servidor perimetral de Verizon). Si usa la **red Azure CDN de Akamai**, no puede establecer el parámetro de intervalos IP porque las direcciones IP no son estáticas.|
| Protocolos admitidos | Protocolos que se permiten para una solicitud realizada con la SAS de cuenta. Se recomienda la configuración HTTPS.|

## <a name="see-also"></a>Otras referencias
- [Uso de Firmas de acceso compartido (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Firmas de acceso compartido, Parte 2: Creación y uso de una SAS con Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Protección de recursos de Azure Content Delivery Network con la autenticación por tokens](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
