---
title: 'Introducción a Azure Storage: almacenamiento en la nube en Azure | Microsoft Docs'
description: Azure Storage es una solución de almacenamiento en la nube de Microsoft. Azure Storage proporciona almacenamiento para objetos de datos, que presenta una alta disponibilidad, es seguro, durable y redundante y se puede escalar de forma masiva.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: tamram
ms.openlocfilehash: 071b209ffa8ffeb8ef6d998f08bcd68868e29911
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-azure-storage"></a>Introducción a Azure Storage

Azure Storage es la solución de almacenamiento de Microsoft para los escenarios modernos de almacenamiento de datos. Azure Storage ofrece un almacén de objetos que se puede escalar de forma masiva destinado a objetos de datos, un servicio de sistema de archivos para la nube, un almacén de mensajería para mensajería confiable y un almacén NoSQL. Azure Storage se caracteriza por:

- **Durable y con una elevada disponibilidad.** La redundancia garantiza que los datos están seguros en caso de errores de hardware transitorios. También puede optar por replicar datos entre centros de datos o regiones geográficas para obtener protección adicional frente a catástrofes locales o desastres naturales. Los datos replicados de esta manera permanecen con una alta disponibilidad en caso de una interrupción inesperada. 
- **Seguro.** Todos los datos escritos en Azure Storage se cifran mediante el servicio. Azure Storage proporciona un control pormenorizado sobre quién tiene acceso a los datos.
- **Escalable.** Azure Storage está diseñado para poderse escalar de forma masiva para satisfacer las necesidades de rendimiento y almacenamiento de datos de las aplicaciones de hoy en día. 
- **Administrado.** Microsoft Azure se encarga del mantenimiento y de cualquier problema crítico que pueda surgir.
- **Accesible.** Es posible acceder a los datos de Azure Storage desde cualquier parte del mundo a través de HTTP o HTTPS. Microsoft proporciona SDK para Azure Storage en diversos lenguajes: .NET, Java, Node.js, Python, PHP, Ruby, Go y otros, así como una API REST consolidada. Azure Storage admite la escritura en Azure PowerShell o la CLI de Azure. Y Azure Portal y el Explorador de Azure Storage ofrecen soluciones visuales sencillas para trabajar con los datos.  

## <a name="azure-storage-services"></a>Servicios de Azure Storage

Azure Storage incluye estos servicios de datos: 

- [Blobs de Azure](../blobs/storage-blobs-introduction.md): un almacén de objetos que se pueden escalar de forma masiva pata texto y datos binarios.
- [Azure Files](../files/storage-files-introduction.md): recursos compartidos de archivos administrados para implementaciones locales y en la nube.
- [Colas de Azure](../queues/storage-queues-introduction.md): un almacén de mensajería para mensajería confiable entre componentes de aplicación. 
- [Tablas de Azure](../../cosmos-db/table-storage-overview.md): un almacén NoSQL para el almacenamiento sin esquema de datos estructurados.

Para acceder a cada servicio se usa una cuenta de almacenamiento. Para comenzar, consulte [Crear una cuenta de almacenamiento](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Almacenamiento de blobs

Azure Blob Storage es la solución de almacenamiento de objetos de Microsoft para la nube. Blob Storage está optimizado para el almacenamiento de cantidades masivas de datos no estructurados, como texto o datos binarios. 

Blob Storage resulta muy conveniente para:

* Servicio de imágenes o documentos directamente a un explorador.
* Almacenamiento de archivos para acceso distribuido.
* Streaming de audio y vídeo.
* Almacenamiento de datos para copia de seguridad y restauración, recuperación ante desastres y archivado.
* Almacenamiento de datos para el análisis en local o en un servicio hospedado de Azure.

Se puede acceder a los objetos de Blob Storage desde cualquier lugar del mundo a través de HTTP o HTTPS. Los usuarios o las aplicaciones cliente pueden acceder a los blobs mediante direcciones URL, la [API REST de Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), la [CLI de Azure](https://docs.microsoft.com/cli/azure/storage) o una biblioteca de cliente de Azure Storage. Las bibliotecas de cliente de almacenamiento están disponibles para varios lenguajes, como [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/) y [Ruby](http://azure.github.io/azure-storage-ruby).

Para más información sobre Blob Storage, consulte [Introducción al almacenamiento de objetos en Azure](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Archivos de Azure
[Azure Files](../files/storage-files-introduction.md) permite configurar recursos compartidos de archivos de red de alta disponibilidad a los que se puede acceder mediante el protocolo Bloque de mensajes del servidor (SMB) estándar. Esto significa que varias máquinas virtuales pueden compartir los mismos archivos con acceso de lectura y escritura. También puede leer los archivos mediante la interfaz REST o las bibliotecas de cliente de Storage.

Lo que distingue Azure Files de los archivos en un recurso compartido de archivos corporativo es que puede acceder a ellos desde cualquier lugar del mundo mediante una dirección URL que apunte al archivo e incluya un token de firma de acceso compartido (SAS). Puede generar tokens SAS; permiten el acceso específico a un recurso privado durante un período de tiempo determinado.

Los recursos compartidos de archivos se pueden utilizar para muchos escenarios comunes:

* Muchas aplicaciones locales usan recursos compartidos de archivos. Esta característica facilita la migración de las aplicaciones que comparten datos en Azure. Si monta el recurso compartido de archivos en la misma letra de unidad que usa la aplicación local, la parte de la aplicación que tiene acceso al recurso compartido de archivos debería funcionar con cambios mínimos, si es que hay alguno.

* Los archivos de configuración se pueden almacenar en un recurso compartido de archivos y se puede acceder a ellos desde varias máquinas virtuales. Las herramientas y utilidades que usen varios desarrolladores de un grupo pueden almacenarse en un recurso compartido de archivos, lo que garantiza que todos los usuarios puedan encontrarlas y que utilizan la misma versión.

* Los registros de diagnóstico, las métricas y los volcados de memoria son solo tres ejemplos de datos que se pueden escribir en un recurso compartido de archivos y procesarse o analizarse posteriormente.

En este momento, las listas de control de acceso (ACL) y la autenticación basada en Active Directory no se admiten, pero se admitirán en algún momento. Las credenciales de las cuentas de almacenamiento se usan para permitir la autenticación al recurso compartido de archivos. Esto significa que cualquier usuario con el recurso compartido montado tendrá acceso completo de lectura/escritura al recurso compartido.

Para más información sobre Azure Files, consulte [Introducción a Azure Files](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

El servicio Azure Queue se utiliza para almacenar y recuperar mensajes. La cola de mensajes puede ser de hasta 64 KB de tamaño y contener millones de mensajes. Las colas se utilizan generalmente para almacenar listas de mensajes y procesarlas de forma asincrónica.

Por ejemplo, supongamos que quiere que los clientes puedan cargar imágenes y desea crear vistas en miniatura de cada una. El cliente puede esperar a que cree las vistas en miniatura mientras se cargan las imágenes. Una alternativa sería utilizar una cola. Cuando el cliente finalice la carga, escribe un mensaje en la cola. A continuación, una función de Azure recupera el mensaje de la cola y crea las vistas en miniatura. Las partes de este procesamiento se escalan por separado, por lo que controlará mejor el ajuste para usarlas.

Para más información sobre las colas de Azure, consulte [Introducción a las colas](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Almacenamiento de tablas

Azure Table Storage ahora forma parte de Azure Cosmos DB. Para ver la documentación de Azure Table Storage, consulte [Introducción a Azure Table Storage](../../cosmos-db/table-storage-overview.md). Además del servicio Azure Table Storage existente, hay una nueva Table API de Azure Cosmos DB que ofrece tablas con rendimiento optimizado, distribución global e índices secundarios automáticos. Para más información sobre la nueva experiencia premium y poder probarla, consulte [Introducción a Table API de Azure Cosmos DB](https://aka.ms/premiumtables).

Para más información sobre Table Storage, consulte [Introducción a Azure Table Storage](../../cosmos-db/table-storage-overview.md).

## <a name="disk-storage"></a>Almacenamiento en disco

Azure Storage también incluye todas las funcionalidades de disco administrado y no administrado que utilizan las máquinas virtuales. Para más información acerca de estas características, consulte la [documentación del servicio Compute](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>Tipos de cuentas de almacenamiento

En esta tabla se muestran los distintos tipos de cuentas de almacenamiento y los objetos que pueden utilizarse con cada uno de ellos.

|**Tipo de cuenta de almacenamiento**|**Uso general estándar**|**Uso general premium**|**Niveles de acceso frecuente y esporádico de Blob Storage**|
|-----|-----|-----|-----|
|**Servicios admitidos**| Servicios de blobs, archivos y colas | Blob Service | Blob Service|
|**Tipos de blobs compatibles**|Blobs en bloques, blobs en páginas y blobs en anexos | Blobs en páginas | Blobs en bloques y blobs en anexos|

### <a name="general-purpose-storage-accounts"></a>Cuentas de almacenamiento de uso general

Hay dos tipos de cuentas de almacenamiento de uso general.

#### <a name="standard-storage"></a>Standard Storage

Las cuentas de almacenamiento más usadas son las estándar, que se pueden usar para cualquier tipo de datos. Las cuentas de almacenamiento estándar usan medios magnéticos para almacenar los datos.

#### <a name="premium-storage"></a>Premium Storage

Premium Storage proporciona almacenamiento de alto rendimiento para blobs en páginas, que se utiliza principalmente para los archivos de disco duro virtual. Las cuentas de almacenamiento premium usan SSD para almacenar los datos. Microsoft recomienda usar Premium Storage para todas las máquinas virtuales.

### <a name="blob-storage-accounts"></a>Cuentas de Blob Storage

La cuenta de Blob Storage es una cuenta de almacenamiento especializada en blobs en bloques y en anexos. En estas cuentas no se pueden almacenar blobs en páginas, como los de archivos de disco duro virtual. Estas cuentas permiten establecer un nivel de acceso frecuente o esporádico, que se puede cambiar en cualquier momento.

El nivel de acceso frecuente se usa para los archivos a los que se accede con frecuencia; se paga más por el almacenamiento, pero el costo de acceso a los blobs es mucho menor. Para los blobs almacenados en el nivel de acceso esporádico, se paga más por acceder a los blobs, pero mucho menos por el almacenamiento.

## <a name="accessing-your-blobs-files-and-queues"></a>Acceso a los blobs, las colas y los archivos

Cada cuenta de almacenamiento tiene dos claves de autenticación y ambas sirven para cualquier operación. Existen dos claves para recuperar las claves cuando proceda y así mejorar la seguridad. Es fundamental mantener las claves seguras, ya que su posesión, junto con el nombre de cuenta, permite acceso ilimitado a todos los datos de la cuenta de almacenamiento.

En esta sección se examinan dos formas de proteger la cuenta de almacenamiento y los datos. Para información detallada acerca de cómo proteger la cuenta de almacenamiento y los datos, consulte [Azure Storage security guide](storage-security-guide.md) (Guía de seguridad de Azure Storage).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Protección del acceso a las cuentas de almacenamiento con Azure AD

Una manera de proteger el acceso a los datos de almacenamiento es controlar el acceso a las claves de la cuenta de almacenamiento. Con el control de acceso basado en rol (RBAC) de Resource Manager, puede asignar roles a usuarios, grupos o aplicaciones. Estos roles están unidos a un conjunto específico de acciones permitidas o denegadas. El uso de RBAC para conceder acceso a una cuenta de almacenamiento solo sirve para controlar la administración de esa cuenta, por ejemplo, cambiar el nivel de acceso. No se puede utilizar RBAC para conceder acceso a objetos de datos, como un recurso compartido de archivos o un contenedor específico. Sin embargo, puede utilizar RBAC para conceder acceso a las claves de cuenta de almacenamiento, que se pueden usar para leer los objetos de datos.

### <a name="securing-access-using-shared-access-signatures"></a>Protección del acceso con firmas de acceso compartido

Puede utilizar firmas de acceso compartido y directivas de acceso almacenadas para proteger los objetos de datos. Una firma de acceso compartido (SAS) es una cadena con un token de seguridad que puede asociarse al URI de un recurso para delegar el acceso a objetos de almacenamiento concretos y especificar restricciones, como permisos o un intervalo de fecha y hora para el acceso. Esta característica tiene amplias funcionalidades. Para más información, consulte [Using Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Uso de firmas de acceso compartido [SAS]).

### <a name="public-access-to-blobs"></a>Acceso público a blobs

Blob Service permite proporcionar acceso público a un contenedor y sus blobs, o a un blob en particular. Cuando se indica que un contenedor o blob es público, todos los usuarios pueden leerlo de forma anónima: no se requiere autenticación. Un ejemplo de ello es un sitio web que use imágenes, vídeos o documentos de Blob Storage. Para más información, consulte [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Administración del acceso de lectura anónimo a contenedores y blobs).

## <a name="encryption"></a>Cifrado

Hay dos tipos básicos de cifrado disponibles para los servicios de almacenamiento. Para más información sobre la seguridad y el cifrado, consulte [Guía de seguridad de Azure Storage](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Cifrado en reposo

Cifrado del servicio Azure Storage (SSE) en reposo le ayuda a asegurar y proteger sus datos con el fin de cumplir con los compromisos de cumplimiento y seguridad de su organización. Con esta característica, Azure Storage cifra automáticamente sus datos antes de continuar al almacenamiento y los descifra después de la recuperación. La administración de claves, el cifrado y el descifrado son completamente transparentes para los usuarios.

SSE cifra automáticamente los datos de todos los niveles de rendimiento (Estándar y Premium), todos los modelos de implementación (Azure Resource Manager y clásico) y todos los servicios de Azure Storage (Blob, Queue, Table y File). SSE no afecta al rendimiento de Azure Storage.

Para más información sobre el cifrado de SSE en reposo, consulte [Cifrado del servicio Azure Storage para datos en reposo](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Cifrado de cliente

Las bibliotecas de cliente de almacenamiento tienen métodos que puede llamar para cifrar los datos mediante programación antes de enviarlos por cable del cliente a Azure. Se almacenan cifrados, lo que significa que también se cifran en reposo. Cuando se leen los datos de nuevo, se descifra la información después de recibirla.

Para más información sobre el cifrado en el lado cliente, consulte [Cifrado del lado cliente con .NET para Microsoft Azure Storage](storage-client-side-encryption.md).

## <a name="replication"></a>Replicación

Para asegurarse de que los datos sean duraderos, Azure Storage replica varias copias de los mismos. Al configurar la cuenta de almacenamiento, seleccione un tipo de replicación. En la mayoría de los casos, puede modificar esta configuración después de haber creado la cuenta de almacenamiento. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Para más información sobre la recuperación ante desastres, consulte [What to do if an Azure Storage outage occurs](storage-disaster-recovery-guidance.md) (Qué hacer si se produce una interrupción del servicio Azure Storage).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferencia de datos hacia y desde Azure Storage

Puede utilizar la utilidad de la línea de comandos de AzCopy para copiar datos de blobs y archivos en la cuenta de almacenamiento o entre cuentas de almacenamiento. Consulte de los artículos siguientes para ayuda:

* [Transfer data with AzCopy for Windows](storage-use-azcopy.md) (Transferencia de datos con AzCopy en Windows)
* [Transfer data with AzCopy for Linux](storage-use-azcopy-linux.md) (Transferencia de datos con AzCopy en Linux)

AzCopy se instala sobre la [biblioteca de movimiento de datos de Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que está disponible actualmente en versión preliminar.

El servicio Azure Import/Export puede utilizarse para importar grandes cantidades de datos de blobs hacia o desde la cuenta de almacenamiento. Debe preparar y enviar por correo varias unidades de disco duro a un centro de datos de Azure, donde se transfieren los datos desde y hacia los discos duros y, después, se le devuelven las unidades de disco duro. Para más información sobre el servicio Import/Export, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](../storage-import-export-service.md).

## <a name="pricing"></a>Precios

Para información detallada sobre los precios de Azure Storage, consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>API, bibliotecas y herramientas de Storage
Es posible acceder a los recursos de Azure Storage por medio de cualquier lenguaje que pueda realizar solicitudes HTTP/HTTPS. Además, ofrece bibliotecas de programación para varios lenguajes de amplio uso. Estas bibliotecas simplifican muchos aspectos relacionados con el uso de Azure Storage, ya que abordan detalles como la invocación sincrónica y asincrónica, el procesamiento por lotes de las operaciones, la administración de excepciones, los reintentos automáticos y el comportamiento operativo, entre otros. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas, además de otros previstos:

### <a name="azure-storage-data-api-and-library-references"></a>Referencias de biblioteca y de API de datos de Azure Storage
* [API de REST de servicios de almacenamiento](https://docs.microsoft.com/rest/api/storageservices/)
* [Biblioteca de cliente de Storage para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Biblioteca de cliente de Storage para Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Biblioteca de cliente de Storage para Node.js](https://docs.microsoft.com/en-us/javascript/api/azure-storage)
* [Biblioteca de cliente de Storage para Python](https://github.com/Azure/azure-storage-python)
* [Biblioteca de cliente de Storage para PHP](https://github.com/Azure/azure-storage-php)
* [Biblioteca de cliente de Storage para Ruby](https://github.com/Azure/azure-storage-ruby)
* [Biblioteca de cliente de almacenamiento para C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Referencias de biblioteca y de API de administración de Azure Storage
* [API REST del proveedor de recursos de almacenamiento](https://docs.microsoft.com/rest/api/storagerp/)
* [Biblioteca de cliente del proveedor de recursos de Storage para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [API de REST de administración de servicios de almacenamiento (clásico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Referencias de biblioteca y de API de movimiento de datos de Azure Storage
* [API de REST del servicio Import/Export de Storage](https://docs.microsoft.com/rest/api/storageimportexport/)
* [Biblioteca de cliente de movimiento de datos de Storage para .NET](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>Herramientas y utilidades
* [Cmdlets de Azure PowerShell para Storage](https://docs.microsoft.com/powershell/module/azure.storage)
* [Cmdlets de la CLI de Azure para Storage](https://docs.microsoft.com/cli/azure/storage)
* [Utilidad de línea de comandos AzCopy](http://aka.ms/downloadazcopy)
* El [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
* [Herramientas de cliente de Azure Storage](../storage-explorers.md)
* [Herramientas de desarrollo de Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Pasos siguientes

Para iniciar y ejecutar con Azure Storage, consulte [Crear una cuenta de almacenamiento](storage-quickstart-create-account.md).
