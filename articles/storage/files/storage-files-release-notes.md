---
title: Notas de la versión del agente de Azure File Sync (versión preliminar) | Microsoft Docs
description: Notas de la versión del agente de Azure File Sync (versión preliminar)
services: storage
author: wmgries
manager: aungoo
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: wgries
ms.openlocfilehash: 946311c42088d3a5840eb35387c8a552d3d5d70f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735651"
---
# <a name="release-notes-for-the-azure-file-sync-agent-preview"></a>Notas de la versión del agente de Azure File Sync (versión preliminar)
Azure File Sync le permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Las instalaciones de Windows Server se transforman en una memoria caché rápida de los recursos compartidos de archivos de Azure. Puede usar cualquier protocolo disponible en Windows Server para acceder a los datos localmente, como SMB, NFS y FTPS. Puede tener todas las cachés que necesite en todo el mundo.

En este artículo se tratan las notas de la versión de las versiones compatibles del agente de Azure File Sync.

## <a name="supported-versions"></a>Versiones compatibles
Las siguientes versiones son compatibles con Azure File Sync:

| Hito | Número de versión del agente | Fecha de lanzamiento | Status |
|----|----------------------|--------------|------------------|
| Actualización 2 | 3.0.12.0 | 22 de mayo de 2018 | Compatible (versión recomendada) |
| Paquete acumulativo de actualizaciones de abril | 2.3.0.0 | 8 de mayo de 2018 | Compatible |
| Paquete acumulativo de actualizaciones de marzo | 2.2.0.0 | 12 de marzo de 2018 | Compatible |
| Paquete acumulativo de actualizaciones de febrero | 2.1.0.0 | 28 de febrero de 2018 | Compatible |
| Actualización 1 | 2.0.11.0 | 8 de febrero de 2018 | Compatible |
| Paquete acumulativo de actualizaciones de enero | 1.4.0.0 | 8 de enero de 2018 | Compatible |
| Paquete acumulativo de actualizaciones de noviembre | 1.3.0.0 | 30 de noviembre de 2017 | Compatible |
| Paquete acumulativo de actualizaciones de octubre | 1.2.0.0 | 31 de octubre de 2017 | Compatible |
| Versión preliminar inicial | 1.1.0.0 | 26 de septiembre de 2017 | Compatible |

### <a name="azure-file-sync-agent-update-policy"></a>Directiva de actualización del agente de Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-30120"></a>Versión del agente 3.0.12.0
Las notas siguientes son para la versión 3.0.12.0 del agente de Azure File Sync (publicada el 22 de mayo de 2018).

### <a name="agent-installation-and-server-configuration"></a>Instalación del agente y configuración del servidor
Para más información sobre cómo instalar y configurar el agente de Azure File Sync con Windows Server, consulte [Planeamiento de una implementación de Azure File Sync (versión preliminar)](storage-sync-files-planning.md) y [Cómo implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md).

- El paquete de instalación del agente debe instalarse con permisos elevados (administrador).
- El agente no es compatible con las opciones de implementación de Windows Server Core ni Nano Server.
- El agente solo se admite en Windows Server 2016 y Windows Server 2012 R2.
- El agente requiere al menos 2 GB de memoria física.
- El servicio del agente de sincronización de Storage (FileSyncSvc) no admite puntos de conexión de servidor ubicados en un volumen que tiene el directorio de información del volumen del sistema (SVI) comprimido. Esta configuración producirá resultados inesperados.

### <a name="interoperability"></a>Interoperabilidad
- Las aplicaciones antivirus, de copia de seguridad y de otro tipo que accedan a archivos organizados en niveles pueden provocar recuperaciones no deseadas, salvo que respeten el atributo sin conexión y pasen por alto la lectura del contenido de esos archivos. Para más información, consulte [Solución de problemas de Azure File Sync (versión preliminar)](storage-sync-files-troubleshoot.md).
- No utilice el Administrador de recursos del servidor de archivos (FSRM) ni otros filtros de archivos. Los filtros de archivos pueden provocar errores de sincronización sin fin cuando los archivos están bloqueados debido al filtro de archivos.
- No se admite la ejecución de sysprep en un servidor que tenga instalado el agente de Azure File Sync y puede provocar resultados inesperados. La instalación del agente y el registro del servidor se deben realizar después de implementar la imagen del servidor y completar la instalación mínima de sysprep.
- En el mismo volumen no se puede desduplicar datos y ordenar en niveles en la nube.

### <a name="sync-limitations"></a>Limitaciones de la sincronización
Los siguientes elementos no se sincronizan, pero el resto del sistema funciona con normalidad:
- Rutas de acceso de más de 2,048 caracteres.
- La parte de la lista de control de acceso discrecional (DACL) de un descriptor de seguridad, si es mayor de 2 KB. (Este problema aplica únicamente si tiene más de unas 40 entradas de control de acceso [ACE] en un solo elemento).
- La parte de la lista de control de acceso del sistema (SACL) de un descriptor de seguridad que se utiliza para la auditoría.
- Atributos extendidos.
- Flujos de datos alternativos.
- Puntos de repetición de análisis.
- Vínculos físicos.
- La compresión (si se establece en un archivo de servidor) no se conserva al sincronizarse los cambios con ese archivo desde otros puntos de conexión.
- Los archivos cifrados con EFS (u otros cifrados con modo de usuario) que evitan que el servicio lea los datos. 
    
    > [!Note]  
    > Azure File Sync siempre cifra los datos en tránsito. Los datos siempre se cifran en reposo en Azure.
 
### <a name="server-endpoints"></a>Puntos de conexión de servidor
- Un punto de conexión de servidor solo se puede crear en un volumen NTFS. En este momento Azure File Sync no admite ReFS, FAT, FAT32 ni otros sistemas de archivos.
- Los niveles de nube no se admiten en el volumen del sistema. Para crear un punto de conexión de servidor en el volumen del sistema, deshabilite los niveles de la nube al crear el punto de conexión de servidor.
- Los clústeres de conmutación por error solo son compatibles con discos en clúster, pero no con volúmenes compartidos de clúster (CSV).
- No se puede anidar un punto de conexión de servidor. Puede coexistir en el mismo volumen en paralelo con otro punto de conexión.
- No almacene un archivo de paginación de aplicación o sistema operativo en un punto de conexión de servidor.
- Los archivos en niveles quedarán inservibles si los archivos no se vuelven a llamar antes de eliminar el punto de conexión de servidor.
 
### <a name="cloud-tiering"></a>Niveles de nube
- Si un archivo en niveles se copia en otra ubicación mediante el uso de Robocopy, el archivo resultante no estará en niveles. El atributo sin conexión podría estar establecido porque Robocopy incluye incorrectamente dicho atributo en las operaciones de copia.
- Al ver las propiedades de archivo desde un cliente de SMB, puede parecer que el atributo sin conexión se ha establecido incorrectamente debido a que SMB almacena en caché los metadatos del archivo.

## <a name="agent-version-2300"></a>Versión del agente 2.3.0.0
Las notas siguientes son para la versión 2.3.0.0 del agente de Azure File Sync publicada el 8 de mayo de 2018. Estas notas son adicionales a las notas de la versión enumeradas para la versión 2.0.11.0.

Esta versión incluye las siguientes correcciones:
- Las actualizaciones del agente pueden bloquearse si el controlador del filtro de niveles en la nube no se descarga.
- Puede disminuir el rendimiento de la sincronización al sincronizar una gran cantidad de archivos.

## <a name="agent-version-2200"></a>Versión del agente 2.2.0.0
Las notas siguientes son para la versión 2.2.0.0 del agente de Azure File Sync publicada 12 de marzo de 2018.  Estas notas son adicionales a las notas de la versión enumeradas para las versiones 2.1.0.0 y 2.0.11.0.

La instalación de v2.1.0.0 para algunos clientes puede producir un error por el hecho de que FileSyncSvc no se detenga. Esta actualización corrige ese problema.

## <a name="agent-version-2100"></a>Versión del agente 2.1.0.0
Las notas siguientes son para la versión 2.1.0.0 del agente de Azure File Sync publicada el 28 de febrero de 2018. Estas notas son adicionales a las notas de la versión enumeradas para la versión 2.0.11.0.

Esta versión incluye los siguientes cambios:
- Mejoras en el control de la conmutación por error de clústeres.
- Mejoras para el control confiable de archivos en niveles.
- Compatibilidad para la instalación del agente en máquinas de controlador de dominio que se agregan al entorno de un dominio de Windows Server 2008 R2.
- En esta versión se ha corregido la generación excesiva de diagnóstico en servidores con muchos archivos.
- Mejoras en el control de errores en la sesión.
- Mejoras en el control de errores en los problemas de transferencia de archivos.
- Cambiado en esta versión: el intervalo predeterminado para la ejecución de niveles en la nube cuando está habilitada en el punto de conexión de un servidor actualmente es 1 hora. 
- Problema de bloqueo temporal: traslado de recursos de Azure File Sync (servicio de sincronización de almacenamiento) a una nueva suscripción de Azure.

## <a name="agent-version-20110"></a>Versión de agente 2.0.11.0
Las notas siguientes son para la versión 2.0.11.0 del agente de Azure File Sync (publicada el 9 de febrero de 2018). 

### <a name="agent-installation-and-server-configuration"></a>Instalación del agente y configuración del servidor
Para más información sobre cómo instalar y configurar el agente de Azure File Sync con Windows Server, consulte [Planeamiento de una implementación de Azure File Sync (versión preliminar)](storage-sync-files-planning.md) y [Cómo implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md).

- El paquete de instalación de agente (MSI) debe instalarse con permisos elevados (administrador).
- El agente no se admite en las opciones de implementación de Windows Server Core ni Nano Server.
- El agente solo se admite en Windows Server 2016 y Windows Server 2012 R2.
- El agente requiere al menos 2 GB de memoria física.

### <a name="interoperability"></a>Interoperabilidad
- Las aplicaciones antivirus, de copia de seguridad y de otro tipo que accedan a archivos organizados en niveles pueden provocar recuperaciones no deseadas, salvo que respeten el atributo sin conexión y pasen por alto la lectura del contenido de esos archivos. Para más información, consulte [Solución de problemas de Azure File Sync (versión preliminar)](storage-sync-files-troubleshoot.md).
- Esta versión agrega compatibilidad con DFS-R. Para más información, consulte la [Guía de planeamiento](storage-sync-files-planning.md#distributed-file-system-dfs).
- No utilice el Administrador de recursos del servidor de archivos (FSRM) ni otros filtros de archivos. Los filtros de archivos pueden provocar errores de sincronización sin fin cuando los archivos están bloqueados debido al filtro de archivos.
- La duplicación de los servidores registrados (incluida la clonación de máquinas virtuales) puede provocar resultados inesperados. En concreto, la sincronización podría no converger nunca.
- En el mismo volumen no se puede desduplicar datos y ordenar en niveles en la nube.
 
### <a name="sync-limitations"></a>Limitaciones de la sincronización
Los siguientes elementos no se sincronizan, pero el resto del sistema funciona con normalidad:
- Rutas de acceso de más de 2,048 caracteres.
- La parte de la lista de control de acceso discrecional (DACL) de un descriptor de seguridad, si es mayor de 2 KB. (Este problema aplica únicamente si tiene más de unas 40 entradas de control de acceso [ACE] en un solo elemento).
- La parte de la lista de control de acceso del sistema (SACL) de un descriptor de seguridad que se utiliza para la auditoría.
- Atributos extendidos.
- Flujos de datos alternativos.
- Puntos de repetición de análisis.
- Vínculos físicos.
- La compresión (si se establece en un archivo de servidor) no se conserva al sincronizarse los cambios con ese archivo desde otros puntos de conexión.
- Los archivos cifrados con EFS (u otros cifrados con modo de usuario) que evitan que el servicio lea los datos. 
    
    > [!Note]  
    > Azure File Sync siempre cifra los datos en tránsito. Los datos siempre se cifran en reposo en Azure.
 
### <a name="server-endpoints"></a>Puntos de conexión de servidor
- Un punto de conexión de servidor solo se puede crear en un volumen NTFS. En este momento Azure File Sync no admite ReFS, FAT, FAT32 ni otros sistemas de archivos.
- Un punto de conexión de servidor no puede estar en el volumen del sistema. Por ejemplo, C:\MyFolder no es una ruta de acceso aceptable, a menos que C:\MyFolder sea un punto de montaje.
- Los clústeres de conmutación por error solo son compatibles con discos en clúster, pero no con volúmenes compartidos de clúster (CSV).
- No se puede anidar un punto de conexión de servidor. Puede coexistir en el mismo volumen en paralelo con otro punto de conexión.
- En esta versión se ha agregado compatibilidad para que la raíz de sincronización se encuentre en la raíz de un volumen.
- No almacene un archivo de paginación de aplicación o sistema operativo en un punto de conexión de servidor.
- Una novedad de esta versión es que se han añadido nuevos eventos para realizar un seguimiento del tiempo de ejecución total para los niveles de nube (EventID 9016), el progreso de carga de sincronización (EventID 9302) y los archivos que no se han sincronizado (EventID 9900).
- En esta versión se incluyen las siguientes mejoras: 
- Considerable incremento del rendimiento de sincronización de espacios de nombres de recuperación ante desastres rápida.
- No es necesario eliminar grandes cantidades de directorios (más de 10 000) en lotes con v2*.
 
### <a name="cloud-tiering"></a>Niveles de nube
- Modificado respecto a la versión anterior: los nuevos archivos están organizados en niveles dentro de 1 hora (anteriormente 32 horas) sujeto a la configuración de directiva de niveles. Se proporciona un cmdlet de PowerShell para establecer niveles a petición. Puede usar el cmdlet para evaluar los niveles de manera más eficaz sin tener que esperar al proceso en segundo plano.
- Si un archivo en niveles se copia en otra ubicación mediante el uso de Robocopy, el archivo resultante no estará en niveles. El atributo sin conexión podría estar establecido porque Robocopy incluye incorrectamente dicho atributo en las operaciones de copia.
- Al ver las propiedades de archivo desde un cliente de SMB, puede parecer que el atributo sin conexión se ha establecido incorrectamente debido a que SMB almacena en caché los metadatos del archivo.
- Una novedad con respecto a la versión anterior es que los archivos ahora se descargan como archivos organizados en niveles en otros servidores, siempre que el archivo sea nuevo o ya sea un archivo organizado en niveles.

## <a name="agent-version-1100"></a>Versión de agente 1.1.0.0
Las notas siguientes son para la versión 1.1.0.0 del agente de Azure File Sync (publicada el 9 de septiembre de 2017 en versión preliminar inicial). 

### <a name="agent-installation-and-server-configuration"></a>Instalación del agente y configuración del servidor
Para más información sobre cómo instalar y configurar el agente de Azure File Sync con Windows Server, consulte [Planeamiento de una implementación de Azure File Sync (versión preliminar)](storage-sync-files-planning.md) y [Cómo implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md).

- El paquete de instalación de agente (MSI) debe instalarse con permisos elevados (administrador).
- El agente no se admite en las opciones de implementación de Windows Server Core ni Nano Server.
- El agente solo se admite en Windows Server 2016 y 2012 R2.
- El agente requiere al menos 2 GB de memoria física.

### <a name="interoperability"></a>Interoperabilidad
- Las aplicaciones antivirus, de copia de seguridad y de otro tipo que accedan a archivos organizados en niveles pueden provocar recuperaciones no deseadas, salvo que respeten el atributo sin conexión y pasen por alto la lectura del contenido de esos archivos. Para más información, consulte [Solución de problemas de Azure File Sync (versión preliminar)](storage-sync-files-troubleshoot.md).
- No utilice FSRM ni otros filtros de archivos. Los filtros de archivos pueden provocar errores de sincronización sin fin cuando los archivos están bloqueados debido al filtro de archivos.
- La duplicación de los servidores registrados (incluida la clonación de máquinas virtuales) puede provocar resultados inesperados. En concreto, la sincronización podría no converger nunca.
- En el mismo volumen no se puede desduplicar datos y ordenar en niveles en la nube.
 
### <a name="sync-limitations"></a>Limitaciones de la sincronización
Los siguientes elementos no se sincronizan, pero el resto del sistema funciona con normalidad:
- Rutas de acceso de más de 2,048 caracteres.
- La parte de la DACL de un descriptor de seguridad si es mayor de 2 KB. (Este problema aplica únicamente si tiene más de unas 40 entradas de control de acceso (ACE) en un solo elemento).
- La parte de la SACL de un descriptor de seguridad que se utiliza para la auditoría.
- Atributos extendidos.
- Flujos de datos alternativos.
- Puntos de repetición de análisis.
- Vínculos físicos.
- La compresión (si se establece en un archivo de servidor) no se conserva al sincronizarse los cambios con ese archivo desde otros puntos de conexión.
- Los archivos cifrados con EFS (u otros cifrados con modo de usuario) que evitan que el servicio lea los datos. 
    
    > [!Note]  
    > Azure File Sync siempre cifra los datos en tránsito. Los datos siempre se cifran en reposo en Azure.
 
### <a name="server-endpoints"></a>Puntos de conexión de servidor
- Un punto de conexión de servidor solo se puede crear en un volumen NTFS. En este momento Azure File Sync no admite ReFS, FAT, FAT32 ni otros sistemas de archivos.
- Un punto de conexión de servidor no puede estar en el volumen del sistema. Por ejemplo, C:\MyFolder no es una ruta de acceso aceptable, a menos que C:\MyFolder sea un punto de montaje.
- Los clústeres de conmutación por error solo son compatibles con discos en clúster y no con CSV.
- No se puede anidar un punto de conexión de servidor. Puede coexistir en el mismo volumen en paralelo con otro punto de conexión.
- La eliminación de un gran número (más de 10 000) de directorios desde un servidor de una sola vez puede provocar errores de sincronización. Elimine directorios en lotes de menos de 10 000. Asegúrese de que la sincronización de las operaciones de eliminación es correcta antes de eliminar el siguiente lote.
- Actualmente no se admite un punto de conexión de servidor en la raíz de un volumen.
- No almacene un archivo de paginación de aplicación o sistema operativo en un punto de conexión de servidor.
 
### <a name="cloud-tiering"></a>Niveles de nube
- Para asegurarse de que los archivos se pueden recuperar correctamente, el sistema podría no organizar en niveles archivos nuevos o modificados automáticamente hasta 32 horas. Este proceso incluye la primera organización en niveles después de configurar un nuevo punto de conexión de servidor. Se proporciona un cmdlet de PowerShell para establecer niveles a petición. Puede usar el cmdlet para evaluar los niveles de manera más eficaz sin tener que esperar al proceso en segundo plano.
- Si un archivo en niveles se copia en otra ubicación mediante el uso de Robocopy, el archivo resultante no estará en niveles. El atributo sin conexión podría estar establecido porque Robocopy incluye incorrectamente dicho atributo en las operaciones de copia.
- Al ver las propiedades de archivo desde un cliente de SMB, puede parecer que el atributo sin conexión se ha establecido incorrectamente debido a que SMB almacena en caché los metadatos del archivo.
