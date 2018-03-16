---
title: "Guía de procedimientos sobre las notificaciones por correo electrónico del ajuste automático: Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database analiza una consulta SQL y se adapta automáticamente a la carga de trabajo del usuario."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
ms.reviewer: carlrab
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: 611c30639b5fb36bb08ebd3e73c90f8aa2bd09d4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>Notificaciones por correo electrónico para el ajuste automático

Las recomendaciones de ajustes de SQL Database se generan mediante el [ajuste automático](sql-database-automatic-tuning.md) de Azure SQL Database. Esta solución supervisa y analiza de forma continua las cargas de trabajo de las bases de datos SQL Database y proporciona recomendaciones de ajustes personalizadas para cada base de datos individual relacionadas con la creación y eliminación de índices y la optimización de los planes de ejecución de consultas.

Las recomendaciones de ajuste automático de SQL Database pueden verse en [Azure Portal](sql-database-advisor-portal.md) y recuperarse con llamadas a la [API de REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor), o mediante los comandos [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) y [ PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction). Este artículo se basa en el uso de un script de PowerShell para recuperar las recomendaciones de ajuste automático.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatización de las notificaciones por correo electrónico para las recomendaciones del ajuste automático

La siguiente solución automatiza el envío de notificaciones por correo electrónico que contienen recomendaciones sobre el ajuste automático. La solución descrita consiste en automatizar la ejecución de un script de PowerShell para recuperar las recomendaciones de ajustes mediante [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), y automatizar el trabajo de programación de la entrega de correos electrónicos mediante [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Creación de una cuenta de Azure Automation

Para utilizar Azure Automation, primero debe crear una cuenta de automatización y configurarla con los recursos de Azure que se usarán para ejecutar el script de PowerShell. Para obtener más información acerca de Azure Automation, consulte [Introducción a Azure Automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Siga estos pasos para crear la cuenta de Azure Automation a través del método de selección y configuración de la aplicación Automation de Marketplace:

- Inicie sesión en Azure Portal.
- Haga clic en "**+ Crear un recurso**" en la esquina superior izquierda.
- Busque "**Automation**" (presione ENTRAR).
- Haga clic en la aplicación Automation en los resultados de la búsqueda.

![Agregar Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Una vez dentro del panel "Crear una cuenta de automatización", haga clic en "**Crear**".
- Rellene la información necesaria: escriba un nombre para esta cuenta de automatización y seleccione los recursos de Azure y el identificador de suscripción de Azure que se usará para la ejecución del script de PowerShell.
- Para la opción "**Crear cuenta de ejecución de Azure**", seleccione **Sí** para configurar el tipo de cuenta en que el script de PowerShell se ejecuta con la ayuda de Azure Automation. Para obtener más información acerca de los tipos de cuenta, consulte [Cuenta de ejecución](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Para finalizar la creación de la cuenta de automatización, haga clic en **Crear**.

> [!TIP]
> Registre el nombre de la cuenta de Azure Automation, el identificador de suscripción y los recursos (por ejemplo, copiar y pegar en el Bloc de notas) tal y como se especificó al crear la aplicación Automation. Necesitará esta información más adelante.
>

Si tiene varias suscripciones a Azure para las que quiere compilar la misma automatización, deberá repetir este proceso para las otras suscripciones.

## <a name="update-azure-automation-modules"></a>Actualización de los módulos de Azure Automation

El script de PowerShell para recuperar la recomendación de ajuste automático usa los comandos [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) y [Get AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) para los que se requiere la actualización de los módulos de Azure a la versión 4 o posterior.

Siga estos pasos para actualizar los módulos de Azure PowerShell:

- Acceda al panel de la aplicación Automation y seleccione "**Módulos**" en el menú de la izquierda (desplácese hacia abajo, ya que este elemento de menú se encuentra debajo de Recursos compartidos).
- En el panel Módulos, haga clic en "**Actualizar módulos de Azure**" en la parte superior y espere a que se muestre el mensaje "Los módulos de Azure se han actualizado". Este proceso puede tardar unos minutos en completarse.

![Actualización de los módulos de Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Se requiere que los módulos AzureRM.Resources y AzureRM.Sql tengan la versión 4 o posterior.

## <a name="create-azure-automation-runbook"></a>Creación de un runbook de Azure Automation

El siguiente paso consiste en crear un runbook en Azure Automation en el que se encuentre el script de PowerShell para la recuperación de recomendaciones de ajuste.

Para crear un nuevo runbook de Azure Automation, siga estos pasos:

- Acceda a la cuenta de Azure Automation que creó en el paso anterior.
- Una vez esté en el panel de la cuenta de automatización, haga clic en el elemento de menú "**Runbooks**" en el lado izquierdo para crear un nuevo runbook de Azure Automation con el script de PowerShell. Para obtener más información sobre la creación de runbooks de automatización, consulte [Creación de un runbook](../automation/automation-creating-importing-runbook.md).
- Para agregar un runbook nuevo, haga clic en la opción de menú "**+Agregar un runbook**" y, a continuación, haga clic en "**Creación rápida: Crear un runbook nuevo**".
- En el panel Runbook, escriba el nombre de su runbook (en este ejemplo, se usa "**AutomaticTuningEmailAutomation**"), seleccione el tipo de runbook como **PowerShell** y escriba una descripción de este runbook para indicar su finalidad.
- Haga clic en el botón **Crear** para terminar de crear un runbook.

![Agregar un runbook de Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Siga estos pasos para cargar un script de PowerShell en el runbook creado:

- En el panel "**Editar runbook de PowerShell**", seleccione "**RUNBOOKS**" en el árbol de menú y expanda la vista hasta que vea el nombre del runbook (en este ejemplo, "**AutomaticTuningEmailAutomation**"). Seleccione este runbook.
- En la primera línea del panel "Editar runbook de PowerShell" (comenzando por el número 1), copie y pegue el siguiente código de script de PowerShell. Este script de PowerShell se proporciona tal cual para ayudarle a comenzar. Modifíquelo para que se adapte a sus necesidades.

En el encabezado del script de PowerShell proporcionado, es necesario reemplazar `<SUBSCRIPTION_ID_WITH_DATABASES>` por su identificador de suscripción de Azure. Para obtener información sobre cómo recuperar el identificador de la suscripción de Azure, consulte [Getting your Azure Subscription GUID](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/) (Obtención del GUID de la suscripción a Azure).

Si tiene varias suscripciones, puede agregarlas como una lista delimitada por comas a la propiedad "$subscriptions" del encabezado del script.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Haga clic en el botón "**Guardar**" de la esquina superior derecha para guardar el script. Cuando esté satisfecho con el script, haga clic en el botón "**Publicar**" para publicar este runbook. 

En el panel principal del runbook, puede hacer clic en el botón "**Iniciar**" para **probar** el script. Haga clic en la "**Salida**" para ver los resultados del script ejecutado. Esta salida será el contenido de su correo electrónico. La salida de ejemplo del script se puede ver en la captura de pantalla siguiente.

![Ejecutar vista de recomendaciones de ajuste automáticas con Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Asegúrese de ajustar el contenido. Para ello, personalice el script de PowerShell según sus necesidades.

Con los pasos anteriores, el script de PowerShell para recuperar las recomendaciones de ajuste automático se carga en Azure Automation. El siguiente paso consiste en automatizar y programar el trabajo de entrega de correos electrónicos.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatización de las tareas de correo electrónico con Microsoft Flow

Para completar la solución, como último paso, cree un flujo de automatización en Microsoft Flow que conste de tres acciones (trabajos): 

1. "**Azure Automation - Create job**" (Azure Automation - Crear un trabajo): se usa para ejecutar el script de PowerShell a fin de recuperar las recomendaciones de ajuste automático del runbook de Azure Automation.
2. "**Azure Automation - Get job output**" (Azure Automation - Obtener la salida de trabajo): se usa para recuperar la salida del script de PowerShell ejecutado.
3. "**Office 365 Outlook - Enviar un correo electrónico**": se usa para enviar correos electrónicos. Los correos electrónicos se envían mediante la cuenta de Office 365 de la persona que crea el flujo.

Para obtener más información sobre las funcionalidades de Microsoft Flow, consulte [Introducción a Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

El requisito previo para este paso es registrarse para obtener una cuenta de [Microsoft Flow](https://flow.microsoft.com) e iniciar sesión. Una vez dentro de la solución, siga estos pasos para configurar un **flujo nuevo**:

- Acceda al elemento de menú "**Mis flujos**".
- Dentro de Mis flujos, seleccione el vínculo "**+Crear desde cero**" en la parte superior de la página.
- Haga clic en el vínculo "**Buscar entre cientos de conectores y desencadenadores**" en la parte inferior de la página.
- En el campo de búsqueda, escriba "**Periodicidad**" y seleccione "**Programación - Periodicidad**" en los resultados de la búsqueda para programar la ejecución del trabajo de entrega de correos electrónicos.
- Desde el panel Periodicidad, en el campo Frecuencia, seleccione la frecuencia de programación para la ejecución de este flujo; por ejemplo, enviar un correo electrónico automatizado cada minuto, hora, día, semana, etc.

En el siguiente paso, debe agregar tres trabajos (crear, obtener salida y enviar correo electrónico) al flujo de periodicidad recién creado. Para agregar los trabajos necesarios al flujo, siga estos pasos:

1. Cree una acción para ejecutar el script de PowerShell para recuperar las recomendaciones de ajuste.
- Seleccione "**+ Nuevo paso**", seguido de "**Agregar una acción**" dentro del panel de flujo Periodicidad.
- En el campo de búsqueda, escriba "**automation**" y seleccione "**Azure Automation – Create job**" (Azure Automation - Crear un trabajo) en los resultados de la búsqueda.
- En el panel de trabajo Crear, configure las propiedades del trabajo. En esta configuración, necesitará los detalles del identificador de la suscripción de Azure, el grupo de recursos y la cuenta de automatización que se **registraron anteriormente** en el **panel Cuenta de automatización**. Para obtener más información acerca de las opciones disponibles en esta sección, consulte [Azure Automation – Create job](https://docs.microsoft.com/connectors/azureautomation/#Create_job) (Azure Automation - Crear un trabajo).
- Para completar la creación de esta acción, haga clic en "**Guardar flujo**".

2. Crear una acción para recuperar la salida del script de PowerShell ejecutado.
- Seleccione "**+ Nuevo paso**", seguido de "**Agregar una acción**" dentro del panel de flujo Periodicidad.
- En el campo de búsqueda, escriba "**automation**" y seleccione "**Azure Automation – Get job output**" (Azure Automation - Obtener la salida de trabajo) en los resultados de la búsqueda. Para obtener más información acerca de las opciones disponibles en esta sección, consulte [Azure Automation – Get job output](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output)" (Azure Automation - Obtener la salida de trabajo).
- Rellene los campos obligatorios (como cuando creó el trabajo anterior): rellene su identificador de suscripción de Azure, el grupo de recursos y la cuenta de automatización (tal y como se indicó en el panel de la cuenta de automatización).
- Haga clic en el campo "**Id. de trabajo**" para que se muestre el menú "**Contenido dinámico**". En este menú, seleccione la opción "**Id. de trabajo**".
- Para completar la creación de esta acción, haga clic en "**Guardar flujo**".

3. Cree una acción para enviar correos electrónicos mediante la integración de Office 365.
- Seleccione "**+ Nuevo paso**", seguido de "**Agregar una acción**" dentro del panel de flujo Periodicidad.
- En el campo de búsqueda, escriba "**enviar un correo electrónico**" y seleccione "**Office 365 Outlook - Enviar un correo electrónico**" en los resultados de la búsqueda
- En el campo "**Para**", escriba la dirección de correo electrónico a la que debe enviar el correo electrónico de notificación.
- En el campo "**Asunto**", escriba el asunto del correo electrónico; por ejemplo, "Notificación por correo electrónico de las recomendaciones de ajuste automático".
- Haga clic en el campo "**Cuerpo**" para que se muestre el menú "**Contenido dinámico**". Desde dentro de este menú, en "**Get job output**" (Obtener salida de trabajo), seleccione "**Contenido**". 
- Para completar la creación de esta acción, haga clic en "**Guardar flujo**".

> [!TIP]
> Para enviar correos electrónicos automatizados a varios destinatarios, cree flujos independientes. En estos flujos adicionales, cambie la dirección de correo electrónico del destinatario del campo "A" y la línea de asunto del correo electrónico del campo "Asunto". La creación de runbooks nuevos en Azure Automation con scripts de PowerShell personalizados (por ejemplo, con cambio de identificador de suscripción de Azure) permite personalizar mejor los escenarios automatizados, como, por ejemplo, el envío de correos electrónicos sobre recomendaciones del ajuste automático a destinatarios independientes para distintas suscripciones.
>

Los pasos anteriores proporcionan todo lo que debe hacer para configurar el flujo de trabajo de entrega de correos electrónicos. El flujo completo, que consta de tres acciones integradas, se muestra en la imagen siguiente.

![Vista del flujo de notificaciones por correo electrónico para el ajuste automático](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Para probar el flujo, haga clic en "**Ejecutar ahora**" en la esquina superior derecha del panel del flujo.

Las estadísticas de la ejecución de los trabajos automatizados, que muestran si las notificaciones por correo electrónico se enviaron correctamente, se pueden ver desde el panel de análisis de Flow.

![Flujo de ejecución para las notificaciones por correo electrónico del ajuste automático](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

El análisis de Flow resulta útil para supervisar el éxito de las ejecuciones de trabajo y, si es necesario, para solucionar problemas.  En el caso de la solución de problemas, también puede examinar el registro de ejecución del script de PowerShell accesible a través de la aplicación Azure Automation.

El resultado final del correo electrónico automatizado tiene un aspecto similar al siguiente correo electrónico recibido después de compilar y ejecutar esta solución:

![Salida de correo electrónico de ejemplo de notificaciones por correo electrónico de los ajustes automáticos](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Mediante el ajuste del script de PowerShell, puede ajustar la salida y el formato del correo electrónico automatizado según sus necesidades.

También puede personalizar aún más la solución para compilar notificaciones por correo electrónico en función de un evento de ajuste específico, y para varios destinatarios, suscripciones o bases de datos, según sus escenarios personalizados. 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre cómo el ajuste automático puede ayudarle a mejorar el rendimiento de la base de datos, consulte [Ajuste automático en Azure SQL Database](sql-database-automatic-tuning.md).
- Para habilitar la característica de ajuste automático en Azure SQL Database para administrar la carga de trabajo, vea [Habilitación del ajuste automático](sql-database-automatic-tuning-enable.md).
- Para revisar manualmente y aplicar las recomendaciones de ajuste automático, vea [Búsqueda y aplicación de recomendaciones de rendimiento](sql-database-advisor-portal.md).
