---
title: Uso de la importación y exportación para transferir datos al almacenamiento en blobs | Microsoft Docs
description: Obtenga información sobre cómo crear trabajos de importación y exportación en el Portal de Azure clásico para transferir datos al Almacenamiento de blobs.
author: renashahmsft
manager: aungoo
editor: tysonn
services: storage
documentationcenter: ''

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: renash;robinsh

---
# Uso del servicio de importación y exportación de Microsoft Azure para transferir datos al almacenamiento de blobs
## Información general
El servicio de importación y exportación de Azure le permite transferir de forma segura grandes cantidades de datos al Almacenamiento de blobs de Azure mediante el envío de unidades de disco duro a un centro de datos de Azure. También puede usar este servicio para transferir datos desde el Almacenamiento de blobs de Azure hasta las unidades de disco duro y enviarlas al sitio local. Este servicio resulta adecuado para aquellas situaciones en la que quiere transferir varios TB de datos a o desde Azure, pero la carga o descarga a través de la red no es factible debido al ancho de banda limitado o a los costos elevados de la red.

El servicio requiere que las unidades de disco duro estén cifradas con BitLocker para la seguridad de los datos. El servicio admite cuentas de almacenamiento clásicas presentes en todas las regiones de Azure público. Las unidades de disco duro se deben enviar a una de las ubicaciones especificadas más adelante en este artículo.

En este artículo, obtendrá más información sobre el servicio de importación y exportación de Azure y cómo enviar las unidades para copiar datos con el Almacenamiento de blobs de Azure como origen y destino.

> [!IMPORTANT]
> Puede crear y administrar trabajos de importación y exportación de almacenamiento clásico mediante el Portal de Azure clásico o las [API de REST del servicio Importación/Exportación](http://go.microsoft.com/fwlink/?LinkID=329099). En estos momentos, no se admiten las cuentas de almacenamiento de Azure Resource Manager.
> 
> 

## ¿Cuándo debo utilizar el servicio Importación/Exportación de Azure?
Puede usar el servicio de importación y exportación al de Azure cuando la carga o descarga de datos a través de la red es demasiado lenta o el costo de ancho de banda de red adicional resulta prohibitivo.

Puede utilizar este servicio en los siguientes escenarios:

* Migración de datos a la nube: mueva rápidamente grandes cantidades de datos a Azure de manera rápida y rentable.
* Distribución de contenido: envíe rápidamente datos a los sitios de cliente.
* Copia de seguridad: realice copias de seguridad de los datos locales para almacenarlos en el Almacenamiento de blobs de Azure.
* Recuperación de datos: recupere una gran cantidad de datos almacenados en el almacenamiento de blobs y recíbalos en su ubicación local.

## Requisitos previos
En esta sección, hemos enumerado los requisitos previos necesarios para utilizar este servicio. Revíselos detenidamente antes de enviar sus unidades.

### Cuenta de almacenamiento
Debe disponer de una suscripción de Azure existente y una o varias cuentas de almacenamiento **clásico** para utilizar el servicio Importación/Exportación. Puede usar cada trabajo para transferir datos desde o hacia una sola cuenta de almacenamiento clásico. Dicho de otra forma, un trabajo de importación y exportación no puede abarcar varias cuentas de almacenamiento. Para obtener información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

### Tipos de blobs
Puede utilizar el servicio Importación/Exportación de Azure para copiar datos de blobs **en bloque** o **en página**. Y a la inversa, solo puede exportar blobs **en bloque**, **en página** o **en anexos** de Almacenamiento de Azure mediante este servicio.

### Trabajo
Para comenzar el proceso de importación o exportación desde el almacenamiento en blobs, cree primero un trabajo. Un trabajo puede ser un trabajo de importación o un trabajo de exportación:

* Cree un trabajo de importación si desea transferir datos que tiene en su instalación local a blobs en su cuenta de almacenamiento de Azure.
* Cree un trabajo de exportación si desea transferir datos que actualmente están almacenados en blobs de su cuenta de almacenamiento a discos duros que se le enviarán posteriormente.

Cuando crea un trabajo, notifica al servicio de importación y exportación que enviará uno o varios discos duros a un centro de datos de Azure.

* En el caso de un trabajo de importación, enviará las unidades de disco duro que contengan los datos.
* Si es un trabajo de exportación, enviará las unidades de disco duro vacías.
* Puede enviar hasta 10 unidades de disco duro por trabajo.

Para crear un trabajo de importación o exportación, puede usar el [Portal de Azure clásico](https://manage.windowsazure.com/) o la [API de REST de Importación/Exportación de Almacenamiento de Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

### Herramienta de cliente
El primer paso para crear un trabajo de **importación** consiste en preparar la unidad que se enviará para esta operación. Para preparar la unidad, debe conectarla a un servidor local y ejecutar la herramienta cliente de importación y exportación de Azure en dicho servidor. Esta herramienta cliente facilita la copia de los datos en la unidad, el cifrado de los datos en la unidad con BitLocker y la generación de los archivos de diario de unidad.

Los archivos del diario almacenan información básica sobre el trabajo y la unidad, como el número de serie de la unidad o el nombre de la cuenta de almacenamiento. Este archivo de diario no se almacena en la unidad. Se utiliza durante la creación del trabajo de importación. Más adelante en este artículo se proporcionan pasos detallados sobre la creación del trabajo.

La herramienta cliente solo es compatible con el sistema operativo de Windows de 64 bits. Consulte la sección [Sistema operativo](#operating-system) para ver cuáles son las versiones específicas admitidas.

Descargue la versión más reciente de la [herramienta de cliente de Importación/Exportación de Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Para obtener más información sobre el uso de la herramienta de Importación/Exportación de Azure, consulte [Referencia de la herramienta de importación y exportación de Azure](http://go.microsoft.com/fwlink/?LinkId=329032).

### Unidades de disco duro
El servicio de importación y exportación solo admite unidades de disco duro internas SATA II/III de 3,5 pulgadas. Puede utilizar unidades de disco duro de hasta 10 TB. Para los trabajos de importación, solo se procesará el primer volumen de datos de la unidad. El volumen de datos debe tener formato NTFS. Para copiar datos en la unidad de disco duro, puede conectarla directamente mediante un conector SATA o externamente mediante un adaptador USB SATA II/III externo. Se recomienda usar uno de los siguientes adaptadores USB SATA II/III externos:

* Anker 68UPSATAA-02BU
* Anker 68UPSHHDS-BU
* Startech SATADOCK22UE
* Sharkoon QuickPort XT HC

Si su convertidor no aparece en la lista anterior, puede probar a ejecutar la herramienta de importación y exportación de Azure para preparar la unidad y comprobar si funciona antes de adquirir un convertidor compatible.

> [!IMPORTANT]
> Este servicio no admite unidades de disco duro externas que incorporen un adaptador USB integrado. Además, no se puede usar el disco que hay en el interior de la carcasa de las unidades de disco duro externo, así que le rogamos que no envíe este tipo de unidad.
> 
> 

### Cifrado
Los datos de la unidad deben estar cifrados con BitLocker Drive Encryption. Así estarán protegidos mientras se encuentren en tránsito.

En los trabajos de importación, hay dos maneras de realizar el cifrado. La primera consiste en usar el parámetro /encrypt al ejecutar la herramienta de cliente durante la preparación de la unidad. La segunda, en habilitar el cifrado de BitLocker manualmente en la unidad y especificar la clave de cifrado en la línea de comandos de la herramienta de cliente durante la preparación de la unidad.

En los trabajos de exportación, después de copiar los datos en las unidades, el servicio cifrará la unidad con BitLocker antes de enviársela de vuelta al usuario. La clave de cifrado se proporciona a través del Portal de Azure clásico.

### Sistema operativo
Puede utilizar uno de los siguientes sistemas operativos de 64 bits para preparar la unidad de disco duro mediante la herramienta de Importación/Exportación de Azure antes de enviarla a Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. Todos estos sistemas operativos admiten el cifrado de unidad BitLocker.

> [!IMPORTANT]
> <sup>1</sup> Si usa un equipo con Windows 10 para preparar la unidad de disco duro, descargue la versión más reciente de la herramienta de Importación/Exportación de Azure.
> 
> 

### Ubicaciones
El servicio Importación/Exportación de Azure permite copiar datos en todas las cuentas de almacenamiento de Azure público como origen y destino. Las unidades de disco duro se pueden enviar a una de las siguientes ubicaciones. Si la cuenta de almacenamiento se encuentra en una ubicación de Azure público que no se especifica aquí, se proporcionará una de envío alternativa al crear el trabajo mediante el Portal de Azure clásico o la API de REST de Importación/Exportación.

Ubicaciones de envío admitidas:

* Este de EE. UU.
* Oeste de EE. UU.
* Este de EE. UU. 2
* Central EE. UU.:
* Centro-Norte de EE. UU
* Centro-Sur de EE. UU
* Europa del Norte
* Europa occidental
* Asia oriental
* Sudeste asiático
* Australia Oriental
* Sudeste de Australia
* Oeste de Japón
* Este de Japón
* India Central

### Envío
**Envío de unidades al centro de datos:**

Al crear un trabajo de importación o exportación, se le proporcionará una dirección de envío de una de las ubicaciones admitidas para el envío de sus unidades. La dirección de envío proporcionada dependerá de la ubicación de la cuenta de almacenamiento, pero no puede ser igual que la de esta.

Puede utilizar las empresas de transporte FedEx, DHL, UPS o el servicio postal de los Estados Unidos para enviar las unidades.

**Envío de unidades desde el centro de datos:**

Al crear un trabajo de importación o exportación, debe proporcionar a Microsoft un remite para que le devuelva las unidades enviadas cuando finalice su trabajo. Asegúrese de proporcionar una dirección de retorno válida para evitar retrasos en el procesamiento.

También debe proporcionar un número de cuenta válido de las empresas de transporte FedEx o DHL para que Microsoft realice la devolución. En el caso de FedEx, se requiere un número de cuenta para devolver las unidades enviadas desde Estados Unidos y Europa. En DHL, se requiere un número de cuenta para devolver las unidades enviadas desde Asia y Australia. Puede crear una cuenta de [FedEx](http://www.fedex.com/us/oadr/) (para EE. UU. y Europa) o [DHL](http://www.dhl.com/) (Asia y Australia) si no tiene una. Si ya tiene un número de cuenta de uno de estos transportistas, compruebe que sea válido.

Al enviar los paquetes, debe seguir los términos establecidos en los [Términos de servicio de Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Tenga en cuenta que es posible que los medios físicos que está enviando deban cruzar alguna frontera internacional. Usted es el responsable de asegurar que los medios y datos físicos se importan o exportan de acuerdo con todas las normativas aplicables. Antes de enviar los medios físicos, pida asesoramiento para comprobar que los medios y datos se pueden enviar legalmente al centro de datos identificado. De este modo, se asegurará de que llegan a Microsoft de manera puntual. Por ejemplo, cualquier paquete que cruce fronteras internacionales necesita que una factura comercial acompañe al paquete (excepto si cruza fronteras dentro de la Unión Europea). Puede imprimir una copia cumplimentada de la factura comercial desde el sitio web de la compañía. Ejemplo de factura comercial son la [factura comercial de DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) o la [factura comercial de FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Asegúrese de que Microsoft no se ha indicado como el exportador.
> 
> 

## ¿Cómo funciona el servicio de importación y exportación de Azure?
Se pueden transferir datos entre el sitio local y el Almacenamiento de blobs de Azure mediante el servicio Importación/Exportación de Azure; para ello, se crean trabajos y se envían unidades de disco duro a un centro de datos de Azure. Cada unidad de disco duro que se envía está asociada a un único trabajo. Cada trabajo está asociado a una única cuenta de almacenamiento. Revise atentamente la [sección de requisitos previos](#pre-requisites) para conocer las características específicas de este servicio, como los tipos de blobs, los tipos de discos, las ubicaciones y las formas de envío que se admiten.

En esta sección, vamos a realizar una descripción de alto nivel de los pasos que intervienen en los trabajos de importación y exportación. Más adelante, en la sección [Inicio rápido](#quick-start), proporcionaremos instrucciones paso a paso para crear un trabajo de importación y exportación.

### Dentro de un trabajo de importación
En un nivel alto, un trabajo de importación implica los siguientes pasos:

* Determine los datos que desea importar y el número de unidades que necesitará.
* Identifique los blobs de destino para los datos en Almacenamiento de blobs.
* Utilice la herramienta de Importación/Exportación de Azure para copiar los datos en una o varias unidades de disco duro y cífrelas con BitLocker.
* Cree un trabajo de importación en la cuenta de almacenamiento clásico de destino mediante el Portal de Azure clásico o la API de REST de Importación/Exportación. Si utiliza el Portal de Azure clásico, cargue los archivos del diario de unidad.
* Indique el remite y el número de cuenta de la empresa de transporte que se utilizará para que se le devuelvan las unidades.
* Envíe las unidades de disco duro a la dirección de envío proporcionada durante la creación del trabajo.
* Actualice el número de seguimiento de entrega en los datos del trabajo de importación y envíe dicho trabajo.
* Las unidades se reciben y procesan en el centro de datos de Azure.
* Las unidades se devuelven usando la cuenta de la empresa de transporte al remite indicado en el trabajo de importación.
  
    ![Figura 1: Importación de flujos de trabajo](./media/storage-import-export-service/importjob.png)

### Dentro de un trabajo de exportación
En un nivel alto, un trabajo de exportación implica los siguientes pasos:

* Determine los datos que desea exportar y el número de unidades que necesitará.
* Identifique los blobs de origen o las rutas de acceso al contenedor de los datos en el Almacenamiento de blobs.
* Cree un trabajo de exportación en la cuenta de almacenamiento de origen mediante el Portal de Azure clásico o la API de REST de Importación/Exportación.
* Especifique los blobs de origen o las rutas de acceso al contenedor de los datos en el trabajo de exportación.
* Indique el remite y el número de cuenta de la empresa de transporte que se utilizará para que se le devuelvan las unidades.
* Envíe las unidades de disco duro a la dirección de envío proporcionada durante la creación del trabajo.
* Actualice el número de seguimiento de entrega en los datos del trabajo de exportación y envíe dicho trabajo.
* Las unidades se reciben y procesan en el centro de datos de Azure.
* Las unidades se cifran con BitLocker y las claves están disponibles a través del Portal de Azure clásico.
* Las unidades se devuelven usando la cuenta de la empresa de transporte al remite indicado en el trabajo de importación.
  
    ![Figura 2: Exportación de flujos de trabajo](./media/storage-import-export-service/exportjob.png)

### Visualización del estado del trabajo
Puede realizar el seguimiento del estado de sus trabajos de importación y exportación desde el Portal de Azure clásico. Acceda a la cuenta de almacenamiento en el Portal de Azure clásico y haga clic en la pestaña **Importación/Exportación**. En la página aparecerá una lista con sus trabajos. Puede filtrar la lista en función del estado del trabajo, el nombre del trabajo, el tipo de trabajo o el número de seguimiento.

Verá uno de los siguientes estados de trabajo en función de en qué fase del proceso esté la unidad.

| Estado del trabajo | Description |
|:--- |:--- |
| Creating |El trabajo se ha creado pero todavía no se ha proporcionado la información de envío. |
| Envío |El trabajo se ha creado y ya se ha proporcionado la información de envío. **Nota**: Cuando la unidad se envía al centro de datos de Azure, el estado puede seguir indicando "Enviando" durante algún tiempo. Después de que el servicio inicia la copia de los datos, el estado cambia a "Transfiriendo". Si desea ver el estado más específico de la unidad, puede usar la API de REST de Importación/Exportación. |
| Transferring |Los datos se están transfiriendo desde su disco duro (para un trabajo de importación) o a su disco duro (para un trabajo de exportación). |
| Packaging |Se ha completado la transferencia de datos y se está preparando su disco duro para el envío de vuelta. |
| Complete |El disco duro ya se le ha enviado. |

### Tiempo para procesar el trabajo
El tiempo que se tarda en procesar un trabajo de importación o exportación varía en función de determinados factores, como el tiempo de envío, el tipo de trabajo, el tipo y el tamaño de los datos copiados o el tamaño de los discos proporcionados. El servicio Importación/Exportación no tiene un SLA (Acuerdo de Nivel de Servicio). Puede utilizar la API de REST para realizar un seguimiento más estrecho del progreso del trabajo. Hay un parámetro de porcentaje completado en la operación de lista de trabajos que proporciona una indicación del progreso de la copia. Si necesita una estimación para realizar un trabajo de importación o exportación en el que el tiempo es un factor crítico, póngase en contacto con nosotros.

### Precios
**Cuota de manipulación de unidades**

Existe una cuota de manipulación por cada unidad procesada como parte del trabajo de importación o exportación. Consulte los detalles en [Precios de importación/exportación](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Gastos de envío**

Al enviar unidades a Azure, pagar los gastos de envío al transportista. Cuando Azure le devuelve la unidad, los gastos de envío se le cargan en la cuenta de la empresa de transporte que indicó al crear el trabajo.

**Costos de transacción**

La importación de datos en el Almacenamiento de blobs no tiene ningún costo de transacción asociado. Los cargos de salida estándar son aplicables cuando se exportan datos desde el Almacenamiento de blobs. Para más información sobre los costos de transacción, consulte [Detalles de precios de Transferencias de datos ](https://azure.microsoft.com/pricing/details/data-transfers/).

## Inicio rápido
Más adelante, proporcionaremos instrucciones paso a paso para crear trabajos de importación y exportación. Asegúrese de cumplir todos los [requisitos previos](#pre-requisites) antes de continuar.

## Creación de un trabajo de importación
Cree un trabajo de importación para copiar datos en la cuenta de almacenamiento de Azure enviando una o varias unidades de disco duro con estos datos al centro de datos especificado. En el trabajo de importación se comunican detalles sobre las unidades de disco duro, los datos que se copian, la cuenta de almacenamiento de destino y la información de envío al servicio Importación/Exportación de Azure. El proceso de creación de un trabajo de importación consta de tres pasos. En primer lugar, prepare las unidades mediante la herramienta cliente de importación y exportación de Azure. En segundo lugar, envíe un trabajo de importación mediante el Portal de Azure clásico. En tercer lugar, envíe las unidades a la dirección de envío proporcionada durante la creación del trabajo y actualice la información de envío en los detalles del trabajo.

> [!IMPORTANT]
> Puede enviar un único trabajo por cuenta de almacenamiento. Cada unidad que envíe se puede importar a una cuenta de almacenamiento. Por ejemplo, supongamos que desea importar datos en dos cuentas de almacenamiento. Debe utilizar unidades de disco duro independientes para cada cuenta de almacenamiento y crear trabajos independientes por cada cuenta de almacenamiento.
> 
> 

### Preparación de las unidades
El primer paso al importar datos mediante el servicio Importación/Exportación de Azure consiste en preparar las unidades mediante la herramienta de cliente Importación/Exportación de Azure. Siga estos pasos para preparar las unidades.

1. Identifique los datos que se van a importar. Podrían ser directorios o archivos independientes situados en el servidor local o un recurso compartido de red.
2. Determine el número de unidades que necesitará según el tamaño total de los datos. Proporcione el número necesario de unidades de disco duro SATA II/III de 3,5 pulgadas.
3. Identifique la cuenta de almacenamiento, el contenedor, los directores virtuales y los blobs de destino.
4. Determine los directorios o archivos independientes que se copiarán en cada unidad de disco duro.
5. Utilice la [herramienta de Importación/Exportación de Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) para copiar los datos en una o varias unidades de disco duro.
   
   * La herramienta de importación y exportación de Azure crea sesiones de copia para copiar los datos del origen en las unidades de disco duro de destino. En una única sesión de copia, la herramienta puede copiar un único directorio junto con sus subdirectorios, o un único archivo.
   * Es posible que si los datos de origen están repartidos entre muchos directorios, necesite varias sesiones de copia.
   * Cada unidad de disco duro que prepare requerirá al menos una sesión de copia.
6. Puede especificar el parámetro /encrypt para habilitar el cifrado de Bitlocker en la unidad de disco duro. Como alternativa, también puede habilitar el cifrado de Bitlocker manualmente en la unidad de disco duro y proporcionar la clave mientras se ejecuta la herramienta.
7. La herramienta de importación y exportación de Azure genera un archivo de diario de unidad para cada unidad que se haya preparado. El archivo de diario de la unidad se almacena en su equipo local, no en la unidad en sí. El archivo de diario se carga al crear el trabajo de importación. Un archivo de diario de unidad incluye el identificador de la unidad y la clave de BitLocker, así como otras informaciones acerca de la unidad. **Importante**: Cada unidad de disco duro que prepare generará un archivo del diario. Al crear el trabajo de importación mediante el Portal de Azure clásico, tendrá que cargar todos los archivos del diario de las unidades que formen parte de dicho trabajo de importación. Las unidades sin archivos de diario no se procesarán.
8. No modifique los datos de las unidades de disco duro o el archivo de diario después de completar la preparación del disco.

A continuación, se muestran los comandos para preparar la unidad de disco duro mediante la herramienta cliente de importación y exportación de Azure, junto con algunos ejemplos.

Comando PrepImport de la herramienta de importación y exportación de Azure para la primera sesión de copia de un directorio:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Ejemplo:**

En el ejemplo siguiente, copiamos todos los archivos y subdirectorios de H:\\Video en la unidad de disco duro montada en "X:". Los datos se importarán en la cuenta de almacenamiento de destino especificada mediante la clave de la cuenta de almacenamiento y en el contenedor de almacenamiento llamado "video". Si el contenedor de almacenamiento no existe, se creará. Este comando también formatea y cifra la unidad de disco duro de destino.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Comando PrepImport de la herramienta de importación y exportación de Azure para las sucesivas sesiones de copia de un directorio:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

En las sucesivas sesiones de copia en la misma unidad de disco duro, especifique el mismo nombre de archivo del diario e indique un nuevo id. de sesión. No hace falta proporcionar la clave de la cuenta de almacenamiento ni la unidad de destino, ni tampoco formatear y cifrar la unidad. En este ejemplo, vamos a copiar la carpeta H:\\Photo y sus subdirectorios en la misma unidad de destino: el contenedor de almacenamiento llamado "photo".

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Comando PrepImport de la herramienta de Importación/Exportación de Azure para la primera sesión de copia de un archivo:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Comando PrepImport de la herramienta de Importación/Exportación de Azure para las sucesivas sesiones de copia de un archivo:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Recordatorio**: De forma predeterminada, los datos se importan como blobs en bloques. Puede utilizar el parámetro /BlobType para importar los datos como un blobs en páginas. Por ejemplo, si va a importar archivos VHD que se van a montar como discos en una máquina virtual de Azure, debe importarlos como blobs en páginas. Si no está seguro de qué tipo de blob debe usar, puede especificar /blobType:auto para que nos ayude a determinar cuál es el correcto. En este caso, todos los archivos vhd y vhdx se importan como blobs en páginas y el resto como blobs en bloques.

Para más información sobre el uso de la herramienta de cliente de Importación/Exportación de Azure, consulte [Preparar las unidades de disco duro para un trabajo de importación](https://msdn.microsoft.com/library/dn529089.aspx).

Además, consulte [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](https://msdn.microsoft.com/library/dn529097.aspx) si desea obtener instrucciones paso a paso más detalladas.

### Creación del trabajo de importación
1. Una vez preparada la unidad, acceda a la cuenta de almacenamiento en el [Portal de Azure clásico](https://manage.windowsazure.com) y visualice el panel. En **vista rápida**, haga clic en **Crear un trabajo de importación**. Revise los pasos y active la casilla para indicar que ha preparado su unidad y que tiene el archivo de diario de unidad disponible.
2. En el paso 1, proporcione la información de contacto de la persona responsable de este trabajo de importación y una dirección de retorno válida. Si desea guardar datos de registro detallados del trabajo de importación, active la opción **Guardar el registro detallado en mi contenedor de blobs 'waimportexport'**.
3. En el paso 2, cargue los archivos de diario de unidad que haya obtenido durante el paso de preparación de la unidad. Tendrá que cargar un archivo por cada unidad que haya preparado.
   
   ![Creación del trabajo de importación - Paso 3](./media/storage-import-export-service/import-job-03.png)
4. En el paso 3, escriba un nombre descriptivo para el trabajo de importación. Tenga en cuenta que el nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. El nombre elegido le servirá para realizar un seguimiento de sus trabajos mientras están en progreso y una vez se hayan completado.
   
   A continuación, seleccione en la lista la región de su centro de datos. La región del centro de datos indicará el centro de datos y la dirección donde debe enviar el paquete. Para obtener más información, consulte las P+F que se muestran a continuación.
5. En el paso 4, seleccione en la lista la empresa de transporte que realizará la devolución y, después, escriba el número de cuenta de dicha empresa. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación.
   
   Si tiene un número de seguimiento, seleccione en la lista la empresa de transporte que realizará la entrega y, después, escriba el número de seguimiento.
   
   Si todavía no tiene un número de seguimiento, elija **Facilitaré mi información de envío para este trabajo de importación cuando haya enviado mi paquete** y, a continuación, finalice el proceso de importación.
6. Para escribir el número de seguimiento tras haber enviado el paquete, vuelva a la página **Importación/Exportación** de la cuenta de almacenamiento en el Portal de Azure clásico, seleccione el trabajo en la lista y elija **Información de envío**. Navegue por el asistente y escriba el número de seguimiento en el paso 2.
   
    Si el número de seguimiento no se actualiza dentro del plazo de 2 semanas desde la creación del trabajo, este expirará.
   
    Si el estado se encuentra en estado de creación, envío o transferencia, también puede actualizar el número de cuenta del transportista en el paso 2 del asistente. Una vez que el trabajo se encuentre en estado de empaquetado, no podrá actualizar el número de cuenta del transportista de ese trabajo.
7. Puede realizar un seguimiento del progreso del trabajo en el panel del portal. Consulte en [Visualización del estado del trabajo](#viewing-your-job-status) lo que significa cada estado de trabajo de la sección anterior.

## Creación de un trabajo de exportación
Cree un trabajo de exportación para notificar al servicio Importación/Exportación que va a enviar al centro de datos una o varias unidades vacías para que se exporten los datos de la cuenta de almacenamiento a las unidades y recibirlas posteriormente.

### Preparación de las unidades
Para preparar las unidades para el trabajo de exportación, se recomienda realizar las siguientes comprobaciones previas:

1. Compruebe el número de discos necesarios mediante el comando PreviewExport de la herramienta Importación/Exportación de Azure. Para más información, consulte [Vista previa de uso de disco para un trabajo de exportación](https://msdn.microsoft.com/library/azure/dn722414.aspx). Esto le ayuda a obtener una vista previa del uso del disco para los blobs que ha seleccionado, en función del tamaño de las unidades que se va a usar.
2. Compruebe que puede leer o escribir en la unidad de disco duro que se va a enviar para el trabajo de exportación.

### Creación del trabajo de exportación
1. Para crear un trabajo de exportación, acceda a la cuenta de almacenamiento en el [Portal de Azure clásico](https://manage.windowsazure.com) y visualice el panel. En **Vista rápida**, haga clic en **Crear un trabajo de exportación** y siga los pasos del asistente.
2. En el paso 2, proporcione la información de contacto de la persona responsable de este trabajo de exportación. Si desea guardar datos de registro detallados del trabajo de exportación, active la opción **Guardar el registro detallado en mi contenedor de blobs 'waimportexport'**.
3. En el paso 3, especifique los datos de blobs que desea exportar desde su cuenta de almacenamiento a una o varias unidades vacías. Puede elegir exportar todos los datos de blobs de la cuenta de almacenamiento o especificar los blobs o conjuntos de blobs que se van a exportar.
   
   Para especificar un blob que desea exportar, utilice el selector **Igual a** y especifique la ruta relativa al blob, empezando por el nombre del contenedor. Utilice *$root* para especificar el contenedor raíz.
   
   Para especificar todos los blobs que empiecen por un prefijo, utilice el selector **Empieza por** y especifique el prefijo, empezando por una barra diagonal "/". El prefijo puede ser el prefijo del nombre del contenedor, el nombre del contenedor completo o el nombre del contendor completo seguido del prefijo del nombre del blob.
   
   En la siguiente tabla se muestran ejemplos de rutas de acceso del blob válidas:
   
   | Selector | Ruta del blob | Description |
   | --- | --- | --- |
   | Starts With |/ |Exporta todos los blobs de la cuenta de almacenamiento. |
   | Starts With |/$root/ |Exporta todos los blobs del contenedor raíz. |
   | Starts With |/book |Exporta todos los blobs de cualquier contenedor que empiecen por el prefijo **book**. |
   | Starts With |/music/ |Exporta todos los blobs del contenedor **music**. |
   | Starts With |/music/love |Exporta todos los blobs del contenedor **music** que empiecen por el prefijo **love**. |
   | Equal To |$root/logo.bmp |Exporta el blob **logo.bmp** del contenedor raíz. |
   | Equal To |videos/story.mp4 |Exporta el blob **story.mp4** del contenedor **videos** |
   
   Debe proporcionar las rutas de acceso del blob en formatos válidos para evitar errores durante el procesamiento, tal y como se muestra en esta captura de pantalla.
   
   ![Creación del trabajo de exportación - Paso 3](./media/storage-import-export-service/export-job-03.png)
4. En el paso 4, escriba un nombre descriptivo para el trabajo de exportación. El nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios.
   
   La región del centro de datos indicará el centro de datos al que debe enviar su paquete. Para obtener más información, consulte las P+F que se muestran a continuación.
5. En el paso 5, seleccione en la lista el transportista para la devolución y, a continuación, escriba el número de cuenta del transportista. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de exportación.
   
   Si tiene un número de seguimiento, seleccione en la lista la empresa de transporte que realizará la entrega y, después, escriba el número de seguimiento.
   
   Si todavía no tiene un número de seguimiento, elija **Proporcionaré la información de envío de este trabajo de exportación cuando haya enviado el paquete** y, a continuación, finalice el proceso de exportación.
6. Para escribir el número de seguimiento tras haber enviado el paquete, vuelva a la página **Importación/Exportación** de la cuenta de almacenamiento en el Portal de Azure clásico, seleccione el trabajo en la lista y elija **Información de envío**. Navegue por el asistente y escriba el número de seguimiento en el paso 2.
   
    Si el número de seguimiento no se actualiza dentro del plazo de 2 semanas desde la creación del trabajo, este expirará.
   
    Si el estado se encuentra en estado de creación, envío o transferencia, también puede actualizar el número de cuenta del transportista en el paso 2 del asistente. Una vez que el trabajo se encuentre en estado de empaquetado, no podrá actualizar el número de cuenta del transportista de ese trabajo.
   
   > [!NOTE]
   > Si el blob que se va a exportar está en uso en el momento de la copia en la unidad de disco duro, el servicio de importación y exportación de Azure tomará una instantánea del blob y copiará la instantánea.
   > 
   > 
7. Puede realizar un seguimiento del progreso del trabajo en el panel del Portal de Azure clásico. Vea lo que significa cada estado del trabajo en la sección anterior sobre "Visualización del estado del trabajo".
8. Después de recibir las unidades con los datos exportados, puede ver y copiar las claves de BitLocker generadas por el servicio para la unidad. Acceda a la cuenta de almacenamiento en el Portal de Azure clásico y haga clic en la pestaña Importación/Exportación. Seleccione su trabajo de exportación de la lista y haga clic en el botón Ver claves. Las claves de BitLocker aparecen como se muestra a continuación:
   
   ![Visualización de claves de BitLocker de un trabajo de exportación](.\\media\\storage-import-export-service\\export-job-bitlocker-keys.png)

Vaya a la sección de P+F a continuación, en ella se tratan las preguntas más comunes que se encuentran los clientes al utilizar este servicio.

## Preguntas frecuentes
**¿Cuánto tiempo se tardan en copiar los datos una vez que las unidades llegan al centro de datos?**

El tiempo de copia varía según distintos factores, como el tipo de trabajo, el tipo y el tamaño de los datos que se copian, el tamaño de los discos proporcionados y la carga de trabajo existente, pero puede oscilar entre un par de días y un par de semanas. Por lo tanto, es difícil proporcionar una estimación general. El servicio, cuando es posible, trata de optimizar el trabajo mediante la copia de varias unidades en paralelo. Si necesita una estimación para realizar un trabajo de importación o exportación en el que el tiempo es un factor crítico, póngase en contacto con nosotros.

**¿Cuándo debo usar el servicio Importación/Exportación de Azure?** Importación/Exportación de Azure se debe usar si la carga o la descarga a través de la red demora más de 7 días. Puede calcular cuánto demorará con cualquier calculadora en línea o si [descarga](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) una que se encuentra en nuestro ejemplo de API de REST de Importación/Exportación de Azure en el repositorio de ejemplos de Azure en [Calculadora de velocidad de la transferencia de datos](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). No es un cálculo exacto, sino que una indicación aproximada.

**¿Puedo usar el servicio Importación/Exportación de Azure con una cuenta de almacenamiento de Azure Resource Manager?**

No, no se pueden copiar datos en una cuenta de almacenamiento de Azure Resource Manager directamente mediante el servicio Importación/Exportación de Azure. El servicio solo admite cuentas de almacenamiento clásico. En breve, se agregará compatibilidad con las cuentas de almacenamiento de Azure Resource Manager. Como alternativa, puede importar datos a una cuenta de almacenamiento clásico y migrarlos a la de Azure Resource Manager mediante [AzCopy](storage-use-azcopy.md) o CopyBlob.

**¿Puedo copiar archivos de Azure mediante el servicio Importación/Exportación de Azure?**

No, el servicio de importación y exportación de Azure solo admite blobs en bloques y blobs en páginas. El resto de los tipos de almacenamiento, incluidos Archivos, Tablas y Colas de Azure, no se admiten.

**¿Está disponible el servicio Importación/Exportación de Azure para suscripciones de CSP?**

No, el servicio Importación/Exportación de Azure no admite suscripciones de CSP. En el futuro, se agregará la compatibilidad.

**¿Puedo omitir el paso de preparación de las unidades en un trabajo de importación o puedo preparar una unidad sin realizar una copia?**

Cualquier unidad que quiera omitir en la importación de datos se debe preparar mediante la herramienta de cliente de Importación/Exportación de Azure. Debe utilizar la herramienta cliente para copiar datos en la unidad.

**¿Es necesario realizar alguna preparación en el disco al crear un trabajo de exportación?**

No, pero se recomiendan algunas comprobaciones previas. Compruebe el número de discos necesarios mediante el comando PreviewExport de la herramienta Importación/Exportación de Azure. Para más información, consulte [Vista previa de uso de disco para un trabajo de exportación](https://msdn.microsoft.com/library/azure/dn722414.aspx). Esto le ayuda a obtener una vista previa del uso del disco para los blobs que ha seleccionado, en función del tamaño de las unidades que se va a usar. Compruebe también que puede leer y escribir en la unidad de disco duro que se va a enviar para el trabajo de exportación.

**¿Qué sucede si envío por error un disco duro que no cumple con los requisitos de compatibilidad?**

El centro de datos de Azure le devolverá la unidad que no cumple con los requisitos de compatibilidad. Si solo algunos de los dispositivos del paquete cumplen con los requisitos de compatibilidad, esas unidades se procesarán, y las unidades que no cumplan los requisitos se le devolverán.

**¿Puedo cancelar un trabajo?**

Puede cancelar un trabajo si el estado es Creating o Shipping.

**¿Durante cuánto tiempo puedo visualizar el estado de los trabajos completados en el Portal de Azure clásico?**

Puede ver el estado de los trabajos completados durante 90 días. Los trabajos completados se eliminarán una vez transcurrido ese plazo.

**Si deseo importar o exportar más de 10 unidades, ¿qué debo hacer?**

Un trabajo de importación o exportación solo puede hacer referencia a 10 unidades en un único trabajo para el servicio de Importación/Exportación. Si desea enviar más de 10 unidades, puede crear varios trabajos. Las unidades asociadas al mismo trabajo se deben enviar juntas en el mismo paquete.

**¿Puedo usar un adaptador USB SATA que no esté en la lista de adaptadores recomendados?**

Si su convertidor no aparece en la lista anterior, puede probar a ejecutar la herramienta de importación y exportación de Azure para preparar la unidad y comprobar si funciona antes de adquirir un convertidor compatible.

**¿El servicio formatea las unidades antes de devolverlas?**

No. Todas las unidades están cifradas con BitLocker.

**¿Puedo adquirir unidades de Microsoft para los trabajos de importación y exportación?**

No. Debe enviar sus propias unidades tanto para los trabajos de importación como para los de exportación.

**Cuando finalice el trabajo de importación, ¿cómo se mostrarán los datos en la cuenta de almacenamiento? ¿Se conservará la jerarquía de directorios?**

Al preparar una unidad de disco duro para un trabajo de importación, el destino se especifica mediante el parámetro llamado "/dstdir:". Este es el contenedor de destino de la cuenta de almacenamiento donde se copian los datos de la unidad de disco duro. Dentro de él se crean directorios virtuales para las carpetas de la unidad de disco duro y blobs para los archivos.

**Si la unidad tiene archivos que ya existen en la cuenta de almacenamiento, ¿el servicio sobrescribirá los blobs existentes en dicha cuenta?**

Al preparar la unidad, puede especificar que los archivos de destino se sobrescriban u omitan mediante el parámetro llamado "/Disposition:<rename|no-overwrite|overwrite>". De forma predeterminada, el servicio cambiará el nombre de los nuevos archivos en lugar de sobrescribir los blobs existentes.

**¿Es compatible la herramienta de Importación/Exportación de Azure con sistemas operativos de 32 bits?** No. La herramienta de cliente solo es compatible con sistemas operativos Windows de 64 bits. Consulte la sección sobre sistemas operativos en los [requisitos previos](#pre-requisites) para ver una lista completa de las versiones de SO admitidas.

**¿Debo incluir alguna otra cosa, aparte de la unidad de disco duro, en el paquete?**

Envíe solo las unidades de disco duro. No incluya elementos como cables de alimentación o cables USB.

**¿Es necesario enviar mis unidades con FedEx o DHL?**

Puede enviar las unidades al centro de datos con cualquier empresa de transporte conocida, como FedEx, DHL o UPS, o bien mediante el servicio postal de los Estados Unidos. Sin embargo, para devolver las unidades enviadas desde el centro de datos, debe indicar un número de cuenta de FedEx en Estados Unidos y Europa, o bien un número de cuenta de DHL en las regiones de Asia y Australia.

**¿Existe alguna restricción internacional en el envío de las unidades?**

Tenga en cuenta que es posible que los medios físicos que está enviando deban cruzar alguna frontera internacional. Usted es el responsable de asegurar que los medios y datos físicos se importan o exportan de acuerdo con todas las normativas aplicables. Antes de enviar los medios físicos, pida asesoramiento para comprobar que los medios y datos se pueden enviar legalmente al centro de datos identificado. De este modo, se asegurará de que llegan a Microsoft de manera puntual.

**Al crear un trabajo, la dirección de envío es una ubicación distinta de la ubicación de la cuenta de almacenamiento. ¿qué debo hacer?**

Algunas ubicaciones de la cuenta de almacenamiento se asignan a ubicaciones de envío alternativas. Ubicaciones de envío anteriormente disponibles se pueden asignar también temporalmente a ubicaciones alternativas. Compruebe siempre la dirección de envío proporcionada durante la creación del trabajo antes de enviar las unidades.

**¿Por qué el estado del trabajo en el Portal de Azure clásico indica Enviando cuando el sitio web de la empresa de transporte muestra que el paquete está entregado?**

El estado del Portal de Azure clásico cambia de Enviando a Transfiriendo cuando se inicia el procesamiento de la unidad. Si la unidad ha llegado a las instalaciones, pero no se ha iniciado el procesamiento, se mostrará el estado de trabajo Enviando.

**Al enviar la unidad, la empresa de transporte le pedirá el nombre de contacto y el número de teléfono del centro de datos. ¿Qué debo proporcionar?**

El número de teléfono se le proporciona durante la creación del trabajo. Si necesita un nombre de contacto, póngase en contacto con nosotros en waimportexport@microsoft.com y le facilitaremos esa información.

**¿Puedo usar el servicio Importación/Exportación de Azure para copiar buzones PST y datos de SharePoint en Office 365?**

Consulte [Importar archivos PST o datos de SharePoint a Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**¿Puedo usar el servicio Importación/Exportación de Azure para copiar las copias de seguridad sin conexión en el servicio Copia de seguridad de Azure?**

Consulte [Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](../backup/backup-azure-backup-import-export.md).

## Consulte también:
* [Configurar la herramienta de importación/exportación de Azure](https://msdn.microsoft.com/library/dn529112.aspx)
* [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md)
* [Ejemplo de API de REST de Importación/Exportación de Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

<!---HONumber=AcomDC_0921_2016-->