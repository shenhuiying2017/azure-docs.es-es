<properties 
	pageTitle="Configurar las cadenas de conexión de Azure" 
	description="Aprenda a configurar cadenas de conexión en una cuenta de almacenamiento en Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Configuración de las cadenas de conexión de Almacenamiento de Azure

## Información general

Una cadena de conexión incluye la información necesaria para tener acceso a su cuenta de almacenamiento de Azure de manera programática. Puede configurar una cadena de conexión para conectarse a Almacenamiento de Azure de las maneras siguientes:

- Conéctese al emulador de almacenamiento de Azure mientras comprueba localmente el servicio o aplicación.

- Conéctese a una cuenta de almacenamiento en Azure mediante los extremos predeterminados para los servicios de almacenamiento.

- Conéctese a una cuenta de almacenamiento en Azure mediante los extremos explícitos para los servicios de almacenamiento.

Si su aplicación es un servicio en la nube que se ejecuta en Azure, normalmente guardará la cadena de conexión en el [archivo de esquema de configuración de servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Si su aplicación se ejecuta en otro entorno (por ejemplo, en el escritorio), normalmente guardará la cadena de conexión en un archivo app.config o en otro archivo de configuración. Puede usar la clase `CloudConfigurationManager` de Azure para tener acceso a la cadena de conexión en tiempo de ejecución independientemente de dónde se ejecuta.

## Creación de una cadena de conexión para el emulador de almacenamiento

El emulador de almacenamiento es una cuenta local con un nombre y una clave conocidos. Dado que el nombre y la clave son los mismos para todos los usuarios, puede usar un formato de cadena de método abreviado para hacer referencia al emulador de almacenamiento en una cadena de conexión. Establezca el valor de la cadena de conexión en `UseDevelopmentStorage=true`.

También puede especificar que se use un proxy HTTP cuando se está probando el servicio con el emulador de almacenamiento. Esto puede ser útil para observar solicitudes y respuestas HTTP mientras está depurando operaciones con los servicios de almacenamiento. Para especificar un proxy, agregue la opción `DevelopmentStorageProxyUri` a la cadena de conexión y establezca su valor en el URI del proxy. Por ejemplo, esta es una cadena de conexión que apunta al emulador de almacenamiento y configura un proxy HTTP:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Creación de una cadena de conexión para una cuenta de Almacenamiento de Azure

Para crear una cadena de conexión para su cuenta de Almacenamiento de Azure, use el formato de cadena de conexión que aparece a continuación. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS (recomendado), reemplace `myAccountName` por el nombre de la cuenta de almacenamiento y reemplace `myAccountKey` por la clave de acceso a la cuenta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por ejemplo, la cadena de conexión tendrá un aspecto similar a la siguiente cadena de conexión de ejemplo:

```        DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg==
```

> [AZURE.NOTE] Almacenamiento de Azure admite HTTP y HTTPS en una cadena de conexión; sin embargo, se recomienda encarecidamente utilizar HTTPS.
    
## Creación de una cadena de conexión para un extremo de Almacenamiento explícito

Puede especificar de manera explícita los extremos del servicio en la cadena de conexión si:

- Ha asignado un nombre de dominio personalizado para la cuenta de almacenamiento con el servicio Blob.
- Dispone de una firma de acceso compartido para el acceso a los recursos de almacenamiento en una cuenta de almacenamiento.

Para crear una cadena de conexión que especifique un extremo de Blob explícito, especifique el extremo de servicio completo para cada servicio, incluida la especificación de protocolo (HTTP o HTTPS) con el formato siguiente:

``` 
BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;FileEndpoint=myFileEndpoint;[credentials]
```

Debe especificar al menos un extremo de servicio, pero no es necesario que los especifique todos. Por ejemplo, si va a crear una cadena de conexión para su uso con un extremo de blob personalizado, es opcional especificar los extremos de la cola y de la tabla. Tenga en cuenta que si opta por omitir los extremos de la cola y la tabla de la cadena de conexión, no podrá tener acceso a los servicios Cola y Tabla desde el código utilizando dicha cadena de conexión.

Cuando especifica de manera explícita los extremos de servicio de la cadena de conexión, tiene dos opciones para especificar `credentials` en la cadena anterior:

- Puede especificar el nombre y la clave de cuenta: `AccountName=myAccountName;AccountKey=myAccountKey` 
- Puede especificar una firma de acceso compartido: `SharedAccessSignature=base64Signature`

### Especificación de un extremo de blob con un nombre de dominio personalizado 

Si ha registrado un nombre de dominio personalizado para usarlo con el servicio Blob, puede interesarle configurar explícitamente el extremo el blob en la cadena de conexión. El valor final que aparece en la cadena de conexión se utiliza para construir los URI de solicitud para el servicio Blob e indica el formato de los URI que se devuelven para el código. 

Por ejemplo, una cadena de conexión para un extremo de blob en un dominio personalizado podría ser similar a:

```
DefaultEndpointsProtocol=https;BlobEndpoint=www.mydomain.com;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg== 
```

### Especificación de un extremo de blob con una firma de acceso compartido 

Puede crear una cadena de conexión con extremos explícitos para permitir el acceso a recursos de almacenamiento a través de una firma de acceso compartida. En este caso, puede especificar una firma de acceso compartido como parte de la cadena de conexión, en lugar de las credenciales del nombre y clave de la cuenta. El token de firma de acceso compartido encapsula la información sobre el recurso al que se va a tener acceso, el período de tiempo durante el cual estará disponible y los permisos que se van a conceder. Para obtener más información acerca de las firmas de acceso compartido, consulte [Delegación del acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

Para crear una cadena de conexión que incluya una firma de acceso compartido, especifique la cadena con el siguiente formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

El extremo puede ser el extremo del servicio predeterminado o un extremo personalizado.  `base64Signature` corresponde a la parte de la firma de una firma de acceso compartido. La firma es un HMAC calculado sobre una cadena para firmar y una clave válidas con el algoritmo SHA256, el resultado es un Base64 codificado.

### Creación de una cadena de conexión con un sufijo de extremo

Para crear una cadena de conexión para un servicio de almacenamiento en regiones o instancias con distintos sufijos de extremo, como para Azure China o Azure Governance, utilice el siguiente formato de cadena de conexión. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS, reemplace `myAccountName` por el nombre de la cuenta de almacenamiento, reemplace `myAccountKey` por su clave de acceso a la cuenta y reemplace `mySuffix` por el sufijo de URI:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Por ejemplo, la cadena de conexión debe tener un aspecto similar a la siguiente cadena de conexión de ejemplo:

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==;EndpointSuffix=core.chinacloudapi.cn;



<!--HONumber=52--> 