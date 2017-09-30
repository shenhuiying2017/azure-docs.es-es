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
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eb0b4d7cde568ab1809daa8025120828e75f5d76
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="frequently-asked-questions-about-azure-files"></a>Preguntas más frecuentes sobre Azure Files

## <a name="general"></a>General
* **P. ¿Qué es Azure Files?**  
   
    Azure Files es un sistema de archivos distribuido de Azure. Proporciona una interfaz de protocolo SMB que permite a los usuarios montar el almacenamiento como un recurso compartido nativo en una máquina virtual de Azure compatible o en una máquina local.

* **P. ¿Por qué es útil Azure Files?**  
   
    Azure Files proporciona acceso a datos compartidos en varias máquinas virtuales y plataformas. Consulte [¿Por qué es útil Azure Files?](storage-files-introduction.md#why-azure-files-is-useful).

* **P. ¿Cuándo debo usar Azure Files en lugar de Azure Blobs o Azure Disks?**  
   
    Microsoft Azure proporciona varias formas de almacenar datos en la nube y obtener acceso a ellos.  
   
    Azure Files: proporciona una interfaz SMB, bibliotecas de cliente y una interfaz de REST que permite un fácil acceso desde cualquier lugar a archivos almacenados.  
   
    Azure Blob: proporciona bibliotecas de cliente y una interfaz de REST que permite que los datos no estructurados se almacenen y se obtenga acceso a ellos a gran escala en blobs en bloques.  
   
    Azure Data Disks: proporciona bibliotecas de cliente y una interfaz de REST que permite que los datos se almacenen persistentemente y se obtenga acceso a ellos desde un disco duro virtual conectado.  
   
    Obtenga más información en [Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Data Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **P. ¿Cómo puedo empezar a usar Azure Files?**  
   
    Para empezar, puede crear un recurso compartido de Azure File. Puede crear recursos compartidos de Azure Files mediante Azure Portal, los cmdlets de PowerShell de Azure Storage, las bibliotecas de cliente de Azure Storage o la API de REST de Azure Storage. En este tutorial, aprenderá a hacer lo siguiente:

    * [Crear un recurso compartido de Azure File mediante el portal](storage-how-to-use-files-portal.md)
    * [Crear un recurso compartido de Azure File mediante Powershell](storage-how-to-create-file-share.md#create-file-share-through-powershell)
    * [Crear un recurso compartido de Azure File mediante la CLI](storage-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **P. ¿Qué replicaciones admite Azure Files?**  
   
    Por ahora, Azure Files solo admite LRS o GRS. Está previsto que RA-GRS se admite, pero aún no se puede indicar ninguna fecha.
    
## <a name="scale-targetslimits"></a>Destinos y límites de escala

* **P. ¿Cuántos clientes pueden acceder al mismo archivo simultáneamente?**

    Hay una cuota de 2000 identificadores abiertos en un único archivo. Una vez que tenga 2000 identificadores abiertos, se mostrará un error para indicar que se alcanzó la cuota.

## <a name="security-authentication-and-access-control"></a>Seguridad, autenticación y control de acceso

* **P. ¿Cuáles son las diferentes formas de obtener acceso a los archivos de Azure Files?**
    
    Puede montar el recurso compartido de archivos en la máquina local mediante el protocolo SMB 3.0 o usar herramientas como el [Explorador de Storage](http://storageexplorer.com/) para obtener acceso a los archivos en el recurso compartido de archivos. Desde su aplicación, puede usar las bibliotecas de cliente de almacenamiento, las API de REST o Powershell para obtener acceso a los archivos en el recurso compartido de Azure File.

* **P. ¿Cómo se puede proporcionar acceso a un archivo específico a través de un explorador web?**
    
    Con SAS se pueden generar tokens con permisos específicos que son válidos durante un intervalo de tiempo determinado. Por ejemplo, puede generar un token con acceso de solo lectura a un archivo determinado para un período de tiempo específico. Cualquier persona que posea esta dirección URL puede obtener acceso al archivo directamente desde cualquier explorador web mientras sea válido. Las claves SAS se pueden generar fácilmente desde una interfaz de usuario, como el Explorador de Storage.

* **P. ¿Es posible especificar permisos de solo lectura o solo escritura en las carpetas del recurso compartido?**
    
    Si el recurso compartido de archivos se monta a través de SMB, no se tiene este nivel de control sobre los permisos. Sin embargo, se puede lograr. Para ello, es preciso crear una firma de acceso compartido (SAS) mediante la API de REST o las bibliotecas de cliente.  

* **P. ¿Cómo se puede habilitar el cifrado del lado del servidor en Azure Files?**

    El [cifrado del lado del servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para Azure Files se encuentra disponible con carácter general en todas las regiones y nubes públicas y nacionales. Puede habilitar el cifrado del lado del servidor para Azure Files mediante [Azure Portal](https://portal.azure.com/), la [API del proveedor de recursos de Microsoft Azure Storage](/rest/api/storagerp/storageaccounts), [Azure Powershell](https://msdn.microsoft.com/library/azure/mt607151.aspx) o la [CLI de Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
    
    Después de habilitar el cifrado del lado del servidor en Azure Files, se cifrarán automáticamente todos los datos nuevos escritos en el almacenamiento de archivos en esa cuenta de almacenamiento. Esta característica está disponible para todos los datos nuevos que se escriben en recursos compartidos nuevos o existentes en una cuenta de almacenamiento nueva o existente. No se aplica ningún cargo adicional por habilitar esta característica. Para obtener más información, vea este artículo sobre [cómo habilitar el cifrado del lado del servidor en Azure Files](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **P. ¿Azure Files admite la autenticación basada en Active Directory?**
   
    En la actualidad no se admite la autenticación basada en AD ni las ACL, pero se admitirá en un futuro cercano. Por ahora, las claves de las cuentas de Almacenamiento de Azure se usan para proporcionar autenticación al recurso compartido de archivos. La solución alternativa es usar las firmas de acceso compartido (SAS) a través de la API de REST o de las bibliotecas de cliente. Con SAS se pueden generar tokens con permisos específicos que son válidos durante un intervalo de tiempo determinado. Por ejemplo, se puede generar un token con acceso de solo lectura a un archivo dado que expire al cabo de 10 minutos. Todos los usuarios que posean dicho token, mientras tenga validez, tendrán acceso de solo lectura al archivo durante esos 10 minutos.
   
    SAS solo se admite a través de la API de REST o de las bibliotecas de cliente. Si el recurso compartido de archivos se monta a través del protocolo SMB, no se puede usar una SAS para delegar el acceso a su contenido. 
    
* **P. ¿Cuáles son las directivas de cumplimiento de datos que admite Azure Files?**

   Azure Files se ejecuta sobre la misma arquitectura de almacenamiento que otros servicios de almacenamiento de Azure Storage y aplica las mismas directivas de cumplimiento de datos. Para obtener más información sobre la conformidad de datos de Azure Storage, puede descargar y consultar el [documento sobre la protección de datos de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409).

## <a name="on-premises-access"></a>Acceso local

* **P. ¿Tengo que usar Azure ExpressRoute para conectarme a Azure Files desde una máquina virtual local?**
   
    No. Aunque no se disponga de ExpressRoute, es posible acceder al recurso compartido de archivos de forma local, siempre que el puerto 445 (salida TCP) esté abierto para el acceso a Internet. Pero si está interesado en hacerlo, puede usar ExpressRoute con Azure Files.

* **P. ¿Cómo puedo montar un recurso compartido de Azure File en mi máquina local?** 
    
    Puede montar el recurso compartido de archivos a través del protocolo SMB, siempre y cuando el puerto 445 (salida TCP) esté abierto y el cliente admita el protocolo SMB 3.0 (por ejemplo, si usa Windows 10 o Windows Server 2012). Trabaje con su proveedor de ISP local para desbloquear el puerto. Mientras tanto, puede ver los archivos con el [Explorador de Storage](../../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents).


## <a name="billing-and-pricing"></a>Precios y facturación
* **P. ¿Cuenta el tráfico de red entre una máquina virtual de Azure y un recurso compartido de archivos como ancho de banda externo que se carga a la suscripción?**
   
    Si el recurso compartido de archivos y la máquina virtual están en la misma región de Azure, el tráfico entre ellos es gratuito. Si se encuentran en regiones distintas, el tráfico entre ellos se cargará como ancho de banda externo.

## <a name="backup"></a>Copia de seguridad

* **P. ¿Cómo puedo realizar una copia de seguridad de mi recurso compartido de Azure Files?**
    
    Puede usar AzCopy, RoboCopy o una herramienta de copia de seguridad de terceros que pueda hacer una copia de seguridad de un recurso compartido de archivos montado. Esperamos integrar la característica de tomar instantáneas de recursos compartidos de archivos en un futuro próximo, de modo que pueda usarla para realizar una copia de seguridad del recurso compartido de Azure Files.

## <a name="performance"></a>Rendimiento

* **P. ¿Cuáles son los límites de escala de Azure Files?**
    Para obtener información sobre los objetivos de escalabilidad y rendimiento de Azure Files, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files).

* **P. Mi rendimiento era lento al intentar descomprimir archivos en Azure Files. ¿qué debo hacer?**
    
    Para transferir una gran cantidad de archivos a Azure Files, es aconsejable usar AzCopy (Windows, versión preliminar para Linux/Unix) o Azure Powershell, ya que estas herramientas se han optimizado para la transferencia a través de red.

* **P. ¿Qué revisiones se han lanzado para corregir el problema de rendimiento lento con Azure Files?**
    
    El equipo de Windows ha lanzado recientemente una revisión para solucionar un problema de rendimiento lento cuando el cliente accede a Azure Files desde Windows 8.1 o Windows Server 2012 R2. Para obtener más información, consulte el artículo asociado de Knowledge Base, [Rendimiento lento en el acceso a Azure Files desde Windows 8.1 o Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Características e interoperabilidad con otros servicios
* **P. ¿Es un "testigo del recurso compartido de archivos" para un clúster de conmutación por error uno de los casos de uso de Azure Files?**
   
    Actualmente no se admite.

* **P. ¿Hay alguna operación de cambio de nombre en la API de REST?**
   
    De momento, no.

* **P. ¿Es posible tener recursos compartidos anidados, es decir, un recurso compartido en un recurso compartido?**
    
    Nº El recurso compartido de archivos es el controlador virtual que se puede montar, por lo que no se admiten recursos compartidos anidados.

* **P. ¿Cómo se usa Azure Files con IBM MQ?**
    
    IBM ha publicado un documento para guiar a los clientes de IBM MQ a la hora de configurar Azure Files con su servicio. Para más información, consulte [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Configuración del administrador de colas de varias instancias de IBM MQ con el servicio de archivos de Microsoft Azure).


## <a name="troubleshooting"></a>Solución de problemas
* **P. ¿Cómo se pueden solucionar los errores de Azure Files?**
    
    Puede hacer referencia al [artículo de solución de problemas de archivos de Azure](storage-troubleshoot-windows-file-connection-problems.md) para ver una guía completa de solución de problemas. 


## <a name="see-also"></a>Otras referencias
Consulte los vínculos siguientes para obtener más información acerca Azure Files.

### <a name="conceptual-articles-and-videos"></a>Artículos y vídeos conceptuales
* [Azure Files: un sistema de archivos SMB en la nube sin dificultades para Windows y Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Uso de Azure Files con Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Compatibilidad de herramientas con el almacenamiento de archivos
* [Uso de AzCopy con Almacenamiento de Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Uso de la CLI de Azure con Almacenamiento de Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Solución de problemas de Azure Files](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Publicaciones de blog
* [Azure Files is now generally available (Azure Files ya está disponible con carácter general)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure Files (En el interior de Azure Files)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (Introducción al servicio de archivos de Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migración de datos a Azure Files](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referencia
* [Referencia de la biblioteca de clientes de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

