---
title: "Solución de inicio o detención de máquinas virtuales fuera de las horas de trabajo [versión preliminar]| Microsoft Docs"
description: "Las soluciones de administración de máquinas virtuales inician y detienen las máquinas virtuales de Azure Resource Manager según una programación y supervisan de forma proactiva la información de Log Analytics."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/07/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0fec06e4a167e615381fca17def46923d9f0f1b


---
# <a name="startstop-vms-during-offhours-preview-solution-in-automation"></a>Inicio o detención de máquinas virtuales fuera de las horas de trabajo [versión preliminar] en Automation
La solución de inicio y detención de máquinas virtuales fuera de las horas de trabajo (versión preliminar) inicia y detiene las máquinas virtuales de Azure Resource Manager y proporciona información detallada sobre el éxito de los trabajos de Automation que inician y detienen las máquinas virtuales con Log Analytics de OMS.  

## <a name="prerequisites"></a>Requisitos previos
* Los Runbooks funciona con una [cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md).  La cuenta de ejecución es el método de autenticación preferido puesto que emplea autenticación de certificados en lugar de una contraseña que puede caducar o cambiar con frecuencia.  
* Esta solución solo puede administrar máquinas virtuales que se encuentren en la misma suscripción y grupo de recursos que la cuenta de Automation.  
* Se implementa únicamente en las siguientes regiones de Azure: sudeste de Australia, este de EE. UU., Sudeste asiático y Europa Occidental.  Los Runbooks que administran la programación de máquina virtual pueden dirigirse a máquinas virtuales de cualquier región.  
* Para enviar notificaciones de correo electrónico cuando los Runbooks de máquina virtual terminen de iniciarse y detenerse, se requiere una suscripción de clase empresarial de Office 365.  

## <a name="solution-components"></a>Componentes de soluciones
Esta solución consta de los siguientes recursos que se importarán y agregarán a la cuenta de Automation.

### <a name="runbooks"></a>Runbooks
| Runbook | Description |
| --- | --- |
| CleanSolution-MS-Mgmt-VM |Este Runbook elimina todos los recursos contenidos y las programaciones cuando se dispone a eliminar la solución desde la suscripción. |
| SendMailO365-MS-Mgmt |Este Runbook envía un correo electrónico a través de Office 365 Exchange. |
| StartByResourceGroup-MS-Mgmt-VM |Este Runbook está diseñado para iniciar las máquinas virtuales (tanto clásicas como basadas en ARM) que residen en una lista específica de grupos de recursos de Azure. |
| StopByResourceGroup-MS-Mgmt-VM |Este Runbook está diseñado para detener las máquinas virtuales (tanto clásicas como basadas en ARM) que residen en una lista específica de grupos de recursos de Azure. |

<br>

### <a name="variables"></a>Variables
| Variable | Description |
| --- | --- |
| Runbook **SendMailO365-MS-Mgmt** | |
| SendMailO365-IsSendEmail-MS-Mgmt |Especifica si los Runbooks StartByResourceGroup-MS-Mgmt-VM y StopByResourceGroup-MS-Mgmt-VM pueden enviar notificaciones por correo electrónico cuando finalicen.  Seleccione **True** para habilitar las alertas de correo electrónico y **False** para deshabilitarlas. El valor predeterminado es **False**. |
| Runbook **StartByResourceGroup-MS-Mgmt-VM** | |
| StartByResourceGroup-ExcludeList-MS-Mgmt-VM |Escriba los nombres de máquina virtual que se excluirán de la operación de administración separados por punto y coma (;). Los valores distinguen mayúsculas de minúsculas y admiten caracteres comodín (asterisco). |
| StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt |Texto que se puede anexar al principio del cuerpo del mensaje de correo electrónico. |
| StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt |Especifica el nombre de la cuenta de Automation que contiene el Runbook de correo electrónico.  **No modifique esta variable.** |
| StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt |Especifica el nombre del Runbook de correo electrónico.  Lo usan los Runbooks StartByResourceGroup-MS-Mgmt-VM y StopByResourceGroup-MS-Mgmt-VM para enviar correo electrónico.  **No modifique esta variable.** |
| StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt |Especifica el nombre del grupo de recursos que contiene el Runbook de correo electrónico.  **No modifique esta variable.** |
| StartByResourceGroup SendMailO365 EmailSubject MS Mgmt |Especifica el texto de la línea de asunto del correo electrónico. |
| StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt |Especifica los destinatarios del correo electrónico.  Separe los diversos nombres mediante punto y coma (;). |
| StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM |Escriba los nombres de máquina virtual que se excluirán de la operación de administración separados por punto y coma (;). Los valores distinguen mayúsculas de minúsculas y admiten caracteres comodín (asterisco).  El valor predeterminado (asterisco) incluirá todos los grupos de recursos de la suscripción. |
| StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM |Especifica la suscripción que contiene las máquinas virtuales que se administrarán mediante esta solución.  Debe ser la misma suscripción donde reside la cuenta de Automation de esta solución. |
| Runbook **StopByResourceGroup-MS-Mgmt-VM** | |
| StopByResourceGroup-ExcludeList-MS-Mgmt-VM |Escriba los nombres de máquina virtual que se excluirán de la operación de administración separados por punto y coma (;). Los valores distinguen mayúsculas de minúsculas y admiten caracteres comodín (asterisco). |
| StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt |Texto que se puede anexar al principio del cuerpo del mensaje de correo electrónico. |
| StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt |Especifica el nombre de la cuenta de Automation que contiene el Runbook de correo electrónico.  **No modifique esta variable.** |
| StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt |Especifica el nombre del grupo de recursos que contiene el Runbook de correo electrónico.  **No modifique esta variable.** |
| StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt |Especifica el texto de la línea de asunto del correo electrónico. |
| StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt |Especifica los destinatarios del correo electrónico.  Separe los diversos nombres mediante punto y coma (;). |
| StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM |Escriba los nombres de máquina virtual que se excluirán de la operación de administración separados por punto y coma (;). Los valores distinguen mayúsculas de minúsculas y admiten caracteres comodín (asterisco).  El valor predeterminado (asterisco) incluirá todos los grupos de recursos de la suscripción. |
| StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM |Especifica la suscripción que contiene las máquinas virtuales que se administrarán mediante esta solución.  Debe ser la misma suscripción donde reside la cuenta de Automation de esta solución. |

<br>

### <a name="schedules"></a>Programaciones
| Schedule | Description |
| --- | --- |
| StartByResourceGroup-Schedule-MS-Mgmt |Programación del Runbook StartByResourceGroup, que realiza el inicio de máquinas virtuales administradas por esta solución. |
| StopByResourceGroup-Schedule-MS-Mgmt |Programación del Runbook StopByResourceGroup, que realiza el cierre de las máquinas virtuales administradas por esta solución. |

### <a name="credentials"></a>Credenciales
| Credential: | Description |
| --- | --- |
| O365Credential |Especifica una cuenta de usuario de Office 365 válida para enviar correo electrónico.  Solo es necesario si la variable SendMailO365-IsSendEmail-MS-Mgmt está establecida en **True**. |

## <a name="configuration"></a>Configuración
Realice los pasos siguientes para agregar la solución de inicio y detención de máquinas virtuales fuera de las horas de trabajo [versión preliminar] a la cuenta de Automation y luego configure las variables para personalizar la solución.

1. En la pantalla de inicio de Azure Portal, seleccione el icono **Marketplace**.  Si el icono ya no está anclado a la pantalla de inicio, seleccione **Nuevo** en el panel de navegación izquierdo.  
2. En la hoja Marketplace, escriba **Iniciar VM** en el cuadro de búsqueda y, a continuación, seleccione la solución **Start/Stop VMs during off-hours [Preview]** (Iniciar o detener máquinas virtuales fuera de las horas de trabajo [versión preliminar]) en los resultados de búsqueda.  
3. En la hoja **Start/Stop VMs during off-hours [Preview]** (Iniciar o detener máquinas virtuales fuera de las horas de trabajo [versión preliminar]), revise la información de resumen y luego haga clic en **Crear**.  
4. Aparece la hoja **Agregar solución** donde se le pide que configure la solución antes de importarla en la suscripción de Automation.<br><br> ![Hoja Agregar solución de administración de VM](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5. En la hoja **Agregar solución**, seleccione **Espacio de trabajo** y aquí seleccione un espacio de trabajo de OMS que esté vinculado a la misma suscripción de Azure en la que se encuentra la cuenta de Automation o cree un nuevo espacio de trabajo de OMS.  Si no tiene un espacio de trabajo de OMS, puede seleccionar **Crear nuevo espacio de trabajo** y en la hoja **OMS Workspace** (Espacio de trabajo de OMS) realizar estos pasos: 
   
   * Especifique un nombre para el nuevo **espacio de trabajo de OMS**.
   * Seleccione una **suscripción** a la que vincularlo en la lista desplegable si la opción predeterminada seleccionada no es adecuada.
   * Para **Grupo de recursos**, puede crear un nuevo grupo de recursos o seleccionar uno existente.  
   * Seleccione una **ubicación**.  Actualmente las únicas ubicaciones proporcionadas para selección son **sudeste de Australia**, **este de EE. UU.**, **Sudeste asiático** y **Europa Occidental**.
   * Seleccione un **plan de tarifa**.  La solución se ofrece en dos niveles: gratuito y nivel de pago de OMS.  El nivel gratis tiene un límite sobre la cantidad de datos recopilados diariamente, el período de retención y los minutos de tiempo de ejecución del trabajo de Runbook.  El nivel de pago de OMS no tiene ningún límite sobre la cantidad de datos recopilados a diario.  
     
     > [!NOTE]
     > Aunque el nivel de pago Independiente se muestra como opción, no es aplicable.  Si lo selecciona y continúa con la creación de esta solución en su suscripción, se producirá un error.  Esto se solucionará cuando se lance oficialmente la solución.<br>Si usa esta solución, solo se emplearán minutos de trabajo e ingesta de registros de automatización.  La solución no agrega nodos OMS adicionales a su entorno.  
     > 
     > 
6. Después de proporcionar la información necesaria en la hoja **OMS workspace** (Espacio de trabajo de OMS), haga clic en **Crear**.  Mientras se comprueba la información y se crea el espacio de trabajo, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú.  Volverá a la hoja **Agregar solución**.  
7. En la hoja **Agregar solución**, seleccione **Cuenta de automatización**.  Si va a crear un nuevo espacio de trabajo de OMS, será necesario crear también una nueva cuenta de Automation que se asociará con el nuevo espacio de trabajo de OMS especificado anteriormente, lo que incluye la suscripción, el grupo de recursos y la región de Azure.  Puede seleccionar **Crear una cuenta de automatización** y, en la hoja **Agregar cuenta de automatización**, proporcionar la siguiente información: 
   
   * En el campo **Nombre**, escriba el nombre de la cuenta de Automation.
     
     Todas las demás opciones se rellenan automáticamente en función del espacio de trabajo de OMS seleccionado y no se pueden modificar.  Una cuenta de ejecución de Azure es el método de autenticación predeterminado para los Runbooks incluidos en esta solución.  Después de hacer clic en **Aceptar**, se validan las opciones de configuración y se crea la cuenta de Automation.  Puede realizar un seguimiento de su progreso en **Notificaciones** en el menú. 
     
     Si no, puede seleccionar una cuenta de ejecución de Automation existente.  Observe que la cuenta seleccionada no puede estar vinculada ya a otro espacio de trabajo de OMS, si no aparecerá un mensaje en la hoja para informarle al respecto.  Si ya está vinculada, deberá seleccionar una cuenta de ejecución de Automation diferente o crear una nueva.<br><br> ![Cuenta de Automation ya vinculada al espacio de trabajo de OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>
8. Por último, en la hoja **Agregar solución**, seleccione **Configuración** Aparece la hoja **Parámetros**.  En la hoja **Parámetros**, se le pide que:  
   
   * Especifique los **nombres de grupo de recursos de destino**, que es un nombre de grupo de recursos que contiene las máquinas virtuales que se administrarán mediante esta solución.  Puede especificar más de un nombre y separarlos con punto y coma (los valores distinguen mayúsculas de minúsculas).  Se puede usar un carácter comodín si quiere dirigirse a las máquinas virtuales de todos los grupos de recursos de la suscripción.
   * Seleccione una **programación** que sea una fecha y una hora periódicas para iniciar y detener las máquinas virtuales en los grupos de recursos de destino.  
9. Cuando haya completado la configuración de los valores iniciales necesarios para la solución, seleccione **Crear**.  Todas las configuraciones se validarán y se intentará implementar la solución en su suscripción.  Este proceso puede tardar varios segundos en completarse y puede realizar el seguimiento de su progreso en **Notificaciones** en el menú. 

## <a name="collection-frequency"></a>Frecuencia de recopilación
El trabajo de Automation y los datos de transmisión del trabajo se ingieren en el repositorio de OMS cada cinco minutos.  

## <a name="using-the-solution"></a>Uso de la solución
Al agregar la solución de administración de VM, en el espacio de trabajo de OMS se agregará el icono **StartStopVM View** (Ver StartStopVM) al panel de OMS.  Este icono muestra una representación numérica y gráfica de los trabajos de Runbook de la solución que se han iniciado y han finalizado correctamente.<br><br> ![Icono de StartStopVM View](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png) (Ver StartStopVM) de administración de VM  

En la cuenta de Automation, puede acceder a la solución y administrarla; para ello, seleccione el icono **Soluciones** y luego, en la hoja **Soluciones**, seleccione la solución **Start-Stop-VM[Workspace]** de la lista.<br><br> ![Lista de soluciones de Automation](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

Al seleccionar la solución se muestra la hoja de la solución **Start-Stop-VM[Workspace]**, donde al igual que en su espacio de trabajo de OMS, puede revisar detalles importantes como el icono **StartStopVM**, que muestra una representación numérica y gráfica de los trabajos de Runbook de la solución que se han iniciado y se han completado correctamente.<br><br> ![Hoja de solución de máquina virtual de Automation](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

Desde aquí también puede abrir el espacio de trabajo de OMS y realizar análisis adicionales de los registros de trabajos.  Simplemente haga clic en **Toda la configuración** y, en la hoja **Configuración**, seleccione **Inicio rápido** y luego, en la hoja **Inicio rápido**, seleccione **OMS Portal** (Portal de OMS).   Se abrirá una nueva pestaña o una nueva sesión de explorador y se presentará el espacio de trabajo de OMS asociado con la cuenta de Automation y la suscripción.  

### <a name="configuring-email-notifications"></a>Configuración de notificaciones por correo electrónico
Para habilitar las notificaciones de correo electrónico cuando finalicen los Runbooks de inicio y detención de máquinas virtuales, deberá modificar la credencial **O365Credential** y al menos las siguientes variables:

* SendMailO365-IsSendEmail-MS-Mgmt
* StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
* StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

Para configurar la credencial **O365Credential**, lleve a cabo los pasos siguientes:

1. En la cuenta de Automation, haga clic en **All Settings** (Toda la configuración) en la parte superior de la ventana. 
2. En la hoja **Configuración**, en la sección **Recursos de Automatización**, seleccione **Recursos**. 
3. En la hoja **Recursos**, seleccione el icono **Credenciales** y, en la hoja **Credenciales**, seleccione **O365Credential**.  
4. Escriba un nombre de usuario y una contraseña válidos de Office 365 y, luego, haga clic en **Guardar** para guardar los cambios.  

Para configurar las variables resaltadas anteriormente, realice los pasos siguientes:

1. En la cuenta de Automation, haga clic en **All Settings** (Toda la configuración) en la parte superior de la ventana. 
2. En la hoja **Configuración**, en la sección **Recursos de Automatización**, seleccione **Recursos**. 
3. En la hoja **Recursos**, seleccione el icono **Variables** y, en la hoja **Variables**, seleccione la variable mostrada arriba y luego modifique su valor a continuación de la descripción especificada para ella en la sección [Variables](##variables) anteriormente.  
4. Haga clic en **Guardar** para guardar los cambios en la variable.   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>Modificación de la programación de inicio y apagado
La administración de la programación de inicio y apagado en esta solución sigue los mismos pasos descritos en [Programación de un Runbook en Azure Automation](automation-scheduling-a-runbook.md).  Recuerde que no se puede modificar la configuración de programación.  Deberá deshabilitar la programación existente y crear una nueva y, a continuación, crear un vínculo al Runbook **StartByResourceGroup-MS-Mgmt-VM** o **StopByResourceGroup-MS-Mgmt-VM** al que quiere que se aplique la programación.   

## <a name="log-analytics-records"></a>Registros de Log Analytics
Automation crea dos tipos de registros en el repositorio de OMS.

### <a name="job-logs"></a>Registros de trabajo
| Propiedad | Description |
| --- | --- |
| Autor de llamada |La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| Categoría |Clasificación del tipo de datos.  Para Automation, el valor será JobLogs. |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| JobId |GUID que es el identificador del trabajo de Runbook. |
| operationName |Especifica el tipo de operación realizada en Azure.  Para Automation, el valor será Job. |
| resourceId |Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook. |
| ResourceGroup |Especifica el nombre del grupo de recursos del trabajo de Runbook. |
| ResourceProvider |Especifica el servicio de Azure que proporciona los recursos que puede implementar y administrar.  Para Automation, el valor es Azure Automation. |
| ResourceType |Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook. |
| resultType |El estado del trabajo de Runbook.  Los valores posibles son:<br>Started<br>Stopped<br>Suspended<br>Con error<br>- Correcto |
| resultDescription |Describe el estado de resultado del trabajo de Runbook.  Los valores posibles son:<br>- Se inicia el trabajo<br>- Error del trabajo<br>- Trabajo completado |
| RunbookName |Especifica el nombre del Runbook. |
| SourceSystem |Especifica el sistema de origen para los datos enviados.  Para Automation, el valor será OpsManager. |
| StreamType |Especifica el tipo de evento. Los valores posibles son:<br>- Detallado<br>- Salida<br>error<br>Warning (Advertencia) |
| SubscriptionId |Especifica el identificador de suscripción del trabajo. |
| Hora |Fecha y hora en que se ejecuta el trabajo de Runbook. |

### <a name="job-streams"></a>Transmisiones de trabajo
| Propiedad | Description |
| --- | --- |
| Autor de llamada |La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| Categoría |Clasificación del tipo de datos.  Para Automation, el valor es JobStreams. |
| JobId |GUID que es el identificador del trabajo de Runbook. |
| operationName |Especifica el tipo de operación realizada en Azure.  Para Automation, el valor será Job. |
| ResourceGroup |Especifica el nombre del grupo de recursos del trabajo de Runbook. |
| resourceId |Especifica el id. de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook. |
| ResourceProvider |Especifica el servicio de Azure que proporciona los recursos que puede implementar y administrar.  Para Automation, el valor es Azure Automation. |
| ResourceType |Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook. |
| resultType |El resultado del trabajo de Runbook en el momento en que se generó el evento.  Los valores posibles son:<br>- En curso |
| resultDescription |Incluye la secuencia de salida del Runbook. |
| RunbookName |El nombre del Runbook. |
| SourceSystem |Especifica el sistema de origen para los datos enviados.  Para Automation, el valor será OpsManager. |
| StreamType |El tipo de flujo de trabajo. Los valores posibles son:<br>progreso<br>- Salida<br>Warning (Advertencia)<br>Error<br>DEBUG<br>- Detallado |
| Hora |Fecha y hora en que se ejecuta el trabajo de Runbook. |

Cuando se realiza cualquier búsqueda de registros que devuelve registros de la categoría **JobLogs** o **JobStreams**, puede seleccionar la vista **JobLogs** o **JobStreams** que muestra un conjunto de iconos que resumen las actualizaciones devueltas en la búsqueda.

## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo
En la tabla siguiente se proporcionan búsquedas de registros de ejemplo para los registros de trabajo recopilados por esta solución. 

| Consultar | Description |
| --- | --- |
| Buscar trabajos del Runbook StartVM que se hayan completado correctamente |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g |
| Buscar trabajos del Runbook StopVM que se hayan completado correctamente |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g |
| Mostrar estado del trabajo con el tiempo para los Runbooks StartVM y StopVM |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") |

## <a name="next-steps"></a>Pasos siguientes
* Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de trabajos de Automatización con Log Analytics, consulte [Búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md)
* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md)
* Para más información acerca de Log Analytics de OMS y de los orígenes de recopilación de datos, consulte [Conexión de Almacenamiento de Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)




<!--HONumber=Nov16_HO2-->


