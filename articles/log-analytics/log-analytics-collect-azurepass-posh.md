---
title: "Collect Azure PaaS resource metrics with Log Analytics (Recopilación de métricas de recursos de PaaS de Azure con Log Analytics) | Microsoft Docs"
description: "Aprenda a habilitar la recopilación de métricas de recursos de PaaS de Azure con PowerShell para la retención y el análisis en Log Analytics."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 83491c4902dabc6bab1e222551298cfaffbaecf4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Configuración de la recopilación de métricas de recursos de PaaS de Azure con Log Analytics

Los recursos de la plataforma como servicio (PaaS) de Azure, así como Azure SQL y Web Sites (Web Apps), pueden emitir a Log Analytics los datos de las métricas de rendimiento de forma nativa. Este script permite a los usuarios habilitar el registro de métricas para los recursos de PaaS ya implementados en un grupo de recursos específico o a través de una suscripción completa. 

En la actualidad, no hay ninguna manera de habilitar el registro de métricas para los recursos PaaS a través de Azure Portal. Por lo tanto, debe usar un script de PowerShell. Esta funcionalidad del registro nativo de métricas, junto con la supervisión de Log Analytics, le permiten supervisar los recursos de Azure a escala. 

## <a name="prerequisites"></a>requisitos previos
Compruebe que dispone de los siguientes módulos de Azure Resource Manager instalados en el equipo antes de continuar:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Se recomienda que todos los módulos de Azure Resource Manager tengan la misma versión para garantizar su compatibilidad al ejecutar los comandos de Azure Resource Manager de PowerShell.
>
Para instalar en su equipo la versión más reciente de los módulos de Azure Resource Manager, consulte [Instalar y configurar Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Habilitar Azure Diagnostics  
Configurar Azure Diagnostics para los recursos de PaaS se lleva a cabo mediante la ejecución del script **Enable-AzureRMDiagnostics.ps1**, que está disponible en la [Galería de PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  El script admite los siguientes escenarios:
  
* Especificar un recurso relacionado con uno o varios grupos de recursos en una suscripción  
* Especificar un recurso relacionado con un grupo específico de recursos en una suscripción  
* Volver a configurar un recurso para reenviarlo a otra área de trabajo

Solo se admiten los recursos que admiten recopilar métricas con Azure Diagnostics y las envían directamente a Log Analytics.  Para obtener una lista detallada, revise [Recopilación de registros y métricas de Azure para servicios de Log Analytics](log-analytics-azure-storage.md) 

Siga estos pasos para descargar y ejecutar el script.

1.  En la pantalla de inicio de Windows, escriba **PowerShell** y haga clic con el botón derecho en PowerShell desde los resultados de búsqueda.  Seleccione en el menú **Ejecutar como administrador**.   
2. Guarde el archivo de script **Enable-AzureRMDiagnostics.ps1** en local al ejecutar el siguiente comando y proporcione una ruta de acceso para almacenarlo.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Ejecute `Login-AzureRmAccount` para crear una conexión con Azure.   
4. Ejecute el siguiente script `.\Enable-AzureRmDiagnostics.ps1` sin ningún parámetro para habilitar la recopilación de datos de un recurso específico en su suscripción o con el parámetro `-ResourceGroup <myResourceGroup>` para especificar un recurso en un grupo de recursos específico.   
5. Seleccione la suscripción adecuada en la lista si tiene más de una especificando el valor correcto.<br><br> ![Seleccione la suscripción que devuelve el script](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Se lo contrario, el script selecciona automáticamente la única suscripción disponible.
6. A continuación, el script devuelve una lista de áreas de trabajo de Log Analytics registradas en la suscripción.  De la lista, seleccione la adecuada.<br><br> ![Seleccione el área de trabajo que devuelve el script](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Seleccione el recurso de Azure desde el que le gustaría habilitar la recopilación. Por ejemplo, si escribe 5, puede habilitar la recopilación de datos para las bases de datos de SQL Azure.<br><br> ![Seleccionar el tipo de recurso que devuelve el script](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Solo puede seleccionar los recursos que admiten recopilar métricas con Azure Diagnostics y las envían directamente a Log Analytics.  El script mostrará el valor de **True** en la columna **Métricas** para la lista de recursos que detecta en su suscripción o grupo de recursos especificado.    
8. Se le pedirá que confirme la selección.  Escriba **Y** para habilitar el registro de métricas para todos los recursos seleccionados para el ámbito definido, que en nuestro ejemplo son todas las bases de datos SQL de la suscripción.  

El script se ejecutará en cada recurso que coincida con los criterios seleccionados y habilitará la recopilación de métricas para ellos. Cuando finalice, verá un mensaje que indica que se ha completado la configuración.  

Poco después de la finalización, empezará a ver los datos desde el recurso de PaaS de Azure en el repositorio de Log Analytics.  Se crea un registro con tipo `AzureMetrics` las soluciones de administración de [Azure SQL Analytics](log-analytics-azure-sql.md) y [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md) admiten el análisis de estos registros.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Actualizar un recurso para enviar datos a otra área de trabajo
Si tiene un recurso que ya está enviando datos a un área de trabajo de Log Analytics y más adelante decide volver a configurarlo para hacer referencia a otra área de trabajo, puede ejecutar el script con el parámetro `-Update`.  

**Ejemplo:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Se le pedirá que responda a la misma información que cuando se ejecutó el script para realizar la configuración inicial.  

## <a name="next-steps"></a>pasos siguientes

* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de soluciones y orígenes de datos. 

* Use [Campos personalizados](log-analytics-custom-fields.md) para redistribuir los registros de eventos en campos individuales.

* Revise [Crear un panel personalizado para usar en Log Analytics](log-analytics-dashboards.md) para entender cómo visualizar las búsquedas de registro de maneras significativas para la organización.