---
title: "Uso de etiquetas con formato JSON para programar el estado de la máquina virtual de Azure | Microsoft Docs"
description: "En este artículo se explica cómo usar cadenas JSON en las etiquetas para automatizar la programación de inicio y apagado de máquinas virtuales."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: af0bc6b24a403bd09092ac0a099c500d651d15b7
ms.lasthandoff: 04/27/2017


---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Escenario de Automatización de Azure: uso de etiquetas con formato JSON para crear una programación de inicio y apagado de VM de Azure
Normalmente, los clientes quieren programar el inicio y apagado de las máquinas virtuales para reducir los costos de suscripción o dar cabida a requisitos empresariales y técnicos.

El siguiente escenario le permite configurar el inicio y apagado automatizados de máquinas virtuales por medio de una etiqueta denominada Schedule en un nivel de grupo de recursos o de máquina virtual de Azure. Esta programación se puede configurar de domingo a sábado con una hora de inicio y una hora de apagado.

Tenemos algunas opciones disponibles de forma inmediata. Entre ellos se incluyen los siguientes:

* [Conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) con una configuración de escalado automático que le permite escalar o reducir horizontalmente.
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) , que tiene una funcionalidad integrada para programar las operaciones de inicio y apagado.

Sin embargo, estas opciones solo admiten escenarios específicos y no se pueden aplicar a las máquinas virtuales de infraestructura como servicio (IaaS).

Cuando la etiqueta Schedule se aplica a un grupo de recursos, se aplica también a todas las máquinas virtuales dentro de ese grupo de recursos. Si también se aplica una programación directamente a una máquina virtual, tiene prioridad la última programación en el siguiente orden:

1. Programación aplicada a un grupo de recursos
2. Programación aplicada a un grupo de recursos y a una máquina virtual en el grupo de recursos
3. Programación aplicada a una máquina virtual

En este escenario, lo que ocurre básicamente es que se toma una cadena JSON con un formato especificado y se agrega como el valor de una etiqueta denominada Schedule. Posteriormente, un Runbook muestra todos los grupos de recursos y máquinas virtuales e identifica las programaciones para cada máquina virtual según los escenarios mencionados anteriormente. A continuación, recorre las máquinas virtuales que tienen programaciones adjuntas y evalúa qué acciones deben realizarse. Por ejemplo, determina qué máquinas virtuales necesitan detenerse, apagarse o ignorarse.

Estos runbooks se autentican mediante la [Cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Descarga de los Runbooks para el escenario
Este escenario consta de cuatro Runbooks de flujo de trabajo de PowerShell que puede descargar de la [Galería de TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) o del repositorio [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) relativo a este proyecto.

| Runbook | Descripción |
| --- | --- |
| Test-ResourceSchedule |Comprueba la programación de cada máquina virtual y realiza el apagado o inicio de estas según la programación. |
| Add-ResourceSchedule |Agrega la etiqueta Schedule a una máquina virtual o grupo de recursos. |
| Update-ResourceSchedule |Modifica la etiqueta Schedule existente reemplazándola por una nueva. |
| Remove-ResourceSchedule |Quita la etiqueta Schedule de una máquina virtual o grupo de recursos. |

## <a name="install-and-configure-this-scenario"></a>Instalación y configuración de este escenario
### <a name="install-and-publish-the-runbooks"></a>Instalar y publicar los Runbooks
Después de descargar los Runbooks, puede importarlos mediante el procedimiento descrito en [Creación o importación de un runbook en Azure Automation](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Publique cada Runbook después de que se hayan importado correctamente en la cuenta de Automatización.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Incorporación de una programación al Runbook Test-ResourceSchedule
Haga lo siguiente para habilitar la programación en el Runbook Test-ResourceSchedule. Este es el Runbook que comprueba las máquinas virtuales que debe iniciar, apagar o quedar como están.

1. Desde el Portal de Azure, abra la cuenta de Automatización y haga clic en el icono **Runbooks** .
2. En la hoja de **Test-ResourceSchedule**, haga clic en el icono **Programaciones**.
3. En la hoja **Programaciones**, haga clic en **Agregar una programación**.
4. En la hoja **Programaciones**, seleccione **Vincular una programación a su Runbook**. A continuación, seleccione **Crear una programación nueva**.
5. En la hoja **Nueva programación** , escriba el nombre de esta programación, por ejemplo: *HourlyExecution*.
6. Para la programación **Inicio**, establezca la hora de inicio en un incremento de hora.
7. Seleccione **Periodicidad** y, en **Recur every interval** (Repetir cada intervalo), seleccione **1 hora**.
8. Compruebe que **Configurar expiración** está establecido en **No** y, después, haga clic en **Crear** para guardar la nueva programación.
9. En hoja de opciones **Programar Runbook**, seleccione **Configuración de ejecución y parámetros**. En la hoja **Parámetros** de Test-ResourceSchedule, escriba el nombre de la suscripción en el campo **SubscriptionName**.  Este es el único parámetro necesario para el Runbook.  Cuando haya terminado, haga clic en **Aceptar**.

La programación del Runbook debe parecerse a lo siguiente cuando se complete:

![Runbook Test-ResourceSchedule configurado](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>Formato de la cadena JSON
Esta solución básicamente toma una cadena JSON con un formato especificado y lo agrega como el valor de una etiqueta denominada Schedule. Posteriormente, un Runbook muestra todos los grupos de recursos y máquinas virtuales e identifica las programaciones para cada máquina virtual.

El Runbook recorre las máquinas virtuales que tienen programaciones adjuntas y comprueba qué acciones deben ejecutarse. Este es un ejemplo de cómo aplicar el formato a las soluciones:

```json
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
```

Información detallada sobre esta estructura:

1. El formato de esta estructura JSON está optimizado para evitar la limitación de 256 caracteres de un valor de etiqueta única en Azure.
2. *TzId* es la zona horaria de la máquina virtual. Este identificador se puede obtener con la clase .NET. TimeZoneInfo en una sesión de PowerShell: **[System.TimeZoneInfo]::GetSystemTimeZones()**.

   ![GetSystemTimeZones en PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * Los días de la semana se representan con un valor numérico entre 0 y 6. El valor 0 corresponde al domingo.
   * La hora de inicio se representa con el atributo **S** y su valor se presenta en un formato de 24 horas.
   * La hora de fin o apagado se representa con el atributo **E** y su valor se presenta en un formato de 24 horas.

     Si los atributos **S** y **E** tienen un valor de cero (0), la máquina virtual permanecerá en el estado que tenga en el momento de la evaluación.
3. Si quiere omitir la evaluación para un determinado día de la semana, no agregue la sección del día de la semana en cuestión. En el siguiente ejemplo solo se evaluará el lunes y se ignorarán los demás días de la semana:

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>Etiquetado de máquinas virtuales o grupos de recursos
Para apagar las máquinas virtuales, debe etiquetar las máquinas virtuales o los grupos de recursos en los que se encuentran. No se evalúan las máquinas virtuales que no tienen una etiqueta Schedule. Por lo tanto, estas no se iniciarán ni apagarán.

Hay dos maneras de etiquetar máquinas virtuales o grupos de recursos con esta solución. Puede hacerlo directamente desde el portal. O bien, puede usar los Runbooks Add-ResourceSchedule, Update-ResourceSchedule y Remove-ResourceSchedule.

### <a name="tag-through-the-portal"></a>Etiquetado a través del portal
Haga lo siguiente para etiquetar una máquina virtual o un grupo de recursos en el portal:

1. Acople la cadena JSON y compruebe que no hay espacios.  La cadena JSON debe tener este aspecto:

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. Seleccione el icono de **etiqueta** de una máquina virtual o grupo de recursos para aplicarle esta programación.

   ![Opción de etiqueta de máquina virtual](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. Las etiquetas se definen siguiendo un par clave/valor. Escriba **Schedule** en el campo **Clave** y pegue la cadena JSON en el campo **Valor**. Haga clic en **Save**. La nueva etiqueta debería aparecer en la lista de etiquetas del recurso.

   ![Etiqueta Schedule de máquina virtual](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>Etiquetado en PowerShell
Todos los Runbooks importados contienen información de ayuda al principio del script que describe cómo ejecutar los Runbooks directamente desde PowerShell. Puede llamar a los Runbooks Add-ScheduleResource y Update-ScheduleResource desde PowerShell. Para ello, utilice los parámetros necesarios que le permiten crear o actualizar la etiqueta Schedule en una máquina virtual o grupo de recursos fuera del portal.

Para crear, agregar y eliminar etiquetas a través de PowerShell, primero es preciso [configurar el entorno de PowerShell para Azure](/powershell/azure/overview). Una vez completada la configuración, puede continuar con los pasos siguientes.

### <a name="create-a-schedule-tag-with-powershell"></a>Creación de una etiqueta Schedule con PowerShell
1. Abra una sesión de PowerShell. A continuación, use lo siguiente para autenticarse con la cuenta de ejecución y especificar una suscripción:

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Defina una tabla hash de programación. Este es un ejemplo de cómo debe estar conformada:

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. Defina los parámetros que el Runbook necesita. En el siguiente ejemplo, el destino es una máquina virtual:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    Si quiere etiquetar un grupo de recursos, quite el parámetro *VMName* de la tabla hash $params de la siguiente manera:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. Ejecute el Runbook Add-ResourceSchedule con los siguientes parámetros para crear la etiqueta Schedule:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. Para actualizar una etiqueta de una máquina virtual o un grupo de recursos, ejecute el Runbook **Update-ResourceSchedule** con los siguientes parámetros:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>Eliminación de una etiqueta Schedule con PowerShell
1. Abra una sesión de PowerShell y ejecute lo siguiente para autenticarse con la cuenta de ejecución y seleccionar y especificar una suscripción:

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Defina los parámetros que el Runbook necesita. En el siguiente ejemplo, el destino es una máquina virtual:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    Si quiere eliminar una etiqueta de un grupo de recursos, simplemente quite el parámetro *VMName* de la tabla hash $params de la siguiente manera:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Ejecute el Runbook Remove-ResourceSchedule para eliminar la etiqueta Schedule:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. Para actualizar una etiqueta de una máquina virtual o un grupo de recursos, ejecute el Runbook Remove-ResourceSchedule con los siguientes parámetros:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> Se recomienda supervisar de forma proactiva estos Runbooks (y el estado de las máquinas virtuales) para comprobar que las máquinas virtuales se apagan e inician como es debido.
>

Para ver los detalles del trabajo del Runbook Test-ResourceSchedule en el Portal de Azure, seleccione el icono **Trabajos** del Runbook. La opción de resumen del trabajo le mostrará los parámetros de entrada y el flujo de salida, además de información general sobre el trabajo y las excepciones que se hayan producido.

El **resumen del trabajo** incluye los mensajes de los flujos de salida, advertencia y error. Seleccione el icono **Salida** para ver los resultados detallados de la ejecución del Runbook.

![Salida de Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer Runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md).
* Para aprender más sobre los tipos de Runbook, sus ventajas y sus limitaciones, consulte [Tipos de Runbooks de Automatización de Azure](automation-runbook-types.md).
* Para más información sobre las características de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)(Anuncio de la compatibilidad nativa con scripts de PowerShell en Automatización de Azure).
* Para más información sobre el registro y salida de Runbooks, vea [Salidas de runbook y mensajes en Automatización de Azure](automation-runbook-output-and-messages.md).
* Para obtener más información sobre una cuenta de ejecución de Azure y de cómo autenticar sus runbooks mediante ella, consulte [Autenticación de Runbooks con una cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md).

