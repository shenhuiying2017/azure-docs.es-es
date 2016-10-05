<properties
    pageTitle="Automatizar la eliminación de grupos de recursos | Microsoft Azure"
    description="Versión del flujo de trabajo de PowerShell de un escenario de Azure Automation, incluidos Runbooks para quitar todos los grupos de recursos de su suscripción."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
	/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>

# Escenario de Azure Automation: automatizar la eliminación de grupos de recursos

Muchos clientes crean más de un grupo de recursos, donde algunos están dedicados a la administración de aplicaciones de producción y otros pueden encontrarse en entornos de desarrollo, pruebas y de ensayo. Automatizar la implementación de estos recursos es una cosa, pero poder dar de baja un grupo de recursos con un clic del botón es otra. El uso de Automation para controlarlo es un caso de uso y una oportunidad perfectos para simplificar esta tarea común de administración. Esto también resulta útil si está trabajando con una suscripción de Azure que tiene un límite de gasto a través de una oferta de miembro como MSDN o el programa Microsoft Partner Network Cloud Essentials, por ejemplo.

Este escenario se basa en un Runbook de PowerShell y está diseñado para quitar uno o más grupos de recursos que especifique en su suscripción. El Runbook admite la primera prueba antes de continuar, que es su valor predeterminado. De esta forma no podrá eliminarlo accidentalmente sin estar absolutamente seguro de que está preparado para completar este procedimiento.

## Obtención del escenario

Este escenario consta de un Runbook de PowerShell que puede descargar en la [Galería de PowerShell](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript) o importar directamente desde la [Galería de Runbooks](automation-runbook-gallery.md) en Azure Portal.<br><br>

Runbook | Description|
----------|------------|
Remove-ResourceGroup | Quita uno o más grupos de recursos de Azure y sus recursos de la suscripción.  
<br> Se definen los siguientes parámetros de entrada para este Runbook:

Parámetro | Description|
----------|------------|
NameFilter (obligatorio) | Permite especificar un filtro de nombre para limitar los grupos de recursos que desea eliminar. Puede pasar varios valores mediante una lista separada por comas.<br>El filtro no distingue mayúsculas de minúsculas y coincidirá con cualquier grupo de recursos que contenga la cadena.|
PreviewMode (opcional) | Ejecute el Runbook para ver qué grupos de recursos se pueden eliminar, pero no realice ninguna acción. <br>El valor predeterminado es **true** para evitar la eliminación accidental de uno o más grupos de recursos pasados al Runbook.  

## Instalación y configuración de este escenario

### Requisitos previos

Este Runbook se autentica mediante la [Cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md).

### Instalar y publicar los Runbooks

Tras descargar el Runbook, puede importarlo con el procedimiento descrito para [importar Runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation). Publique el Runbook después de que se hayan importado correctamente en la cuenta de Automation.


## Uso del Runbook

Los pasos siguientes le guiarán a través de la ejecución de este Runbook y le ayudarán a familiarizarse con su funcionamiento. Solo probaremos el Runbook de este ejemplo, sin eliminar realmente el grupo de recursos.

1. Desde el Portal de Azure, abra su cuenta de Automatización y haga clic en el icono **Runbooks**.
2. Seleccione el Runbook **Remove-ResourceGroup** y haga clic en **Iniciar**.
3. Cuando se inicia el Runbook, se abre la hoja **Iniciar runbook** y puede configurar los valores siguientes para los parámetros. Escriba el nombre de uno o más grupos de recursos de la suscripción con la que desea probarlos y no causará ningún daño si los elimina accidentalmente.<br> ![Parámetros Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] Asegúrese de que la opción **Previewmode** está establecida en **true** con el fin de evitar la eliminación de los grupos de recursos seleccionados. **Tenga en cuenta** que este Runbook no quitará el grupo de recursos que contiene la cuenta de Automation que ejecuta este Runbook.

4. Cuando haya configurado todos los valores de parámetro, haga clic en **Aceptar** y el Runbook se pondrá en cola para su ejecución.

Para ver los detalles del trabajo del Runbook **Remove-ResourceGroup** en Azure Portal, seleccione el icono **Trabajos** del Runbook. La opción de resumen del trabajo le mostrará los parámetros de entrada y el flujo de salida, además de información general sobre el trabajo y las excepciones que se hayan producido.<br> ![Estado del trabajo del Runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

El **resumen del trabajo** incluye los mensajes de los flujos de salida, advertencia y error. Seleccione el icono **Salida** para ver los resultados detallados de la ejecución del Runbook.<br> ![Resultados de la salida del Runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## Pasos siguientes

- Para empezar a crear su propio Runbook, consulte [Creación o importación de un Runbook en Azure Automation](automation-creating-importing-runbook.md).
- Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer Runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md).

<!---HONumber=AcomDC_0928_2016-->