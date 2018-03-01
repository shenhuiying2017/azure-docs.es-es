---
title: Uso de Azure Import/Export para transferir datos a y desde Azure Storage | Microsoft Docs
description: "Aprenda a crear trabajos de importación y exportación en Azure Portal para transferir datos a y desde Azure Storage."
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
ms.date: 10/03/2017
ms.author: muralikk
ms.openlocfilehash: 0c34b7ce028ef0fae77322513f62557fa9f9929c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Uso del servicio Microsoft Azure Import/Export para transferir datos a Azure Storage
En este artículo, se proporcionan instrucciones detalladas sobre el uso del servicio Azure Import/Export para transferir de forma segura grandes cantidades de datos a Azure Blob Storage y Azure Files mediante el envío de unidades de disco duro a un centro de datos de Azure. También se puede usar este servicio para transferir datos desde Azure Storage hasta las unidades de disco duro y enviarlas al sitio local. Es posible importar los datos de una sola unidad de disco SATA interna a Azure Blob Storage o Azure Files. 

> [!IMPORTANT] 
> Este servicio acepta solo HDD o SSD de SATA internas. No se admite ningún otro dispositivo. No envíe dispositivos de HDD o NAS externos, entre otros, ya que se devolverán en cuanto sea posible o se descartarán.
>
>

Siga estos pasos si los datos del disco se van a importar en Azure Storage.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>Paso 1: Preparar las unidades que utilizan la herramienta WAImportExport y generar los archivos de diario.

1.  Identifique los datos que se van a importar en Azure Storage. Podrían ser directorios o archivos independientes situados en el servidor local o un recurso compartido de red.
2.  Dependiendo del tamaño total de los datos, proporcione el número necesario de unidades de disco duro SATA II o III de 2,5 o 3,5 pulgadas o SSD de 2,5 pulgadas.
3.  Conecte los discos duros directamente con SATA o con adaptadores USB externos a una máquina de Windows.
4.  Cree un volumen NTFS en cada unidad de disco duro y asigne una letra de unidad al volumen. No hay puntos de montaje.
5.  Para habilitar el cifrado en la máquina Windows, habilite el cifrado BitLocker en el volumen NTFS. Use las instrucciones que se indican en https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx.
6.  Copie completamente los datos en estos volúmenes NTFS únicos cifrados mediante la funcionalidad de copiar y pegar, arrastrar y colocar, Robocopy o cualquier herramienta de este tipo.
7.  Descargue WAImportExport v1 desde https://www.microsoft.com/en-us/download/details.aspx?id=42659.
8.  Descomprima en la carpeta predeterminada waimportexportv1. Por ejemplo, C:\WaImportExportV1.  
9.  Ejecute como administrador y abra una instancia de PowerShell o la línea de comandos y cambie el directorio a la carpeta descomprimida. Por ejemplo, cd C:\WaImportExportV1.
10. Copie la siguiente línea de comandos en el Bloc de notas y edítela para crear una línea de comandos.
  ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ /skipwrite
    
    /j: el nombre de un archivo denominado archivo de diario con la extensión .jrn. Se genera un archivo de diario por unidad, por lo que se recomienda utilizar el número de serie del disco como nombre para el archivo de diario.
    /sk: clave de la cuenta de Azure Storage. /t: letra de unidad del disco que se va a enviar. Por ejemplo, D /bk: es la clave de BitLocker de la unidad. /srcdir: letra de la unidad del disco que se va a enviar seguida de :\. Por ejemplo, D:\.
    /dstdir: nombre del contenedor de Azure Storage en el que se van a importar los datos.
    /skipwrite 
    
11. Repita el paso 10 para cada disco que tenga que enviarse.
12. Se crea un archivo de diario con el nombre proporcionado con el parámetro /j: para cada ejecución de la línea de comandos.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>Paso 2: Crear un trabajo de importación en Azure Portal.

1. Inicie sesión en https://portal.azure.com/ y en Más servicios -> ALMACENAMIENTO -> "Trabajos de importación o exportación". Haga clic en **Crear el trabajo de importación o exportación**.

2. En la sección Aspectos básicos, seleccione "Importar en Azure", escriba una cadena para el nombre del trabajo, seleccione una suscripción y escriba o seleccione un grupo de recursos. Escriba un nombre descriptivo para el trabajo de importación. Tenga en cuenta que el nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. El nombre elegido le sirve para realizar un seguimiento de sus trabajos mientras estén en curso y una vez que se hayan completado.

3. En la sección Detalles del trabajo, cargue los archivos de diario de unidad que haya obtenido durante el paso de preparación de la unidad. Si se usó waimportexport.exe versión 1, debe cargar un archivo por cada unidad que haya preparado. Seleccione la cuenta de almacenamiento a la que se importarán los datos en la sección "Destino de importación" de la cuenta de almacenamiento. La ubicación de la entrega se rellena automáticamente según la región de la cuenta de almacenamiento seleccionada.
   
   ![Creación del trabajo de importación - Paso 3](./media/storage-import-export-service/import-job-03.png)
4. En la sección Información de envío de devolución, seleccione el transportista en la lista desplegable y escriba un número de cuenta de transportista válido que creó con ese transportista. Microsoft usa esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación. Proporcione información completa y válida sobre el contacto, como nombre, teléfono, correo electrónico, dirección postal, ciudad, código postal, estado o provincia y país o región.
   
5. En la sección Resumen, se proporciona la dirección de envío del centro de datos de Azure para usarla para enviar discos a Azure DC. Asegúrese de que en la etiqueta de envío se mencione el nombre del trabajo y la dirección completa. 

6. Haga clic en Aceptar en la página Resumen para completar la creación del trabajo de importación.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>Paso 3: Enviar las unidades a la dirección de envío del centro de datos de Azure especificada en el paso 2.
Para enviar el paquete al centro de datos de Azure se puede usar FedEx, UPS o DHL.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>Paso 4: Actualizar el trabajo creado en el paso 2 con el número de seguimiento del envío.
Después de enviar los discos, vuelva a la página **Importación o exportación** de Azure Portal para actualizar el número de seguimiento con estos pasos: a) Navegue al trabajo de importación y haga clic en él, b) Haga clic en **Actualizar el estado del trabajo y la información de seguimiento cuando se envíen las unidades**. c) Seleccione la casilla "Marcar como enviado" d) Proporcione la compañía de transporte y el número de seguimiento.
Si el número de seguimiento no se actualiza dentro del plazo de 2 semanas desde la creación del trabajo, este expirará. Puede realizar un seguimiento del progreso del trabajo en el panel del portal. Consulte en [Visualización del estado del trabajo](#viewing-your-job-status)lo que significa cada estado de trabajo de la sección anterior.

## <a name="when-should-i-use-the-azure-importexport-service"></a>¿Cuándo debo utilizar el servicio Azure Import/Export?
Considere el uso del servicio de Azure Import/Export cuando la carga o descarga de datos a través de la red sea demasiado lenta o el costo de ancho de banda de red adicional resulte prohibitivo.

Puede utilizar este servicio en los siguientes escenarios:

* Migración de datos a la nube: mueva rápidamente grandes cantidades de datos a Azure de manera rápida y rentable.
* Distribución de contenido: envíe rápidamente datos a los sitios de cliente.
* Copia de seguridad: realice copias de seguridad de los datos locales para almacenarlos en Azure Storage.
* Recuperación de datos: recupere una gran cantidad de datos almacenados en el almacenamiento y recíbalos en su ubicación local.

## <a name="prerequisites"></a>requisitos previos
En esta sección, se enumeran los requisitos previos necesarios para utilizar este servicio. Revíselos detenidamente antes de enviar sus unidades.

### <a name="storage-account"></a>Cuenta de almacenamiento
Debe disponer de una suscripción de Azure existente y una o varias cuentas de almacenamiento para utilizar el servicio Import/Export. Azure Import/Export solo admite las cuentas clásicas de Blob Storage y las cuentas de almacenamiento de uso general v1. Puede utilizar cada trabajo para transferir datos desde o hacia una sola cuenta de almacenamiento. Dicho de otra forma, un trabajo de importación y exportación no puede abarcar varias cuentas de almacenamiento. Para obtener información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

### <a name="data-types"></a>Tipos de datos
Puede usar el servicio Azure Import/Export para copiar datos de blobs **en bloque**, **en páginas** o **archivos**. Y a la inversa, solo puede exportar blobs **en bloque**, **en páginas** o **en anexos** de Azure Storage mediante este servicio. El servicio admite solo la importación de Azure Files en Azure Storage. Actualmente no se admite la exportación de Azure Files.

### <a name="job"></a>Trabajo
Para comenzar el proceso de importación o exportación desde el almacenamiento, cree primero un trabajo. Un trabajo puede ser un trabajo de importación o un trabajo de exportación:

* Cree un trabajo de importación si desea transferir datos que tiene en su instalación local a la cuenta de almacenamiento de Azure.
* Cree un trabajo de exportación si desea transferir datos que actualmente están almacenados en la cuenta de almacenamiento a discos duros que se envían a Microsoft. Cuando crea un trabajo, notifica al servicio Import/Export que enviará uno o varios discos duros a un centro de datos de Azure.

* En el caso de un trabajo de importación, enviará las unidades de disco duro que contengan los datos.
* Si es un trabajo de exportación, enviará las unidades de disco duro vacías.
* Puede enviar hasta 10 unidades de disco duro por trabajo.

Para crear un trabajo de importación o exportación, puede usar Azure Portal o la [API de REST de Import/Export de Azure Storage](/rest/api/storageimportexport).

> [!Note]
> A partir del 28 de febrero de 2018 en adelante no se admitirán las API de RDFE. Para seguir usando el servicio, migre a las [API de REST de Import/Export de ARM](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json). 

### <a name="waimportexport-tool"></a>Herramienta WAImportExport
El primer paso para crear un trabajo de **importación** consiste en preparar las unidades que se enviarán para esta operación. Para preparar las unidades, debe conectarlas a un servidor local y ejecutar la herramienta WAImportExport en dicho servidor. Esta herramienta facilita la copia de los datos en la unidad, el cifrado de los datos en la unidad con BitLocker y la generación de los archivos de diario de unidad.

Los archivos del diario almacenan información básica sobre el trabajo y la unidad, como el número de serie de la unidad o el nombre de la cuenta de almacenamiento. Este archivo de diario no se almacena en la unidad. Se utiliza durante la creación del trabajo de importación. Más adelante en este artículo se proporcionan pasos detallados sobre la creación del trabajo.

La herramienta WAImportExport solo es compatible con el sistema operativo de Windows de 64 bits. Consulte la sección [Sistema operativo](#operating-system) para ver cuáles son las versiones específicas admitidas.

Descargue la versión más reciente de [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Para más información acerca del uso de la herramienta WAImportExport, consulte [WAImportExport Tool Reference](storage-import-export-tool-how-to.md) (Referencia acerca de la herramienta WAImportExport).

>[!NOTE]
>**Versión anterior:** puede [descargar la versión WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) de la herramienta y consultar la [guía de uso de WAImportExpot V1](storage-import-export-tool-how-to-v1.md). La versión WAImportExpot V1 de la herramienta proporciona compatibilidad para **preparar discos cuando hay datos escritos previamente en el disco**. Si la única clave disponible es SAS-Key, debe usar la herramienta WAImportExpot V1.

>

### <a name="hard-disk-drives"></a>Unidades de disco duro
El servicio Import/Export solo admite SSD de 2,5 pulgadas o HDD internos SATA II o III de 2,5 o 3,5 pulgadas. Un único trabajo de importación/exportacón puede tener un máximo de 10 unidades HDD/SSD, y cada una de ellas puede tener cualquier tamaño. Un volumen grande de unidades puede distribuirse entre varios trabajos, y no hay ningún límite en el número de trabajos que se pueden crear. 

Para los trabajos de importación, solo se procesa el primer volumen de datos de la unidad. El volumen de datos debe tener formato NTFS.

> [!IMPORTANT]
> Este servicio no admite unidades de disco duro externas que incorporen un adaptador USB integrado. Además, no se puede usar el disco que hay en el interior de la carcasa de las unidades de disco duro externo, así que no envíe este tipo de unidad.
> 
> 

A continuación se muestra una lista de adaptadores USB externos utilizados para copiar datos en unidades de disco duro internas. Anker 68UPSATAA-02BU Anker 68UPSHHDS-BU Startech SATADOCK22UE Orico 6628SUS3-C-BK (serie 6628) Base de acoplamiento de unidad de disco duro externa SATA de intercambio directo Thermaltake BlacX (USB 2.0 y eSATA)

### <a name="encryption"></a>Cifrado
Los datos de la unidad deben estar cifrados con BitLocker Drive Encryption. Este cifrado protege los datos mientras se encuentren en tránsito.

En los trabajos de importación, hay dos maneras de realizar el cifrado. La primera manera consiste en especificar la opción al usar el archivo CSV de conjunto de datos mientras se ejecuta la herramienta WAImportExport durante la preparación de la unidad. La segunda, en habilitar el cifrado de BitLocker manualmente en la unidad y especificar la clave de cifrado en el archivo CSV de conjunto de unidades al ejecutar la línea de comandos de la herramienta WAImportExport durante la preparación de la unidad.

En los trabajos de exportación, después de copiar los datos en las unidades, el servicio cifrará la unidad con BitLocker antes de enviársela de vuelta al usuario. La clave de cifrado se proporciona a través de Azure Portal.  

### <a name="operating-system"></a>Sistema operativo
Puede utilizar uno de los siguientes sistemas operativos de 64 bits para preparar la unidad de disco duro mediante la herramienta WAImportExport antes de enviarla a Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. Todos estos sistemas operativos admiten el cifrado de unidad BitLocker.

### <a name="locations"></a>Ubicaciones
El servicio Azure Import/Export permite copiar datos en todas las cuentas de almacenamiento de Azure público como origen y destino. Las unidades de disco duro se pueden enviar a una de las ubicaciones de la lista. Si la cuenta de almacenamiento se encuentra en una ubicación de Azure pública que no se especifica aquí, se proporcionará una ubicación de envío alternativa al crear el trabajo mediante Azure Portal o la API de REST de Import/Export.

Ubicaciones de envío admitidas:

* Este de EE. UU
* Oeste de EE. UU
* Este de EE. UU. 2
* Oeste de EE. UU. 2
* Central EE. UU:
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
* Centro de Alemania
* Noreste de Alemania

### <a name="shipping"></a>Envío
**Envío de unidades al centro de datos:**

Al crear un trabajo de importación o exportación, se le proporcionará una dirección de envío de una de las ubicaciones admitidas para el envío de sus unidades. La dirección de envío proporcionada depende de la ubicación de la cuenta de almacenamiento, pero no puede ser la misma que la de esta.

Para enviar las unidades a la dirección de envío se puede usar FedEx, UPS o DHL.

**Envío de unidades desde el centro de datos:**

Al crear un trabajo de importación o exportación, debe proporcionar a Microsoft un remite para que le devuelva las unidades enviadas cuando finalice su trabajo. Asegúrese de proporcionar una dirección de devolución válida para evitar retrasos en el procesamiento.

El transportista debe contar con un sistema de seguimiento adecuado a fin de mantener la cadena de custodia. Debe proporcionar un número de cuenta válido de las empresas de transporte FedEx, UPS o DHL para que Microsoft realice la devolución. En el caso de FedEx, UPS o DHL, se requiere un número de cuenta para devolver las unidades enviadas desde Estados Unidos y Europa. En DHL, se requiere un número de cuenta para devolver las unidades enviadas desde Asia y Australia. Puede crear una cuenta de [FedEx](http://www.fedex.com/us/oadr/) (para EE. UU. y Europa) o [DHL](http://www.dhl.com/) (Asia y Australia) si no tiene una. Si ya tiene un número de cuenta de uno de estos transportistas, compruebe que sea válido.

Al enviar los paquetes, debe seguir los términos establecidos en los [Términos de servicio de Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Tenga en cuenta que es posible que los medios físicos que envía deban cruzar alguna frontera internacional. Usted es el responsable de asegurar que los medios y datos físicos se importan o exportan de acuerdo con todas las normativas aplicables. Antes de enviar los medios físicos, pida asesoramiento para comprobar que los medios y datos se pueden enviar legalmente al centro de datos identificado. De este modo, se asegura de que llegan a Microsoft de manera puntual. Por ejemplo, cualquier paquete que cruce fronteras internacionales debe ir acompañado de una factura comercial (excepto si cruza fronteras dentro de la Unión Europea). Puede imprimir una copia cumplimentada de la factura comercial desde el sitio web de la compañía. Algunos ejemplos de factura comercial son la [factura comercial de DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) y la [factura comercial de FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Asegúrese de que Microsoft no se ha indicado como el exportador.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>¿Cómo funciona Azure Import/Export?
Se pueden transferir datos entre el sitio local y Azure Storage mediante el servicio Azure Import/Export; para ello, se crean trabajos y se envían unidades de disco duro a un centro de datos de Azure. Cada unidad de disco duro que se envía está asociada a un único trabajo. Cada trabajo está asociado a una única cuenta de almacenamiento. Revise atentamente la [sección de requisitos previos](#pre-requisites) para conocer las características específicas de este servicio, como los tipos de datos, los tipos de discos, las ubicaciones y las formas de envío que se admiten.

En esta sección se describen los pasos de alto nivel implicados en la importación y la exportación de trabajos. Más adelante, en la sección [Inicio rápido](#quick-start), se proporcionan instrucciones paso a paso para crear un trabajo de importación y exportación.

### <a name="inside-an-import-job"></a>Dentro de un trabajo de importación
En un nivel alto, un trabajo de importación implica los siguientes pasos:

* Determine los datos que desea importar y el número de unidades que necesita.
* Identifique la ubicación de los blobs o archivos de destino para los datos en Azure Storage.
* Utilice la herramienta WAImportExport para copiar los datos en una o varias unidades de disco duro y cífrelas con BitLocker.
* Cree un trabajo de importación en la cuenta de almacenamiento de destino mediante Azure Portal o la API de REST de Import/Export. Si utiliza Azure Portal, cargue los archivos del diario de unidad.
* Indique el remite y el número de cuenta de la empresa de transporte que se utilizará para que se le devuelvan las unidades.
* Envíe las unidades de disco duro a la dirección de envío proporcionada durante la creación del trabajo.
* Actualice el número de seguimiento de entrega en los datos del trabajo de importación y envíe dicho trabajo.
* Las unidades se reciben y procesan en el centro de datos de Azure.
* Las unidades se devuelven usando la cuenta de la empresa de transporte al remite indicado en el trabajo de importación.
  
    ![Figura 1: Importación de flujos de trabajo](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Dentro de un trabajo de exportación
> [!IMPORTANT]
> El servicio solo admite la exportación de blobs de Azure y no admite la exportación de Azure Files...
> 
>

En un nivel alto, un trabajo de exportación implica los siguientes pasos:

* Determine los datos que desea exportar y el número de unidades que necesita.
* Identifique los blobs de origen o las rutas de acceso al contenedor de los datos en Blob Storage.
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
Puede realizar el seguimiento del estado de sus trabajos de importación y exportación desde Azure Portal. Haga clic en la pestaña **Import/Export**. Aparece una lista de los trabajos en la página.

![Visualización del estado del trabajo](./media/storage-import-export-service/jobstate.png)

Verá uno de los siguientes estados de trabajo en función de la fase del proceso en que se encuentre la unidad.

| Estado del trabajo | DESCRIPCIÓN |
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

| Estado de la unidad | DESCRIPCIÓN |
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
El tiempo que se tarda en procesar un trabajo de importación o exportación varía en función de determinados factores, como el tiempo de envío, el tipo de trabajo, el tipo y el tamaño de los datos copiados o el tamaño de los discos proporcionados. El servicio Import/Export no tiene un Acuerdo de Nivel de Servicio, pero después de que se reciben los discos se esfuerza por completar la copia en un período de tiempo de entre 7 y 10 días. Puede utilizar la API de REST para realizar un seguimiento más estrecho del progreso del trabajo. Hay un parámetro de porcentaje completado en la operación de lista de trabajos que proporciona una indicación del progreso de la copia. Si necesita una estimación para realizar un trabajo de importación o exportación en el que el tiempo es un factor crítico, póngase en contacto con nosotros.

### <a name="pricing"></a>Precios
**Cuota de manipulación de unidades**

Existe una cuota de manipulación por cada unidad procesada como parte del trabajo de importación o exportación. Consulte los detalles en [Precios de Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Gastos de envío**

Al enviar unidades a Azure, pagar los gastos de envío al transportista. Cuando Azure le devuelve la unidad, los gastos de envío se le cargan en la cuenta de la empresa de transporte que indicó al crear el trabajo.

**Costos de transacción**

No hay ningún costo de transacción además de los costos de transacciones de almacenamiento estándar al importar datos en Azure Storage. Los cargos de salida estándar son aplicables cuando se exportan datos desde Blob Storage. Para más información sobre los costos de transacción, consulte [Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>¿Cómo importar datos en Azure File Storage mediante los HDD y SDD de SATA internos?
Siga estos pasos si los datos del disco se importarán en Azure File Storage.
El primer paso al importar datos mediante el servicio Azure Import/Export consiste en preparar las unidades mediante la herramienta WAImportExport. Siga estos pasos para preparar las unidades.

1. Identifique los datos que se importarán en Azure File Storage. Podrían ser directorios o archivos independientes situados en el servidor local o un recurso compartido de red.  
2. Determine el número de unidades que necesitará según el tamaño total de los datos. Proporcione el número necesario de unidades de disco duro SATA II o III de 2,5 o 3,5 pulgadas o SSD de 2,5 pulgadas.
4. Determine los directorios o archivos independientes que se copiarán en cada unidad de disco duro.
5. Cree los archivos CSV para el conjunto de datos y el conjunto de unidades.
    
  A continuación se muestra un ejemplo de archivo CSV de conjunto de datos para la importación de datos como Azure Files:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   En el ejemplo anterior, 100M_1.csv.txt se copiará en la raíz de "fileshare". Si no existe "Fileshare", se creará uno. Todos los archivos y carpetas bajo 50M_original se copiarán en fileshare de forma recursiva. Se mantendrá la estructura de carpetas.

    Obtenga más información sobre cómo [preparar el archivo CSV de conjunto de datos](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


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

6.  Utilice la herramienta [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) para copiar sus datos a uno o varios discos duros.
7.  Puede especificar "Encrypt" en el campo de cifrado en el archivo CSV de conjunto de unidades para habilitar el cifrado de BitLocker en la unidad de disco duro. Como alternativa, también puede habilitar el cifrado de BitLocker manualmente en la unidad de disco duro, especificar "AlreadyEncrypted" y proporcionar la clave en el archivo CSV de conjunto de unidades mientras se ejecuta la herramienta.

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



## <a name="create-an-export-job"></a>Crear un trabajo de exportación
Cree un trabajo de exportación para notificar al servicio Import/Export que va a enviar al centro de datos una o varias unidades vacías para que se exporten los datos de la cuenta de almacenamiento a las unidades y recibirlas posteriormente.

### <a name="prepare-your-drives"></a>Preparación de las unidades
Para preparar las unidades para el trabajo de exportación, se recomienda realizar las siguientes comprobaciones previas:

1. Compruebe el número de discos necesarios mediante el comando PreviewExport de la herramienta WAImportExport. Para más información, consulte [Vista previa de uso de disco para un trabajo de exportación](https://msdn.microsoft.com/library/azure/dn722414.aspx). Esto le ayuda a obtener una vista previa del uso del disco para los blobs que ha seleccionado, en función del tamaño de las unidades que se va a usar.
2. Compruebe que puede leer o escribir en la unidad de disco duro que se va a enviar para el trabajo de exportación.

### <a name="create-the-export-job"></a>Creación del trabajo de exportación
1. Para crear un trabajo de exportación, navegue a Más servicios -> ALMACENAMIENTO -> "Trabajos de importación o exportación" en Azure Portal. Haga clic en **Crear el trabajo de importación o exportación**.
2. En el paso 1, Aspectos básicos, seleccione "Exportar desde Azure", escriba una cadena para el nombre del trabajo, seleccione una suscripción, escriba o seleccione un grupo de recursos. Escriba un nombre descriptivo para el trabajo de importación. Tenga en cuenta que el nombre que escriba solo puede contener letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. El nombre elegido le servirá para realizar un seguimiento de sus trabajos mientras están en progreso y una vez se hayan completado. Proporcione la información de contacto de la persona responsable de este trabajo de exportación. 

3. En el paso 2, Detalles del trabajo, seleccione la cuenta de almacenamiento desde donde se exportarán los datos en la sección Cuenta de almacenamiento. La ubicación de la entrega se rellenará automáticamente según la región de la cuenta de almacenamiento seleccionada. Especifique los datos de blobs que desea exportar desde su cuenta de almacenamiento a una o varias unidades vacías. Puede elegir exportar todos los datos de blobs de la cuenta de almacenamiento o especificar los blobs o conjuntos de blobs que se van a exportar.
   
   Para especificar un blob que desea exportar, utilice el selector **Igual a** y especifique la ruta relativa al blob, empezando por el nombre del contenedor. Utilice *$root* para especificar el contenedor raíz.
   
   Para especificar todos los blobs que empiecen por un prefijo, utilice el selector **Empieza por** y especifique el prefijo, empezando por una barra diagonal "/". El prefijo puede ser el prefijo del nombre del contenedor, el nombre del contenedor completo o el nombre del contendor completo seguido del prefijo del nombre del blob.
   
   En la siguiente tabla se muestran ejemplos de rutas de acceso del blob válidas:
   
   | Selector | Ruta del blob | DESCRIPCIÓN |
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

4. En el paso 3, Información de envío de devolución, seleccione el transportista en la lista desplegable y escriba un número de cuenta de transportista válido que creó con ese transportista. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación. Proporcione información completa y válida sobre el contacto, como nombre, teléfono, correo electrónico, dirección postal, ciudad, código postal, estado o provincia y país o región.
   
 5. En la página Resumen, se proporciona la dirección de envío del centro de datos de Azure para usarla para enviar discos a Azure DC. Asegúrese de que en la etiqueta de envío se mencione el nombre del trabajo y la dirección completa. 

6. Haga clic en Aceptar en la página Resumen para completar la creación del trabajo de importación.

7. Después de enviar los discos, vuelva a la página **Importación o exportación** de Azure Portal, a) Navegue al trabajo de importación y haga clic en él b) Haga clic en **Actualizar el estado del trabajo y la información de seguimiento cuando se envíen las unidades**. 
     c) Seleccione la casilla "Marcar como enviado" d) Proporcione la compañía de transporte y el número de seguimiento.
    
   Si el número de seguimiento no se actualiza dentro del plazo de 2 semanas desde la creación del trabajo, este expirará.
   
8. Puede realizar un seguimiento del progreso del trabajo en el panel del portal. Consulte en [Visualización del estado del trabajo](#viewing-your-job-status)lo que significa cada estado de trabajo de la sección anterior.

   > [!NOTE]
   > Si el blob que se va a exportar está en uso en el momento de la copia en la unidad de disco duro, el servicio Azure Import/Export tomará una instantánea del blob y la copiará.
   > 
   > 
 
9. Después de recibir las unidades con los datos exportados, puede ver y copiar las claves de BitLocker generadas por el servicio para la unidad. Navegue al trabajo de exportación en Azure Portal y haga clic en la pestaña Import/Export. Seleccione el trabajo de exportación en la lista y haga clic en la opción Claves de BitLocker. Las claves de BitLocker aparecen como se muestra a continuación:
   
   ![Visualización de claves de BitLocker de un trabajo de exportación](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Vaya a la sección de P+F a continuación, en ella se tratan las preguntas más comunes que se encuentran los clientes al utilizar este servicio.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Puedo copiar Azure File Storage mediante el servicio Azure Import/Export?**

Sí, el servicio Azure Import/Export permite importar a Azure File Storage. Actualmente no admite la exportación de Azure Files.

**¿Está disponible el servicio Azure Import/Export para suscripciones de CSP?**

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

Un trabajo de importación o exportación solo puede hacer referencia a 10 unidades en un único trabajo para el servicio Import/Export. Si desea enviar más de 10 unidades, puede crear varios trabajos. Las unidades asociadas al mismo trabajo se deben enviar juntas en el mismo paquete.
Microsoft ofrece orientación y asistencia cuando la capacidad de datos abarca varios trabajos de importación en disco. Para más información, póngase en contacto con bulkimport@microsoft.com.

**¿El servicio formatea las unidades antes de devolverlas?**

Nº Todas las unidades están cifradas con BitLocker.

**¿Puedo adquirir unidades de Microsoft para los trabajos de importación y exportación?**

Nº Debe enviar sus propias unidades tanto para los trabajos de importación como para los de exportación.

** ¿Cómo puedo acceder a los datos importados por este servicio**

Se puede acceder a los datos almacenados en la cuenta de Azure Storage mediante Azure Portal o con una herramienta independiente denominada Explorador de Storage. https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**Cuando finalice el trabajo de importación, ¿cómo se mostrarán los datos en la cuenta de almacenamiento? ¿Se conservará la jerarquía de directorios?**

Al preparar una unidad de disco duro para un trabajo de importación, el destino se especifica mediante el campo DstBlobPathOrPrefix del archivo CSV de conjunto de datos. Este es el contenedor de destino de la cuenta de almacenamiento donde se copian los datos de la unidad de disco duro. Dentro de él se crean directorios virtuales para las carpetas de la unidad de disco duro y blobs para los archivos. 

**Si la unidad tiene archivos que ya existen en la cuenta de almacenamiento, ¿el servicio sobrescribirá los blobs o archivos existentes en dicha cuenta?**

Al preparar la unidad, puede especificar que los archivos de destino se sobrescriban u omitan mediante el campo del archivo CSV de conjunto de datos llamado Disposition:<rename|no-overwrite|overwrite>. De forma predeterminada, el servicio cambiará el nombre de los nuevos archivos en lugar de sobrescribir los blobs o archivos existentes.

**¿Es compatible la herramienta WAImportExport con sistemas operativos de 32 bits?**
Nº La herramienta WAImportExport solo es compatible con sistemas operativos de Windows de 64 bits. Consulte la sección sobre sistemas operativos en los [requisitos previos](#pre-requisites) para ver una lista completa de las versiones de SO admitidas.

**¿Debo incluir alguna otra cosa, aparte de la unidad de disco duro, en el paquete?**

Envíe solo las unidades de disco duro. No incluya elementos como cables de alimentación o cables USB.

**¿Es necesario enviar mis unidades con FedEx o DHL?**

Puede enviar las unidades al centro de datos con cualquier empresa de transporte conocida, como FedEx, DHL o UPS, o bien mediante el servicio postal de los Estados Unidos. Sin embargo, para devolver las unidades enviadas desde el centro de datos, debe indicar un número de cuenta de FedEx en Estados Unidos y Europa, o bien un número de cuenta de DHL en las regiones de Asia y Australia.

**¿Existe alguna restricción internacional en el envío de las unidades?**

Tenga en cuenta que es posible que los medios físicos que está enviando deban cruzar alguna frontera internacional. Usted es el responsable de asegurar que los medios y datos físicos se importan o exportan de acuerdo con todas las normativas aplicables. Antes de enviar los medios físicos, pida asesoramiento para comprobar que los medios y datos se pueden enviar legalmente al centro de datos identificado. De este modo, se asegurará de que llegan a Microsoft de manera puntual.

**Al crear un trabajo, la dirección de envío es una ubicación distinta de la ubicación de la cuenta de almacenamiento. ¿qué debo hacer?**

Algunas ubicaciones de la cuenta de almacenamiento se asignan a ubicaciones de envío alternativas. Ubicaciones de envío anteriormente disponibles se pueden asignar también temporalmente a ubicaciones alternativas. Compruebe siempre la dirección de envío proporcionada durante la creación del trabajo antes de enviar las unidades.

**Al enviar la unidad, la empresa de transporte le pedirá la dirección de contacto y el número de teléfono del centro de datos. ¿Qué debo proporcionar?**

El número de teléfono y la dirección del centro de datos se proporcionan como parte de la creación del trabajo.

**¿Puedo usar el servicio Azure Import/Export para copiar buzones PST y datos de SharePoint en Office 365?**

Consulte [Importar archivos PST o datos de SharePoint a Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**¿Puedo usar el servicio Azure Import/Export para copiar las copias de seguridad sin conexión en el servicio Azure Backup?**

Consulte [Flujo de trabajo de copia de seguridad sin conexión en Azure Backup](../../backup/backup-azure-backup-import-export.md).

**¿Cuántas unidades HDD se pueden incluir como máximo en un envío?**

Un envío puede incluir cualquier cantidad de HDD, pero si los discos pertenecen a varios trabajos, se recomienda: a) Etiquetar los discos con los nombres de trabajo correspondientes. b) Actualizar los trabajos con un número de seguimiento que lleve el sufijo -1, -2, etc.
  
**¿Qué tamaño máximo de blobs en bloques y blobs en páginas admite la importación/exportación de disco?**

El tamaño máximo aproximado de blobs en bloques admitido es de 4,768 TB o 5.000.000 MB.
El tamaño máximo de blobs en páginas es de 1 TB.

**¿Admite el servicio Import/Export el cifrado AES 256?**

El servicio Azure Import/Export usa de forma predeterminada el cifrado Bitlocker AES 128, pero se puede aumentar a AES 256 si los datos se cifran manualmente con Bitlocker antes de copiarlos. 

Si usa [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), a continuación se muestra un comando de ejemplo.
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Si usa [WAImportExport Tool](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip), especifique "AlreadyEncrypted" y proporcione la clave en el archivo driveset.csv.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>pasos siguientes

* [Configuración de la herramienta WAImportExport](storage-import-export-tool-how-to.md)
* [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md)
* [Ejemplo de API de REST de Azure Import/Export](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

