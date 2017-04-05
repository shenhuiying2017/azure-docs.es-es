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
ms.date: 3/24/2017
ms.author: pullabhk;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a42488d618c58b36fa8105c1b22fd32ca615d1b1
ms.lasthandoff: 03/27/2017


---

# <a name="troubleshoot-azure-backup-server"></a>Solución de problemas de Azure Backup Server

Puede solucionar los errores detectados al usar Azure Backup Server con la información incluida en la tabla siguiente.

>
>

## <a name="registration-and-agent-related-issues"></a>Problemas relacionados con el registro y el agente
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Registrar en un almacén | Se han proporcionado credenciales de almacén no válidas. El archivo está dañado o no tiene asociadas las credenciales más recientes para el servicio de recuperación. | Para solucionar este error:  <ol><li> Descargue el archivo de credenciales más reciente del almacén e inténtelo de nuevo. <br>O BIEN</li> <li> Si la acción anterior no funciona, intente descargar las credenciales en un directorio local diferente o cree un nuevo almacén. <br>O BIEN</li> <li> Intente actualizar la configuración de fecha y hora como se indica en [este blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>O BIEN</li> <li> Compruebe si c:\windows\temp tiene más de 65 000 archivos. Mueva los archivos obsoletos a otra ubicación o elimine los elementos de la carpeta Temp. <br>O BIEN</li> <li> Compruebe el estado de los certificados. <br> a. Abra "Administrar certificados de equipo" (en el Panel de control). <br> b. Expanda el nodo "Personal" y su nodo secundario "Certificados". <br> c.  Quite el certificado "Microsoft Azure Tools". <br> d. Vuelva a intentar el registro en el cliente de Azure Backup. <br> O BIEN </li> <li> Compruebe si hay alguna directiva de grupo implementada. </li></ol> |
| Insertar agentes en servidores protegidos | Error en la operación del agente debido a un error de comunicación con el servicio Coordinador de agentes DPM en \<nombreDelServidor> | **Si no funciona la acción recomendada mostrada en el producto**: <ol><li> Si está conectando un equipo desde un dominio que no sea de confianza, siga [estos](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) pasos. <br> O BIEN </li><li> Si está conectando un equipo desde un dominio de confianza, solucione el problema mediante los pasos descritos en [este blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>O BIEN</li><li> Pruebe a deshabilitar el antivirus como un paso para la solución de problemas. Si resuelve el problema, modifique la configuración del antivirus como se sugiere en [este artículo] (https://technet.microsoft.com/library/hh757911.aspx).</li></ol> |
| Insertar agentes en servidores protegidos | Las credenciales especificadas para el servidor no son válidas. | **Si no funciona la acción recomendada mostrada en el producto**: <br> Pruebe a instalar de forma manual el agente de protección en el servidor de producción, como se especifica en [este artículo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|


## <a name="configuring-protection-group"></a>Configurar grupo de protección
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Configurar grupos de protección | DPM no pudo enumerar el componente de la aplicación en el equipo protegido (nombre del equipo protegido). | Haga clic en "Actualizar" en la pantalla de interfaz de usuario para configurar el grupo de protección en el nivel de origen de datos o el componente correspondiente. |
| Configurar grupos de protección | No se puede configurar la protección. | Si el servidor protegido es un servidor SQL Server, compruebe si se han proporcionado permisos de rol sysadmin para la cuenta del sistema (NTAuthority\System) en el equipo protegido, como se indica en [este artículo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configurar grupos de protección | No hay suficiente espacio disponible en el grupo de almacenamiento para este grupo de protección. | Los discos que se agregan al grupo de almacenamiento [no deben contener ninguna partición](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Elimine los volúmenes existentes en los discos y agréguelos al grupo de almacenamiento|

## <a name="backup"></a>Backup
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Backup | La réplica es incoherente | Puede encontrar más detalles acerca de las causas de la incoherencia de la réplica y sugerencias relevantes [aquí](https://technet.microsoft.com/library/cc161593.aspx). <br> <ol><li> En el caso de una copia de seguridad de BMR o estado del sistema, compruebe si Windows Server Backup está instalado en el servidor protegido. </li><li> Compruebe si hay problemas relacionados con el espacio en el grupo de almacenamiento de DPM en el servidor DPM/MABS y asígnelos según sea necesario. </li><li> Compruebe el estado del servicio de instantáneas de volumen en el servidor protegido. Si se encuentra en estado deshabilitado, configúrelo para que se inicie manualmente e inicie el servicio en el servidor. A continuación, vuelva a la consola de DPM/MABS y comience la sincronización con el trabajo de comprobación de coherencia.</li></ol>|
| Backup | Se produjo un error inesperado mientras se ejecutaba el trabajo; el dispositivo no está listo. | **Si no funciona la acción recomendada mostrada en el producto**: <br> <ol><li>Configure el espacio de almacenamiento de instantáneas como ilimitado para los elementos del grupo de protección y ejecute la comprobación de coherencia. <br></li> O BIEN <li>Pruebe a eliminar el grupo de protección existente y cree varios nuevos, uno con cada elemento individual en él.</li></ol> |
| Backup | Si está haciendo una copia de seguridad solo del Estado del sistema, compruebe si hay suficiente espacio disponible en el equipo protegido para almacenarla. | <ol><li>Compruebe que WSB está instalado en el equipo protegido.</li><li>Compruebe que hay suficiente espacio en el equipo protegido para el estado del sistema: la forma más fácil de hacerlo es ir al equipo protegido, abrir WSB, hacer clic en las selecciones y seleccionar BMR. La interfaz de usuario indicará cuánto espacio es necesario. Abra WSB -> Copia de seguridad local -> Programación de copia de seguridad -> Seleccionar configuración de copia de seguridad -> Servidor completo (se muestra el tamaño). Use este tamaño para la comprobación.</li></ol>
| Backup | No se pudo crear el punto de recuperación en línea | Si el mensaje de error dice "Microsoft Azure Backup Agent no pudo crear una instantánea del volumen seleccionado", pruebe a aumentar el espacio en el volumen del punto de recuperación y réplica.
| Backup | No se pudo crear el punto de recuperación en línea | Si aparece el mensaje de error "No se ha establecido la frase de contraseña de cifrado para este servidor. Configure la frase de contraseña de cifrado, intente configurarla. Si esto no funciona, <br> <ol><li>compruebe si la ubicación temporal existe. La ubicación mencionada en el Registro HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config debe existir con el nombre "ScratchLocation".</li><li> Si no existe la ubicación temporal, intente volver a registrarlo con la frase de contraseña de cifrado anterior. **Siempre que configure una frase de contraseña de cifrado, guárdela en una ubicación segura**.</li><ol>
| Backup | Error en la copia de seguridad de la BMR | Si la BMR es enorme, vuelva a intentarlo después de mover algunos archivos de la aplicación a la unidad del sistema operativo. |
| Backup | Error al acceder a carpetas o archivos compartidos | Pruebe a modificar la configuración del antivirus como se sugiere [aquí](https://technet.microsoft.com/library/hh757911.aspx).|

