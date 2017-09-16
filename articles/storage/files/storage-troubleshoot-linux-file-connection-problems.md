---
title: "Solución de problemas de Azure File Storage en Linux | Microsoft Docs"
description: "Solución de problemas de Azure File Storage en Linux"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: dc32a57bf49d20faa2e0c241f99b1af7d02b586f
ms.contentlocale: es-es
ms.lasthandoff: 09/08/2017

---
# <a name="troubleshoot-azure-file-storage-problems-in-linux"></a>Solución de problemas de Azure File Storage en Linux

En este artículo se enumeran los problemas habituales relacionados con Microsoft Azure File Storage cuando se conecta desde clientes de Linux. También se proporcionan posibles causas de estos problemas y sus resoluciones.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[permiso denegado] Cuota de disco superada" al intentar abrir un archivo

En Linux, recibe un mensaje de error similar al siguiente:

**<filename> [permiso denegado] Cuota de disco superada**

### <a name="cause"></a>Causa

Se ha alcanzado el límite superior de identificadores abiertos simultáneos permitidos para un archivo.

### <a name="solution"></a>Solución

Reduzca el número de identificadores abiertos simultáneos cerrando algunos de ellos y, después, vuelva a realizar la operación. Para más información, consulte [Lista de comprobación de rendimiento y escalabilidad de Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-file-storage-in-linux"></a>Copia de archivos lenta en y desde Azure File Storage en Linux

-   Si no tiene un requisito mínimo de tamaño de E/S específico, se recomienda que utilice 1 MB como el tamaño de E/S para disfrutar de un rendimiento óptimo.
-   Si conoce el tamaño final de un archivo que amplía mediante operaciones de escritura y el software no presenta problemas de compatibilidad cuando una cola no escrita del archivo contiene ceros, establezca el tamaño de archivo con antelación en lugar de hacer que cada escritura sea una escritura de ampliación.
-   Utilice el método de copia correcto:
    -   Use [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para todas las transferencias entre dos recursos compartidos de archivos.
    -   Utilice [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre recursos compartidos de archivos y un equipo local.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Error de montaje (112): El host está apagado" debido a un tiempo de espera de reconexión

Se produce un error de montaje "112" en el cliente de Linux cuando este lleva inactivo mucho tiempo. Después del tiempo de inactividad extendido, el cliente se desconecta y se agota el tiempo de espera de la conexión.  

### <a name="cause"></a>Causa

La conexión puede estar inactiva por las razones siguientes:

-   Errores de comunicación de la red que impiden el restablecimiento de una conexión TCP con el servidor cuando se usa la opción de montaje predeterminada “flexible”
-   Correcciones de reconexión recientes que no están presentes en los kernels anteriores

### <a name="solution"></a>Solución

Este problema de reconexión en el kernel de Linux se ha corregido como parte de los siguientes cambios:

- [Fix reconnect to not defer smb3 session reconnect long after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93) (Corregir la reconexión para que no aplace la reconexión de la sesión de smb3 mucho después de la reconexión del socket)
-   [Call echo service immediately after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7) (Llamar al servicio de eco inmediatamente después de volver a conectar el socket)
-   [CIFS: Fix a possible memory corruption during reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b) (CIFS: Corregir un posible daño de memoria durante la reconexión)
-   [CIFS: Fix a possible double locking of mutex during reconnect (or kernel v4.9 and later)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) (CIFS: Corregir un posible bloqueo doble de exclusión mutua durante la reconexión: para el kernel v4.9 y versiones posteriores)

Pero es posible que estos cambios todavía no se migren a todas las distribuciones de Linux. Esta corrección y otras correcciones de reconexión se realizan en los siguientes kernels de Linux conocidos: 4.4.40, 4.8.16 y 4.9.1. Puede obtener esta corrección actualizando a una de estas versiones de kernel recomendadas.

### <a name="workaround"></a>Solución alternativa

Una solución alternativa para este problema es especificar un montaje forzado. Esta solución obliga al cliente a esperar hasta que se establezca una conexión o hasta que se interrumpa explícitamente, y puede usarse para evitar errores debidos a los tiempos de espera de la red. Sin embargo, esta solución alternativa puede provocar esperas indefinidas. Esté preparado para detener las conexiones según sea necesario.

Si no puede actualizar a las versiones más recientes del kernel, puede solucionar este problema manteniendo un archivo en el recurso compartido de archivos de Azure en el que se escribe cada 30 segundos o menos. Esta debe ser una operación de escritura, como volver a escribir la fecha de creación o modificación en el archivo. De lo contrario, podría obtener resultados almacenados en caché y la operación podría no desencadenar la reconexión.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-file-storage-by-using-smb-30"></a>"Error de montaje (115): Operación en curso" cuando monta Azure File Storage mediante SMB 3.0

### <a name="cause"></a>Causa

En algunas distribuciones de Linux todavía no se admiten las características de cifrado de SMB 3.0 y es posible que los usuarios reciban un mensaje de error "115" al tratar de montar Azure File Storage mediante SMB 3.0 debido a una característica que falta.

### <a name="solution"></a>Solución

La característica de cifrado de SMB 3.0 para Linux se introdujo en el kernel 4.11. Esta característica permite el montaje de recursos compartidos de Azure File desde el entorno local o una región distinta de Azure. En el momento de la publicación, esta funcionalidad se ha usado en Ubuntu 17.04 y Ubuntu 16.10. Si el cliente de SMB de Linux no admite el cifrado, monte Azure File Storage con SMB 2.1 desde una máquina virtual Linux de Azure que se encuentre en el mismo centro de datos que la cuenta de File Storage.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Rendimiento lento en un recurso compartido de archivos de Azure montado en una VM de Linux

### <a name="cause"></a>Causa

Una posible causa de un rendimiento lento es que el almacenamiento en caché está deshabilitado.

### <a name="solution"></a>Solución

Para comprobar si el almacenamiento en caché está deshabilitado, busque la entrada **cache=**. 

**Cache=none** indica que el almacenamiento en caché está deshabilitado.  Vuelva a montar el recurso compartido mediante el comando de montaje predeterminado o agregando explícitamente la opción **cache=strict** al comando de montaje con el fin de asegurarse de que el almacenamiento en caché predeterminado o el modo de almacenamiento en caché "strict" esté habilitado.

En algunos escenarios, la opción de montaje **serverino** puede hacer que el comando **ls** se ejecute en cada entrada de directorio. Este comportamiento produce una disminución del rendimiento cuando se muestra un listado de un directorio grande. Puede comprobar las opciones de montaje en la entrada **/etc/fstab**:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

También puede comprobar si se usan las opciones correctas ejecutando el comando **sudo mount | grep cifs** y comprobando su salida, como la siguiente salida de ejemplo:

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Si no está presente la opción**cache=strict** o **serverino**, desmonte y vuelva a montar Azure File Storage ejecutando el comando de montaje desde la [documentación](../storage-how-to-use-files-linux.md). A continuación, vuelva a comprobar que la entrada **/etc/fstab** tiene las opciones correctas.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Se perdieron las marcas de tiempo al copiar archivos de Windows a Linux

En las plataformas Unix y Linux, el comando **cp -p** produce un error si los archivos 1 y 2 pertenecen a distintos usuarios.

### <a name="cause"></a>Causa

La marca para exigir **f** en COPYFILE da como resultado la ejecución de **cp -p -f** en Unix. Este comando tampoco conserva la marca de tiempo del archivo que no el usuario posee.

### <a name="workaround"></a>Solución alternativa

Utilice el usuario de la cuenta de almacenamiento para copiar los archivos:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

