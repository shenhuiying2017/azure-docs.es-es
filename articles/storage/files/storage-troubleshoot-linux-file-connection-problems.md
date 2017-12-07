---
title: "Solución de problemas de Azure Files en Linux | Microsoft Docs"
description: "Solución de problemas de Azure File en Linux"
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
ms.date: 09/19/2017
ms.author: genli
ms.openlocfilehash: 8fd09c748786f0bfe070c3f41201eddc3d272116
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Solución de problemas de Azure File en Linux

En este artículo se enumeran los problemas habituales relacionados con Microsoft Azure Files cuando se conecta desde clientes Linux. También se proporcionan posibles causas de estos problemas y sus resoluciones.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[permiso denegado] Cuota de disco superada" al intentar abrir un archivo

En Linux, recibe un mensaje de error similar al siguiente:

**<filename> [permiso denegado] Cuota de disco superada**

### <a name="cause"></a>Causa

Se ha alcanzado el límite superior de identificadores abiertos simultáneos permitidos para un archivo.

### <a name="solution"></a>Solución

Reduzca el número de identificadores abiertos simultáneos cerrando algunos de ellos y, después, vuelva a realizar la operación. Para más información, consulte [Lista de comprobación de rendimiento y escalabilidad de Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Copia de archivos lenta en y desde Azure Files en Linux

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
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Error de montaje (115): Operación en curso" cuando monta Azure Files mediante SMB 3.0

### <a name="cause"></a>Causa

En algunas distribuciones de Linux todavía no se admiten las características de cifrado de SMB 3.0 y es posible que los usuarios reciban un mensaje de error "115" al tratar de montar Azure Files mediante SMB 3.0 debido a una característica que falta. SMB 3.0 con cifrado completo solo se admite por el momento cuando se usa Ubuntu 16.04 o una versión posterior.

### <a name="solution"></a>Solución

La característica de cifrado de SMB 3.0 para Linux se introdujo en el kernel 4.11. Esta característica permite el montaje de recursos compartidos de Azure File desde el entorno local o una región distinta de Azure. En el momento de la publicación, esta funcionalidad se ha usado en Ubuntu 17.04 y Ubuntu 16.10. Si el cliente de SMB de Linux no admite el cifrado, monte Azure Files con SMB 2.1 desde una máquina virtual Linux de Azure que se encuentre en el mismo centro de datos que la cuenta de File Storage.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Rendimiento lento en un recurso compartido de archivos de Azure montado en una VM de Linux

### <a name="cause"></a>Causa

Una posible causa de un rendimiento lento es que el almacenamiento en caché está deshabilitado.

### <a name="solution"></a>Solución

Para comprobar si el almacenamiento en caché está deshabilitado, busque la entrada **cache=**. 

**Cache=none** indica que el almacenamiento en caché está deshabilitado.  Vuelva a montar el recurso compartido mediante el comando de montaje predeterminado o agregando explícitamente la opción **cache=strict** al comando de montaje con el fin de asegurarse de que el almacenamiento en caché predeterminado o el modo de almacenamiento en caché "strict" esté habilitado.

En algunos escenarios, la opción de montaje **serverino** puede hacer que el comando **ls** se ejecute en cada entrada de directorio. Este comportamiento produce una disminución del rendimiento cuando se muestra un listado de un directorio grande. Puede comprobar las opciones de montaje en la entrada **/etc/fstab**:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

También puede comprobar si se usan las opciones correctas ejecutando el comando **sudo mount | grep cifs** y comprobando su salida, como la siguiente salida de ejemplo:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Si no está presente la opción **cache=strict** o **serverino**, desmonte y vuelva a montar Azure Files ejecutando el comando de montaje desde la [documentación](../storage-how-to-use-files-linux.md). A continuación, vuelva a comprobar que la entrada **/etc/fstab** tiene las opciones correctas.

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

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>No se puede conectar o montar un recurso compartido de Azure File

### <a name="cause"></a>Causa

Las causas comunes de este problema son las siguientes:


- Está usando un cliente de distribución de Linux incompatible. Se recomienda usar las siguientes distribuciones de Linux para conectarse al recurso compartido de Azure File:

    - Ubuntu Server 14.04+ 
    - RHEL 7+ 
    - CentOS 7+ 
    - Debian 8 
    - openSUSE 13.2+ 
    - SUSE Linux Enterprise Server 12

- Las utilidades de CIFS no están instaladas en el cliente.
- La versión 2.1 mínima de SMB/CIFS no está instalada en el cliente.
- No se admite el cifrado SMB 3.0 en el cliente. El cifrado SMB 3.0 está disponible en Ubuntu 16.4 y versiones posteriores y en SUSE 12.3 y versiones posteriores. Otras distribuciones requieren kernel 4.11 y versiones posteriores.
- Está intentando conectarse a una cuenta de almacenamiento a través del puerto TCP 445 que no es compatible.
- Está intentando conectarse al recurso compartido de Azure File desde una máquina virtual de Azure y la máquina virtual no se encuentra en la misma región que la cuenta de almacenamiento.

### <a name="solution"></a>Solución

Para resolver el problema, use la [herramienta de solución de problemas para los errores de montaje de Azure Files](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Esta herramienta puede ayudarle a validar el cliente que ejecuta el entorno, detectar la configuración de cliente incompatible que podría provocar un error de acceso en Azure Files, proporcionar instrucciones preceptivas sobre la solución autónoma de problemas y recopilar los seguimientos de diagnóstico.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: no se puede acceder a "&lt;ruta de acceso&gt;": error de entrada/salida

Cuando se intenta mostrar los archivos en un recurso compartido de archivos de Azure mediante el comando ls, este comando se bloquea al enumerar los archivos y se recibe el siguiente error:

**ls: no se puede acceder a "&lt;ruta de acceso&gt;": error de entrada/salida**


### <a name="solution"></a>Solución
Actualice el kernel de Linux a las siguientes versiones que tengan una solución para este problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Todas las versiones que sean mayores o iguales que 4.13

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
