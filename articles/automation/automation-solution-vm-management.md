---
title: "Solución Start/Stop VMs during off-hours (versión preliminar)| Microsoft Docs"
description: "Esta solución de administración de máquinas virtuales inicia y detiene máquinas virtuales de Azure Resource Manager según una programación y realiza una supervisión proactiva desde Log Analytics."
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: magoedte
ms.openlocfilehash: 4424cbb83bdb31c60e15d62f9387b4050611a98d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Solución Start/Stop VMs during off-hours (versión preliminar) en Azure Automation

La solución Start/Stop VMs during off-hours inicia y detiene las máquinas virtuales de Azure en según las programaciones definidas por el usuario, proporciona información detallada mediante Azure Log Analytics y envía mensajes de correo electrónico opcionales mediante [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Admite tanto máquinas virtuales clásicas como Azure Resource Manager para la mayoría de los escenarios. 

Esta solución proporciona una opción de automatización descentralizada para los usuarios que desean reducir sus costos mediante el uso de recursos de bajo costo sin servidor. Con esta solución, es posible:

* Programar el inicio y la detención de las máquinas virtuales.
* Programar el inicio y la detención de las máquinas virtuales mediante etiquetas de Azure (no se admite en las máquinas virtuales clásicas).
* Detener automáticamente las máquinas virtuales si se detecta un bajo uso de la CPU.

## <a name="prerequisites"></a>Requisitos previos

- Los runbooks funcionan con una [cuenta de ejecución de Azure](automation-offering-get-started.md#authentication-methods).  La cuenta de ejecución es el método de autenticación preferido, ya que emplea la autenticación mediante certificado, en lugar de una contraseña que puede expirar o cambiar con frecuencia.  

- Esta solución solo administrar máquinas virtuales que se encuentren en la misma suscripción que la cuenta de Azure Automation.  

- Esta solución se implementa únicamente en las siguientes regiones de Azure: Sudeste de Australia, Centro de Canadá, Centro de la India, Este de EE. UU., Japón Oriental, Sudeste Asiático, Sur de Reino Unido y Europa Occidental.  
    
    > [!NOTE]
    > Los runbooks que administran la programación de las máquinas virtuales pueden dirigirse a las de cualquier región.  

- Para enviar notificaciones por correo electrónico cuando finalicen los runbooks de inicio y detención de máquinas virtuales, durante la incorporación desde Azure Marketplace, seleccione **Sí** para implementar SendGrid. 

    > [!IMPORTANT]
    > SendGrid es un servicio de terceros. Para obtener soporte, póngase en contacto con [SendGrid](https://sendgrid.com/contact/).  
    >
   
    Las limitaciones de SendGrid son:

    * Un máximo de una cuenta de SendGrid por usuario y suscripción.
    * Un máximo de dos cuentas de SendGrid por suscripción.

Si ha implementado una versión anterior de esta solución, debe eliminarla de la cuenta antes de implementar esta.  

## <a name="solution-components"></a>Componentes de soluciones

Esta solución incluye runbooks preconfigurados, programaciones e integración con Log Analytics para que pueda personalizar el inicio y el apagado de las máquinas virtuales para adaptarlas a las necesidades de su empresa. 

### <a name="runbooks"></a>Runbooks

En la tabla siguiente se enumeran los runbooks implementados en su cuenta de Automation.  No debe realizar cambios en el código del runbook. En su lugar, escriba un runbook que aporte una nueva funcionalidad.

> [!IMPORTANT]
> No ejecute directamente ningún runbook cuyo nombre tenga anexado "child".
>

Todos los runbooks primarios incluyen el parámetro All *WhatIf*. Cuando se establece en **True**, *Whatif* permite detallar el comportamiento exacto del runbook cuando se ejecuta sin el parámetro *WhatIf* y valida que el destino son las máquinas virtuales correctas.  Los runbooks solo realizan sus acciones definidas cuando el parámetro *WhatIf* está establecido en **False**. 

|**Runbook** | **Parámetros** | **Descripción**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Se llama solo desde el runbook primario. Crea alertas según el recurso para el escenario de AutoStop.| 
|AutoStop_CreateAlert_Parent | WhatIf: True o False <br> VMList | Crea o actualiza las reglas de alerta de Azure en las máquinas virtuales de los grupos de recursos o la suscripción de destino. <br> VMList: lista de máquinas virtuales separadas por comas.  Por ejemplo, *vm1,vm2,vm3*.| 
|AutoStop_Disable | None | Deshabilita la programación predeterminada y las alertas de AutoStop.| 
|AutoStop_StopVM_Child | WebHookData | Se llama solo desde el runbook primario. Las reglas de alertas llaman a este runbook para detener la máquina virtual.|  
|Bootstrap_Main | None | Se usa una vez para realizar configuraciones de arranque como webhookURI, a las que normalmente no se puede acceder desde Azure Resource Manager. Este runbook se quita automáticamente tras la implementación correcta.|  
|ScheduledStartStop_Child | VMName <br> Acción: iniciar o detener <br> ResourceGroupName | Se llama solo desde el runbook primario. Ejecuta detener o iniciar en la detención programada.|  
|ScheduledStartStop_Parent | Acción: iniciar o detener <br> WhatIf: True o False | Esto afecta a todas las máquinas virtuales de la suscripción. Edite **External_Start_ResourceGroupNames** y **External_Stop_ResourceGroupNames** para que se ejecute solo en estos grupos de recursos de destino. También puede excluir máquinas virtuales específicas si actualiza la variable **External_ExcludeVMNames**. *WhatIf* se comporta igual que en otros runbooks.|  
|SequencedStartStop_Parent | Acción: iniciar o detener <br> WhatIf: True o False | Crear etiquetas denominadas **SequenceStart** y **SequenceStop** en todas las máquinas virtuales en las que desee secuenciar la actividad de inicio y detención. El valor de la etiqueta debe ser un entero positivo (1, 2, 3) que se corresponda con el orden en que se desee que se realice el inicio o la detención. *WhatIf* se comporta igual que en otros runbooks. <br> **Nota**: Las máquinas virtuales deben estar en grupos de recursos definidos como External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames y External_ExcludeVMNames, en las variables de Azure Automation. Para que las acciones surtan efecto, deben tener las etiquetas correspondientes.|

### <a name="variables"></a>Variables

En la tabla siguiente se enumeran las variables creadas en su cuenta de Automation.  Solo se deben modificar las variables cuyo prefijo sea **External**. La modificación de variables con el prefijo **Internal** produce efectos no deseados.  

|**Variable** | **Descripción**|
---------|------------|
|External_AutoStop_Condition | El operador condicional necesario para configurar la condición antes de desencadenar una alerta. Los valores que se aceptan son **GreaterThan**, **GreaterThanOrEqual**, **LessThan** y **LessThanOrEqual**.|  
|External_AutoStop_Description | Alerta para detener la máquina virtual si el porcentaje de CPU supera el umbral.|  
|External_AutoStop_MetricName | El nombre de la métrica de rendimiento para el que se configura la regla de alertas de Azure.| 
|External_AutoStop_Threshold | Umbral para la regla de alertas de Azure especificada en la variable *External_AutoStop_MetricName*. Los valores de porcentaje pueden oscilar entre 1 y 100.|  
|External_AutoStop_TimeAggregationOperator | El operador de agregación de tiempo, que se aplica al tamaño de la ventana seleccionada para evaluar la condición. Los valores que se aceptan son **Promedio**, **Mínimo**, **Máximo**, **Total** y **Último**.|  
|External_AutoStop_TimeWindow | El tamaño de la ventana en la que Azure analiza la métrica seleccionada para desencadenar una alerta. Este parámetro acepta la entrada en formato timespan. Los valores posibles son de 5 minutos a 6 horas.|  
|External_EmailFromAddress | Especifica el remitente del correo electrónico.|  
|External_EmailSubject | Especifica el texto de la línea de asunto del correo electrónico.|  
|External_EmailToAddress | Especifica los destinatarios del correo electrónico. Separa los nombres con una coma.|  
|External_ExcludeVMNames | Escriba los nombres de máquina virtual que se van a excluir y sepárelos con una coma sin espacios en blanco.|  
|External_IsSendEmail | Especifica la opción de enviar una notificación por correo electrónico al finalizar.  Especifique **Si** o **No** para no enviar correo electrónico.  Esta opción debe ser **No** si no se han habilitado notificaciones por correo electrónico durante la implementación inicial.|  
|External_Start_ResourceGroupNames | Especifica uno o varios grupos de recursos y separa los valores con una coma, destinados a las acciones de inicio.|  
|External_Stop_ResourceGroupNames | Especifica uno o varios grupos de recursos y separa los valores con una coma, destinados a las acciones de detención.|  
|Internal_AutomationAccountName | Especifica el nombre de la cuenta de Automation.|  
|Internal_AutoSnooze_WebhookUri | Especifica el URI de Webhook llamado para el escenario de AutoStop.|  
|Internal_AzureSubscriptionId | Especifica el identificador de la suscripción de Azure.|  
|Internal_ResourceGroupName | Especifica el nombre el grupo de recursos de la cuenta de Automation.|  
|Internal_SendGridAccountName | Especifica el nombre de cuenta de SendGrid.|  
|Internal_SendGridPassword | Especifica la contraseña de la cuenta de SendGrid.|  

<br>

En todos los escenarios, las variables **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** y **External_ExcludeVMNames** son necesarias para dirigirse a las máquinas virtuales, aunque hay que proporcionar una lista separada por comas de las máquinas virtuales para el runbook **AutoStop_CreateAlert_Parent**. Es decir, para que se realicen acciones de iniciar o detener tengan, las máquinas virtuales deben residir en el recurso de destino. La lógica funciona un poco como directiva de Azure, en el sentido de que el destino puede ser la suscripción o el grupo de recursos, y las acciones las pueden heredar las máquinas virtuales recién creadas. Este enfoque evita tener que mantener una programación independiente para cada máquina virtual y administrar los inicios y detenciones en escala.

### <a name="schedules"></a>Programaciones

En la tabla siguiente se enumera cada una de las programaciones predeterminadas que se crean en su cuenta de Automation.  Puede modificarlas o crear sus propias programaciones personalizadas.  De forma predeterminada, se deshabilitan todas ellas, excepto **Scheduled_StartVM** y **Scheduled-StopVM**.

No se deben habilitar todas las programaciones, ya que ello podría crear acciones de programación que se superpongan. Lo mejor es determinar las optimizaciones que se desean y realizar las modificaciones oportunas.  Vea los escenarios de ejemplo en la sección de información general para obtener una explicación.   

|**Nombre de programación** | **Frecuencia** | **Descripción**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Cada ocho horas | Ejecuta el runbook AutoStop_CreateAlert_Parent cada ocho horas, que a su vez detiene los valores basados en la máquina virtual en External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames y External_ExcludeVMNames en las variables de Azure Automation.  Como alternativa, puede especificar una lista separada por comas de las máquinas virtuales mediante el parámetro VMList.|  
|Scheduled_StopVM | Definido por el usuario, diario | Ejecuta el runbook Scheduled_Parent con un parámetro de *Stop* cada día a la hora especificada.  Detiene automáticamente todas las máquinas virtuales que cumplan las reglas definidas por las variables de los recursos. Debe habilitar la programación relacionada, **Scheduled-StartVM**.|  
|Scheduled_StartVM | Definido por el usuario, diario | Ejecuta el runbook Scheduled_Parent con un parámetro de *Start* cada día a la hora especificada.  Inicia automáticamente todas las máquinas virtuales que cumplan las reglas definidas por las variables adecuadas.  Debe habilitar la programación relacionada, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), todos los viernes | Ejecuta el runbook Sequenced_Parent con un parámetro de *Stop* todos los viernes a la hora especificada.  Detiene secuencialmente (de forma ascendente) todas las máquinas virtuales que tengan la etiqueta **SequenceStop** definida por las variables adecuadas.  Para más información acerca de los valores de las etiquetas y las variables de los recursos, consulte la sección Runbooks.  Debe habilitar la programación relacionada, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 PM (hora UTC), todos los lunes | Ejecuta el runbook Sequenced_Parent con un parámetro de *Start* cada lunes a la hora determinada. Inicia secuencialmente (de forma descendente) todas las máquinas virtuales que tengan la etiqueta **SequenceStart** definida por las variables adecuadas.  Para más información acerca de los valores de las etiquetas y las variables de los recursos, consulte la sección Runbooks.  Debe habilitar la programación relacionada, **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Configuración

Realice los siguientes pasos para agregar la solución Start/Stop VMs during off-hours a su cuenta de Automation y, después, configure las variables para personalizar la solución.

1. En Azure Portal, haga clic en **Nuevo**.<br> ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. En el panel de Marketplace, escriba una palabra clave como **Start** o **Start/Stop**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Como alternativa, puede escribir en una o varias palabras clave del nombre completo de la solución y presione Entrar.  Seleccione **Start/Stop VMs during off-hours [Preview]** (Iniciar o detener máquinas virtuales fuera de las horas de trabajo [versión preliminar]) desde los resultados de la búsqueda.  
3. En el panel **Start/Stop VMs during off-hours [Preview]** de la solución seleccionada, revise la información de resumen y haga clic en **Crear**.  
4. Aparece el panel **Agregar solución**. Se le pide que configure la solución antes de importarla en la suscripción de Automation.<br><br> ![Hoja Agregar solución de administración de VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  En el panel **Agregar solución**, seleccione **Área de trabajo**. Seleccione un área de trabajo de OMS que esté vinculada a la suscripción de Azure en la que se encuentra la cuenta de Automation. Si no tiene ningún área de trabajo, seleccione **Crear nueva área de trabajo**. En el panel **Área de trabajo de OMS**, realice las siguientes operaciones: 
   - Especifique un nombre para el nuevo **espacio de trabajo de OMS**.
   - Seleccione la **suscripción** a la que vincularlo en la lista desplegable si la opción predeterminada seleccionada no es adecuada.
   - En **Grupo de recursos**, puede crear un grupo de recursos nuevo o seleccionar uno existente.  
   - Seleccione una **ubicación**.  Actualmente las únicas ubicaciones disponibles son: **Sudeste de Australia**, **Centro de Canadá**, **Centro de la India**, **Este de EE. UU.**, **Japón Oriental**, **Sudeste Asiático**, **Sur de Reino Unido** y **Europa Occidental**.
   - Seleccione un **plan de tarifa**.  La solución se ofrece en dos niveles: gratis y pagado en OMS.  El nivel gratis tiene limitados la cantidad de datos que se recopilan a diario, el período de retención y los minutos de tiempo de ejecución del trabajo del runbook.  El nivel de pago de OMS no tiene ningún límite sobre la cantidad de datos recopilados a diario.  

        > [!NOTE]
        > Aunque el nivel de pago por GB (Independiente) se muestra como opción, no es aplicable.  Si lo selecciona y continúa con la creación de esta solución en su suscripción, se producirá un error.  Esto se solucionará cuando se lance oficialmente la solución.<br>Esta solución solo usa minutos de trabajo e ingesta de registros.  La solución no agrega nodos de OMS adicionales a su entorno.  

6. Después de proporcionar la información necesaria en el panel **Área de trabajo de OMS**, haga clic en **Crear**.  Se puede realizar un seguimiento de su progreso en la opción **Notificaciones** del menú, que le devuelve al panel **Agregar solución** cuando haya finalizado.  
7. En el panel **Agregar solución**, seleccione **Cuenta de Automation**.  Si va a crear una nueva área de trabajo de OMS, también debe crear una nueva cuenta de Automation con la que asociarla.  Seleccionar **Crear una cuenta de Automation** y, en el panel **Agregar cuenta de Automation**, especifique la siguiente información: 
  - En el campo **Nombre**, escriba el nombre de la cuenta de Automation.

    Las restantes opciones se rellenan automáticamente en función del área de trabajo de OMS seleccionado. Dichas opciones no se pueden modificar.  Una cuenta de ejecución de Azure es el método de autenticación predeterminado para los Runbooks incluidos en esta solución.  Después de hacer clic en **Aceptar**, se validan las opciones de configuración y se crea la cuenta de Automation.  Puede realizar un seguimiento de su progreso en **Notificaciones** en el menú. 

    Si no, puede seleccionar una cuenta de ejecución de Automation existente.  Tenga en cuenta que la cuenta que seleccione no puede estar vinculada a otra área de trabajo de OMS. Si lo está, recibirá un mensaje y tendrá que seleccionar una cuenta de ejecución de Automation diferente, o bien crear una nueva.<br><br> ![Cuenta de Automation ya vinculada al espacio de trabajo de OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Por último, en el panel **Agregar solución**, seleccione **Configuración**. Aparece el panel **Parámetros**.<br><br> ![Panel de parámetros para la solución](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  En este caso, se le pedirá que:  
   - Especifique los **nombres del grupo de recursos de destino**. Estos son los nombres de los grupos de recursos que contienen máquinas virtuales que se administrarán mediante esta solución.  Puede especificar más de un nombre y separarlos con una coma (los valores no distinguen mayúsculas de minúsculas).  Se puede usar un carácter comodín si quiere dirigirse a las máquinas virtuales de todos los grupos de recursos de la suscripción.
   - Especifique el **lista de exclusiones de máquinas virtuales (cadena)**. Es el nombre de una o varias máquinas virtuales del grupo de recursos de destino.  Puede especificar más de un nombre y separarlos con una coma (los valores no distinguen mayúsculas de minúsculas).  Se admite el uso de un carácter comodín.
   - Seleccione una **programación**. Se trata de la fecha y hora recurrentes para iniciar y detener las máquinas virtuales en los grupos de recursos de destino.  De forma predeterminada, la programación se configura para la zona horaria UTC. No se puede seleccionar otra región.  Para configurar su zona horaria concreta después de configurar la solución, consulte el siguiente artículo [Modificación de la programación de inicio y apagado](#modifying-the-startup-and-shutdown-schedule).
   - Para recibir **notificaciones por correo electrónico** de SendGrid, acepte el valor predeterminado **Sí** y proporcione una dirección de correo electrónico válida. Si selecciona **No** pero posteriormente decide que desea recibir notificaciones por correo electrónico, puede actualizar la variable **External_EmailToAddress** con direcciones de correo electrónico válidas separadas por una coma y, después, modificar la variable **External_IsSendEmail** con el valor **Yes**.  

9. Cuando haya configurado los valores iniciales necesarios para la solución, seleccione **Crear**.  Cuando todos los valores se hayan validado, la solución se implementa en su suscripción.  Este proceso puede tardar varios segundos en finalizar y se puede realizar un seguimiento de su progreso en **Notificaciones** en el menú. 

## <a name="collection-frequency"></a>Frecuencia de recopilación

Tanto el registro de trabajos como los datos del flujo de trabajos de Automation se ingieren en el repositorio de Log Analytics cada cinco minutos.  

## <a name="using-the-solution"></a>Uso de la solución

Al agregar la solución de administración de máquinas virtuales, el icono **StartStopVMView** se agrega al panel de su área de trabajo de Log Analytics desde Azure Portal.  Este icono muestra un número y una representación gráfica de los trabajos de runbook de la solución que se han iniciado y han finalizado correctamente.<br><br> ![Icono de StartStopVM View](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png) (Ver StartStopVM) de administración de VM  

En su cuenta de Automation, para acceder a la solución y administrarla, seleccione la opción **Área de trabajo**. En la página de Log Analytics, seleccione **Soluciones** en el panel izquierdo.  En la página **Soluciones**, seleccione la solución **Start-Stop-VM[workspace]** en la lista.<br><br> ![Lista de soluciones en Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Al seleccionar la solución se muestra el panel de la solución **Start-Stop-VM [workspace]**. En él puede examinar detalles importantes como el icono **StartStopVM**. Al igual que en el área de trabajo de Log Analytics, este icono muestra un número y una representación gráfica de los trabajos de runbook de la solución que se han iniciado y han finalizado correctamente.<br><br> ![Página de la solución Update Management de Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Desde aquí puede realizar más análisis de los registros de trabajos. Para ello, solo debe hacer clic en el icono del anillo. El panel de la solución muestra el historial de trabajos y las consultas de búsqueda de registros predefinidas. Conmute al portal avanzado de Log Analytics para realizar búsquedas basadas en sus consultas de búsqueda.  

Todos los runbooks primarios incluyen el parámetro All *WhatIf*. Cuando se establece en **True**, permite detallar el comportamiento exacto del runbook cuando se ejecuta sin el parámetro *WhatIf* y valida que el destino son las máquinas virtuales correctas. Los runbooks solo realizan sus acciones definidas cuando el parámetro *WhatIf* está establecido en **False**.  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>Escenario 1: Detención e inicio diarios de máquinas virtuales en una suscripción o en grupos de recursos de destino 

Se trata de la configuración predeterminada cuando se implementa la solución por primera vez.  Por ejemplo, puede configurarla para que detenga todas las máquinas virtuales de una suscripción por la noche, cuando salga del trabajo, e iniciarlas por la mañana cuando vuelva a la oficina. Al configurar las programaciones **Scheduled-StartVM** y **Scheduled-StopVM** durante la implementación, inician y detienen las máquinas virtuales de destino.
>[!NOTE]
>La zona horaria es su zona horaria actual cuando configura el parámetro de hora de la programación. Sin embargo, en Azure Automation se almacena en formato UTC.  No es necesario realizar ninguna conversión de zona horaria ya que se controla durante la implementación.

Para controlar qué las máquinas virtuales están en el ámbito, configure las siguientes variables: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** y **External_ExcludeVMNames**.  

>[!NOTE]
> El valor de **Target ResourceGroup Names** se almacena como el valor tanto de **External_Start_ResourceGroupNames** como de **External_Stop_ResourceGroupNames**. A fin de lograr un mayor detalle, puede modificar cada una de estas variables para distintos grupos de recursos de destino.  Para la acción de inicio, use **External_Start_ResourceGroupNames** y para la acción de detención use **External_Stop_ResourceGroupNames**. Las máquinas virtuales se agregan automáticamente a las programaciones de inicio y detención.

Para probar y validar la configuración, inicie manualmente el runbook **ScheduledStartStop_Parent** y establezca el parámetro ACTION en **start** o en **stop** y el parámetro WHATIF en **True**.<br><br> ![Configuración de los parámetros del runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 Obtenga una vista previa de la acción programada y realice los cambios necesarios antes de implementarla en las máquinas virtuales de producción.  Puede ejecutar manualmente el runbook con el parámetro establecido en **False** o dejar que la programación de Automation **Schedule-StartVM** y **Schedule-StopVM** se ejecuten automáticamente siguiendo la programación que ha establecido.

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>Escenario 2: Secuenciación de las máquinas virtuales que se van a iniciar o detener en una suscripción mediante etiquetas
En un entorno que incluya dos, o más, componentes de varias máquinas virtuales que admitan una carga de trabajo distribuida, es importante que se pueda decidir la secuencia en que los componentes se inician o detienen en orden.  Para ello, puede seguir estos pasos:

1. Agregue unas etiquetas **SequenceStart** y **SequenceStop** con un valor entero positivo a las máquinas virtuales cuyo destino se establece en las variables **External_Start_ResourceGroupNames** y **External_Stop_ResourceGroupNames**.  Las acciones de inicio y detención se realizan en orden ascendente.  Para aprender a etiquetar una máquina virtual, consulte [Etiquetado de una máquina virtual en Azure](../virtual-machines/windows/tag.md) y [Etiquetado de una máquina virtual Linux en Azure](../virtual-machines/linux/tag.md).
2. Modifique las programaciones de **Sequenced-StartVM** y **Sequenced-StopVM** a una fecha y hora que cumplan sus requisitos, y habilite la programación.  

Para probar y validar la configuración, inicie manualmente el runbook **SequencedStartStop_Parent**. Establezca el parámetro ACTION en **start** o **stop** y el parámetro WHATIF en **True**.<br><br> ![Configuración de los parámetros del runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Obtenga una vista previa de la acción y realice los cambios necesarios antes de implementarla en las máquinas virtuales de producción.  Cuando esté preparado, ejecute manualmente el runbook con el parámetro establecido en **False** o deje que la programación de Automation **Sequenced-StartVM** y **Sequenced-StopVM** se ejecuten automáticamente siguiendo su programación prescrita.  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>Escenario 3: Detención e inicio automáticos de máquinas virtuales en una suscripción en función del uso de la CPU
Esta solución puede ayudarle a administrar el costo de la ejecución de máquinas virtuales en su suscripción mediante la evaluación de las máquinas virtuales de Azure que no se utilizan durante los períodos de poca actividad, por ejemplo, las horas no laborables, y apagarlas automáticamente si el uso del procesador es menor de un porcentaje concreto.  

De forma predeterminada, la solución está preconfigurada para evaluar la métrica de CPU en porcentaje, con el fin de ver si la utilización media es del 5 %, o menos.  Esto se controla mediante las siguientes variables y puede modificarse si los valores predeterminados no satisfacen sus requisitos:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Puede habilitar que el destino de la acción sea una suscripción y un grupo de recursos, o una lista específica de máquinas virtuales, pero no ambas cosas.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Destino de la acción de detención a un grupo de recursos y una suscripción

1. Configure las variables **External_Stop_ResourceGroupNames** y **External_ExcludeVMNames** para especificar las máquinas virtuales de destino.  
2. Habilite y actualice la programación de **Schedule_AutoStop_CreateAlert_Parent**.
3. Ejecute el runbook **AutoStop_CreateAlert_Parent** con el parámetro ACTION establecido en **start** y el parámetro WHATIF establecido en **True** para obtener una vista previa de los cambios.

#### <a name="target-the-stop-action-by-vm-list"></a>Destino de la acción de detención por lista de máquinas virtuales

1. Ejecute el runbook **AutoStop_CreateAlert_Parent** con el parámetro ACTION establecido en **start**, agregue una lista separada por comas de máquinas virtuales en el parámetro *VMList* y establezca el parámetro WHATIF en **True**. Obtenga una vista previa de los cambios.  
2. Configure el parámetro **External_ExcludeVMNames** con una lista separada por comas de máquinas virtuales (VM1,VM2,VM3).
3. Este escenario no respeta las variables **External_Start_ResourceGroupNames** y **External_Stop_ResourceGroupnames**.  Para este escenario, es preciso que cree su propia programación de Automation. Para más información, consulte [Programación de un runbook en Azure Automation](../automation/automation-schedules.md).

Ahora que tiene una programación para detener las máquinas virtuales en función del uso de la CPU, debe habilitar una de las programaciones siguientes para iniciarlas.

* Elija el destino de acción de inicio por suscripción y grupo de recursos.  Vea los pasos descritos en [Escenario 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) para realizar las pruebas y habilitar las programaciones de **Scheduled-StartVM**.
* Elija el destino de acción de inicio por suscripción, grupo de recursos y etiqueta.  Vea los pasos descritos en [Escenario 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) para realizar las pruebas y habilitar la programación **Sequenced-StartVM**.


### <a name="configuring-email-notifications"></a>Configuración de las notificaciones por correo electrónico

Para configurar las notificaciones por correo electrónico después de implementar la solución, modifique las tres variables siguientes:

* External_EmailFromAddress: especifique la dirección de correo electrónico del remitente.
* External_EmailToAddress: especifique una lista de las direcciones de correo electrónico separadas por comas (user@hotmail.com, user@outlook.com) que van a recibir correos electrónicos de notificación.
* External_IsSendEmail: establézcalo en **Sí** para recibir mensajes de correo electrónico. Para deshabilitar las notificaciones de correo electrónico, establezca el valor en **No**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modificación de las programaciones de inicio y apagado

La administración de las programaciones de inicio y apagado en esta solución sigue los mismos pasos descritos en [Programación de un runbook en Azure Automation](automation-schedules.md).     

## <a name="log-analytics-records"></a>Registros de Log Analytics

Automation crea dos tipos de registros en el repositorio de OMS: registros de trabajo y flujos de trabajo.

### <a name="job-logs"></a>Registros de trabajo

Propiedad | DESCRIPCIÓN|
----------|----------|
Autor de llamada |  La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados.|
Categoría | Clasificación del tipo de datos.  Para Automation, el valor será JobLogs.|
CorrelationId | GUID que es el identificador de correlación del trabajo de Runbook.|
JobId | GUID que es el identificador del trabajo de Runbook.|
operationName | Especifica el tipo de operación realizada en Azure.  En Automation, el valor es Job.|
ResourceId | Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
ResourceGroup | Especifica el nombre del grupo de recursos del trabajo de Runbook.|
ResourceProvider | Especifica el servicio de Azure que proporciona los recursos que puede implementar y administrar.  Para Automation, el valor es Azure Automation.|
ResourceType | Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
resultType | El estado del trabajo de Runbook.  Los valores posibles son:<br>Started<br>Stopped<br>Suspended<br>Con error<br>- Correcto|
resultDescription | Describe el estado de resultado del trabajo de Runbook.  Los valores posibles son:<br>- Se inicia el trabajo<br>- Error del trabajo<br>- Trabajo completado|
RunbookName | Especifica el nombre del Runbook.|
SourceSystem | Especifica el sistema de origen para los datos enviados.  En Automation, el valor es OpsManager|
StreamType | Especifica el tipo de evento. Los valores posibles son:<br>- Detallado<br>- Salida<br>error<br>Warning (Advertencia)|
SubscriptionId | Especifica el identificador de suscripción del trabajo.
Hora | Fecha y hora en que se ejecuta el trabajo de Runbook.|


### <a name="job-streams"></a>Transmisiones de trabajo

Propiedad | DESCRIPCIÓN|
----------|----------|
Autor de llamada |  La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados.|
Categoría | Clasificación del tipo de datos.  Para Automation, el valor es JobStreams.|
JobId | GUID que es el identificador del trabajo de Runbook.|
operationName | Especifica el tipo de operación realizada en Azure.  En Automation, el valor es Job.|
ResourceGroup | Especifica el nombre del grupo de recursos del trabajo de Runbook.|
ResourceId | Especifica el id. de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
ResourceProvider | Especifica el servicio de Azure que proporciona los recursos que puede implementar y administrar.  Para Automation, el valor es Azure Automation.|
ResourceType | Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook.|
resultType | El resultado del trabajo de Runbook en el momento en que se generó el evento. Un valor posible es:<br>- En curso|
resultDescription | Incluye la secuencia de salida del Runbook.|
RunbookName | El nombre del Runbook.|
SourceSystem | Especifica el sistema de origen para los datos enviados.  En Automation, el valor es OpsManager.|
StreamType | El tipo de flujo de trabajo. Los valores posibles son:<br>- Progreso<br>- Salida<br>Warning (Advertencia)<br>Error<br>DEBUG<br>- Detallado|
Hora | Fecha y hora en que se ejecuta el trabajo de Runbook.|

Cuando se realiza cualquier búsqueda de registros que devuelve registros de la categoría **JobLogs** o **JobStreams**, puede seleccionar las vistas **JobLogs** o **JobStreams**, que muestran un conjunto de iconos que resumen las actualizaciones devueltas en la búsqueda.

## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo

En la tabla siguiente se proporcionan búsquedas de registros de ejemplo para los registros de trabajo recopilados por esta solución. 

Consultar | DESCRIPCIÓN|
----------|----------|
Búsqueda de trabajos del runbook ScheduledStartStop_Parent que hayan finalizado correctamente | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
Búsqueda de trabajos del runbook SequencedStartStop_Parent que hayan finalizado correctamente | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>Eliminación de la solución

Si decide que ya no necesita usar la solución, puede eliminarla de la cuenta de Automation.  La eliminación de la solución solo quita los runbooks. No elimina las programaciones ni las variables que se crearon cuando se agregó la solución.  Esos recursos se deben eliminar manualmente si no se usan con otros runbooks.  

Para eliminar la solución, realice los siguientes pasos.

1.  En su cuenta de Automation, seleccione **Área de trabajo** en el panel izquierdo.  
2.  En la página **Soluciones**, seleccione la solución **Start-Stop-VM[Workspace]**.  En la página **VMManagementSolution[Workspace]**, en el menú, seleccione **Eliminar**.<br><br> ![Eliminación de la solución de administración de máquinas virtuales](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  En la ventana **Eliminar solución**, confirme que desea eliminar la solución.
4.  Mientras se comprueba la información y se elimina la solución, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú.  Cuando comience el proceso para eliminar la solución, se le devuelve a la página **Soluciones**.  

No se eliminarán la cuenta de Automation ni el área de trabajo de Log Analytics como parte de este proceso.  Si no desea conservar el área de trabajo de Log Analytics, debe eliminarla manualmente,  lo que se puede hacer desde Azure Portal:

1.    En la pantalla de inicio de Azure Portal, seleccione **Log Analytics**.
2. En el panel **Log Analytics**, seleccione el área de trabajo.
3. Seleccione **Eliminar** en el menú del panel de configuración del área de trabajo.  
      
## <a name="next-steps"></a>Pasos siguientes

- Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de trabajos de Automation con Log Analytics, consulte [Descripción de las búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md).
- Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md).
- Para más información acerca de Log Analytics y de los orígenes de recopilación de datos, consulte [Recopilación de registros y métricas de Azure para servicios de Log Analytics](../log-analytics/log-analytics-azure-storage.md).






   

