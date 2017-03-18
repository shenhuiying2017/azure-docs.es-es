---
title: "Solución de problemas de Azure File Storage | Microsoft Docs"
description: "Solución de problemas de Azure File Storage"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: fbc5f600-131e-4b99-828a-42d0de85fff7
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 62d2cd990bff4ffc982eef507ad69c68c00a65ab
ms.lasthandoff: 03/14/2017


---
# <a name="troubleshooting-azure-file-storage-problems"></a>Solución de problemas de Azure File Storage
En este artículo se enumeran los problemas habituales relacionados con Microsoft Azure File Storage cuando se conecta desde clientes de Windows y Linux. También se proporcionan las posibles causas y soluciones de estos problemas.

**Problemas generales (que se producen en los clientes de Windows y Linux)**

* [Error de cuota al tratar de abrir un archivo](#quotaerror)
* [Rendimiento reducido al acceder a Azure File Storage desde Windows o Linux](#slowboth)
* [Seguimiento de las operaciones de lectura y escritura en Azure File Storage](#traceop)

**Problemas del cliente de Windows**

* [Rendimiento reducido al acceder a Azure File Storage desde Windows 8.1 o Windows Server 2012 R2](#windowsslow)
* [Error 53 al tratar de montar un recurso compartido de archivos de Azure](#error53)
* [Error 87 El parámetro es incorrecto al intentar montar un recurso compartido de archivos de Azure](#error87)
* [El comando net use se procesó correctamente, pero no veo el recurso compartido de archivos de Azure montado o la letra de unidad en la interfaz de usuario del Explorador de Windows](#netuse)
* [Mi cuenta de almacenamiento contiene "/" y el comando net use genera un error](#slashfails)
* [Mi aplicación o servicio no puede acceder a la unidad de Azure Files montada](#accessfiledrive)
* [Recomendaciones adicionales para optimizar el rendimiento](#additional)
* [Error "El archivo se va a copiar a un destino que no es compatible con el cifrado" al cargar archivos a Azure Files o copiarlos.](#encryption)

**Problemas del cliente de Linux**

* [Error intermitente de ES: "El host está apagado (Error 112)" en los recursos compartidos de archivos, o bien el shell se bloquea al ejecutar comandos list en el punto de montaje](#errorhold)
* [Error de montaje 115 al tratar de montar Azure Files en la VM de Linux](#error15)
* [El recurso compartido de archivos de Azure montado en la máquina virtual de Linux experimenta un rendimiento lento](#delayproblem)


<a id="quotaerror"></a>

## <a name="quota-error-when-trying-to-open-a-file"></a>Error de cuota al tratar de abrir un archivo
En Windows, recibe mensajes de error similares a los siguientes:

`1816 ERROR_NOT_ENOUGH_QUOTA <--> 0xc0000044`
`STATUS_QUOTA_EXCEEDED`
`Not enough quota is available to process this command`
`Invalid handle value GetLastError: 53`

En Linux, recibirá mensajes de error similares a los siguientes:

`<filename> [permission denied]`
`Disk quota exceeded`

### <a name="cause"></a>Causa
El problema se produce porque se ha alcanzado el límite de identificadores abiertos simultáneos permitidos para un archivo.

### <a name="solution"></a>Solución
Reduzca el número de identificadores abiertos simultáneos cerrando algunos de ellos y, después, vuelva a intentarlo. Para obtener más información, consulte [Lista de comprobación de rendimiento y escalabilidad de Microsoft Azure Storage](storage-performance-checklist.md).

<a id="slowboth"></a>

## <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Rendimiento reducido al acceder a File Storage desde Windows o Linux
* Si no tiene un requisito mínimo de tamaño de E/S específico, se recomienda que utilice 1 MB como el tamaño de E/S para disfrutar de un rendimiento óptimo.
* Si conoce el tamaño final de un archivo que está ampliando con escrituras y el software aún no presenta problemas de compatibilidad cuando la cola del archivo, que aún no se ha escrito, contiene ceros, establezca el tamaño del archivo con antelación en lugar de que cada escritura sea una de ampliación.
* Utilice el método de copia correcto:
      * Utilice AZCopy para todas las transferencias entre dos recursos compartidos de archivos. Para más detalles, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy#file-copy).
      * Use Robocopy entre un recurso compartido de archivos y un equipo local. Consulte [Multi-threaded robocopy for faster copies](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) (Robocopy multiproceso para copias más rápidas) para más detalles.
<a id="windowsslow"></a>

## <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Rendimiento reducido al acceder a File Storage desde Windows 8.1 o Windows Server 2012 R2
En el caso de los clientes que ejecutan Windows 8.1 o Windows Server 2012 R2, asegúrese de que la revisión [KB3114025](https://support.microsoft.com/kb/3114025) esté instalada. Esta revisión mejora el rendimiento de la creación y el cierre de los identificadores.

Puede ejecutar el siguiente script para comprobar si se ha instalado la revisión:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si la revisión está instalada, se muestra el siguiente resultado:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`
`{96c345ef-3cac-477b-8fcd-bea1a564241c}    REG_DWORD    0x1`

> [!NOTE]
> Las imágenes de Windows Server 2012 R2 de Azure Marketplace tienen la revisión KB3114025 instalada de manera predeterminada desde diciembre de 2015.
>
>

<a id="traceop"></a>

### <a name="how-to-trace-the-read-and-write-operations-in-azure-file-storage"></a>Seguimiento de las operaciones de lectura y escritura en Azure File Storage

[Analizador de mensajes de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=44226) puede mostrarle la solicitud de un cliente en texto simple, y hay una relación bastante aproximada entre las solicitudes de conexión y las transacciones (suponiendo que aquí esté SMB y no REST).  El inconveniente es que tendrá que ejecutar esto en cada cliente, lo que requiere mucho tiempo si tiene muchos trabajadores de VM IaaS.

Si usa el análisis de mensajes con ProcMon, puede obtener una idea bastante aproximada de qué código de aplicación es responsable de las transacciones.

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>Recomendaciones adicionales para optimizar el rendimiento
No cree ni abra nunca un archivo de E/S en caché que solicite acceso de escritura, pero no de lectura. Es decir, cuando llame a **CreateFile()**, nunca especifique solo **GENERIC_WRITE**, sino también **GENERIC_READ | GENERIC_WRITE** en todos los casos. Un identificador de solo escritura no puede almacenar en caché escrituras pequeñas localmente, incluso cuando se trate del único identificador abierto para el archivo. Esto supone una grave pérdida de rendimiento en las escrituras pequeñas. Tenga en cuenta que el modo "a" para CRT **fopen()** abre un identificador de solo escritura.

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>"Error 53" o "Error 67" al tratar de montar o desmontar un recurso compartido de archivos de Azure
Este problema puede deberse a las condiciones siguientes:

### <a name="cause-1"></a>Causa 1
"Error de sistema 53. Acceso denegado". Por motivos de seguridad, se bloquean las conexiones a los recursos compartidos de archivos de Azure si no se cifra el canal de comunicación y el intento de conexión no se realiza desde el mismo centro de datos en el que residen los recursos compartidos de archivos de Azure. No se efectúa el cifrado del canal de comunicación si el sistema operativo de cliente del usuario no es compatible con el cifrado SMB. Esto se indica mediante el mensaje de error "Error de sistema 53. Acceso denegado" cuando un usuario trata de montar un recurso compartido de archivos de forma local o desde un centro de datos distinto. Windows 8, Windows Server 2012 y versiones posteriores de cada solicitud de negociación que incluya SMB 3.0 admiten el cifrado.

### <a name="solution-for-cause-1"></a>Solución para la causa 1
Conéctese desde un cliente que cumpla los requisitos de Windows 8, Windows Server 2012 o versiones posteriores, o bien que se conecte desde una máquina virtual que se encuentre en el mismo centro de datos que la cuenta de almacenamiento de Azure que se utilice para el recurso compartido de archivos de Azure.

### <a name="cause-2"></a>Causa 2
Se puede producir el error del sistema 53 o 67 al montar un recurso compartido de archivos de Azure si se ha bloqueado la comunicación saliente al centro de datos de Azure Files en el puerto 445. Haga clic en [aquí](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) para ver el resumen de los ISP que permiten o deniegan el acceso desde el puerto 445.

Comcast y algunas organizaciones de TI bloquean dicho puerto. Para descubrir si este es el motivo del mensaje de error del sistema 53, puede usar la utilidad Portqry para enviar una consulta al punto de conexión TCP:445. Si el punto de conexión TCP:445 se muestra como filtrado, eso quiere decir que el puerto TCP está bloqueado. Aquí se muestra una consulta de ejemplo:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Si el puerto TCP 445 está bloqueado por una regla a lo largo de la ruta de acceso de red, verá el siguiente resultado:

**TCP port 445 (microsoft-ds service): FILTERED**

Para obtener más información sobre el uso de Portqry, consulte [Descripción de la utilidad de línea de comandos Portqry.exe](https://support.microsoft.com/kb/310099).

### <a name="solution-for-cause-2"></a>Solución para la causa 2
Colabore con su organización de TI para abrir el puerto 445, de modo que acepte las conexiones salientes a los [intervalos de IP de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

<a id="error87"></a>
### <a name="cause-3"></a>Causa 3
También es posible recibir los errores del sistema 53 u 87 si se ha habilitado la comunicación NTLMv1 en el cliente. Si se habilita NTLMv1, el cliente será menos seguro. Por lo tanto, se bloqueará la comunicación con Azure Files. Para comprobar si esta es la causa del error, averigüe si la siguiente subclave del Registro tiene establecido un valor de 3:

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel.

Para obtener más información, consulte el tema [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) en TechNet.

### <a name="solution-for-cause-3"></a>Solución para la causa 3
Para resolver este problema, revierta el valor de LmCompatibilityLevel en la clave del Registro HKLM\SYSTEM\CurrentControlSet\Control\Lsa al valor predeterminado de 3.

Azure Files solo admite la autenticación NTLMv2. Asegúrese de que la directiva de grupo se aplica a los clientes. De este modo, evitará que se produzca este error. Esto también se considera una práctica recomendada de seguridad. Para obtener más información, consulte el artículo sobre [cómo configurar los clientes para usar NTLMv2 mediante la directiva de grupo](https://technet.microsoft.com/library/jj852207\(v=ws.11\).aspx).

La configuración recomendada para la directiva es **Enviar solo respuesta NTLMv2**. Esto corresponde a un valor del Registro de 3. Los clientes utilizan únicamente la autenticación NTLMv2, y emplean la seguridad de sesión NTLMv2 si el servidor lo admite. Los controladores de dominio aceptan la autenticación LM, NTLM y NTLMv2.

<a id="netuse"></a>

## <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>El comando net use se ha procesado correctamente, pero no veo el recurso compartido de archivos de Azure montado en el Explorador de Windows
### <a name="cause"></a>Causa
De manera predeterminada, el Explorador de Windows no se ejecuta como administrador. Si se ejecuta **net use** desde un símbolo del sistema de administrador, se la unidad de red asigna "como administrador". Dado que las unidades asignadas son específicas para los usuarios, la cuenta de usuario con la que se haya iniciado sesión no mostrará las unidades si estas se han montado con una cuenta de usuario distinta.

### <a name="solution"></a>Solución
Monte el recurso compartido desde una línea de comandos que no sea de administrador. Como alternativa, puede seguir las instrucciones de [este tema de TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar el valor del Registro **EnableLinkedConnections**.

<a id="slashfails"></a>

## <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Mi cuenta de almacenamiento contiene "/" y el comando net use genera un error
### <a name="cause"></a>Causa
Cuando se ejecuta el comando **net use** en el símbolo del sistema (cmd.exe), se analiza agregando "/" como una opción de línea de comandos, lo cual hace que la asignación genere un error.

### <a name="solution"></a>Solución
Puede utilizar cualquiera de los siguientes pasos para solucionar el problema:

•  Utilice el siguiente comando de PowerShell:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

Desde un archivo por lotes, esto se puede realizar de la siguiente forma:

`Echo new-smbMapping ... | powershell -command –`

•   Escriba comillas dobles a ambos lados de la clave para solucionar el problema (a menos que "/" sea el primer carácter). En ese caso, use el modo interactivo y escriba la contraseña por separado o vuelva a generar las claves para obtener una que no empiece con el carácter de la barra diagonal (/).

<a id="accessfiledrive"></a>

## <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Mi aplicación o servicio no puede acceder a la unidad de Azure Files montada
### <a name="cause"></a>Causa
Las unidades se montan para usuarios individuales. Si su aplicación o servicio se ejecuta con una cuenta de usuario diferente, los usuarios no verán la unidad.

### <a name="solution"></a>Solución
Monte la unidad desde la misma cuenta de usuario en la que se encuentre la aplicación. Puede hacerlo con herramientas como psexec.

Otra opción para **net use** consiste en pasar el nombre y la clave de la cuenta de almacenamiento en los parámetros de nombre y la contraseña de usuario del comando **net use**.

Tras seguir estas instrucciones, es posible que aparezca el siguiente mensaje de error: "Error de sistema 1312. Una sesión de inicio especificada no existe. Es posible que haya finalizado" cuando ejecute **net use** para la cuenta de servicio de red o de sistema. En este caso, asegúrese de que el nombre de usuario pasado a **net use** incluya la información de dominio (por ejemplo: "[nombre de la cuenta de almacenamiento].file.core.windows.net").

<a id="encryption"></a>

## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Error "El archivo se va a copiar a un destino que no es compatible con el cifrado"
### <a name="cause"></a>Causa
Es posible copiar archivos cifrados por BitLocker en Azure Files. Sin embargo, File Storage no admite NTFS EFS. Por lo tanto, es probable que esté usando EFS en este caso. Si tiene algún archivo que se haya cifrado mediante EFS, las operaciones de copia en File Storage generarán un error, a menos que el comando de copia descifre el archivo copiado.

### <a name="workaround"></a>Solución alternativa
Para copiar un archivo en File Storage, debe descifrarlo primero. Para ello, emplee uno de los métodos siguientes:

•   Use **copy /d**.

•   Establezca la siguiente clave del Registro:

* Ruta de acceso = HKLM\Software\Policies\Microsoft\Windows\System
* Tipo de valor = DWORD
* Nombre = CopyFileAllowDecryptedRemoteDestination
* Valor = 1

Sin embargo, tenga en cuenta que la configuración de la clave del Registro afecta a todas las operaciones de copia de recursos compartidos de red.

<a id="errorhold"></a>

## <a name="host-is-down-error-112-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Error "El host está apagado (Error 112)" en los recursos compartidos de archivos, o bien el shell se bloquea al ejecutar comandos list en el punto de montaje
### <a name="cause"></a>Causa
Este error se produce en el cliente de Linux cuando este lleva inactivo mucho tiempo. Cuando el cliente está inactivo durante mucho tiempo, el cliente se desconecta y se agota el tiempo de espera de la conexión. 

La conexión puede estar inactiva por diversas razones. Una razón son los errores de comunicación de red que impiden el restablecimiento de una conexión TCP con el servidor cuando se usa la opción de montaje "flexible", que es el valor predeterminado.

Otra razón podría ser que también hay algunas correcciones de reconexión que no están presentes en los kernels anteriores.

### <a name="solution"></a>Solución

La especificación de un montaje forzado obligará al cliente a esperar hasta que se establezca una conexión o hasta que se interrumpa explícitamente, y puede usarse para evitar errores debidos a los tiempos de espera de la red. Sin embargo, los usuarios deben tener en cuenta que esto podría provocar la espera indefinida y que deben considerar la detención de una conexión según sea necesario.

Este problema de reconexión en el kernel de Linux se ha corregido como parte de los siguientes conjuntos de cambios:

* [Fix reconnect to not defer smb3 session reconnect long after socket reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93) (Corregir la reconexión para que no aplace la reconexión de la sesión de smb3 mucho después de la reconexión del socket)

* [Call echo service immediately after socket reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7) (Llamar al servicio de eco inmediatamente después de volver a conectar el socket)

* [CIFS: Fix a possible memory corruption during reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b) (CIFS: Corregir un posible daño de memoria durante la reconexión)

* [CIFS: Fix a possible double locking of mutex during reconnect - for kernels v4.9 and higher](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) (CIFS: Corregir un posible bloqueo doble de exclusión mutua durante la reconexión: para los kernels v4.9 y versiones posteriores) 

Pero es posible que este cambio todavía no se migre a todas las distribuciones de Linux. Esta es la lista de los kernels de Linux conocidos que tienen esta y otras correcciones de reconexión: 4.4.40+ 4.8.16+ 4.9.1+.
Puede cambiar a las versiones de kernel recomendadas antes para obtener la corrección más reciente.

### <a name="workaround"></a>Solución alternativa
Si no se puede cambiar a las versiones más recientes del kernel, puede solucionar este problema manteniendo un archivo en el recurso compartido de archivos de Azure en el que se escribe cada 30 segundos o menos. Esta debe ser una operación de escritura, como volver a escribir la fecha de creación o modificación en el archivo. De lo contrario, podría obtener resultados almacenados en caché y la operación podría no desencadenar la reconexión. 


<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>Error de montaje 115 al tratar de montar Azure Files en la VM de Linux
### <a name="cause"></a>Causa
Las distribuciones de Linux aún no admiten la característica de cifrado de SMB 3.0. En algunas distribuciones, el usuario puede recibir un mensaje de error 115 al tratar de montar Azure Files mediante SMB 3.0 debido a una característica que falta.

### <a name="solution"></a>Solución
Si el cliente de SMB de Linux que se use no admite el cifrado, monte Azure Files utilizando SMB 2.1 desde una VM de Linux que se encuentre en el mismo centro de datos que la cuenta de File Storage.

<a id="delayproblem"></a>

## <a name="azure-file-share-mounted-on-linux-vm-experiencing-slow-performance"></a>El recurso compartido de archivos de Azure montado en la máquina virtual de Linux experimenta un rendimiento lento

Una posible razón del rendimiento lento podría ser que el almacenamiento en caché está deshabilitado. Con el fin de comprobar si está habilitado, busque "caché =".  *cache=none* indica que el almacenamiento en caché está deshabilitado. Vuelva a montar el recurso compartido con el comando de montaje predeterminado o agregue expresamente la opción **caché=strict** para montar el comando con el fin de asegurarse de que el almacenamiento en caché predeterminado o el modo de almacenamiento en caché "strict" estén habilitados.

En algunos escenarios, la opción de montaje serverino puede hacer que el comando ls ejecute stat en cada entrada de directorio y este comportamiento produzca una disminución del rendimiento al enumerar un directorio grande. Puede comprobar las opciones de montaje en la entrada "/etc/fstab":

`//azureuser.file.core.windows.net/cifs        /cifs   cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

También puede comprobar si se están usando las opciones simplemente ejecutando el comando **sudo mount | grep cifs** y examinando su salida:

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs
(rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,
dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Si no están presentes las opciones cache=strict o serverino, desmonte y vuelva a montar Azure Files ejecutando el comando mount desde la [documentación](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share) y vuelva a comprobar que la entrada "/etc/fstab" tiene las opciones correctas.

## <a name="learn-more"></a>Más información
* [Introducción a Almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)
* [Introducción a Azure File Storage en Linux](storage-how-to-use-files-linux.md)

