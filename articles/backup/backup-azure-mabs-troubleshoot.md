---
title: "Solución de problemas de Azure Backup Server | Microsoft Docs"
description: "Solucionar problemas de instalación, registro de Azure Backup Server y copia de seguridad y restauración de las cargas de trabajo de la aplicación"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pullabhk;markgal;
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 71da98bf6d53ab50df4f6e40cf0b548752d10f93
ms.contentlocale: es-es
ms.lasthandoff: 09/28/2017

---

# <a name="troubleshoot-azure-backup-server"></a>Solución de problemas de Azure Backup Server

Puede solucionar los errores detectados al usar Azure Backup Server con la información incluida en la tabla siguiente.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Error: Se han proporcionado credenciales de almacén no válidas. El archivo está dañado o no tiene asociadas las credenciales más recientes para el servicio de recuperación. 

Siga estos [pasos de solución de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) para resolver esta incidencia.

## <a name="error-the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>Error: Error en la operación del agente debido a un error de comunicación con el servicio Coordinador de agentes DPM en el servidor. 

Siga estos [pasos de solución de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) para resolver esta incidencia.

## <a name="error-setup-could-not-update-registry-metadata"></a>Error: El programa de instalación no pudo actualizar los metadatos del Registro.

Siga estos [pasos de solución de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues) para resolver esta incidencia.


## <a name="installation-issues"></a>Problemas de instalación

| Operación | Detalles del error | Solución alternativa |
|-----------|---------------|------------|
|Instalación | El programa de instalación no pudo actualizar los metadatos del Registro. Este error de actualización podría provocar un uso excesivo de almacenamiento. Para evitar esto, actualice la entrada del Registro del recorte de ReFS. | Ajustar la clave del Registro SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim. Establezca el valor Dword en 1. |
|Instalación | El programa de instalación no pudo actualizar los metadatos del Registro. Este error de actualización podría provocar un uso excesivo de almacenamiento. Para evitar esto, actualice la entrada del Registro de SnapOptimization del volumen. | Cree la clave del Registro, SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds, con un valor de cadena vacía. |

## <a name="registration-and-agent-related-issues"></a>Problemas relacionados con el registro y el agente
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Registrar en un almacén | Se han proporcionado credenciales de almacén no válidas. El archivo está dañado o no tiene asociadas las credenciales más recientes para el servicio de recuperación. | Para solucionar este error:  <ol><li> Descargue el archivo de credenciales más reciente del almacén e inténtelo de nuevo. <br>O BIEN</li> <li> Si la acción anterior no funciona, intente descargar las credenciales en un directorio local diferente o cree un nuevo almacén. <br>O BIEN</li> <li> Intente actualizar la configuración de fecha y hora como se indica en [este blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>O BIEN</li> <li> Compruebe si c:\windows\temp tiene más de 65 000 archivos. Mueva los archivos obsoletos a otra ubicación o elimine los elementos de la carpeta Temp. <br>O BIEN</li> <li> Compruebe el estado de los certificados. <br> a. Abra "Administrar certificados de equipo" (en el Panel de control). <br> b. Expanda el nodo "Personal" y su nodo secundario "Certificados". <br> c.  Quite el certificado "Microsoft Azure Tools". <br> d. Vuelva a intentar el registro en el cliente de Azure Backup. <br> O BIEN </li> <li> Compruebe si hay alguna directiva de grupo implementada. </li></ol> |
| Insertar agentes en servidores protegidos | Error en la operación del agente debido a un error de comunicación con el servicio Coordinador de agentes DPM en \<nombreDelServidor> | **Si no funciona la acción recomendada mostrada en el producto**: <ol><li> Si está conectando un equipo desde un dominio que no sea de confianza, siga [estos](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) pasos. <br> O BIEN </li><li> Si está conectando un equipo desde un dominio de confianza, solucione el problema mediante los pasos descritos en [este blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>O BIEN</li><li> Pruebe a deshabilitar el antivirus como un paso para la solución de problemas. Si resuelve el problema, modifique la configuración del antivirus como se sugiere en [este artículo] (https://technet.microsoft.com/library/hh757911.aspx).</li></ol> |
| Insertar agentes en servidores protegidos | Las credenciales especificadas para el servidor no son válidas. | **Si no funciona la acción recomendada mostrada en el producto**: <br> Pruebe a instalar de forma manual el agente de protección en el servidor de producción, como se especifica en [este artículo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| El agente de Azure Backup no pudo conectar con el servicio Azure Backup (Id.: 100050) | El agente de Azure Backup no pudo conectar con el servicio Azure Backup. | **Si no funciona la acción recomendada mostrada en el producto**: <br>1. Desde el símbolo del sistema con privilegios elevados, ejecute el siguiente comando: psexec -i -s "c:\Archivos de programa\Internet Explorer\iexplore.exe". Se abrirá la ventana de Internet Explorer. <br/> 2. Vaya a Herramientas -> Opciones de Internet -> Conexiones -> Configuración de LAN. <br/> 3. Compruebe la configuración de proxy de la cuenta del sistema. Configure la dirección IP de proxy y el puerto. <br/> 4. Cierre Internet Explorer.|
| Error en la instalación del agente de Azure Backup | Error en la instalación de Microsoft Azure Recovery Services. Se revirtieron todos los cambios realizados por la instalación de Microsoft Azure Recovery Services en el sistema. (Id.: 4024) | Instalación manual de Azure Agent


## <a name="configuring-protection-group"></a>Configurar grupo de protección
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Configurar grupos de protección | DPM no pudo enumerar el componente de la aplicación en el equipo protegido (nombre del equipo protegido). | Haga clic en "Actualizar" en la pantalla de interfaz de usuario para configurar el grupo de protección en el nivel de origen de datos o el componente correspondiente. |
| Configurar grupos de protección | No se puede configurar la protección. | Si el servidor protegido es un servidor SQL Server, compruebe si se han proporcionado permisos de rol sysadmin para la cuenta del sistema (NTAuthority\System) en el equipo protegido, como se indica en [este artículo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configurar grupos de protección | No hay suficiente espacio disponible en el grupo de almacenamiento para este grupo de protección. | Los discos que se agregan al grupo de almacenamiento [no deben contener ninguna partición](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Elimine los volúmenes existentes en los discos y agréguelos al grupo de almacenamiento|
| Cambio de directiva |No se ha podido modificar la directiva de copia de seguridad. Error: no se pudo realizar la operación actual debido a un error de servicio interno [0x29834]. Vuelva a intentar la operación más tarde. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. |**Causa:**<br/>Este error se genera cuando está habilitada la configuración de seguridad, intenta reducir la duración de retención por debajo de los valores mínimos especificados anteriormente y se encuentra en una versión no admitida (debajo de MAB versión 2.0.9052 y Azure Backup Server update 1). <br/>**Acción recomendada:**<br/> En este caso, debe establecer el período de retención por encima el período de retención mínimo (siete días para un valor diario, cuatro semanas para uno semanal, tres semanas para mensual o un año para la copia anual) para continuar con las actualizaciones relacionadas con la directiva. Si lo desea, el enfoque preferido sería actualizar el agente de copia de seguridad y Azure Backup Server para aprovechar todas las actualizaciones de seguridad. |

## <a name="backup"></a>Backup
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Backup | La réplica es incoherente | Puede encontrar más detalles acerca de las causas de la incoherencia de la réplica y sugerencias relevantes [aquí](https://technet.microsoft.com/library/cc161593.aspx). <br> <ol><li> En el caso de una copia de seguridad de BMR o estado del sistema, compruebe si Windows Server Backup está instalado en el servidor protegido. </li><li> Compruebe si hay problemas relacionados con el espacio en el grupo de almacenamiento de DPM en el servidor DPM/MABS y asígnelos según sea necesario. </li><li> Compruebe el estado del servicio de instantáneas de volumen en el servidor protegido. Si se encuentra en estado deshabilitado, configúrelo para que se inicie manualmente e inicie el servicio en el servidor. A continuación, vuelva a la consola de DPM/MABS y comience la sincronización con el trabajo de comprobación de coherencia.</li></ol>|
| Backup | Se produjo un error inesperado mientras se ejecutaba el trabajo; el dispositivo no está listo. | **Si no funciona la acción recomendada mostrada en el producto**: <br> <ol><li>Configure el espacio de almacenamiento de instantáneas como ilimitado para los elementos del grupo de protección y ejecute la comprobación de coherencia. <br></li> O BIEN <li>Pruebe a eliminar el grupo de protección existente y cree varios nuevos, uno con cada elemento individual en él.</li></ol> |
| Backup | Si está haciendo una copia de seguridad solo del Estado del sistema, compruebe si hay suficiente espacio disponible en el equipo protegido para almacenarla. | <ol><li>Compruebe que WSB está instalado en el equipo protegido.</li><li>Compruebe que hay suficiente espacio en el equipo protegido para el estado del sistema: la forma más fácil de hacerlo es ir al equipo protegido, abrir WSB, hacer clic en las selecciones y seleccionar BMR. La interfaz de usuario indicará cuánto espacio es necesario. Abra WSB -> Copia de seguridad local -> Programación de copia de seguridad -> Seleccionar configuración de copia de seguridad -> Servidor completo (se muestra el tamaño). Use este tamaño para la comprobación.</li></ol>
| Backup | No se pudo crear el punto de recuperación en línea | Si el mensaje de error dice "Microsoft Azure Backup Agent no pudo crear una instantánea del volumen seleccionado", pruebe a aumentar el espacio en el volumen del punto de recuperación y réplica.
| Backup | No se pudo crear el punto de recuperación en línea | Si el mensaje de error dice "Windows Azure Backup Agent no se puede conectar con el servicio OBEngine", compruebe que el servicio OBEngine está en la lista de servicios en ejecución en el equipo. Si el servicio OBEngine no se está ejecutando, use el comando "net start OBEngine" para iniciarlo.
| Copia de seguridad | No se pudo crear el punto de recuperación en línea | Si aparece el mensaje de error "No se ha establecido la frase de contraseña de cifrado para este servidor. Configure la frase de contraseña de cifrado, intente configurarla. Si esto no funciona, <br> <ol><li>compruebe si la ubicación temporal existe. La ubicación mencionada en el Registro HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config debe existir con el nombre "ScratchLocation".</li><li> Si no existe la ubicación temporal, intente volver a registrarlo con la frase de contraseña de cifrado anterior. **Siempre que configure una frase de contraseña de cifrado, guárdela en una ubicación segura**.</li><ol>
| Backup | Error en la copia de seguridad de la BMR | Si la BMR es enorme, vuelva a intentarlo después de mover algunos archivos de la aplicación a la unidad del sistema operativo. |
| Backup | Error al acceder a carpetas o archivos compartidos | Pruebe a modificar la configuración del antivirus como se sugiere [aquí](https://technet.microsoft.com/library/hh757911.aspx).|
| Copia de seguridad | Hubo un error en los trabajos de creación de puntos de recuperación en línea para la máquina virtual de VMware. DPM detectó el error de VMware al intentar obtener información de ChangeTracking. ErrorCode - FileFaultFault (Id. 33621 ) |  1. Restablezca el ctk en VMWare, para las máquinas virtuales afectadas <br/> Compruebe que los discos independientes no están en VMWare <br/> Detenga la protección de las máquinas virtuales afectadas y vuelva a proteger con el botón Actualizar <br/> Ejecute CC para las máquinas virtuales afectadas|


## <a name="change-passphrase"></a>Cambiar la frase de contraseña
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Cambiar la frase de contraseña |El PIN de seguridad escrito no es correcto. Proporcione el PIN de seguridad correcto para completar esta operación. |**Causa:**<br/> Este error se genera cuando se escribe un PIN de seguridad no válido o caducado mientras se realiza una operación crítica (por ejemplo, cambiar la frase de contraseña). <br/>**Acción recomendada:**<br/> Para completar la operación, debe escribir un PIN de seguridad válido. Para obtener el PIN, inicie sesión en Azure Portal y navegue hasta el almacén de Recovery Services > Configuración > Propiedades > Generar PIN de seguridad. Use este código PIN para cambiar la frase de contraseña. |
| Cambiar la frase de contraseña |Error en la operación ID: 120002 |**Causa:**<br/>Este error se genera cuando está habilitada la configuración de seguridad, intenta cambiar la frase de contraseña y se encuentra en una versión no compatible.<br/>**Acción recomendada:**<br/> Para cambiar la frase de contraseña, primero debe actualizar el agente de copia de seguridad a la versión mínima 2.0.9052 y Azure Backup Server a la actualización mínima 1 y, después, escribir el PIN de seguridad válido. Para obtener el PIN, inicie sesión en Azure Portal y navegue hasta el almacén de Recovery Services > Configuración > Propiedades > Generar PIN de seguridad. Use este código PIN para cambiar la frase de contraseña. |


## <a name="configure-email-notifications"></a>Configuración de notificaciones de correo electrónico
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Intentando configurar las notificaciones por correo electrónico con una cuenta de Office 365. | obteniendo el identificador de error: 2013| **Causa:**<br/> Intentar usar la cuenta de Office 365 <br/> **Acción recomendada:**<br/> En primer lugar, hay que asegurarse de que la opción para permitir la retransmisión anónima en un conector de recepción para el servidor DPM, está configurada en Exchange. En este vínculo se indica cómo configurarla: http://technet.microsoft.com/en-us/library/bb232021.aspx <br/> Si no puede usar una retransmisión SMTP interna y necesita configurarla con un servidor Office 365, puede configurar IIS para que actúe como retransmisión. <br/> Debe configurar el servidor DPM para poder retransmitir el protocolo SMTP a Office 365 con IIS (consulte https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx) para configurar IIS de modo que retransmita a Office 365 <br/> Nota importante: En el paso 3->g->ii, procure usar el formato user@domain.com y NO dominio\usuario <br/> Indique a DPM que use el nombre del servidor local como servidor SMTP, el puerto 587, y, a continuación, el correo electrónico del usuario del que deben provenir los mensajes de correo electrónico. <br/> El nombre de usuario y la contraseña en la página de configuración SMTP de DPM deben ser una cuenta de dominio del dominio DPM. <br/> Nota: Al cambiar la dirección del servidor SMTP, realice el cambio a la nueva configuración, cierre el cuadro de configuración y, a continuación, vuelva a abrirlo para asegurarse de que refleja el nuevo valor.  No basta con cambiar y probar para adoptar la nueva configuración, de modo que se recomienda realizar esta prueba. <br/> En cualquier momento durante este proceso, puede borrar esta configuración cerrando la consola de DPM y modificando las siguientes claves del Registro:<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Elimine las claves SMTPPassword y SMTPUserName. <br/> Puede agregarlas de nuevo en la interfaz de usuario cuando la inicie otra vez.

