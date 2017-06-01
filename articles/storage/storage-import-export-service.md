---
title: Uso de Azure Import/Export para transferir datos al almacenamiento de blobs y desde este | Microsoft Docs
description: "Aprenda a crear trabajos de importación y exportación en Azure Portal para transferir datos al almacenamiento de blobs y desde este."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 668f53f2-f5a4-48b5-9369-88ec5ea05eb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: muralikk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 1aebecdaacd3525bec07a9359e52d2bc3d1539de
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Uso del servicio Microsoft Azure Import/Export para transferir datos al almacenamiento de blobs
El servicio Azure Import/Export le permite transferir de forma segura grandes cantidades de datos a Azure Blob Storage mediante el envío de unidades de disco duro a un centro de datos de Azure. También puede usar este servicio para transferir datos desde el Almacenamiento de blobs de Azure hasta las unidades de disco duro y enviarlas al sitio local. Este servicio resulta adecuado para aquellas situaciones en la que quiere transferir varios terabytes (TB) de datos a Azure o desde esta plataforma, pero la carga o descarga a través de la red no es factible debido al ancho de banda limitado o a los costos elevados de la red.

El servicio requiere que las unidades de disco duro estén cifradas con BitLocker para la seguridad de los datos. El servicio admite cuentas de almacenamiento clásicas y de Azure Resource Manager (nivel de acceso esporádico y estándar) presentes en todas las regiones de Azure público. Las unidades de disco duro se deben enviar a una de las ubicaciones especificadas más adelante en este artículo.

En este artículo obtendrá más información sobre el servicio Azure Import/Export y cómo enviar las unidades para copiar datos con Azure Blob Storage como origen y destino.

## <a name="when-should-i-use-the-azure-importexport-service"></a>¿Cuándo debo utilizar el servicio Importación/Exportación de Azure?
Considere el uso del servicio de Azure Import/Export cuando la carga o descarga de datos a través de la red sea demasiado lenta o el costo de ancho de banda de red adicional resulte prohibitivo.

Puede utilizar este servicio en los siguientes escenarios:

* Migración de datos a la nube: mueva rápidamente grandes cantidades de datos a Azure de manera rápida y rentable.
* Distribución de contenido: envíe rápidamente datos a los sitios de cliente.
* Copia de seguridad: realice copias de seguridad de los datos locales para almacenarlos en el Almacenamiento de blobs de Azure.
* Recuperación de datos: recupere una gran cantidad de datos almacenados en el almacenamiento de blobs y recíbalos en su ubicación local.

## <a name="prerequisites"></a>Requisitos previos
En esta sección, se enumeran los requisitos previos necesarios para utilizar este servicio. Revíselos detenidamente antes de enviar sus unidades.

### <a name="storage-account"></a>Cuenta de almacenamiento
Debe disponer de una suscripción de Azure existente y una o varias cuentas de almacenamiento para utilizar el servicio Import/Export. Puede utilizar cada trabajo para transferir datos desde o hacia una sola cuenta de almacenamiento. Dicho de otra forma, un trabajo de importación y exportación no puede abarcar varias cuentas de almacenamiento. Para obtener información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Tipos de blobs
Puede usar el servicio Azure Import/Export para copiar datos de blobs **en bloque** o **en páginas**. Y a la inversa, solo puede exportar blobs **en bloque**, **en páginas** o **en anexos** de Azure Storage mediante este servicio.

### <a name="job"></a>Trabajo
Para comenzar el proceso de importación o exportación desde el almacenamiento en blobs, cree primero un trabajo. Un trabajo puede ser un trabajo de importación o un trabajo de exportación:

* Cree un trabajo de importación si desea transferir datos que tiene en su instalación local a blobs en su cuenta de almacenamiento de Azure.
* Cree un trabajo de exportación si desea transferir datos que actualmente están almacenados en blobs de su cuenta de almacenamiento a discos duros que se le enviarán posteriormente.

Cuando crea un trabajo, notifica al servicio de importación y exportación que enviará uno o varios discos duros a un centro de datos de Azure.

* En el caso de un trabajo de importación, enviará las unidades de disco duro que contengan los datos.
* Si es un trabajo de exportación, enviará las unidades de disco duro vacías.
* Puede enviar hasta 10 unidades de disco duro por trabajo.

Para crear un trabajo de importación o exportación, puede usar Azure Portal o la [API de REST de Import/Export de Azure Storage](/rest/api/storageimportexport).

### <a name="waimportexport-tool"></a>Herramienta WAImportExport
El primer paso para crear un trabajo de **importación** consiste en preparar las unidades que se enviarán para esta operación. Para preparar las unidades, debe conectarlas a un servidor local y ejecutar la herramienta WAImportExport en dicho servidor. Esta herramienta facilita la copia de los datos en la unidad, el cifrado de los datos en la unidad con BitLocker y la generación de los archivos de diario de unidad.

Los archivos del diario almacenan información básica sobre el trabajo y la unidad, como el número de serie de la unidad o el nombre de la cuenta de almacenamiento. Este archivo de diario no se almacena en la unidad. Se utiliza durante la creación del trabajo de importación. Más adelante en este artículo se proporcionan pasos detallados sobre la creación del trabajo.

La herramienta WAImportExport solo es compatible con el sistema operativo de Windows de 64 bits. Consulte la sección [Sistema operativo](#operating-system) para ver cuáles son las versiones específicas admitidas.

Descargue la versión más reciente de [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip). Para más información acerca del uso de la herramienta WAImportExport, consulte [WAImportExport Tool Reference](storage-import-export-tool-how-to.md) (Referencia acerca de la herramienta WAImportExport).

>[!NOTE]
>**Versión anterior:** puede [descargar la versión WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) de la herramienta y consultar la [guía de uso de WAImportExpot V1](storage-import-export-tool-how-to-v1.md). La versión WAImportExpot V1 de la herramienta proporciona compatibilidad para **preparar discos cuando hay datos escritos previamente en el disco**. También deberá usar la herramienta WAImportExpot V1 si la única clave disponible es una clave SAS.

>

### <a name="hard-disk-drives"></a>Unidades de disco duro
El servicio Import/Export solo admite SSD de 2,5 pulgadas o HDD internos SATA II o III de 2,5 o 3,5 pulgadas. Un único trabajo de importación/exportacón puede tener un máximo de 10 unidades HDD/SSD, y cada una de ellas puede tener cualquier tamaño. Un volumen grande de unidades puede distribuirse entre varios trabajos, y no hay ningún límite en el número de trabajos que se pueden crear. 

Para los trabajos de importación, solo se procesará el primer volumen de datos de la unidad. El volumen de datos debe tener formato NTFS.

> [!IMPORTANT]
> Este servicio no admite unidades de disco duro externas que incorporen un adaptador USB integrado. Además, no se puede usar el disco que hay en el interior de la carcasa de las unidades de disco duro externo, así que le rogamos que no envíe este tipo de unidad.
> 
> 

### <a name="encryption"></a>Cifrado
Los datos de la unidad deben estar cifrados con BitLocker Drive Encryption. Así estarán protegidos mientras se encuentren en tránsito.

En los trabajos de importación, hay dos maneras de realizar el cifrado. La primera manera consiste en especificar la opción al usar el archivo CSV de conjunto de datos mientras se ejecuta la herramienta WAImportExport durante la preparación de la unidad. La segunda, en habilitar el cifrado de BitLocker manualmente en la unidad y especificar la clave de cifrado en el archivo CSV de conjunto de unidades al ejecutar la línea de comandos de la herramienta WAImportExport durante la preparación de la unidad.

En los trabajos de exportación, después de copiar los datos en las unidades, el servicio cifrará la unidad con BitLocker antes de enviársela de vuelta al usuario. La clave de cifrado se proporciona a través de Azure Portal.  

### <a name="operating-system"></a>Sistema operativo
Puede utilizar uno de los siguientes sistemas operativos de 64 bits para preparar la unidad de disco duro mediante la herramienta WAImportExport antes de enviarla a Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. Todos estos sistemas operativos admiten el cifrado de unidad BitLocker.

### <a name="locations"></a>Ubicaciones
El servicio Importación/Exportación de Azure permite copiar datos en todas las cuentas de almacenamiento de Azure público como origen y destino. Las unidades de disco duro se pueden enviar a una de las siguientes ubicaciones. Si la cuenta de almacenamiento se encuentra en una ubicación de Azure pública que no se especifica aquí, se proporcionará una ubicación de envío alternativa al crear el trabajo mediante Azure Portal o la API de REST de Import/Export.

Ubicaciones de envío admitidas:

* Este de EE. UU.
* Oeste de EE. UU.
* Este de EE. UU. 2
* Oeste de EE. UU. 2
* Central EE. UU.:
* Centro-Norte de EE. UU
* Centro-Sur de EE. UU
* Centro occidental de EE.UU.
* Europa del Norte
* Europa occidental
* Asia oriental
* Sudeste asiático
* Australia Oriental
* Sudeste de Australia
* Oeste de Japón
* Este de Japón
* India Central
* Sur de la India
* Oeste de la India
* Centro de Canadá
* Este de Canadá
* Sur de Brasil
* Corea Central
* Gobierno de EE. UU. - Virginia
* Gobierno de EE. UU. - Iowa
* Departamento de Defensa de EE. UU. Este
* Departamento de Defensa de EE. UU. Centro
* Este de China
* Norte de China
* Sur del Reino Unido 2

### <a name="shipping"></a>Envío
**Envío de unidades al centro de datos:**

Al crear un trabajo de importación o exportación, se le proporcionará una dirección de envío de una de las ubicaciones admitidas para el envío de sus unidades. La dirección de envío proporcionada dependerá de la ubicación de la cuenta de almacenamiento, pero no puede ser igual que la de esta.

Puede utilizar las empresas de transporte FedEx, DHL, UPS o el servicio postal de los Estados Unidos para enviar las unidades.

**Envío de unidades desde el centro de datos:**

Al crear un trabajo de importación o exportación, debe proporcionar a Microsoft un remite para que le devuelva las unidades enviadas cuando finalice su trabajo. Asegúrese de proporcionar una dirección de retorno válida para evitar retrasos en el procesamiento.

Puede elegir el transportista que desee para enviar la unidad de disco duro. El transportista debe contar con un sistema de seguimiento adecuado a fin de mantener la cadena de custodia. También debe proporcionar un número de cuenta válido de las empresas de transporte FedEx o DHL para que Microsoft realice la devolución. En el caso de FedEx, se requiere un número de cuenta para devolver las unidades enviadas desde Estados Unidos y Europa. En DHL, se requiere un número de cuenta para devolver las unidades enviadas desde Asia y Australia. Puede crear una cuenta de [FedEx](http://www.fedex.com/us/oadr/) (para EE. UU. y Europa) o [DHL](http://www.dhl.com/) (Asia y Australia) si no tiene una. Si ya tiene un número de cuenta de uno de estos transportistas, compruebe que sea válido.

Al enviar los paquetes, debe seguir los términos establecidos en los [Términos de servicio de Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Tenga en cuenta que es posible que los medios físicos que está enviando deban cruzar alguna frontera internacional. Usted es el responsable de asegurar que los medios y datos físicos se importan o exportan de acuerdo con todas las normativas aplicables. Antes de enviar los medios físicos, pida asesoramiento para comprobar que los medios y datos se pueden enviar legalmente al centro de datos identificado. De este modo, se asegurará de que llegan a Microsoft de manera puntual. Por ejemplo, cualquier paquete que cruce fronteras internacionales necesita que una factura comercial acompañe al paquete (excepto si cruza fronteras dentro de la Unión Europea). Puede imprimir una copia cumplimentada de la factura comercial desde el sitio web de la compañía. Algunos ejemplos de factura comercial son la [factura comercial de DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) y la [factura comercial de FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Asegúrese de que Microsoft no se ha indicado como el exportador.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>¿Cómo funciona el servicio de importación y exportación de Azure?
Se pueden transferir datos entre el sitio local y el Almacenamiento de blobs de Azure mediante el servicio Importación/Exportación de Azure; para ello, se crean trabajos y se envían unidades de disco duro a un centro de datos de Azure. Cada unidad de disco duro que se envía está asociada a un único trabajo. Cada trabajo está asociado a una única cuenta de almacenamiento. Revise atentamente la [sección de requisitos previos](#pre-requisites) para conocer las características específicas de este servicio, como los tipos de blobs, los tipos de discos, las ubicaciones y las formas de envío que se admiten.

En esta sección, se realiza una descripción de alto nivel de los pasos que intervienen en los trabajos de importación y exportación. Más adelante, en la sección [Inicio rápido](#quick-start), se proporcionan instrucciones paso a paso para crear un trabajo de importación y exportación.

### <a name="inside-an-import-job"></a>Dentro de un trabajo de importación
En un nivel alto, un trabajo de importación implica los siguientes pasos:

* Determine los datos que desea importar y el número de unidades que necesitará.
* Identifique la ubicación de los blobs de destino para los datos en Blob Storage.
* Utilice la herramienta WAImportExport para copiar los datos en una o varias unidades de disco duro y cífrelas con BitLocker.
* Cree un trabajo de importación en la cuenta de almacenamiento de destino mediante Azure Portal o la API de REST de Import/Export. Si utiliza Azure Portal, cargue los archivos del diario de unidad.
* Indique el remite y el número de cuenta de la empresa de transporte que se utilizará para que se le devuelvan las unidades.
* Envíe las unidades de disco duro a la dirección de envío proporcionada durante la creación del trabajo.
* Actualice el número de seguimiento de entrega en los datos del trabajo de importación y envíe dicho trabajo.
* Las unidades se reciben y procesan en el centro de datos de Azure.
* Las unidades se devuelven usando la cuenta de la empresa de transporte al remite indicado en el trabajo de importación.
  
    ![Figura 1: Importación de flujos de trabajo](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Dentro de un trabajo de exportación
En un nivel alto, un trabajo de exportación implica los siguientes pasos:

* Determine los datos que desea exportar y el número de unidades que necesitará.
* Identifique los blobs de origen o las rutas de acceso al contenedor de los datos en el Almacenamiento de blobs.
* Cree un trabajo de exportación en la cuenta de almacenamiento de origen mediante Azure Portal o la API de REST de Import/Export.
* Especifique los blobs de origen o las rutas de acceso al contenedor de los datos en el trabajo de exportación.
* Indique el remite y el número de cuenta de la empresa de transporte que se utilizará para que se le devuelvan las unidades.
* Envíe las unidades de disco duro a la dirección de envío proporcionada durante la creación del trabajo.
* Actualice el número de seguimiento de entrega en los datos del trabajo de exportación y envíe dicho trabajo.
* Las unidades se reciben y procesan en el centro de datos de Azure.
* Las unidades se cifran con BitLocker y las claves están disponibles a través de Azure Portal.  
* Las unidades se devuelven usando la cuenta de la empresa de transporte al remite indicado en el trabajo de importación.
  
    ![Figura 2: Exportación de flujos de trabajo](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Visualización del estado del trabajo y de la unidad
Puede realizar el seguimiento del estado de sus trabajos de importación y exportación desde Azure Portal. Haga clic en la pestaña **Import/Export**. En la página aparecerá una lista con sus trabajos.

![Visualización del estado del trabajo](./media/storage-import-export-service/jobstate.png)

Verá uno de los siguientes estados de trabajo en función de en qué fase del proceso esté la unidad.

| Estado del trabajo | Descripción |
|:--- |:--- |
| Creating | Cuando se crea un trabajo, su estado se establece en Creating. Mientras el trabajo se encuentra en el estado Creating, el servicio Import/Export asume que las unidades no se han enviado al centro de datos. Un trabajo puede permanecer en el estado Creating durante dos semanas, después de lo cual el servicio lo eliminará automáticamente. |
| Envío | Después de enviar el paquete, debe actualizar la información de seguimiento en Azure Portal.  De esta forma, el estado del trabajo cambiará a "Shipping". El trabajo permanecerá en el estado Shipping durante un máximo de dos semanas. 
| Received | Una vez recibidas todas las unidades en el centro de datos, el estado del trabajo se establecerá en Received. |
| Transferring | Cuando se haya iniciado el procesamiento de al menos una unidad, el estado del trabajo se establecerá en Transferring. Consulte la sección Estados de unidad anterior para obtener información detallada. |
| Packaging | Cuando todas las unidades hayan terminado el procesamiento, el trabajo pasará al estado Packaging hasta que se le devuelvan las unidades. |
| Completed | Cuando todas las unidades se hayan devuelto al cliente, si el trabajo se ha completado sin errores, se establecerá en el estado Completed. El trabajo se eliminará automáticamente después de 90 días en el estado Completed. |
| Closed | Cuando todas las unidades se hayan devuelto al cliente, si se ha producido algún error durante el procesamiento del trabajo, este se establecerá en el estado Closed. El trabajo se eliminará automáticamente después de 90 días en el estado Closed. |

En la tabla siguiente se describe el ciclo de vida de una unidad individual durante su transición a través de un trabajo de importación o exportación. El estado actual de cada unidad de un trabajo se puede ver ahora en Azure Portal.
En la tabla siguiente se describe cada estado por el que puede pasar una unidad de un trabajo.

| Estado de la unidad | Descripción |
|:--- |:--- |
| Specified | Para un trabajo de importación, cuando se crea el trabajo desde Azure Portal, el estado inicial de una unidad es Specified. Para un trabajo de exportación, ya que no se especifica ninguna unidad cuando se crea el trabajo, el estado inicial de la unidad es Received. |
| Received | La unidad cambia al estado Received cuando el operador del servicio Import/Export ha procesado las unidades recibidas del transportista para un trabajo de importación. Para un trabajo de exportación, el estado inicial de la unidad es Received. |
| NeverReceived | La unidad pasará al estado NeverReceived si llega un paquete para un trabajo que no contiene la unidad. Una unidad también puede cambiar a este estado si han transcurrido dos semanas desde que el servicio recibió la información de envío pero el paquete aún no ha llegado al centro de datos. |
| Transferring | Una unidad pasará al estado Transferring cuando el servicio empiece a transferir datos de la unidad a Windows Azure Storage. |
| Completed | Una unidad pasará al estado Completed cuando el servicio haya transferido correctamente todos los datos sin errores.
| CompletedMoreInfo | Una unidad de disco pasará al estado CompletedMoreInfo cuando el servicio encuentre algún problema mientras copia datos en la unidad o desde ella. La información puede incluir errores, advertencias o mensajes informativos por sobrescribir blobs.
| ShippedBack | La unidad de disco pasará al estado ShippedBack cuando se haya enviado desde el centro de datos a la dirección de devolución. |

Esta imagen de Azure Portal muestra el estado de la unidad de un trabajo de ejemplo:

![Visualización de estado de la unidad](./media/storage-import-export-service/drivestate.png)

En la tabla siguiente se describen los estados de error de la unidad y las medidas tomadas para cada estado.

| Estado de la unidad | Evento | Resolución y paso siguiente |
|:--- |:--- |:--- |
| NeverReceived | Una unidad que está marcada como NeverReceived (porque no se recibió como parte del envío del trabajo) llega en otro envío. | El equipo de operaciones cambiará la unidad al estado Received. |
| N/D | Una unidad que no forma parte de ningún trabajo llega al centro de datos como parte de otro trabajo. | La unidad se marcará como unidad adicional y se devolverá al cliente cuando se complete el trabajo asociado al paquete original. |

### <a name="time-to-process-job"></a>Tiempo para procesar el trabajo
El tiempo que se tarda en procesar un trabajo de importación o exportación varía en función de determinados factores, como el tiempo de envío, el tipo de trabajo, el tipo y el tamaño de los datos copiados o el tamaño de los discos proporcionados. El servicio Import/Export no tiene un Acuerdo de Nivel de Servicio (SLA). Puede utilizar la API de REST para realizar un seguimiento más estrecho del progreso del trabajo. Hay un parámetro de porcentaje completado en la operación de lista de trabajos que proporciona una indicación del progreso de la copia. Si necesita una estimación para realizar un trabajo de importación o exportación en el que el tiempo es un factor crítico, póngase en contacto con nosotros.

### <a name="pricing"></a>Precios
**Cuota de manipulación de unidades**

Existe una cuota de manipulación por cada unidad procesada como parte del trabajo de importación o exportación. Consulte los detalles en [Precios de importación/exportación](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Gastos de envío**

Al enviar unidades a Azure, pagar los gastos de envío al transportista. Cuando Azure le devuelve la unidad, los gastos de envío se le cargan en la cuenta de la empresa de transporte que indicó al crear el trabajo.

**Costos de transacción**

La importación de datos en el Almacenamiento de blobs no tiene ningún costo de transacción asociado. Los cargos de salida estándar son aplicables cuando se exportan datos desde el Almacenamiento de blobs. Para más información sobre los costos de transacción, consulte [Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Inicio rápido
En esta sección se proporcionan instrucciones paso a paso para crear trabajos de importación y exportación. Asegúrese de cumplir todos los [requisitos previos](#pre-requisites) antes de continuar.

## <a name="create-an-import-job"></a>Crear un trabajo de importación
Cree un trabajo de importación para copiar datos en la cuenta de almacenamiento de Azure enviando una o varias unidades de disco duro con estos datos al centro de datos especificado. En el trabajo de importación se comunican detalles sobre las unidades de disco duro, los datos que se copian, la cuenta de almacenamiento de destino y la información de envío al servicio Importación/Exportación de Azure. El proceso de creación de un trabajo de importación consta de tres pasos. En primer lugar, prepare las unidades con la herramienta WAImportExport. En segundo lugar, envíe un trabajo de importación a través de Azure Portal. En tercer lugar, envíe las unidades a la dirección de envío proporcionada durante la creación del trabajo y actualice la información de envío en los detalles del trabajo.   

> [!IMPORTANT]
> Puede enviar un único trabajo por cuenta de almacenamiento. Cada unidad que envíe se puede importar a una cuenta de almacenamiento. Por ejemplo, supongamos que desea importar datos en dos cuentas de almacenamiento. Debe utilizar unidades de disco duro independientes para cada cuenta de almacenamiento y crear trabajos independientes por cada cuenta de almacenamiento.
> 
> 

### <a name="prepare-your-drives"></a>Preparación de las unidades
El primer paso al importar datos mediante el servicio Azure Import/Export consiste en preparar las unidades mediante la herramienta WAImportExport. Siga estos pasos para preparar las unidades.

1. Identifique los datos que se van a importar. Podrían ser directorios o archivos independientes situados en el servidor local o un recurso compartido de red.  
2. Determine el número de unidades que necesitará según el tamaño total de los datos. Proporcione el número necesario de unidades de disco duro SATA II o III de 2,5 o 3,5 pulgadas o SSD de 2,5 pulgadas.
3. Identifique la cuenta de almacenamiento, el contenedor, los directores virtuales y los blobs de destino.
4.    Determine los directorios o archivos independientes que se copiarán en cada unidad de disco duro.
5.    Cree los archivos CSV para el conjunto de datos y el conjunto de unidades.
    
    **Archivo CSV de conjunto de datos**
    
    A continuación se muestra un ejemplo de archivo CSV de conjunto de datos:
    
    ```
    BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
    "F:\50M_original\","containername/",BlockBlob,rename,"None",None 
    ```
   
    En el ejemplo anterior, 100M_1.csv.txt se copiará en la raíz del contenedor denominado "containername". Si el nombre de contenedor "containername" no existe, se creará uno. Todos los archivos y carpetas bajo 50M_original se copiarán en containername de forma recursiva. Se mantendrá la estructura de carpetas.

    Obtenga más información sobre cómo [preparar el archivo CSV de conjunto de datos](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    
    **Recordatorio**: De forma predeterminada, los datos se importan como blobs en bloques. Puede utilizar el parámetro el valor de campo BlobType para importar los datos como blobs en páginas. Por ejemplo, si va a importar archivos VHD que se van a montar como discos en una máquina virtual de Azure, debe importarlos como blobs en páginas.

    **Archivo CSV de conjunto de unidades**

    El valor de la marca del conjunto de unidades es un archivo CSV que contiene la lista de discos a los que se asignan las letras de las unidades para que la herramienta pueda elegir correctamente la lista de discos que se van a preparar. 

    A continuación se muestra un ejemplo de archivo CSV de conjunto de unidades:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    En el ejemplo anterior, se supone que hay dos discos conectados y que se han creado volúmenes NTFS básicos con las letras de volumen G:\ y H:\. La herramienta formateará y cifrará el disco que hospeda H:\ y no formateará ni cifrará el disco que hospeda el volumen G:\.

    Obtenga más información sobre cómo [preparar el archivo CSV de conjunto de unidades](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.    Utilice la herramienta [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) para copiar sus datos a uno o varios discos duros.
7.    Puede especificar "Encrypt" en el campo de cifrado en el archivo CSV de conjunto de unidades para habilitar el cifrado de BitLocker en la unidad de disco duro. Como alternativa, también puede habilitar el cifrado de BitLocker manualmente en la unidad de disco duro, especificar "AlreadyEncrypted" y proporcionar la clave en el archivo CSV de conjunto de unidades mientras se ejecuta la herramienta.

8. No modifique los datos de las unidades de disco duro o el archivo de diario después de completar la preparación del disco.

> [!IMPORTANT]
> Cada unidad de disco duro que prepare generará un archivo del diario. Al crear el trabajo de importación mediante Azure Portal, tendrá que cargar todos los archivos del diario de las unidades que formen parte de dicho trabajo de importación. Las unidades sin archivos de diario no se procesarán.
> 
>

A continuación, se muestran los comandos para preparar la unidad de disco duro mediante la herramienta WAImportExport, junto con algunos ejemplos.

Comando PrepImport de la herramienta WAImportExport para la primera sesión de copia para copiar directorios o archivos con una nueva sesión de copia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Ejemplo 1 de importación**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Para poder **añadir más unidades**, es posible crear un nuevo archivo de conjunto de unidades y ejecutar el comando como se indica a continuación. Para sesiones de copia posteriores en unidades de disco distintas a las especificadas en el archivo .csv InitialDriveset, especifique un nuevo archivo CSV de conjunto de unidades y proporciónelo como valor para el parámetro "AdditionalDriveSet". Use el **mismo nombre de archivo de diario** y proporcione un **nuevo identificador de sesión**. El formato del archivo CSV AdditionalDriveset es el mismo que el formato de InitialDriveSet.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Ejemplo 2 de importación**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Para agregar datos adicionales al mismo conjunto de unidades, se puede llamar al comando PrepImport de la herramienta WAImportExport para que las sesiones de copia posteriores copien archivos o directorios adicionales: para sesiones de copia posteriores en las mismas unidades de disco duro que las especificadas en el archivo .csv InitialDriveset, especifique el **mismo nombre de archivo de diario** y proporcione un **nuevo identificador de sesión**; no es necesario proporcionar la clave de cuenta de almacenamiento.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Ejemplo 3 de importación**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Para más información sobre el uso de la herramienta WAImportExport, consulte [Preparación de unidades de disco duro para un trabajo de importación](storage-import-export-tool-preparing-hard-drives-import.md).

Además, consulte [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) si desea obtener instrucciones paso a paso más detalladas.  

### <a name="create-the-import-job"></a>Creación del trabajo de importación
1. Una vez preparada la unidad, diríjase a su cuenta de almacenamiento en el Azure Portal y visualice el panel. En **Vista rápida**, haga clic en **Crear un trabajo de importación**. Revise los pasos y active la casilla para indicar que ha preparado su unidad y que tiene el archivo de diario de unidad disponible.
2. En el paso 1, proporcione la información de contacto de la persona responsable de este trabajo de importación y una dirección de retorno válida. Si desea guardar datos de registro detallados del trabajo de importación, active la opción **Guardar el registro detallado en mi contenedor de blobs 'waimportexport'**.
3. En el paso 2, cargue los archivos de diario de unidad que haya obtenido durante el paso de preparación de la unidad. Tendrá que cargar un archivo por cada unidad que haya preparado.
   
   ![Creación del trabajo de importación - Paso 3](./media/storage-import-export-service/import-job-03.png)
4. En el paso 3, escriba un nombre descriptivo para el trabajo de importación. Tenga en cuenta que el nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. El nombre elegido le servirá para realizar un seguimiento de sus trabajos mientras están en progreso y una vez se hayan completado.
   
   A continuación, seleccione en la lista la región de su centro de datos. La región del centro de datos indicará el centro de datos y la dirección donde debe enviar el paquete. Para obtener más información, consulte las P+F que se muestran a continuación.
5. En el paso 4, seleccione en la lista la empresa de transporte que realizará la devolución y, después, escriba el número de cuenta de dicha empresa. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación.
   
   Si tiene un número de seguimiento, seleccione en la lista la empresa de transporte que realizará la entrega y, después, escriba el número de seguimiento.
   
   Si todavía no tiene un número de seguimiento, elija **Facilitaré mi información de envío para este trabajo de importación cuando haya enviado mi paquete**y, a continuación, finalice el proceso de importación.
6. Para escribir el número de seguimiento después de haber enviado el paquete, vuelva a la página **Import/Export** de su cuenta de almacenamiento en Azure Portal, seleccione el trabajo de la lista y elija **Shipping Info** (Información de envío). Navegue por el asistente y escriba el número de seguimiento en el paso 2.
   
    Si el número de seguimiento no se actualiza dentro del plazo de 2 semanas desde la creación del trabajo, este expirará.
   
    Si el estado se encuentra en estado de creación, envío o transferencia, también puede actualizar el número de cuenta del transportista en el paso 2 del asistente. Una vez que el trabajo se encuentre en estado de empaquetado, no podrá actualizar el número de cuenta del transportista de ese trabajo.
7. Puede realizar un seguimiento del progreso del trabajo en el panel del portal. Consulte en [Visualización del estado del trabajo](#viewing-your-job-status)lo que significa cada estado de trabajo de la sección anterior.

## <a name="create-an-export-job"></a>Crear un trabajo de exportación
Cree un trabajo de exportación para notificar al servicio Importación/Exportación que va a enviar al centro de datos una o varias unidades vacías para que se exporten los datos de la cuenta de almacenamiento a las unidades y recibirlas posteriormente.

### <a name="prepare-your-drives"></a>Preparación de las unidades
Para preparar las unidades para el trabajo de exportación, se recomienda realizar las siguientes comprobaciones previas:

1. Compruebe el número de discos necesarios mediante el comando PreviewExport de la herramienta WAImportExport. Para más información, consulte [Vista previa de uso de disco para un trabajo de exportación](https://msdn.microsoft.com/library/azure/dn722414.aspx). Esto le ayuda a obtener una vista previa del uso del disco para los blobs que ha seleccionado, en función del tamaño de las unidades que se va a usar.
2. Compruebe que puede leer o escribir en la unidad de disco duro que se va a enviar para el trabajo de exportación.

### <a name="create-the-export-job"></a>Creación del trabajo de exportación
1. Para crear un trabajo de exportación, diríjase a su cuenta de almacenamiento en Azure Portal y acceda al panel. En **Vista rápida**, haga clic en **Crear un trabajo de exportación** y siga los pasos del asistente.
2. En el paso 2, proporcione la información de contacto de la persona responsable de este trabajo de exportación. Si desea guardar datos de registro detallados del trabajo de exportación, active la opción **Guardar el registro detallado en mi contenedor de blobs 'waimportexport'**.
3. En el paso 3, especifique los datos de blobs que desea exportar desde su cuenta de almacenamiento a una o varias unidades vacías. Puede elegir exportar todos los datos de blobs de la cuenta de almacenamiento o especificar los blobs o conjuntos de blobs que se van a exportar.
   
   Para especificar un blob que desea exportar, utilice el selector **Igual a** y especifique la ruta relativa al blob, empezando por el nombre del contenedor. Utilice *$root* para especificar el contenedor raíz.
   
   Para especificar todos los blobs que empiecen por un prefijo, utilice el selector **Empieza por** y especifique el prefijo, empezando por una barra diagonal "/". El prefijo puede ser el prefijo del nombre del contenedor, el nombre del contenedor completo o el nombre del contendor completo seguido del prefijo del nombre del blob.
   
   En la siguiente tabla se muestran ejemplos de rutas de acceso del blob válidas:
   
   | Selector | Ruta del blob | Description |
   | --- | --- | --- |
   | Empieza por |/ |Exporta todos los blobs de la cuenta de almacenamiento. |
   | Empieza por |/$root/ |Exporta todos los blobs del contenedor raíz. |
   | Empieza por |/book |Exporta todos los blobs de cualquier contenedor que empiecen por el prefijo **book** |
   | Empieza por |/music/ |Exporta todos los blobs del contenedor **music** |
   | Empieza por |/music/love |Exporta todos los blobs del contenedor **music** que empiecen por el prefijo **love**. |
   | Igual a |$root/logo.bmp |Exporta el blob **logo.bmp** del contenedor raíz. |
   | Igual a |videos/story.mp4 |Exporta el blob **story.mp4** del contenedor **videos** |
   
   Debe proporcionar las rutas de acceso del blob en formatos válidos para evitar errores durante el procesamiento, tal y como se muestra en esta captura de pantalla.
   
   ![Creación del trabajo de exportación - Paso 3](./media/storage-import-export-service/export-job-03.png)
4. En el paso 4, escriba un nombre descriptivo para el trabajo de exportación. El nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios.
   
   La región del centro de datos indicará el centro de datos al que debe enviar su paquete. Para obtener más información, consulte las P+F que se muestran a continuación.
5. En el paso 5, seleccione en la lista el transportista para la devolución y, a continuación, escriba el número de cuenta del transportista. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de exportación.
   
   Si tiene un número de seguimiento, seleccione en la lista la empresa de transporte que realizará la entrega y, después, escriba el número de seguimiento.
   
   Si todavía no tiene un número de seguimiento, elija **Proporcionaré la información de envío de este trabajo de exportación cuando haya enviado el paquete**y, a continuación, finalice el proceso de exportación.
6. Para escribir el número de seguimiento después de haber enviado el paquete, vuelva a la página **Import/Export** de su cuenta de almacenamiento en Azure Portal, seleccione el trabajo de la lista y elija **Shipping Info** (Información de envío). Navegue por el asistente y escriba el número de seguimiento en el paso 2.
   
    Si el número de seguimiento no se actualiza dentro del plazo de 2 semanas desde la creación del trabajo, este expirará.
   
    Si el estado se encuentra en estado de creación, envío o transferencia, también puede actualizar el número de cuenta del transportista en el paso 2 del asistente. Una vez que el trabajo se encuentre en estado de empaquetado, no podrá actualizar el número de cuenta del transportista de ese trabajo.
   
   > [!NOTE]
   > Si el blob que se va a exportar está en uso en el momento de la copia en la unidad de disco duro, el servicio de importación y exportación de Azure tomará una instantánea del blob y copiará la instantánea.
   > 
   > 
7. Puede realizar un seguimiento del progreso del trabajo en el panel de Azure Portal. Vea lo que significa cada estado del trabajo en la sección anterior sobre "Visualización del estado del trabajo".
8. Después de recibir las unidades con los datos exportados, puede ver y copiar las claves de BitLocker generadas por el servicio para la unidad. Diríjase a su cuenta de almacenamiento en Azure Portal y haga clic en la pestaña Import/Export. Seleccione su trabajo de exportación de la lista y haga clic en el botón Ver claves. Las claves de BitLocker aparecen como se muestra a continuación:
   
   ![Visualización de claves de BitLocker de un trabajo de exportación](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Vaya a la sección de P+F a continuación, en ella se tratan las preguntas más comunes que se encuentran los clientes al utilizar este servicio.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Puedo copiar archivos de Azure mediante el servicio Importación/Exportación de Azure?**

No, el servicio de importación y exportación de Azure solo admite blobs en bloques y blobs en páginas. El resto de los tipos de almacenamiento, incluidos Archivos, Tablas y Colas de Azure, no se admiten.

**¿Está disponible el servicio Importación/Exportación de Azure para suscripciones de CSP?**

No, el servicio Azure Import/Export no admite suscripciones a CSP.

**¿Puedo omitir el paso de preparación de las unidades en un trabajo de importación o puedo preparar una unidad sin realizar una copia?**

Todas las unidades que desee enviar para importar datos deben prepararse mediante la herramienta WAImportExport de Azure. Debe utilizar la herramienta WAImportExport para copiar datos en la unidad.

**¿Es necesario realizar alguna preparación en el disco al crear un trabajo de exportación?**

No, pero se recomiendan algunas comprobaciones previas. Compruebe el número de discos necesarios mediante el comando PreviewExport de la herramienta WAImportExport. Para más información, consulte [Vista previa de uso de disco para un trabajo de exportación](https://msdn.microsoft.com/library/azure/dn722414.aspx). Esto le ayuda a obtener una vista previa del uso del disco para los blobs que ha seleccionado, en función del tamaño de las unidades que se va a usar. Compruebe también que puede leer y escribir en la unidad de disco duro que se va a enviar para el trabajo de exportación.

**¿Qué sucede si envío por error un disco duro que no cumple con los requisitos de compatibilidad?**

El centro de datos de Azure le devolverá la unidad que no cumple con los requisitos de compatibilidad. Si solo algunos de los dispositivos del paquete cumplen con los requisitos de compatibilidad, esas unidades se procesarán, y las unidades que no cumplan los requisitos se le devolverán.

**¿Puedo cancelar un trabajo?**

Puede cancelar un trabajo si el estado es Creating o Shipping.

**¿Durante cuánto tiempo puedo visualizar el estado de los trabajos completados en Azure Portal?**

Puede ver el estado de los trabajos completados durante 90 días. Los trabajos completados se eliminarán una vez transcurrido ese plazo.

**Si deseo importar o exportar más de 10 unidades, ¿qué debo hacer?**

Un trabajo de importación o exportación solo puede hacer referencia a 10 unidades en un único trabajo para el servicio de Importación/Exportación. Si desea enviar más de 10 unidades, puede crear varios trabajos. Las unidades asociadas al mismo trabajo se deben enviar juntas en el mismo paquete.

**¿El servicio formatea las unidades antes de devolverlas?**

No. Todas las unidades están cifradas con BitLocker.

**¿Puedo adquirir unidades de Microsoft para los trabajos de importación y exportación?**

No. Debe enviar sus propias unidades tanto para los trabajos de importación como para los de exportación.

**Cuando finalice el trabajo de importación, ¿cómo se mostrarán los datos en la cuenta de almacenamiento? ¿Se conservará la jerarquía de directorios?**

Al preparar una unidad de disco duro para un trabajo de importación, el destino se especifica mediante el campo DstBlobPathOrPrefix del archivo CSV de conjunto de datos. Este es el contenedor de destino de la cuenta de almacenamiento donde se copian los datos de la unidad de disco duro. Dentro de él se crean directorios virtuales para las carpetas de la unidad de disco duro y blobs para los archivos.

**Si la unidad tiene archivos que ya existen en la cuenta de almacenamiento, ¿el servicio sobrescribirá los blobs existentes en dicha cuenta?**

Al preparar la unidad, puede especificar que los archivos de destino se sobrescriban u omitan mediante el campo del archivo CSV de conjunto de datos llamado Disposition:<rename|no-overwrite|overwrite>. De forma predeterminada, el servicio cambiará el nombre de los nuevos archivos en lugar de sobrescribir los blobs existentes.

**¿Es compatible la herramienta WAImportExport con sistemas operativos de 32 bits?**
No. La herramienta WAImportExport solo es compatible con sistemas operativos de Windows de 64 bits. Consulte la sección sobre sistemas operativos en los [requisitos previos](#pre-requisites) para ver una lista completa de las versiones de SO admitidas.

**¿Debo incluir alguna otra cosa, aparte de la unidad de disco duro, en el paquete?**

Envíe solo las unidades de disco duro. No incluya elementos como cables de alimentación o cables USB.

**¿Es necesario enviar mis unidades con FedEx o DHL?**

Puede enviar las unidades al centro de datos con cualquier empresa de transporte conocida, como FedEx, DHL o UPS, o bien mediante el servicio postal de los Estados Unidos. Sin embargo, para devolver las unidades enviadas desde el centro de datos, debe indicar un número de cuenta de FedEx en Estados Unidos y Europa, o bien un número de cuenta de DHL en las regiones de Asia y Australia.

**¿Existe alguna restricción internacional en el envío de las unidades?**

Tenga en cuenta que es posible que los medios físicos que está enviando deban cruzar alguna frontera internacional. Usted es el responsable de asegurar que los medios y datos físicos se importan o exportan de acuerdo con todas las normativas aplicables. Antes de enviar los medios físicos, pida asesoramiento para comprobar que los medios y datos se pueden enviar legalmente al centro de datos identificado. De este modo, se asegurará de que llegan a Microsoft de manera puntual.

**Al crear un trabajo, la dirección de envío es una ubicación distinta de la ubicación de la cuenta de almacenamiento. ¿qué debo hacer?**

Algunas ubicaciones de la cuenta de almacenamiento se asignan a ubicaciones de envío alternativas. Ubicaciones de envío anteriormente disponibles se pueden asignar también temporalmente a ubicaciones alternativas. Compruebe siempre la dirección de envío proporcionada durante la creación del trabajo antes de enviar las unidades.

**Al enviar la unidad, la empresa de transporte le pedirá el nombre de contacto y el número de teléfono del centro de datos. ¿Qué debo proporcionar?**

El número de teléfono se le proporciona durante la creación del trabajo. Si necesita un nombre de contacto, póngase en contacto con nosotros en waimportexport@microsoft.com y le facilitaremos esa información.

**¿Puedo usar el servicio Importación/Exportación de Azure para copiar buzones PST y datos de SharePoint en Office 365?**

Consulte [Importar archivos PST o datos de SharePoint a Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**¿Puedo usar el servicio Importación/Exportación de Azure para copiar las copias de seguridad sin conexión en el servicio Copia de seguridad de Azure?**

Consulte [Flujo de trabajo de copia de seguridad sin conexión en Copia de seguridad de Azure](../backup/backup-azure-backup-import-export.md).

**¿Cuántas unidades HDD se pueden incluir como máximo en un envío?

Un envío puede incluir cualquier cantidad de HDD, pero si los discos pertenecen a varios trabajos, se recomienda: a) Etiquetar los discos con los nombres de trabajo correspondientes. b) Actualizar los trabajos con un número de seguimiento que lleve el sufijo -1, -2, etc.
  
**¿Qué tamaño máximo de blobs en bloques y blobs en páginas admite la importación/exportación de disco?

El tamaño máximo aproximado de blobs en bloques admitido es de 4,768 TB o 5.000.000 MB.
El tamaño máximo de blobs en páginas es de 1 TB.
## <a name="next-steps"></a>Pasos siguientes

* [Configuración de la herramienta WAImportExport](storage-import-export-tool-how-to.md)
* [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md)
* [Ejemplo de API de REST de Azure Import/Export](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)


