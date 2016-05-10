<properties
    pageTitle="Mi primer runbook gráfico en Automatización de Azure | Microsoft Azure"
    description="Tutorial que le guiará a través de la creación, prueba y publicación de un runbook gráfico simple."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/21/2016"
    ms.author="magoedte;bwren"/>

# Mi primer runbook gráfico

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

Este tutorial le guiará por la creación de un [runbook gráfico](automation-runbook-types.md#graphical-runbooks) en Automatización de Azure. Comenzaremos con un runbook simple que probaremos y publicaremos, y explicaremos cómo hacer un seguimiento del estado del trabajo del runbook. A continuación, modificaremos el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. A continuación, haremos un Runbook más sólido al agregar parámetros de Runbook y vínculos condicionales.

## Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

-	suscripción de Azure. Si aún no tiene ninguna, puede [activar los beneficios de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien <a href="/pricing/free-account/" target="_blank">[registrarse para obtener una cuenta gratis](https://azure.microsoft.com/free/).
-	[Cuenta de Automatización](automation-security-overview.md) para contener el Runbook y autenticarse en recursos de Azure. Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
-	Una máquina virtual de Azure. Detendremos e iniciaremos esta máquina, por lo que no debería ser de producción.


## Paso 1: crear nuevo runbook

Empezaremos creando un runbook simple que genere el texto *Hello World*.

1.	En el Portal de Azure, abra su cuenta de Automatización. La página de la cuenta de Automatización proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de ellos son los módulos que se incluyen automáticamente en una cuenta nueva de Automatización. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).
2.	Haga clic en el icono **Runbooks** para abrir la lista de runbooks.<br> ![Control de runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
3.	Para crear un nuevo runbook, haga clic en el botón **Agregar un runbook** y luego en **Crear un nuevo runbook**.
4.	Asigne al runbook el nombre *MyFirstRunbook-Graphical*.
5.	En este caso, vamos a crear un [Runbook gráfico](automation-graphical-authoring-intro.md), así que seleccione **Gráfico** en **Tipo de runbook**.<br> ![Nuevo runbook](media/automation-first-runbook-graphical/new-runbook.png)<br>
6.	Haga clic en **Crear** para crear el runbook y abrir el editor gráfico.

## Paso 2: agregar actividades al runbook

El control Biblioteca en la parte izquierda del editor permite seleccionar actividades para agregarlas al Runbook. Vamos a agregar un cmdlet **Write-Output** para generar el texto desde el Runbook.

1.	En el control Biblioteca, haga clic en el cuadro de texto de búsqueda y escriba **Write-Output**. A continuación se mostrarán los resultados de la búsqueda. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.	Desplácese hasta la parte inferior de la lista. Puede hacer clic con el botón derecho en **Write-Output** y seleccione **Agregar a lienzo**, o bien hacer clic en los puntos suspensivos situados al lado del cmdlet y seleccionar **Agregar a lienzo**.
3.	Haga clic en la actividad **Write-Output** en el lienzo. Se abrirá la hoja Control de configuración, que permite configurar la actividad.
4.	El campo **Etiqueta** tiene como valor predeterminado el nombre del cmdlet, pero se puede cambiar a algo más descriptivo. Cámbielo a *Escribir Hello World para la salida*.
5.	Haga clic en **Parámetros** para especificar los valores de los parámetros del cmdlet. Algunos cmdlets tienen varios conjuntos de parámetros y debe seleccionar los que usará. En este caso, **Write-Output** solo tiene un conjunto de parámetros, por lo que no es necesario seleccionar uno. <br>![Propiedades Write-Output](media/automation-first-runbook-graphical/write-output-properties-update.png)
6.	Seleccione el parámetro **InputObject**. Este es el parámetro donde especificaremos el texto que se va a enviar a la secuencia de salida.
7.	En la lista desplegable **Origen de datos**, seleccione **Expresión de PowerShell**. La lista desplegable **Origen de datos** proporciona diferentes orígenes que se usan para rellenar un valor de parámetro. Puede usar la salida de esos orígenes como otra actividad, un recurso de Automatización o una expresión de PowerShell. En este caso, solo deseamos generar el texto *Hello World*. Podemos usar una expresión de PowerShell y especificar una cadena.
8.	En el cuadro **Expresión**, escriba*"Hello World"* y haga clic en **Aceptar** dos veces para volver al lienzo.<br>![Expresión de PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	Para guardar el runbook, haga clic en **Guardar**.<br> ![Guardar runbook](media/automation-first-runbook-graphical/runbook-toolbar-save.png)

## Paso 3: probar el runbook

Antes de que publiquemos el runbook para que esté disponible en producción, queremos probarlo para asegurarnos de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1.	Haga clic en **Test pane** (Probar panel) para abrir la hoja Probar.<br> ![Panel Prueba](media/automation-first-runbook-graphical/runbook-toolbar-test.png)
2.	Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
3.	Se crea un [trabajo de runbook](automation-runbook-execution.md) y su estado se muestra en el panel. El estado del trabajo se iniciará como *En cola*, lo que indica que espera a que haya algún trabajo de Runbook en la nube disponible. A continuación, pasará a *Iniciando* cuando un trabajador de runbook solicite el trabajo y, a continuación, a *En ejecución* cuando el runbook empiece a ejecutarse realmente.  
4.	Cuando se complete el trabajo del runbook, se mostrará su resultado. En nuestro caso, deberíamos ver *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5.	Cierre la hoja Probar para volver al lienzo.

## Paso 4: publicar e iniciar el runbook

El runbook que acabamos de crear aún está en modo borrador. Tenemos que publicarlo antes de que podamos ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En nuestro caso, no tenemos una versión publicada aún porque acabamos de crear el runbook.

1.	Haga clic en **Publicar** para publicar el runbook y, a continuación, en **Sí** cuando se le solicite.<br> ![Publicar](media/automation-first-runbook-graphical/runbook-toolbar-publish.png)
2.	Si se desplaza a la izquierda para ver el Runbook en la hoja **Runbooks**, mostrará el **Estado de creación** **Publicado**.
3.	Vuelva a desplazarse a la derecha para ver la hoja de **MyFirstRunbook**. Las opciones en la parte superior nos permiten iniciar el runbook, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.
4.	Queremos iniciar el runbook, por lo que debe hacer clic en **Iniciar** y, a continuación, en **Sí** cuando se le solicite.<br> ![Iniciar runbook](media/automation-first-runbook-graphical/runbook-controls-start.png)
5.	Se abre una hoja de trabajo para el trabajo de Runbook que acabamos de crear. Podemos cerrar esta hoja, pero en este caso la dejaremos abierta para poder ver el progreso del trabajo.
6.	El estado del trabajo se muestra en **Resumen de trabajos** y coincide con los estados que vimos cuando probamos el runbook.<br>![Resumen del trabajo](media/automation-first-runbook-graphical/runbook-job-summary.png)
7.	Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. Se abre la hoja **Salida**y podemos ver nuestro *Hello World* en el panel.<br> ![Resumen del trabajo](media/automation-first-runbook-graphical/runbook-job-output.png)  
8.	Cierre la hoja Salida.
9.	Haga clic en **Todos los registros** para abrir la hoja Secuencias para el trabajo de Runbook. Solo deberíamos ver *Hello World* en el flujo de salida, pero se pueden mostrar otras secuencias de un trabajo de runbook como Detallado y Error si el runbook escribe en ellas.<br> ![Resumen del trabajo](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10.	Cierre las hojas Todos los registros y Trabajo para volver a la hoja MyFirstRunbook.
11.	Haga clic en **Trabajos** para abrir la hoja Trabajos de este Runbook. Enumera todos los trabajos creados por este runbook. Solo deberíamos ver un trabajo en la lista ya que solo ejecutamos el trabajo una vez.<br> ![Trabajos](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	Puede hacer clic en esta tarea para abrir el mismo panel Trabajo que vimos cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## Paso 5: Creación de recursos de variables

Hemos probado y publicado nuestro runbook, pero hasta ahora no hace nada útil. Queremos que administre recursos de Azure. Antes de configurar la autenticación, crearemos una variable que contenga el identificador de suscripción y haga referencia a él al configurar la actividad que se va a autenticar en el paso 6 a continuación. Antes de continuar, copie el identificador de suscripción de la opción Suscripciones en el panel Navegación.

1. En la hoja Cuentas de Automatización, haga clic en el icono **Recursos** y se abre la hoja **Recursos**.
2. En la hoja Recursos, haga clic en el icono **Variables**.
3. En la hoja Variables, haga clic en **Add a variable** (Agregar una variable).<br>![Variable de Automatización](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. En la hoja Nueva variable, en el cuadro **Nombre** escriba **AzureSubscriptionId** y en el cuadro **Valor** escriba su identificador de suscripción. Mantenga *cadena* como **Tipo** y el valor predeterminado en **Cifrado**.  
5. Haga clic en **Crear** para crear la variable.  


## Paso 6: Adición de autenticación para administrar recursos de Azure

Ahora que tenemos una variable que contiene el identificador de suscripción, podemos configurar nuestro Runbook para que se autentique con ese identificador y las credenciales a las que se hace referencia en los [requisitos previos](#prerequisites). Para hacerlo, agregamos el cmdlet **Add-AzureRMAccount**. Pero antes de hacerlo, necesitamos crear un parámetro de entrada de Runbook para la credencial porque no se puede llamar directamente desde el parámetro **Add-AzureRMAccount** **Credential**.

1.	Para abrir el editor gráfico, haga clic en **Editar** en la hoja MyFirstRunbook.<br> ![Editar runbook](media/automation-first-runbook-graphical/runbook-controls-edit.png)
2.	Ya no necesitamos **Escribir Hello World en la salida**, así que haga clic en él y seleccione **Eliminar**.
3.	Haga clic en **Entrada y salida** y, a continuación, en **Agregar entrada** para abrir la hoja Parámetro de entrada de Runbook.<br> ![Entrada y salida de runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput.png)
4.	Especifique *AzureCredentialAssetName* en **Nombre**. Mantenga *cadena* en **Tipo** y, en **Valor predeterminado personalizado**, escriba su credencial de Automatización de Azure. Haga clic en **Aceptar**.
5.	En el control Biblioteca, expanda **Recursos**, **Variables** y agregue **AzureSubscriptionId** al lienzo seleccionando **"Add Get Variable" to canvas** ("Agregar variable Get" al lienzo).
6.	En el lienzo, seleccione **Get-AutomationVariable** y en el panel Control de configuración escriba **Get Subscription ID** en el cuadro de texto **Etiqueta**.  
7.	En el control Biblioteca, escriba **Get-AutomationPS** en el cuadro de texto de búsqueda.
8.	Agregue **Get-AutomationPSCredential** al lienzo.<br>![Buscar cmdlet Get-AutomationPSCred](media/automation-first-runbook-graphical/search-get-automationpscredential-cmdlet.png)
9.	En el lienzo, seleccione **Get-AutomationPSCredential** y en el panel Control de configuración escriba **Get Azure Credential** en el cuadro de texto **Etiqueta**.
10.	Haga clic en **Parámetros**; aparece la hoja Activity Parameter Configuration (Configuración de parámetros de actividad). 
11. En la hoja Activity Parameter Configuration (Configuración de parámetros de actividad), haga clic en **Nombre** para que aparezca la hoja Parameter Value (Valor de parámetro).  
12.	 En la hoja Parameter Value (Valor de parámetro), seleccione **Runbook input** (Entrada de Runbook) en **Origen de datos** y seleccione **AzureCredentialAssetName** en la lista y haga clic en **Aceptar** dos veces.   
13.	Mantenga el mouse sobre **Get Subscription ID** hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Get Azure Credential**. La flecha que acaba de crear es un *vínculo*. Se iniciará el Runbook con **Get Subscription ID** y ejecute **Get Azure Credential**.<br> ![Crear vínculo entre actividades](media/automation-first-runbook-graphical/runbook-link-getsubscription-getcredential.png)
14.	En el control Biblioteca, escriba **Add-AzureRm** en el cuadro de texto de búsqueda.
15. Agregue **Add-AzureRmAccount** al lienzo.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
16.	Mantenga el puntero sobre **Get Azure Credential** hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Add-AzureRmAccount**.
17.	Seleccione **Add-AzureRmAccount** y haga clic en **Parámetros** en el panel Control de configuración.
18.	**Add-AzureRmAccount** tiene varios conjuntos de parámetros, por lo que necesitamos seleccionar uno antes de poder proporcionar valores de los parámetros. Haga clic en **Conjunto de parámetros** y seleccione el conjunto de parámetros **Id. de suscripción**.
19.	Una vez seleccionado el conjunto de parámetros, los parámetros se muestran en la hoja Activity Parameter Configuration (Configuración de parámetros de actividad). Haga clic en **Credencial**.<br> ![Agregar parámetros de cuenta de Azure Resource Manager](media/automation-first-runbook-graphical/add-azurermaccount-parameters.png)
20.	En la hoja Parameter Value (Valor de parámetro), seleccione **Activity output** (Salida de la actividad) en **Origen de datos**, seleccione **Get Azure Credential** en la lista y haga clic en **Aceptar**.   
21.	Haga clic en **SubscriptionId** y en la hoja Parameter Value (Valor de parámetro), seleccione **Get Subscription ID** en la lista y haga clic en **Aceptar** dos veces para volver al lienzo.
22.	En el panel Control de configuración escriba **Connect to Azure** en el cuadro de texto**Etiqueta**.

En este punto, el Runbook debe tener el siguiente aspecto: <br>![Configuración de autenticación de Runbook](media/automation-first-runbook-graphical/runbook-authentication-config.png)

## Paso 7: Adición de una actividad para iniciar una máquina virtual

Ahora agregaremos una actividad **Start-AzureRmVM** para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual en su suscripción de Azure. Por ahora vamos a codificar ese nombre en el cmdlet.

1. En el control Biblioteca, escriba **Start-AzureRm** en el cuadro de texto de búsqueda.
2. Agregue **Start-AzureRmVM** al lienzo y haga clic y arrástrelo para colocarlo debajo de **Connect to Azure**.
3. Mantenga el puntero sobre **Connect to Azure** hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Start-AzureRmVM**. 
4.	Seleccione **Start-AzureRmVM**. Haga clic en **Parámetros** y, a continuación, en **Conjunto de parámetros** para ver los conjuntos de **Start-AzureRmVM**. Seleccione el conjunto de parámetros **ResourceGroupNameParameterSetName**. Tenga en cuenta que **ResourceGroupName** y **Name** tienen signos de exclamación al lado. Esto indica que son parámetros necesarios. Tenga en cuenta que ambos esperan valores de cadena.
5.	Seleccione **Name**. Seleccione **PowerShell expression** (Expresión de PowerShell) como **Origen de datos** y escriba el nombre, entre comillas dobles, de la máquina virtual que vamos a iniciar con este Runbook. Haga clic en **Aceptar**.<br>![Valor del parámetro de nombre Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.	Seleccione **ResourceGroupName**. Utilice **PowerShell expression** (Expresión de PowerShell) como **Origen de datos** y escriba el nombre del grupo de recursos entre comillas dobles. Haga clic en **Aceptar**.<br> ![Parámetros Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
8.	Haga clic en el panel Prueba para que podemos probar el runbook.
9.	Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

En este punto, el Runbook debe tener el siguiente aspecto: <br>![Configuración de autenticación de Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm.png)

## Paso 8: Adición de parámetros de entrada adicionales al Runbook

Actualmente, nuestro Runbook inicia la máquina virtual en el grupo de recursos especificado en el cmdlet **Start-AzureRmVM**, pero nuestro Runbook sería más útil si pudiéramos especificar los dos al iniciar el Runbook. Ahora agregaremos parámetros de entrada para que el runbook proporcione esa funcionalidad.

1. Abra el editor gráfico haciendo clic en **Editar** en el panel **MyFirstRunbook**.
2. Haga clic en **Entrada y salida** y, a continuación, en **Agregar entrada** para abrir el panel Parámetros de entrada de runbook.<br> ![Entrada y salida de runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput.png)
3. Especifique *VMName* para el campo **Name**. Mantenga *string* en **Type** y cambie **Mandatory** a *Yes*. Haga clic en **OK**.
4. Cree un segundo parámetro de entrada obligatorio denominado *ResourceGroupName* y haga clic en **Aceptar** para cerrar el panel **Entrada y salida**.<br> ![Parámetros de entrada de runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Seleccione la actividad **Start-AzureRmVM** y haga clic en **Parámetros**.
6. Cambie el **Origen de datos** de **Nombre** a **Runbook input** (Entrada de Runbook) y seleccione **VMName**.<br>
7. Cambie el **Origen de datos** de **ResourceGroupName** a **Runbook input** (Entrada de Runbook) y seleccione **ResourceGroupName**.<br> ![Parámetros Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Guarde el runbook y abra el panel Prueba. Tenga en cuenta que ahora puede proporcionar valores para las dos variables de entrada que se usarán en la prueba.
9. Cierre el panel Prueba.
10.	Haga clic en **Publicar** para publicar la nueva versión del runbook.
11.	Detenga la máquina virtual que inició en el paso anterior.
12.	Haga clic en **Iniciar** para iniciar el runbook. Escriba el valor de **VMName** y **ResourceGroupName** para la máquina virtual que va a iniciar.<br> ![Inicio del runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13.	Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.

## Paso 9: Creación de un vínculo condicional

Ahora modificaremos el Runbook que solo intente iniciar la máquina virtual si aún no se ha iniciado. Para hacerlo, agregaremos un cmdlet **Get-AzureRmVM** al Runbook; esto obtendrá el estado de nivel de instancia de la máquina virtual. A continuación, vamos a agregar un módulo de código de Flujo de trabajo de PowerShell denominado **Get Status** con un fragmento de código de PowerShell para determinar si el estado de la máquina virtual está en ejecución o detenido. Un vínculo condicional desde el módulo **Get Status** solo ejecutará **Start-AzureRmVM** si se detiene el estado de ejecución actual. Por último, se mostrará un mensaje que indica si la máquina virtual se inició correctamente o no mediante el cmdlet Write-Output de PowerShell.

1. Abra **MyFirstRunbook**en el editor gráfico.
2. Para quitar el vínculo entre **Connect to Azure** y **Start-AzureRmVM** haga clic en él y presione la tecla *Suprimir*.
3. En el control Biblioteca, escriba **Get-AzureRm** en el cuadro de texto de búsqueda.
4. Agregue **Get-AzureRmVM** al lienzo.
5. Seleccione **Get-AzureRmVM** y, a continuación, **Conjunto de parámetros** para ver los conjuntos de **Get-AzureRmVM**. Seleccione el conjunto de parámetros **GetVirtualMachineInResourceGroupNameParamSet**. Tenga en cuenta que **ResourceGroupName** y **Name** tienen signos de exclamación al lado. Esto indica que son parámetros necesarios. Tenga en cuenta que ambos esperan valores de cadena.
6. En **Origen de datos** de **Nombre**, seleccione **Runbook input** (Entrada de Runbook) y **VMName**. Haga clic en **OK**.
7. En **Origen de datos** de **ResourceGroupName**, seleccione **Runbook input** (Entrada de Runbook) y **ResourceGroupName**. Haga clic en **OK**.
8. En **Origen de datos** de **Estado**, seleccione **Valor constante** y haga clic en **Verdadero**. Haga clic en **OK**.  
9. Cree un vínculo de **Connect to Azure** a **Get-AzureRmVM**.
10. En el control Biblioteca, expanda **Control de Runbook** y agregue **Código** al lienzo.  
11. Cree un vínculo de **Get-AzureRmVM** a **Código**.  
12. Haga clic en **Código** y, en el panel Configuración, cambie la etiqueta a **Get Status**.
13. Seleccione el parámetro **Código**; aparece la hoja **Editor de código**.  
14. En el editor de código, pegue el siguiente fragmento de código:

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

16. Cree un vínculo de **Get Status** a **Start-AzureRmVM**.<br> ![Runbook con el módulo de código](media/automation-first-runbook-graphical/runbook-startazurermvm-get-status.png)
17. Seleccione el vínculo y, en el panel Configuración, cambie **Apply condition** (Aplicar condición) a **Sí**. Tenga en cuenta que el vínculo se convierte en una línea discontinua que indica que la actividad de destino solo se ejecutará si la condición se resuelve como verdadera.  
18. En **Expresión de condición**, escriba *$ActivityOutput['Get Status'] -eq "Stopped"*. **Start-AzureRmVM** ahora solo se ejecutará si la máquina virtual se detiene.
19.	En el control Biblioteca, expanda **Cmdlets** y, a continuación, **Microsoft.PowerShell.Utility**.
20.	Agregue **Write-Output** al lienzo dos veces.<br> ![Runbook con Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-final.png)
21. En el primer control **Write-Output**, haga clic en **Parámetros** y cambie el valor de **Etiqueta** a *Notify VM Started* (Notificar VM iniciada).
22. Para **InputObject**, cambie **Origen de datos** a **PowerShell expression** (Expresión de PowerShell) y escriba la expresión *"$VMName successfully started."*.
23. En el segundo control **Write-Output**, haga clic en **Parámetros** y cambie el valor de **Etiqueta** a *Notify VM Start Failed* (Notificar error al iniciar VM).
24. Para **InputObject**, cambie **Origen de datos** a **PowerShell expression** (Expresión de PowerShell) y escriba la expresión *"$VMName could not start."*.
25. Cree un vínculo de **Start-AzureRmVM** a **Notify VM Started** (Notificar VM iniciada) y **Notify VM Start Failed** (Notificar error al iniciar VM).
26. Seleccione el vínculo a **Notify VM Started** (Notificar VM iniciada) y cambie **Aplicar condición** a **Verdadero**.
27. En **Expresión de condición**, escriba *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Este control Write-Output ahora se ejecutará solo si la máquina virtual se ha iniciado correctamente.
28. Seleccione el vínculo a **Notify VM Start Failed** (Notificar error al iniciar VM) y cambie **Aplicar condición** a **Verdadero**.
29. En **Expresión de condición**, escriba *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Este control Write-Output ahora se ejecutará solo si la máquina virtual no se ha iniciado correctamente.
30.	Guarde el runbook y abra el panel Prueba.
31.	Inicie el runbook con la máquina virtual detenida- Se debería iniciar.

## Pasos siguientes

-	Para más información acerca de la creación de gráficos, consulte [Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md)
-	Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
-	Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-PowerShell.md)

<!---HONumber=AcomDC_0427_2016-->
