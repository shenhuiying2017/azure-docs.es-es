<properties 
   pageTitle="Conceptos de runbooks de Automatización de Azure"
   description="Describe los conceptos básicos que debería comprender para la creación de runbooks en Automatización de Azure."
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Conceptos de runbooks de Automatización de Azure

Los runbooks de Automatización de Azure se implementan como flujos de trabajo de Windows PowerShell. Esta sección proporciona una breve introducción de las características más importantes de flujos de trabajo que son comunes a los runbooks de Automatización. Se puede consultar la información detallada sobre los flujos de trabajo en [Introducción a los flujos de trabajo de Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).


## Flujos de trabajo de Windows PowerShell

Un flujo de trabajo es una secuencia de pasos programados y conectados que realizan tareas de larga duración o requieren la coordinación de varios pasos en varios dispositivos o nodos administrados. Las ventajas de un flujo de trabajo en un script normal incluyen la capacidad de realizar una acción en varios dispositivos simultáneamente y la capacidad de recuperarse automáticamente de los errores. Un flujo de trabajo de Windows PowerShell es un script de Windows PowerShell que se aprovecha de Windows Workflow Foundation. Aunque el flujo de trabajo está escrito con sintaxis de Windows PowerShell y se inicia mediante Windows PowerShell, se procesa mediante Windows Workflow Foundation.

### Estructura básica

Un flujo de trabajo de Windows PowerShell se inicia con la palabra clave **Workflow**, seguida del cuerpo del script entre llaves. El nombre del flujo de trabajo sigue a la palabra clave **Workflow**, tal como se muestra en la sintaxis siguiente. El nombre del flujo de trabajo coincide con el nombre del runbook de Automatización.

    Workflow Test-Runbook
    {
       <Commands>
    }

Para agregar parámetros al flujo de trabajo, use la palabra clave **Param**, tal como se muestra en la sintaxis siguiente. El Portal de administración pedirá al usuario que proporcione valores para estos parámetros cuando inicie el runbook. Este ejemplo utiliza el atributo Parameter opcional que especifica si el parámetro es obligatorio.

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### Nomenclatura

El nombre del flujo de trabajo debe cumplir el formato verbo-nombre estándar con Windows PowerShell. Puede consultar [Verbos aprobados para comandos de Windows PowerShell](http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx) para obtener una lista de verbos aprobados que se pueden usar. El nombre del flujo de trabajo debe coincidir con el nombre del runbook de Automatización. Si se va a importar el runbook, el nombre de archivo debe coincidir con el nombre del flujo de trabajo y debe terminar en. ps1.

### Limitaciones

Para obtener una lista completa de las limitaciones y diferencias de sintaxis entre flujos de trabajo de Windows PowerShell y Windows PowerShell, consulte [Diferencias sintácticas entre flujos de trabajo de scripts y scripts](http://technet.microsoft.com/library/jj574140.aspx).

## Actividades

Una actividad es una tarea específica de un flujo de trabajo. Al igual que un script se compone de uno o varios comandos, un flujo de trabajo se compone de una o varias actividades que se realizan en secuencia. El flujo de trabajo de Windows PowerShell convierte automáticamente muchos de los cmdlets de Windows PowerShell en actividades cuando se ejecuta un flujo de trabajo. Cuando se especifica uno de estos cmdlets en el runbook, Windows Workflow Foundation ejecuta realmente la actividad correspondiente. Para los cmdlets sin actividad correspondiente, el flujo de trabajo de Windows PowerShell ejecuta automáticamente el cmdlet dentro de una actividad [InlineScript](#inlinescript). Hay un conjunto de cmdlets que están excluidos y no se pueden usar en un flujo de trabajo a menos que incluya explícitamente en un bloque de InlineScript. Para obtener más información sobre estos conceptos, consulte [Uso de actividades en los flujos de trabajo de scripts](http://technet.microsoft.com/library/jj574194.aspx).

Las actividades de flujo de trabajo comparten un conjunto de parámetros comunes para configurar su funcionamiento. Para obtener más información acerca de los parámetros comunes del flujo de trabajo, consulte [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

## Módulos de integración

Un *módulo de integración* es un paquete que contiene un módulo de Windows PowerShell y puede importarse en Automatización de Azure. Los cmdlets de los módulos de integración que se importan en Automatización de Azure están automáticamente disponibles para todos los runbooks en la misma cuenta de Automatización. Puesto que Automatización de Azure se basa en Windows PowerShell 4.0, admite la carga automática de módulos, lo que significa que se pueden usar cmdlets desde los módulos instalados sin importarlos en el script con [Import-Module](http://technet.microsoft.com/library/hh849725.aspx).

## Ejecución en paralelo

Una ventaja de los flujos de trabajo de Windows PowerShell es la capacidad para realizar un conjunto de comandos en paralelo en lugar de hacerlo secuencialmente como con un script típico. Esto es especialmente útil en runbooks, ya que pueden realizar varias acciones que tardan mucho tiempo en completarse. Por ejemplo, un runbook puede proporcionar un conjunto de máquinas virtuales. En lugar de realizar cada proceso de aprovisionamiento de manera secuencial, las acciones se podrían realizar simultáneamente para mejorar la eficiencia general. Solo cuando todas se hayan completado, el runbook continuará.

Puede utilizar la palabra clave **Parallel** para crear un bloque de scripts con varios comandos que se ejecutarán simultáneamente. Usa la sintaxis que se muestra a continuación. En este caso, Activity1 y Activity2 se iniciarán al mismo tiempo. Activity3 se iniciará después de que se hayan completado Activity1 y Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Puede utilizar la construcción **ForEach-Parallel** para procesar comandos para cada elemento de una colección simultáneamente. Los elementos de la colección se procesan en paralelo mientras que los comandos del bloque de scripts se ejecutan secuencialmente. Usa la sintaxis que se muestra a continuación. En este caso, Activity1 se iniciará al mismo tiempo para todos los elementos de la colección. Para cada elemento, Activity2 se iniciará una vez completada Activity1. Activity3 se iniciará únicamente después de que se hayan completado Activity1 y Activity2 para todos los elementos.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

La palabra clave **Sequence** se utiliza para ejecutar comandos de manera secuencial dentro de un bloque de scripts **Parallel**. El bloque de scripts **Sequence** se ejecuta en paralelo con otros comandos, pero los comandos dentro del bloque se ejecutan secuencialmente. Usa la sintaxis que se muestra a continuación. En este caso, Activity1, Activity2 y Activity3 se iniciarán al mismo tiempo. Activity4 se iniciará únicamente después de que se haya completado Activity3. Activity5 se iniciará después de que se hayan completado todas las demás actividades.

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## Puntos de control

Un *punto de control* es una instantánea del estado actual del flujo de trabajo que incluye el valor actual de las variables y cualquier salida generada en ese punto. El último punto de control que se va a completar en un runbook se guarda en la base de datos de Automatización para que pueda reanudar el flujo de trabajo si hay un problema como una interrupción del equipo durante el tiempo de ejecución. Sin un punto de control, el flujo de trabajo podría empezar desde el principio. Los datos del punto de control se quitan una vez completado el trabajo de runbook.

Puede establecer un punto de control en un flujo de trabajo con la actividad **Checkpoint-Workflow**. Al incluir esta actividad en un runbook, se toma inmediatamente un punto de control. Si el runbook se suspende por una excepción, cuando se reanude el trabajo, se reanudará desde el último conjunto de puntos de control.

En el código de ejemplo siguiente, se produce una excepción después de Activity2 que causa la suspensión del runbook. Cuando se reanuda el trabajo, se inicia ejecutando Activity2, ya que estaba justo después del último conjunto de puntos de control.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Debe establecer los puntos de control en un runbook después de las actividades que puedan ser propensas a la excepción y no deben repetirse si se reanuda el runbook. Por ejemplo, el runbook puede crear una máquina virtual. Puede establecer un punto de control antes y después de los comandos para crear la máquina virtual. Si se produce un error en la creación, los comandos se repiten cuando se reanude el runbook. Si la creación se realiza correctamente, pero más adelante se produce un error en el runbook, la máquina virtual no se volverá a crear cuando se reanude el runbook.

Para obtener más información acerca de los puntos de control, consulte [Adición de puntos de control a un flujo de trabajo de scripts](http://technet.microsoft.com/library/jj574114.aspx).

## Suspensión de un flujo de trabajo

Puede forzar que un runbook se suspenda con la actividad **Suspend-Workflow**. Esta actividad establecerá un punto de control y hará que el flujo de trabajo se suspenda inmediatamente. La suspensión de un flujo de trabajo es útil para runbooks que pueden requerir que un paso manual se realice antes de ejecutar otro conjunto de actividades.

Para obtener más información sobre la suspensión de un flujo de trabajo, consulte [Procedimiento para hacer que un flujo de trabajo se suspenda a sí mismo](http://technet.microsoft.com/library/jj574175.aspx).

## InlineScript

La actividad **InlineScript** ejecuta un bloque de comandos de un script de PowerShell tradicional en lugar de un flujo de trabajo de PowerShell y devuelve su salida al flujo de trabajo. Mientras que los comandos de un flujo de trabajo se envían a Windows Workflow Foundation para su procesamiento, los comandos de un bloque de InlineScript se procesan mediante Windows PowerShell. La actividad usa los parámetros comunes de flujo de trabajo estándar incluidos **PSCredential**, que le permite especificar que el bloque de código se pueda ejecutar con credenciales alternativas.

InlineScript usa la sintaxis que se muestra a continuación.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Aunque las actividades de InlineScript pueden ser críticas en algunos runbooks, no son compatibles con las construcciones de flujo de trabajo y solo deben utilizarse cuando sea necesario por las razones siguientes:

- No puede usar los puntos de control desde un bloque de InlineScript. Si se produce un error dentro del bloque, se debe reanudar desde el principio del bloque.
- InlineScript afecta a la escalabilidad del runbook ya que retiene la sesión de Windows PowerShell durante todo el bloque de InlineScript.
- Actividades como Get-AutomationVariable y Get-AutomationPSCredential ya no están disponibles en un bloque de InlineScript.  

Si necesita usar un InlineScript, debe minimizar su alcance. Por ejemplo, si el runbook recorre en iteración una colección mientras aplica la misma operación a cada elemento, el bucle debería producirse fuera de InlineScript. Esto le proporcionará las siguientes ventajas:

- También puede realizar un [punto de control](#checkpoints) en el flujo de trabajo después de cada iteración. Si el trabajo se suspende o se interrumpe y se reanuda, el bucle podrá reanudarse.
- Puede usar **ForEach –Parallel** para controlar los elementos de la colección al mismo tiempo.

Tenga presente las siguientes recomendaciones si usa un InlineScript en el runbook:

- Sin embargo, puede pasar valores en el script con el modificador del alcance **$Using**. Por ejemplo, una variable denominada $abc que se ha establecido fuera del InlineScript se convertiría en $using:abc dentro un InlineScript.

- Para devolver la salida de una InlineScript, asigne la salida a una variable y envíe cualquier dato que se va a devolver a la secuencia de salida. En el ejemplo siguiente se asigna la cadena "hi" a una variable denominada $output.

	<pre><code>$output = InlineScript { Write-Output "hi" }</code></pre>

- Evite la definición de flujos de trabajo en el ámbito de InlineScript. Aunque puede parecer que algunos flujos de trabajo funcionen correctamente, no es un escenario probado. Como resultado, se pueden encontrar confusos mensajes de error o un comportamiento inesperado.

Para obtener más información acerca del uso de InlineScript, consulte [Ejecución de comandos de Windows PowerShell en un flujo de trabajo](http://technet.microsoft.com/library/jj574197.aspx) y [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## Artículos relacionados

- [Creación o importación de un runbook](http://technet.microsoft.com/library/dn919921.aspx) 

<!---HONumber=62-->