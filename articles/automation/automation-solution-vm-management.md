---
title: "Solución Start/Stop VMs during off-hours | Microsoft Docs"
description: "Las soluciones de administración de máquinas virtuales inician y detienen Azure Virtual Machines Resource Manager según una programación y supervisan de forma proactiva la información de Log Analytics."
services: automation
documentationCenter: 
authors: georgewallace
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: e6f1189b9729c57718a5cd6d6f6a583b94f6f142
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Solución Start/Stop VMs during off-hours en Azure Automation

La solución Start/Stop VMs during off-hours (inicio o detención de máquinas virtuales durante las horas de poca actividad) inicia y detiene las virtuales máquinas de Azure según programaciones definidas por el usuario, proporciona información a través de Log Analytics y envía mensajes de correo electrónico opcionales aprovechando [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Admite tanto máquinas virtuales clásicas como Azure Resource Manager para la mayoría de los escenarios. 

Esta solución proporciona una funcionalidad de automatización descentralizada para los clientes que desean reducir los costos aprovechando los recursos de bajo precio sin servidor. Características incluidas:

* Programación de las máquinas virtuales para iniciarse o detenerse
* Programación de las máquinas virtuales para iniciarse o detenerse en orden ascendente mediante etiquetas de Azure (no se admite para las máquinas virtuales clásicas)
* Detención automática de las máquinas virtuales en función de la baja utilización de la CPU

## <a name="prerequisites"></a>Requisitos previos

- Los Runbooks funciona con una [cuenta de ejecución de Azure](automation-offering-get-started.md#authentication-methods).  La cuenta de ejecución es el método de autenticación preferido puesto que emplea autenticación de certificados en lugar de una contraseña que puede caducar o cambiar con frecuencia.  

- Esta solución solo puede administrar VM que se encuentren en la misma suscripción que la cuenta de Automation.  

- Esta solución se implementa únicamente en las siguientes regiones de Azure: Sudeste de Australia, Centro de Canadá, Centro de la India, Este de EE. UU., Japón Oriental, Sudeste Asiático, Sur de Reino Unido y Europa Occidental.  
    
    > [!NOTE]
    > Los runbooks que administran la programación de las máquinas virtuales pueden dirigirse a las de cualquier región.  

- Para enviar notificaciones por correo electrónico cuando se completen los runbooks de inicio y detención de máquinas virtuales, durante la incorporación desde Azure Marketplace, debe seleccionar **Sí** para implementar SendGrid. 

    > [!IMPORTANT]
    > SendGrid es un servicio de terceros, para permitir su uso, póngase en contacto con [SendGrid](https://sendgrid.com/contact/).  
    >
   
    Las limitaciones de SendGrid son las siguientes:

    * Una cuenta de SendGrid como máximo por usuario y suscripción
    * Dos cuentas de SendGrid como máximo por suscripción

Si ha implementado una versión anterior de esta solución, primero deberá eliminarla de la cuenta antes de implementar esta.  

## <a name="solution-components"></a>Componentes de soluciones

Esta solución incluye runbooks preconfigurados, programaciones e integración con Log Analytics que le permiten personalizar el inicio y el apagado de las máquinas virtuales para adaptarlas a las necesidades de su empresa. 

### <a name="runbooks"></a>Runbooks

En la tabla siguiente se enumeran los runbooks implementados en su cuenta de Automation.  Se recomienda no realizar cambios en el código del runbook, sino escribir su propio runbook con la nueva funcionalidad.

> [!NOTE]
> No se ejecuta directamente ningún runbook con el nombre "Child" anexo al final de su nombre.
>

Todos los runbooks primarios incluyen el parámetro *WhatIf*, que, cuando se establece en **True**, permiten detallar el comportamiento exacto que el runbook adopta cuando se ejecuta sin el parámetro *WhatIf* y valida las máquinas virtuales correctas a las que se destina.  Los runbooks solo realizan las acciones definidas cuando el parámetro *WhatIf* está establecido en **False**. 

|**Runbook** | **Parámetros** | **Descripción**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Se llama solo desde el runbook primario. Crea alertas según el recurso para el escenario de AutoStop.| 
|AutoStop_CreateAlert_Parent | WhatIf: True o False <br> VMList | Crea o actualiza las reglas de alerta de Azure en las máquinas virtuales de los grupos de recursos o la suscripción de destino. <br> VMList: lista de máquinas virtuales de separadas por comas.  Por ejemplo, *vm1,vm2,vm3*| 
|AutoStop_Disable | Ninguna | Se deshabilitan la programación predeterminada y las alertas AutoStop.| 
|AutoStop_StopVM_Child | WebHookData | Se llama solo desde el runbook primario. Las reglas de alerta llaman a este runbook, que realiza el trabajo de detener la máquina virtual.|  
|Bootstrap_Main | Ninguna | Se usa una vez para las configuraciones de arranque de la instalación como webhookURI que normalmente no es accesible desde Azure Resource Manager. Este runbook se quitará automáticamente si la implementación se ha realizado de modo correcto.|  
|ScheduledStartStop_Child | VMName <br> Acción: iniciar o detener <br> ResourceGroupName | Se llama solo desde el runbook primario. Realiza la ejecución real de la detención o el inicio para la detención programada.|  
|ScheduledStartStop_Parent | Acción: iniciar o detener <br> WhatIf: True o False | Esta operación surtirá efecto en todas las máquinas virtuales en la suscripción a menos que modifique **External_Start_ResourceGroupNames** y **External_Stop_ResourceGroupNames**, lo que la restringirá para que se ejecute solo en estos grupos de recursos de destino. También puede excluir máquinas virtuales específicas si actualiza la variable **External_ExcludeVMNames**. WhatIf se comporta igual que en otros runbooks.|  
|SequencedStartStop_Parent | Acción: iniciar o detener <br> WhatIf: True o False | Cree una etiqueta denominada **SequenceStart** y otra denominada **SequenceStop** en cada máquina virtual para la que desee secuenciar la actividad de inicio\\detención. El valor de la etiqueta debe ser un entero positivo (1,2,3) que se corresponda con el orden en que el inicio\\detención se realiza de forma ascendente. WhatIf se comporta igual que en otros runbooks. <br> **Nota: Las máquinas virtuales deben estar dentro de los grupos de recursos definidos External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames y External_ExcludeVMNames, en las variables de Azure Automation, y tener las etiquetas apropiadas para que las acciones surtan efecto.**|

### <a name="variables"></a>variables

En la tabla siguiente se enumeran las variables creadas en su cuenta de Automation.  Se recomienda que solo modifique las variables con el prefijo **External**; si se modifican las variables con el prefijo **Internal**, se producirán efectos no deseados.  

|**Variable** | **Descripción**|
---------|------------|
|External_AutoStop_Condition | Este es el operador condicional necesario para configurar la condición antes de desencadenar una alerta. Los valores aceptables son **GreaterThan**, **GreaterThanOrEqual**, **LessThan** y **LessThanOrEqual**.|  
|External_AutoStop_Description | Alerta para detener la máquina virtual si el porcentaje de CPU supera el umbral.|  
|External_AutoStop_MetricName | Nombre de la métrica de rendimiento de la regla de Azure Alert para la que se configurará.| 
|External_AutoStop_Threshold | Umbral para la regla de Azure Alert especificada en la variable *External_AutoStop_MetricName*. Los valores de porcentaje pueden oscilar entre 1 y 100.|  
|External_AutoStop_TimeAggregationOperator | Operador de agregación de tiempo que se aplicará al tamaño de la ventana seleccionada para evaluar la condición. Los valores aceptables son **Average**, **Minimum**, **Maximum**, **Total** y **Last**.|  
|External_AutoStop_TimeWindow | Tamaño de la ventana en la que Azure analizará la métrica seleccionada para desencadenar una alerta. Este parámetro acepta la entrada en formato timespan. Los valores posibles son de cinco minutos a seis horas.|  
|External_EmailFromAddress | Especifica el remitente del correo electrónico.|  
|External_EmailSubject | Especifica el texto de la línea de asunto del correo electrónico.|  
|External_EmailToAddress | Especifica los destinatarios del correo electrónico. Separe los nombres con una coma.|  
|External_ExcludeVMNames | Escriba los nombres de máquina virtual que desea excluir, separándolos con una coma sin espacios en blanco.|  
|External_IsSendEmail | Especifica la opción de enviar la notificación de correo electrónico cuando se complete.  Especifique **Si** o **No** para no enviar correo electrónico.  Esta opción debe ser **No** si no ha creado SendGrid durante la implementación inicial.|  
|External_Start_ResourceGroupNames | Especifica uno o varios grupos de recursos, separando los valores con una coma, destinados a las acciones de inicio.|  
|External_Stop_ResourceGroupNames | Especifica uno o varios grupos de recursos, separando los valores con una coma, destinados a las acciones de detención.|  
|Internal_AutomationAccountName | Especifica el nombre de la cuenta de Automation.|  
|Internal_AutoSnooze_WebhookUri | Especifica el URI de Webhook llamado para el escenario de AutoStop.|  
|Internal_AzureSubscriptionId | Especifica el identificador de la suscripción de Azure.|  
|Internal_ResourceGroupName | Especifica el nombre el grupo de recursos de la cuenta de Azure Automation.|  
|Internal_SendGridAccountName | Especifica el nombre de cuenta de SendGrid.|  
|Internal_SendGridPassword | Especifica la contraseña de la cuenta de SendGrid.|  

<br>

En todos los escenarios, las variables **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** y **External_ExcludeVMNames** son necesarias para dirigirse a las máquinas virtuales, aunque hay que proporcionar una lista separada por comas de las máquinas virtuales para el runbook **AutoStop_CreateAlert_Parent**. Es decir, las máquinas virtuales deben residir en grupos de recursos de destino para que las acciones de iniciar o detener tengan lugar. La lógica funciona un poco como la directiva de Azure, en el sentido de que se puede tener como destino en la suscripción o el grupo de recursos y hay acciones heredadas por las máquinas virtuales recién creadas. Este enfoque evita tener que mantener una programación independiente para cada máquina virtual y administrar el inicio o la detención en escala.

### <a name="schedules"></a>Programaciones

En la tabla siguiente se enumera cada una de las programaciones predeterminadas que se crean en su cuenta de Automation.  Puede modificarlas o crear sus propias programaciones personalizadas.  De forma predeterminada, cada una de estas programaciones se deshabilitan excepto **Scheduled_StartVM** y **Scheduled-StopVM**.

No se recomienda habilitar todas las programaciones, ya que podría crear una superposición en la programación que realiza una acción.  En su lugar, sería mejor determinar qué optimizaciones desea realizar y modificarlas en consecuencia.  Vea los escenarios de ejemplo en la sección de información general para obtener una explicación.   

|**ScheduleName** | **Frecuencia** | **Descripción**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Se ejecuta cada ocho horas | Ejecuta el runbook AutoStop_CreateAlert_Parent cada ocho horas, que a su vez detendrá los valores basados en la máquina virtual en "External_Start_ResourceGroupNames", "External_Stop_ResourceGroupNames" y "External_ExcludeVMNames" en las variables de Azure Automation.  Como alternativa, puede especificar una lista separada por comas de las máquinas virtuales mediante el parámetro "VMList".|  
|Scheduled_StopVM | Definido por el usuario, todos los días | Ejecuta el runbook Scheduled_Parent con un parámetro de "Stop" cada día a la hora determinada.  Se detendrán automáticamente todas las máquinas virtuales que cumplan las reglas definidas a través de las variables de recurso.  Se recomienda habilitar la programación hermana, Scheduled-StartVM.|  
|Scheduled_StartVM | Definido por el usuario, todos los días | Ejecuta el runbook Scheduled_Parent con un parámetro de *Stop* cada día a la hora determinada.  Se iniciarán automáticamente todas las máquinas virtuales que cumplan las reglas definidas y parte de las variables adecuadas.  Se recomienda habilitar la programación hermana, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1:00 a. m. (hora UTC), todos los viernes | Ejecuta el runbook Sequenced_Parent con un parámetro de *Stop* cada viernes a la hora determinada.  Detendrá secuencialmente (de forma ascendente) todas las máquinas virtuales con una etiqueta de **SequenceStop** definida con las variables adecuadas.  Consulte la sección de Runbooks para obtener más detalles sobre los valores de etiqueta y las variables de recurso.  Se recomienda habilitar la programación hermana, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 p. m. (hora UTC), todos los lunes | Ejecuta el runbook Sequenced_Parent con un parámetro de *Start* cada lunes a la hora determinada.  Detendrá secuencialmente (de forma descendente) todas las máquinas virtuales con una etiqueta de **SequenceStop** definida y con las variables adecuadas.  Consulte la sección de Runbooks para obtener más detalles sobre los valores de etiqueta y las variables de recurso.  Se recomienda habilitar la programación hermana, **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Configuración

Realice los pasos siguientes para agregar la solución de inicio y detención de máquinas virtuales fuera de las horas de trabajo [versión preliminar] a la cuenta de Automation y luego configure las variables para personalizar la solución.

1. En Azure Portal, haga clic en **Nuevo**.<br> ![Portal de Azure](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. En el panel de Marketplace, escriba **Iniciar VM**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Start/Stop VMs during off-hours [Preview]** (Iniciar o detener máquinas virtuales fuera de las horas de trabajo [versión preliminar]) desde los resultados de la búsqueda.  
3. En el panel **Start/Stop VMs during off-hours [Preview]** (Iniciar o detener máquinas virtuales fuera de las horas de trabajo [versión preliminar]), revise la información de resumen y luego haga clic en **Crear**.  
4. Aparece el panel **Agregar solución** donde se le pide que configure la solución antes de importarla en la suscripción de Automation.<br><br> ![Hoja Agregar solución de administración de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  En la hoja **Agregar solución**, seleccione **Área de trabajo** y aquí seleccione un área de trabajo de OMS que esté vinculada a la misma suscripción de Azure en la que se encuentra la cuenta de Automation o cree una nueva.  Si no tiene un área de trabajo, puede seleccionar **Crear área de trabajo nueva** y en el panel **OMS Workspace** (Área de trabajo de OMS) realizar estos pasos: 
   - Especifique un nombre para el nuevo **espacio de trabajo de OMS**.
   - Seleccione una **suscripción** a la que vincularlo en la lista desplegable si la opción predeterminada seleccionada no es adecuada.
   - Para **Grupo de recursos**, puede crear un nuevo grupo de recursos o seleccionar uno existente.  
   - Seleccione una **ubicación**.  Actualmente las únicas ubicaciones que se pueden seleccionar son: **Sudeste de Australia**, **Centro de Canadá**, **Centro de la India**, **Este de EE. UU.**, **Japón Oriental**, **Sudeste Asiático**, **Sur de Reino Unido** y **Europa Occidental**.
   - Seleccione un **plan de tarifa**.  La solución se ofrece en dos niveles: gratuito y nivel de pago de OMS.  El nivel gratis tiene un límite sobre la cantidad de datos recopilados diariamente, el período de retención y los minutos de tiempo de ejecución del trabajo de Runbook.  El nivel de pago de OMS no tiene ningún límite sobre la cantidad de datos recopilados a diario.  

        > [!NOTE]
        > Aunque el nivel de pago por GB (Independiente) se muestra como opción, no es aplicable.  Si lo selecciona y continúa con la creación de esta solución en su suscripción, se producirá un error.  Esto se solucionará cuando se lance oficialmente la solución.<br>Si usa esta solución, solo se emplearán minutos de trabajo e ingesta de registros de automatización.  La solución no agrega nodos OMS adicionales a su entorno.  

6. Después de proporcionar la información necesaria en la hoja **OMS workspace** (Espacio de trabajo de OMS), haga clic en **Crear**.  Mientras se comprueba la información y se crea el espacio de trabajo, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú.  Volverá a la hoja **Agregar solución**.  
7. En la hoja **Agregar solución**, seleccione **Cuenta de Automation**.  Si va a crear un nuevo espacio de trabajo de OMS, será necesario crear también una nueva cuenta de Automation que se asociará con el nuevo espacio de trabajo de OMS especificado anteriormente, lo que incluye la suscripción, el grupo de recursos y la región de Azure.  Puede seleccionar **Crear una cuenta de Automation** y, en la hoja **Agregar cuenta de Automation**, proporcionar la siguiente información: 
  - En el campo **Nombre**, escriba el nombre de la cuenta de Automation.

    Todas las demás opciones se rellenan automáticamente en función del espacio de trabajo de OMS seleccionado y no se pueden modificar.  Una cuenta de ejecución de Azure es el método de autenticación predeterminado para los Runbooks incluidos en esta solución.  Después de hacer clic en **Aceptar**, se validan las opciones de configuración y se crea la cuenta de Automation.  Puede realizar un seguimiento de su progreso en **Notificaciones** en el menú. 

    Si no, puede seleccionar una cuenta de ejecución de Automation existente.  Observe que la cuenta seleccionada no puede estar vinculada ya a otro espacio de trabajo de OMS, si no aparecerá un mensaje en la hoja para informarle al respecto.  Si ya está vinculada, deberá seleccionar una cuenta de ejecución de Automation diferente o crear una nueva.<br><br> ![Cuenta de Automation ya vinculada al espacio de trabajo de OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Por último, en la hoja **Agregar solución**, seleccione **Configuración** Aparece la hoja **Parámetros**.<br><br> ![Panel de parámetros para la solución](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  En la hoja **Parámetros**, se le pide que:  
   - Especifique los **nombres de grupo de recursos de destino**, que es un nombre de grupo de recursos que contiene las máquinas virtuales que se administrarán mediante esta solución.  Puede especificar más de un nombre y separarlos con una coma (los valores no distinguen mayúsculas de minúsculas).  Se puede usar un carácter comodín si quiere dirigirse a las máquinas virtuales de todos los grupos de recursos de la suscripción.
   - Especifique la **lista de exclusiones de máquinas virtuales (cadena)**, que es el nombre de una o varias máquinas virtuales del grupo de recursos de destino.  Puede especificar más de un nombre y separarlos con una coma (los valores no distinguen mayúsculas de minúsculas).  Se admite el uso de un carácter comodín.
   - Seleccione una **programación** que sea una fecha y una hora periódicas para iniciar y detener las máquinas virtuales en los grupos de recursos de destino.  De forma predeterminada, la programación se configura en la zona horaria UTC y no se podrá seleccionar una región diferente.  Si desea volver a configurar la programación de la zona horaria concreta después de configurar la solución, consulte el siguiente artículo [Modificación de la programación de inicio y apagado](#modifying-the-startup-and-shutdown-schedule).
   - Para recibir **notificaciones por correo electrónico** de SendGrid, acepte el valor predeterminado **Sí** y proporcione una dirección de correo electrónico válida.  Si selecciona **No** y más adelante decide que desea recibir notificaciones por correo electrónico, deberá volver a implementar la solución de nuevo desde Azure Marketplace.  

10. Cuando haya completado la configuración de los valores iniciales necesarios para la solución, seleccione **Crear**.  Todas las configuraciones se validarán y se intentará implementar la solución en su suscripción.  Este proceso puede tardar varios segundos en completarse y puede realizar el seguimiento de su progreso en **Notificaciones** en el menú. 

## <a name="collection-frequency"></a>Frecuencia de recopilación

El trabajo de automatización y los datos de transmisión del trabajo se ingieren en el repositorio de Log Analytics cada cinco minutos.  

## <a name="using-the-solution"></a>Uso de la solución

Al agregar la solución VM Management, en el área de trabajo de Log Analytics desde Azure Portal, se agrega el icono **StartStopVM View** (Vista de StartStopVM) al panel.  Este icono muestra una representación numérica y gráfica de los trabajos de runbook de la solución que se han iniciado y han finalizado correctamente.<br><br> ![Icono de StartStopVM View](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png) (Ver StartStopVM) de administración de VM  

En su cuenta de Automation, puede tener acceso a la solución y administrarla seleccionando la opción **Área de trabajo** y, en la página de Log Analytics, **Soluciones** en el panel izquierdo.  En la página **Soluciones**, seleccione la solución **Start-Stop-VM[Workspace]** en la lista.<br><br> ![Lista de soluciones en Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Al seleccionar la solución, se mostrará la hoja de la solución **Start-Stop-VM[Workspace]**, donde, al igual que en su área de trabajo de Log Analytics, puede revisar detalles importantes como el icono **StartStopVM**, que muestra una representación numérica y gráfica de los trabajos de runbook de la solución que se han iniciado y se han completado correctamente.<br><br> ![Página de la solución Update Management de Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Desde aquí también puede realizar más análisis de los registros de trabajos haciendo clic en el icono de anillo y desde el panel de la solución muestra el historial de trabajos, las consultas de búsqueda de registros predefinidas y cambiar al portal de análisis avanzado de Log Analytics para buscar en función de las consultas de búsqueda.  

Todos los runbooks primarios incluyen el parámetro *WhatIf*, que, cuando se establece en **True**, permiten detallar el comportamiento exacto que el runbook adopta cuando se ejecuta sin el parámetro *WhatIf* y valida las máquinas virtuales correctas a las que se destina.  Los runbooks solo realizan las acciones definidas cuando el parámetro *WhatIf* está establecido en **False**.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>Escenario 1: detención e inicio diarios de máquinas virtuales a través de una suscripción o grupos de recursos de destino 

Se trata de la configuración predeterminada cuando se implementa la solución por primera vez.  Por ejemplo, puede configurarla para detener todas las máquinas virtuales a través de una suscripción por la noche cuando salga del trabajo e iniciarlas por la mañana cuando esté de vuelta en la oficina. Al configurar las programaciones **Scheduled-StartVM" y **Scheduled-StopVM** durante la implementación, inician y detienen las máquinas virtuales de destino.
>[!NOTE]
>La zona horaria es su zona horaria actual cuando configura el parámetro de hora de programación; sin embargo, se almacena en formato UTC en Azure Automation.  No es necesario realizar ninguna conversión de zona horaria ya que se controla durante la implementación.

Controle que las máquinas virtuales están en el ámbito configurando las variables **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames y **External_ExcludeVMNames**.  

>[!NOTE]
> El valor de la variable **Target ResourceGroup Names**" se almacena como el valor tanto para la variable **External_Start_ResourceGroupNames** como para **External_Stop_ResourceGroupNames**. A fin de lograr un mayor detalle, puede modificar cada una de estas variables para distintos grupos de recursos de destino.  Para la acción de inicio, use **External_Start_ResourceGroupNames** y, para la acción de detención, use **External_Stop_ResourceGroupNames** en su lugar. Las máquinas virtuales nuevas se agregan automáticamente a las programaciones de inicio y detención.

Para probar y validar la configuración, inicie manualmente el runbook **ScheduledStartStop_Parent** y establezca el parámetro *ACTION* en **start** o en **stop**, y el parámetro *WhatIf* en **true**.<br><br> ![Configuración de los parámetros del runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Le permite obtener una vista previa de la acción que tendría lugar y realizar los cambios según sea necesario antes de implementarse en las máquinas virtuales de producción.  Una vez que esté familiarizado, puede ejecutar manualmente el runbook con el parámetro establecido en **false** o dejar que la programación de Automation **Schedule-StartVM** y **Schedule-StopVM** se ejecute automáticamente siguiendo su programación prescrita.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>Escenario 2: Secuencia de la detención o inicio de las máquinas virtuales a través de una suscripción mediante etiquetas
En un entorno que incluye dos o más componentes en varias máquinas virtuales que admiten una carga de trabajo distribuida, es importante que se pueda decidir qué secuencia de componentes se inician o detienen en orden.  Para ello, puede seguir estos pasos:

1. Agregar una etiqueta **SequenceStart** y **SequenceStop** con un valor entero positivo a las máquinas virtuales a través de la suscripción destinada en las variables **External_Start_ResourceGroupNames** y  **External_Stop*ResourceGroupNames**.  Las acciones de inicio y detención se realizarán en orden ascendente.  Para aprender a etiquetar una máquina virtual, consulte [Etiquetado de una máquina virtual Windows en Azure](../virtual-machines/windows/tag.md) y [Etiquetado de una máquina virtual Linux en Azure](../virtual-machines/linux/tag.md)
2. Modifique las programaciones de **Sequenced-StartVM** y **Sequenced-StopVM** con la fecha y la hora para satisfacer sus requisitos, y habilite la programación.  

Para probar y validar la configuración, inicie manualmente el runbook **SequencedStartStop_Parent** y establezca el parámetro *ACTION* en **start** o en **stop**, y el parámetro *WhatIf* en **True**.<br><br> ![Configuración de los parámetros del runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Le permite obtener una vista previa de la acción que tendría lugar y realizar los cambios según sea necesario antes de implementarse en las máquinas virtuales de producción.  Una vez que esté familiarizado, puede ejecutar manualmente el runbook con el parámetro establecido en **false** o dejar que la programación de Automation **Sequenced-StartVM** y **Sequenced-StopVM** se ejecute automáticamente siguiendo su programación prescrita.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>Escenario 3: detención e inicio automáticos de máquinas virtuales a través de una suscripción según el uso de CPU
Para ayudar a administrar el costo de la ejecución de máquinas virtuales en su suscripción, esta solución puede ayudar al evaluar las máquinas virtuales de Azure que no se utilizan durante los períodos de poca actividad, por ejemplo, después de horas, y apagarlas automáticamente si el uso del procesador es menor de un porcentaje concreto.  

De forma predeterminada, la solución está preconfigurada para evaluar la métrica de CPU en porcentaje y si la utilización media es del 5 %, como mucho.  Esto se controla mediante las siguientes variables y puede modificarse si sus valores predeterminados no satisfacen sus requisitos:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Solo puede habilitar destinatarios de la acción en una suscripción y un grupo de recursos, o con una lista específica de máquinas virtuales, pero no ambos.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Destino de la acción de detención a un grupo de recursos y una suscripción

1. Configure las variables **External_Stop_ResourceGroupNames** y **External_ExcludeVMNames** para especificar las máquinas virtuales de destino.  
2. Habilite y actualice la programación de **Schedule_AutoStop_CreateAlert_Parent**.
3. Ejecute el runbook **AutoStop_CreateAlert_Parent** con el parámetro *ACTION* establecido en **start** y el parámetro *WhatIf* establecido en  **True** para obtener una vista previa de los cambios.

#### <a name="target-stop-action-by-vm-list"></a>Destino de la acción de detección con una lista de máquinas virtuales

1. Ejecute el runbook **AutoStop_CreateAlert_Parent** con el parámetro *ACTION* establecido en **start**, agregue una lista separada por comas de máquinas virtuales en el parámetro *VMList*, y el parámetro *WhatIf* establecido en **True** para obtener una vista previa de los cambios.  
2. Configure el parámetro **External_ExcludeVMNames** con la lista de máquinas virtuales separadas con una coma (VM1,VM2,VM3).
3. Este escenario no respeta las variables **External_Start_ResourceGroupNames** y **External_Stop_ResourceGroupnames**.  En este escenario, debe crear su propia programación de Automation. Para obtener detalles, vea [Programación de un runbook en Azure Automation](../automation/automation-schedules.md).

Ahora que tiene una programación para detener las máquinas virtuales según el uso de CPU, debe habilitar una de las programaciones siguientes para iniciarlas.

* Destine la acción de inicio según la suscripción y el grupo de recursos.  Vea los pasos descritos en [Escenario Nº 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) para realizar las pruebas y habilitar la programación **Scheduled-StartVM**.
* Destine la acción de inicio según la suscripción, el grupo de recursos y la etiqueta.  Vea los pasos descritos en [Escenario Nº 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) para realizar las pruebas y habilitar la programación "Sequenced-StartVM".


### <a name="configuring-e-mail-notifications"></a>Configuración de notificaciones por correo electrónico

Para configurar las notificaciones por correo electrónico después de implementar la solución, puede modificar las tres variables siguientes:

* External_EmailFromAddress: especifique la dirección de correo electrónico del remitente
* External_EmailToAddress: lista de direcciones de correo electrónico separadas por comas (user@hotmail.com, user@outlook.com) para recibir correos electrónicos de notificación
* External_IsSendEmail: si lo establece en **Si**, recibirá mensajes de correo electrónico; para deshabilitar las notificaciones de correo electrónico, establezca el valor en **No**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modificación de las programaciones de inicio y apagado

La administración de las programaciones de inicio y apagado en esta solución sigue los mismos pasos descritos en [Programación de un runbook en Azure Automation](automation-schedules.md).     

## <a name="log-analytics-records"></a>Registros de Log Analytics

Automation crea dos tipos de registros en el repositorio de OMS.

### <a name="job-logs"></a>Registros de trabajo

Propiedad | Description|
----------|----------|
Autor de llamada |  La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados.|
Categoría | Clasificación del tipo de datos.  Para Automation, el valor será JobLogs.|
CorrelationId | GUID que es el identificador de correlación del trabajo de Runbook.|
JobId | GUID que es el identificador del trabajo de Runbook.|
operationName | Especifica el tipo de operación realizada en Azure.  Para Automation, el valor será Job.|
resourceId | Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
ResourceGroup | Especifica el nombre del grupo de recursos del trabajo de Runbook.|
ResourceProvider | Especifica el servicio de Azure que proporciona los recursos que puede implementar y administrar.  Para Automation, el valor es Azure Automation.|
ResourceType | Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
resultType | El estado del trabajo de Runbook.  Los valores posibles son:<br>Started<br>Stopped<br>Suspended<br>Con error<br>- Correcto|
resultDescription | Describe el estado de resultado del trabajo de Runbook.  Los valores posibles son:<br>- Se inicia el trabajo<br>- Error del trabajo<br>- Trabajo completado|
RunbookName | Especifica el nombre del Runbook.|
SourceSystem | Especifica el sistema de origen para los datos enviados.  Para Automation, el valor será OpsManager.|
StreamType | Especifica el tipo de evento. Los valores posibles son:<br>- Detallado<br>- Salida<br>error<br>Warning (Advertencia)|
SubscriptionId | Especifica el identificador de suscripción del trabajo.
Hora | Fecha y hora en que se ejecuta el trabajo de Runbook.|


### <a name="job-streams"></a>Transmisiones de trabajo

Propiedad | Description|
----------|----------|
Autor de llamada |  La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados.|
Categoría | Clasificación del tipo de datos.  Para Automation, el valor es JobStreams.|
JobId | GUID que es el identificador del trabajo de Runbook.|
operationName | Especifica el tipo de operación realizada en Azure.  Para Automation, el valor será Job.|
ResourceGroup | Especifica el nombre del grupo de recursos del trabajo de Runbook.|
resourceId | Especifica el id. de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
ResourceProvider | Especifica el servicio de Azure que proporciona los recursos que puede implementar y administrar.  Para Automation, el valor es Azure Automation.|
ResourceType | Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
resultType | El resultado del trabajo de Runbook en el momento en que se generó el evento.  Los valores posibles son:<br>- En curso|
resultDescription | Incluye la secuencia de salida del Runbook.|
RunbookName | El nombre del Runbook.|
SourceSystem | Especifica el sistema de origen para los datos enviados.  Para Automation, el valor será OpsManager.|
StreamType | El tipo de flujo de trabajo. Los valores posibles son:<br>progreso<br>- Salida<br>Warning (Advertencia)<br>Error<br>DEBUG<br>- Detallado|
Hora | Fecha y hora en que se ejecuta el trabajo de Runbook.|

Cuando se realiza cualquier búsqueda de registros que devuelve registros de la categoría **JobLogs** o **JobStreams**, puede seleccionar la vista **JobLogs** o **JobStreams** que muestra un conjunto de iconos que resumen las actualizaciones devueltas en la búsqueda.

## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo

En la tabla siguiente se proporcionan búsquedas de registros de ejemplo para los registros de trabajo recopilados por esta solución. 

Consultar | Descripción|
----------|----------|
Búsqueda de trabajos del runbook ScheduledStartStop_Parent que se hayan completado correctamente | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
Búsqueda de trabajos del runbook SequencedStartStop_Parent que se hayan completado correctamente | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>Eliminación de la solución

Si decide que ya no necesita usar la solución más, puede eliminarla de la cuenta de Automation.  La eliminación de la solución solo quitará los runbooks, no eliminará las programaciones ni las variables que se crearon al agregar la solución.  Esos recursos se deben eliminar manualmente si no los está usando con otros runbooks.  

Para eliminar la solución, realice los siguientes pasos.

1.  En su cuenta de Automation, seleccione **Área de trabajo** en el panel izquierdo.  
2.  En la página **Soluciones**, seleccione la solución **Start-Stop-VM[Workspace]**.  En la página **VMManagementSolution[Workspace]**, en el menú, haga clic en **Eliminar**.<br><br> ![Eliminación de la solución de administración de máquinas virtuales](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  En la ventana **Eliminar solución**, confirme que desea eliminar la solución.
4.  Mientras se comprueba la información y se elimina la solución, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú.  Volverá a la página **Soluciones** una vez iniciado el proceso para quitar la solución.  

No se eliminarán la cuenta de Automation ni el área de trabajo de Log Analytics como parte de este proceso.  Si no desea conservar el área de trabajo de Log Analytics, debe eliminarla manualmente.  Esto puede realizarse también desde Azure Portal.   En la pantalla de inicio de Azure Portal, seleccione **Log Analytics** y, después, en la hoja **Log Analytics**, seleccione el área de trabajo y haga clic en **Eliminar** desde el menú en la hoja de configuración del área de trabajo.  
      
## <a name="next-steps"></a>Pasos siguientes

- Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de trabajos de Automation con Log Analytics, consulte [Búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md)
- Para más información acerca de Log Analytics y de los orígenes de recopilación de datos, consulte [Recopilación de registros y métricas de Azure para servicios de Log Analytics](../log-analytics/log-analytics-azure-storage.md)






   

