---
title: "Mi primer runbook gráfico en Azure Automation | Microsoft Docs"
description: "Tutorial que le guiará a través de la creación, prueba y publicación de un runbook gráfico simple."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: "runbook, plantilla de runbook, automatización de runbooks, runbook de azure"
ms.assetid: dcb88f19-ed2b-4372-9724-6625cd287c8a
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 948510eaaf55854bbc14d49bf78a8584c43f182d
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="my-first-graphical-runbook"></a>Mi primer runbook gráfico

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Este tutorial le guiará por la creación de un [runbook gráfico](automation-runbook-types.md#graphical-runbooks) en Azure Automation. Puede comenzar con un runbook simple que prueba y publica mientras aprende a cómo hacer un seguimiento del estado del trabajo del runbook. A continuación, puede modificar el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. Después, puede completar el tutorial haciendo el runbook más sólido con la adición de parámetros de runbook y vínculos condicionales.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Cuenta de Automation](automation-offering-get-started.md) para contener el Runbook y autenticarse en recursos de Azure. Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Una máquina virtual de Azure. Puede detener e iniciar esta máquina, por lo que no debería ser una máquina virtual de producción.

## <a name="create-runbook"></a>Creación de runbook

Empiece creando un runbook simple cuya salida sea el texto *Hola mundo*.

1. En Azure Portal, abra su cuenta de Automation.

   La página de la cuenta de Automation proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de esos recursos son los módulos que se incluyen automáticamente en una cuenta nueva de Automation. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).

2. Seleccione **Runbooks** en **ADMINISTRACIÓN DE PROCESOS** para abrir la lista de runbooks.
3. Para crear un runbook, seleccione **+ Agregar un Runbook** y luego haga clic en **Crear un runbook nuevo**.
4. Asigne al runbook el nombre *MyFirstRunbook-Graphical*.
5. En este caso, va a crear un [runbook gráfico](automation-graphical-authoring-intro.md), así que seleccione **Gráfico** en **Tipo de runbook**.<br> ![Nuevo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Haga clic en **Crear** para crear el runbook y abrir el editor gráfico.

## <a name="add-activities"></a>Adición de actividades

El control Biblioteca en la parte izquierda del editor permite seleccionar actividades para agregarlas al Runbook. Va a agregar un cmdlet **Write-Output** para generar el texto desde el Runbook.

1. En el control Biblioteca, haga clic en el cuadro de texto de búsqueda y escriba **Write-Output**. Los resultados de la búsqueda se muestran en la siguiente imagen: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Desplácese hasta la parte inferior de la lista. Haga clic con el botón derecho en **Write-Output** y seleccione **Agregar al lienzo** o haga clic en los puntos suspensivos junto al cmdlet y seleccione **Agregar al lienzo**.
1. Haga clic en la actividad **Write-Output** en el lienzo. Esta acción abre la página Control de configuración, que le permite configurar la actividad.
1. El campo **Etiqueta** tiene como valor predeterminado el nombre del cmdlet, pero se puede cambiar a algo más descriptivo. Cámbielo a *Escribir Hello World para la salida*.
1. Haga clic en **Parámetros** para especificar los valores de los parámetros del cmdlet.

   Algunos cmdlets tienen varios conjuntos de parámetros; debe seleccionar el que usará. En este caso, **Write-Output** solo tiene un conjunto de parámetros, por lo que no es necesario seleccionar uno.

1. Seleccione el parámetro **InputObject** . Este es el parámetro donde especifica el texto que va a enviar al flujo de salida.
1. En la lista desplegable **Origen de datos**, seleccione **PowerShell expression** (Expresión de PowerShell). La lista desplegable **Origen de datos** proporciona diferentes orígenes que se usan para rellenar un valor de parámetro.

   Puede usar la salida de esos orígenes como otra actividad, un recurso de Automation o una expresión de PowerShell. En este caso, la salida es simplemente *Hola mundo*. Puede usar una expresión de PowerShell y especificar una cadena.<br>

1. En el cuadro **Expresión**, escriba *"Hello World"* y haga clic en **Aceptar** dos veces para volver al lienzo.
1. Guarde el Runbook, para lo que debe hacer clic en **Guardar**.

## <a name="test-the-runbook"></a>Probar el runbook

Antes de publicar el runbook para que esté disponible en producción, puede que quiera probarlo para asegurarse de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1. Seleccione **Panel de prueba** para abrir la página Probar.
1. Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
1. Se crea un [trabajo de runbook](automation-runbook-execution.md) y su estado se muestra en el panel.

   El estado del trabajo se inicia como *En cola*, lo que indica que está esperando a que haya disponible un trabajo de runbook en la nube. Su estado cambia a *Iniciando* cuando un trabajo de runbook solicita el trabajo. Cuando el runbook comienza a ejecutarse realmente, el estado es *En ejecución*.

1. Cuando se complete el trabajo del runbook, se mostrará su resultado. En este caso, verá *Hola mundo*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Cierre la página Probar para volver al lienzo.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar el runbook

El runbook que acaba de crear aún está en modo de borrador. Debe publicarse antes de ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En este caso, no tiene una versión publicada aún porque acaba de crear el runbook.

1. Seleccione **Publicar** para publicar el runbook y **Sí** cuando se le solicite.
1. Si se desplaza a la izquierda para ver el runbook en la página **Runbooks**, se muestra **Publicado** en **Estado de creación**.
1. Vuelva a desplazarse a la derecha para ver la página de **MyFirstRunbook**.

   Las opciones en la parte superior nos permiten iniciar el runbook, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.

1. Para iniciar el runbook, seleccione **Iniciar** y **Sí** cuando se le solicite.
1. Se abre una página de trabajo para el trabajo de runbook creado. Compruebe que en **Estado del trabajo** aparece **Completado**.
1. Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. Se abre la página **Salida** y puede ver el texto *Hola mundo* en el panel.
1. Cierre la página Salida.
1. Haga clic en **Todos los registros** para abrir la página Transmisiones para el trabajo de runbook. Solo debería ver *Hola mundo* en el flujo de salida, pero se pueden mostrar otras transmisiones de un trabajo de Runbook como Detallado y Error si el Runbook escribe en ellas.
1. Cierre las páginas Todos los registros y Trabajo para volver a la página MyFirstRunbook.
1. Para ver todos los trabajos del runbook, cierre la página **Trabajo** y seleccione **Trabajos** en **RECURSOS**. Enumera todos los trabajos creados por este runbook. Solo debería ver un trabajo en la lista ya que solo ejecutó el trabajo una vez.
1. Puede hacer clic en este trabajo para abrir el mismo panel Trabajo que vio cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## <a name="create-variable-assets"></a>Creación de recursos de variables

Ha probado y publicado su runbook, pero hasta ahora no hace nada útil. Quiere que administre recursos de Azure. Antes de configurar el runbook que se va a autenticar, cree una variable que contenga el identificador de suscripción y haga referencia a él después de configurar la actividad que se va a autenticar en el paso 6 a continuación. Incluir una referencia al contexto de la suscripción le permite trabajar fácilmente entre varias suscripciones. Antes de continuar, copie el identificador de suscripción de la opción Suscripciones en el panel Navegación.

1. En la página Cuentas de Automatización, seleccione **Variables** en **RECURSOS COMPARTIDOS**.
1. Seleccione **Agregar una variable**.
1. En la página Nueva variable, en el cuadro **Nombre**, escriba **AzureSubscriptionId** y, en el cuadro **Valor**, escriba su identificador de suscripción. Mantenga *cadena* como **Tipo** y el valor predeterminado en **Cifrado**.
1. Haga clic en **Crear** para crear la variable.

## <a name="add-authentication"></a>Agregar autenticación

Ahora que tiene una variable que contiene el identificador de suscripción, puede configurar el Runbook para que se autentique con las credenciales de ejecución a las que se hace referencia en los [requisitos previos](#prerequisites). Lo hace al agregar la conexión de ejecución de Azure como **Recurso** y el cmdlet **Add-AzureRMAccount** al lienzo.

1. Vuelva al runbook y seleccione **Editar** en la página MyFirstRunbook.
1. Ya no necesita **Escribir Hola mundo para la salida**, así que haga clic en el botón de puntos suspensivos (...) y seleccione **Eliminar**.
1. En el control Biblioteca, expanda **RECURSOS**, **Conexiones** y agregue **AzureRunAsConnection** al lienzo mediante la selección de **Agregar a lienzo**.
1. En el control Biblioteca, escriba **Add-AzureRmAccount** en el cuadro de texto de búsqueda.
1. Agregue **Agregue-AzureRmAccount** al lienzo.
1. Mantenga el puntero sobre **Get Run As Connection** (Obtener conexión de ejecución) hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Add-AzureRmAccount**. La flecha que ha creado es un *vínculo*. Se inicia el runbook con **Get Run As Connection** (Obtener conexión de ejecución); después, ejecute **Add-AzureRmAccount**.<br> ![Crear vínculo entre actividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. En el lienzo, seleccione **Add-AzureRmAccount** y, en el panel Control de configuración, escriba **Login to Azure** (Iniciar sesión en Azure) en el cuadro de texto **Etiqueta**.
1. Haga clic en **Parámetros**; aparece la página Configuración de parámetros de la actividad.
1. **Add-AzureRmAccount** tiene varios conjuntos de parámetros, por lo que necesita seleccionar uno antes de poder proporcionar los valores de los parámetros. Haga clic en **Conjunto de parámetros** y seleccione el conjunto de parámetros **ServicePrincipalCertificate**.
1. Una vez seleccionado el conjunto de parámetros, estos se muestran en la página Configuración de parámetros de la actividad. Haga clic en **APPLICATIONID**.<br> ![Agregar parámetros de cuenta de Azure Resource Manager](media/automation-first-runbook-graphical/add-azurermaccount-params.png)
1. En la página Valor de parámetro, seleccione **Resultado de la actividad** en **Origen de datos**, seleccione **Get Run As Connection** (Obtener conexión de ejecución) en la lista, en el cuadro de texto **Field path** (Ruta de acceso de campo), escriba **ApplicationId** y, finalmente, haga clic en **Aceptar**. Está especificando el nombre de la propiedad para la ruta de acceso del campo porque la actividad genera un objeto con varias propiedades.
1. Haga clic en **CERTIFICATETHUMBPRINT** y, en la página Valor de parámetro, seleccione **Resultado de la actividad** en **Origen de datos**. Seleccione **Get Run As Connection** (Obtener conexión de ejecución) en la lista; en el cuadro de texto **Field path** (Ruta de acceso de campo), escriba **CertificateThumbprint** y haga clic en **Aceptar**.
1. Haga clic en **SERVICEPRINCIPAL** y, en la página Valor de parámetro, seleccione **ConstantValue** en **Origen de datos**, haga clic en la opción **True** y en **Aceptar**.
1. Haga clic en **TENANTID** y, en la página Valor de parámetro, seleccione **Resultado de la actividad** en **Origen de datos**. Seleccione **Get Run As Connection** (Obtener conexión de ejecución) en la lista; en el cuadro de texto **Field path** (Ruta de acceso de campo), escriba **TenantId** y haga clic en **Aceptar** dos veces.
1. En el control Biblioteca, escriba **Set-AzureRmContext** en el cuadro de texto de búsqueda.
1. Agregue **Set-AzureRmContext** al lienzo.
1. En el lienzo, seleccione **Set-AzureRmContext** y, en el panel Control de configuración, escriba **Specify Subscription Id** (Especificar id. de suscripción) en el cuadro de texto **Etiqueta**.
1. Haga clic en **Parámetros**; aparece la página Configuración de parámetros de la actividad.
1. **Set-AzureRmContext** tiene varios conjuntos de parámetros, por lo que necesita seleccionar uno antes de poder proporcionar los valores de los parámetros. Haga clic en **Conjunto de parámetros** y seleccione el conjunto de parámetros **SubscriptionId**.
1. Una vez seleccionado el conjunto de parámetros, estos se muestran en la página Configuración de parámetros de la actividad. Haga clic en **SubscriptionID**
1. En la página Valor de parámetro, seleccione **Recurso de variable** en **Origen de datos** y seleccione **AzureSubscriptionId** en la lista y haga clic en **Aceptar** dos veces.
1. Mantenga el puntero sobre **Login to Azure** (Iniciar sesión en Azure) hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Specify Subscription Id** (Especificar id. de suscripción).

En este punto, el Runbook debe tener el siguiente aspecto:  <br>![Configuración de autenticación de Runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Adición de una actividad para iniciar una máquina virtual

Aquí puede agregar una actividad **Start-AzureRmVM** para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual en su suscripción de Azure. Por ahora, va a codificar ese nombre en el cmdlet.

1. En el control Biblioteca, escriba **Start-AzureRm** en el cuadro de texto de búsqueda.
2. Agregue **Start-AzureRmVM** al lienzo y haga clic y arrástrelo para colocarlo debajo de **Specify Subscription Id** (Especificar id. de suscripción).
1. Mantenga el puntero sobre **Specify Subscription Id** (Especificar id. de suscripción) hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Start-AzureRmVM**.
1. Seleccione **Start-AzureRmVM**. Haga clic en **Parámetros** y en **Conjunto de parámetros** para ver los conjuntos de **Start-AzureRmVM**. Seleccione el conjunto de parámetros **ResourceGroupNameParameterSetName** . **ResourceGroupName** y **Name** tienen signos de exclamación al lado. Esto indica que son parámetros necesarios. Tenga en cuenta que ambos esperan valores de cadena.
1. Seleccione **Name**. Seleccione **Expresión de PowerShell** como **Origen de datos** y escriba el nombre, entre comillas dobles, de la máquina virtual que inicie con este runbook. Haga clic en **OK**.
1. Seleccione **ResourceGroupName**. Utilice **Expresión de PowerShell** como **Origen de datos** y escriba el nombre del grupo de recursos entre comillas dobles. Haga clic en **OK**.
1. Haga clic en el panel Prueba para que podemos probar el runbook.
1. Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

En este punto, el Runbook debe tener el siguiente aspecto:  <br>![Configuración de autenticación de Runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Adición de parámetros de entrada adicionales

Actualmente, el runbook inicia la máquina virtual en el grupo de recursos que especificó en el cmdlet **Start-AzureRmVM**. El runbook sería más útil si pudiéramos especificar ambos cuando se inicia el runbook. Ahora puede agregar parámetros de entrada al runbook para proporcionar esa funcionalidad.

1. Abra el editor gráfico haciendo clic en **Editar** en el panel **MyFirstRunbook**.
1. Seleccione **Entrada y salida**, y en **Agregar entrada** para abrir el panel Parámetros de entrada de Runbook.
1. Especifique *VMName* para el campo **Name**. Mantenga *cadena* en **Tipo** y cambie **Obligatorio** a *Sí*. Haga clic en **OK**.
1. Cree un segundo parámetro de entrada obligatorio denominado *ResourceGroupName* y haga clic en **Aceptar** para cerrar el panel **Entrada y salida**.<br> ![Parámetros de entrada de runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Seleccione la actividad **Start-AzureRmVM** y haga clic en **Parámetros**.
1. Cambie **Origen de datos** de **Nombre** a **Entrada de runbook** y seleccione **VMName**.
1. Cambie **Origen de datos** de **ResourceGroupName** a **Entrada de runbook** y seleccione **ResourceGroupName**.<br> ![Parámetros Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Guarde el runbook y abra el panel Prueba. Ahora puede proporcionar valores para las dos variables de entrada que se usan en la prueba.
1. Cierre el panel Prueba.
1. Haga clic en **Publicar** para publicar la nueva versión del runbook.
1. Detenga la máquina virtual que inició en el paso anterior.
1. Haga clic en **Iniciar** para iniciar el runbook. Escriba el valor de **VMName** y **ResourceGroupName** para la máquina virtual que va a iniciar.
1. Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.

## <a name="create-a-conditional-link"></a>Creación de un vínculo condicional

Ahora puede modificar el runbook para que solo intente iniciar la máquina virtual si aún no se ha iniciado. Para hacerlo, agregamos al runbook un cmdlet **Get-AzureRmVM**, con el que se obtiene el estado de nivel de instancia de la máquina virtual. A continuación, agregue un módulo de código de Flujo de trabajo de PowerShell denominado **Get Status** con un fragmento de código de PowerShell para determinar si la máquina virtual está en ejecución o detenida. Un vínculo condicional desde el módulo **Get Status** solo ejecuta **Start-AzureRmVM** si el estado de ejecución actual es detenido. Por último, se muestra un mensaje que indica si la máquina virtual se inició correctamente o no mediante el cmdlet Write-Output de PowerShell.

1. Abra **MyFirstRunbook** en el editor gráfico.
1. Para quitar el vínculo entre **Specify Subscription Id** (Especificar id. de suscripción) y **Start-AzureRmVM**, haga clic en él y presione la tecla *Suprimir*.
1. En el control Biblioteca, escriba **Get-AzureRm** en el cuadro de texto de búsqueda.
1. Agregue **Get-AzureRmVM** al lienzo.
1. Seleccione **Get-AzureRmVM** y después **Conjunto de parámetros** para ver los conjuntos de **Get-AzureRmVM**. Seleccione el conjunto de parámetros **GetVirtualMachineInResourceGroupNameParamSet** . **ResourceGroupName** y **Name** tienen signos de exclamación al lado. Esto indica que son parámetros necesarios. Tenga en cuenta que ambos esperan valores de cadena.
1. En **Origen de datos** de **Nombre**, seleccione **Entrada de runbook** y **VMName**. Haga clic en **OK**.
1. En **Origen de datos** de **ResourceGroupName**, seleccione **Entrada de runbook** y **ResourceGroupName**. Haga clic en **OK**.
1. En **Origen de datos** para **Estado**, seleccione **Valor constante** y haga clic en **Verdadero**. Haga clic en **OK**.
1. Cree un vínculo de **Specify Subscription Id** (Especificar id. de suscripción) a **Get-AzureRmVM**.
1. En el control Biblioteca, expanda **Control de Runbook** y agregue **Código** al lienzo.  
1. Cree un vínculo de **Get-AzureRmVM** a **Código**.  
1. Haga clic en **Código** y, en el panel Configuración, cambie la etiqueta a **Get Status**.
1. Seleccione el parámetro **Código**; aparece la página **Editor de código**.  
1. En el editor de código, pegue el siguiente fragmento de código:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Cree un vínculo de **Get Status** a **Start-AzureRmVM**.<br> ![Runbook con el módulo de código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Seleccione el vínculo y, en el panel Configuración, cambie **Aplicar condición** a **Sí**. Tenga en cuenta que el vínculo se convierte en una línea discontinua que indica que la actividad de destino solo se ejecuta si la condición se resuelve como verdadera.  
1. En **Expresión de condición**, escriba *$ActivityOutput['Get Status'] -eq "Stopped"*. **Start-AzureRmVM** ahora solo se ejecuta si la máquina virtual está detenida.
1. En el control Biblioteca, expanda **Cmdlets** y **Microsoft.PowerShell.Utility**.
1. Agregue **Write-Output** al lienzo dos veces.
1. En el primer control **Write-Output**, haga clic en **Parámetros** y cambie el valor de **Etiqueta** a *Notify VM Started* (Notificar VM iniciada).
1. Para **InputObject**, cambie **Origen de datos** a **PowerShell expression** (Expresión de PowerShell) y escriba la expresión *"$VMName successfully started."* ($VMName iniciada correctamente).
1. En el segundo control **Write-Output**, haga clic en **Parámetros** y cambie el valor de **Etiqueta** a *Notify VM Start Failed* (Notificar error al iniciar VM).
1. En **InputObject**, cambie **Origen de datos** a **PowerShell expression** (Expresión de PowerShell) y escriba la expresión *"$VMName could not start."* ($VMName no se pudo iniciar).
1. Cree un vínculo de **Start-AzureRmVM** a **Notify VM Started** (Notificar VM iniciada) y **Notify VM Start Failed** (Notificar error al iniciar VM).
1. Seleccione el vínculo a **Notify VM Started** (Notificar VM iniciada) y cambie **Aplicar condición** a **Verdadero**.
1. En **Expresión de condición**, escriba *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Este control Write-Output ahora se ejecuta solo si la máquina virtual se ha iniciado correctamente.
1. Seleccione el vínculo a **Notify VM Start Failed** (Notificar error al iniciar VM) y cambie **Aplicar condición** a **Verdadero**.
1. En **Expresión de condición**, escriba *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Este control Write-Output ahora se ejecuta solo si la máquina virtual no se ha iniciado correctamente. El runbook debe tener un aspecto similar al de la imagen siguiente: <br> ![Runbook con Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Guarde el runbook y abra el panel Prueba.
1. Inicie el runbook con la máquina virtual detenida- Se debería iniciar.

## <a name="next-steps"></a>pasos siguientes

* Para obtener más información sobre la creación de gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md)
* Para empezar a trabajar con Runbooks de PowerShell, consulte [Mi primer Runbook de PowerShell](automation-first-runbook-textual-powershell.md)
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer Runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)

