---
title: "Solución de problemas de Azure Files en Windows | Microsoft Docs"
description: "Solución de problemas de Azure Files en Windows"
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
ms.openlocfilehash: 5aacc8a920c9343c5efa89128aabb1505fc2d9aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Solucione problemas de Azure Files en Windows

En este artículo se enumeran los problemas habituales relacionados con Microsoft Azure Files cuando se conecta desde clientes Windows. También se proporcionan posibles causas de estos problemas y sus resoluciones. Además de los pasos de solución de problemas de este artículo, también puede usar [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para asegurarse de que el entorno de cliente Windows cumpla los requisitos previos. AzFileDiagnostics automatiza la detección de la mayoría de los síntomas que se mencionan en este artículo y le ayuda a configurar su entorno para obtener un rendimiento óptimo. Esta información también se puede encontrar en el [Solucionador de problemas de recursos compartidos de Azure Files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares), que proporciona los pasos necesarios para ayudarle con problemas relativos la conexión, asignación o montaje de recursos compartidos de Azure Files.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Error 53, Error 67 o Error 87 al montar o desmontar un recurso compartido de archivos de Azure

Cuando intenta montar un recurso compartido de archivos desde un entorno local o un centro de datos diferente, es posible que observe los errores siguientes:

- Error de sistema 53. No se ha encontrado la ruta de acceso de la red.
- Error de sistema 67. No se encuentra el nombre de red especificado.
- Error de sistema 87. El parámetro no es correcto.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicación sin cifrar

Por seguridad, se bloquean las conexiones a recursos compartidos de archivos de Azure si no el canal de comunicación no está cifrado y si el intento de conexión no se realiza desde el mismo centro de datos donde residen los recursos compartidos de archivos de Azure. Se proporciona el cifrado del canal de comunicación solamente si el sistema operativo cliente del usuario admite el cifrado SMB.

Windows 8, Windows Server 2012 y versiones posteriores de cada sistema negocian solicitudes que incluyen SMB 3.0, que es compatible con el cifrado.

### <a name="solution-for-cause-1"></a>Solución para la causa 1

Conéctese desde un cliente que satisfaga uno de los requisitos siguientes:

- Cumple los requisitos de Windows 8 y Windows Server 2012 o versiones posteriores
- Se conecta desde una máquina virtual en el mismo centro de datos que la cuenta de almacenamiento de Azure que se usa para el recurso compartido de archivos de Azure

### <a name="cause-2-port-445-is-blocked"></a>Causa 2: Puerto 445 bloqueado

Los errores del sistema 53 o 67 pueden producirse cuando se bloquea la comunicación de salida del puerto 445 a un centro de datos de Azure Files. Para ver el resumen de los ISP que permiten o deniegan el acceso desde el puerto 445, visite [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para descubrir si este es el motivo del mensaje "Error de sistema 53", puede usar Portqry para enviar una consulta al punto de conexión TCP:445. Si el punto de conexión TCP:445 se muestra como filtrado, eso quiere decir que el puerto TCP está bloqueado. Aquí se muestra una consulta de ejemplo:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Si el puerto TCP 445 está bloqueado por una regla a lo largo de la ruta de acceso de red, verá la siguiente salida:

  `TCP port 445 (microsoft-ds service): FILTERED`

Para más información sobre el uso de Portqry, consulte [Descripción de la utilidad de línea de comandos Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Solución para la causa 2

Colabore con su departamento de TI para abrir el puerto 445, de modo que acepte las conexiones salientes a los [intervalos de IP de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Causa 3: NTLMv1 habilitado

También se producen los errores del sistema 53 u 87 si se ha habilitado la comunicación NTLMv1 en el cliente. Azure Files solo admite la autenticación NTLMv2. Si se habilita NTLMv1, el cliente será menos seguro. Por lo tanto, se bloquea la comunicación con Azure Files. 

Para determinar si esta es la causa del error, averigüe si la siguiente subclave del Registro está establecida en el valor 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa &gt; LmCompatibilityLevel**

Para obtener más información, consulte el tema [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) en TechNet.

### <a name="solution-for-cause-3"></a>Solución para la causa 3

Revierta el valor **LmCompatibilityLevel** al predeterminado, 3, en la siguiente subclave del Registro:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Error 1816 "Cuota insuficiente para procesar este comando" cuando se copia a un recurso compartido de archivos de Azure

### <a name="cause"></a>Causa

El error 1816 se produce cuando se alcanza el límite superior de identificadores abiertos simultáneos que se permiten para un archivo en el equipo donde se está montando el recurso compartido de archivos.

### <a name="solution"></a>Solución

Reduzca el número de identificadores abiertos simultáneos cerrando algunos de ellos y vuelva a intentarlo. Para más información, consulte [Lista de comprobación de rendimiento y escalabilidad de Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Lentitud al copiar archivos a y desde Azure Files en Windows

Puede que experimente un rendimiento lento cuando intente transferir archivos al servicio Azure Files.

- Si no tiene un requisito mínimo de tamaño de E/S específico, se recomienda que utilice 1 MB como el tamaño de E/S para disfrutar de un rendimiento óptimo.
-   Si conoce el tamaño final de un archivo que está ampliando mediante operaciones de escritura y el software no presenta problemas de compatibilidad cuando la cola no escrita del archivo contiene ceros, establezca el tamaño de archivo con antelación en lugar de hacer que cada escritura sea una escritura de ampliación.
-   Utilice el método de copia correcto:
    -   Use [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para todas las transferencias entre dos recursos compartidos de archivos.
    -   Use [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) entre recursos compartidos de archivos en un equipo local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Consideraciones para Windows 8.1 o Windows Server 2012 R2

En el caso de los clientes que ejecutan Windows 8.1 o Windows Server 2012 R2, asegúrese de que la revisión [KB3114025](https://support.microsoft.com/help/3114025) esté instalada. Esta revisión mejora el rendimiento de la creación y el cierre de identificadores.

Puede ejecutar el siguiente script para comprobar si se ha instalado la revisión:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si la revisión está instalada, se muestra el siguiente resultado:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Las imágenes de Windows Server 2012 R2 de Azure Marketplace tienen la revisión KB3114025 instalada de manera predeterminada desde diciembre de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>No hay ninguna carpeta con una letra de unidad en **Mi PC**

Si asigna un recurso compartido de archivos de Azure como administrador mediante net use, el recurso compartido parece que falta.

### <a name="cause"></a>Causa

De manera predeterminada, el Explorador de archivos de Windows no se ejecuta como administrador. Si se ejecuta net use desde un símbolo del sistema de administrador, la unidad de red se asigna como administrador. Dado que las unidades asignadas son específicas para los usuarios, la cuenta de usuario con la que se haya iniciado sesión no mostrará las unidades si estas se han montado con una cuenta de usuario distinta.

### <a name="solution"></a>Solución
Monte el recurso compartido desde una línea de comandos que no sea de administrador. Como alternativa, puede seguir las instrucciones de [este tema de TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar el valor del Registro **EnableLinkedConnections**.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Se produce un error en el comando net use si la cuenta de almacenamiento contiene una barra diagonal

### <a name="cause"></a>Causa

El comando net use interpreta la barra diagonal (/) como una opción de línea de comandos. Si el nombre de la cuenta de usuario comienza por una barra diagonal, se produce un error en la asignación de unidad.

### <a name="solution"></a>Solución

Puede utilizar cualquiera de los siguientes pasos para solucionar el problema:

- Ejecute el siguiente comando de PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Desde un archivo por lotes, puede ejecutar el comando de este modo:

  `Echo new-smbMapping ... | powershell -command –`

- Encierre la clave entre comillas dobles para solucionar el problema, a menos que la barra diagonal sea el primer carácter. En ese caso, use el modo interactivo y escriba la contraseña por separado o vuelva a generar las claves para obtener una que no empiece por barra diagonal.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>La aplicación o el servicio no pueden acceder a una unidad montada de Azure Files

### <a name="cause"></a>Causa

Las unidades se montan para usuarios individuales. Si su aplicación o servicio se ejecuta con una cuenta de usuario diferente a la que montó la unidad, la aplicación no verá la unidad.

### <a name="solution"></a>Solución

Use una de las soluciones siguientes:

-   Monte la unidad desde la misma cuenta de usuario que contiene la aplicación. Puede usar una herramienta como PsExec.
- Pase el nombre y la clave de la cuenta de almacenamiento en los parámetros de nombre de usuario y contraseña del comando net use.

Después de seguir estas instrucciones, podría recibir el mensaje de error siguiente cuando ejecute net use para la cuenta de servicio de red o del sistema: "Error de sistema 1312. Una sesión de inicio especificada no existe. Es posible que haya finalizado." En este caso, asegúrese de que el nombre de usuario pasado a net use incluya la información de dominio (por ejemplo, "[nombre de la cuenta de almacenamiento].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Error "El archivo se va a copiar a un destino que no es compatible con el cifrado"

Cuando se copia un archivo a través de la red, el archivo se descifra en el equipo de origen, se transmite en texto no cifrado y se vuelve a cifrar en el destino. Sin embargo, es posible que vea el siguiente error cuando intente copiar un archivo cifrado: "El archivo se va a copiar a un destino que no es compatible con el cifrado."

### <a name="cause"></a>Causa
Este problema puede producirse si está usando Sistema de cifrado de archivos (EFS). Es posible copiar archivos cifrados por BitLocker en Azure Files. En cambio, Azure Files no admite NTFS EFS.

### <a name="workaround"></a>Solución alternativa
Para copiar un archivo a través de la red, primero debe descifrarlo. Use uno de los siguientes métodos:

- Use el comando **copy /d**. Permite que los archivos cifrados se guarden como archivos descifrados en el destino.
- Establezca la siguiente clave del Registro:
  - Ruta de acceso = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Nombre = CopyFileAllowDecryptedRemoteDestination
  - Valor = 1

Tenga en cuenta que la configuración de la clave del Registro afecta a todas las operaciones de copia llevadas a cabo en recursos compartidos de red.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
