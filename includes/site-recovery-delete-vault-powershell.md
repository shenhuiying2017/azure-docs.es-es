## <a name="delete-a-recovery-services-vault-powershell"></a>Eliminación de un almacén de Recovery Services (PowerShell)

1. Obtención del almacén de Recovery Services

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. Eliminación del almacén

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> Use el comando anterior con extrema precaución porque si elimina por accidente cualquier almacén, perderá todos los datos. Se trata de una acción permanente que no se puede revertir.  


