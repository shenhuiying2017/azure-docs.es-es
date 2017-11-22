---
title: "Configuración de informes de Azure Backup"
description: "En este artículo se habla sobre la configuración de informes de Power BI para Azure Backup con el almacén de Recovery Services."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40433df5ebe90aec3a9294f2c5a6083c4567b161
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="configure-azure-backup-reports"></a>Configuración de informes de Azure Backup
En este artículo se habla sobre los pasos para configurar informes de Azure Backup con el almacén de Recovery Services y para acceder a estos informes mediante Power BI. Después de realizar estos pasos, puede ir directamente a Power BI para ver todos los informes, personalizarlos y crearlos. 

## <a name="supported-scenarios"></a>Escenarios admitidos
1. Los informes de Azure Backup se admiten para copias de seguridad de máquinas virtuales de Azure y copias de seguridad de archivos y carpetas con el Agente de Azure Recovery Services.
2. Los informes de Azure SQL, DPM y Azure Backup Server no se admiten en este momento.
3. Puede ver los informes de los almacenes y las suscripciones, en caso de que la misma cuenta de Storage esté configurada para cada uno de los almacenes. La cuenta de Storage seleccionada debe estar en la misma región que el almacén de Recovery Services.
4. La frecuencia de actualización programada para los informes es de 24 horas en Power BI. También puede realizar una actualización ad hoc de los informes en Power BI, en cuyo caso, se usan los últimos datos de la cuenta de Storage del cliente para informes de representación. 
5. Los informes de Azure Backup no se admiten en las nubes nacionales.

## <a name="prerequisites"></a>Requisitos previos
1. Cree una [cuenta de Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account) para informes. Esta cuenta de Storage se usa para almacenar datos relacionados de informes.
2. [Cree una cuenta de Power BI](https://powerbi.microsoft.com/landing/signin/) para ver, personalizar y crear sus propios informes mediante el portal de Power BI.
3. Registre el proveedor de recursos **Microsoft.insights** si no se ha registrado ya, con la suscripción de la cuenta de Storage y también con la suscripción del almacén de Recovery Services para habilitar el flujo de datos de informes a la cuenta de Storage. Para hacer lo mismo, debe ir a Azure Portal > Suscripción > Proveedores de recursos y busque este proveedor para registrarlo. 

## <a name="configure-storage-account-for-reports"></a>Configuración de la cuenta de Storage para los informes
Use los pasos siguientes para configurar la cuenta de Storage para el almacén de Recovery Services mediante Azure Portal. Se trata de una única configuración y, una vez que se configura la cuenta de Storage, puede ir a Power BI directamente para ver el paquete de contenido y aprovechar los informes.
1. Si ya tiene abierto un almacén de Recovery Services, vaya al siguiente paso. Si no tiene abierto un almacén de Recovery Services pero está en Azure Portal, en el menú del centro, haga clic en **Examinar**.

   * En la lista de recursos, escriba **Recovery Services**.
   * Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de Recovery Services**cuando lo vea.

      ![Creación del almacén de Recovery Services, paso 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Aparece la lista de almacenes de Recovery Services. En la lista de almacenes de Recovery Services, seleccione un almacén.

     Se abre el panel del almacén seleccionado.
2. En la lista de elementos que aparece en el almacén, haga clic en **Informes de copia de seguridad** en la sección Supervisión e informes para configurar la cuenta de Storage para los informes.

      ![Selección del elemento de menú Informes de copia de seguridad, paso 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. En la hoja Informes de Backup, haga clic en el vínculo **Configuración de diagnóstico**. Se abrirá la interfaz de usuario de Configuración de diagnóstico, que se utiliza para insertar datos en la cuenta de almacenamiento del cliente.

      ![Habilitación de diagnósticos, paso 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. A continuación, haga clic en el vínculo **Activar diagnósticos**. Se abrirá la interfaz de usuario para configurar la cuenta de almacenamiento. 

      ![Activación de diagnósticos, paso 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Escriba el nombre de la configuración en el campo **Nombre** y seleccione la casilla **Archivar en una cuenta de almacenamiento** para que los datos de los informes puedan empezar a fluir en la cuenta de almacenamiento.

      ![Habilitación de diagnósticos, paso 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Haga clic en el selector de Cuenta de almacenamiento y seleccione la cuenta de almacenamiento y la suscripción correspondientes en la lista para almacenar los datos de informes. A continuación, haga clic en **Aceptar**.

      ![Selección de cuenta de Storage, paso 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Seleccione la casilla **AzureBackupReport** bajo la sección Registro y mueva el control deslizante para seleccionar el período de retención para estos datos de informes. Los datos de informes de la cuenta de Storage se mantienen durante el período seleccionado con este control deslizante.

      ![Guardar la cuenta de almacenamiento, paso 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Revise todos los cambios y haga clic en el botón **Guardar** situado en la parte superior, como se muestra en la ilustración anterior. Esta acción garantiza que todos los cambios se guardan, y la cuenta de Storage está ahora configurada para almacenar datos de informes.

9. La tabla Configuración de diagnóstico ahora debe mostrar la nueva configuración habilitada para el almacén. Si no aparece, actualice la tabla para ver la configuración actualizada.

      ![Ver la configuración de diagnóstico, paso 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Cuando haya configurado los informes al guardar la cuenta de almacenamiento, debe **esperar 24 horas** para que finalice la inserción de datos iniciales. Debe importar el paquete de contenido de Azure Backup en Power BI solo después de ese tiempo. Consulte la [sección de preguntas frecuentes](#frequently-asked-questions) para más información. 
>
>

## <a name="view-reports-in-power-bi"></a>Visualización de informes en Power BI 
Después de configurar la cuenta de Storage para informes con el almacén de Recovery Services, los datos de informes tardarán unas 24 horas en empezar a fluir. Transcurridas 24 horas desde la configuración de la cuenta de Storage, use los pasos siguientes para ver informes en Power BI:
1. [Inicie sesión](https://powerbi.microsoft.com/landing/signin/) en Power BI.
2. Haga clic en **Obtener datos** y haga clic en Get en **Servicios** en la Biblioteca de paquetes de contenido. Utilice los pasos mencionados en la [documentación de Power BI para acceder al paquete de contenido](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importación del paquete de contenido](./media/backup-azure-configure-reports/content-pack-import.png)
3. Escriba **Azure Backup** en la barra de búsqueda y haga clic en **Obtenerla ahora**.

      ![Obtención del paquete de contenido](./media/backup-azure-configure-reports/content-pack-get.png)
4. Escriba el nombre de la cuenta de Storage configurada en el paso 5 anterior y haga clic en el botón **Siguiente**.

    ![Escribir el nombre de la cuenta de Storage](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Escriba la clave de cuenta de almacenamiento de esta cuenta de Storage. Puede [ver y copiar las claves de acceso de almacenamiento](../storage/common/storage-create-storage-account.md#manage-your-storage-account); para ello, vaya a la cuenta de Storage en Azure Portal. 

     ![Escribir la cuenta de Storage](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Haga clic en el botón **Iniciar sesión**. Después de iniciar sesión correctamente, obtiene la notificación **Importando datos**.

    ![Importando paquete de contenido](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Más tarde, obtiene la notificación **Correcto** después de que se ha completado la importación. La importación del paquete de contenido podría tardar un poco más, en caso de que haya muchos datos en la cuenta de Storage.
    
    ![Importación correcta del paquete de contenido](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Una vez que los datos se importan correctamente, el paquete de contenido de **Azure Backup** se muestra en **Aplicaciones** en el panel de navegación. En la lista ahora aparecen el panel, los informes y el conjunto de datos de Azure Backup con una estrella amarilla que identifica los informes recién importados. 

     ![Paquete de contenido de Azure Backup](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Haga clic en **Azure Backup** en Paneles, donde se muestra un conjunto de informes de claves anclados.

      ![Panel de Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Para ver el conjunto completo de informes, haga clic en cualquier informe en el panel.

      ![Estado de la tarea de Azure Backup](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Haga clic en cada pestaña de los informes para ver los informes de dicha área.

      ![Pestañas de informes de Azure Backup](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
1. **¿Cómo se comprueba si los datos de informes han empezado a fluir en la cuenta de Storage?**
    
    Puede ir a la cuenta de Storage configurada y seleccione Containers. Si el contenedor tiene una entrada para insights-logs-azurebackupreport, esto indica que los datos de informes han empezado a fluir.

2. **¿Cuál es la frecuencia de inserción de datos en la cuenta de Storage y en el paquete de contenido de Azure Backup en Power BI?**

   Para los usuarios del día 0, la inserción de datos en la cuenta de Storage tardaría 24 horas aproximadamente. Cuando se complete esta inserción inicial, los datos se actualizan con la frecuencia siguiente, según se muestra en esta ilustración. 
      * Los datos relacionados con **trabajos, alertas, elementos de copia de seguridad, almacenes, servidores protegidos y directivas** se insertan en la cuenta de Storage del cliente cuando esta se registra.
      * Los datos relacionados con **Storage** se insertan en la cuenta de Storage del cliente cada 24 horas.
   
    ![Frecuencia de inserción de datos de informes de Azure Backup](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Power BI tiene una [actualización programada una vez al día](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Puede realizar una actualización manual de los datos en Power BI para el paquete de contenido.

3. **¿Cuánto tiempo se pueden conservar los informes?** 

   Al configurar la cuenta de almacenamiento, puede seleccionar el período de retención de datos de informes en la cuenta de Storage (con el paso 6 de la sección Configuración de la cuenta de Storage para los informes anterior). Además de eso, también puede [analizar informes en Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) y guardarlos durante un período de retención más largo, según sus necesidades. 

4. **¿Aparecerán todos los datos en los informes después de configurar la cuenta de Storage?**

   Todos los datos generados después de **"configurar la cuenta de Storage"** se insertarán en la cuenta de Storage y estarán disponibles en los informes. No obstante, **los trabajos en curso no se insertan** para los informes. Cuando el trabajo se completa o si se produce algún error, este se envía a los informes.

5. **Si ya se ha configurado la cuenta de Storage para ver los informes, ¿se puede cambiar la configuración para usar otra cuenta de Storage?** 

   Sí, puede cambiar la configuración para que se remita a una cuenta de Storage distinta. Debe usar la cuenta de Storage que acaba de configurar al conectarse al paquete de contenido de Azure Backup. Además, después de configurar una cuenta de Storage distinta, los nuevos datos fluirán a esta cuenta de Storage. No obstante, los datos más antiguos (antes de cambiar la configuración) permanecerían aún en la cuenta de Storage anterior.

6. **¿Se pueden ver los informes en los almacenes y las suscripciones?** 

   Sí, puede configurar la misma cuenta de Storage en varios almacenes para ver los informes entre ellos. Además, puede configurar la misma cuenta de Storage para almacenes a través de las suscripciones. Después, puede usar esta cuenta de Storage al conectarse al contenido de Azure Backup en Power BI para ver los informes. No obstante, la cuenta de Storage seleccionada debe estar en la misma región que el almacén de Recovery Services.
   
## <a name="troubleshooting-errors"></a>Solución de errores
| Detalles del error | Resolución |
| --- | --- |
| Después de configurar la cuenta de Storage para los informes de Backup, **Cuenta de Storage** sigue mostrando **No configurado**. | Si ha configurado correctamente la cuenta de Storage, los datos de los informes se transmitirán a pesar de este problema. Para resolver este problema, vaya a Azure Portal > Más servicios > Configuración de diagnóstico > Almacén RS > Editar configuración. Elimine el valor configurado previamente y cree una nueva configuración en la misma hoja. Esta vez establezca el campo **Nombre** en **servicio**. Se debería mostrar la cuenta de Storage configurada. |
|Después de importar el paquete de contenido de Azure Backup en Power BI, aparece el error **404-no se encuentra el contenedor**. | Tal como se sugiere en este documento, debe esperar 24 horas después de configurar los informes en el almacén de Recovery Services para verlos correctamente en Power BI. Si intenta obtener los informes antes de 24 horas obtendrá este error, ya que aún no hay datos completos para mostrar informes válidos. |

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha configurado la cuenta de Storage y ha importado el paquete de contenido de Azure Backup, el siguiente paso es personalizar estos informes y usar el modelo de datos de informes para crear informes. Consulte los siguientes artículos para obtener más información.

* [Uso del modelo de datos de informes de Azure Backup](backup-azure-reports-data-model.md)
* [Filtrado de informes en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Creación de informes en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

