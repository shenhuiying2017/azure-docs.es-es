---
title: "Parámetros de entrada de runbook | Microsoft Docs"
description: "Los parámetros de entrada de Runbook aumentan la flexibilidad de los Runbooks porque permiten pasar datos a un Runbook cuando se inicia. En este artículo se describen los distintos escenarios donde se usan parámetros de entrada en Runbooks."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 4d3dff2c-1f55-498d-9a0e-eee497e5bedb
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sngun
ms.openlocfilehash: 889d1ac1597bd88ae7455ac98bfdb34f4013e0de
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="runbook-input-parameters"></a>Parámetros de entrada de Runbook

Los parámetros de entrada de Runbook aumentan la flexibilidad de los Runbooks porque permiten pasarle datos cuando se inicia. Los parámetros permiten dirigir acciones de Runbook a entornos y escenarios específicos. En este artículo, le guiaremos por los distintos escenarios donde se usan parámetros de entrada en Runbooks.

## <a name="configure-input-parameters"></a>Configuración de parámetros de entrada

Los parámetros de entrada pueden configurarse en los runbooks gráficos, de PowerShell, de flujo de trabajo de PowerShell y de Python. Un Runbook puede tener varios parámetros con tipos de datos diferentes o sin parámetros. Los parámetros de entrada pueden ser obligatorios u opcionales, y puede asignar un valor predeterminado a los parámetros opcionales. Puede asignar valores a los parámetros de entrada para un Runbook cuando lo inicia mediante uno de los métodos disponibles. Entre estos métodos, se incluye iniciar un Runbook mediante el portal o un servicio web. También puede iniciarlo como Runbook secundario al que se llama insertado en otro Runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Configuración de parámetros de entrada en Runbooks de PowerShell y de flujo de trabajo de PowerShell

Los Runbooks de PowerShell y de [flujo de trabajo de PowerShell](automation-first-runbook-textual.md) en Azure Automation admiten parámetros de entrada que se definen mediante los siguientes atributos.  

| **Propiedad** | **Descripción** |
|:--- |:--- |
| Tipo |Obligatorio. Tipo de datos previsto para el valor del parámetro. Es válido cualquier tipo .NET. |
| Nombre |Obligatorio. El nombre del parámetro. Debe ser único dentro del Runbook y puede contener solo letras, números o caracteres de subrayado. Debe empezar por una letra. |
| Obligatorio |Opcional. Especifica si se debe proporcionar un valor para el parámetro. Si se establece en **$true**, se debe proporcionar un valor cuando se inicia el Runbook. Si se establece en **$false**, el valor es opcional. |
| Valor predeterminado |Opcional.  Especifica un valor que se usará para el parámetro si no se pasa un valor al iniciar el Runbook. Se puede establecer un valor predeterminado para cualquier parámetro y hará automáticamente que el parámetro sea opcional independientemente de la configuración de Mandatory. |

Windows PowerShell admite más atributos de parámetros de entrada de los enumerados aquí, como validación, alias y conjuntos de parámetros. Sin embargo, Azure Automation actualmente solo admite los parámetros de entrada enumerados antes.

Una definición de parámetro en los Runbooks de flujo de trabajo de PowerShell tiene el formato general siguiente, donde los distintos parámetros están separados por comas.

   ```powershell
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
   ```

> [!NOTE]
> Al definir parámetros, si no se especifica el atributo **Mandatory** , el parámetro se considera opcional de forma predeterminada. Además, si se establece un valor predeterminado para un parámetro en Runbooks de flujo de trabajo de PowerShell, PowerShell lo tratará como un parámetro opcional, independientemente del valor del atributo **Mandatory**.
> 
> 

Por ejemplo, vamos a configurar los parámetros de entrada para un Runbook de flujo de trabajo de PowerShell que proporciona detalles sobre las máquinas virtuales, una o varias, de un grupo de recursos. Este Runbook tiene dos parámetros, como se muestra en la siguiente captura de pantalla: el nombre de la máquina virtual y el nombre del grupo de recursos.

![Automation, flujo de trabajo de PowerShell](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

En esta definición de parámetros, los parámetros **$VMName** y **$resourceGroupName** son parámetros simples de tipo String. Sin embargo, los Runbooks de PowerShell y de flujo de trabajo de PowerShell admiten todos los tipos simples y complejos, como **object** o **PSCredential**, como parámetros de entrada.

Si el Runbook tiene un parámetro de entrada de tipo object, use una tabla hash de PowerShell con pares (nombre,valor) para pasar un valor. Por ejemplo, si tiene el siguiente parámetro en un Runbook:

     [Parameter (Mandatory = $true)]
     [object] $FullName

Puede pasar el siguiente valor al parámetro:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## <a name="configure-input-parameters-in-graphical-runbooks"></a>Configuración de parámetros de entrada en Runbooks gráficos

Para [configurar un Runbook gráfico](automation-first-runbook-graphical.md) con parámetros de entrada, vamos a crear un Runbook gráfico que proporciona detalles sobre las máquinas virtuales, una o varias, de un grupo de recursos. La configuración de un Runbook consta de dos actividades principales, como se describe a continuación.

[**Autenticación de Runbooks con una cuenta de ejecución de Azure**](automation-sec-configure-azure-runas-account.md) para autenticarse con Azure.

[**Get-AzureRmVm**](https://msdn.microsoft.com/library/mt603718.aspx) para obtener las propiedades de las máquinas virtuales.

Puede usar la actividad [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) para obtener los nombres de las máquinas virtuales. La actividad **Get AzureRmVm** acepta dos parámetros, el **nombre de máquina virtual** y el **nombre del grupo de recursos**. Puesto que estos parámetros requieren valores diferentes cada vez que se inicia el Runbook, puede agregar parámetros de entrada a su Runbook. Estos son los pasos para agregar parámetros de entrada:

1. Seleccione el Runbook gráfico de la hoja **Runbooks** y [**edítelo**](automation-graphical-authoring-intro.md).
2. En el editor de Runbooks, haga clic en **Entrada y salida** para abrir la hoja **Entrada y salida**.
   
    ![Runbook gráfico de Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. La hoja **Entrada y salida** muestra una lista de parámetros de entrada definidos para el Runbook. En esta hoja, puede agregar un nuevo parámetro de entrada o editar la configuración de uno existente. Para agregar un nuevo parámetro para el Runbook, haga clic en **Agregar entrada** para abrir la hoja **Parámetro de entrada de Runbook**. En ella puede configurar los siguientes parámetros:
   
   | **Propiedad** | **Descripción** |
   |:--- |:--- |
   | Nombre |Obligatorio.  El nombre del parámetro. Debe ser único dentro del Runbook y puede contener solo letras, números o caracteres de subrayado. Debe empezar por una letra. |
   | Descripción |Opcional. Descripción del propósito del parámetro de entrada. |
   | Tipo |Opcional. Tipo de datos previsto para el valor del parámetro. Los tipos de parámetro admitidos son **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime** y **Object**. Si no se selecciona ningún tipo de datos, el valor predeterminado es **String**. |
   | Obligatorio |Opcional. Especifica si se debe proporcionar un valor para el parámetro. Si elige **yes**, se debe proporcionar un valor cuando se inicia el Runbook. Si elige **no**, no se necesita un valor cuando se inicia el Runbook y se puede establecer un valor predeterminado. |
   | Valor predeterminado |Opcional. Especifica un valor que se usará para el parámetro si no se pasa un valor al iniciar el Runbook. Puede establecerse un valor predeterminado para un parámetro que no sea obligatorio. Para establecer un valor predeterminado, elija **Personalizado**. Este valor se usa a menos que se proporcione otro valor al iniciar el Runbook. Elija **Ninguno** si no desea proporcionar ningún valor predeterminado. |
   
    ![Agregar nueva entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Cree dos parámetros con las siguientes propiedades que usará la actividad **Get-AzureRmVm**:
   
   * **Parameter1:**
     
     * Nombre: VMName
     * Tipo: String
     * Obligatorio: No
   * **Parameter2:**
     
     * Nombre: resourceGroupName
     * Tipo: String
     * Obligatorio: No
     * Valor predeterminado: Personalizado
     * Valor predeterminado personalizado: \<Nombre del grupo de recursos que contiene las máquinas virtuales>
5. Una vez agregados los parámetros, haga clic en **Aceptar**.  Ahora puede verlos en la **hoja Entrada y salida**. Haga clic en **Aceptar** de nuevo y después en **Guardar** y en **Publicar** para publicar el Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Configuración de parámetros de entrada en runbooks de Python

A diferencia de los runbooks gráficos, de PowerShell y de flujo de trabajo de PowerShell, los runbooks de Python no toman parámetros con nombre.
Todos los parámetros de entrada se analizan como una matriz de valores de argumento.
Para obtener acceso a la matriz, debe importar el módulo `sys` al script de Python y usar la matriz `sys.argv`.
Es importante tener en cuenta que el primer elemento de la matriz, `sys.argv[0]`, es el nombre del script, por lo que el primer parámetro de entrada real es `sys.argv[1]`.

Para obtener un ejemplo de cómo usar los parámetros de entrada en un runbook de Python, vea [My first Python runbook in Azure Automation](automation-first-runbook-textual-python2.md) (Mi primer runbook de Python en Azure Automation).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Asignación de valores a parámetros de entrada en Runbooks

Puede pasar valores a parámetros de entrada en Runbooks en los siguientes escenarios.

### <a name="start-a-runbook-and-assign-parameters"></a>Inicio de un Runbook y asignación de parámetros

Se puede iniciar un Runbook de muchas maneras: mediante Azure Portal, con un webhook, con cmdlets de PowerShell, con la API de REST o con el SDK. A continuación, se describen distintos métodos para iniciar un Runbook y asignar parámetros.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Inicio de un Runbook publicado mediante el Portal de Azure y asignación de parámetros

Al [iniciar el runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), se abre la hoja **Iniciar runbook** y puede indicar los valores de los parámetros que acaba de crear.

![Empezar a usar el portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

En la etiqueta bajo el cuadro de entrada, puede ver los atributos que se han definido para el parámetro. Entre los atributos, se incluye si es obligatorio u opcional, el tipo y el valor predeterminado. En el globo de ayuda junto al nombre del parámetro puede ver toda la información importante que necesita para tomar decisiones acerca de los valores de entrada de los parámetros. Esta información incluye si un parámetro es obligatorio u opcional. También incluye el tipo y el valor predeterminado (si existe), además de otras notas útiles.

![Globo de ayuda](media/automation-runbook-input-parameters/automation-05-helpbaloon.png)

> [!NOTE]
> Los parámetros de tipo String admiten valores de cadena **Empty** .  Si escribe **[EmptyString]** en el cuadro del parámetro de entrada, pasará una cadena vacía al parámetro. Además, los parámetros de tipo String no permiten que se pasen valores **Null** . Si no pasa ningún valor al parámetro de tipo String, PowerShell lo interpretará como Null.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Inicio de un Runbook publicado mediante cmdlets de PowerShell y asignación de parámetros

* **Cmdlets de Azure Resource Manager:** puede iniciar un Runbook de automatización creado en un grupo de recursos mediante [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Ejemplo:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Cmdlets de Administración de servicios de Azure:** puede iniciar un Runbook de automatización creado en un grupo de recursos predeterminado mediante [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Ejemplo:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Cuando se inicia un Runbook mediante cmdlets de PowerShell, se crea un parámetro predeterminado, **MicrosoftApplicationManagementStartedBy**, con el valor **PowerShell**. Puede ver este parámetro en la hoja **Detalles del trabajo** .  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Inicio de un Runbook con un SDK y asignación de parámetros

* **Método Azure Resource Manager:** puede iniciar un Runbook mediante el SDK de un lenguaje de programación. A continuación, se muestra un fragmento de código C# para iniciar un Runbook en su cuenta de Automation. Puede ver todo el código en nuestro [repositorio de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```
* **Método Administración de servicios de Azure:** puede iniciar un Runbook mediante el SDK de un lenguaje de programación. A continuación, se muestra un fragmento de código C# para iniciar un Runbook en su cuenta de Automation. Puede ver todo el código en nuestro [repositorio de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```      
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```
  
  Para iniciar este método, cree un diccionario para almacenar los parámetros del Runbook, **VMName** y **resourceGroupName**, así como sus valores. A continuación, inicie el Runbook. A continuación, se muestra el fragmento de código C# para llamar al método definido más arriba.
  
  ```
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Inicio de un Runbook con la API de REST y asignación de parámetros
Se puede crear un trabajo de runbook e iniciarlo con la API de REST de Azure Automation usando el método **PUT** con el siguiente identificador URI de solicitud.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

En el identificador URI de solicitud, reemplace los siguientes parámetros:

* **subscription-id** : identificador de su suscripción a Azure.  
* **cloud-service-name:** nombre del servicio en la nube al que se debe enviar la solicitud.  
* **automation-account-name:** nombre de la cuenta de automatización hospedada en el servicio en la nube especificado.  
* **job-id:** GUID del trabajo. En PowerShell, los GUID pueden crearse con el comando **[GUID]::NewGuid().ToString()** .

Para pasar parámetros al trabajo de Runbook, use el cuerpo de la solicitud. Admite las dos propiedades siguientes proporcionadas en formato JSON:

* **Nombre del Runbook**: obligatorio. El nombre del Runbook para que se inicie el trabajo.  
* **Parámetros del Runbook**: opcionales. Diccionario de la lista de parámetros en formato (nombre, valor), donde el nombre debe ser de tipo String y el valor puede ser cualquier valor JSON válido.

Si desea iniciar el Runbook **Get-AzureVMTextual** creado antes con **VMName** y **resourceGroupName** como parámetros, use el siguiente formato JSON para el cuerpo de la solicitud.

   ```
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

Si el trabajo se crea correctamente, se devuelve un código de estado HTTP 201. Para más información sobre los encabezados y el cuerpo de la respuesta, consulte el artículo acerca de cómo [crear un trabajo de Runbook mediante la API de REST](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Prueba de un Runbook y asignación de parámetros
Cuando se [prueba la versión de borrador del Runbook](automation-testing-runbook.md) mediante la opción de prueba, se abre la hoja **Prueba** y puede configurar los valores de los parámetros recién creados.

![Prueba y asignación de parámetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Vinculación de una programación a un Runbook y asignación de parámetros
También puede [vincular una programación](automation-schedules.md) a su Runbook para que este se inicie en un momento determinado. Asigne los parámetros de entrada al crear la programación y el Runbook los usará cuando se inicie mediante la programación. No se puede guardar la programación hasta que se proporcionen todos los valores de los parámetros obligatorios.

![Programación y asignación de parámetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Creación de un Webhook para un Runbook y asignación de parámetros
Puede crear un [Webhook](automation-webhooks.md) para el Runbook y configurar los parámetros de entrada del Runbook. No se puede guardar el Webhook hasta que se proporcionen todos los valores de los parámetros obligatorios.

![Creación de un Webhook y asignación de parámetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Cuando se ejecuta un Runbook mediante un webhook, se envía un parámetro de entrada predefinido **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** , junto con los parámetros de entrada que definió. Puede hacer clic para expandir el parámetro **WebhookData** para más detalles.

![Parámetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la entrada y salida de Runbooks, consulte [Azure Automation: Runbook Input, Output, and Nested Runbooks](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)(Automatización de Azure: entrada y salida de Runbooks, y Runbooks anidados).
* Para más información acerca de diferentes maneras de iniciar un Runbook, consulte [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md).
* Para editar un Runbook de texto, consulte [Edición de runbooks de texto en Automatización de Azure](automation-edit-textual-runbook.md).
* Para editar un Runbook gráfico, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).

