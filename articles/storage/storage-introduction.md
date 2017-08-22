---
title: "Introducción a Azure Storage | Microsoft Docs"
description: "Información general sobre Azure Storage, el servicio de almacenamiento de datos en línea de Microsoft en la nube. Obtenga más información sobre cómo usar la mejor solución de almacenamiento en la nube disponible en sus aplicaciones."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/26/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 98670b60daca7091e09ce2ab03cf2eaff015070e
ms.contentlocale: es-es
ms.lasthandoff: 07/27/2017

---
# <a name="introduction-to-microsoft-azure-storage"></a>Introducción a Microsoft Azure Storage

## <a name="overview"></a>Información general
Azure Storage es la solución de almacenamiento en la nube para las aplicaciones modernas que dependen de la durabilidad, la disponibilidad y la escalabilidad para satisfacer las necesidades de sus clientes. Al leer este artículo, los desarrolladores, los profesionales de TI y los responsables de negocios aprenderán lo siguiente:

* Qué es Azure Storage y cómo puede aprovecharlo en las aplicaciones en la nube, móviles, de servidor y de escritorio
* Qué tipos de datos puede almacenar con los servicios de Azure Storage: datos de (objetos) blob, datos de tablas NoSQL, mensajes en cola y recursos compartidos de archivos.
* Cómo se administra el acceso a los datos en Azure Storage
* Cómo se consigue que los datos de Azure Storage sean duraderos mediante la redundancia y la replicación
* Qué pasos hay que seguir a continuación para crear una primera aplicación de Azure Storage

<!-- after our quick starts are available, replace this link with a link to one of those. 
Had to remove this article, it refers to the VS quickstarts, and they've stopped publishing them. Robin --> 
<!-- To get up and running with Azure Storage quickly, see [Get started with Azure Storage in five minutes](storage-getting-started-guide.md). -->

Para obtener más información sobre las herramientas, las bibliotecas y otros recursos para trabajar con Azure Storage, consulte el apartado [Pasos siguientes](#next-steps) que encontrará a continuación.

## <a name="what-is-azure-storage"></a>¿Qué es Azure Storage?
La informática en la nube posibilita nuevos escenarios para aplicaciones que requieren un almacenamiento escalable, duradero y de alta disponibilidad para sus datos, que es justo el motivo por el que Microsoft ha desarrollado Azure Storage. Además de permitir a los desarrolladores compilar aplicaciones a gran escala para sustentar nuevos escenarios, Azure Storage proporciona también la base de almacenamiento para Azure Virtual Machines, una prueba más de su solidez.

Azure Storage se puede escalar de forma masiva para almacenar y procesar cientos de terabytes de datos y admitir así los escenarios de datos de gran tamaño requeridos por las aplicaciones científicas, de análisis financiero y multimedia. Igualmente, permite almacenar las pequeñas cantidades de datos que se necesitan en un sitio web de una pequeña empresa. Sean cuales sean sus necesidades, solo tiene que pagar por los datos que almacene. Actualmente, Azure Storage alberga decenas de billones de objetos de cliente únicos y administra, por término medio, millones de solicitudes por segundo.

Otra de sus características es la elasticidad, lo que le permite diseñar aplicaciones para una gran audiencia global y escalarlas según sea necesario, tanto por la cantidad de datos almacenados como por el número de solicitudes de estos datos. Solo pagará por lo que use y únicamente cuando lo use.

Azure Storage utiliza un sistema de creación automática de particiones que equilibra la carga de los datos automáticamente en función del tráfico. Esto significa que, conforme crece la demanda en la aplicación, Azure Storage asigna automáticamente los recursos adecuados para ajustarse a este requisito.

Puede obtener acceso a este servicio desde cualquier lugar del mundo y cualquier tipo de aplicación, ya se ejecute en la nube, en el escritorio, en un servidor local o en un dispositivo móvil o tableta. Azure Storage se puede usar en escenarios móviles donde la aplicación almacena un subconjunto de datos en el dispositivo y los sincroniza con todo un conjunto de datos almacenados en la nube.

Azure Storage admite clientes que utilizan una gran variedad de sistemas operativos (incluidos Windows y Linux) y diversos lenguajes de programación (incluidos .NET, Node.js, Python, Ruby, PHP y C++, así como lenguajes de programación móvil) para un desarrollo más práctico. Además, expone recursos de datos a través de sencillas API REST, disponibles para cualquier cliente capaz de enviar y recibir datos mediante HTTP/HTTPS.

Azure Premium Storage ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un alto consumo de E/S que se ejecutan en Azure Virtual Machines. Con Azure Premium Storage, puede conectar varios discos de datos persistentes a una máquina virtual y configurarlos para satisfacer sus requisitos en términos de rendimiento. Un disco de SSD de Azure Premium Storage realiza copias de seguridad de cada disco de datos para ofrecer un rendimiento máximo de E/S. Consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](storage-premium-storage.md) para ver información detallada.

## <a name="introducing-the-azure-storage-services"></a>Introducción de los servicios Azure Storage
Azure Storage proporciona los cuatro servicios siguientes: Blob Storage, Table Storage, Queue Storage y File Storage.

* Blob Storage almacena datos de objetos no estructurados. Un blob puede ser un tipo cualquiera de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El almacenamiento de blobs a veces se conoce también como almacenamiento de objetos.
* Table Storage almacena conjuntos de datos estructurados. Se trata de un almacén de datos de clave-atributo NoSQL, que permite el desarrollo rápido de grandes cantidades de datos y el acceso inmediato a los mismos.
* Queue Storage ofrece una solución de mensajería confiable para el procesamiento de flujos de trabajo y para la comunicación entre los componentes de los servicios en la nube.
* File Storage ofrece almacenamiento compartido para aplicaciones heredadas que usan el protocolo SMB estándar. Las máquinas virtuales y los servicios en la nube de Azure pueden compartir datos de archivos entre componentes de aplicaciones a través de recursos compartidos montados, y las aplicaciones locales pueden tener acceso a datos de archivos de un recurso compartido a través de la API REST del servicio Archivo.

Una cuenta de almacenamiento de Azure es una cuenta segura que proporciona acceso a los servicios de Azure Storage. La cuenta de almacenamiento ofrece el espacio de nombres exclusivo para los recursos de almacenamiento. La siguiente imagen muestra las relaciones entre los recursos de Azure Storage en una cuenta de almacenamiento:

![Recursos de Azure Storage](./media/storage-introduction/storage-concepts.png)

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[!INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Blob Storage
Blob Storage ofrece una solución rentable y escalable a aquellos usuarios con grandes cantidades de datos de objetos no estructurados para almacenar en la nube. Esta característica se puede usar para almacenar contenido como:

* Documentos
* Datos de contenido social, como fotos, vídeos, música y blogs
* Copias de seguridad de archivos, equipos, bases de datos y dispositivos
* Imágenes y texto para las aplicaciones web
* Datos de configuración para las aplicaciones en la nube
* Datos de gran tamaño, como registros y otros conjuntos de datos grandes

Cada blob se organiza en un contenedor. Los contenedores también ofrecen una forma útil de asignar directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede incluir un número cualquiera de contenedores y, a su vez, un contenedor puede incluir un número cualquiera de blobs, hasta alcanzar el límite de capacidad de 500 TB de la cuenta de almacenamiento.

Blob Storage ofrece tres tipos de blobs: blobs en bloques, blobs en anexos y blobs en páginas (discos).

* Los blobs en bloques están optimizados para el streaming y para el almacenamiento de objetos en la nube, y constituyen una opción idónea para almacenar documentos, archivos multimedia y copias de seguridad, entre otros.
* Los blobs en anexos son similares a los blobs en bloques, pero están optimizados para anexar las operaciones. Un blob de anexos puede actualizarse solo al agregar un nuevo bloque al final. Los blob en anexos son una buena opción para escenarios como el registro, donde es necesario escribir solo al final del blob nuevos datos.
* Los blobs en páginas están optimizados para representar discos IaaS y admitir la escritura aleatoria. Pueden tener un tamaño máximo de 1 TB. Un disco IaaS asociado a una red de máquinas virtuales de Azure es un VHD almacenado como blob en páginas.

Para conjuntos de datos de gran tamaño donde las restricciones de red hacen imposible la carga o descarga de datos en Blob Storage a través de una conexión, puede enviar una unidad de disco duro a Microsoft para importar o exportar datos directamente desde el centro de datos. Consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](storage-import-export-service.md).

## <a name="table-storage"></a>Table Storage

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

A menudo, las aplicaciones modernas demandan almacenes de datos con una flexibilidad y escalabilidad superiores a las que requerían las generaciones anteriores de software. Table Storage ofrece un tipo de almacenamiento de alta disponibilidad y escalabilidad masiva, de forma que las aplicaciones pueden escalarse automáticamente para ajustarse a la demanda de los usuarios. Este tipo de almacenamiento se basa en un almacén de claves/atributos NoSQL de Microsoft con un diseño sin esquema que lo diferencia de las bases de datos relacionales tradicionales. Si se tiene un almacén de datos sin esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. Table Storage es fácil de usar, por lo que los desarrolladores pueden crear aplicaciones de forma rápida. El acceso a los datos es rápido y rentable para todos los tipos de aplicaciones y,  Además, su coste es muy inferior al del SQL tradicional para volúmenes de datos similares.

Este tipo de almacenamiento se basa en un almacén de clave-atributo, lo que significa que cada valor de una tabla se almacena con un nombre de propiedad tipado. El nombre de propiedad se puede usar para filtrar y especificar criterios de selección. Una colección de propiedades y sus valores, componen una entidad. Puesto que este tipo de almacenamiento no tiene esquema, dos entidades de una misma tabla pueden contener distintas colecciones de propiedades y dichas propiedades pueden ser de distintos tipos.

Table Storage se puede usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos y cualquier otro tipo de metadatos requerido por el servicio.  Una tabla puede almacenar un número cualquiera de entidades y una cuenta de almacenamiento puede incluir un número cualquiera de tablas, hasta alcanzar el límite de capacidad de este tipo de cuenta.

Al igual que ocurre con Blob Storage y Queue Storage, los desarrolladores pueden administrar Table Storage y obtener acceso al mismo a través de los protocolos REST estándar. Sin embargo, este tipo de almacenamiento también admite un subconjunto del protocolo OData, lo que simplifica las capacidades de consulta avanzadas y permite los formatos JSON y AtomPub (basados en XML).

Para las aplicaciones actuales basadas en Internet, las bases de datos NoSQL, como las de Table Storage, ofrecen una alternativa popular a las bases de datos relacionales tradicionales.

## <a name="queue-storage"></a>Queue Storage
A la hora de diseñar aplicaciones para escala, los componentes de las mismas suelen desacoplarse para poder escalarlos de forma independiente. Queue Storage ofrece una solución de mensajería de confianza para la comunicación asincrónica entre los componentes de las aplicaciones, independientemente de que se ejecuten en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Además, este tipo de almacenamiento admite la administración de tareas asincrónicas y la creación de flujos de trabajo de procesos.

Una cuenta de almacenamiento puede contener un número cualquiera de colas y, a su vez, una cola puede contener un número cualquiera de mensajes, hasta alcanzar el límite de capacidad de este tipo de cuenta. Los mensajes individuales pueden tener un tamaño máximo de 64 KB.

## <a name="file-storage"></a>File Storage
El servicio Azure Files permite configurar recursos compartidos de archivos de red de alta disponibilidad a los que se puede acceder mediante el protocolo Bloque de mensajes del servidor (SMB) estándar. Esto significa que varias máquinas virtuales pueden compartir los mismos archivos con acceso de lectura y escritura. También puede leer los archivos mediante la interfaz REST o las bibliotecas de cliente de Storage.

Lo que distingue Azure File Storage de los archivos en un recurso compartido de archivos corporativo es que puede acceder a ellos desde cualquier lugar del mundo mediante una dirección URL que apunte al archivo e incluya un token de firma de acceso compartido (SAS). Puede generar tokens SAS; permiten el acceso específico a un recurso privado durante un período de tiempo determinado.

Los recursos compartidos de archivos se pueden utilizar para muchos escenarios comunes:

* Muchas aplicaciones locales usan recursos compartidos de archivos. Esta característica facilita la migración de las aplicaciones que comparten datos en Azure. Si monta el recurso compartido de archivos en la misma letra de unidad que usa la aplicación local, la parte de la aplicación que tiene acceso al recurso compartido de archivos debería funcionar con cambios mínimos, si es que hay alguno.

* Los archivos de configuración se pueden almacenar en un recurso compartido de archivos y se puede acceder a ellos desde varias máquinas virtuales. Las herramientas y utilidades que usen varios desarrolladores de un grupo pueden almacenarse en un recurso compartido de archivos, lo que garantiza que todos los usuarios puedan encontrarlas y que utilizan la misma versión.

* Los registros de diagnóstico, las métricas y los volcados de memoria son solo tres ejemplos de datos que se pueden escribir en un recurso compartido de archivos y procesarse o analizarse posteriormente.

En este momento, las listas de control de acceso (ACL) y la autenticación basada en Active Directory no se admiten, pero se admitirán en algún momento. Las credenciales de las cuentas de almacenamiento se usan para permitir la autenticación al recurso compartido de archivos. Esto significa que cualquier usuario con el recurso compartido montado tendrá acceso completo de lectura/escritura al recurso compartido.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Acceso a recursos de blobs, tablas, colas y archivos
De forma predeterminada, solo el propietario de la cuenta de almacenamiento puede obtener acceso a los recursos de la cuenta. A fin de mantener la seguridad de los datos, cada solicitud en relación con los recursos de la cuenta deberá autenticarse. Esta autenticación se basa en un modelo de clave compartida. Los blobs también pueden configurarse para admitir la autenticación anónima.

Al crear una cuenta de almacenamiento, se le asignan dos claves de acceso privado que se utilizan para la autenticación. Tener dos claves garantiza que la aplicación siga estando disponible durante el proceso habitual de regeneración de las claves, una práctica común de administración de las claves de seguridad.

Si debe permitir el acceso controlado de los usuarios a los recursos de almacenamiento, puede crear una firma de acceso compartido. Una firma de acceso compartido (SAS) es un token que se puede asociar a una dirección URL que permite el acceso delegado a un recurso de almacenamiento. Cualquier usuario que tenga el token puede obtener acceso al recurso que señala con los permisos que especifica durante el plazo válido correspondiente. A partir de la versión 2015-04-05, Azure Storage admite dos tipos de firmas de acceso compartido: SAS de servicio y SAS de cuenta.

SAS de servicio delega el acceso a un recurso en solo uno de los servicios de almacenamiento: Blob Storage, Queue Storage, Table Storage o File Storage.

SAS de cuenta delega el acceso a los recursos en uno o varios de los servicios de almacenamiento. Puede delegar el acceso a las operaciones de nivel de servicio que no están disponibles con SAS de servicio. También puede delegar el acceso para leer, escribir y eliminar operaciones en contenedores de blobs, tablas, colas y recursos compartidos de archivos que no están permitidos con SAS de servicio.

Por último, puede especificar que un contenedor y sus blobs, o un blob específico, estén disponibles para el acceso público. Cuando se indica que un contenedor o blob es público, todos los usuarios pueden leerlo de forma anónima: no se requiere autenticación.  Los contenedores y los blobs públicos son útiles para exponer recursos, como archivos multimedia y documentos hospedados en sitios web.  Si desea reducir la latencia de red para una audiencia global, puede almacenar en caché los datos de blobs usados por los sitios web con el servicio CDN de Azure.

Para más información sobre firmas de acceso compartido, consulte [Uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md) . Consulte [Administración del acceso de lectura anónimo a contenedores y blobs](storage-manage-access-to-resources.md) y [Authentication for the Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179428.aspx) (Autenticación para los servicios de Azure Storage) para más información sobre el acceso seguro a su cuenta de almacenamiento.

## <a name="replication-for-durability-and-high-availability"></a>Replicación para obtener durabilidad y alta disponibilidad
Los datos de su cuenta de almacenamiento de Microsoft Azure se replican siempre para garantizar la durabilidad y la alta disponibilidad. La replicación copia los datos, dentro del mismo centro de datos o a otro, en función de la opción de replicación que elija. La replicación protege los datos y conserva el tiempo de actividad de la aplicación en el caso de errores transitorios del hardware. Si los datos se replican en un segundo centro de datos, esto también los protege frente a un error catastrófico en la ubicación principal.

La replicación garantiza que la cuenta de almacenamiento cumpla el [contrato de nivel de servicio (SLA) para Storage](https://azure.microsoft.com/support/legal/sla/storage/), incluso en caso de errores. Consulte en el SLA información acerca de las garantías de durabilidad y disponibilidad de Azure Storage.

Cuando cree una cuenta de almacenamiento, puede seleccionar una de las siguientes opciones de replicación:

* **Almacenamiento con redundancia local (LRS).** El almacenamiento con redundancia local mantiene tres copias de sus datos. LRS se replica tres veces dentro de un único centro de datos de una sola región. LRS protege los datos frente a errores comunes del hardware, pero no frente a errores de un único centro de datos.

    LRS se ofrece con un descuento. Para la máxima durabilidad, es recomendable utilizar el almacenamiento con redundancia geográfica, que se describe a continuación.
* **Almacenamiento con redundancia de zona (ZRS).** El almacenamiento con redundancia de zona mantiene tres copias de los datos. ZRS se replica tres veces entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones, proporcionando mayor durabilidad que LRS. ZRS garantiza la durabilidad de sus datos dentro de una sola región.

    ZRS ofrece un mayor nivel de durabilidad que LRS; sin embargo, para disfrutar de la máxima durabilidad, es recomendable usar el almacenamiento con redundancia geográfica, que se describe a continuación.

  > [!NOTE]
  > Actualmente, el ZRS solo está disponible para los blobs en bloques y únicamente se admite en las versiones del 14/02/2014 y posteriores.
  >
  > Una vez haya creado su cuenta de almacenamiento y seleccionado el ZRS, no es posible convertirla para usar ningún otro tipo de replicación o viceversa.
  >
  >
* **Almacenamiento con redundancia geográfica (GRS)**. GRS mantiene seis copias de sus datos. Con GRS, sus datos se replican tres veces dentro de la región primaria, y se replican también tres veces en una región secundaria a cientos de kilómetros de distancia de la región primaria, proporcionando el nivel más alto de durabilidad. En caso de que se produzca un error en la región primaria, Azure Storage conmutará por error a la región secundaria. GRS garantiza la durabilidad de sus datos en dos regiones independientes.

    Para obtener información sobre los emparejamientos principales y secundarios por región, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).
* **Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)**. El almacenamiento con redundancia geográfica con acceso de lectura replica sus datos en una ubicación geográfica secundaria y, además, proporciona acceso de lectura a ellos en la ubicación secundaria. El almacenamiento con redundancia geográfica con acceso de lectura le permite tener acceso a los datos desde la ubicación principal o la secundaria, ante la eventualidad de que alguna de las ubicaciones deje de estar disponible. El almacenamiento con redundancia geográfica con acceso de lectura es la opción predeterminada para su cuenta de almacenamiento cuando la crea.

  > [!IMPORTANT]
  > Si no especificó ZRS cuando creó la cuenta, puede cambiar cómo se replican los datos después de haber creado su cuenta. Sin embargo, tenga en cuenta que cambiar de LRS a GRS o RA-GRS puede suponer un coste adicional único de transferencia de datos.
  >
  >

Consulte [Replicación de Azure Storage](storage-redundancy.md) para más información sobre las opciones de replicación de almacenamiento.

Para obtener información de precios para la replicación de cuentas de almacenamiento, consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services) para más información acerca de qué servicios están disponibles en cada región.

Para obtener detalles de arquitectura sobre la durabilidad con Azure Storage, consulte [Documento de SOSP: Azure Storage: un servicio de almacenamiento en la nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferencia de datos hacia y desde Azure Storage
Puede utilizar la utilidad de línea de comandos de AzCopy para copiar datos de blobs, archivos y tablas en la cuenta de almacenamiento o entre las cuentas de almacenamiento. Consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md) para más información.

AzCopy se instala sobre la [biblioteca de movimiento de datos de Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que está disponible actualmente en versión preliminar.

El servicio Azure Import/Export proporciona una manera de importar datos de blob a la cuenta de almacenamiento o exportarlos desde la misma a través de un disco duro enviado al centro de datos de Azure. Para más información sobre el servicio Import/Export, consulte [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](storage-import-export-service.md).

## <a name="pricing"></a>Precios
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>API, bibliotecas y herramientas de Storage
Es posible acceder a los recursos de Azure Storage por medio de cualquier lenguaje que pueda realizar solicitudes HTTP/HTTPS. Además, ofrece bibliotecas de programación para varios lenguajes de amplio uso. Estas bibliotecas simplifican muchos aspectos relacionados con el uso de Azure,  Storage, ya que abordan detalles como la invocación sincrónica y asincrónica, el procesamiento por lotes de las operaciones, la administración de excepciones, los reintentos automáticos y el comportamiento operativo, entre otros. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas, además de otros previstos:

### <a name="azure-storage-data-services"></a>Servicios de datos de Azure Storage
* [API de REST de servicios de almacenamiento](http://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Biblioteca de cliente de Storage para. NET, Windows Phone y Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Biblioteca de cliente de Storage para C++](https://github.com/Azure/azure-storage-cpp)
* [Biblioteca de cliente de Storage para Java/Android](https://azure.microsoft.com/develop/java/)
* [Biblioteca de cliente de Storage para Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Biblioteca de cliente de Storage para PHP](https://azure.microsoft.com/develop/php/)
* [Biblioteca de cliente de Storage para Ruby](https://azure.microsoft.com/develop/ruby/)
* [Biblioteca de cliente de Storage para Python](https://azure.microsoft.com/develop/python/)
* [Cmdlets de Storage para PowerShell 1.0](/powershell/module/azurerm.storage/#storage)

### <a name="azure-storage-management-services"></a>Servicios de administración de Azure Storage
* [Referencia de API de REST de proveedor de recursos de almacenamiento](/rest/api/storagerp/)
* [Biblioteca de cliente del proveedor de recursos de Storage para .NET](/dotnet/api/microsoft.azure.management.storage)
* [Cmdlets de proveedor de recursos de Storage para PowerShell 1.0](/powershell/module/azure.storage)
* [API de REST de administración de servicios de almacenamiento (clásico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Servicios de movimiento de datos de Azure Storage
* [API de REST del servicio Storage Import/Export](storage-import-export-service.md)
* [Biblioteca de cliente de movimiento de datos de Storage para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Herramientas y utilidades
* El [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
* [Herramientas de cliente de Azure Storage](storage-explorers.md)
* [SDK y herramientas de Azure](https://azure.microsoft.com/tools/)
* [Emulador de Azure Storage](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [Utilidad de línea de comandos AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre Azure Storage, consulte los siguientes recursos:

### <a name="documentation"></a>Documentación
* [Documentación de Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Cree una cuenta de almacenamiento](storage-create-storage-account.md)

<!-- after our quick starts are available, replace this link with a link to one of those. 
Had to remove this article, it refers to the VS quickstarts, and they've stopped publishing them. Robin --> 
<!--* [Get started with Azure Storage in five minutes](storage-getting-started-guide.md)
-->

### <a name="for-administrators"></a>Para administradores
* [Usar Azure PowerShell con Azure Storage](storage-powershell-guide-full.md)
* [Uso de la CLI de Azure con Azure Storage](storage-azure-cli.md)

### <a name="for-net-developers"></a>Para desarrolladores de .NET
* [Introducción a Azure Blob Storage mediante .NET](storage-dotnet-how-to-use-blobs.md)
* [Introducción a Azure Table Storage mediante .NET](storage-dotnet-how-to-use-tables.md)
* [Introducción a Azure Queue Storage mediante .NET](storage-dotnet-how-to-use-queues.md)
* [Introducción a Azure File Storage en Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Para desarrolladores de Java/Android
* [Uso de Blob Storage en Java](storage-java-how-to-use-blob-storage.md)
* [Uso de Table Storage en Java](storage-java-how-to-use-table-storage.md)
* [Uso de Queue Storage en Java](storage-java-how-to-use-queue-storage.md)
* [Uso de File Storage en Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Para desarrolladores de Node.js
* [Uso de Blob Storage en Node.js](storage-nodejs-how-to-use-blob-storage.md)
* [Uso de Table Storage en Node.js](storage-nodejs-how-to-use-table-storage.md)
* [Uso de Queue Storage en Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para desarrolladores de PHP
* [Uso de Blob Storage en PHP](storage-php-how-to-use-blobs.md)
* [Uso de Table Storage en PHP](storage-php-how-to-use-table-storage.md)
* [Uso de Queue Storage en PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para desarrolladores de Ruby
* [Uso de Blob Storage en Ruby](storage-ruby-how-to-use-blob-storage.md)
* [Uso de Table Storage en Ruby](storage-ruby-how-to-use-table-storage.md)
* [Uso de Queue Storage en Ruby](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para desarrolladores de Python
* [Uso de Blob Storage en Python](storage-python-how-to-use-blob-storage.md)
* [Uso de Table Storage en Python](storage-python-how-to-use-table-storage.md)
* [Uso de Queue Storage en Python](storage-python-how-to-use-queue-storage.md)
* [Uso de File Storage en Python](storage-python-how-to-use-file-storage.md)

