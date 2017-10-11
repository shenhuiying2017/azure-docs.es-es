---
title: "Recopilación de datos de Log Analytics con un runbook en Azure Automation | Microsoft Docs"
description: "Tutorial paso a paso que le guía en la creación de un runbook en Azure Automation para recopilar datos en el repositorio de OMS y analizarlos en Log Analytics."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 59f674c9c6404da7f5384539189f41a4ba1a939a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Recopilación de datos de Log Analytics con un runbook de Azure Automation
Puede recopilar una cantidad significativa de datos en Log Analytics desde diversos orígenes como son los [orígenes de datos](../log-analytics/log-analytics-data-sources.md) de agentes y también los [datos recopilados de Azure](../log-analytics/log-analytics-azure-storage.md).  Sin embargo, hay un escenario en el que es necesario recopilar datos que no son accesibles a través de estos orígenes estándar.  Puede usar [HTTP Data Collector API](../log-analytics/log-analytics-data-collector-api.md) para escribir los datos de Log Analytics desde cualquier cliente de API de REST.  Un método común para realizar esta recopilación de datos es usar un runbook en Azure Automation.   

Este tutorial le guía a través del proceso para crear y programar un runbook en Azure Automation para escribir datos en Log Analytics.


## <a name="prerequisites"></a>Requisitos previos
Este escenario requiere los siguientes recursos configurados en su suscripción de Azure.  Ambos pueden ser una cuenta gratuita.

- [Área de trabajo de Log Analytics](../log-analytics/log-analytics-get-started.md).
- [Cuenta de Azure Automation](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Información general del escenario
En este tutorial, escribirá un runbook que recopila información acerca de los trabajos de Automation.  Los runbooks de Azure Automation se implementan con PowerShell, de modo que podrá empezar por escribir y probar un script en el editor de Azure Automation.  Cuando haya comprobado que está recopilando la información necesaria, escribirá los datos en Log Analytics y comprobará el tipo de datos personalizado.  Finalmente, creará una programación para iniciar el runbook a intervalos regulares.

> [!NOTE]
> Puede configurar Azure Automation para enviar información de trabajos a Log Analytics sin este runbook.  Este escenario se usa principalmente para el tutorial y se recomienda enviar los datos a un área de trabajo de prueba.  


## <a name="1-install-data-collector-api-module"></a>1. Instalación del módulo Data Collector API
Cada [solicitud de HTTP Data Collector API](../log-analytics/log-analytics-data-collector-api.md#create-a-request) debe tener el formato adecuado e incluir un encabezado de autorización.  Puede hacer esto en el runbook, aunque puede reducir la cantidad de código necesario con un módulo que simplifica este proceso.  Uno de los módulos que puede usar es [OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI) en la Galería de PowerShell.

Para usar un [módulo](../automation/automation-integration-modules.md) en un runbook, debe estar instalado en su cuenta de Automation.  Cualquier runbook de la misma cuenta puede utilizar entonces las funciones del módulo.  Puede instalar un nuevo módulo seleccionando **Activos** > **Módulos** > **Agregar un módulo** en su cuenta de Automation.  

Sin embargo, la Galería de PowerShell le ofrece una opción rápida para implementar un módulo directamente en su cuenta de Automation, de modo que puede usar esa opción para este tutorial.  

![Módulo OMSIngestionAPI](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Vaya a la [Galería de PowerShell](https://www.powershellgallery.com/).
2. Busque **OMSIngestionAPI**.
3. Haga clic en el botón **Deploy to Azure Automation** (Implementar en Azure Automation).
4. Seleccione su cuenta de Automation y haga clic en **Aceptar** para instalar el módulo.


## <a name="2-create-automation-variables"></a>2. Creación de las variables de Automation
[Las variables de Automation](..\automation\automation-variables.md) contienen valores que pueden usarse en todos los runbooks en su cuenta de Automation.  Hacen que los runbooks sean más flexibles, ya que permiten cambiar estos valores sin necesidad de modificar el runbook real. Cada solicitud de HTTP Data Collector API requiere el identificador y la clave del área de trabajo de OMS y los recursos de variable son ideales para almacenar esta información.  

![Variables](media/operations-management-suite-runbook-datacollect/variables.png)

1. En Azure Portal, vaya a su cuenta de Automation.
2. Seleccione **Variables** en **Recursos compartidos**.
2. Haga clic en **Agregar una variable** y cree las dos variables en la tabla siguiente.

| Propiedad | Valor de identificador de área de trabajo | Valor de clave de área de trabajo |
|:--|:--|:--|
| Nombre | WorkspaceId | WorkspaceKey |
| Tipo | String | String |
| Valor | Pegue el identificador de área de trabajo de su área de trabajo de Log Analytics. | Pegue la clave secundaria o principal de su área de trabajo de Log Analytics. |
| Cifrados | No | Sí |



## <a name="3-create-runbook"></a>3. Creación de runbook

Azure Automation tiene un editor en el portal donde puede editar y probar el runbook.  Tiene la opción de usar el editor de scripts para trabajar con [PowerShell directamente](../automation/automation-edit-textual-runbook.md) o [crear un runbook gráfico](../automation/automation-graphical-authoring-intro.md).  En este tutorial, trabajará con un script de PowerShell. 

![Edición de un runbook](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Vaya a su cuenta de Automation.  
2. Haga clic en **Runbooks** > **Agregar un runbook** > **Crear un nuevo runbook**.
3. Como nombre del runbook, escriba **Collect-Automation-jobs**.  Como tipo de runbook, seleccione **PowerShell**.
4. Haga clic en **Crear** para crear el runbook e inicie el editor.
5. Copie y pegue el código siguiente en el runbook.  Consulte los comentarios en el script para ver una explicación del código.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Prueba del runbook
Azure Automation incluye un entorno para [probar el runbook](../automation/automation-testing-runbook.md) antes de publicarlo.  Puede inspeccionar los datos recopilados por el runbook y comprobar que escribe en Log Analytics según lo previsto antes de publicarlo para producción. 
 
![Prueba del runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Haga clic en **Guardar** para guardar el runbook.
1. Haga clic en **Panel de prueba** para abrir el runbook en el entorno de prueba.
3. Puesto que el runbook tiene parámetros, se le pedirá que escriba sus valores.  Escriba el nombre del grupo de recursos y la cuenta de Automation desde los que va a recopilar la información de los trabajos.
4. Haga clic en **Iniciar** para iniciar el runbook.
3. Se iniciará el runbook con el estado **En cola** antes de pasar a **En ejecución**.  
3. El runbook debe mostrar una salida detallada con los trabajos recopilados en formato json.  Si no aparece ningún trabajo, puede que no se haya creado ninguno en la cuenta de Automation en la última hora.  Pruebe a iniciar un runbook en la cuenta de Automation y, a continuación, vuelva a realizar la prueba.
4. Asegúrese de que el resultado no muestra ningún error en el comando de registro de Log Analytics.  Debería ver un mensaje similar al siguiente.

    ![Registro de salida](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Comprobación de los registros de Log Analytics
Una vez que el runbook ha completado la prueba y se ha comprobado que la salida se ha recibido correctamente, puede comprobar que los registros se crearon mediante una [búsqueda de registros de Log Analytics](../log-analytics/log-analytics-log-searches.md).

![Resultados del registro](media/operations-management-suite-runbook-datacollect/log-output.png)

1. En Azure Portal, seleccione el área de trabajo de Log Analytics.
2. Haga clic en **Búsqueda de registros**.
3. Escriba el siguiente comando `Type=AutomationJob_CL` y haga clic en el botón de búsqueda. Tenga en cuenta que el tipo de registro incluye _CL, que no se especifica en el script.  Dicho sufijo se anexa automáticamente al tipo de registro para indicar que es un tipo de registro personalizado.
4. Debería ver que uno o varios de los registros devueltos indican que el runbook funciona según lo previsto.


## <a name="6-publish-the-runbook"></a>6. Publicación del runbook
Una vez que haya comprobado que el runbook funciona correctamente, debe publicarlo para que pueda ejecutarse en un entorno de producción.  Puede seguir editando y probando el runbook sin modificar la versión publicada.  

![Publicación del runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Vuelva a su cuenta de Automation.
2. Haga clic en **Runbooks** y seleccione **Collect-Automation-jobs**.
3. Haga clic en **Editar** y, a continuación, en **Publicar**.
4. Haga clic en **Sí** cuando se le pida que confirme que desea sobrescribir la versión publicada previamente.

## <a name="7-set-logging-options"></a>7. Establecimiento de las opciones de registro 
Para la prueba, podía ver la [salida detallada](../automation/automation-runbook-output-and-messages.md#message-streams) porque estableció la variable $VerbosePreference en el script.  En entornos de producción, si desea ver una salida detallada, debe establecer las propiedades de registro del runbook.  Para el runbook usado en este tutorial, se mostrarán los datos json que se envían a Log Analytics.

![Registro y seguimiento](media/operations-management-suite-runbook-datacollect/logging.png)

1. En las propiedades del runbook, seleccione **Registro y seguimiento** en **Configuración de runbook**.
2. Cambie la configuración de **Registrar registros detallados** a **Activado**.
3. Haga clic en **Guardar**.

## <a name="8-schedule-runbook"></a>8. Programación de un runbook
La manera más común de iniciar un runbook que recopila datos de supervisión es programarlo para que se ejecute automáticamente.  Para ello, debe crear una [programación en Azure Automation](../automation/automation-schedules.md) y asociarla al runbook.

![Programación de un runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. En las propiedades del runbook, seleccione **Programaciones** en **Recursos**.
2. Haga clic en **Agregar una programación** > **Vincular una programación a su runbook** > **Crear una nueva programación**.
5. Escriba los valores siguientes para la programación y haga clic en **Crear**.

| Propiedad | Valor |
|:--|:--|
| Nombre | AutomationJobs-Hourly |
| Se inicia | Seleccione cualquier hora al menos 5 minutos después de la hora actual. |
| Periodicidad | Periódica |
| Repetir cada | 1 hora |
| Configurar expiración | No |

Una vez creada la programación, debe establecer los valores de los parámetros que se usarán cada vez que esta inicie el runbook.

6. Haga clic en **Configurar parámetros y ejecutar configuraciones**.
7. Rellene los valores de **ResourceGroupName** y **AutomationAccountName**.
8. Haga clic en **Aceptar**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Comprobación de que el runbook se inicia según la programación
Siempre que se inicia un runbook, [se crea un trabajo](../automation/automation-runbook-execution.md) y se registran sus resultados.  De hecho, son los mismos trabajos que el runbook recopila.  Puede verificar que el runbook se inicia como se esperaba comprobando los trabajos del runbook una vez que haya pasado la hora de inicio de la programación.

![Trabajos](media/operations-management-suite-runbook-datacollect/jobs.png)

1. En las propiedades del runbook, seleccione **Trabajos** en **Recursos**.
2. Debería ver una lista de trabajos de cada vez que se inició el runbook.
3. Haga clic en uno de los trabajos para ver sus detalles.
4. Haga clic en **Todos los registros** para ver los registros y la salida del runbook.
5. Desplácese hacia abajo para buscar una entrada similar a la de la imagen siguiente.<br>![Detallado](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Haga clic en esta entrada para ver los datos de json detallados que se enviaron a Log Analytics.



## <a name="next-steps"></a>Pasos siguientes
- Use el [Diseñador de vistas](../log-analytics/log-analytics-view-designer.md) para crear una vista que muestre los datos que se han recopilado en el repositorio de Log Analytics.
- Empaquete el runbook en una [solución de administración](operations-management-suite-solutions-creating.md) para distribuirla a los clientes.
- Más información sobre [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Más información sobre [Azure Automation](https://docs.microsoft.com/azure/automation/).
- Más información sobre la [HTTP Data Collector API](../log-analytics/log-analytics-data-collector-api.md).