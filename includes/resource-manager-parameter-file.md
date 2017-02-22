Si utiliza un archivo de parámetros para pasar los valores de parámetro durante la implementación, tendrá que crear un archivo JSON con un formato similar al del ejemplo siguiente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

El tamaño del archivo de parámetros no puede ser superior a 64 KB.

Si necesita proporcionar un valor confidencial para un parámetro (por ejemplo, una contraseña), agregue ese valor a un almacén de claves. Recupere el almacén de claves durante la implementación, como se muestra en el ejemplo anterior. Para más información, consulte [Paso de valores seguros durante la implementación](../articles/azure-resource-manager/resource-manager-keyvault-parameter.md). 



<!--HONumber=Nov16_HO3-->


