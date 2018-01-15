---
title: "Control de errores en runbooks gráficos de Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo implementar la lógica de control de errores en runbooks gráficos de Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2016
ms.author: magoedte
ms.openlocfilehash: afe7043e31c05444dded089dc02689a3b0c94659
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Control de errores en runbooks gráficos de Azure Automation

Una entidad de seguridad clave en el diseño de runbooks que se debe tener en cuenta es identificar los distintos problemas que puede tener un runbook. Dichos problemas pueden incluir condiciones de correcto, estados de error esperado y condiciones de error inesperado.

Los runbooks deben incluir el control de errores. Para validar el resultado de una actividad o controlar un error con runbooks gráficos, se puede usar una actividad de código de Windows PowerShell, definir una lógica condicional en el vínculo de salida de la actividad o aplicar otro método.          

A menudo, aunque se produzca un error de no terminación en una actividad de un runbook, se procesarán todas las actividades posteriores, independientemente del error. Es probable que el error genere una excepción, pero se podrá ejecutar la siguiente actividad. Esta es la forma en que se ha diseñado que PowerShell controle los errores.    

Los tipos de errores de PowerShell que se pueden producir durante la ejecución provocan la terminación o no la provocan. Estas son las diferencias entre los errores de terminación y de no terminación:

* **Error de terminación**: error grave durante la ejecución que detiene el comando (o la ejecución del script) por completo. Entre los ejemplos se incluyen cmdlets inexistentes, errores de sintaxis que impiden la ejecución de un cmdlet u otros errores irrecuperables.

* **Error de no terminación**: error que no es grave, lo que permite que la ejecución no se interrumpe. Entre los ejemplos se incluyen errores operativos, como los errores de archivos no encontrados y problemas de permisos.

Los runbooks gráficos de Azure Automation se han mejorado con la funcionalidad de inclusión del control de errores. Ahora es posible convertir las excepciones en errores de no terminación y crear vínculos de error entre las actividades. Este proceso permite a los autores de runbooks detectar errores y administrar cualquier condición realizada o inesperada.  

## <a name="when-to-use-error-handling"></a>Cuándo se debe utilizar el control de errores

Siempre que haya una actividad crítica que genere un error o una excepción es importante que se procese la siguiente actividad del runbook y controlar el error adecuadamente. Esto es especialmente crítico cuando los runbooks dan soporte a un proceso de operaciones de un negocio o un servicio.

Por cada actividad que pueda generar un error, el autor del runbook puede agregar un vínculo de error que señale a cualquier otra actividad.  La actividad de destino puede ser de cualquier tipo, entre las que se incluyen actividades de código, la invocación de un cmdlet, la invocación de otro runbook, etc.

Además, la actividad de destino también puede tener vínculos salientes. Dichos vínculos pueden ser normales o de error, lo que significa que el autor del runbook puede implementar una lógica de control de errores compleja sin tener que recurrir a una actividad de código. La práctica recomendada consiste en crear un runbook específico para el control de errores con una funcionalidad común, pero no es obligatorio. La lógica de control de errores en una actividad de código de PowerShell no es la única opción.  

Por ejemplo, piense en un runbook que intenta iniciar una máquina virtual e instalar una aplicación en ella. Si la máquina virtual no se inicia correctamente, realiza dos acciones:

1. Envía una notificación acerca de este problema.
2. Inicia otro runbook que aprovisiona automáticamente una nueva máquina virtual.

Una solución es en tener un vínculo de error que apunte a una actividad que controle el paso uno. Por ejemplo, puede conectar el cmdlet **Write-Warning** a una actividad para el paso dos, como el cmdlet **AzureRmAutomationRunbook inicio**.

Este comportamiento se puede generalizar para que se pueda usar en muchos runbooks. Solo es preciso colocar estas dos actividades en un runbook de control de errores independiente y seguir las instrucciones anteriores. Antes de llamar a este runbook de control de errores se puede construir un mensaje personalizado a partir de los datos del runbook original y, después, pasarlo como parámetro al runbook de control de errores.

## <a name="how-to-use-error-handling"></a>Uso del control de errores

Todas las actividades tienen una opción de configuración que convierte las excepciones en errores que no provocan la terminación. De manera predeterminada esta opción está deshabilitada. Se recomienda habilitar esta opción en todas aquellas actividades en las que se desee controlar los errores.  

Al habilitar esta configuración, se garantiza que tanto los errores de la actividad que provocan terminación como los que no la provocan se tratan como errores que no la provocan y se pueden controlar con un vínculo de error.  

Tras configurar esta opción, cree una actividad que controle el error. Si una actividad produce algún error, se siguen los vínculos de error de salida, no los vínculos normales, aunque la actividad genere también una salida normal.<br><br> ![Ejemplo de vínculo de error de runbook de Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

En el ejemplo siguiente, un runbook recupera una variable que contiene el nombre del equipo de una máquina virtual. A continuación, intenta iniciar la máquina virtual con la siguiente actividad.<br><br> ![Ejemplo de control de errores de runbook de Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Las actividades **Get-AutomationVariable** y **Start-AzureRmVm** están configuradas para convertir excepciones en errores.  Si hay problemas para obtener la variable o para iniciar la máquina virtual, se generan errores.<br><br> ![Configuración de la actividad de control de errores de runbook de Automation](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Fluyen vínculos de errores de flujo de estas actividades a una única actividad de **administración de errores** (una actividad de código). Esta actividad se configura con una expresión simple de PowerShell que utiliza la palabra clave *Throw* para detener el procesamiento, junto con *$Error.Exception.Message* para obtener el mensaje que describe la excepción actual.<br><br> ![Ejemplo de código de control de errores de runbook de Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>pasos siguientes

* Para más información acerca de los vínculos y tipos de vínculo de los runbooks gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md).
