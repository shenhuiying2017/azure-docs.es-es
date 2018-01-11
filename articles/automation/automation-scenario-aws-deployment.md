---
title: "Automatización de la implementación de una máquina virtual en Amazon Web Services | Microsoft Docs"
description: "En este artículo se muestra cómo usar Azure Automation para automatizar la creación de una máquina virtual de Amazon Web Services"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 1d85c01a-d795-4523-8194-84fc15b53838
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: tiandert; bwren
ms.openlocfilehash: 5f81150a0ef60cbf10010374f1ec80b0c05b6c6f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Escenario de Azure Automation: aprovisionamiento de una máquina virtual de AWS
En este artículo se muestra cómo se puede aprovechar Azure Automation para aprovisionar una máquina virtual en su suscripción de Amazon Web Services (AWS) y asignar a dicha máquina virtual un nombre específico, lo que AWS denomina "etiquetar" la máquina virtual.

## <a name="prerequisites"></a>Requisitos previos
Para la finalidad de este artículo, debe tener una cuenta de Azure Automation y una suscripción de AWS. Para más información sobre cómo configurar una cuenta de Azure Automation con las credenciales de una suscripción de AWS, consulte [Autenticación de Runbooks con Amazon Web Services](automation-config-aws-account.md).  Antes de continuar, debe crear o actualizar esta cuenta con sus credenciales de suscripción de AWS, ya que en los pasos siguientes haremos referencia a esta cuenta.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implementar un módulo Amazon Web Services de PowerShell 
Nuestro runbook de aprovisionamiento de la máquina virtual aprovechará el módulo AWS de PowerShell para realizar su trabajo. Realice los pasos siguientes para agregar el módulo a la cuenta de Automation que esté configurada con sus credenciales de suscripción de AWS.  

1. Abra el explorador web, navegue a la [Galería de PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) y haga clic en el **botón Deploy to Azure Automation** (Implementar en Azure Automation).<br><br> ![Importación del módulo AWS de PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Aparecerá la página de inicio de sesión de Azure y, una vez que se autentique, se le dirigirá a Azure Portal y se mostrará la página siguiente.<br><br> ![Página de importación de módulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Seleccione el grupo de recursos en la lista desplegable **Grupo de recursos** y, en el panel Parámetros, proporcione la información siguiente:
   
   * En la lista desplegable **New or Existing Automation Account (string)** (Cuenta de Automation nueva o existente [cadena]), seleccione **Existente**.  
   * En el cuadro **Nombre de cuenta de Automation (cadena)**, escriba el nombre exacto de la cuenta de Automation que incluye las credenciales de la suscripción de AWS.  Por ejemplo, si ha creado una cuenta dedicada denominada **AWSAutomation**, debe escribir exactamente eso en el cuadro.
   * Seleccione la región adecuada en la lista desplegable **Automation Account Location** (Ubicación de la cuenta de Automatización).
4. Cuando termine de escribir la información necesaria, haga clic en **Crear**.
   
   > [!NOTE]
   > Al importar un módulo de PowerShell en Azure Automation, también se extraen los cmdlets. Estas actividades no aparecerán hasta que el módulo haya acabado completamente de importar y extraer los cmdlets. Este proceso puede tardar unos minutos.  
   > <br>
   > 
   > 
5. En Azure Portal, abra la cuenta de Automation indicada en el paso 3.
6. Haga clic en el icono **Recursos** y, en el panel **Recursos**, seleccione el icono **Módulos**.
7. En la página **Módulos**, verá el módulo **AWSPowerShell** en la lista.

## <a name="create-aws-deploy-vm-runbook"></a>Implementar AWS y crear runbook de VM
Una vez que haya implementado el módulo AWS de PowerShell, puede crear un Runbook para automatizar el aprovisionamiento de una máquina virtual en AWS mediante un script de PowerShell. Los pasos siguientes muestran cómo aprovechar un script nativo de PowerShell en Azure Automation.  

> [!NOTE]
> Para obtener más información y conocer las opciones de este script, visite la [Galería de PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Descargue el script de New-AwsVM de PowerShell de la Galería de PowerShell; para ello, abra una sesión de PowerShell y escriba lo siguiente:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. En Azure Portal, abra su cuenta de Automation y seleccione **Runbooks** en la sección **Automatización de procesos** de la izquierda.  
3. En la página **Runbooks**, seleccione **Agregar un Runbook**.
4. En el panel **Agregar un Runbook**, seleccione **Creación rápida** (crear un nuevo runbook).
5. En el panel de propiedades **Runbook**, escriba el nombre del runbook en el cuadro Nombre y, en la lista desplegable **Tipo de Runbook**, seleccione **PowerShell** y, a continuación, haga clic en **Crear**.<br><br> ![Panel de creación de runbooks](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Cuando aparezca la página Editar Runbook de PowerShell, copie y pegue el script de PowerShell en el lienzo de creación de runbooks.<br><br> ![Script de PowerShell de Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Tenga en cuenta lo siguiente al trabajar con el script de PowerShell de ejemplo:
    > 
    > * El Runbook contiene una serie de valores de parámetro predeterminados. Evalúe todos los valores predeterminados y actualícelos cuando sea necesario.
    > * Si ha almacenado las credenciales de AWS como un recurso de credencial con un nombre diferente de **AWScred**, deberá actualizar el script en la línea 57 como corresponda.  
    > * Cuando trabaje con los comandos de la CLI de AWS en PowerShell, especialmente con este Runbook de ejemplo, debe especificar la región de AWS. En caso contrario, se producirá un error en los cmdlets.  Consulte el tema de AWS [Specify AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Especificación de la región de AWS) en las herramientas de AWS para documentos de PowerShell para más información.  
    >

7. Para recuperar una lista de nombres de imagen de su suscripción de AWS, inicie PowerShell ISE e importe el módulo AWS de PowerShell.  Para realizar la autenticación con AWS, reemplace **Get-AutomationPSCredential** en su entorno ISE por **AWScred = Get-Credential**.  Se le solicitarán las credenciales. Puede proporcionar su **Access Key ID** (Identificador de clave de acceso) como nombre de usuario y su **Secret Access Key** (Clave de acceso secreta) como contraseña.  Observe el ejemplo siguiente:  

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

    Se devuelve el siguiente resultado:<br><br>
   ![Obtener imágenes AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copie y pegue el nombre de una de las imágenes en una variable de Automation que se indica en el Runbook como **$InstanceType**. Dado que en este ejemplo usamos la suscripción gratuita en capas de AWS, emplearemos **t2.micro** en nuestro ejemplo de runbook.  
9. Guarde el runbook, haga clic en **Publish** (Publicar) para publicarlo y, después, en **Yes** (Sí), cuando se le solicite.

### <a name="testing-the-aws-vm-runbook"></a>Prueba del Runbook de la máquina virtual de AWS
Antes de probar el Runbook, es necesario verificar algunas cosas. Concretamente:  

* Se ha creado un recurso que se autenticará con AWS denominado **AWScred** , o bien el script se ha actualizado para que haga referencia al nombre de su recurso de credenciales.    
* El módulo AWS de PowerShell se ha importado en Azure Automation  
* Se ha creado un nuevo runbook y los valores de parámetro se han comprobado y actualizado donde ha sido necesario  
* Las opciones **Registrar registros detallados** y, opcionalmente, **Registrar registros de progreso** del valor **Registro y seguimiento** del runbook se han establecido con el valor **Activado**.<br><br> ![Seguimiento y registro de Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Deseamos iniciar el runbook, así que hacemos clic en **Iniciar** y, después, en **Aceptar** cuando se abre el panel Iniciar runbook.
2. En el panel Iniciar runbook, proporcione un **VMname**.  Acepte los valores predeterminados de los demás parámetros que configuró previamente en el script anterior.  Haga clic en **Aceptar** para iniciar el trabajo del runbook.<br><br> ![Iniciar nuevo runbook AWS VM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Se abre un panel de trabajo para el trabajo de runbook que acabamos de crear. Cierre este panel.
4. Para ver el progreso del trabajo y las **transmisiones** de salida, seleccione el icono **Todos los registros** en el panel del trabajo de runbook.<br><br> ![Salida de transmisiones](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar que la máquina virtual se está aprovisionando, inicie sesión en la Consola de administración de AWS si todavía no ha iniciado sesión.<br><br> ![Máquinas virtual implementada en consola AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con cuadernos gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
* Para obtener más información sobre los tipos de runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Azure Automation](automation-runbook-types.md)
* Para obtener más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

