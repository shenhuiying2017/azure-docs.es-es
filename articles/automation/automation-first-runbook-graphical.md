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
ms.topic: get-started-article
ms.date: 05/17/2017
ms.author: magoedte;bwren
ms.openlocfilehash: ab4ca2bf37e30bff66de7acc3932e3cb89b6e93b
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
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
* 
            [Cuenta de Automation](automation-offering-get-started.md) para contener el Runbook y autenticarse en recursos de Azure. Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Una máquina virtual de Azure. Puede detener e iniciar esta máquina, por lo que no debería ser una máquina virtual de producción.

## <a name="step-1---create-runbook"></a>Paso 1: Creación del runbook
Empiece creando un runbook simple cuya salida sea el texto *Hola mundo*.

1. En Azure Portal, abra su cuenta de Automation.

   La página de la cuenta de Automation proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de esos recursos son los módulos que se incluyen automáticamente en una cuenta nueva de Automation. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).

2. Seleccione **Runbooks** en **ADMINISTRACIÓN DE PROCESOS** para abrir la lista de runbooks.
3. Para crear un runbook, haga clic en el botón **Agregar un runbook** y en **Crear un nuevo runbook**.
4. Asigne al runbook el nombre *MyFirstRunbook-Graphical*.
5. En este caso, va a crear un [runbook gráfico](automation-graphical-authoring-intro.md), así que seleccione **Gráfico** en **Tipo de runbook**.<br> ![Nuevo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Haga clic en **Crear** para crear el runbook y abrir el editor gráfico.

## <a name="step-2---add-activities-to-the-runbook"></a>Paso 2:  agregar actividades al runbook
El control Biblioteca en la parte izquierda del editor permite seleccionar actividades para agregarlas al Runbook. Va a agregar un cmdlet **Write-Output** para generar el texto desde el Runbook.

1. En el control Biblioteca, haga clic en el cuadro de texto de búsqueda y escriba **Write-Output**. Los resultados de la búsqueda se muestran en la siguiente imagen: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2. Desplácese hasta la parte inferior de la lista. Haga clic con el botón derecho en **Write-Output** y seleccione **Agregar al lienzo** o haga clic en los puntos suspensivos junto al cmdlet y seleccione **Agregar al lienzo**.
3. Haga clic en la actividad **Write-Output** en el lienzo. Esta acción abre la hoja Control de configuración, que le permite configurar la actividad.
4. El campo **Etiqueta** tiene como valor predeterminado el nombre del cmdlet, pero se puede cambiar a algo más descriptivo. Cámbielo a *Escribir Hello World para la salida*.
5. Haga clic en **Parámetros** para especificar los valores de los parámetros del cmdlet.

   Algunos cmdlets tienen varios conjuntos de parámetros; debe seleccionar el que usará. En este caso, **Write-Output** solo tiene un conjunto de parámetros, por lo que no es necesario seleccionar uno. <br> ![Propiedades Write-Output](media/automation-first-runbook-graphical/write-output-properties-b.png)

6. Seleccione el parámetro **InputObject** . Este es el parámetro donde especifica el texto que va a enviar al flujo de salida.
7. En la lista desplegable **Origen de datos**, seleccione **PowerShell expression** (Expresión de PowerShell). La lista desplegable **Origen de datos** proporciona diferentes orígenes que se usan para rellenar un valor de parámetro.

   Puede usar la salida de esos orígenes como otra actividad, un recurso de Automation o una expresión de PowerShell. En este caso, la salida es simplemente *Hola mundo*. Puede usar una expresión de PowerShell y especificar una cadena.

8. En el cuadro **Expresión**, escriba *"Hello World"* y haga clic en **Aceptar** dos veces para volver al lienzo.<br> ![PowerShell Expression](media/automation-first-runbook-graphical/expression-hello-world.png)
9. Guarde el Runbook, para lo que debe hacer clic en **Guardar**.<br> ![Guardar runbook](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## <a name="step-3---test-the-runbook"></a>Paso 3: probar el runbook
Antes de publicar el runbook para que esté disponible en producción, puede que quiera probarlo para asegurarse de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1. Haga clic en **Panel de prueba** para abrir la hoja Prueba.<br> ![Panel Prueba](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2. Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
3. Se crea un [trabajo de runbook](automation-runbook-execution.md) y su estado se muestra en el panel.

   El estado del trabajo se inicia como *En cola*, lo que indica que está esperando a que haya disponible un trabajo de runbook en la nube. Su estado cambia a *Iniciando* cuando un trabajo de runbook solicita el trabajo. Cuando el runbook comienza a ejecutarse realmente, el estado es *En ejecución*.

4. Cuando se complete el trabajo del runbook, se mostrará su resultado. En este caso, verá *Hola mundo*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5. Cierre la hoja Probar para volver al lienzo.

## <a name="step-4---publish-and-start-the-runbook"></a>Paso 4: publicar e iniciar el runbook
El runbook que acaba de crear aún está en modo de borrador. Debe publicarse antes de ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En este caso, no tiene una versión publicada aún porque acaba de crear el runbook.

1. Haga clic en **Publicar** para publicar el runbook y en **Sí** cuando se le solicite.<br> ![Publicar](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2. Si se desplaza a la izquierda para ver el runbook en la hoja **Runbooks**, se muestra **Publicado** en **Estado de creación**.
3. Vuelva a desplazarse a la derecha para ver la hoja de **MyFirstRunbook**.

   Las opciones en la parte superior nos permiten iniciar el runbook, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.

4. Inicie el runbook. Para ello, haga clic en **Iniciar** y en **Sí** cuando se le solicite.<br> ![Iniciar runbook](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5. Se abre una hoja de trabajo para el trabajo de runbook que se ha creado. Compruebe que en **Estado del trabajo** aparece **Completado**.
7. Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. Se abre la hoja **Salida** y puede ver el texto *Hola mundo* en el panel.
8. Cierre la hoja Salida.
9. Haga clic en **Todos los registros** para abrir la hoja Transmisiones para el trabajo de Runbook. Solo debería ver *Hola mundo* en el flujo de salida, pero se pueden mostrar otras transmisiones de un trabajo de Runbook como Detallado y Error si el Runbook escribe en ellas.
10. Cierre las hojas Todos los registros y Trabajo para volver a la hoja MyFirstRunbook.
11. Para ver todos los trabajos del runbook, cierre la hoja **Trabajo** y seleccione **Trabajos** en **RECURSOS**. Enumera todos los trabajos creados por este runbook. Solo debería ver un trabajo en la lista ya que solo ejecutó el trabajo una vez.
12. Puede hacer clic en este trabajo para abrir el mismo panel Trabajo que vio cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## <a name="step-5---create-variable-assets"></a>Paso 5: Creación de recursos de variables
Ha probado y publicado su runbook, pero hasta ahora no hace nada útil. Quiere que administre recursos de Azure. Antes de configurar el runbook que se va a autenticar, cree una variable que contenga el identificador de suscripción y haga referencia a él después de configurar la actividad que se va a autenticar en el paso 6 a continuación. Incluir una referencia al contexto de la suscripción le permite trabajar fácilmente entre varias suscripciones. Antes de continuar, copie el identificador de suscripción de la opción Suscripciones en el panel Navegación.

1. En la hoja Cuentas de Automation, seleccione **Variables** en **RECURSOS COMPARTIDOS**.
2. Haga clic en **Agregar una variable**.<br>
            ![Variable de Automation](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. En la hoja Nueva variable, en el cuadro **Nombre**, escriba **AzureSubscriptionId** y en el cuadro **Valor** escriba su identificador de suscripción. Mantenga *cadena* como **Tipo** y el valor predeterminado en **Cifrado**.  
5. Haga clic en **Crear** para crear la variable.  

## <a name="step-6---add-authentication-to-manage-azure-resources"></a>Paso 6: Adición de autenticación para administrar recursos de Azure
Ahora que tiene una variable que contiene el identificador de suscripción, puede configurar el Runbook para que se autentique con las credenciales de ejecución a las que se hace referencia en los [requisitos previos](#prerequisites). Lo hace al agregar la conexión de ejecución de Azure como **Recurso** y el cmdlet **Add-AzureRMAccount** al lienzo.  

1. Vuelva l Runbook y seleccione **Editar** en la hoja de MyFirstRunbook.<br> ![Editar runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2. Ya no necesita **Escribir Hola mundo para la salida**, así que haga clic en el botón de puntos suspensivos y seleccione **Eliminar**.
3. En el control Biblioteca, expanda **RECURSOS**, **Conexiones** y agregue **AzureRunAsConnection** al lienzo mediante la selección de **Agregar a lienzo**.
5. En el control Biblioteca, escriba **Add-AzureRmAccount** en el cuadro de texto de búsqueda.
6. Agregue **Agregue-AzureRmAccount** al lienzo.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7. Mantenga el puntero sobre **Get Run As Connection** (Obtener conexión de ejecución) hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Add-AzureRmAccount**. La flecha que ha creado es un *vínculo*. Se inicia el runbook con **Get Run As Connection** (Obtener conexión de ejecución); después, ejecute **Add-AzureRmAccount**.<br> ![Crear vínculo entre actividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8. En el lienzo, seleccione **Add-AzureRmAccount** y, en el panel Control de configuración, escriba **Login to Azure** (Iniciar sesión en Azure) en el cuadro de texto **Etiqueta**.
9. Haga clic en **Parámetros** ; aparece la hoja Configuración de parámetros de la actividad.
10. **Add-AzureRmAccount** tiene varios conjuntos de parámetros, por lo que necesita seleccionar uno antes de poder proporcionar los valores de los parámetros. Haga clic en **Conjunto de parámetros** y seleccione el conjunto de parámetros **ServicePrincipalCertificate**.
11. Una vez seleccionado el conjunto de parámetros, los parámetros se muestran en la hoja Activity Parameter Configuration (Configuración de parámetros de actividad). Haga clic en **APPLICATIONID**.<br> ![Agregar parámetros de cuenta de Azure Resource Manager](media/automation-first-runbook-graphical/add-azurermaccount-params.png)
12. En la hoja Valor de parámetro, seleccione **Activity output** (Salida de la actividad) en **Origen de datos**, seleccione **Get Run As Connection** (Obtener conexión de ejecución) en la lista, en el cuadro de texto **Field path** (Ruta de acceso de campo), escriba **ApplicationId** y, finalmente, haga clic en **Aceptar**. Está especificando el nombre de la propiedad para la ruta de acceso del campo porque la actividad genera un objeto con varias propiedades.
13. Haga clic en **CERTIFICATETHUMBPRINT** y, en la hoja Valor de parámetro, seleccione **Activity output** (Salida de la actividad) en **Origen de datos**. Seleccione **Get Run As Connection** (Obtener conexión de ejecución) en la lista; en el cuadro de texto **Field path** (Ruta de acceso de campo), escriba **CertificateThumbprint** y haga clic en **Aceptar**.
14. Haga clic en **SERVICEPRINCIPAL** y, en la hoja Valor de parámetro, seleccione **ConstantValue** en **Origen de datos**, haga clic en la opción **Verdadero** y en **Aceptar**.
15. Haga clic en **TENANTID** y, en la hoja Valor de parámetro, seleccione **Activity output** (Salida de la actividad) en **Origen de datos**. Seleccione **Get Run As Connection** (Obtener conexión de ejecución) en la lista; en el cuadro de texto **Field path** (Ruta de acceso de campo), escriba **TenantId** y haga clic en **Aceptar** dos veces.  
16. En el control Biblioteca, escriba **Set-AzureRmContext** en el cuadro de texto de búsqueda.
17. Agregue **Set-AzureRmContext** al lienzo.
18. En el lienzo, seleccione **Set-AzureRmContext** y, en el panel Control de configuración, escriba **Specify Subscription Id** (Especificar id. de suscripción) en el cuadro de texto **Etiqueta**.
19. Haga clic en **Parámetros** ; aparece la hoja Configuración de parámetros de la actividad.
20. **Set-AzureRmContext** tiene varios conjuntos de parámetros, por lo que necesita seleccionar uno antes de poder proporcionar los valores de los parámetros. Haga clic en **Conjunto de parámetros** y seleccione el conjunto de parámetros **SubscriptionId**.  
21. Una vez seleccionado el conjunto de parámetros, los parámetros se muestran en la hoja Activity Parameter Configuration (Configuración de parámetros de actividad). Haga clic en **SubscriptionID**
22. En la hoja Valor de parámetro, seleccione **Recurso de variable** en **Origen de datos** y seleccione **AzureSubscriptionId** en la lista y haga clic en **Aceptar** dos veces.   
23. Mantenga el puntero sobre **Login to Azure** (Iniciar sesión en Azure) hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Specify Subscription Id** (Especificar id. de suscripción).

En este punto, el Runbook debe tener el siguiente aspecto:  <br>![Configuración de autenticación de Runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Paso 7: Adición de una actividad para iniciar una máquina virtual
Aquí puede agregar una actividad **Start-AzureRmVM** para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual en su suscripción de Azure. Por ahora, va a codificar ese nombre en el cmdlet.

1. En el control Biblioteca, escriba **Start-AzureRm** en el cuadro de texto de búsqueda.
2. Agregue **Start-AzureRmVM** al lienzo y haga clic y arrástrelo para colocarlo debajo de **Specify Subscription Id** (Especificar id. de suscripción).
3. Mantenga el puntero sobre **Specify Subscription Id** (Especificar id. de suscripción) hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Start-AzureRmVM**.
4. Seleccione **Start-AzureRmVM**. Haga clic en **Parámetros** y en **Conjunto de parámetros** para ver los conjuntos de **Start-AzureRmVM**. Seleccione el conjunto de parámetros **ResourceGroupNameParameterSetName** . **ResourceGroupName** y **Name** tienen signos de exclamación al lado. Esto indica que son parámetros necesarios. Tenga en cuenta que ambos esperan valores de cadena.
5. Seleccione **Name**. Seleccione **Expresión de PowerShell** como **Origen de datos** y escriba el nombre, entre comillas dobles, de la máquina virtual que inicie con este runbook. Haga clic en **Aceptar**.<br>![Valor del parámetro de nombre Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6. Seleccione **ResourceGroupName**. Utilice **Expresión de PowerShell** como **Origen de datos** y escriba el nombre del grupo de recursos entre comillas dobles. Haga clic en **Aceptar**.<br> ![Parámetros Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
7. Haga clic en el panel Prueba para que podemos probar el runbook.
8. Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

En este punto, el Runbook debe tener el siguiente aspecto:  <br>![Configuración de autenticación de Runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters-to-the-runbook"></a>Paso 8: Adición de parámetros de entrada adicionales al Runbook
Actualmente, el runbook inicia la máquina virtual en el grupo de recursos que especificó en el cmdlet **Start-AzureRmVM**. El runbook sería más útil si pudiéramos especificar ambos cuando se inicia el runbook. Ahora puede agregar parámetros de entrada al runbook para proporcionar esa funcionalidad.

1. Abra el editor gráfico haciendo clic en **Editar** en el panel **MyFirstRunbook**.
2. Haga clic en **Entrada y salida**, y en **Agregar entrada** para abrir el panel Parámetro de entrada de runbook.<br> ![Entrada y salida de runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Especifique *VMName* para el campo **Name**. Mantenga *cadena* en **Tipo** y cambie **Obligatorio** a *Sí*. Haga clic en **Aceptar**.
4. Cree un segundo parámetro de entrada obligatorio denominado *ResourceGroupName* y haga clic en **Aceptar** para cerrar el panel **Entrada y salida**.<br> ![Parámetros de entrada de runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Seleccione la actividad **Start-AzureRmVM** y haga clic en **Parámetros**.
6. Cambie **Origen de datos** de **Nombre** a **Entrada de runbook** y seleccione **VMName**.<br>
7. Cambie **Origen de datos** de **ResourceGroupName** a **Entrada de runbook** y seleccione **ResourceGroupName**.<br> ![Parámetros Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Guarde el runbook y abra el panel Prueba. Ahora puede proporcionar valores para las dos variables de entrada que se usan en la prueba.
9. Cierre el panel Prueba.
10. Haga clic en **Publicar** para publicar la nueva versión del runbook.
11. Detenga la máquina virtual que inició en el paso anterior.
12. Haga clic en **Iniciar** para iniciar el runbook. Escriba el valor de **VMName** y **ResourceGroupName** para la máquina virtual que va a iniciar.<br> ![Start Runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.

## <a name="step-9---create-a-conditional-link"></a>Paso 9: Creación de un vínculo condicional
Ahora puede modificar el runbook para que solo intente iniciar la máquina virtual si aún no se ha iniciado. Para hacerlo, agregamos al runbook un cmdlet **Get-AzureRmVM**, con el que se obtiene el estado de nivel de instancia de la máquina virtual. A continuación, agregue un módulo de código de Flujo de trabajo de PowerShell denominado **Get Status** con un fragmento de código de PowerShell para determinar si la máquina virtual está en ejecución o detenida. Un vínculo condicional desde el módulo **Get Status** solo ejecuta **Start-AzureRmVM** si el estado de ejecución actual es detenido. Por último, se muestra un mensaje que indica si la máquina virtual se inició correctamente o no mediante el cmdlet Write-Output de PowerShell.

1. Abra **MyFirstRunbook** en el editor gráfico.
2. Para quitar el vínculo entre **Specify Subscription Id** (Especificar id. de suscripción) y **Start-AzureRmVM**, haga clic en él y presione la tecla *Suprimir*.
3. En el control Biblioteca, escriba **Get-AzureRm** en el cuadro de texto de búsqueda.
4. Agregue **Get-AzureRmVM** al lienzo.
5. Seleccione **Get-AzureRmVM** y después **Conjunto de parámetros** para ver los conjuntos de **Get-AzureRmVM**. Seleccione el conjunto de parámetros **GetVirtualMachineInResourceGroupNameParamSet** . **ResourceGroupName** y **Name** tienen signos de exclamación al lado. Esto indica que son parámetros necesarios. Tenga en cuenta que ambos esperan valores de cadena.
6. En **Origen de datos** de **Nombre**, seleccione **Entrada de runbook** y **VMName**. Haga clic en **Aceptar**.
7. En **Origen de datos** de **ResourceGroupName**, seleccione **Entrada de runbook** y **ResourceGroupName**. Haga clic en **Aceptar**.
8. En **Origen de datos** para **Estado**, seleccione **Valor constante** y haga clic en **Verdadero**. Haga clic en **Aceptar**.
9. Cree un vínculo de **Specify Subscription Id** (Especificar id. de suscripción) a **Get-AzureRmVM**.
10. En el control Biblioteca, expanda **Control de Runbook** y agregue **Código** al lienzo.  
11. Cree un vínculo de **Get-AzureRmVM** a **Código**.  
12. Haga clic en **Código** y, en el panel Configuración, cambie la etiqueta a **Get Status**.
13. Seleccione el parámetro **Código**; aparece la hoja **Editor de código**.  
14. En el editor de código, pegue el siguiente fragmento de código:
    
     ```powershell
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```
15. Cree un vínculo de **Get Status** a **Start-AzureRmVM**.<br> ![Runbook con el módulo de código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. Seleccione el vínculo y, en el panel Configuración, cambie **Aplicar condición** a **Sí**. Tenga en cuenta que el vínculo se convierte en una línea discontinua que indica que la actividad de destino solo se ejecuta si la condición se resuelve como verdadera.  
17. En **Expresión de condición**, escriba *$ActivityOutput['Get Status'] -eq "Stopped"*. **Start-AzureRmVM** ahora solo se ejecuta si la máquina virtual está detenida.
18. En el control Biblioteca, expanda **Cmdlets** y **Microsoft.PowerShell.Utility**.
19. Agregue **Write-Output** al lienzo dos veces.<br> ![Runbook con Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. En el primer control **Write-Output**, haga clic en **Parámetros** y cambie el valor de **Etiqueta** a *Notify VM Started* (Notificar VM iniciada).
21. Para **InputObject**, cambie **Origen de datos** a **PowerShell expression** (Expresión de PowerShell) y escriba la expresión *"$VMName successfully started."* ($VMName iniciada correctamente).
22. En el segundo control **Write-Output**, haga clic en **Parámetros** y cambie el valor de **Etiqueta** a *Notify VM Start Failed* (Notificar error al iniciar VM).
23. En **InputObject**, cambie **Origen de datos** a **PowerShell expression** (Expresión de PowerShell) y escriba la expresión *"$VMName could not start."* ($VMName no se pudo iniciar).
24. Cree un vínculo de **Start-AzureRmVM** a **Notify VM Started** (Notificar VM iniciada) y **Notify VM Start Failed** (Notificar error al iniciar VM).
25. Seleccione el vínculo a **Notify VM Started** (Notificar VM iniciada) y cambie **Aplicar condición** a **Verdadero**.
26. En **Expresión de condición**, escriba *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Este control Write-Output ahora se ejecuta solo si la máquina virtual se ha iniciado correctamente.
27. Seleccione el vínculo a **Notify VM Start Failed** (Notificar error al iniciar VM) y cambie **Aplicar condición** a **Verdadero**.
28. En **Expresión de condición**, escriba *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Este control Write-Output ahora se ejecuta solo si la máquina virtual no se ha iniciado correctamente.
29. Guarde el runbook y abra el panel Prueba.
30. Inicie el runbook con la máquina virtual detenida- Se debería iniciar.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la creación de gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md)
* Para empezar a trabajar con Runbooks de PowerShell, consulte [Mi primer Runbook de PowerShell](automation-first-runbook-textual-powershell.md)
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer Runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)

