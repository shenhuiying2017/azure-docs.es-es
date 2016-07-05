<properties
   pageTitle="Automatizar la implementación de una máquina virtual en Amazon Web Services | Microsoft Azure"
   description="En este artículo se muestra cómo usar la Automatización de Azure para automatizar la creación de una máquina virtual de Amazon Web Services"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="tiandert; bwren" />

# Escenario de Automatización de Azure: aprovisionamiento de una máquina virtual de AWS 

En este artículo se muestra cómo se puede aprovechar la Automatización de Azure para aprovisionar una máquina virtual en su suscripción de Amazon Web Services (AWS) y asignar a dicha máquina virtual un nombre específico, lo que AWS denomina "etiquetar" la máquina virtual.

## Requisitos previos

Para la finalidad de este artículo, debe tener una cuenta de Automatización de Azure y una suscripción de AWS. Para obtener más información sobre cómo configurar una cuenta de Automatización de Azure con sus credenciales de la suscripción de AWS, revise [Configure Authentication with Amazon Web Services](../automation/automation-sec-configure-aws-account.md) (Configurar la autenticación con Amazon Web Services). Antes de continuar debe crear o actualizar esta cuenta con sus credenciales de suscripción de AWS, ya que en los pasos siguientes haremos referencia a esta cuenta.


## Implementar un módulo Amazon Web Services de PowerShell

Nuestro runbook de aprovisionamiento de la máquina virtual aprovechará el módulo AWS de PowerShell para realizar su trabajo. Realice los pasos siguientes para agregar el módulo a la cuenta de Automatización que esté configurada con sus credenciales de suscripción de AWS.

1. Abra el explorador web, vaya a la [Galería de PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) y haga clic en el botón **Deploy to Azure Automation** (Implementar en Automatización de Azure).<br> ![Importación del módulo AWS de PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)

2. Aparecerá la página de inicio de sesión de Azure y, una vez que se autentique, se le dirigirá al Portal de Azure y se mostrará la hoja siguiente.<br> ![Hoja de importación de módulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)

3. Seleccione el grupo de recursos en la lista desplegable **Grupo de recursos** y, en la hoja Parámetros, proporcione la información siguiente:
   * En la lista desplegable **New or Existing Automation Account (string)** (Cuenta de Automatización nueva o existente [cadena]), seleccione **Existente**.  
   * En el cuadro **Automation Account Name (string)** (Nombre de cuenta de Automatización [cadena]), escriba el nombre exacto de la cuenta de Automatización que incluye las credenciales de la suscripción de AWS. Por ejemplo, si ha creado una cuenta dedicada denominada **AWSAutomation**, debe escribir exactamente eso en el cuadro.
   * Seleccione la región adecuada en la lista desplegable **Automation Account Location** (Ubicación de la cuenta de Automatización).

4. Cuando termine de escribir la información necesaria, haga clic en **Crear**.

    >[AZURE.NOTE] Al importar un módulo de PowerShell en Automatización de Azure, también se extraen los cmdlets. Estas actividades no aparecerán hasta que el módulo haya acabado completamente de importar y extraer los cmdlets. Este proceso puede tardar unos minutos. <br>
5. En el Portal de Azure, abra la cuenta de Automatización indicada en el paso 3.
6. Haga clic en el icono **Recursos** y, en la hoja **Recursos**, seleccione el icono **Módulos**.
7. En la hoja **Módulos**, verá el módulo **AWSPowerShell** en la lista.

## Implementar AWS y crear runbook de VM

Una vez que haya implementado el módulo AWS de PowerShell, puede crear un Runbook para automatizar el aprovisionamiento de una máquina virtual en AWS mediante un script de PowerShell. Los pasos siguientes muestran cómo aprovechar un script nativo de PowerShell en Automatización de Azure.

>[AZURE.NOTE] Para obtener más información y conocer las opciones de este script, visite la [Galería de PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).


1. Descargue el script de PowerShell New-AwsVM desde la Galería de PowerShell. Para ello, abra una sesión de PowerShell y escriba lo siguiente:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
<br>
2. Desde el Portal de Azure, abra su cuenta de Automatización y haga clic en el icono **Runbooks**.  
3. En la hoja **Runbooks**, seleccione **Add a runbook** (Agregar un Runbook).
4. En la hoja **Add a runbook** (Agregar un Runbook), seleccione **Creación rápida** (Crear un nuevo Runbook).
5. En la hoja de propiedades **Runbook**, escriba un nombre de Runbook en el cuadro Nombre y, en la lista desplegable **Runbook type** (Tipo de Runbook), seleccione **PowerShell**. Después, haga clic en **Crear**.<br> ![Hoja de importación de módulo](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Cuando aparezca la hoja Edit PowerShell Runbook (Editar Runbook de PowerShell), copie y pegue el script de PowerShell en el lienzo de creación de Runbooks.<br> ![Script de PowerShell de Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>

    >[AZURE.NOTE] Tenga en cuenta lo siguiente al trabajar con el script de PowerShell de ejemplo:
    >
    > - El Runbook contiene una serie de valores de parámetro predeterminados. Evalúe todos los valores predeterminados y actualícelos cuando sea necesario.
    > - Si ha almacenado las credenciales de AWS como un recurso de credencial con un nombre diferente de **AWScred**, deberá actualizar el script en la línea 57 como corresponda.  
    > - Cuando trabaje con los comandos de la CLI de AWS en PowerShell, especialmente con este Runbook de ejemplo, debe especificar la región de AWS. En caso contrario, se producirá un error en los cmdlets. Ver el tema de AWS [Specify AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Especificar región de AWS) en las herramientas de AWS para documentos de PowerShell para obtener más detalles. <br>
7. Para recuperar una lista de nombres de imagen de su suscripción de AWS, inicie PowerShell ISE e importe el módulo AWS de PowerShell. Para realizar la autenticación con AWS, reemplace **Get-AutomationPSCredential** en su entorno ISE por **AWScred = Get-Credential**. Se le solicitarán las credenciales. Puede proporcionar su **Access Key ID** (Identificador de clave de acceso) para el nombre de usuario y **Secret Access Key** (Acceso de clave secreta) para la contraseña. Observe el ejemplo siguiente:

		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AwsRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password

		# Set up the environment to access AWS
		Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AwsRegion

		Get-EC2ImageByName -ProfileName AWSProfile
Se devuelve el siguiente resultado:<br> ![Obtener imágenes AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)  
8. Copie y pegue el nombre de una de las imágenes en una variable de Automatización que se indica en el Runbook como **$InstanceType**. Dado que en este ejemplo usamos la suscripción gratuita en capas de AWS, usaremos **t2.micro** en nuestro ejemplo de Runbook.
9. Guarde el Runbook, haga clic en **Publicar** para publicarlo y después haga clic en **Sí** cuando se le solicite.


### Prueba del Runbook de la máquina virtual de AWS
Antes de probar el Runbook, es necesario verificar algunas cosas. Concretamente:

   -  Se ha creado un recurso que se autenticará con AWS denominado **AWScred**, o bien el script se ha actualizado para que haga referencia al nombre de su recurso de credenciales.  
   -  El módulo AWS de PowerShell se ha importado en Automatización de Azure
   -  Se ha creado un nuevo runbook y los valores de parámetro se han comprobado y actualizado donde ha sido necesario
   -  En la configuración del runbook **Registro y seguimiento**, **Registrar registros detallados** y, opcionalmente, **Registrar registros de progreso** se han establecido con el valor **Activado**.<br> ![Seguimiento y registro de Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)

1. Como queremos iniciar el Runbook, haga clic en **Iniciar** y, después, en **Aceptar** cuando se abra la hoja Iniciar Runbook.
2. En la hoja Iniciar Runbook, proporcione un **VMname**. Acepte los valores predeterminados de los demás parámetros que configuró previamente en el script anterior. Haga clic en **Aceptar** para iniciar el trabajo de Runbook.<br> ![Iniciar nuevo runbook AWS VM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Se abre un panel de trabajo para el trabajo de runbook que acabamos de crear. Cierre este panel.
4. Podemos ver el progreso del trabajo y las **Secuencias** de salida si seleccionamos el icono **Todos los registros** en la hoja de trabajo de Runbook.<br> ![Salida de transmisiones](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar que la máquina virtual se está aprovisionando, inicie sesión en la Consola de administración de AWS si todavía no ha iniciado sesión.<br> ![Máquinas virtual implementada en consola AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## Pasos siguientes
-   Para empezar a trabajar con Runbooks gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
-	Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
-	Para obtener más información sobre los tipos de Runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Automatización de Azure](automation-runbook-types.md)
-	Para obtener más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Anuncio de la compatibilidad nativa con scripts de PowerShell en Automatización de Azure)

<!---HONumber=AcomDC_0622_2016-->