---
title: "Preguntas más frecuentes sobre Azure Backup Agent | Microsoft Docs"
description: "Respuestas a preguntas habituales sobre cómo funciona Azure Backup Agent y los límites de copia de seguridad y retención."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "copia de seguridad y recuperación ante desastres; servicio de copia de seguridad"
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 227cdc87f3e2c8ed393145f4bbde7f74606bdf3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="questions-about-the-azure-backup-agent"></a>Preguntas sobre Azure Backup Agent
Este artículo contiene las respuestas a preguntas comunes que le ayudan a comprender rápidamente los componentes de Azure Backup Agent. En algunas de las respuestas, hay vínculos a artículos que tienen información completa. También se pueden publicar preguntas sobre el servicio Copia de seguridad de Azure en el [foro de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configuración de la copia de seguridad
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>¿Dónde puedo descargar el agente de Copia de seguridad de Azure más reciente? <br/>
El agente más reciente para realizar copias de seguridad de Windows Server, System Center DPM o el cliente de Windows se puede descargar de [aquí](http://aka.ms/azurebackup_agent). Si desea hacer una copia de seguridad de una máquina virtual, utilice el agente de máquina virtual (que instala automáticamente la extensión adecuada). El agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Al configurar al agente de Copia de seguridad de Azure, se me solicita que especifique las credenciales de almacén. ¿Expiran las credenciales de almacén?
Sí, las credenciales de almacén expiran a las 48 horas. Si el archivo caduca, inicie sesión en el Portal de Azure y descargue los archivos de credenciales de almacén desde el almacén.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>¿Desde qué tipos de unidades puedo realizar copias de seguridad de archivos y carpetas? <br/>
No se puede hacer copias de seguridad de las siguientes unidades/volúmenes:

* Medios extraíbles: todos los orígenes de elementos de copia de seguridad deben notificarse como corregidos.
* Volúmenes de solo lectura: el volumen debe ser grabable para que el servicio de copia de instantáneas de volumen (VSS) funcione.
* Volúmenes sin conexión: el volumen debe estar en línea para que VSS funcione.
* Recurso compartido de red: el volumen debe ser local en el servidor para que la copia de seguridad se realice en línea.
* Volúmenes protegidos por BitLocker: el volumen debe desbloquearse antes de que se pueda crear la copia de seguridad.
* Identificación del sistema de archivos: NTFS es el único sistema de archivos admitido.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>¿De qué tipos de archivos y carpetas puedo hacer copias de seguridad desde mi servidor?<br/>
Se admiten los siguientes tipos:

* Cifrados
* Comprimidos
* Dispersos
* Comprimidos + dispersos
* Vínculos físicos: no compatibles, se omiten
* Puntos de repetición: no compatibles, se omiten
* Cifrados + dispersos: no compatibles, se omiten
* Secuencias comprimidas: no compatibles, se omiten
* Secuencias dispersas: no compatibles, se omiten

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>¿Se puede instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure de la que el servicio Copia de seguridad de Azure ya ha realizado una copia de seguridad mediante la extensión de máquina virtual? <br/>
Totalmente. Copia de seguridad de Azure proporciona copia de seguridad de nivel de máquina virtual para las máquinas virtuales de Azure que usen la extensión de máquina virtual. Para proteger los archivos y carpetas del SO invitado de Windows, instale en él el agente de Azure Backup.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>¿Puedo instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure para realizar la copia de seguridad de los archivos y carpetas que hay en un almacenamiento temporal proporcionado por la máquina virtual de Azure? <br/>
Sí. Instale el agente de Azure Backup en el SO invitado de Windows y realice una copia de seguridad de los archivos y las carpetas en un almacenamiento temporal. Las copias de seguridad de los trabajos dejarán de funcionar cuando se borren los datos del almacenamiento temporal. Además, si se han eliminado los datos del almacenamiento temporal, la restauración solo se podrá realizar en un almacenamiento no volátil.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>¿Cuál es el requisito de tamaño mínimo para la carpeta de caché? <br/>
El tamaño de la carpeta de caché determina la cantidad de datos de los que se realiza la copia de seguridad. La carpeta de caché debe tener un 5% del espacio requerido para el almacenamiento de datos.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>¿Cómo registro mi servidor en otro centro de datos?<br/>
Los datos de copia de seguridad se envían al centro de datos del almacén en el que está registrado. La forma más sencilla de cambiar el centro de datos es desinstalar el agente y volver a instalarlo y registrarlo en un nuevo almacén que pertenece al centro de datos deseado.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>¿Funciona el agente de Azure Backup en un servidor que usa la desduplicación de Windows Server 2012? <br/>
Sí. El servicio del agente convierte los datos desduplicados en datos normales cuando prepara la operación de copia de seguridad. Luego optimiza los datos para la copia de seguridad, los cifra y los envía al servicio de copia de seguridad en línea.

## <a name="backup"></a>Copia de seguridad
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>¿Cómo se cambia la ubicación de la memoria caché especificada para el agente de Copia de seguridad de Azure?<br/>
Use la siguiente lista para cambiar la ubicación de caché.

1. Detenga el módulo de copia de seguridad, para lo que debe ejecutar el siguiente comando en un símbolo del sistema con privilegios elevados:

    ```PS C:\> Net stop obengine``` 
  
2. No mueva los archivos. En su lugar, copie la carpeta de espacio en caché en otra unidad con espacio suficiente. El espacio en caché original se puede eliminar después de confirmar que las copias de seguridad funcionan con el espacio en caché nuevo.
3. Actualice las siguientes entradas del Registro con la ruta de acceso a la nueva carpeta del espacio en caché.<br/>

    | Ruta de acceso del Registro | Clave del Registro | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nueva ubicación de la carpeta de la memoria caché* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nueva ubicación de la carpeta de la memoria caché* |

4. Reinicie el módulo de copia de seguridad, para lo que debe ejecutar el siguiente comando en un símbolo del sistema con privilegios elevados:

    ```PS C:\> Net start obengine```

Una vez que la creación de la copia de seguridad se haya completado correctamente en la nueva ubicación de la memoria caché, puede quitar la carpeta de la memoria caché original.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>¿Dónde se puede colocar la carpeta de caché para que Azure Backup Agent funcione según lo previsto?<br/>
No se recomiendan las siguientes ubicaciones para la carpeta de caché:

* Recursos compartidos de red o medios extraíbles: la carpeta de caché debe ser local para el servidor que necesita realizar copias de seguridad mediante la copia de seguridad en línea. No se admiten ubicaciones de red ni medios extraíbles como unidades USB.
* Volúmenes sin conexión: la carpeta de caché debe estar en línea para la copia de seguridad esperada con Azure Backup Agent.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>¿Hay algún atributo de la carpeta de caché que no se admita?<br/>
No se admiten los siguientes atributos ni sus combinaciones para la carpeta de caché:

* Cifrados
* Desduplicados
* Comprimidos
* Dispersos
* Punto de reanálisis

Ni la carpeta de caché ni los metadatos del disco duro virtual tienen los atributos necesarios para el agente de Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>¿Hay alguna forma de ajustar la cantidad de ancho de banda que utiliza el servicio Copia de seguridad?<br/>
  Sí, use la opción **Cambiar propiedades** del agente de Copia de seguridad para ajustar el ancho de banda. Puede ajustar la cantidad de ancho de banda y los momentos en que se usa dicho ancho de banda. Para obtener instrucciones detalladas, consulte  **[Habilitación de la limitación de la red](backup-configure-vault.md#enable-network-throttling)**.

## <a name="manage-backups"></a>Administración de copias de seguridad
### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>¿Qué ocurre si cambio el nombre de un servidor de Windows de cuyos datos se está realizando una copia de seguridad en Azure?<br/>
Al cambiar el nombre de un servidor, se detienen todas las copias de seguridad configuradas actualmente.
Registre el nuevo nombre del servidor en el almacén de Backup. Al registrar el nuevo nombre en el almacén, la primera operación de copia de seguridad es *completa*. Si necesita recuperar los datos de una copia de seguridad de un almacén con el nombre de servidor antiguo, puede utilizar la opción [**Otro servidor**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) del **Asistente para recuperar datos**.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>¿Cuál es la longitud máxima de la ruta de acceso que se puede especificar en la directiva de Backup mediante el agente de Azure Backup? <br/>
El agente de Copia de seguridad de Azure usa NTFS. La [especificación de longitud de la ruta de acceso del archivo está limitada por la API de Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Si los archivos que desea proteger tienen una ruta de acceso mayor de lo que permite la API de Windows, realice la copia de seguridad de la carpeta primaria o de la unidad de disco.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>¿Qué caracteres se permiten en la ruta de acceso de archivo de la directiva de Copia de seguridad de Azure cuando se usa el agente de Copia de seguridad de Azure? <br>
 El agente de Copia de seguridad de Azure usa NTFS. Permite [caracteres compatibles con NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) como parte de la especificación de los archivos. 
 
### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Aparece la advertencia: "Azure Backups have not been configured for this server" (No se ha configurado Azure Backup para este servidor) aunque he configurado una directiva de copia de seguridad <br/>
Esta advertencia se produce cuando la configuración de programación de la copia de seguridad almacenada en el servidor local no es la misma que la configuración del almacén de copia de seguridad. Cuando el servidor o la configuración se recuperan a un estado válido conocido, las programaciones de copia de seguridad pueden perder la sincronización. Si recibe esta advertencia, [vuelva a configurar la directiva de copia de seguridad](backup-azure-manage-windows-server.md) y elija **Ejecutar copia de seguridad ahora** para volver a sincronizar el servidor local con Azure.
