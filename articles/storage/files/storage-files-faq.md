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
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: 0bcf56e06c34af94746d42d8af18e32fcd9a7496
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Preguntas más frecuentes sobre Azure Files
[Azure Files](storage-files-introduction.md) le ofrece recursos compartidos de archivos en la nube totalmente administrados, a los que se puede obtener acceso mediante el protocolo de [Bloque de mensajes del servidor (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) estándar, también conocido como Sistema de archivos de Internet común o CIFS. Los recursos compartidos de archivos de Azure se pueden montar simultáneamente en implementaciones de Windows, Linux y macOS en la nube o locales. También puede almacenar en caché recursos compartidos de archivos de Azure en equipos de Windows Server mediante el uso de Azure File Sync (versión preliminar) para tener un acceso rápido cerca de donde se usan los datos.

En este artículo se responden las preguntas más frecuentes sobre las características y las funcionalidades de Azure Files, incluido el uso de Azure File Sync con Azure Files. Si no encuentra una respuesta a su pregunta, póngase en contacto con nosotros mediante los siguientes canales (en orden incremental):

1. La sección Comentarios de este artículo.
2. [Foro de Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Soporte técnico de Microsoft. Para crear una solicitud de soporte técnico, en Azure Portal, vaya a la pestaña **Ayuda**, seleccione el botón **Ayuda y soporte técnico** y elija **Nueva solicitud de soporte técnico**.

## <a name="general"></a>General
* <a id="why-files-useful"></a>**¿Por qué es útil Azure Files?**  
   Puede usar Azure Files para crear recursos compartidos de archivos en la nube, sin tener que administrar la sobrecarga de un servidor físico o dispositivo. El trabajo más monótono se realiza automáticamente, como aplicar las actualizaciones del sistema operativo y reemplazar los discos incorrectos. Para obtener más información sobre los escenarios en los que Azure Files puede ayudarle, vea [¿Por qué es útil Azure Files?](storage-files-introduction.md#why-azure-files-is-useful)

* <a id="file-access-options"></a>**¿Cuáles son las diferentes formas de obtener acceso a los archivos de Azure Files?**  
    Puede montar el recurso compartido de archivos en la máquina local mediante el protocolo SMB 3.0, o bien puede usar herramientas como el [Explorador de Storage](http://storageexplorer.com/) para obtener acceso a los archivos en el recurso compartido de archivos. Desde la aplicación, puede usar las bibliotecas de cliente de almacenamiento, las API de REST, PowerShell o la CLI de Azure para obtener acceso a los archivos en el recurso compartido de archivos de Azure.

* <a id="what-is-afs"></a>**¿Qué es Azure File Sync?**  
    Puede usar Azure File Sync para centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Azure File Sync transforma las máquinas de Windows Server en una caché rápida de los recursos compartidos de archivos de Azure. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a sus datos localmente, como SMB, Network File System (NFS) y el servicio de protocolo de transferencia de archivos (FTPS). Puede tener todas las cachés que necesite en todo el mundo.

* <a id="files-versus-blobs"></a>**¿Por qué debo usar para mis datos un recurso compartido de archivos de Azure en vez de Azure Blob Storage?**  
    Tanto Azure Files como Azure Blob Storage ofrecen una manera de almacenar grandes cantidades de datos en la nube, pero son útiles para fines ligeramente diferentes. 
    
    Azure Blob Storage es útil para aplicaciones de gran escala y nativas de la nube que deben almacenar datos no estructurados. Para maximizar el rendimiento y la escala, Azure Blob Storage es una abstracción de almacenamiento más sencilla que un sistema de archivos verdadero. Solo puede tener acceso a Azure Blob Storage a través de bibliotecas de cliente basadas en REST (o directamente mediante el protocolo basado en REST).

    Azure Files es específicamente un sistema de archivos. Azure Files tiene todos los resúmenes de archivo con los que está familiarizado después de trabajar durante años con sistemas operativos locales. Al igual que Azure Blob Storage, Azure Files ofrece una interfaz de REST y bibliotecas de cliente basadas en REST. A diferencia de Azure Blob Storage, Azure Files ofrece a SMB acceso a recursos compartidos de archivos de Azure. Mediante el uso de SMB, puede montar directamente un recurso compartido de archivos de Azure en Windows, Linux o macOS, ya sea en máquinas virtuales locales como en la nube, sin tener que escribir ningún código ni adjuntar controladores especiales al sistema de archivos. También puede almacenar en caché recursos compartidos de archivos de Azure en servidores de archivos locales mediante el uso de Azure File Sync para tener un acceso rápido cerca de donde se usan los datos. 
   
    Para obtener una explicación más detallada sobre las diferencias entre Azure Files y Azure Blob Storage, vea [Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber más de Azure Blob Storage, consulte [Introducción a Blob Storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**¿Por qué debo usar un recurso compartido de archivos de Azure en vez de Azure Disks?**  
    Un disco en Azure Disks no es más que un disco. Un disco independiente por sí mismo no resulta muy útil. Para sacar provecho de Azure Disks, tendrá que conectarlo a una máquina virtual que se ejecute en Azure. Azure Disks puede usarse para todo aquello para lo que quiera usar un disco en un servidor local. Puede usarlo como disco del sistema operativo, como espacio de intercambio para un sistema operativo, o como almacenamiento dedicado para una aplicación. Un uso interesante de Azure Disks consiste en crear un servidor de archivos en la nube para usarlo en los mismos lugares en los que usaría un recurso compartido de archivos de Azure. Implementar un servidor de archivos en las máquinas virtuales de Azure es una manera de alto rendimiento de obtener almacenamiento de archivos en Azure cuando necesite opciones de implementación no compatibles actualmente con Azure Files (por ejemplo, la compatibilidad con el protocolo NFS o almacenamiento premium). 

    Pero la ejecución de un servidor de archivos con Azure Disks como almacenamiento back-end suele ser mucho más costosa que el uso de un recurso compartido de archivos de Azure por diversos motivos. En primer lugar, además de pagar por el almacenamiento en disco, también debe pagar por el gasto de ejecutar una o más máquinas virtuales de Azure. En segundo lugar, también debe administrar las máquinas virtuales que se usan para ejecutar el servidor de archivos. Por ejemplo, usted es el responsable de las actualizaciones del sistema operativo. Por último, si finalmente se necesita que los datos se almacenen en la caché local, el usuario tiene la última palabra para configurar y administrar las tecnologías de replicación (como la Replicación del sistema de archivos distribuido, o DFSR) para que esto ocurra.

    Un enfoque para obtener lo mejor tanto de Azure Files como de un servidor de archivos hospedado en máquinas virtuales de Azure (además de usar Azure Disks como almacenamiento de back-end) consiste en instalar Azure File Sync en un servidor de archivos hospedado en máquina virtual en la nube. Si el recurso compartido de archivos de Azure se encuentra en la misma región que el servidor de archivos, puede habilitar los niveles en la nube y establecer un porcentaje de espacio libre en el volumen al máximo (99 %). Esto garantiza la duplicación mínima de datos. También puede usar todas las aplicaciones que quiera con los servidores de archivos, como aplicaciones que requieren compatibilidad con el protocolo NFS.

    Para obtener más información sobre una opción para configurar un elevado rendimiento y un servidor de archivos que tenga una alta disponibilidad en Azure, vea [Deploying IaaS VM guest clusters in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/) (Implementación de clústeres invitados de máquinas virtuales de IaaS en Microsoft Azure). Para obtener una descripción más detallada sobre las diferencias entre Azure Files y Azure Disks, vea [Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para obtener más información sobre Azure Disks, vea [Introducción a Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**¿Cómo puedo empezar a usar Azure Files?**  
   Empezar a trabajar con Azure Files es fácil. En primer lugar, [cree un recurso compartido de archivos](storage-how-to-create-file-share.md) y, después, móntelo en su sistema operativo preferido: 

    * [Montaje en Windows](storage-how-to-use-files-windows.md)
    * [Montaje en Linux](storage-how-to-use-files-linux.md)
    * [Montaje en macOS](storage-how-to-use-files-mac.md)

   Para obtener información más detallada sobre cómo implementar un recurso compartido de archivos de Azure para reemplazar los recursos compartidos de archivos de producción en su organización, vea [Planeamiento de una implementación de Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>**¿Qué opciones de redundancia de almacenamiento son compatibles con Azure Files?**  
    Actualmente, Azure Files solo admite almacenamiento con redundancia local (LRS) o almacenamiento con redundancia geográfica (GRS). Pensamos admitir en el futuro el almacenamiento con redundancia de zona (ZRS) y el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS), pero aún no sabemos cuándo estará disponible.

* <a id="tier-options"></a>**¿Qué capas de almacenamiento son compatibles con Azure Files?**  
    Actualmente, Azure Files solo admite la capa de almacenamiento estándar. En este momento, aún no sabemos cuándo serán compatibles el almacenamiento premium y el almacenamiento de acceso esporádico. 
    
    > [!NOTE]
    > No se pueden crear recursos compartidos de archivos de Azure desde cuentas de almacenamiento que sean solo para blobs o desde cuentas de almacenamiento premium.

* <a id="give-us-feedback"></a>**Me gustaría que se agregara una característica específica a Azure Files. ¿Pueden agregarla?**  
    El equipo de Azure Files está interesado en conocer todos los comentarios que tenga sobre nuestro servicio. Vote en las solicitudes de características en [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files). Estamos deseando entusiasmarle con muchas características nuevas.

## <a name="azure-file-sync"></a>Azure File Sync
* <a id="afs-region-availability"></a>**¿Qué regiones se admiten en Azure File Sync (versión preliminar)?**  
    Actualmente, Azure File Sync está disponible en el Oeste de EE. UU., Europa Occidental, Este de Australia y el Sudeste Asiático. Se agregará compatibilidad con más regiones mientras se trabaja con vistas a la disponibilidad general. Para obtener más información, vea [Disponibilidad en regiones](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>**¿Puedo tener servidores unidos a un dominio y no unidos a un dominio en el mismo grupo de sincronización?**  
    Sí. Un grupo de sincronización puede contener puntos de conexión de servidor que tienen pertenencias diferentes de Active Directory, incluso aunque no estén unidos a dominio. Aunque técnicamente la configuración funciona, no se recomienda como configuración normal, ya que las listas de control de acceso (ACL) que se definen para los archivos y carpetas de un servidor no podrán aplicarse a otros servidores del grupo de sincronización. Para obtener mejores resultados, se recomienda realizar la sincronización entre servidores en el mismo bosque de Active Directory, entre servidores en bosques distintos de Active Directory con relaciones de confianza establecidas o entre servidores que no están en un dominio. Se recomienda no usar una combinación de estas configuraciones.

* <a id="afs-change-detection"></a>**He creado un archivo directamente en el recurso compartido de archivos de Azure mediante SMB o en el portal. ¿Cuánto tiempo tarda el archivo en sincronizarse con los servidores del grupo de sincronización?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Si se cambia el mismo archivo en dos servidores aproximadamente al mismo tiempo, ¿qué sucede?**  
    Azure File Sync usa una estrategia simple de resolución de conflictos: conservamos los cambios realizados en los archivos que se modifican en dos servidores al mismo tiempo. El cambio de escritura más reciente mantiene el nombre de archivo original. El archivo anterior tiene la máquina de "origen" y el número de conflicto anexados al nombre. Sigue esta taxonomía: 
   
    \<NombreDeArchivoSinExtensión\>-\<NombreDeMáquina\>\[-#\].\<ext\>  

    Por ejemplo, el primer conflicto de CompanyReport.docx se convertiría en CompanyReport-CentralServer.docx si CentralServer es donde se ha producido la operación de escritura anterior. El segundo conflicto se denominará CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>**¿Se admite el almacenamiento con redundancia geográfica en Azure File Sync?**  
    Sí, Azure Files admite almacenamiento con redundancia local (LRS) y almacenamiento con redundancia geográfica (GRS). Si se produce una conmutación por error de GRS entre regiones emparejadas, se recomienda tratar la región nueva como una copia de seguridad de datos únicamente. Azure File Sync no inicia automáticamente la sincronización con la nueva región principal. 

* <a id="sizeondisk-versus-size"></a>**¿Por qué la propiedad *Tamaño en disco* de un archivo no coincide con la propiedad *Tamaño* después de usar Azure File Sync?**  
    El Explorador de archivos de Windows expone dos propiedades para representar el tamaño de un archivo: **Tamaño** y **Tamaño en disco**. Estas propiedades difieren ligeramente en significado. **Tamaño** representa el tamaño completo del archivo. **Tamaño en disco** representa el tamaño del flujo de archivos que se almacena en el disco. Los valores de estas propiedades pueden diferir por diversos motivos, como la compresión, el uso de la desduplicación de datos o los niveles en la nube con Azure File Sync. Si un archivo está organizado en niveles en un recurso compartido de archivos de Azure, el tamaño en disco será cero, ya que la secuencia de archivos se almacena en el recurso compartido de archivos de Azure, no en el disco. También es posible que un archivo se encuentre parcialmente organizado en niveles (o parcialmente recuperado). En el caso de un archivo parcialmente organizado en niveles, parte del archivo está en el disco. Esto puede ocurrir cuando aplicaciones como reproductores multimedia o utilidades zip leen parcialmente los archivos. 

* <a id="is-my-file-tiered"></a>**¿Cómo se puede saber si un archivo se ha organizado en niveles?**  
    Hay varias maneras de comprobar si un archivo se ha organizado en niveles en el recurso compartido de archivos de Azure:
    
   *  **Compruebe los atributos de archivo en el archivo.**
     Para ello, haga clic con el botón derecho en un archivo, vaya a **Detalles** y desplácese hacia abajo a la propiedad **Attributes**. Un archivo en niveles tendrá los siguientes atributos establecidos:     
        
        | Letra del atributo | Atributo | Definición |
        |:----------------:|-----------|------------|
        | Una  | Archivar | Indica que se debe realizar la copia de seguridad del archivo con un software de copia de seguridad. Este atributo siempre se establece, independientemente de si el archivo está organizado en niveles o está completamente almacenado en el disco. |
        | P | Archivos dispersos | Indica que el archivo es un archivo disperso. Un archivo disperso es un tipo especializado de archivo que ofrece NTFS para un uso eficaz cuando el archivo en el flujo del disco está en gran parte vacío. Azure File Sync usa archivos dispersos porque un archivo o está completamente organizado en niveles o parcialmente recuperado. En un archivo completamente organizado en niveles, su flujo de archivos se almacena en la nube. En un archivo parcialmente recuperado, esa parte del archivo ya está en el disco. Si un archivo está totalmente recuperado en el disco, Azure File Sync lo convierte de un archivo disperso a un archivo normal. |
        | L | Punto de repetición de análisis | Indica que el archivo tiene un punto de repetición de análisis. Un punto de repetición de análisis es un puntero especial para su uso en un filtro del sistema de archivos. Azure File Sync usa puntos de repetición de análisis a fin de definir para el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) la ubicación en la nube donde está almacenado el archivo. Esto permite el acceso sin problemas. Los usuarios no necesitan saber que se está usando Azure File Sync ni cómo obtener acceso al archivo en el recurso compartido de archivos de Azure. Cuando un archivo completo se recupera, Azure File Sync quita el punto de repetición de análisis del archivo. |
        | O | Sin conexión | Indica que parte del contenido del archivo, o la totalidad, no se ha almacenado en el disco. Cuando un archivo completo se recupera, Azure File Sync quita este atributo. |

        ![Cuadro de diálogo Propiedades de un archivo con la pestaña Detalles seleccionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Puede ver los atributos de todos los archivos en una carpeta mediante la adición del campo **Atributos** a la presentación de la tabla del Explorador de archivos. Para ello, haga clic con el botón derecho en una columna existente (por ejemplo, **Tamaño**), seleccione **Más** y, después, **Atributos** en la lista desplegable.
        
   * **Use `fsutil` para buscar puntos de repetición de análisis en un archivo.**
       Como se indica en la opción anterior, un archivo organizado en niveles siempre tiene establecido un punto de repetición de análisis. Un puntero de repetición de análisis es un puntero especial para el filtro del sistema de archivos de Azure File Sync (StorageSync.sys). Para comprobar si un archivo tiene un punto de repetición de análisis, en un símbolo del sistema con privilegios elevados o en una ventana de PowerShell, ejecute la utilidad `fsutil`:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Si el archivo tiene un punto de repetición de análisis, debería ver **Valor de la etiqueta de análisis: 0x8000001e**. Este valor hexadecimal es el valor del punto de repetición de análisis que pertenece a Azure File Sync. La salida también contendrá los datos de repetición de análisis que representan la ruta de acceso al archivo en el recurso compartido de archivos de Azure.

        > [!WARNING]  
        > El comando de la utilidad `fsutil reparsepoint` también puede eliminar un punto de repetición de análisis. No ejecute este comando a menos que el equipo de ingeniería de Azure File Sync se lo indique. Si lo ejecuta, podría producirse una pérdida de datos. 

* <a id="afs-recall-file"></a>**Uno de los archivos que quiero usar se ha organizado en niveles. ¿Cómo puedo recuperarlo en el disco para usarlo de forma local?**  
    La manera más fácil de recuperar un archivo en el disco es abrirlo. El filtro del sistema de archivos de Azure File Sync (StorageSync.sys) descarga sin problemas el archivo desde el recurso compartido de archivos de Azure de forma automática. Para los tipos de archivo que se pueden leer parcialmente, como los archivos multimedia o los archivos .zip, si se abre un archivo, este no se descargará entero.

    También puede usar PowerShell para forzar que se recupere un archivo. Esta opción puede resultar útil cuando se quiere recuperar muchos archivos a la vez, como todos los archivos de una carpeta. Abra una sesión de PowerShell en el nodo del servidor donde está instalado Azure File Sync y ejecute los siguientes comandos de PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**¿Cómo puedo forzar la organización en niveles de un archivo o directorio?**  
    Cuando se habilita la característica de organización en niveles en la nube, se crean automáticamente niveles en los archivos según la última hora de acceso y de modificación a fin de conseguir el porcentaje de espacio libre en el volumen especificado en el punto de conexión en la nube. Aun así, en ocasiones podría querer forzar un archivo para organizarse en niveles de forma manual. Esto puede resultar útil cuando se guarda un archivo grande que no piensa volver a usar durante mucho tiempo y quiere espacio libre en el volumen ahora para usarlo para otros archivos o carpetas. Puede forzar la organización en niveles con los siguientes comandos de PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>**¿Cómo se interpreta el *espacio disponible del volumen* cuando tengo varios puntos de conexión de servidor en un volumen?**  
    Cuando hay más de un punto de conexión de servidor en un volumen, el umbral de espacio disponible del volumen efectivo es el mayor espacio disponible del volumen especificado en cualquier punto de conexión de servidor de ese volumen. Los archivos se pueden almacenar en capas según sus patrones de uso, independientemente de qué punto de conexión de servidor al que pertenezcan. Por ejemplo, si tiene dos puntos de conexión de servidor en un volumen, Endpoint1 y Endpoint2, donde Endpoint1 tiene un umbral de espacio disponible del volumen del 25 %, y Endpoint2 tiene un umbral de espacio disponible del volumen de 50 %, el umbral del volumen de espacio disponible para ambos puntos de conexión de servidor será el 50 %.

* <a id="afs-files-excluded"></a>**¿Qué archivos o carpetas excluye automáticamente Azure File Sync?**  
    De forma predeterminada, Azure File Sync excluye los archivos siguientes:
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Las siguientes carpetas también se excluyen de forma predeterminada:

    * \System Volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>**¿Puedo usar Azure File Sync con Windows Server 2008 R2, Linux o un dispositivo de almacenamiento conectado a la red (NAS)?**  
    En la actualidad, Azure File Sync solo admite Windows Server 2016 y Windows Server 2012 R2. En este momento, no tenemos otros planes para compartir, pero estamos dispuestos a admitir plataformas adicionales según la demanda de los clientes. Indíquenos a través de [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files) qué plataformas le gustaría que fueran compatibles.

## <a name="security-authentication-and-access-control"></a>Seguridad, autenticación y control de acceso
* <a id="ad-support"></a>**¿Admite Azure Files la autenticación y el control de acceso basados en Active Directory?**  
    Azure Files ofrece dos formas de administrar el control de acceso:

    - Puede usar firmas de acceso compartido (SAS) para generar tokens que tengan permisos específicos y que sean válidos para un intervalo de tiempo específico. Por ejemplo, se puede generar un token con acceso de solo lectura a un archivo específico que expire al cabo de 10 minutos. Todos los usuarios que posean dicho token, mientras tenga validez, tendrán acceso de solo lectura al archivo durante esos 10 minutos. Actualmente, solo se admiten claves de firma de acceso compartido a través de la API de REST o en bibliotecas de cliente. Debe montar el recurso compartido de archivos de Azure a través de SMB mediante el uso de las claves de cuenta de almacenamiento.

    - Azure File Sync conserva y replica todas las ACL discrecionales, o DACL, locales o basadas en Active Directory en todos los puntos de conexión de servidor con los que se sincroniza. Dado que Windows Server ya se autentica con Active Directory, Azure File Sync es una opción provisional eficaz hasta que llegue la compatibilidad total con autenticación basada en Active Directory y ACL.

    En la actualidad, Azure Files no es directamente compatible con Active Directory.

* <a id="encryption-at-rest"></a>**¿Cómo se puede garantizar que el recurso compartido de archivos de Azure está cifrado en reposo?**  
    El cifrado del servicio Azure Storage se habilitará en breve de forma predeterminada en todas las regiones. En cuanto a estas regiones, no es necesario que haga nada para habilitar el cifrado. Si se trata de otras regiones, vea la información sobre el [cifrado del lado servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**¿Cómo se puede proporcionar acceso a un archivo específico a través de un explorador web?**  
    Puede usar firmas de acceso compartido para generar tokens que tengan permisos específicos y que sean válidos para un intervalo de tiempo específico. Por ejemplo, puede generar un token que proporcione acceso de solo lectura a un archivo específico durante un período de tiempo determinado. Cualquier persona que posea la dirección URL puede obtener acceso al archivo directamente desde cualquier explorador web mientras el token sea válido. Puede generar fácilmente una clave de firma de acceso compartido desde una interfaz de usuario, como el Explorador de Storage.

* <a id="file-level-permissions"></a>**¿Es posible especificar permisos de solo lectura o solo escritura en las carpetas del recurso compartido?**  
    Si monta el recurso compartido de archivos a través de SMB, no tendrá control de nivel de carpeta sobre los permisos. Pero si crea una firma de acceso compartido mediante la API de REST o bibliotecas de cliente, puede especificar permisos de solo lectura o de solo escritura en las carpetas del recurso compartido.

* <a id="ip-restrictions"></a>**¿Puedo implementar restricciones de IP en un recurso compartido de archivos de Azure?**  
    Sí. Es posible limitar el acceso al recurso compartido de archivos de Azure en el nivel de cuenta de almacenamiento. Para más información, vea [Configuración de Firewalls y redes virtuales de Azure Storage](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**¿Qué directivas de cumplimiento de datos admite Azure Files?**  
   Azure Files se ejecuta sobre la misma arquitectura de almacenamiento que se usa en otros servicios de almacenamiento de Azure Storage. Azure Files aplica las mismas directivas de cumplimiento de datos que se usan en otros servicios de Azure Storage. Para obtener más información sobre el cumplimiento de datos de Azure Storage, puede descargar y consultar el [documento Microsoft Azure Data Protection](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) (Protección de datos de Microsoft Azure) e ir al [Centro de confianza de Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Acceso local
* <a id="expressroute-not-required"></a>**¿Tengo que usar Azure ExpressRoute para conectarme a Azure Files o debo usar Azure File Sync en un entorno local?**  
    No. No es necesario ExpressRoute para obtener acceso a un recurso compartido de archivos de Azure. Si está montando un recurso compartido de archivos de Azure directamente en un entorno local, lo único que se necesita es tener abierto el puerto 445 (salida TCP) para tener acceso a Internet (este es el puerto que SMB usa para comunicarse). Si usa Azure File Sync, lo único que se necesita es el puerto 443 (salida TCP) para el acceso HTTPS (no se necesita SMB). Pero *puede usar* ExpressRoute con cualquiera de estas opciones de acceso.

* <a id="mount-locally"></a>**¿Cómo puedo montar un recurso compartido de archivos de Azure en mi máquina local?**  
    Puede montar el recurso compartido de archivos a través del protocolo SMB, siempre y cuando el puerto 445 (salida TCP) esté abierto y el cliente admita el protocolo SMB 3.0 (por ejemplo, si usa Windows 10 o Windows Server 2016). Si el puerto 445 está bloqueado por una directiva de su organización o por su ISP, puede usar Azure File Sync para obtener acceso al recurso compartido de archivos de Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>**¿Cómo puedo realizar una copia de seguridad de mi recurso compartido de archivos de Azure?**  
    Puede usar [instantáneas periódicas de recursos compartidos (versión preliminar)](storage-how-to-use-files-snapshots.md) para evitar cualquier eliminación accidental. También puede usar AzCopy, Robocopy o una herramienta de copia de seguridad de terceros que pueda hacer una copia de seguridad de un recurso compartido de archivos montado. 

## <a name="share-snapshots"></a>Instantáneas de recursos compartido
### <a name="share-snapshots-general"></a>Instantáneas de recurso compartido: general
* <a id="what-are-snaphots"></a>**¿Qué son las instantáneas de recursos compartidos de archivos?**  
    Puede usar las instantáneas de recurso compartido de archivos de Azure para crear versiones de solo lectura de los recursos compartidos de archivos. También puede usar Azure Files para copiar una versión anterior del contenido en el mismo recurso compartido, en una ubicación alternativa en Azure o de forma local para modificaciones posteriores. Para obtener más información sobre las instantáneas de recurso compartido, vea [Información general de las instantáneas de recurso compartido](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>**¿Dónde se almacenan mis instantáneas de recurso compartido?**  
    Las instantáneas de recurso compartido se almacenan en la misma cuenta de almacenamiento que el recurso compartido de archivos.

* <a id="snapshot-perf-impact"></a>**¿Afecta al rendimiento el uso de instantáneas de recurso compartido?**  
    Las instantáneas de recurso compartido no tienen ninguna sobrecarga de rendimiento.

* <a id="snapshot-consistency"></a>**¿Son coherentes con la aplicación las instantáneas de recurso compartido?**  
    No, las instantáneas de recurso compartido no son coherentes con la aplicación. El usuario debe vaciar las escrituras de la aplicación en el recurso compartido antes de realizar la instantánea de recurso compartido.

* <a id="snapshot-limits"></a>**¿Hay límites en el número de instantáneas de recurso compartido que se pueden usar?**  
    Sí. Azure Files puede retener un máximo de 200 instantáneas de recurso compartido. Las instantáneas de recurso compartido no cuentan en la cuota del recurso compartido, así que no hay ningún límite de recurso compartido en el espacio total usado por todas las instantáneas de recurso compartido. Los límites de cuenta de almacenamiento se siguen aplicando. Una vez que llegue a las 200 instantáneas de recurso compartido, debe eliminar las instantáneas más antiguas para poder crear otras.

### <a name="create-share-snapshots"></a>Creación de instantáneas de recurso compartido
* <a id="file-snaphsots"></a>**¿Puedo crear instantáneas de recurso compartido de archivos individuales?**  
    Las instantáneas de recurso compartido se crean en el nivel de recurso compartido de archivos. Puede restaurar archivos individuales desde la instantánea de recurso compartido de archivos, pero no puede crear instantáneas de recurso compartido de nivel de archivo. Aun así, si ha realizado una instantánea de recurso compartido de nivel de recurso compartido y quiere enumerar las instantáneas de recurso compartido en las que ha cambiado un archivo determinado, puede hacerlo en **Versiones anteriores** en un recurso compartido montado en Windows. 
    
    Si necesita una característica de instantánea de archivos, indíquenoslo a través de [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>**¿Puedo crear instantáneas de recurso compartido a partir de un recurso compartido de archivos cifrados?**  
    Puede realizar una instantánea de recurso compartido de los recursos compartidos de archivos de Azure que tengan el cifrado en reposo habilitado. Puede restaurar archivos de una instantánea de recurso compartido a un recurso compartido de archivos cifrados. Si el recurso compartido está cifrado, la instantánea de recurso compartido también estará cifrada.

* <a id="geo-redundant-snaphsots"></a>**¿Tienen mis instantáneas de recurso compartido redundancia geográfica?**  
    Las instantáneas de recurso compartido tienen la misma redundancia que el recurso compartido de archivos de Azure para el que se han tomado. Si ha seleccionado el almacenamiento con redundancia geográfica para la cuenta, la instantánea de recurso compartido también se almacena de manera redundante en la región emparejada.

### <a name="manage-share-snapshots"></a>Administración de instantáneas de recurso compartido
* <a id="browse-snapshots-linux"></a>**¿Puedo examinar mis instantáneas de recurso compartido con Linux?**  
    Puede usar la CLI de Azure 2.0 para crear, enumerar, examinar y restaurar instantáneas de recurso compartido en Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>**¿Puedo copiar las instantáneas de recurso compartido a una cuenta de almacenamiento diferente?**  
    Puede copiar archivos desde instantáneas de recurso compartido en otra ubicación, pero no puede copiar las instantáneas en sí mismas.

### <a name="restore-data-from-share-snapshots"></a>Restauración de datos desde instantáneas de recurso compartido
* <a id="promote-share-snapshot"></a>**¿Puedo promover una instantánea de recurso compartido al recurso compartido de base?**  
    Puede copiar datos desde una instantánea de recurso compartido en otro destino, pero no puede promover una instantánea de recurso compartido al recurso compartido de base.

* <a id="restore-snapshotted-file-to-other-share"></a>**¿Puedo restaurar los datos desde mi instantánea de recurso compartido a otra cuenta de almacenamiento?**  
    Sí. Los archivos de una instantánea de recurso compartido se pueden copiar en la ubicación original o en una ubicación alternativa que incluya la misma cuenta de almacenamiento o una cuenta de almacenamiento diferente, ya sea en la misma región o en regiones diferentes. También puede copiar archivos en una ubicación local o en cualquier otra nube.    
  
### <a name="clean-up-share-snapshots"></a>Limpiar instantáneas de recurso compartido
* <a id="delete-share-keep-snapshots"></a>**¿Puedo eliminar mi recurso compartido pero no las instantáneas de recurso compartido?**  
    Si tiene instantáneas de recurso compartido activas en el recurso, no puede eliminar el recurso compartido. Puede usar una API para eliminar instantáneas de recurso compartido junto con el recurso compartido. También puede eliminar las instantáneas de recurso compartido y el recurso compartido en Azure Portal.

* <a id="delete-share-with-snapshots"></a>**¿Qué les ocurrirá a mis instantáneas de recurso compartido si elimino la cuenta de almacenamiento?**  
    Si elimina la cuenta de almacenamiento, también se eliminarán las instantáneas de recurso compartido.

## <a name="billing-and-pricing"></a>Precios y facturación
* <a id="vm-file-share-network-traffic"></a>**¿El tráfico de red entre una máquina virtual de Azure y un recurso compartido de archivos de Azure cuenta como ancho de banda externo que se carga a la suscripción?**  
    Si el recurso compartido de archivos y la máquina virtual están en la misma región de Azure, no hay ningún cargo adicional por el tráfico entre el recurso compartido de archivos y la máquina virtual. Si el recurso compartido de archivos y la máquina virtual se encuentran en distintas regiones, el tráfico entre ellos se cargará como ancho de banda externo.

* <a id="share-snapshot-price"></a>**¿Cuánto cuestan las instantáneas de recurso compartido?**  
     Durante la versión preliminar, no hay ningún cargo por la característica de instantánea de recurso compartido. Se seguirá aplicando la salida de almacenamiento estándar y los costos de transacción. Después de la disponibilidad general, se cobrará a las suscripciones la capacidad y las transacciones en las instantáneas de recurso compartido.
     
     Las instantáneas de recurso compartido son de naturaleza incremental. La instantánea de recurso compartido de base es el mismo recurso compartido. Todas las instantáneas de recurso compartido siguientes son incrementales y solo almacenan la diferencia de la instantánea de recurso compartido anterior. Se le facturará únicamente por el contenido cambiado. Si tiene un recurso compartido con 100 GB de datos, pero solo se han cambiado 5 GB desde la última instantánea de recurso compartido, esa instantánea de recurso compartido consumirá solo 5 GB adicionales y se le facturarán por tanto 105 GB. Para obtener más información sobre los cargos de salida estándar y de transacciones, vea la [página de precios](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Escala y rendimiento
* <a id="files-scale-limits"></a>**¿Cuáles son los límites de escala de Azure Files?**  
    Para obtener información sobre los objetivos de escalabilidad y rendimiento de Azure Files, vea [Objetivos de escalabilidad y rendimiento de Azure Files](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>**Necesito un recurso compartido de archivos mayor que las opciones que ofrece Azure Files. ¿Puedo aumentar el tamaño del recurso compartido de archivos de Azure?**  
    No. El tamaño máximo de un recurso compartido de archivos de Azure es de 5 TB. Actualmente, este es un límite estricto que no se puede ajustar. Estamos trabajando en una solución que le permitirá aumentar el tamaño del recurso compartido a 100 TB, pero aún no sabemos cuándo estará preparada.

* <a id="open-handles-quota"></a>**¿Cuántos clientes pueden obtener acceso al mismo archivo simultáneamente?**   
    Hay una cuota de 2000 identificadores abiertos en un único archivo. Si tiene 2000 identificadores abiertos, se muestra un mensaje de error que indica que se ha alcanzado la cuota.

* <a id="zip-slow-performance"></a>**El rendimiento es lento cuando descomprimo archivos en Azure Files. ¿qué debo hacer?**  
    Para transferir una gran cantidad de archivos a Azure Files, es aconsejable usar AzCopy (para Windows; en versión preliminar para Linux y UNIX) o Azure PowerShell. Estas herramientas se han optimizado para la transferencia a través de red.

* <a id="slow-perf-windows-81-2012r2"></a>**He montado el recurso compartido de archivos de Azure en Windows Server 2012 R2 o Windows 8.1 y el rendimiento es lento. ¿Por qué?**  
    Hay un problema conocido al montar un recurso compartido de archivos de Azure en Windows Server 2012 R2 y Windows 8.1. El problema se ha revisado en la actualización acumulativa de abril de 2014 para Windows 8.1 y Windows Server 2012 R2. Para obtener un rendimiento óptimo, asegúrese de que todas las instancias de Windows Server 2012 R2 y Windows 8.1 tienen aplicada esta revisión. (Siempre debería recibir revisiones de Windows a través de Windows Update). Para obtener más información, vea el artículo asociado de Microsoft Knowledge Base, [Rendimiento lento en el acceso a Azure Files desde Windows 8.1 o Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Características e interoperabilidad con otros servicios
* <a id="cluster-witness"></a>**¿Puedo usar mi recurso compartido de archivos de Azure como un *testigo del recurso compartido de archivos* para el clúster de conmutación por error de Windows Server?**  
    Actualmente, esta configuración no se admite para un recurso compartido de archivos de Azure. Para obtener más información sobre cómo configurar esta opción para Azure Blob Storage, consulte [Implementar un testigo en la nube para un clúster de conmutación por error](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>**¿Puedo montar un recurso compartido de archivos de Azure en una instancia de Azure Container Instances?**  
    Sí, los recursos compartidos de archivos de Azure son una buena opción si quiere conservar información sin tener que depender de la duración de una instancia del contenedor. Para obtener más información, vea [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>**¿Hay alguna operación de cambio de nombre en la API de REST?**  
    De momento, no.

* <a id="nested-shares"></a>**¿Puedo configurar recursos compartidos anidados, es decir, un recurso compartido en otro recurso compartido?**  
    No. El recurso compartido de archivos *es* el controlador virtual que se puede montar, por lo que no se admiten recursos compartidos anidados.

* <a id="ibm-mq"></a>**¿Cómo se usa Azure Files con IBM MQ?**  
    IBM ha publicado un documento que ayuda a los clientes de IBM MQ a la hora de configurar Azure Files con el servicio IBM. Para obtener más información, vea [How to set up an IBM MQ multi-instance queue manager with Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Configuración del administrador de colas de varias instancias de IBM MQ con el servicio Microsoft Azure Files).

## <a name="see-also"></a>Otras referencias
* [Solución de problemas de Azure Files en Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solución de problemas de Azure Files en Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solución de problemas de Azure File Sync (versión preliminar)](storage-sync-files-troubleshoot.md)
