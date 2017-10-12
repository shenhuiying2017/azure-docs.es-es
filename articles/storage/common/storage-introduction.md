---
title: "Introducción a Azure Storage | Microsoft Docs"
description: "Introducción a Azure Storage, el servicio de almacenamiento de datos de Microsoft en la nube."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: tamram
ms.openlocfilehash: e7b32aa2de5d6501e8d7894a936e9ab8b2f4f42f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-storage"></a>Introducción a Microsoft Azure Storage

Microsoft Azure Storage es un servicio en la nube administrado por Microsoft que proporciona almacenamiento altamente disponible, seguro, duradero, escalable y redundante. Microsoft se encarga del mantenimiento y soluciona automáticamente los problemas críticos. 

Azure Storage consta de tres servicios de datos: Blob Storage, File Storage y Queue Storage. Blob Storage admite almacenamiento estándar y premium; el segundo, solo con SSD para el rendimiento más rápido. Otra característica es el almacenamiento de acceso esporádico, que permite almacenar grandes cantidades de datos a los que se accede con muy poca frecuencia a costo reducido.

En este artículo, aprenderá acerca de lo siguiente:
* Los servicios Azure Storage
* Los tipos de cuentas de almacenamiento
* El acceso a los blobs, las colas y los archivos
* El cifrado
* La replicación 
* La migración de datos de almacenamiento
* Las distintas bibliotecas de cliente de Storage disponibles 

Para empezar a trabajar con Azure Storage rápidamente, consulte alguno de las siguientes guías de inicio rápido:
* [Creación de una cuenta de almacenamiento mediante PowerShell](storage-quickstart-create-storage-account-powershell.md)
* [Creación de una cuenta de almacenamiento mediante la CLI](storage-quickstart-create-storage-account-cli.md)

## <a name="introducing-the-azure-storage-services"></a>Introducción de los servicios Azure Storage

Para utilizar cualquiera de los servicios que proporciona Azure Storage (Blob Storage, File Storage y Queue Storage), cree primero una cuenta de almacenamiento para migrar datos desde o hacia uno de sus servicios específicos. 

## <a name="blob-storage"></a>Almacenamiento de blobs

Básicamente, los blobs son archivos como los que se almacenan en el equipo (o tableta, dispositivo móvil, etc.). Pueden ser imágenes, archivos de Microsoft Excel, archivos HTML, discos duros virtuales (VHD), macrodatos, como registros, copias de seguridad de bases de datos..., cualquier cosa. Los blobs se almacenan en contenedores, que son similares a las carpetas. 

Al almacenar archivos en Blob Storage, se puede acceder a ellos desde cualquier lugar del mundo mediante direcciones URL, la interfaz de REST o una de las bibliotecas de cliente de almacenamiento de SDK de Azure. Las bibliotecas de cliente de almacenamiento están disponibles en varios lenguajes, como Node.js, Java, PHP, Ruby, Python y .NET. 

Hay tres tipos de blobs: blobs en bloques, blobs en anexos y blobs en páginas (para los archivos de disco duro virtual).

* Los blobs en bloques se utilizan para hospedar archivos normales de hasta 4,7 TB. 
* Los blobs en páginas se utilizan para almacenar archivos de acceso aleatorio de hasta 8 TB de tamaño. Se usan para los archivos de disco duro virtual de copia de seguridad de máquinas virtuales.
* Los blobs en anexos consisten en bloques, como los blobs en bloques, pero están optimizados para operaciones de anexión. Se utilizan, por ejemplo, para registrar información en el mismo blob desde varias máquinas virtuales.

Para conjuntos de datos de gran tamaño donde las restricciones de red impiden la carga o descarga de datos en Blob Storage a través de una conexión, puede enviar un conjunto de discos duros a Microsoft para importar o exportar datos directamente desde el centro de datos. Consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](../storage-import-export-service.md).

## <a name="azure-files"></a>Archivos de Azure
[Azure Files](../files/storage-files-introduction.md) permite configurar recursos compartidos de archivos de red de alta disponibilidad a los que se puede acceder mediante el protocolo Bloque de mensajes del servidor (SMB) estándar. Esto significa que varias máquinas virtuales pueden compartir los mismos archivos con acceso de lectura y escritura. También puede leer los archivos mediante la interfaz REST o las bibliotecas de cliente de Storage. 

Lo que distingue Azure Files de los archivos en un recurso compartido de archivos corporativo es que puede acceder a ellos desde cualquier lugar del mundo mediante una dirección URL que apunte al archivo e incluya un token de firma de acceso compartido (SAS). Puede generar tokens SAS; permiten el acceso específico a un recurso privado durante un período de tiempo determinado. 

Los recursos compartidos de archivos se pueden utilizar para muchos escenarios comunes: 

* Muchas aplicaciones locales usan recursos compartidos de archivos. Esta característica facilita la migración de las aplicaciones que comparten datos en Azure. Si monta el recurso compartido de archivos en la misma letra de unidad que usa la aplicación local, la parte de la aplicación que tiene acceso al recurso compartido de archivos debería funcionar con cambios mínimos, si es que hay alguno.

* Los archivos de configuración se pueden almacenar en un recurso compartido de archivos y se puede acceder a ellos desde varias máquinas virtuales. Las herramientas y utilidades que usen varios desarrolladores de un grupo pueden almacenarse en un recurso compartido de archivos, lo que garantiza que todos los usuarios puedan encontrarlas y que utilizan la misma versión.

* Los registros de diagnóstico, las métricas y los volcados de memoria son solo tres ejemplos de datos que se pueden escribir en un recurso compartido de archivos y procesarse o analizarse posteriormente.

En este momento, las listas de control de acceso (ACL) y la autenticación basada en Active Directory no se admiten, pero se admitirán en algún momento. Las credenciales de las cuentas de almacenamiento se usan para permitir la autenticación al recurso compartido de archivos. Esto significa que cualquier usuario con el recurso compartido montado tendrá acceso completo de lectura/escritura al recurso compartido.

## <a name="queue-storage"></a>Queue Storage

El servicio Azure Queue se utiliza para almacenar y recuperar mensajes. La cola de mensajes puede ser de hasta 64 KB de tamaño y contener millones de mensajes. Las colas se utilizan generalmente para almacenar listas de mensajes y procesarlas de forma asincrónica. 

Por ejemplo, supongamos que quiere que los clientes puedan cargar imágenes y desea crear vistas en miniatura de cada una. El cliente puede esperar a que cree las vistas en miniatura mientras se cargan las imágenes. Una alternativa sería utilizar una cola. Cuando el cliente finalice la carga, escribe un mensaje en la cola. A continuación, una función de Azure recupera el mensaje de la cola y crea las vistas en miniatura. Las partes de este procesamiento se escalan por separado, por lo que controlará mejor el ajuste para usarlas.

## <a name="table-storage"></a>Almacenamiento de tablas

Azure Table Storage estándar ahora forma parte de Cosmos DB. Para ver esa documentación, consulte [Introducción a Azure Table Storage](../../cosmos-db/table-storage-overview.md). Para Azure Table Storage también hay tablas premium disponibles, que ofrecen tablas con rendimiento optimizado, distribución global e índices secundarios automáticos. Para aprender más sobre la nueva experiencia premium y probarla, consulte [Introducción a la API Table de Azure Cosmos DB](https://aka.ms/premiumtables).

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

#### <a name="standard-storage"></a>Standard storage 

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

Hay un par de tipos básicos de cifrado disponibles para los servicios de almacenamiento. 

### <a name="encryption-at-rest"></a>Cifrado en reposo 

Puede habilitar el cifrado de servicio de almacenamiento (SSE) en File Service (versión preliminar) o en Blob Service para una cuenta de Azure Storage. Si está habilitado, todos los datos escritos en el servicio específico se cifran antes de la escritura. Cuando se leen los datos, se descifran antes de devolverlos. 

### <a name="client-side-encryption"></a>cifrado de cliente

Las bibliotecas de cliente de almacenamiento tienen métodos que puede llamar para cifrar los datos mediante programación antes de enviarlos por cable del cliente a Azure. Se almacenan cifrados, lo que significa que también se cifran en reposo. Cuando se leen los datos de nuevo, se descifra la información después de recibirla. 

### <a name="encryption-in-transit-with-azure-file-shares"></a>Cifrado durante el tránsito con recursos compartidos de Azure Files

Para más información sobre firmas de acceso compartido, consulte [Uso de firmas de acceso compartido (SAS)](../storage-dotnet-shared-access-signature-part-1.md) . Consulte [Administración del acceso de lectura anónimo a contenedores y blobs](../blobs/storage-manage-access-to-resources.md) y [Authentication for the Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179428.aspx) (Autenticación para los servicios de Azure Storage) para más información sobre el acceso seguro a su cuenta de almacenamiento.

Para más información acerca de cómo proteger la cuenta de almacenamiento y el cifrado, consulte [Azure Storage security guide](storage-security-guide.md) (Guía de seguridad de Azure Storage).

## <a name="replication"></a>Replicación

Para asegurarse de que los datos sean duraderos, Azure Storage puede mantener (y administrar) varias copias de los datos. Esto se denomina replicación o, a veces, redundancia. Al configurar la cuenta de almacenamiento, seleccione un tipo de replicación. En la mayoría de los casos, puede modificar esta configuración después de configura la cuenta de almacenamiento. 

Todas las cuentas de almacenamiento tienen **almacenamiento con redundancia local (LRS)**. Esto se traduce en tres copias de los datos administrados por Azure Storage en el centro de datos especificado cuando se configuró la cuenta de almacenamiento. Cuando se realizan cambios en una copia, se actualizan las otras dos antes de indicar que se ha realizado correctamente. Esto significa que las tres réplicas siempre están sincronizadas. Además, las tres copias residen en dominios de error y de actualización independientes, lo que significa que los datos están disponibles aunque se produzca un error en un nodo de almacenamiento que contenga los datos o se quede sin conexión para la actualización. 

**Almacenamiento con redundancia local (LRS)**

Como se explicó anteriormente, con el almacenamiento con redundancia local tiene tres copias de los datos en un único centro de datos. Esto resuelve el problema de que los datos dejen de estar disponibles si se produce un error en un nodo de almacenamiento o se queda sin conexión para la actualización, pero no es el caso si un centro de datos entero deja de estar disponible.

**Almacenamiento con redundancia de zona (ZRS)**

El almacenamiento con redundancia de zona (ZRS) mantiene las tres copias locales de los datos, así como otro conjunto de tres copias de los datos. El segundo conjunto de tres copias se replica de forma asincrónica en centros de datos de una o dos regiones. Tenga en cuenta que ZRS solo está disponible para los blobs en bloques de cuentas de almacenamiento de uso general. Además, una vez que haya creado la cuenta de almacenamiento y seleccionado ZRS, no puede utilizar otro tipo de replicación ni viceversa.

Las cuentas de ZRS proporcionan mayor durabilidad que LRS, pero las cuentas con ZRS no tienen métricas ni la funcionalidad de registro. 

**Almacenamiento con redundancia geográfica (GRS)**

El almacenamiento con redundancia geográfica (GRS) mantiene las tres copias locales de los datos en una región principal y otro conjunto de tres copias de los datos en una región secundaria a miles de kilómetros de la región principal. En caso de que se produzca un error en la región principal, Azure Storage conmutará por error a la región secundaria. 

**Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).** 

El almacenamiento con redundancia geográfica con acceso de lectura es exactamente igual que GRS, salvo que el acceso a los datos en la ubicación secundaria es de lectura. Si el centro de datos principal deja de estar disponible temporalmente, puede continuar leyendo los datos desde la ubicación secundaria. Esto puede resultar muy útil. Por ejemplo, podría tener una aplicación web que cambia a modo de solo lectura y apunta a la copia secundaria, lo cual supone cierto acceso, aunque no estén disponibles las actualizaciones. 

> [!IMPORTANT]
> Si no especificó ZRS cuando creó la cuenta, puede cambiar cómo se replican los datos después de haber creado su cuenta. Sin embargo, tenga en cuenta que cambiar de LRS a GRS o RA-GRS puede suponer un coste adicional único de transferencia de datos.
>

Para más información sobre la replicación, consulte [Azure Storage replication](storage-redundancy.md) (Replicación de Azure Storage).

Para más información sobre la recuperación ante desastres, consulte [What to do if an Azure Storage outage occurs](storage-disaster-recovery-guidance.md) (Qué hacer si se produce una interrupción del servicio Azure Storage).

Para un ejemplo de cómo aprovechar el almacenamiento de RA-GRS para garantizar la alta disponibilidad, consulte [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](storage-designing-ha-apps-with-ragrs.md).

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

### <a name="azure-storage-data-services"></a>Servicios de datos de Azure Storage
* [API de REST de servicios de almacenamiento](/rest/api/storageservices/)
* [Biblioteca de cliente de Storage para .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Biblioteca de cliente de almacenamiento para C++](https://github.com/Azure/azure-storage-cpp)
* [Biblioteca de cliente de Storage para Java/Android](https://azure.microsoft.com/develop/java/)
* [Biblioteca de cliente de Storage para Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Biblioteca de cliente de Storage para PHP](https://azure.microsoft.com/develop/php/)
* [Biblioteca de cliente de Storage para Python](https://azure.microsoft.com/develop/python/)
* [Biblioteca de cliente de Storage para Ruby](https://azure.microsoft.com/develop/ruby/)
* [Cmdlets de Storage para PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Comandos de Storage para la CLI 2.0](/cli/azure/storage)

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre Blob Storage](../blobs/storage-blobs-introduction.md)
* [Más información sobre File Storage](../storage-files-introduction.md)
* [Más información sobre Queue Storage](../queues/storage-queues-introduction.md)

Para empezar a trabajar con Azure Storage rápidamente, consulte alguno de las siguientes guías de inicio rápido:
* [Creación de una cuenta de almacenamiento mediante PowerShell](storage-quickstart-create-storage-account-powershell.md)
* [Creación de una cuenta de almacenamiento mediante la CLI](storage-quickstart-create-storage-account-cli.md)

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>Para administradores
* [Usar Azure PowerShell con Azure Storage](storage-powershell-guide-full.md)
* [Uso de la CLI de Azure con Azure Storage](../storage-azure-cli.md)

### <a name="for-net-developers"></a>Para desarrolladores de .NET
* [Introducción al Almacenamiento de blobs de Azure mediante .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Desarrollo para Azure Files con .NET](../files/storage-dotnet-how-to-use-files.md)
* [Introducción a Azure Table Storage mediante .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introducción a Azure Queue Storage mediante .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Para desarrolladores de Java/Android
* [Uso de Blob Storage en Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Desarrollo con Java para Azure Files](../files/storage-java-how-to-use-file-storage.md)
* [Uso de Table Storage en Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Uso de Queue Storage en Java](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Para desarrolladores de Node.js
* [Uso de Blob Storage en Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Uso de Table Storage en Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Uso de Queue Storage en Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para desarrolladores de PHP
* [Uso de Blob Storage en PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Uso de Table Storage en PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Uso de Queue Storage en PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para desarrolladores de Ruby
* [Uso de Blob Storage en Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Uso de Table Storage en Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Uso de Queue Storage en Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para desarrolladores de Python
* [Uso del almacenamiento de blobs de Python](../blobs/storage-python-how-to-use-blob-storage.md)
* [Desarrollo para Azure Files con Python](../files/storage-python-how-to-use-file-storage.md)
* [Uso de Table Storage en Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Uso de Queue Storage en Python](../storage-python-how-to-use-queue-storage.md)