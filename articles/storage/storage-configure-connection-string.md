<properties 
	pageTitle="Configurar una cadena de conexión a Almacenamiento de Azure | Microsoft Azure"
	description="Configuración de una cadena de conexión para una cuenta de Almacenamiento de Azure Una cadena de conexión incluye la información necesaria para autenticar el acceso a una cuenta de almacenamiento desde la aplicación en tiempo real."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2016"
	ms.author="tamram"/>

# Configuración de las cadenas de conexión de Almacenamiento de Azure

## Información general

Una cadena de conexión incluye la información de autenticación necesaria para obtener acceso a los datos de una cuenta de almacenamiento de Azure desde la aplicación en tiempo de ejecución. Puede configurar una cadena de conexión de las maneras siguientes:

- Conéctese al emulador de almacenamiento de Azure.
- Acceda a la cuenta de almacenamiento en Azure.
- Obtenga acceso a recursos especificados en Azure a través de una firma de acceso compartido (SAS).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## Almacenamiento de la cadena de conexión

La aplicación necesitaría obtener acceso a la cadena de conexión en tiempo de ejecución para autenticar las solicitudes realizadas en Almacenamiento de Azure. Tiene diversas opciones para almacenar la cadena de conexión:

- Para una aplicación que se ejecuta en el escritorio o en un dispositivo, puede almacenar la cadena de conexión en un archivo `app.config ` o `web.config`. Agregue la cadena de conexión a la sección **AppSettings**.
- Para una aplicación que se ejecute en un servicio en la nube de Azure, puede guardar la cadena de conexión en el [archivo de esquema de configuración de servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Agregue la cadena de conexión a la sección **ConfigurationSettings** del archivo de configuración del servicio.
- También puede utilizar la cadena de conexión directamente en el código. Sin embargo, para la mayoría de los escenarios, recomendamos que almacene su cadena de configuración en un archivo de configuración.

Almacenar la cadena de conexión dentro de un archivo de configuración facilita la actualización de la cadena de conexión para cambiar entre el emulador de almacenamiento y una cuenta de almacenamiento de Azure en la nube. Solo necesitará editar la cadena de conexión para apuntar al entorno de destino.

Puede usar la clase [Administrador de configuración de Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para obtener acceso a la cadena de conexión en tiempo de ejecución independientemente de dónde se ejecute la aplicación.

## Creación de una cadena de conexión para el emulador de almacenamiento

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte [Uso de Emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md) para obtener más información sobre el emulador de almacenamiento.

## Creación de una cadena de conexión para una cuenta de Almacenamiento de Azure

Para crear una cadena de conexión para su cuenta de Almacenamiento de Azure, use el formato de cadena de conexión que aparece a continuación. Indique si desea conectarse a la cuenta de almacenamiento a través de HTTPS (recomendado) o HTTP, reemplace `myAccountName` por el nombre de la cuenta de almacenamiento y reemplace `myAccountKey` por la clave de acceso a la cuenta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por ejemplo, la cadena de conexión tendrá un aspecto similar a la siguiente cadena de conexión de ejemplo:

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Almacenamiento de Azure admite HTTP y HTTPS en una cadena de conexión; sin embargo, se recomienda encarecidamente utilizar HTTPS.

## Creación de una cadena de conexión con una firma de acceso compartido

Si dispone de una dirección URL de firma de acceso compartido (SAS), puede utilizar SAS en la cadena de conexión. Dado que SAS incluye en el identificador URI la información necesaria para autenticar la solicitud, el identificador URI de SAS proporciona el protocolo, el punto de conexión de servicio y las credenciales necesarias para disponer de acceso al recurso.

Para crear una cadena de conexión que incluya una firma de acceso compartido, especifique la cadena con el siguiente formato:

    BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	SharedAccessSignature=sasToken

Cada punto de conexión de servicio es opcional, aunque la cadena de conexión debe contener al menos uno.

Se recomienda usar HTTPS con SAS. Para obtener más información sobre las firmas de acceso compartido, consulte [Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).

>[AZURE.NOTE] Si está especificando SAS en una cadena de conexión en un archivo de configuración, debe codificar caracteres especiales en la dirección URL.

### Ejemplo de SAS de servicio

Este es un ejemplo de una cadena de conexión que incluye un SAS de servicio para el almacenamiento de blobs:

	BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

Aquí se muestra un ejemplo de la misma cadena de conexión con codificación de caracteres especiales:

	BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

### Ejemplo de SAS de cuenta

Este es un ejemplo de una cadena de conexión que incluye un SAS de cuenta para el almacenamiento de blobs y archivos: Tenga en cuenta que se especifican los puntos de conexión para ambos servicios:

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl

Aquí se muestra un ejemplo de la misma cadena de conexión con codificación de dirección URL:

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl

## Creación de una cadena de conexión para un extremo de almacenamiento explícito

Puede especificar de manera explícita los puntos de conexión de servicio en la cadena de conexión en lugar de usar los puntos de conexión predeterminados. Para crear una cadena de conexión que especifique un punto de conexión explícito, especifique el punto de conexión de servicio completo para cada servicio, incluida la especificación de protocolo (HTTPS (recomendado) o HTTP) con el formato siguiente:

	DefaultEndpointsProtocol=[http|https];
	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	AccountName=myAccountName;
	AccountKey=myAccountKey

Un escenario donde puede que desee especificar un punto de conexión explícito es si ha asignado el punto de conexión de almacenamiento de blobs a un dominio personalizado. En ese caso, puede especificar el punto de conexión personalizado para el Almacenamiento de blobs en la cadena de conexión y, opcionalmente, especificar los puntos de conexión predeterminados para el otro servicio si la aplicación los utiliza.

Aquí se muestran ejemplos de cadenas de conexión válidas que especifican un punto de conexión explícito para el servicio BLOB:

	# Blob endpoint only
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=account-key

	# All service endpoints
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	FileEndpoint=myaccount.file.core.windows.net;
	QueueEndpoint=myaccount.queue.core.windows.net;
	TableEndpoint=myaccount;
	AccountName=storagesample;
	AccountKey=account-key

El valor final que aparece en la cadena de conexión se utiliza para construir los URI de solicitud para el servicio Blob e indica el formato de los URI que se devuelven para el código.

Tenga en cuenta que si opta por omitir un punto de conexión de servicio de la cadena de conexión, no podrá usar esa cadena de conexión para obtener acceso a los datos en ese servicio desde el código.

### Creación de una cadena de conexión con un sufijo de extremo

Para crear una cadena de conexión para un servicio de almacenamiento en regiones o instancias con distintos sufijos de extremo, como para Azure China o Azure Governance, utilice el siguiente formato de cadena de conexión. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS, reemplace `myAccountName` por el nombre de la cuenta de almacenamiento, reemplace `myAccountKey` por su clave de acceso a la cuenta y reemplace `mySuffix` por el sufijo de URI:


	DefaultEndpointsProtocol=[http|https];
	AccountName=myAccountName;
	AccountKey=myAccountKey;
	EndpointSuffix=mySuffix;


Por ejemplo, la cadena de conexión debe tener un aspecto similar a la siguiente cadena de conexión:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

## Análisis de una cadena de conexión

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## Pasos siguientes

- [Uso del emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md)
- [Exploradores de almacenamiento de Azure](storage-explorers.md)

<!---HONumber=AcomDC_0615_2016-->