<properties 
   pageTitle="Visualización del estado de un trabajo de runbook en Automatización de Azure"
   description="Cuando se inicia un runbook en Automatización de Azure, se crea un trabajo. Este artículo proporciona información acerca de cómo realizar un seguimiento de cada trabajo y ver su estado actual."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2015"
   ms.author="bwren" />

# Visualización del estado de un trabajo de runbook en Automatización de Azure


Cuando se inicia un runbook en Automatización de Azure, se crea un trabajo. Un trabajo es una instancia única de ejecución de un runbook. Un único runbook puede tener varios trabajos, cada uno con su propio conjunto de valores para los parámetros del runbook. Hay varias maneras de comprobar el estado de un trabajo concreto y de todos los trabajos para uno o varios runbooks.

## Estados del trabajo

En la tabla siguiente se describen los diferentes estados posibles para un trabajo.

| Status| Descripción|
|:---|:---|
|Completed|El trabajo se completó correctamente.|
|Failed|El trabajo finalizó con un error.|
|Error, esperando recursos|Se produjo un error en el trabajo porque ha alcanzado el límite de [distribución equilibrada](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare) tres veces y se ha iniciado en el mismo punto de control o en el inicio del runbook cada vez.|
|En cola|El trabajo espera que los recursos en un trabajador de Automatización estén disponible para que se puede iniciar.|
|Iniciando|El trabajo se ha asignado a un trabajador y el sistema está en proceso de iniciarse.|
|Reanudando|El sistema está en proceso de reanudar el trabajo después de que se suspendió.|
|Ejecución|El trabajo se está ejecutando.|
|En ejecución, esperando recursos|El trabajo se ha descargado porque ha alcanzado el límite de [distribución equilibrada](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare). Se reanudará en breve desde su último punto de control.|
|Stopped|El trabajo lo detuvo el usuario antes de completarse.|
|Deteniéndose|El sistema está en proceso de detener el trabajo.|
|Suspended|El trabajo lo ha suspendido el usuario, el sistema o un comando en el runbook. Un trabajo suspendido se puede iniciar de nuevo y se reanudará desde su último punto de control o desde el principio del runbook si no tiene ningún punto de control. El runbook solo lo suspenderá el sistema en el caso de una excepción. De forma predeterminada, se establece ErrorActionPreference en **Continuar**, lo que significa que el trabajo seguirá ejecutándose en un error. Si se establece esta variable de preferencia en **Detener**, se suspenderá el trabajo en un error.|
|Suspendiendo|El sistema está intentando suspender el trabajo a petición del usuario. El runbook debe alcanzar su siguiente punto de control antes de que se pueda suspender. Si ya ha pasado su último punto de comprobación, se completará antes de que se pueda suspender.|

## Visualización de un estado de trabajo con el Portal de administración de Azure

### Panel de Automatización

El panel de Automatización muestra un resumen de todos los runbooks de una cuenta de Automatización particular. También incluye la información general del uso de la cuenta. El gráfico de resumen muestra el número de trabajos totales de todos los runbooks que han pasado a cada estado en un número determinado de días u horas. Puede seleccionar el intervalo de tiempo en la esquina superior derecha del gráfico. El eje de tiempo del gráfico cambiará según el tipo de intervalo de tiempo que seleccione. Puede elegir si desea mostrar la línea de un estado determinado haciendo clic en él en la parte superior de la pantalla.

Puede utilizar los pasos siguientes para mostrar el panel Automatización.

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Seleccione la pestaña **Panel**.

### Panel de runbook

El panel de runbook muestra un resumen de un único runbook. El gráfico de resumen muestra el número de trabajos totales para el runbook que ha pasado a cada estado en un número determinado de días u horas. Puede seleccionar el intervalo de tiempo en la esquina superior derecha del gráfico. El eje de tiempo del gráfico cambiará según el tipo de intervalo de tiempo que seleccione. Puede elegir si desea mostrar la línea de un estado determinado haciendo clic en él en la parte superior de la pantalla.

Puede utilizar los pasos siguientes para mostrar el panel de runbook.

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Haga clic en el nombre de un runbook.
1. Seleccione la pestaña **Panel**.

### Resumen del trabajo

Puede ver una lista de todos los trabajos que se han creado para un runbook determinado y su estado más reciente. Puede filtrar esta lista por estado del trabajo y el intervalo de fechas del último cambio del trabajo. Haga clic en el nombre de un trabajo para ver su información detallada y la salida. La vista detallada del trabajo incluye los valores para los parámetros de runbook que se proporcionaron con ese trabajo.

Puede utilizar los pasos siguientes para ver los trabajos de un runbook.

1. En el Portal de administración de Azure, seleccione **Automatización** y, a continuación, haga clic en el nombre de una cuenta de Automatización.
1. Haga clic en el nombre de un runbook.
1. Seleccione la pestaña **Trabajos**.
1. Haga clic en la columna **Trabajo creado** de un trabajo para ver sus detalles y la salida.

## Recuperación del estado del trabajo con Windows PowerShell

Puede utilizar [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) para recuperar los trabajos creados para un runbook y los detalles de un trabajo determinado. Si inicia un runbook con Windows PowerShell mediante el uso [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx), devolverá el trabajo resultante. Utilice la salida [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) para obtener la salida de un trabajo.

Los comandos de ejemplo siguientes recuperan el último trabajo para un runbook de ejemplo y muestra su estado, los valores proporcionan para los parámetros de runbook y la salida del trabajo.

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Artículos relacionados

- [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md)

<!---HONumber=58--> 