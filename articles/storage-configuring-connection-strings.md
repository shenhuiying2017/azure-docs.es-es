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

# Configurar las cadenas de conexión de Azure

## Información general
Las cadenas de conexión contienen los parámetros necesarios para tener acceso a su cuenta de almacenamiento en Azure. Puede configurar una cadena de conexión de las maneras siguientes:

- Conéctese al emulador de almacenamiento de Azure mientras comprueba localmente el servicio o aplicación.

- Conéctese a una cuenta de almacenamiento en Azure mediante los extremos predeterminados para los servicios de almacenamiento.

- Conéctese a una cuenta de almacenamiento en Azure mediante los extremos explícitos para los servicios de almacenamiento.

Si su aplicación es un servicio en la nube que se ejecuta en Azure, el mejor lugar para almacenar la cadena de conexión es en el [Esquema de configuración del servicio de Azure (archivo .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Si su aplicación se ejecuta en otro entorno (por ejemplo, en el escritorio), probablemente desee almacenar la cadena de conexión en un archivo app.config o en otro archivo de configuración. Puede usar la clase CloudConfigurationManager de Azure para obtener acceso a la cadena de conexión en tiempo de ejecución independientemente de dónde se ejecute.

## Conectarse al emulador de almacenamiento

El emulador de almacenamiento es una cuenta local con un nombre y una clave conocidos. Dado que el nombre y la clave son los mismos para todos los usuarios, puede usar un formato de cadena de método abreviado para hacer referencia al emulador de almacenamiento en una cadena de conexión. Establezca el valor de la cadena de conexión en  `UseDevelopmentStorage=true`.

También puede especificar que se use un proxy HTTP cuando se está probando el servicio con el emulador de almacenamiento. Esto puede ser útil para observar solicitudes y respuestas HTTP mientras está depurando operaciones con los servicios de almacenamiento. Para especificar un proxy, agregue la opción DevelopmentStorageProxyUri a la cadena de conexión y establezca su valor en el URI del proxy. Por ejemplo, esta es una cadena de conexión que apunta al emulador de almacenamiento y configura un proxy HTTP:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Conectarse a una cuenta de almacenamiento de Azure

Puede definir una cadena de conexión para una cuenta de almacenamiento en Azure de una de las siguientes maneras:

- Se utilizan los extremos predeterminados para los servicios de almacenamiento. Se trata de la opción más sencilla para crear una cadena de conexión. Cuando use este formato de cadena de conexión deberá 

- especificar los extremos explícitos para los servicios de almacenamiento. Esta opción permite crear una cadena de conexión más compleja. Cuando se utiliza este formato de cadena, puede especificar los extremos del servicio de almacenamiento que incluyen un nombre de dominio personalizado o minimizar la exposición de información para una cadena de conexión basada en firmas de acceso compartido.


> [AZURE.NOTE] Los servicios de almacenamiento de Azure admiten HTTP y HTTPS; sin embargo, se recomienda encarecidamente utilizar HTTPS.

## Crear una cadena de conexión con los extremos predeterminados

Para crear una cadena de conexión que confíe en los extremos predeterminados para el servicio de almacenamiento, utilice el siguiente formato de cadena de conexión. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS, reemplace myAccountName con el nombre de la cuenta de almacenamiento y reemplace myAccountKey con la clave de acceso a la cuenta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por ejemplo, la cadena de conexión debe tener un aspecto similar a la siguiente cadena de conexión de ejemplo:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==

## Crear una cadena de conexión con los extremos explícitos

Quizás le resulte conveniente especificar de forma explícita los extremos del servicio en la cadena de conexión por las razones siguientes:

- Ha registrado un nombre de dominio personalizado para la cuenta de almacenamiento con el servicio Blob.

- Dispone de una firma de acceso compartida para el acceso a los recursos de almacenamiento.

### Especificar un extremo de blob con un nombre de dominio personalizado 
Si ha registrado un nombre de dominio personalizado para usarlo con el servicio Blob, puede interesarle configurar explícitamente el extremo el blob en la cadena de conexión. El valor final que aparece en la cadena de conexión se utiliza para construir los URI de solicitud para el servicio Blob e indica el formato de los URI que se devuelven para el código. 

Para crear una cadena de conexión que especifique extremos explícitos, indique el extremo completo del servicio para cada servicio, incluida la especificación de protocolo (HTTP o HTTPS) con el formato siguiente:

    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;[credentials]

Cuando se especifican explícitamente extremos de servicios, tiene dos opciones para especificar las credenciales. Puede especificar el nombre y la clave (`AccountName=myAccountName;AccountKey=myAccountKey`), tal y como se muestra en la sección anterior o puede **especificar una firma de acceso compartido**, tal y como se muestra en la sección Especificar extremos con una firma de acceso compartido. Si va a especificar el nombre y la clave de la cuenta, el formato completo de la cadena es:
    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;AccountName=myAccountName;AccountKey=myAccountKey

Puede especificar los extremos de un blob, una tabla y una cola en una cadena de conexión. Es preciso que especifique al menos un extremo, pero no necesita especificar los tres. Por ejemplo, si va a crear una cadena de conexión para su uso con un extremo de blob personalizado, es opcional especificar los extremos de la cola y de la tabla. Tenga en cuenta que si opta por omitir los extremos de la cola y la tabla de la cadena de conexión, no podrá tener acceso a los servicios Cola y Tabla desde el código utilizando dicha cadena de conexión.

### Especificar extremos con una firma de acceso compartido 
Puede crear una cadena de conexión con extremos explícitos para permitir el acceso a recursos de almacenamiento a través de una firma de acceso compartida. En este caso, puede especificar una firma de acceso compartido como parte de la cadena de conexión, en lugar de las credenciales del nombre y clave de la cuenta. El token de firma de acceso compartido encapsula la información sobre el recurso al que se va a tener acceso, el período de tiempo durante el cual estará disponible y los permisos que se van a conceder. Para obtener más información acerca de las firmas de acceso compartido, consulte [Delegar el acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

Para crear una cadena de conexión que incluya una firma de acceso compartido, especifique la cadena con el siguiente formato:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

El extremo puede ser el extremo del servicio predeterminado o un extremo personalizado.  `base64Signature` corresponde a la parte de la firma de una firma de acceso compartido. La firma es un HMAC calculado sobre una cadena para firmar y una clave válidas con el algoritmo SHA256, el resultado es un Base64 codificado.

<!--HONumber=47-->
