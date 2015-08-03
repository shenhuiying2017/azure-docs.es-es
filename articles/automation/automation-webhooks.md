<properties 
   pageTitle="Webhooks de Automatización de Azure "
   description="Un webhook que permite a un cliente iniciar un runbook en Automatización de Azure desde una llamada HTTP. Este artículo describe cómo crear un webhook y cómo llamar a uno para que inicie un runbook."
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
   ms.date="07/05/2015"
   ms.author="bwren" />

# Webhooks de Automatización de Azure

Un *webhook* le permite iniciar un runbook determinado en Automatización de Azure a través de una sola solicitud HTTP. Esto permite a los servicios externos, como Visual Studio Online, GitHub o aplicaciones personalizadas, iniciar runbooks sin necesidad de implementar una solución completa mediante la API de Automatización de Azure.

![Webhooks](media/automation-webhooks/webhooks-overview.png)

Puede comparar webhooks con otros métodos para iniciar un runbook [a partir un runbook de Automatización de Azure.](automation-starting-a-runbook.md)

## Detalles de un webhook

En la tabla siguiente se describen las propiedades que debe configurar para un webhook.

| Propiedad | Descripción |
|:---|:---|
|Nombre | Puede proporcionar cualquier nombre que desee para un webhook, ya que esto no se expone al cliente. Solo se utiliza para que identifique el runbook en Automatización de Azure. <br> Como práctica recomendada, debe proporcionar al webhook un nombre relacionado con el cliente que lo va a usar. |
|URL |La dirección URL del webhook es la dirección única que llama a un cliente con una solicitud HTTP POST para iniciar el runbook vinculado al webhook. Se genera automáticamente al crear el webhook. No se puede especificar una dirección URL personalizada. <br> <br> La dirección URL contiene un token de seguridad que permite que el runbook se invoque por un sistema de terceros sin autenticación adicional. Por este motivo, debe tratarse como una contraseña. Por motivos de seguridad, solo puede ver la dirección URL en el portal de vista previa de Azure en el momento en que se crea el webhook. Debe anotar la dirección URL en una ubicación segura para su uso futuro. |
|Fecha de expiración | Al igual que un certificado, cada webhook tiene una fecha de caducidad en la que ya no se puede usar. No se puede cambiar esta fecha de caducidad después de que se cree el webhook, y el webhook tampoco puede habilitarse de nuevo cuando se alcanza la fecha de caducidad. En este caso, debe crear otro webhook para reemplazar el actual y actualizar el cliente para utilizar el nuevo webhook. |
| Enabled | Al crearse, los webhooks se habilitan de forma predeterminada. Si se establece como Disabled, ningún cliente podrá usarlo. Puede establecer la propiedad **Enabled** al crear el webhook o en cualquier momento una vez creado. |


### Parámetros
Un webhook puede definir valores para parámetros de runbook que se usan cuando se inicia dicho webhook inicia el runbook. El webhook debe incluir los valores de los parámetros obligatorios del runbook y puede incluir valores para los parámetros opcionales. Varios webhooks vinculados a un único runbook puede utilizar distintos valores de parámetros.

>[AZURE.NOTE]Los valores de parámetros establecidos por un webhook actualmente no se pueden cambiar una vez creado el webhook. Debe crear otro webhook que use distintos valores de parámetros.

Cuando un cliente inicia un runbook mediante un webhook, no puede reemplazar los valores de parámetros definidos en el webhook. Para recibir datos del cliente, el runbook puede aceptar un único parámetro denominado **$WebhookData** del tipo [object] que contendrá los datos que el cliente incluye en la solicitud POST.

![Webhookdata](media/automation-webhooks/webhookdata.png)

El objeto **$WebhookData** tendrá las siguientes propiedades:

| Propiedad | Descripción |
|:--- |:---|
| WebhookName | Nombre del webhook. |
| RequestHeader | Tabla hash que contiene los encabezados de la solicitud POST entrante. |
| RequestBody | El cuerpo de la solicitud POST entrante. Esto conservará todo el formato como cadena de JSON, XML o datos codificados de formulario. El runbook debe escribirse para que trabaje con el formato de datos que se espera.|


No hay ninguna configuración del webhook obligatoria para admitir el parámetro **$WebhookData**, y el runbook no tiene que aceptarlo. Si el runbook no define el parámetro, se ignoran los detalles de la solicitud enviados desde el cliente.

Si especifica un valor para $WebhookData al crear el webhook, el valor se reemplazará cuando el webhook inicie el runbook con los datos de la solicitud POST del cliente, incluso si el cliente no incluye ningún dato en el cuerpo de la solicitud. Si inicia un runbook con $WebhookData mediante un método que no sea un webhook, puede proporcionar un valor para $Webhookdata que el runbook reconocerá. Este valor debe ser un objeto con las mismas propiedades que $Webhookdata para que el runbook pueda funcionar correctamente con él.

>[AZURE.NOTE]Los valores de todos los parámetros de entrada se registran con el trabajo de runbook. Esto significa que se registrará cualquier entrada que proporcione el cliente en la solicitud de webhook y que estará disponible para cualquiera con acceso al trabajo de automatización. Por este motivo, debe tener cuidado en cómo incluir información confidencial en las llamadas de webhook.

## Seguridad

La seguridad de un webhook se basa en la privacidad de su dirección URL que contiene un token de seguridad que permite que se invoque. Automatización de Azure no realiza ninguna autenticación en la solicitud siempre que se haga en la dirección URL correcta. Por esta razón, no deben utilizarse webhooks de runbooks que realicen funciones altamente confidenciales sin utilizar medios alternativos para validar la solicitud.

Puede incluir lógica en el runbook para determinar que se llamó por un webhook; para ello, active la propiedad **WebhookName** del parámetro $WebhookData. El runbook puede realizar más validaciones buscando información concreta en las propiedades **RequestHeader** o **RequestBody**.

Otra estrategia es hacer que el runbook realice alguna validación de una condición externa cuando recibe una solicitud de webhook. Por ejemplo, considere un runbook llamado por GitHub cuando hay una nueva confirmación a un repositorio de GitHub. El runbook puede conectarse a GitHub para asegurarse de que una nueva confirmación se ha realizado realmente antes de continuar.

## Creación de un webhook

Use el procedimiento siguiente para crear un nuevo webhook vinculado a un runbook en el Portal de vista previa de Azure.

1. Desde la **Hoja de runbooks** en el Portal de vista previa de Azure, haga clic en el runbook que va a iniciar el webhook para ver su hoja de detalles. 
3. Haga clic en **Webhook** en la parte superior de la hoja para abrir la hoja **Agregar webhook**. <br> ![Botón Webhooks](media/automation-webhooks/webhooks-button.png)
4. Haga clic en **Crear nuevo webhook** para abrir **Crear hoja de webhook**.
5. Especifique un **nombre**, una **fecha de caducidad** para el webhook y si debe habilitarse. Vea [Detalles de un webhook](#details-of-a-webhook) para más información sobre estas propiedades.
6. Haga clic en el icono de copiar y presione Ctrl+C para copiar la dirección URL del webhook. A continuación, guárdela en un lugar seguro. **Una vez creado el webhook, no se puede recuperar la dirección URL de nuevo.** <br> ![Dirección URL de Webhook](media/automation-webhooks/copy-webhook-url.png)
3. Haga clic en **Parámetros** para especificar los valores de los parámetros del runbook. Si el runbook tiene parámetros obligatorios, no podrá crear el webhook, salvo que se especifiquen los valores.
1. Haga clic en **Crear** para crear el proyecto.


## Uso de un webhook

Para utilizar un webhook después de que se haya creado, la aplicación cliente debe emitir una solicitud HTTP POST con la dirección URL del webhook. La sintaxis del webhook estará en el formato siguiente.

	http://<Webhook Server>/token?=<Token Value>

El cliente recibirá uno de los siguientes códigos de retorno de la solicitud POST.

| Código | Texto | Descripción |
|:---|:----|:---|
| 202 | Accepted | La solicitud se aceptó y el runbook se puso en cola correctamente. |
| 400 | Bad Request | No se aceptó la solicitud por uno de los siguientes motivos. <ul> <li>El webhook ha caducado.</li> <li>El webhook está deshabilitado.</li> <li>El token en la dirección URL no es válido.</li> </ul>|
| 404 | No encontrado | No se aceptó la solicitud por uno de los siguientes motivos. <ul><li>No se encontró el webhook.</li> <li>No se encontró el runbook.</li> <li>No se encontró la cuenta.</li></ul> |
| 500 | Internal Server Error | La dirección URL es válida, pero se produjo un error. Vuelva a enviar la solicitud. |

Asumiendo que la solicitud sea correcta, la respuesta del webhook contendrá el Id. de trabajo en formato JSON como se muestra a continuación. Contendrá un solo Id. de trabajo, pero el formato JSON permite realizar potenciales mejoras en el futuro.

	{"JobIds":["<JobId>"]}  

El cliente no puede determinar cuando se completa el trabajo del runbook ni su estado de finalización a partir del webhook. Para determinar esta información, debe usar el Id. de trabajo con otro método como [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) o la [API de Automatización de Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### Ejemplo

En el ejemplo siguiente se usa Windows PowerShell para iniciar un runbook con un webhook. Tenga en cuenta que cualquier lenguaje que pueda realizar una solicitud HTTP puede utilizar un webhook; Windows PowerShell se usa aquí simplemente como ejemplo.

El runbook espera una lista de máquinas virtuales con formato JSON en el cuerpo de la solicitud. También se incluye información sobre quién inicia el runbook y la fecha y hora en que se inicia en el encabezado de la solicitud.

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


La siguiente imagen muestra la información del encabezado (con un seguimiento de [Fiddler](http://www.telerik.com/fiddler)) de esta solicitud. Aquí se incluyen los encabezados estándar de una solicitud HTTP, además de los encabezados de fecha y hora personalizados y que se han agregado. Todos estos valores están disponible para el runbook en la propiedad **RequestHeaders** de **WebhookData**.

![Botón Webhooks](media/automation-webhooks/webhook-request-headers.png)

La siguiente imagen muestra el cuerpo de la solicitud (con un seguimiento de[Fiddler](http://www.telerik.com/fiddler)) que está disponible para el runbook en la propiedad **RequestBody** de **WebhookData**. Tiene formato JSON porque ese era el formato que se incluía en el cuerpo de la solicitud.

![Botón Webhooks](media/automation-webhooks/webhook-request-body.png)

La siguiente imagen muestra la solicitud que se envía desde Windows PowerShell y la respuesta resultante. El Id. de trabajo se extrae de la respuesta y se convierte en una cadena.

![Botón Webhooks](media/automation-webhooks/webhook-request-response.png)

El siguiente runbook de muestra acepta la solicitud del ejemplo anterior e inicia las máquinas virtuales especificadas en el cuerpo de la solicitud.

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = (ConvertFrom-Json -InputObject $WebhookBody).VirtualMachines
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				Write-Output "Starting $VM.Name."
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}

	

## Artículos relacionados

- [Inicio de un runbook](automation-starting-a-runbook.md)
- [Visualización del estado de un trabajo de runbook](automation-viewing-the-status-of-a-runbook-job.md) 

<!---HONumber=July15_HO4-->