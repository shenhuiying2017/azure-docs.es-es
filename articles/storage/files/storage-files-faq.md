---
title: "Preguntas más frecuentes sobre Azure Files | Microsoft Docs"
description: Obtenga respuestas a algunas preguntas frecuentes sobre Azure Files.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: 91c46ea0897f83811cfa5c1a8b67677caff14569
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Preguntas más frecuentes sobre Azure Files
[Azure Files](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) le ofrece recursos compartidos de archivos en la nube totalmente administrados, a los que se puede acceder mediante el [protocolo de Bloque de mensajes del servidor (SMB) estándar](storage-files-introduction.md), también conocido como Sistema de archivos de Internet común o CIFS. Los recursos compartidos de Azure Files se pueden montar simultáneamente en implementaciones de Windows, Linux y macOS en la nube o locales. Además, los recursos compartidos de archivos de Azure Files se pueden almacenar en la caché de los servidores de Windows Server con Azure File Sync (versión preliminar), que permite un acceso rápido allí donde se utilizan los datos.

En este artículo se detallarán algunas de las preguntas más frecuentes acerca de las características y las funcionalidades de Azure Files, incluyendo Azure File Sync. Si no encuentra una respuesta que se ajuste a su pregunta, no dude en ponerse en contacto con nosotros mediante los siguientes canales (en orden incremental):

1. En la sección Comentarios de este artículo.
2. [Foro de Azure Storage](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Soporte técnico de Microsoft: para crear un nuevo caso de soporte técnico, vaya a la pestaña "Ayuda y soporte técnico" en Azure Portal y haga clic en "Nueva solicitud de soporte técnico".

## <a name="general"></a>General
* <a id="why-files-useful"></a>**¿Por qué es útil Azure Files?**  
   Azure Files permite crear recursos compartidos de archivos en la nube sin tener que administrar la sobrecarga de un servidor físico o de un dispositivo/aplicación. Esto significa que el usuario puede dedicar menos tiempo a aplicar actualizaciones de sistema operativo y a reemplazar discos incorrectos: todo ese trabajo más monótono se realiza automáticamente. Para más información sobre los escenarios en los que puede ayudar Azure Files, consulte [¿Por qué es útil Azure Files?](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>**¿Cuáles son las diferentes formas de obtener acceso a los archivos de Azure Files?**  
    Puede montar el recurso compartido de archivos en la máquina local mediante el protocolo SMB 3.0 o usar herramientas como el [Explorador de Storage](http://storageexplorer.com/) para obtener acceso a los archivos en el recurso compartido de archivos. Desde la aplicación, puede usar las bibliotecas de cliente de almacenamiento, las API de REST, PowerShell o CLI para obtener acceso a los archivos en el recurso compartido de archivos de Azure Files.

* <a id="what-is-afs"></a>**¿Qué es Azure File Sync?**  
    Azure File Sync le permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

* <a id="files-versus-blobs"></a>**¿Por qué debo utilizar para mis datos un recurso compartido de archivos de Azure Files en vez de Azure Blob Storage?**  
    Tanto Azure Files como Azure Blob Storage proporcionan una manera de almacenar grandes cantidades de datos en la nube, pero son útiles para fines ligeramente diferentes. Azure Blob Storage es útil para aplicaciones de gran escala y nativas de la nube que deben almacenar datos no estructurados. Para maximizar el rendimiento y la escala, Azure Blob Storage es una abstracción de almacenamiento más sencilla que un sistema de archivos verdadero. Además, Azure Blob Storage solo puede obtenerse a través de bibliotecas de cliente basada en REST (o directamente mediante el protocolo basado en REST).

    Azure Files, por otro lado, desea ser específicamente un sistema de archivos, con todas las abstracciones de archivos conocidas y deseadas durante años de sistemas operativos locales. Como Azure Blob Storage, Azure Files ofrece una interfaz REST y bibliotecas de cliente basadas en REST, pero a diferencia de Azure Blob Storage, Azure Files también ofrece acceso de SMB a los recursos compartidos de los archivos de Azure. Esto significa que puede montar directamente un recurso compartido de Azure Files en Windows, Linux o macOS, máquinas virtuales locales o en la nube, sin tener que escribir ningún código ni adjuntar controladores especiales al sistema de archivos. Además, los recursos compartidos de Azure Files se pueden almacenar en caché en servidores de archivos local mediante Azure File Sync para un acceso rápido allí donde se utilizan los datos. 
   
    Para obtener una explicación más detallada sobre las diferencias entre Azure Files y Azure Blob Storage, consulte [Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber más de Azure Blob Storage, consulte [Introducción a Blob Storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**¿Por qué debo utilizar un recurso compartido de archivos de Azure Files en vez de Azure Disks?**  
    Una instancia de Azure Disks es justamente eso, un disco. Un disco independiente por sí mismo no resulta muy útil. Para sacar provecho de una instancia de Azure Disks, tendrá que conectarlo a máquina virtual se ejecuta en Azure. Azure Disks puede usarse para todo para lo que quiera usar un disco en un servidor local: como disco del sistema operativo, como espacio de intercambio para un sistema operativo, o como almacenamiento dedicado para una aplicación. Un uso interesante para de Azure Disks es crear un servidor de archivos en la nube para usarlo en exactamente los mismos lugares en los que usaría un recurso compartido de Azure Files. Implementar un servidor de archivos en las máquinas virtuales de Azure es una manera excelente y de alto rendimiento de obtener almacenamiento de archivos en Azure cuando necesite opciones de implementación no compatibles actualmente con Azure Files (por ejemplo, la compatibilidad con el protocolo NFS o almacenamiento premium). 

    Por otro lado, la ejecución de un servidor de archivos con Azure Disks como almacenamiento back-end suele ser mucho más costoso que el uso de un recurso compartido de Azure Files por diversos motivos. En primer lugar, además de pagar por el almacenamiento en disco, también debe pagar por el gasto de ejecutar una o más máquinas virtuales de Azure. En segundo lugar, también debe administrar las máquinas virtuales que se usan para ejecutar el servidor de archivos, por ejemplo, siendo responsable de las actualizaciones del sistema operativo. Por último, si finalmente se requieren datos almacenados en la caché local, el usuario tiene la última palabra para configurar y administrar las tecnologías de replicación (como la replicación del Sistema de archivos distribuido) para que esto ocurra.

    Un enfoque interesante para obtener lo mejor tanto de Azure Files como de un servidor de archivos hospedados en máquinas virtuales de Azure con Azure Disks como almacenamiento de back-end consiste en instalar Azure File Sync en el servidor de archivos hospedado en máquina virtual en la nube. Si el recurso compartido de Azure Files se encuentra en la misma región que el servidor de archivos, puede habilitar los niveles en la nube y establecer un porcentaje de espacio libre en el volumen al máximo (99 %). Esto garantiza la duplicación mínima de datos mientras permite utilizar las aplicaciones que se deseen en los servidores de archivos, como las que requieran la compatibilidad con el protocolo NFS.

    Para obtener más información sobre la forma de configurar un elevado rendimiento y un servidor de archivos que tenga una alta disponibilidad en Azure, consulte la entrada del blog [Deploying IaaS VM Guest Clusters in Microsoft Azure (Implementación de clústeres invitados de VM de IaaS en Microsoft Azure)](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para una explicación más detallada sobre las diferencias entre Azure Files y Azure Disks, consulte [Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para aprender más sobre Azure Disks, consulte [Introducción a Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**¿Cómo puedo empezar a usar Azure Files?**  
    Comenzar con Azure Files es sencillo: basta con [crear un recurso compartido de archivos](storage-how-to-create-file-share.md) y montarlo en el sistema operativo preferido: 

    - [Montaje en Windows](storage-how-to-use-files-windows.md)
    - [Montaje en Linux](storage-how-to-use-files-linux.md)
    - [Montaje en macOS](storage-how-to-use-files-mac.md)

    Para obtener información más detallada sobre cómo implementar un recurso compartido de Azure Files para reemplazar los recursos compartidos de archivos de producción en su organización, consulte [Planeamiento de una implementación de Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>**¿Qué opciones de redundancia de almacenamiento son compatibles con Azure Files?**  
    Azure Files actualmente solo admite el almacenamiento con redundancia local (LRS) y almacenamiento con redundancia geográfica (GRS). Pensamos admitir en el futuro el almacenamiento con redundancia de zona (ZRS) y el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS), pero en este momento no contamos con escalas de tiempo para compartir.

* <a id="tier-options"></a>**¿Qué capas de almacenamiento son compatibles actualmente con Azure Files?**  
    Actualmente, Azure Files solo admite la capa de almacenamiento estándar. Igualmente, en estos momentos no tenemos escalas de tiempo que compartir para la compatibilidad premium y de acceso esporádico. Tenga en cuenta que no se pueden crear recursos compartidos de archivos de Azure Files desde las cuentas de almacenamiento que sean solo para blobs o desde las cuentas de almacenamiento premium.

* <a id="give-us-feedback"></a>**Me gustaría ver la característica *x* agregada a Azure Files, ¿podrían agregarla?**  
    Por supuesto, el equipo de Azure Files está interesado en conocer todos los comentarios que tengan sobre nuestro servicio. Vote en las solicitudes de características en [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files). Estamos deseando entusiasmarle con muchas características nuevas.

## <a name="azure-file-sync"></a>Azure File Sync
* <a id="afs-region-availability"></a>**¿Qué regiones se admiten actualmente en Azure File Sync (versión preliminar)?**  
    Azure File Sync está disponible actualmente en el oeste de Estados Unidos, Europa Occidental, este de Australia y Asia Suroriental. Agregaremos compatibilidad con otras regiones mientras seguimos trabajando hacia la disponibilidad general. Para más información, consulte la sección [Disponibilidad en regiones](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>**¿Puedo tener servidores unidos a un dominio y no unidos a un dominio en el mismo grupo de sincronización?**  
    Sí, un grupo de sincronización puede contener puntos de conexión de servidor que tienen pertenencias diferentes de Active Directory, incluso los que no estén unidos a dominio. Aunque técnicamente funciona la configuración, no se recomienda como una configuración normal ya que las listas de control de acceso que se definen para los archivos y carpetas en un servidor no podrán aplicarse a otros servidores del grupo de sincronización. Para obtener mejores resultados, se recomienda realizar la sincronización entre servidores del mismo bosque de Active Directory, entre servidores de bosques distintos de Active Directory con relaciones de confianza establecidas o entre servidores que no están en un dominio, pero no una combinación de todos los anteriores.

* <a id="afs-change-detection"></a>**He creado un archivo directamente en el recurso compartido de archivos de Azure Files a través de SMB o a través del portal. ¿Cuánto tiempo tarda la sincronización del archivo con los servidores del grupo de sincronización?** 
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Cuando se cambia el mismo archivo en dos servidores aproximadamente al mismo tiempo, ¿qué sucede?**  
    Azure File Sync utiliza una sencilla estrategia de resolución de conflictos: conservamos ambos cambios. La operación de escritura más reciente mantiene el nombre de archivo original. El archivo anterior tiene la máquina de "origen" y el número de conflicto se anexa al nombre con esta taxonomía: 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    Por ejemplo, el primer conflicto de `CompanyReport.docx` se convierte en `CompanyReport-CentralServer.docx` si `CentralServer` es donde se produjo la operación de escritura anterior. El segundo conflicto se identifica como `CompanyReport-CentralServer-1.docx`.

* <a id="afs-storage-redundancy"></a>**¿Se admite el almacenamiento con redundancia geográfica (GRS) en Azure File Sync?**  
    Sí, Azure File Sync admite tanto el almacenamiento con redundancia local (LRS) como el almacenamiento con redundancia geográfica (GRS). En el caso de una conmutación por error de GRS entre regiones emparejadas, se recomienda tratar la nueva región como una copia de seguridad de datos únicamente. Azure File Sync no iniciará automáticamente la sincronización con la nueva región principal. 

* <a id="sizeondisk-versus-size"></a>**¿Por qué la propiedad *Tamaño en disco* de un archivo no coincide con la propiedad *Tamaño* después de usar Azure File Sync?**  
    El Explorador de Windows Files expone dos propiedades, **Tamaño** y **Tamaño en disco** para representar el tamaño de un archivo. Estas propiedades se diferencian ligeramente en el significado; **Tamaño** representa el tamaño completo del archivo, mientras que **Tamaño en disco** representa el tamaño del flujo de archivos realmente almacenados en el disco. Estas propiedades pueden diferir por diversos motivos, como la compresión, el uso de la desduplicación de datos o, en nuestro caso, de niveles en la nube con Azure File Sync. Si un archivo está interconectado a un recurso compartido de Azure Files, el tamaño en disco será de cero porque la secuencia de archivos se almacena en el recurso compartido de Azure Files, no en el disco. También es posible que un archivo se encuentre parcialmente organizado en niveles (o parcialmente retirado), lo que significa que parte del archivo está en el disco. Esto puede ocurrir cuando aplicaciones como reproductores multimedia o utilidades zip leen parcialmente los archivos. 

* <a id="is-my-file-tiered"></a>**¿Cómo se puede saber si un archivo se ha organizado en niveles?**  
    Hay varias maneras de comprobar si un archivo se ha organizado en niveles en el recurso compartido de Azure Files:
    
    1. **Compruebe los atributos de archivo en el archivo.** Para ello, haga clic con el botón derecho en un archivo, vaya a **Detalles** y desplácese hacia abajo a la propiedad **Attributes**. Un archivo en niveles tendrá el siguiente conjunto de atributos:     
        
        | Letra del atributo | Atributo | Definición |
        |:----------------:|-----------|------------|
        | Una  | Archivar | Indica que se debe realizar la copia de seguridad del archivo con un software de copia de seguridad. Este atributo siempre se establece independientemente de si el archivo está organizado en niveles o está completamente almacenado en el disco. |
        | P | Archivos dispersos | Indica que el archivo es un archivo disperso, que es un tipo especializado de archivo que ofrece NTFS para un uso eficaz cuando el flujo del archivo en el disco está en gran parte vacío. Azure File Sync usa archivos dispersos porque un archivo está completamente organizado en niveles, lo que significa que su flujo de archivos solo se almacena en la nube, o parcialmente retirado, lo que significa que esa parte del archivo ya está en el disco. Si un archivo está totalmente recuperado en el disco, Azure File Sync lo convertirá de un archivo disperso a un archivo normal. |
        | L | Punto de repetición de análisis | Indica que el archivo tiene un punto de repetición de análisis, que es un puntero especial para su uso en un filtro de sistema de archivos. Azure File Sync usa puntos de repetición de análisis para definir el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) donde se almacenará el archivo en la nube. Esto permite un acceso ininterrumpido sin que el usuario final tenga que saber que se está usando Azure File Sync o cómo obtener acceso al archivo en el recurso compartido de Azure Files. Cuando un archivo completo se recupera, Azure File Sync quita el punto de repetición de análisis del archivo. |
        | O | Sin conexión | Indica que parte del contenido del archivo, o la totalidad, no se ha almacenado en el disco. Cuando un archivo completo se recupera, Azure File Sync quita este atributo. |

        ![Cuadro de diálogo Propiedades de un archivo con la pestaña Detalles seleccionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        También es posible ver los atributos de todos los archivos en una carpeta mediante la adición del campo **Atributos** a la presentación de la tabla del Explorador de archivos. Para ello, haga clic con el botón derecho en una columna existente (por ejemplo, Tamaño), seleccione **Más** y después **Atributos** en la lista desplegable.
        
    2. **Utilice `fsutil` para buscar puntos de repetición de análisis en un archivo.** Como se mencionó en la opción anterior, un archivo en niveles siempre tendrá establecido un punto de repetición de análisis, o un puntero especial al filtro de sistema de archivos de Azure File Sync (StorageSync.sys). Puede comprobar si un archivo tiene un punto de repetición de análisis con la utilidad `fsutil` en un símbolo del sistema con privilegios elevados o con una sesión de PowerShell:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Si el archivo tiene un punto de repetición de análisis, debería ver **Valor de la etiqueta de análisis: 0x8000001e**. Este valor hexadecimal es el valor de punto de repetición de análisis pertenecen Azure File Sync. La salida también contendrá los datos de repetición de análisis que representan la ruta de acceso al archivo en el recurso compartido de Azure Files.

        > [!Warning]  
        > El comando de la utilidad `fsutil reparsepoint` contiene también la posibilidad de eliminar un punto de repetición de análisis. No ejecute este comando a menos que lo indique el equipo de ingenieros de Azure File Sync, pues podría perder sus datos. 

* <a id="afs-recall-file"></a>**Uno de los archivos que quiero usar se ha organizado en niveles... ¿Cómo puedo recuperarlo en el disco para usarlo de forma local?**  
    La manera más fácil de recuperar un archivo en el disco es abrirlo. El filtro del sistema de archivos de Azure File Sync (StorageSync.sys) descargará sin problemas el archivo desde el recurso compartido de Azure Files sin tener que realizar ningún trabajo. Para los tipos de archivo que se pueden leer parcialmente, como archivos multimedia o archivos zip, si se abre un archivo, no dará como resultado la descarga de todo el archivo.

    También es posible forzar la recuperación de un archivo mediante PowerShell. Por ejemplo, esto puede resultar útil cuando se desea recuperar muchos archivos a la vez (por ejemplo, todos los archivos de una carpeta). Abra una sesión de PowerShell en el nodo del servidor donde está instalado Azure File Sync y ejecute los siguientes comandos de PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**¿Cómo puedo forzar la organización en niveles de un archivo o directorio?**  
    Cuando se habilita, la característica de organización en niveles en la nube creará automáticamente niveles en los archivos según la última hora de acceso y de modificación a fin de conseguir el porcentaje de espacio libre en el volumen especificado en el punto de conexión en la nube; sin embargo, en ocasiones, podría querer forzar un archivo para organizarse en niveles de forma manual. Por ejemplo, esto puede resultar útil cuando se guarda un archivo grande que no piensa volver a utilizar durante mucho tiempo y desea espacio libre en el volumen ahora para otros archivos o carpetas. Puede forzar la organización en niveles con los siguientes comandos de PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**¿Qué archivos o carpetas excluye automáticamente Azure File Sync?**
    De forma predeterminada, Azure File Sync excluye los archivos siguientes:
    
    - desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    Las siguientes carpetas también se excluyen de forma predeterminada:

    - \System Volume Information
    - \$RECYCLE.BIN
    - \SyncShareState

* <a id="afs-os-support"></a>**Me gustaría utilizar Azure File Sync con Windows Server 2008 R2, con Linux o con el dispositivo NAS. ¿Lo permite Azure File Sync?**
    En la actualidad, Azure File Sync solo admite Windows Server 2016 y Windows Server 2012 R2. En este momento, no tenemos otros planes para compartir, pero estamos abiertos e interesados en admitir plataformas adicionales según la demanda de los clientes. Comparta con nosotros qué plataformas le gustaría que fueran compatibles en [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Seguridad, autenticación y control de acceso
* <a id="ad-support"></a>**¿Admite Azure Files la autenticación y el control de acceso basados en Active Directory?**  
    Azure Files ofrece dos formas para administrar el control de acceso:

    - Con SAS se pueden generar tokens con permisos específicos que son válidos durante un intervalo de tiempo determinado. Por ejemplo, se puede generar un token con acceso de solo lectura a un archivo dado que expire al cabo de 10 minutos. Todos los usuarios que posean dicho token, mientras tenga validez, tendrán acceso de solo lectura al archivo durante esos 10 minutos. Solo se admiten claves SAS a través de las bibliotecas de cliente o de la API de REST, debe montar el recurso compartido de Azure Files a través de SMB con las claves de la cuenta de almacenamiento.

    - Azure File Sync conservará y replicará todas las listas de control de acceso (basadas en AD o locales) a todos los puntos de conexión del servidor a los que se sincroniza. Dado que Windows Server ya se autentica con Active Directory, Azure File Sync puede proporcionar una medida provisional excelente hasta que llegue la compatibilidad total con autenticación basada en AD y ACL.

    Azure Files no es directamente compatible con Active Directory en estos momentos.

* <a id="encryption-at-rest"></a>**¿Cómo se puede garantizar que el recurso compartido de Azure Files está cifrado en reposo?**  
    El cifrado en reposo se habilitará en breve de forma predeterminada en todas las regiones. En cuanto a estas regiones, no es necesario que haga nada para habilitar el cifrado. Si se trata de otras regiones, le recomendamos que consulte [Cifrado del lado servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**¿Cómo se puede proporcionar acceso a un archivo específico a través de un explorador web?**  
    Con SAS se pueden generar tokens con permisos específicos que son válidos durante un intervalo de tiempo determinado. Por ejemplo, puede generar un token con acceso de solo lectura a un archivo determinado para un período de tiempo específico. Cualquier persona que posea esta dirección URL puede obtener acceso al archivo directamente desde cualquier explorador web mientras sea válido. Las claves SAS se pueden generar fácilmente desde una interfaz de usuario, como el Explorador de Storage.

* <a id="file-level-permissions"></a>**¿Es posible especificar permisos de solo lectura o solo escritura en las carpetas del recurso compartido?**  
    Si el recurso compartido de archivos se monta a través de SMB, no se tiene este nivel de control sobre los permisos. Sin embargo, se puede lograr. Para ello, es preciso crear una firma de acceso compartido (SAS) mediante la API de REST o las bibliotecas de cliente.

* <a id="ip-restrictions"></a>**¿Puedo implementar restricciones de IP en un recurso compartido de archivos de Azure Files?**  
    Sí, es posible limitar el acceso al recurso compartido de archivos de Azure Files en un nivel de la cuenta de almacenamiento. Para obtener más información, consulte [Configuración de Firewalls y redes virtuales de Azure Storage](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**¿Cuáles son las directivas de cumplimiento de datos que admite Azure Files?**  
   Azure Files se ejecuta sobre la misma arquitectura de almacenamiento que otros servicios de almacenamiento de Azure Storage y aplica las mismas directivas de cumplimiento de datos. Para obtener más información sobre el cumplimiento de datos de Azure Storage, puede descargar y consultar el [documento Microsoft Azure Data Protection (Protección de datos de Microsoft Azure)](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) y el [Centro de confianza de Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Acceso local
* <a id="expressroute-not-required"></a>**¿Tengo que usar Azure ExpressRoute para conectarme a Azure Files o debo usar Azure File Sync en un entorno local?**  
    No, no es necesario ExpressRoute para acceder a un recurso compartido de Azure Files. Si está montando un recurso compartido de Azure Files directamente en un entorno local, todo lo que se necesita es tener abierto el puerto 445 (salida TCP) para acceder a Internet (este es el puerto SMB en el que se comunica). Si usa Azure File Sync, todo lo que se necesita es el puerto 443 (salida TCP) para el acceso HTTPS (no se necesita SMB). Sin embargo, se puede utilizar ExpressRoute con cualquier opción de acceso, si se desea.

* <a id="mount-locally"></a>**¿Cómo puedo montar un recurso compartido de archivos de Azure Files en mi máquina local?**  
    Puede montar el recurso compartido de archivos a través del protocolo SMB, siempre y cuando el puerto 445 (salida TCP) esté abierto y el cliente admita el protocolo SMB 3.0 (por ejemplo, si usa Windows 10 o Windows Server 2016). Si el puerto 445 está bloqueado por una directiva de su organización o por su ISP, puede utilizar Azure File Sync para acceder al recurso compartido de Azure Files.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>**¿Cómo puedo realizar una copia de seguridad de mi recurso compartido de Azure Files?**  
    Puede usar [instantáneas periódicas de recursos compartidos (versión preliminar)](storage-how-to-use-files-snapshots.md) para evitar cualquier eliminación accidental. Puede usar AzCopy, RoboCopy o una herramienta de copia de seguridad de terceros que pueda hacer una copia de seguridad de un recurso compartido de archivos montado. 

## <a name="share-snapshots"></a>Instantáneas de recursos compartido
### <a name="share-snapshots---general"></a>Instantáneas de recurso compartido - general
* <a id="what-are-snaphots"></a>**¿Qué es una instantánea de recurso compartido de archivos?**  
    Las instantáneas de recurso compartido de Azure Files permiten crear versiones de solo lectura de los recursos compartidos de archivos. También permite volver a copiar una versión anterior del contenido en el mismo recurso compartido o en una ubicación alternativa en Azure o de forma local para modificaciones posteriores. Para obtener más información sobre la instantánea de recurso compartido, consulte [Share Snapshot Overview](storage-snapshots-files.md) (Introducción a la instantánea de recurso compartido).

* <a id="where-are-snapshots-stored"></a>**¿Dónde se almacenan mis instantáneas de recurso compartido?**  
    Las instantáneas de recurso compartido se almacenan en la misma cuenta de almacenamiento que el recurso compartido de archivos.

* <a id="snapshot-perf-impact"></a>**¿Hay implicaciones de rendimiento?**  
    Las instantáneas de recurso compartido no tienen ninguna sobrecarga de rendimiento.

* <a id="snapshot-consistency"></a>**¿Son coherentes con la aplicación las instantáneas de recurso compartido?**  
    No. Las instantáneas de recurso compartido no son coherentes con la aplicación. El usuario tendrá que vaciar las escrituras de la aplicación en el recurso compartido antes de realizar la instantánea de recurso compartido.

* <a id="snapshot-limits"></a>**¿Hay límites en el número de instantáneas de recurso compartido?**  
    Azure Files solo puede conservar 200 instantáneas de recurso compartido. Las instantáneas de recurso compartido no cuentan en la cuota del recurso compartido así que no hay ningún límite de recurso compartido en el espacio total utilizado por todas las instantáneas de recurso compartido. Todavía se aplican los límites de la cuenta de almacenamiento. Después de 200 instantáneas de recurso compartido, las instantáneas más antiguas se eliminarán para crear nuevas instantáneas de recurso compartido.

### <a name="create-share-snapshots"></a>Creación de instantáneas de recurso compartido
* <a id="file-snaphsots"></a>**¿Puedo crear instantáneas de recurso compartido de archivos individuales?**  
    Las instantáneas de recurso compartido se crean en el nivel de recurso compartido de archivos. Puede restaurar archivos individuales desde la instantánea de recurso compartido de los archivos, pero no puede crear instantáneas de recurso compartido de nivel de archivo. Sin embargo, si ha realizado una instantánea de recurso compartido de nivel de recurso compartido y desea enumerar las instantáneas de recurso compartido donde ha cambiado un archivo determinado, puede hacerlo en la experiencia "Versiones anteriores" en un recurso compartido de montado en Windows. Si necesita una característica de instantánea de archivos en [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files), no dude en ponerse en contacto con nosotros.

* <a id="encypted-snapshots"></a>**¿Puedo crear instantáneas de recurso compartido a partir de un recurso compartido de archivos cifrados?**  
    Puede realizar una instantánea de recurso compartido de los recursos compartidos de Azure Files que tengan el cifrado en reposo habilitado. Puede restaurar archivos de una instantánea de recurso compartido a un recurso compartido de archivos cifrados. Si el recurso compartido está cifrado, también se cifrará la instantánea de recurso compartido.

* <a id="geo-redundant-snaphsots"></a>**¿Tienen mis instantáneas de recurso compartido redundancia geográfica?**
    La instantánea de recursos compartidos tendrá la misma redundancia que el recurso compartido de archivos de Azure Files para el que fue tomada. Si ha seleccionado almacenamiento con redundancia geográfica (GRS) para la cuenta de la instantánea de recurso compartido, también se almacenará de manera redundante en la región emparejada.

### <a name="manage-share-snapshots"></a>Administración de instantáneas de recurso compartido
* <a id="browse-snapshots-linux"></a>**¿Puedo examinar mis instantáneas de recurso compartido con Linux?**  
    Puede utilizar CLI de Azure 2.0 para crear, enumerar, examinar y restaurar en Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>**¿Puedo copiar las instantáneas de recurso compartido a una cuenta de almacenamiento diferente?**  
    Puede copiar archivos de instantáneas de recurso compartido en otra ubicación, pero no las mismas instantáneas.

### <a name="restore-data-from-share-snapshots"></a>Restauración de datos desde instantáneas de recurso compartido
* <a id="promote-share-snapshot"></a>**¿Puedo promover una instantánea de recurso compartido al recurso compartido de base?**  
    Solo puede copiar datos desde una instantánea de recurso compartido en cualquier destino deseado. Sin embargo, no puede promover una instantánea de recurso compartido al recurso compartido de base.

* <a id="restore-snapshotted-file-to-other-share"></a>**¿Puedo restaurar los datos desde mi instantánea de recurso compartido a otra cuenta de almacenamiento?**  
    Sí. Se pueden copiar los archivos de una instantánea de recurso compartido a la ubicación original o a una alternativa que incluye la misma cuenta de almacenamiento u otra diferente en las mismas regiones o en otras diferentes. También puede copiar archivos a un entorno local o a cualquier otra nube.    
  
### <a name="cleanup-share-snapshot"></a>Limpieza de instantánea de recurso compartido
* <a id="delete-share-keep-snapshots"></a>**¿Puedo eliminar mi recurso compartido pero no las instantáneas de recurso compartido?**
    No podrá eliminar el recurso compartido si tiene instantáneas de recurso compartido activas en el recurso. Hay una API para eliminar las instantáneas de recurso compartido junto con el recurso compartido y se puede conseguir lo mismo desde Azure Portal.

* <a id="delete-share-with-snapshots"></a>**¿Qué les ocurrirá a mis instantáneas de recurso compartido si elimino la cuenta de almacenamiento?**
    Si elimina la cuenta de almacenamiento, también se eliminarán las instantáneas de recurso compartido.

## <a name="billing-and-pricing"></a>Precios y facturación
* <a id="vm-file-share-network-traffic"></a>**¿El tráfico de red entre una máquina virtual de Azure y un recurso compartido de archivos de Azure Files cuenta como ancho de banda externo que se carga a la suscripción?**  
    Si el recurso compartido de archivos y la máquina virtual están en la misma región de Azure, el tráfico entre ellos es gratuito. Si se encuentran en regiones distintas, el tráfico entre ellos se cargará como ancho de banda externo.

* <a id="share-snapshot-price"></a>**¿Cuánto cuestan las instantáneas de recurso compartido?**
     No se cobrará la capacidad durante la versión preliminar de la instantánea de recurso compartido. Sin embargo, se seguirá aplicando la salida de Standard Storage y los costos de transacción. Después de la disponibilidad general, se cobrará tanto la capacidad como las transacciones en la instantánea de recurso compartido.
     
     Las instantáneas de recurso compartido son de naturaleza incremental. La instantánea de recurso compartido de base es el mismo recurso compartido. Todas las instantáneas de recurso compartido siguientes son incrementales y solo almacenarán la diferencia de la instantánea de recurso compartido anterior. Se le facturará únicamente por el contenido cambiado. Si tiene un recurso compartido con 100 GB de datos, pero solo se han cambiado 5 GB desde la última instantánea de recurso compartido, esa instantánea de recurso compartido consumirá solo 5 GB adicionales y se le facturarán por tanto 105 GB. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage/files/) para más información sobre los cargos de salida estándar y de transacciones.

## <a name="scale-and-performance"></a>Escala y rendimiento
* <a id="files-scale-limits"></a>**¿Cuáles son los límites de escala de Azure Files?**  
    Para obtener información sobre los objetivos de escalabilidad y rendimiento de Azure Files, consulte [Azure Files scalability and performance targets](storage-files-scale-targets.md) (Objetivos de escalabilidad y rendimiento de Azure Files).

* <a id="need-larger-share"></a>**Necesito un recurso compartido de archivos mayor que las opciones que ofrece Azure Files... ¿Podría aumentar el tamaño del recurso compartido de archivos de Azure Files?**  
    No, el tamaño máximo de un recurso compartido de archivos de Azure Files es de 5 TB. Actualmente, este es un límite estricto que no se puede ajustar. De todas formas, estamos trabajando en una solución que le permitirá aumentar el tamaño del recurso compartido a 100 TB, pero aún no sabemos cuándo estará preparada.

* <a id="open-handles-quota"></a>**¿Cuántos clientes pueden acceder al mismo archivo simultáneamente?**  
    Hay una cuota de 2000 identificadores abiertos en un único archivo. Una vez que tenga 2000 identificadores abiertos, se mostrará un error para indicar que se alcanzó la cuota.

* <a id="zip-slow-performance"></a>**Mi rendimiento era lento al intentar descomprimir archivos en Azure Files. ¿qué debo hacer?**  
    Para transferir una gran cantidad de archivos a Azure Files, es aconsejable usar AzCopy (Windows, versión preliminar para Linux/Unix) o Azure Powershell, ya que estas herramientas se han optimizado para la transferencia a través de red.

* <a id="slow-perf-windows-81-2012r2"></a>**He montado el recurso compartido de archivos de Azure Files en Windows Server 2012 R2 o Windows 8.1 y el rendimiento es lento. ¿Por qué?**  
    Hay un problema conocido al montar un recurso compartido de Azure Files en Windows Server 2012 R2 y Windows 8.1 que se ha revisado en la actualización acumulativa de abril de 2014 para Windows 8.1 y Windows Server 2012 R2. Asegúrese de que todas las instancias de Windows Server 2012 R2 y Windows 8.1 tienen esta revisión aplicada para un rendimiento óptimo (por supuesto, le recomendamos realizar siempre todas las revisiones de Windows a través de Windows Update). Para obtener más información, consulte el artículo asociado de Knowledge Base, [Rendimiento lento en el acceso a Azure Files desde Windows 8.1 o Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Características e interoperabilidad con otros servicios
* <a id="cluster-witness"></a>**¿Puedo usar mi recurso compartido de archivos de Azure Files como un *testigo del recurso compartido de archivo* para el clúster de conmutación por error de Windows Server?**  
    Esta situación no se admite actualmente para un recurso compartido de Azure Files. Para más información acerca de cómo configurar esta opción para Azure Blob Storage, consulte [Deploy a Cloud Witness for a Failover Cluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) (Implementación de un testigo en la nube para un clúster de conmutación por error).

* <a id="containers"></a>**¿Puedo montar un recurso compartido de archivos de Azure Files en una instancia de Azure Container Instances?**  
    Sí, los recursos compartidos de archivos de Azure Files son una opción fantástica para conservar información sin tener que depender de la duración de una instancia del contenedor. Si quiere obtener más información, consulte [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>**¿Hay alguna operación de cambio de nombre en la API de REST?**  
    De momento, no.

* <a id="nested-shares"></a>**¿Es posible tener recursos compartidos anidados, es decir, un recurso compartido en un recurso compartido?**  
    No. El recurso compartido de archivos es el controlador virtual que se puede montar, por lo que no se admiten recursos compartidos anidados.

* <a id="ibm-mq"></a>**¿Cómo se usa Azure Files con IBM MQ?**  
    IBM ha publicado un documento para guiar a los clientes de IBM MQ a la hora de configurar Azure Files con su servicio. Para más información, consulte [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Configuración del administrador de colas de varias instancias de IBM MQ con el servicio de archivos de Microsoft Azure).

## <a name="see-also"></a>Otras referencias
* [Solucione problemas de Azure Files en Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solución de problemas de Azure Files en Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solución de problemas de Azure File Sync (versión preliminar)](storage-sync-files-troubleshoot.md)