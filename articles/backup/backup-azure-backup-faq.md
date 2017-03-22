
---
title: "Preguntas más frecuentes sobre Azure Backup | Microsoft Docs"
description: "Respuestas a preguntas habituales acerca de: cómo funciona el servicio, el agente de Azure Backup, el almacén de Recovery Services y los límites de copia de seguridad y retención."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "copia de seguridad y recuperación ante desastres; servicio de copia de seguridad"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/10/2017
ms.author: markgal;giridham;arunak;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 463e2a8af1fd319b396c6a769896344cac5f9f32
ms.lasthandoff: 03/14/2017


---
# <a name="questions-about-the-azure-backup-service"></a>Preguntas sobre el servicio Azure Backup
Este artículo tiene secciones de preguntas comunes (con respuestas) para ayudarle a comprender rápidamente los componentes de Azure Backup. En algunas de las respuestas, hay vínculos a artículos que tienen información completa. Para realizar cualquier pregunta acerca de Azure Backup, haga clic en **Comentarios** (a la derecha). Los comentarios aparecen en la parte inferior de este artículo. Para poder escribir comentarios se requiere una cuenta de Livefyre. También se pueden publicar preguntas sobre el servicio Copia de seguridad de Azure en el [foro de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Para examinar rápidamente las secciones de este artículo, use los vínculos de la derecha que aparecen debajo de **En este artículo**.


## <a name="recovery-services-vault"></a>Almacén de Servicios de recuperación

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>¿Hay algún límite del número de almacenes que se pueden crear en cada suscripción de Azure? <br/>
Sí. A partir de septiembre de 2016, puede crear 25 almacenes de Recovery Services o de copia de seguridad por suscripción. Se pueden crear hasta 25 almacenes de Recovery Services por cada región admitida de Azure Backup por suscripción. Si necesita más almacenes, cree otra suscripción.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>¿Hay algún límite en el número de servidores o máquinas que se pueden registrar en cada almacén? <br/>
Sí, se pueden registrar un máximo de 50 máquinas por almacén. Para las máquinas virtuales de IaaS de Azure, el límite es de 200 máquinas virtuales por almacén. Si necesita registrar más máquinas, cree otro almacén.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>¿Cómo registro mi servidor en otro centro de datos?<br/>
Los datos de copia de seguridad se envían al centro de datos del almacén en el que está registrado. La forma más sencilla de cambiar el centro de datos es desinstalar el agente y volver a instalarlo y registrarlo en un nuevo almacén que pertenece al centro de datos deseado.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Si la organización tiene un almacén, ¿cómo se pueden aislar los datos de un servidor desde otro servidor al restaurar los datos?<br/>
Los servidores registrados en el mismo almacén podrán recuperar los datos cuya copia de seguridad hayan realizado otros servidores *que usen la misma frase de contraseña*. Si tiene servidores cuyos datos de copia de seguridad desee aislar de otros servidores de la organización, utilice una frase de contraseña específica para dichos servidores. Por ejemplo, los servidores de recursos humanos podrían usar una frase de contraseña de cifrado, los servidores de contabilidad, otra y los servidores de almacenamiento, otra distinta.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>¿Cuál es el requisito de tamaño mínimo para la carpeta de caché? <br/>
El tamaño de la carpeta de caché determina la cantidad de datos de los que se realiza la copia de seguridad. La carpeta de caché debe tener un 5% del espacio requerido para el almacenamiento de datos.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>¿Puedo "migrar" mi almacén o datos de copia de seguridad de una suscripción a otra? <br/>
No. El almacén se crea en un nivel de suscripción y no se puede reasignar a otra suscripción una vez que se crea.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Los almacenes de Recovery Services se basan en Resource Manager. ¿Se admiten aún los almacenes de Backup (modo clásico)? <br/>
Todos los almacenes de Backup existentes en el [portal clásico](https://manage.windowsazure.com) siguen siendo compatibles. Sin embargo, ya no podrá usar el portal clásico para implementar nuevos almacenes de Backup. Microsoft recomienda el uso de almacenes de Recovery Services para todas las implementaciones porque las futuras mejoras solo se aplican a almacenes de Recovery Services. Si intenta crear un almacén de Backup en el portal clásico, se le redirigirá a [Azure Portal](https://portal.azure.com).

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>¿Se puede migrar un almacén de Copia de seguridad a un almacén de Servicios de recuperación? <br/>
Por desgracia, no, no puede migrar el contenido de un almacén de Backup a un almacén de Recovery Services. Estamos trabajando para agregar esta funcionalidad, pero no está disponible actualmente.

### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>¿Admiten los almacenes de Servicios de recuperación máquinas virtuales implementadas con el modelo clásico o máquinas virtuales implementadas con Resource Manager? <br/>
Los almacenes de Servicios de recuperación admiten ambos modelos.  Puede hacer copias de seguridad de una máquina virtual clásica (creada en el portal de clásico) o de una máquina virtual de Resource Manager (creada en Azure Portal) en un almacén de Recovery Services.

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>He realizado copias de seguridad de mis máquinas virtuales clásicas en un almacén de Backup. ¿Puedo migrar mis máquinas virtuales del modo clásico al modo de Resource Manager y protegerlos en un almacén de Recovery Services?
La recuperación de máquinas virtuales clásica apunta a un almacén de copia de seguridad no migran automáticamente a un almacén de Recovery Services cuando se mueve la máquina virtual del modo clásico al de Resource Manager. Siga estos pasos para transferir las copias de seguridad de máquinas virtuales:

1. En el almacén de Backup, vaya a la pestaña **Elementos protegidos** y seleccione la máquina virtual. Haga clic en [Detener protección](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deje la opción *Eliminar los datos de copia de seguridad asociados***desactivada**.
2. Migre la máquina virtual del modo clásico al modo de Resource Manager. Asegúrese de que la información de almacenamiento y red correspondiente a la máquina virtual también se migra al modo de Resource Manager.
3. Cree un almacén de Recovery Services y configure la copia de seguridad de la máquina virtual migrada mediante la acción **Copia de seguridad** sobre el panel del almacén. Para más información sobre la copia de seguridad de una máquina virtual en un almacén de Recovery Services, consulte el artículo [Primer análisis: protección de máquinas virtuales con un almacén de Recovery Services](backup-azure-vms-first-look-arm.md).



## <a name="azure-backup-agent"></a>Agente de copia de seguridad de Azure

### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>¿Dónde puedo descargar el agente de Copia de seguridad de Azure más reciente? <br/>
El agente más reciente para realizar copias de seguridad de Windows Server, System Center DPM o el cliente de Windows se puede descargar de [aquí](http://aka.ms/azurebackup_agent). Si desea hacer una copia de seguridad de una máquina virtual, utilice el agente de máquina virtual (que instala automáticamente la extensión adecuada). El agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Al configurar al agente de Copia de seguridad de Azure, se me solicita que especifique las credenciales de almacén. ¿Expiran las credenciales de almacén?
Sí, las credenciales de almacén expiran a las 48 horas. Si el archivo caduca, inicie sesión en el Portal de Azure y descargue los archivos de credenciales de almacén desde el almacén.

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>¿Qué ocurre si cambio el nombre de un servidor de Windows de cuyos datos se está realizando una copia de seguridad en Azure?<br/>
Al cambiar el nombre de un servidor, se detienen todas las copias de seguridad configuradas actualmente.
Registre el nuevo nombre del servidor en el almacén de Backup. Al registrar el nuevo nombre en el almacén, la primera operación de copia de seguridad es *completa*. Si necesita recuperar los datos de una copia de seguridad de un almacén con el nombre de servidor antiguo, puede utilizar la opción [**Otro servidor**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) del **Asistente para recuperar datos**.

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

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>¿Cuál es la longitud máxima de la ruta de acceso que se puede especificar en la directiva de Backup mediante el agente de Azure Backup? <br/>
El agente de Copia de seguridad de Azure usa NTFS. La [especificación de longitud de la ruta de acceso del archivo está limitada por la API de Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Si los archivos que desea proteger tienen una ruta de acceso mayor de lo que permite la API de Windows, realice la copia de seguridad de la carpeta primaria o de la unidad de disco.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>¿Qué caracteres se permiten en la ruta de acceso de archivo de la directiva de Copia de seguridad de Azure cuando se usa el agente de Copia de seguridad de Azure? <br>
 El agente de Copia de seguridad de Azure usa NTFS. Permite [caracteres compatibles con NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) como parte de la especificación de los archivos.  

### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>¿Cómo se cambia la ubicación de la memoria caché especificada para el agente de Copia de seguridad de Azure?<br/>
Use la siguiente lista para cambiar la ubicación de caché.

* Detenga el módulo de copia de seguridad, para lo que debe ejecutar el siguiente comando en un símbolo del sistema con privilegios elevados:

```PS C:\> Net stop obengine```
* No mueva los archivos. En su lugar, copie la carpeta de espacio en caché en otra unidad con espacio suficiente. El espacio en caché original se puede eliminar después de confirmar que las copias de seguridad funcionan con el espacio en caché nuevo.
* Actualice las siguientes entradas del Registro con la ruta de acceso a la nueva carpeta del espacio en caché.<br/>

| Ruta de acceso del Registro | Clave del Registro | Valor |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nueva ubicación de la carpeta de la memoria caché* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nueva ubicación de la carpeta de la memoria caché* |

* Reinicie el módulo de copia de seguridad, para lo que debe ejecutar el siguiente comando en un símbolo del sistema con privilegios elevados:

```PS C:\> Net start obengine```

Una vez que la creación de la copia de seguridad se haya completado correctamente en la nueva ubicación de la memoria caché, puede quitar la carpeta de la memoria caché original.

### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>¿Dónde se puede colocar la carpeta de caché del agente de Copia de seguridad de Azure para que funcione según lo previsto?<br/>
No se recomiendan las siguientes ubicaciones para la carpeta de caché:

* Recursos compartidos de red o medios extraíbles: la carpeta de caché debe ser local para el servidor que necesita realizar copias de seguridad mediante la copia de seguridad en línea. No se admiten ubicaciones de red ni medios extraíbles como unidades USB.
* Volúmenes sin conexión: la carpeta de caché debe estar en línea para la copia de seguridad esperada con el agente de Copia de seguridad de Azure.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>¿Hay algún atributo de la carpeta de caché que no se admita?<br/>
No se admiten los siguientes atributos ni sus combinaciones para la carpeta de caché:

* Cifrados
* Desduplicados
* Comprimidos
* Dispersos
* Punto de reanálisis

Ni la carpeta de caché ni los metadatos del disco duro virtual tienen los atributos necesarios para el agente de Azure Backup.


## <a name="virtual-machines"></a>Máquinas virtuales

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>¿Se puede instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure de la que el servicio Copia de seguridad de Azure ya ha realizado una copia de seguridad mediante la extensión de máquina virtual? <br/>
Totalmente. Copia de seguridad de Azure proporciona copia de seguridad de nivel de máquina virtual para las máquinas virtuales de Azure que usen la extensión de máquina virtual. Para proteger los archivos y carpetas del SO invitado de Windows, instale en él el agente de Azure Backup.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>¿Puedo instalar el agente de Copia de seguridad de Azure en una máquina virtual de Azure para realizar la copia de seguridad de los archivos y carpetas que hay en un almacenamiento temporal proporcionado por la máquina virtual de Azure? <br/>
Sí. Instale el agente de Azure Backup en el SO invitado de Windows y realice una copia de seguridad de los archivos y las carpetas en un almacenamiento temporal. Las copias de seguridad de los trabajos dejarán de funcionar cuando se borren los datos del almacenamiento temporal. Además, si se han eliminado los datos del almacenamiento temporal, la restauración solo se podrá realizar en un almacenamiento no volátil.


## <a name="azure-backup-server-and-data-protection-manager"></a>Servidor de copia de seguridad de Azure y Data Protection Manager

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>¿Puedo usar el servidor de Copia de seguridad de Azure para crear una copia de seguridad de reconstrucción completa (BMR) para un servidor físico? <br/>
Sí.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>¿Qué versión de System Center Data Protection Manager se admite? <br/>
Se recomienda que instale el agente de Azure Backup [más reciente](http://aka.ms/azurebackup_agent) en el último paquete acumulativo (UR) de actualizaciones para System Center Data Protection Manager (DPM). A partir de agosto de 2016, el paquete acumulativo de actualizaciones 11 es la actualización más reciente.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>He instalado el agente de Copia de seguridad de Azure para proteger archivos y carpetas. ¿Puedo instalar ahora System Center DPM para que funcione con el agente de Azure Backup con el fin de proteger las cargas de trabajo locales de aplicaciones o de máquinas virtuales para Azure? <br/>
Para usar Azure Backup con System Center Data Protection Manager (DPM), instale DPM en primer lugar e instale el agente de Azure Backup. Instalar los componentes de Azure Backup en este orden garantiza que el agente funcione con DPM. No se recomienda o no se admite la instalación de Azure Backup.


## <a name="how-azure-backup-works"></a>Cómo funciona Azure Backup

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>¿Funciona el agente de Azure Backup en un servidor que usa la desduplicación de Windows Server 2012? <br/>
Sí. El servicio del agente convierte los datos desduplicados en datos normales cuando prepara la operación de copia de seguridad. Luego optimiza los datos para la copia de seguridad, los cifra y los envía al servicio de copia de seguridad en línea.

### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Si se cancela un trabajo de copia de seguridad una vez que se ha iniciado, ¿se eliminan los datos de copia de seguridad transferidos? <br/>
Nº Todos los datos transferidos al almacén, antes de que se cancelara el trabajo de copia de seguridad, permanecen en el almacén. Copia de seguridad de Azure usa un mecanismo para agregar ocasionalmente agregar puntos de control a los datos de copia de seguridad durante la copia de seguridad. Debido a que hay puntos de control en los datos de copia de seguridad, el siguiente proceso de copia de seguridad puede validar la integridad de los archivos. El siguiente trabajo de copia de seguridad será incremental a los datos que ya están en la copia de seguridad. Las copias de seguridad incrementales solo transfieren los datos nuevos o modificados, lo que equivale a una mejor utilización del ancho de banda.

Si cancela un trabajo de copia de seguridad para una máquina virtual de Azure, se omiten los datos transferidos. El siguiente trabajo de copia de seguridad transfiere los datos incrementales desde el último trabajo de copia de seguridad correcto.

### <a name="if-a-backup-job-fails-can-i-configure-the-backup-service-to-send-e-mail-br"></a>¿Puedo configurar el servicio Backup para enviar mensajes de correo electrónico si se produce un error en un trabajo de copia de seguridad? <br/>
Sí, el servicio de copia de seguridad tiene varias alertas basadas en eventos que se pueden usar con un script de PowerShell. Para obtener una descripción completa, consulte [Configuración de notificaciones](backup-azure-monitor-vms.md#configure-notifications).

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>¿Existen límites sobre cuándo y cuántas veces se puede programar un trabajo de copia de seguridad?<br/>
Sí. Sí, en estaciones de trabajo de Windows o Windows Server los trabajos de copia de seguridad se pueden ejecutar un máximo de tres veces al día. Sin embargo, en System Center DPM, los trabajos de copia de seguridad se pueden ejecutar un máximo de dos veces al día. Por último, en las máquinas virtuales de IaaS solo se puede ejecutar un trabajo de copia de seguridad al día. Puede usar la directiva de programación para estaciones de trabajo de Windows o Windows Server para especificar programaciones diarias o semanales. Mediante System Center DPM, puede especificar programaciones diarias, semanales, mensuales y anuales.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>¿Por qué el tamaño de los datos transferidos al almacén de Recovery Services es más pequeño que los datos de los que he hecho copia de seguridad?<br/>
 Todos los datos de los que se realiza una copia de seguridad desde el agente de Copia de seguridad de Azure o SCDPM o del servidor de Copia de seguridad de Azure se comprimen y se cifran antes de ser transferidos. Una vez que se aplica la compresión y el cifrado, el tamaño de los datos del almacén de copia de seguridad se reduce entre un 30% y un 40%.

 ### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>¿Hay alguna forma de ajustar la cantidad de ancho de banda que utiliza el servicio Copia de seguridad?<br/>
  Sí, use la opción **Cambiar propiedades** del agente de Copia de seguridad para ajustar el ancho de banda. Puede ajustar la cantidad de ancho de banda y los momentos en que se usa dicho ancho de banda. Para obtener instrucciones detalladas, consulte ** [Habilitación de la limitación de la red](backup-configure-vault.md#enable-network-throttling)**.



## <a name="what-can-i-back-up"></a>¿De qué puedo hacer copia de seguridad?

### <a name="which-operating-systems-do-azure-backup-support-br"></a>¿Qué sistemas operativos admite Azure Backup? <br/>
Azure Backup admite la siguiente lista de sistemas operativos para la copia de seguridad de archivos y carpetas y aplicaciones de carga de trabajo protegidas con el servidor de copia de seguridad de Azure y System Center Data Protection Manager (DPM).

| Sistema operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows 8 y SP más recientes |64 bits |Enterprise, Pro |
| Windows 7 y SP más recientes |64 bits |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 y SP más recientes |64 bits |Enterprise, Pro |
| Windows 10 |64 bits |Enterprise, Pro, Home |
| Windows Server 2016 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 y SP más recientes |64 bits |Standard, Datacenter, Foundation |
| Windows Server 2012 y SP más recientes |64 bits |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 y SP más recientes |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 y SP más recientes |64 bits |Standard, Workgroup |
| Windows Server 2012 R2 y SP más recientes |64 bits |Essential |
| Windows Server 2008 R2 SP1 |64 bits |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64 bits |Standard, Enterprise, Datacenter, Foundation |

**Para la copia de seguridad de máquinas virtuales de Azure:**

* **Linux**: Copia de seguridad de Azure admite [una lista de distribuciones aprobadas por Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md) , con la excepción de CoreOS Linux.  Otras distribuciones con la iniciativa "traiga su propio Linux" también podrían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual y haya compatibilidad con Python.
* **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>¿Hay algún límite en el tamaño de cada origen de datos del que se realiza una copia de seguridad? <br/>
No hay ningún límite en la cantidad de datos de los que se puede hacer una copia de seguridad en un almacén de datos. Azure Backup limita el tamaño máximo del origen de datos; sin embargo, estos límites son grandes. A partir de agosto de 2015, el tamaño máximo del origen de datos del sistema operativo compatible es:

| S.No | Sistema operativo | Tamaño máximo del origen de datos |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 o superior |54&400; GB |
| 2 |Windows 8 o posterior |54&400; GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

La tabla siguiente explica cómo se determina el tamaño de cada origen de datos.

| Origen de datos | Detalles |
|:---:|:--- |
| Volumen |La cantidad de datos de los que se realiza copia de seguridad de un volumen individual de un equipo servidor o cliente. |
| Máquina virtual de Hyper-V |Suma de los datos de todos los discos duros virtuales de la máquina virtual de los que se hace copia de seguridad |
| Base de datos de Microsoft SQL Server |Tamaño de una sola base de datos SQL de la que se hace copia de seguridad |
| Microsoft SharePoint |Suma de las bases de datos de contenido y configuración de la granja de SharePoint de las que se hace copia de seguridad |
| Microsoft Exchange |Suma de todas las bases de datos de un servidor de Exchange de las que se hace copia de seguridad |
| Estado del sistema y BMR |Cada copia individual del estado del sistema o BMR del equipo del que se hace copia de seguridad |



## <a name="retention-policy-and-recovery-points"></a>Puntos de recuperación y directiva de retención

### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>¿Hay alguna diferencia entre las directiva de retención de DPM y Windows Server o cliente de Windows (es decir, en Windows Server sin DPM)?<br/>
No, tanto DPM como Windows Server o el cliente Windows tienen directivas de retención diarias, semanales, mensuales y anuales.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>¿Puedo configurar de forma selectiva mis directivas de retención (es decir, configurar semanal y diariamente, pero no anual y mensualmente)?<br/>
Sí, la estructura de retención de la Copia de seguridad de Azure permite tener una flexibilidad completa en la definición de la directiva de retención según sus requisitos.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>¿Puedo programar una copia de seguridad a las 6 p.m. y establecer las directivas de retención a una hora diferente?<br/>
No. Las directivas de retención solo pueden aplicarse a puntos de copia de seguridad. En la siguiente ilustración, la directiva de retención se ha especificado para las copias de seguridad de las 12 a.m. y las 6 p.m. <br/>

![Programación de copia de seguridad y retención](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>¿Se transfiere una copia incremental para las directivas de retención programadas? <br/>
No, la copia incremental se envía en el momento mencionado en la página de programación de copia de seguridad. Los puntos que se pueden retener se determinan según la directiva de retención.

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Si se conserva una copia de seguridad durante un período prolongado, ¿se tarda más tiempo en recuperar un punto de datos más antiguo? <br/>
No: el tiempo de recuperación del punto de datos más antiguo o más reciente es el mismo. Cada punto de recuperación se comporta como un punto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Si cada punto de recuperación es como un punto completo, ¿afecta esto al almacenamiento de copia de seguridad facturable total?<br/>
Los productos con un punto de retención a largo plazo típicos almacenan los datos de copia de seguridad como puntos completos. Los puntos completos *no son eficientes* para el almacenamiento, pero resultan más fáciles y rápidos de restaurar. Las copias incrementales son *eficientes* para el almacenamiento, pero requieren que se restaure una cadena de datos, lo que afecta al tiempo de recuperación. La arquitectura de almacenamiento de Copia de seguridad de Azure le ofrece lo mejor de ambos mundos ya que permite almacenar de forma óptima datos para conseguir restauraciones más rápidas e incurrir en pocos costos de almacenamiento. Este enfoque del almacenamiento de datos garantiza que el ancho de banda de entrada y salida se utiliza de manera eficiente. Tanto la cantidad de almacenamiento de datos como el tiempo necesario para recuperar los datos se reducen al mínimo. Obtenga más información sobre la eficacia del almacenamiento de las [copias de seguridad incrementales](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) .

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>¿Hay un límite en el número de puntos de recuperación que se pueden crear?<br/>
Puede crear hasta 9999 puntos de recuperación por instancia protegida. Una instancia protegida es un equipo, un servidor (físico o virtual) o una carga de trabajo configurada para realizar copias de seguridad en Azure. No hay ningún límite en el número de instancias protegidas por el almacén de copia de seguridad. Para más información, consulte las explicaciones de [Copia de seguridad y retención](./backup-introduction-to-azure-backup.md#backup-and-retention) y [Descripción de una instancia protegida](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance).

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>¿Cuántas recuperaciones puedo realizar en los datos cuya copia de seguridad se crea en Azure?<br/>
No hay ningún límite en cuanto al número de recuperaciones de la Copia de seguridad de Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Al restaurar los datos, ¿tengo que pagar por el tráfico de salida de Azure? <br/>
Nº Sus recuperaciones son gratuitas y no se cobra por el tráfico de salida.

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Aparece la advertencia: "Azure Backups have not been configured for this server" (No se ha configurado Azure Backup para este servidor) aunque he configurado una directiva de copia de seguridad <br/>
Esta advertencia se produce cuando la configuración de programación de la copia de seguridad almacenada en el servidor local no es la misma que la configuración del almacén de copia de seguridad. Cuando el servidor o la configuración se recuperan a un estado válido conocido, las programaciones de copia de seguridad pueden perder la sincronización. Si recibe esta advertencia, [vuelva a configurar la directiva de copia de seguridad](backup-azure-manage-windows-server.md) y elija **Ejecutar copia de seguridad ahora** para volver a sincronizar el servidor local con Azure.



## <a name="azure-backup-encryption"></a>Cifrado de Azure Backup

### <a name="is-the-data-sent-to-azure-encrypted-br"></a>¿Se cifran los datos que se envían a Azure? <br/>
Sí. Los datos se cifran en la máquina cliente/servidor/SCDPM local mediante AES256 y se envían a través de un vínculo HTTPS seguro.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>¿También se cifran los datos de copia de seguridad en Azure?<br/>
Sí. Los datos que se envían a Azure permanecen cifrados (en reposo). Microsoft no descifra los datos de copia de seguridad en ningún momento. Al realizar una copia de seguridad de una máquina virtual de Azure, Azure Backup se basa en el cifrado de la máquina virtual. Por ejemplo, si la máquina virtual utiliza Azure Disk Encryption o alguna otra tecnología de cifrado, Azure Backup usa dicho cifrado para proteger los datos.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>¿Cuál es la longitud mínima de la clave de cifrado utilizada para cifrar los datos de copia de seguridad? <br/>
La clave de cifrado debe tener al menos 16 caracteres.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>¿Qué sucede si pierdo la clave de cifrado? ¿Puedo recuperar los datos (o) puede Microsoft recuperar los datos? <br/>
La clave utilizada para cifrar los datos de copia de seguridad está presente en las instalaciones del cliente. Microsoft no mantiene una copia en Azure y no tiene acceso a la clave. Si el cliente pierde la clave, Microsoft no puede recuperar los datos de copia de seguridad.

