---
title: "Solución de problemas de Azure Files Backup"
description: "Este artículo contiene información para solución de problemas que se producen al proteger archivos (recursos compartidos de archivos) de Azure en Azure."
services: backup
ms.service: backup
keywords: No agregue ni edite palabras clave sin consultar a su responsable de SEO Champ.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: ce4e53b3fa839bfc2da6bedecca0b4f730a6adbe
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="troubleshoot-problems-backing-up-azure-files"></a>Solución de problemas de copia de seguridad de archivos de Azure

Puede solucionar los problemas y errores encontrados durante el uso de Azure Files Backup con la información indicada en las siguientes tablas.

## <a name="preview-boundaries"></a>Límites de versión preliminar
Azure Files Backup se encuentra en versión preliminar. No se admiten los siguientes escenarios de copia de seguridad para los recursos compartidos de archivos de Azure:
- Protección de recursos compartidos de archivos en cuentas de Storage con replicación de [almacenamiento con redundancia de zona](../storage/common/storage-redundancy.md#zone-redundant-storage) (ZRS) o [almacenamiento con redundancia geográfica con acceso de lectura](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS).
- Protección de recursos compartidos de archivos en cuentas de Storage que tienen habilitadas redes virtuales.
- Realización de una copia de seguridad de archivos de Azure con PowerShell o la CLI.

## <a name="configuring-backup"></a>Configuración de la copia de seguridad
La tabla siguiente es para configurar la copia de seguridad.

| Configuración de la copia de seguridad | Sugerencias de solución alternativa o resolución |
| ------------------ | ----------------------------- |
| No se pudo encontrar la cuenta de almacenamiento para configurar la copia de seguridad para los recursos compartidos de archivos de Azure | <ul><li>Espere a que la detección finalice. <li>Compruebe si algún recurso compartido de archivos de la cuenta de almacenamiento ya está protegido con otro almacén de Recovery Services. **Nota**: Todos los recursos compartidos de archivos de una cuenta de almacenamiento solo se pueden proteger en un almacén de Recovery Services. <li>Asegúrese de que el recurso compartido de archivos no esté presente en ninguna de las cuentas de almacenamiento no admitidas.|
| Error en la detección de estados del portal de las cuentas de almacenamiento. | Si la suscripción es de un asociado (habilitada para CSP), haga caso omiso del error. Si la suscripción no está habilitada para CSP y las cuentas de almacenamiento no se pueden detectar, póngase en contacto con el servicio de soporte técnico.|
| Error de registro o validación de la cuenta de almacenamiento seleccionada.| Reintente la operación. Si el problema persiste, póngase en contacto con el servicio de soporte técnico.|
| No se pudieron mostrar ni encontrar los recursos compartidos de archivos en la cuenta de almacenamiento seleccionada. | <ul><li> Asegúrese de que la cuenta de almacenamiento existe en el grupo de recursos (y que no se ha eliminado ni movido después de la última validación o registro en el almacén).<li>Asegúrese de que el recurso compartido de archivos que busca proteger no se ha eliminado. <li>Asegúrese de que la cuenta de almacenamiento es una cuenta de almacenamiento admitida para la copia de seguridad de recursos compartidos de archivos.<li>Compruebe si el recurso compartido de archivos ya está protegido en el mismo almacén de Recovery Services.|
| Error en la configuración de copia de seguridad del recurso compartido de archivos (o en la configuración de la directiva de protección). | <ul><li>Vuelva a intentar la operación para ver si el problema persiste. <li> Asegúrese de que el recurso compartido de archivos que quiere proteger no se ha eliminado. <li> Si está intentando proteger varios recursos compartidos de archivos a la vez, y algunos de ellos generan error, reintente la configuración de la copia de seguridad en los recursos compartidos de archivos que han dado error. |
| No se puede eliminar el almacén de Recovery Services después de desproteger un recurso compartido de archivos. | En Azure Portal, abra **Infraestructura de Backup** > **Cuentas de almacenamiento** y haga clic en **Anular el registro** para quitar la cuenta de almacenamiento del almacén de Recovery Services.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Mensajes de error de copia de seguridad o restauración de trabajos

| mensajes de error | Sugerencias de solución alternativa o resolución |
| -------------- | ----------------------------- |
| No se pudo realizar la operación porque no se encuentra el recurso compartido de archivos. | Asegúrese de que el recurso compartido de archivos que busca proteger no se ha eliminado.|
| La cuenta de almacenamiento no se encuentra o no se admite. | <ul><li>Asegúrese de que la cuenta de almacenamiento existe en el grupo de recursos y que no se ha eliminado o quitado del grupo de recursos tras la validación final. <li> Asegúrese de que la cuenta de almacenamiento es una cuenta de almacenamiento admitida para la copia de seguridad de recursos compartidos de archivos.|
| Ha alcanzado el límite máximo de instantáneas para este recurso compartido de archivos. Podrá tomar más cuando expiren las más antiguas. | <ul><li> Este error puede producirse al crear varias copias de seguridad a petición para un archivo. <li> Hay un límite de 200 instantáneas por recurso compartido de archivos, incluidas las realizadas por Azure Backup. Las copias de seguridad programadas antiguas (o instantáneas) se borran automáticamente. Las copias de seguridad a petición (o instantáneas) deben eliminarse si se alcanza el límite máximo.<li> Elimine las copias de seguridad a petición (instantáneas de recursos compartidos de archivos de Azure) desde el portal de Azure Files. **Nota**: Si elimina las instantáneas creadas por Azure Backup, perderá los puntos de recuperación. |
| Error de restauración o copia de seguridad de recursos compartidos de archivos debido a la limitación del servicio de almacenamiento. Esto puede ser debido a que el servicio de almacenamiento está ocupado procesando otras solicitudes para la cuenta de almacenamiento dada.| Vuelva a intentar la operación más tarde. |
| Error de restauración con recurso compartido de archivos no encontrado. | <ul><li>Asegúrese de que existe la cuenta de almacenamiento seleccionada y que no se ha eliminado el recurso compartido de archivos de destino. <li> Asegúrese de que la cuenta de almacenamiento es una cuenta de almacenamiento admitida para la copia de seguridad de recursos compartidos de archivos. |
| Azure Backup no se admite actualmente para Azure Files en cuentas de almacenamiento con redes virtuales habilitadas. | Deshabilite las redes virtuales en la cuenta de almacenamiento para garantizar operaciones correctas de copia de seguridad y restauración. |
| Error de los trabajos de copia de seguridad o restauración debido al estado Bloqueado de la cuenta de almacenamiento. | Quite el bloqueo de la cuenta de almacenamiento o use eliminar bloqueo en lugar de leer bloqueo y reintente la operación. |
| No se pudo llevar a cabo la recuperación porque el número de archivos con errores es superior al umbral permitido. | <ul><li> Los motivos de los errores de recuperación se muestran en un archivo (ruta de acceso proporcionada en los detalles del trabajo). Solucione los errores y vuelva a intentar la operación de restauración solo de los archivos con error. <li> Causas comunes de errores de restauración de archivos: <br/> -Asegúrese de que los archivos con error no están actualmente en uso. <br/> -Existe un directorio con el mismo nombre que el archivo de error en el directorio primario. |
| Error de recuperación. No se pudo recuperar ningún archivo. | <ul><li> Los motivos de los errores de recuperación se muestran en un archivo (ruta de acceso proporcionada en los detalles del trabajo). Solucione los errores y vuelva a intentar la operación de restauración solo de los archivos con error. <li> Causas comunes de los errores de restauración de archivos: <br/> -Asegúrese de que los archivos con error no están actualmente en uso. <br/> -Existe un directorio con el mismo nombre que el archivo de error en el directorio primario. |
| Error en la restauración porque uno de los archivos del origen no existe. | <ul><li> Los elementos seleccionados no están presentes en los datos del punto de recuperación. Para recuperar los archivos, proporcione la lista de archivos correcta. <li> La instantánea del recurso compartido de archivos que corresponde al punto de recuperación se elimina manualmente. Seleccione un punto de recuperación diferente y vuelva a intentar la operación de restauración. |
| Ya hay un trabajo de Recovery en curso para el mismo destino. | <ul><li>La copia de seguridad de recursos compartidos de archivos no admite recuperación en paralelo al mismo recurso compartido de archivos de destino. <li>Espere a que la recuperación existente finalice e inténtelo de nuevo. Si no encuentra un trabajo de recuperación en el almacén de Recovery Services, compruebe otros almacenes de Recovery Services de la misma suscripción. |
| No se puede realizar la operación de restauración porque el recurso compartido de archivos de destino está lleno. | Aumente la cuota de tamaño del recurso compartido de archivos de destino para albergar los datos de restauración y vuelva a intentar la operación. |
| Uno o varios archivos no se pudieron recuperar correctamente. Para más información, consulte la lista de archivos que han dado error en la ruta de acceso anterior. | <ul> <li> Las causas de los errores de recuperación se muestran en el archivo (la ruta de acceso se proporciona en los detalles del trabajo). Solucione los errores y vuelva a intentar la operación de restauración solo de los archivos con error. <li> Las causas comunes de errores de restauración de archivos son: <br/> -Asegúrese de que los archivos con error no están actualmente en uso. <br/> -Existe un directorio con el mismo nombre que el archivo de error en el directorio principal. |

## <a name="see-also"></a>Otras referencias
Para información adicional acerca de la copia de seguridad de los recursos compartidos de archivos de Azure, consulte:
- [Copia de seguridad de recursos compartidos de archivos de Azure](backup-azure-files.md)
- [Preguntas frecuentes acerca de la copia de seguridad de recursos compartidos de archivos de Azure](backup-azure-files-faq.md)