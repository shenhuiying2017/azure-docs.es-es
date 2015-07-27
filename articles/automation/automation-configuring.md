<properties
   pageTitle="Configuración de Automatización de Azure"
   description="Describe los pasos que debe realizar para configurar Automatización de Azure para su uso inicial."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/10/2015"
   ms.author="bwren" />

# Configuración de Automatización de Azure

Este artículo describe las acciones que debe realizar para comenzar a usar Automatización de Azure.

## Cuentas de automatización

Cuando inicia Automatización de Azure por primera vez, debe crear al menos una cuenta de Automatización. Las cuentas de Automatización le permiten aislar los recursos de Automatización (runbooks, recursos) desde los recursos de Automatización contenidos en otras cuentas de Automatización. Puede usar cuentas de Automatización para separar recursos de Automatización en entornos lógicos independientes. Por ejemplo, puede usar una cuenta para desarrollo y otra para producción.

Los recursos de Automatización de cada cuenta de Automatización están asociados con una sola región de Azure, pero las cuentas de Automatización pueden administrar servicios de Azure en cualquier región. El principal motivo para crear cuentas de Automatización en distintas regiones sería si tiene directivas que requieren que los datos y recursos se aíslen en una región específica.

>[AZURE.NOTE]A las cuentas de automatización y los recursos que contienen, que se crean con el Portal de vista previa de Azure, no se puede acceder desde el portal de Azure. Si desea administrar estas cuentas o sus recursos con Windows PowerShell, debe usar los módulos del Administrador de recursos de Azure.
>
>Las cuentas de automatización que se crean con el portal de Azure se pueden administrar a través de cualquiera de los portales y de cualquiera de los conjuntos de cmdlets. Tras crear la cuenta da igual la forma en que cree y administre los recursos en la misma. Si piensa seguir usando el portal de Azure, debe utilizarlo en lugar del Portal de vista previa de Azure para crear las cuentas de automatización.


En caso de que se produzca un problema con su cuenta de Azure, como un pago vencido, es posible que se suspenda una cuenta de Automatización. En este caso, no será posible obtener acceso a la cuenta, se suspenderá todo trabajo que esté en ejecución y se deshabilitarán todas las programaciones. Podrá ver la cuenta, pero no podrá ver los recursos existentes en ella. Una vez que corrija el problema y que se habilite la cuenta de Automatización, podrá habilitar las programaciones y reiniciar todo runbook que se haya suspendido.


## Configuración de la autenticación a los recursos de Azure

Cuando tiene acceso a los recursos de Azure mediante los [cmdlets de Azure](http://msdn.microsoft.com/library/azure/jj554330.aspx), deberá proporcionar autenticación a su suscripción de Azure. En la Automatización de Azure, esto se hace con una cuenta profesional en Azure Active Directory que configure como administrador para su suscripción. Luego puede crear una [credencial](http://msdn.microsoft.com/library/dn940015.aspx) para esta cuenta de usuario y utilizarla con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) en el runbook.

>[AZURE.NOTE]No es posible usar cuentas de Microsoft, anteriormente conocidas como LiveID, con Automatización de Azure.

## Creación de un usuario nuevo de Azure Active Directory para administrar una suscripción de Azure

1. Inicie sesión en el portal de Azure como administrador de servicios para la suscripción de Azure que desea administrar.
2. Seleccione **Active Directory**.
3. Seleccione el nombre del directorio asociado con su suscripción de Azure. En caso de ser necesario, puede cambiar esta asociación en **Configuración > Suscripciones > Editar directorio**.
4. [Cree un usuario de Active Directory nuevo](http://msdn.microsoft.com/library/azure/hh967632.aspx). Seleccione **Nuevo usuario de la organización** en el **Tipo de usuario** y no **Habilitar Multi-Factor Authentication**.
5. Observe el nombre completo y la contraseña temporal del usuario.
7. Seleccione **Configuración > Administradores > Agregar**.
8. Escriba el nombre de usuario completo del usuario que creó.
9. Seleccione la suscripción que desea que administre el usuario.
10. Cierre sesión en Azure y vuelva a iniciarla con la cuenta que acaba de crear. Se le pedirá que cambie la contraseña del usuario.
11. Cree un nuevo [recurso Credencial de Automatización de Azure](http://msdn.microsoft.com/library/dn940015.aspx) para la cuenta de usuario que creó. El **Tipo de credencial** debe ser **Credencial de Windows PowerShell**.


## Uso de la credencial en un runbook

Puede recuperar la credencial en un runbook mediante el uso de la actividad [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) y, a continuación, úsela con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para conectarse a su suscripción de Azure. Si credencial es un administrador de varias suscripciones de Azure, deberá usar también [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar la suscripción correcta. Esto se muestra en el ejemplo de Windows PowerShell que aparece a continuación, que normalmente aparecerá en la parte superior de la mayoría de los runbooks de Automatización de Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

Deberá repetir estas líneas después de cada [punto de control](automation-runbook-execution#checkpoints) de su runbook. Si el runbook se suspende y luego se reanuda en otro trabajo, se deberá volver a realizar la autenticación.

## Artículos relacionados
- [Azure Automation: Authenticating to Azure using Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/) (Automatización de Azure: autenticación en Azure mediante Azure Active Directory)
 

<!---HONumber=July15_HO3-->