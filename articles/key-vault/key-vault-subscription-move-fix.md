<properties
	pageTitle="Cambio del identificador de inquilino de Key Vault después de mover la suscripción | Microsoft Azure"
	description="Aprenda a cambiar el identificador de inquilino de un Key Vault después de que se haya movido una suscripción a otro inquilino"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>

# Cambio del identificador de inquilino de Key Vault después de mover la suscripción
### P: Mi suscripción se ha movido del inquilino A al inquilino B. ¿Cómo cambio el identificador de inquilino de Key Vault existente y establezco las ACL correctas para las entidades de seguridad del inquilino B?

Cuando se crea un Key Vault nuevo en una suscripción, se asocia automáticamente a un identificador de inquilino de Azure Active Directory predeterminado de dicha suscripción. Las entradas de la directiva de acceso también se asocian con este identificador de inquilino. Al mover su suscripción de Azure del inquilino A al inquilino B, las entidades de seguridad (usuarios y aplicaciones) de este último inquilino no pueden acceder a los Key Vaults existentes. Para corregir este problema, es preciso

- cambiar el identificador de inquilino asociado a todos las Key Vaults existentes en esta suscripción al inquilino B
- quitar todas las entradas de la directiva de acceso existente
- agregar nuevas entradas de la directiva de acceso que están asociadas al inquilino B.

Por ejemplo, si tiene el Key Vault 'myvault' en una suscripción que se ha movido del inquilino A al inquilino B, con el siguiente código podrá cambiar el identificador de inquilino de este Key Vault y quitar directivas de acceso antiguas.

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Dado que este almacén estaba en el inquilino A antes de mover el valor original de **$vault. Properties.TenantId** es el inquilino A, mientras que **(Get-AzureRmContext). Tenant.TenantId** es el inquilino B.

Ahora que el almacén está asociado al identificador de inquilino correcto y que se han quitado las entradas antiguas de la directiva de acceso, establezca nuevas entradas de la directiva de acceso con [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## Pasos siguientes

- Si le queda alguna duda al respecto de Key Vault, visite los [foros de Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->