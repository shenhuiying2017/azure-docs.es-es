<properties
   pageTitle="Uso de etiquetas con formato JSON para crear una programación de inicio y apagado de VM de Azure | Microsoft Azure"
   description="En este artículo se explica cómo usar cadenas JSON en las etiquetas para automatizar la programación de inicio y apagado de máquinas virtuales."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/18/2016"
   ms.author="magoedte;paulomarquesc" />

# Escenario de Automatización de Azure: uso de etiquetas con formato JSON para crear una programación de inicio y apagado de VM de Azure

Normalmente, los clientes quieren programar el inicio y apagado de las máquinas virtuales de cara a reducir los costos de suscripción o dar cabida a requisitos empresariales y técnicos.

El siguiente escenario permite configurar el inicio y apagado automatizados de máquinas virtuales por medio de una etiqueta denominada Schedule en un nivel de grupo de recursos o de máquina virtual de Azure. Esta programación se puede configurar de domingo a sábado con una hora de inicio y una hora de apagado. Existen algunas opciones directamente de fábrica, como usar [Conjuntos de escala de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) con opciones de escala automática que permitirán reducir o escalar horizontalmente, o el servicio [DevTest Labs](../devtest-lab/devtest-lab-overview.md), que tiene la capacidad incorporada de programar operaciones de inicio y apagado, pero estas opciones solo admiten escenarios muy concretos y no son válidas en máquinas virtuales de IaaS.

Cuando la etiqueta Schedule se aplica a un grupo de recursos, se aplicará a todas las máquinas virtuales dentro de ese grupo de recursos. Si también se aplica una programación directamente a una máquina virtual, tendrá prioridad la última programación en el siguiente orden:

1.  Programación aplicada a un grupo de recursos
2.  Programación aplicada a un grupo de recursos y a una máquina virtual en el grupo de recursos
3.  Programación aplicada a una máquina virtual

En este escenario, lo que ocurre básicamente es que se toma una cadena JSON con un formato especificado y se agrega como el valor de una etiqueta denominada Schedule. Luego, un Runbook enumera todos los grupos de recursos y máquinas virtuales e identifica las programaciones de cada máquina virtual según los escenarios mencionados anteriormente, recorre estas máquinas virtuales con las programaciones adjuntas y evalúa qué acción debe realizarse, cuáles se pueden detener, apagar u omitir.

Estos Runbooks se autentican mediante la [Cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md).

## Obtención del escenario

Este escenario consta de cuatro Runbooks de flujo de trabajo de PowerShell que puede descargar de la [Galería de TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) o del repositorio [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) relativo a este proyecto.

Runbook | Descripción 
----------|----------
Test-ResourceSchedule | Comprueba la programación de cada máquina virtual (apaga o inicia las máquinas virtuales según la programación)
Add-ResourceSchedule | Agrega la etiqueta Schedule a una máquina virtual o grupo de recursos
Update-ResourceSchedule | Modifica una etiqueta Schedule existente reemplazándola por otra
Remove-ResourceSchedule | Quita la etiqueta Schedule de una máquina virtual o grupo de recursos 


## Instalación y configuración de este escenario

### Instalar y publicar los Runbooks

Tras descargar los Runbooks, puede importarlos con el procedimiento descrito para [importar Runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation). Publique cada Runbook después de que se hayan importado correctamente en la cuenta de Automatización.


### Agregar una programación a Test-ResourceSchedule

Haga lo siguiente para habilitar la programación en el Runbook Test-ResourceSchedule. Este es el Runbook que comprueba qué máquina virtual se va a iniciar, apagar o dejar tal cual.

1. Desde el Portal de Azure, abra la cuenta de Automatización y haga clic en el icono **Runbooks**.
2. En la hoja de **Test-ResourceSchedule**, haga clic en el icono **Programaciones**.
3. En la hoja **Programaciones**, haga clic en **Agregar una programación**.
4. En la hoja **Programaciones**, seleccione **Vincular una programación a su Runbook** y, en la hoja **Programación**, seleccione **Crear una nueva programación**.
5.  En la hoja **Nueva programación**, escriba el nombre de esta programación. (por ejemplo, HourlyExecution).
6. Como **inicio** de la programación, establezca la hora de inicio en un incremento de hora redondeado.
7. Seleccione **Periodicidad** y, como intervalo de **repetición**, seleccione **1 hora**.
8. Compruebe que **Configurar expiración** está establecido en **No** y, después, haga clic en **Crear** para guardar la nueva programación.
9. En la hoja de opciones **Programar Runbook**, seleccione la opción **Configuración de ejecución y parámetros** y, en la hoja **Parámetros** de Test-ResourceSchedule, escriba el nombre de la suscripción en el campo **SubscriptionName**. Este es el único parámetro necesario para el Runbook. Haga clic en **Aceptar** cuando finalice.
   

La programación del Runbook debe parecerse a lo siguiente cuando se complete:<br> ![Runbook Test-ResourceSchedule configurado](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## Formato JSON de programación

Esta solución básicamente toma una cadena JSON con un formato especificado y lo agrega como el valor de una etiqueta denominada Schedule. Luego, un Runbook enumera todos los grupos de recursos y máquinas virtuales e identifica las programaciones de cada máquina virtual, recorre estas máquinas virtuales con sus programaciones adjuntas y evalúa qué acción debe realizarse. Este es un ejemplo de qué formato debe aplicarse.

    {
       "TzId": "Eastern Standard Time", 
        "0": {  
           "S": "11",
           "E": "17" 
        },
        "1": {
           "S": "9",
           "E": "19"
        },
        "2": {
           "S": "9",
           "E": "19"
        },
    }

Información detallada sobre esta estructura:

1. El formato de esta estructura JSON está optimizado para evitar la limitación de 256 caracteres de un valor de etiqueta única en Azure.

2. *TzId* es la zona horaria de la máquina virtual. Este identificador se puede obtener con la clase .NET. TimeZoneInfo en una sesión de PowerShell: **[System.TimeZoneInfo]::GetSystemTimeZones()**.<br>

    ![GetSystemTimeZones en PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - Los días de la semana se representan con un valor numérico entre 0 y 6. Un valor 0 equivale al domingo.
    - La hora de inicio se representa con el atributo **S** y su valor se presenta en un formato de 24 horas.
    - La hora de fin o apagado se representa con el atributo **E** y su valor se presenta en un formato de 24 horas.

    Si los atributos S y E tienen un valor de cero (0), la máquina virtual permanecerá en el estado que tenga en el momento de la evaluación.

3. Si quiere omitir la evaluación de un determinado día de la semana, no agregue la sección del día de la semana en cuestión. En el siguiente ejemplo solo se evaluará el lunes y se omitirán los demás días de la semana.
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## Etiquetar máquinas virtuales o grupos de recursos

Para apagar las máquinas virtuales, debe etiquetarlas o etiquetar los grupos de recursos donde esas máquinas virtuales se encuentren. Las máquinas virtuales que no tengan una etiqueta Schedule no se evalúan y, por lo tanto, no se inician ni se apagan. Hay dos formas de etiquetar máquinas virtuales o grupos de recursos con esta solución: directamente desde el Portal o con los Runbooks **Add-ResourceSchedule**, **Update-ResourceSchedule** y **Remove-ResourceSchedule**.

### Etiquetado a través del portal

Haga lo siguiente para etiquetar una máquina virtual o un grupo de recursos en el Portal.

1. Acople la cadena JSON y compruebe que no hay espacios. La cadena JSON debe tener este aspecto:

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. Seleccione un máquina virtual o un grupo de recursos para aplicar esta programación seleccionando el icono de etiqueta.<br>![Opción de etiqueta de máquina virtual](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. Las etiquetas se definen siguiendo un par clave/valor. Escriba **Schedule** en el campo **Clave** y pegue la cadena JSON en el campo **Valor**. Luego, haga clic en **Guardar**. La nueva etiqueta debería aparecer en la lista de etiquetas del recurso.<br>![Etiqueta Schedule de máquina virtual](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### Etiquetar desde PowerShell

Todos los Runbooks importados contienen información de ayuda al principio del script que describe cómo ejecutar esos Runbooks directamente desde PowerShell. Para llamar a los Runbooks Add-ScheduleResource y Update-ScheduleResource desde PowerShell, hay que pasar los parámetros necesarios que permiten crear o actualizar la etiqueta Schedule en una máquina virtual o un grupo de recursos fuera del portal.

Para crear, agregar y eliminar etiquetas a través de PowerShell, primero es preciso [configurar el entorno de PowerShell para Azure](../powershell-install-configure.md). Una vez completada la configuración, puede continuar con los pasos siguientes.

### Creación de una etiqueta Schedule con PowerShell

1. Abra una sesión de PowerShell y ejecute lo siguiente para autenticarse con la cuenta de ejecución y especificar una suscripción:
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. Defina una tabla hash de programación. Este es un ejemplo de cómo debe estar conformada:
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. Defina los parámetros que el Runbook necesita. En el siguiente ejemplo, el destino es una máquina virtual:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    Si quiere etiquetar un grupo de recursos, simplemente quite el parámetro *VMName* de la tabla hash $params. Así:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. Ejecute el Runbook **Add-ResourceSchedule** con los siguientes parámetros para crear la etiqueta Schedule:

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. Si quiere actualizar una etiqueta de una máquina virtual o un grupo de recursos, ejecute el Runbook **Update-ResourceSchedule** con los siguientes parámetros:

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### Eliminación de una etiqueta Schedule con PowerShell

1. Abra una sesión de PowerShell y ejecute lo siguiente para autenticarse con la cuenta de ejecución y seleccionar y especificar una suscripción:
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. Defina los parámetros que el Runbook necesita. En el siguiente ejemplo, el destino es una máquina virtual:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    Si quiere eliminar una etiqueta de un grupo de recursos, simplemente quite el parámetro *VMName* de la tabla hash $params. Así:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. Ejecute el Runbook **Remove-ResourceSchedule** para eliminar la etiqueta Schedule:

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. Si quiere actualizar una etiqueta de una máquina virtual o un grupo de recursos, ejecute el Runbook **Remove-ResourceSchedule** con los siguientes parámetros:

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] Se recomienda supervisar de forma proactiva estos Runbooks (y el estado de las máquinas virtuales) para comprobar que las máquinas virtuales se apagan e inician como deben.

Puede ver los detalles del trabajo de Runbook **Test-ResourceSchedule** en el Portal de Azure si selecciona el icono Trabajos del Runbook. La opción Resumen del trabajo le mostrará los parámetros de entrada y el flujo de salida, además de información general sobre el trabajo y las excepciones que se hayan producido. El historial incluirá mensajes del flujo de salida y de los flujos de error y de advertencia. Seleccione el icono Salida para ver los resultados detallados de la ejecución del Runbook.<br> ![Salida de Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## Pasos siguientes

-  Para empezar a trabajar con runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md).
-  Para saber más sobre los tipos de runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Automatización de Azure](automation-runbook-types.md).
-  Para más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Anuncio de la compatibilidad nativa con scripts de PowerShell en Automatización de Azure).
-  Para más información sobre el registro y salida de Runbooks, vea [Salidas de runbook y mensajes en la Automatización de Azure](automation-runbook-output-and-messages.md).
-  Para más información acerca de una cuenta de ejecución de Azure y de cómo autenticar sus Runbooks mediante ella, consulte [Autenticación de Runbooks con una cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md).

<!---HONumber=AcomDC_0720_2016-->