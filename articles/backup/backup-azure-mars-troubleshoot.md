---
title: "Solución de problemas de Azure Backup Agent (agente MARS) | Microsoft Docs"
description: "Solución de problemas de instalación y registro de Azure Backup Agent"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: da297177ca56822f7d50dbe06ec022640bf5d0cb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="troubleshooting-azure-backup-agent-configurationregistration-issues"></a>Solución de problemas de configuración y registro de Azure Backup Agent
## <a name="recommended-steps"></a>Pasos recomendados
Consulte las acciones recomendadas que se especifican a continuación para resolver los errores correspondientes que encuentre durante la configuración o el registro de Azure Backup Agent.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Error: Se han proporcionado credenciales de almacén no válidas. El archivo está dañado o no tiene asociadas las credenciales más recientes para el servicio de recuperación.
| Detalles del error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |
| **Error** </br> *Se han proporcionado credenciales de almacén no válidas. El archivo está dañado o no tiene asociadas las credenciales más recientes para el servicio de recuperación. (Id.: 34513)* | <ol><li> Las credenciales del cliente no son válidas (es decir, se descargaron más de 48 horas antes del momento del registro).<li>   Azure Backup Agent no puede descargar un archivo temporal en la carpeta temporal de Windows. <li>Las credenciales de almacén están en una ubicación de red. <li>TLS 1.0 está deshabilitado<li> Hay un servidor proxy configurado que bloquea la conexión <br> |  <ol><li>Descargue las credenciales de almacén nuevas<li>Vaya a Opciones de Internet > Seguridad > Internet, haga clic en Nivel personalizado > desplácese hasta que vea la sección de descarga de archivo y seleccione Habilitar.<li>Es posible que también tenga que agregar el sitio a los [sitios de confianza](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Cambie la configuración para usar el proxy y proporcione los detalles del proxy <li> Haga coincidir la fecha y hora (UTC) con su máquina<li>Vaya a C:/Windows/Temp para revisar si hay más de 60 000 o 65 000 archivos (con la extensión .tmp) y elimínelos.<li>Puede ejecutar el paquete SDP en el servidor para probar esto. Si recibe un error que indica que no se permiten las descargas de archivos, es casi seguro que hay un gran número de archivos en el directorio C:/Windows/Temp.<li>Asegúrese de que tiene instalado .NET Framework 4.6.2. <li>Si deshabilitó TLS 1.0 por cumplir con PCI, consulte este [vínculo para la solución de problemas](https://support.microsoft.com/help/4022913). <li>Si tiene instalado un antivirus en el servidor, excluya los archivos siguientes de un examen antivirus. <ol><li>CBengine.exe<li>CSC.exe (relacionado con .NET Framework. Hay un archivo CSC.exe por versión de .NET instalada en el servidor. Excluya todos los archivos CSC.exe vinculados a todas las versiones de .NET Framework en el servidor afectado). <li>Ubicación de caché o carpeta temporal. <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la ubicación de caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Error: El Agente de Microsoft Azure Recovery Services no se pudo conectar a Microsoft Azure Backup

| Detalles del error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |
| **Error** </br><ol><li>*El Agente de Microsoft Azure Recovery Services no se pudo conectar a Microsoft Azure Backup. (Id.: 100050) Compruebe la configuración de red y asegúrese de que tiene conexión a Internet*<li>*(407) Se requiere autenticación del proxy* |El proxy bloquea la conexión* |  <ol><li>Vaya a Opciones de Internet > Seguridad > Internet, haga clic en Nivel personalizado > desplácese hasta que vea la sección de descarga de archivo y seleccione Habilitar.<li>Es posible que también tenga que agregar el sitio a los [sitios de confianza](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Cambie la configuración para usar el proxy y proporcione los detalles del proxy <li>Si tiene instalado un antivirus en el servidor, excluya los archivos siguientes de un examen antivirus. <ol><li>CBengine.exe<li>(en lugar de dpmra.exe)<li>CSC.exe (relacionado con .NET Framework. Hay un archivo CSC.exe por versión de .NET instalada en el servidor. Excluya todos los archivos CSC.exe vinculados a todas las versiones de .NET Framework en el servidor afectado). <li>Ubicación de caché o carpeta temporal <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la ubicación de caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-failed-to-set-the-encryption-key-for-secure-backups"></a>Error: No se pudo establecer la clave de cifrado para proteger las copias de seguridad

| Detalles del error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |      
| **Error** </br>*No se pudo establecer la clave de cifrado para proteger las copias de seguridad. No se pudo realizar la operación actual debido a un error de servicio interno "Error de entrada no válida". Vuelva a intentar la operación más tarde. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft* |El servidor ya está registrado con otro almacén| Anule el registro del servidor del almacén y vuelva a registrarlo.

## <a name="error-the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Error: La activación no se completó correctamente. No se pudo realizar la operación actual debido a un error de servicio interno [0x1FC07]

| Detalles del error | Causas posibles | Acciones recomendadas |
| ---     | ---     | ---    |          
| **Error** </br><ol><li>*La activación no se completó correctamente. No se pudo realizar la operación actual debido a un error de servicio interno [0x1FC07]. Vuelva a intentar la operación más tarde. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft* <li>*Error 34506. La frase de contraseña de cifrado almacenada en este equipo no está configurada correctamente* | <li> La carpeta temporal se encuentra en un volumen sin espacio suficiente <li> La carpeta temporal se movió de manera incorrecta a otra ubicación <li> Falta el archivo OnlineBackup.KEK | <li>Mueva la carpeta temporal o la ubicación de caché a un volumen con espacio libre equivalente al 5 % o 10 % del tamaño total de los datos de copia de seguridad. Consulte los pasos que se mencionan en [este artículo](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup) para mover correctamente la ubicación de caché.<li> Asegúrese de que exista el archivo OnlineBackup.KEK <br>*La ubicación predeterminada de la carpeta temporal o la ruta de acceso a la ubicación de caché es C:\Archivos de programa\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

## <a name="next-steps"></a>Pasos siguientes
* Más detalles sobre cómo [crear copias de seguridad de Windows Server con Azure Backup Agent](tutorial-backup-windows-server-to-azure.md).
* Si necesita restaurar una copia de seguridad, use este artículo: [Restaurar archivos en una máquina de Windows Server o del Cliente de Windows](backup-azure-restore-windows-server.md).
