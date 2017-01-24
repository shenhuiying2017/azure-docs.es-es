---
title: "Control de errores en runbooks gráfico de Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo implementar la lógica de control de errores en runbooks gráficos de Azure Automation."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Control de errores en runbooks gráfico de Azure Automation

Una entidad de seguridad de diseño de runbook clave que se debe tener en cuenta es identificar los diferentes problemas que puede experimentar un runbook como correcto, estados de error esperado y condiciones de error inesperado.  Los runbooks debe incluir el control de errores que se detectan en consecuencia.  Con los runbooks gráficos, si deseara para validar el resultado de una actividad o controlar un error de una manera adecuada, podría seguir con una actividad de código de PowerShell, definir la lógica condicional en el vínculo de salida de la actividad o aplicar otro método.          

A menudo, cuando se ejecutan los runbooks, si hay un error que no implica la terminación que se produce con una actividad, cualquier actividad posterior se procesará en cualquier caso.  Por supuesto, es probable que genere una excepción, pero se permitirá la ejecución de la siguiente actividad. La razón de este comportamiento es el diseño de del lenguaje de PowerShell para controlar los errores.    

Los tipos de errores de PowerShell que se pueden producir durante la ejecución provocan la terminación o no la provocan.  Estas son las diferencias:

* Error que provoca terminación: un error grave durante la ejecución que detiene el comando (o la ejecución del script) por completo. Algunos ejemplos pueden incluir cmdlets inexistente, errores de sintaxis que impiden la ejecución de un cmdlet u otros errores irrecuperables.

* Error que no provoca terminación: un error que no es grave, por lo que la ejecución no se interrumpe. Algunos ejemplos incluyen errores operativos como que no se encuentre un archivo, problemas de permisos, etc.

La mejora de los runbooks gráficos de Azure Automation les permite controlar errores, por lo que ahora es posible convertir las excepciones en errores que no provocan terminación y crear vínculos de error entre las actividades. Esto permite a los autores de runbooks detectar errores y tener una ruta de acceso para administrar la condición realizada o inesperada.  

## <a name="when-to-use"></a>Cuándo se deben usar

El control de la ejecución del flujo de trabajo es importante para garantizar que siempre que haya una actividad crítica que genere un error o una excepción se pueda evitar el paso a la siguiente actividad del runbook y controlar el error adecuadamente.  Esto es especialmente necesario cuando los runbooks admiten el proceso de operaciones de un negocio o un servicio.

Por cada actividad que pueda generar un error, el autor del runbook puede agregar un vínculo de error que señale a cualquier otra actividad.  La actividad de destino puede ser de cualquier tipo: actividad de código, la invocación de un cmdlet, la invocación de otro runbook o de cualquier otra cosa. 

Además, la actividad de destino también puede tener vínculos de salida, que pueden ser vínculos regulares o de error, por lo que el autor del runbook puede implementar una lógica de control de errores compleja sin tener que recurrir a la inclusión de una actividad de código.  Aunque se recomienda crear un runbook dedicado al control de errores con una funcionalidad común, no es obligatorio y la lógica de control de errores en una actividad del código de PowerShell no es la única alternativa.  

Por ejemplo, piense en un runbook que intenta iniciar una máquina virtual e instalar una aplicación en ella, pero si la máquina virtual no se inicia correctamente, realiza dos acciones: 

1. Enviar una notificación acerca de este problema.
2. Iniciar otro runbook que aprovisiona automáticamente una nueva máquina virtual. 

Una solución sería tener un vínculo de error que señale a una actividad para controlar el paso 1, como el cmdlet **Write-Warning**, conectado a una actividad para el paso 2, como el cmdlet **Start-AzureRmAutomationRunbook**. 

También puede generalizar este comportamiento para que se pueda usar en muchos runbooks y colocar estas dos actividades en un runbook de control de errores independiente, para lo que se seguirían las instrucciones ya sugeridas.  Antes de llamar a este runbook de control de errores se puede construir un mensaje personalizado a partir de los datos del runbook original y, después, pasarlo como un parámetro al runbook de control de errores. 

## <a name="how-to-use"></a>Modo de uso

Todas las actividades tienen una configuración que permite convertir las excepciones en errores que no provocan la terminación. De manera predeterminada esta opción está deshabilitada.  Esta opción debe habilitarse en todas aquellas actividades en las que se desee controlar los errores.  Al habilitar su configuración, se garantiza que tanto los errores de la actividad que provocan terminación como los que no la provocan se tratarán como errores que no la provocan y se pueden controlar con un vínculo de error.  Después de configurar esta opción, cree las actividades que van a controlar el error.  Si una actividad produce algún error, se seguirán los vínculos de error de salida, no los vínculos normales, aunque la actividad haya generado también una salida normal.<br><br> ![Ejemplo de vínculo de error de runbook de Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

En este sencillo ejemplo, hay un runbook que recupera una variable que contiene el nombre del equipo de una máquina virtual y, después, intenta iniciar la máquina virtual con la siguiente actividad.<br><br> ![Ejemplo de control de errores de runbook de Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Las actividades **Get-AutomationVariable** y **Start-AzureRmVm** están configuradas para convertir excepciones en errores.  Si hay problemas para obtener la variable o para iniciar la máquina virtual, se generarán errores.<br><br> ![Configuración de la actividad de control de errores de runbook de Automation](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Los vínculos de error fluyen desde estas actividades a una sola actividad de **administración de errores** (una actividad de código), que se configura con una expresión de PowerShell sencilla, para lo que se utiliza la palabra clave *Throw* para detener el procesamiento, junto con *$Error.Exception.Message* para obtener el mensaje que describe la excepción actual.<br><br> ![Ejemplo de código de control de errores de runbook de Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de los vínculos y para conocer los tipos de vínculo de los runbooks gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


