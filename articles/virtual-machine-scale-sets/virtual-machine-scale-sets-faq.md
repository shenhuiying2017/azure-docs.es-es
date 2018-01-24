---
title: "Preguntas frecuentes sobre los conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga respuestas a preguntas frecuentes sobre los conjuntos de escalado de máquinas virtuales."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: 52be84b73e70a02c43ef71917dc272060d82b42d
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Preguntas frecuentes sobre los conjuntos de escalado de máquinas virtuales de Azure

Obtenga respuestas a preguntas frecuentes sobre los conjuntos de escalado de máquinas virtuales en Azure.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>¿Cuáles son los procedimientos recomendados para el escalado automático de Azure?

Para procedimientos recomendados para el escalado automático, consulte [Procedimientos recomendados de escalado automático en elementos virtuales](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>¿Dónde puedo encontrar los nombres de métricas para un escalado automático que use métricas basadas en host?

Para nombres de métrica para un escalado automático que use métricas basadas en host, consulte [Métricas compatibles con Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>¿Hay algún ejemplo de escalado automático basado en una longitud de cola y un tema de Azure Service Bus?

Sí. Para ejemplos de escalado automático basados en un tema de Service Bus y una longitud de cola, consulte [Métricas comunes de escalado automático de Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Para una cola de Service Bus, use el siguiente JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Para una cola de almacenamiento, use el siguiente JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Reemplace los valores de ejemplo con los identificadores uniformes de recursos (URI) del recurso.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>¿Debo realizar el escalado automático usando métricas basadas en host o usar una extensión de diagnóstico?

Puede crear una configuración de escalado automático en una máquina virtual para usar las métricas de nivel de host, o usar las métricas basadas en SO invitado.

Para obtener una lista de métricas admitidas, consulte [Métricas comunes de escalado automático de Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Para obtener un ejemplo completo para conjuntos de escalado de máquinas virtuales, consulte [Configuración avanzada de escalado automático con plantillas de Resource Manager para conjuntos de escalado de máquinas virtuales](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

El ejemplo utiliza la métrica de CPU de nivel de host y una métrica de recuento de mensajes.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>¿Cómo se configuran las reglas de alerta en un conjunto de escalado de máquinas virtuales?

Puede crear alertas en las métricas de los conjuntos de escalado de máquinas virtuales a través de PowerShell o CLI de Azure. Para obtener más información, consulte [Ejemplos de inicio rápido de PowerShell de Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) y [Ejemplos de inicio rápido de CLI multiplataforma de Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

El elemento TargetResourceId del conjunto de escalado de máquinas virtuales tiene el siguiente aspecto: 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Puede elegir cualquier contador de rendimiento de máquina virtual como métrica sobre la que establecer la alerta. Para más información, consulte [Métricas de SO invitado para máquinas virtuales Windows basadas en Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) y [Métricas de SO invitado para máquinas virtuales Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) en el artículo [Métricas comunes de escalado automático de Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>¿Cómo se configura el escalado automático en un conjunto de escalado de máquinas virtuales utilizando PowerShell?

Para configurar el escalado automático en un conjunto de escalado de máquinas virtuales que se establece mediante el uso de PowerShell, consulte la entrada de blog [How to add autoscale to an Azure virtual machine scale set](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/) (Cómo agregar escalado automático a un conjunto de escalado de máquinas virtuales de Azure).




## <a name="certificates"></a>Certificados

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>¿Cómo se envía de forma segura un certificado a la máquina virtual? ¿Cómo puedo realizar el aprovisionamiento de un conjunto de escalado de máquinas virtuales para ejecutar un sitio web donde el SSL del sitio web se envíe de forma segura a partir de una configuración de certificado? (La operación común de rotación de certificados sería casi igual a una operación de actualización de la configuración). ¿Puedo ver un ejemplo de cómo hacerlo? 

Para enviar de forma segura un certificado a la máquina virtual, puede instalar un certificado de cliente directamente en un almacén de certificados de Windows desde el almacén de claves del cliente.

Use el siguiente JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

El código admite Windows y Linux.

Para más información, consulte el artículo sobre la [creación o actualización de un conjunto de escalado de máquinas virtuales](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Ejemplo de un certificado autofirmado

1.  Creación de un certificado autofirmado en un almacén de claves.

    Utilice los siguientes comandos de PowerShell:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Login-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Este comando le proporciona la entrada para la plantilla de Azure Resource Manager.

    Para obtener un ejemplo de cómo crear un certificado autofirmado en un almacén de claves, consulte [Escenarios de seguridad de los clústeres de Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Creación de la plantilla de Resource Manager.

    Agregue esta propiedad a **virtualMachineProfile** como parte del recurso del conjunto de escalado de máquinas virtuales:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>¿Puedo especificar un par de claves SSH para usar en la autenticación de SSH con un conjunto de escalado de máquinas virtuales Linux desde una plantilla de Resource Manager?  

Sí. La API de REST para **osProfile** es similar a la API de REST de máquina virtual estándar. 

Incluya **osProfile** en la plantilla:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Este bloque JSON se usa en [la plantilla de inicio rápido de GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
El perfil de SO se utiliza también en [la plantilla de inicio rápido de GitHub grelayhost.json](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Para más información, consulte el artículo sobre la [creación o actualización de un conjunto de escalado de máquinas virtuales](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>¿Cómo se quitan los certificados en desuso? 

Para quitar certificados en desuso, quite el certificado antiguo de la lista de certificados del almacén. Deje en la lista todos los certificados que desee que permanezcan en el equipo. Esto no quita el certificado de todas las máquinas virtuales. Tampoco agrega el certificado a las nuevas máquinas virtuales que se creen en el conjunto de escalado de máquinas virtuales. 

Para quitar el certificado de las máquinas virtuales existentes, tiene que escribir una extensión de script personalizado que quite manualmente los certificados del almacén de certificados.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>¿Cómo puedo inyectar una clave pública SSH existente en la capa SSH del conjunto de escalado de máquinas virtuales durante el aprovisionamiento? Quiero almacenar los valores de clave pública SSH en Azure Key Vault y luego usarlos en la plantilla de Resource Manager.

Si va a proporcionar a las máquinas virtuales solo una clave SSH pública, no es necesario colocar las claves públicas en Key Vault. Las claves públicas no son secretas.
 
Puede proporcionar claves públicas SSH en texto sin formato al crear una máquina virtual Linux:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
Nombre del elemento de linuxConfiguration | Obligatorio | type | DESCRIPCIÓN
--- | --- | --- | --- |  ---
ssh | Sin  | Colección | Especifica la configuración de la clave SSH para un sistema operativo Linux
path | Sí | string | Especifica la ruta de acceso de Linux en donde se deben colocar las claves SSH o el certificado
keyData | Sí | string | Especifica una clave pública SSH codificada en base64

Para ver un ejemplo, consulte [la plantilla de inicio rápido de GitHub 101-vm-sshkey ](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Cuando ejecuto `Update-AzureRmVmss` después de agregar más de un certificado desde el mismo almacén de claves, me aparece el mensaje siguiente:
 
>Update-AzureRmVmss: List secret contains repeated instances of /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, which is disallowed. (Update-AzureRmVmss: muestra secretos que contienen instancias repetidas de /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, lo que no se permite.)
 
Esto puede ocurrir si se intenta volver a agregar el mismo almacén en lugar de utilizar un nuevo certificado de almacén para el almacén de origen existente. El comando `Add-AzureRmVmssSecret` no funciona correctamente si agrega secretos adicionales.
 
Para agregar más secretos desde el mismo almacén de claves, actualice la lista $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Para la estructura de entrada esperada, consulte el articulo sobre [creación o actualización de un conjunto de escalado de máquinas virtuales](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
Busque el secreto en el objeto de conjunto de escalado de máquinas virtuales que se encuentra en el almacén de claves. Luego, agregue la referencia del certificado (la dirección URL junto con el nombre del secreto) a la lista asociada con el almacén.

> [!NOTE] 
> Actualmente no se pueden quitar certificados de las máquinas virtuales mediante la API del conjunto de escalado de máquinas virtuales.
>

Las nuevas máquinas virtuales no tendrán el certificado antiguo. Sin embargo, las máquinas virtuales que tienen el certificado y que ya se han implementado tendrán el certificado antiguo.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>¿Puedo insertar certificados en el conjunto de escalado de máquinas virtuales sin proporcionar la contraseña cuando el certificado está en el almacén secreto?

No es necesario codificar las contraseñas en los scripts. Puede recuperar dinámicamente las contraseñas con los permisos que usa para ejecutar el script de implementación. Si tiene un script que mueve un certificado del almacén de secretos al almacén de claves, el comando `get certificate` del almacén de secretos también genera la contraseña del archivo. pfx.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>¿Cómo funciona la propiedad de secretos de virtualMachineProfile.osProfile de un conjunto de escalado de máquinas virtuales? ¿Por qué necesito el valor de sourceVault cuando tengo que especificar el URI absoluto para un certificado mediante la propiedad certificateUrl? 

Es necesario que una referencia de certificado de administración remota de Windows (WinRM) esté presente en la propiedad de secretos del perfil de sistema operativo. 

El propósito de indicar el almacén de origen es aplicar directivas de lista de control de acceso (ACL) que existen en el modelo de servicio de Azure Cloud de un usuario. Si el almacén de origen no está especificado, los usuarios que no tengan permisos para implementar secretos o acceder a ellos en un almacén de claves podrían hacerlo mediante un proveedor de recursos de proceso (CRP). Las ACL existen incluso para recursos que no existen.

Si proporciona un identificador de almacén de origen incorrecto pero una dirección URL de almacén de claves válida, se informará de un error cuando sondee la operación.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Si agrego secretos a un conjunto de escalado de máquinas virtuales ¿los secretos se insertan en las máquinas virtuales existentes, o solo en las nuevas? 

Los certificados se agregan a todas las máquinas virtuales, incluso a las ya existentes. Si la propiedad upgradePolicy de la máquina virtual está establecida en **manual**, el certificado se agrega a la máquina virtual al realizar una actualización manual en ella.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>¿Dónde se ponen los certificados para las máquinas virtuales Linux?

Para información sobre cómo implementar certificados para las máquinas virtuales Linux, consulte [Deploy certificates to VMs from a customer-managed key vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) (Implementación de certificados en máquinas virtuales desde un almacén de claves administrado por el cliente).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>¿Cómo se agrega un nuevo certificado de almacén a un nuevo objeto de certificado?

Para agregar un certificado del almacén a un secreto existente, vea el ejemplo siguiente de PowerShell. Use un solo objeto secreto.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>¿Qué ocurre con los certificados si restablece la imagen inicial de una máquina virtual?

Si restablece la imagen inicial de una máquina virtual, los certificados se eliminan. El restablecimiento de la imagen inicial elimina todo el contenido del disco del sistema operativo. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>¿Qué ocurre si se elimina un certificado del almacén de claves?

Si elimina el secreto del almacén de claves y ejecuta `stop deallocate` en todas las máquinas virtuales y las inicia de nuevo, se producirá un error. El error se produce porque el CRP necesita recuperar los secretos desde el almacén de claves, pero no puede. En este escenario, puede eliminar los certificados del conjunto de escalado de máquinas virtuales. 

El componente de CRP no conserva los secretos de cliente. Si ejecuta `stop deallocate` para todas las máquinas virtuales en el conjunto de escalado de máquinas virtuales, se elimina la memoria caché. En este escenario, los secretos se recuperan del almacén de claves.

Este problema no se produce en el escalado horizontal porque hay una copia en caché del secreto en Azure Service Fabric (en el modelo de inquilino único de estructura).
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>¿Por qué tengo que especificar la ubicación exacta de la dirección URL de certificado (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), como se indica en [Escenarios de seguridad de los clústeres de Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
Según la documentación de Azure Key Vault, la API de REST Get Secret debe devolver la versión más reciente del secreto si no se especifica la versión.
 
Método | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Reemplace {*secret-name*} por el nombre y {*secret-version*} por la versión del secreto que quiere recuperar. Se puede excluir la versión del secreto. En ese caso, se recupera la versión actual.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>¿Por qué tengo que especificar la versión de certificado al usar Key Vault?

El propósito del requisito de Key Vault de especificar la versión del certificado es que el usuario vea claramente qué certificado se implementa en sus máquinas virtuales.

Si crea una máquina virtual y luego actualiza el secreto en el almacén de claves, ese nuevo certificado no se descarga en las máquinas virtuales. Pero parece que las máquinas virtuales hacen referencia a él y las nuevas máquinas virtuales obtienen el nuevo secreto. Para evitar esta situación, se le pide la referencia de la versión de secreto.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mi equipo funciona con varios certificados que se distribuyen como claves públicas .cer. ¿Cuál es el enfoque recomendado para implementar estos certificados en un conjunto de escalado de máquinas virtuales?

Para implementar el conjunto de claves públicas .cer en un conjunto de escalado de máquinas virtuales, puede generar un archivo .pfx que contenga solo los archivos .cer. Para ello, use `X509ContentType = Pfx`. Por ejemplo, cargue el archivo .cer como un objeto x509Certificate2 en C# o PowerShell y, a continuación, llame al método. 

Para más información, consulte [Método X509Certificate.Export (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>No veo una opción para que los usuarios pasen certificados como cadenas base64. La mayoría de otros proveedores de recursos tienen esta opción.

Para emular el pase de un certificado como una cadena base64, puede extraer la última dirección URL con versiones en una plantilla de Resource Manager. Incluya la siguiente propiedad JSON en la plantilla de Resource Manager:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>¿Tengo que encapsular los certificados en objetos JSON en almacenes de claves?

En los conjuntos de escalado de máquinas virtuales y las máquinas virtuales, los certificados se tienen que encapsular en objetos JSON. 

También se admite el tipo de contenido application/x-pkcs12. Para instrucciones sobre el uso de application/x-pkcs12, consulte [PFX certificates in Azure Key Vault](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/) (Certificados PFX en Azure Key Vault).
 
Actualmente no se admiten archivos .cer. Para usar archivos .cer, expórtelos en contenedores .pfx.



## <a name="compliance-and-security"></a>Cumplimiento y seguridad

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>¿Son los conjuntos de escalado de máquinas virtuales compatibles con PCI?

Los conjuntos de escalado de máquinas virtuales son una capa de API fina sobre el CRP. Ambos componentes forman parte de la plataforma de proceso en el árbol de servicio de Azure.

Desde la perspectiva del cumplimiento, los conjuntos de escalado de máquinas virtuales son una parte fundamental de la plataforma de proceso de Azure. Comparten un equipo, herramientas, procesos, metodología de implementación, controles de seguridad, compilación just-in-time (JIT), supervisión, alertas y así sucesivamente, con el CRP propio. Los conjuntos de escalado de máquinas virtuales son compatibles con la Industria de tarjetas de pago (PCI) porque el CRP forma parte de la certificación de estándares de seguridad de datos de PCI (DSS) actual.

Para más información, consulte el [Centro de confianza de Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>¿La [identidad de servicio administrada de Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) funciona con conjuntos de escalado de máquinas virtuales?

Sí. Puede ver algunas plantillas MSI de ejemplo en las plantillas de ejemplo de inicio rápido de Azure. Linux: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Extensiones

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>¿Cómo puedo eliminar una extensión del conjunto de escalado de máquinas virtuales?

Para eliminar una extensión del conjunto de escalado de máquinas virtuales, utilice el siguiente ejemplo de PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Puede encontrar el valor extensionName en `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-operations-management-suite"></a>¿Hay un ejemplo de plantilla de conjunto de escalado de máquinas virtuales que se integre con Operations Management Suite?

Para un ejemplo de plantilla de conjunto de escalado de máquinas virtuales que se integre con Operations Management Suite, vea el segundo ejemplo de [Deploy an Azure Service Fabric cluster and enable monitoring by using Log Analytics](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric) (Implementar un clúster de Azure Service Fabric y habilitar la supervisión mediante el uso de Log Analytics).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Parece que las extensiones se ejecutan en paralelo en los conjuntos de escalado de máquinas virtuales. Esto hace que una extensión de script personalizado genere un error. ¿Qué puedo hacer para solucionar esto?

Para aprender sobre la secuenciación de extensión en conjuntos de escalado de máquinas virtuales, consulte [Extension sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Secuenciación de extensión en conjuntos de escalado de máquinas virtuales de Azure).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>¿Cómo se restablece la contraseña para las máquinas virtuales en el conjunto de escalado de máquinas virtuales?

Hay dos formas principales de cambiar la contraseña de las máquinas virtuales en conjuntos de escalado.

- Crear directamente un modelo de conjunto de escalado de máquinas virtuales. Disponible con Compute API 2017-12-01 y versiones posteriores.

    Actualizar las credenciales de administrador directamente en el modelo del conjunto de escalado (por ejemplo, mediante Azure Resource Explorer, PowerShell o CLI). Una vez que el conjunto de escalado está actualizado, todas las nuevas máquinas virtuales tienen nuevas credenciales. Las máquinas virtuales existentes solo tienen las nuevas credenciales si se restablece la imagen inicial. 

- Restablecer la contraseña mediante las extensiones de acceso de la máquina virtual.

    Utilice el siguiente ejemplo de PowerShell:
    
    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}
     
    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>¿Cómo agrego una extensión a todas las máquinas virtuales del conjunto de escalado de máquinas virtuales?

Si la directiva de actualización se establece en **automática**, al volver a implementar la plantilla con las nuevas propiedades de extensión se actualizan todas máquinas virtuales.

Si la directiva de actualización se establece en **manual**, actualice primero la extensión y, a continuación, actualice manualmente todas las instancias en las máquinas virtuales.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Si las extensiones asociadas con un conjunto de escalado de máquinas virtuales existente se actualizan, ¿afectará a las máquinas virtuales ya existentes? (Es decir, ¿*no* coincidirán las máquinas virtuales con el modelo de conjunto de escalado de máquinas virtuales?) ¿O se ignoran? Cuando se recuperan los servicios de una máquina existente o se restablece su imagen inicial, ¿se ejecutarán los scripts que están configurados actualmente en el conjunto de escalado de máquinas virtuales, o se usarán los que se configuraron la primera vez que se creó la máquina?

Si la definición de extensión en el modelo del conjunto de escalado de máquinas virtuales se actualiza y se establece la propiedad upgradePolicy en **automática**, actualiza las máquinas virtuales. Si se establece la propiedad upgradePolicy en **manual**, las extensiones se marcan como que no coinciden con el modelo. 

Si se ha recuperado el servicio de una máquina virtual existente, aparece como un reinicio y las extensiones no se ejecutan de nuevo. Si se restablece su imagen inicial es como a sustituir el disco de sistema operativo con la imagen de origen. Cualquier especialización del modelo más reciente, como las extensiones, se ejecuta.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-azure-ad-domain"></a>¿Cómo puedo unir un conjunto de escalado de máquinas virtuales a un dominio de Azure AD?

Para unir un conjunto de escalado de máquinas virtuales a un dominio de Azure Active Directory (Azure AD), puede definir una extensión. 

Para definir una extensión, utilice la propiedad JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Una extensión de conjunto de escalado de máquinas virtuales está intentando instalar algo que requiere un reinicio. Por ejemplo, "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

Si la extensión de conjunto de escalado de máquinas virtuales está intentando instalar algo que requiere un reinicio, puede usar la extensión de configuración de estado deseado de Azure Automation (DSC de Automatización). Si el sistema operativo es Windows Server 2012 R2, Azure extrae en el programa de instalación de Windows Management Framework (WMF) 5.0, reinicia y continúa con la configuración. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>¿Cómo activo el antimalware en el conjunto de escalado de máquinas virtuales?

Para activar el antimalware en el conjunto de escalado de máquinas virtuales, utilice el siguiente ejemplo de PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Tengo que ejecutar un script personalizado hospedado en una cuenta de almacenamiento privada. El script se ejecuta correctamente cuando el almacenamiento es público, pero cuando intento usar una firma de acceso compartido (SAS), se produce un error. Se muestra este mensaje: "Faltan los parámetros obligatorios para la firma de acceso compartido". Vínculo + SAS funciona bien desde mi explorador local.

Para ejecutar un script personalizado que está hospedado en una cuenta de almacenamiento privado, establezca una configuración protegida con el nombre y la clave de la cuenta de almacenamiento. Para más información, consulte la sección sobre la [Extensión del script personalizado para Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Redes
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>¿Es posible asignar un grupo de seguridad de red (NSG) a un conjunto de escalado, para que se aplique a todas las NIC de VM en el conjunto?

Sí. Un grupo de seguridad de red se puede aplicar directamente a un conjunto de escalado haciendo referencia a él en la sección networkInterfaceConfigurations del perfil de red. Ejemplo:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>¿Cómo hago un intercambio de VIP para conjuntos de escalado de máquinas virtuales de la misma suscripción y la misma región?

Si tiene dos conjuntos de escalado de máquinas virtuales con servidores front-end de Azure Load Balancer, y están en la misma suscripción y región, puede desasignar las direcciones IP públicas de cada uno de ellos y asignarlas al otro. Consulte, por ejemplo, [VIP Swap: Blue-green deployment in Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) (Intercambio de VIP: implementación Blue-green en Azure Resource Manager). Esto implica un retraso ya que los recursos se desasignan y asignan a nivel de red. Una opción más rápida es usar Azure Application Gateway con dos grupos de back-end y una regla de enrutamiento. También puede hospedar la aplicación con [Azure App Service](https://azure.microsoft.com/services/app-service/), que permite realizar un cambio rápido entre las ranuras de ensayo y las de producción.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>¿Cómo especifico un intervalo de direcciones IP privadas para la asignación estática de direcciones IP privadas?

Las direcciones IP se seleccionan de una subred que especifique. 

El método de asignación de direcciones IP de un conjunto de escalado de máquinas virtuales siempre es "dinámico", pero eso no significa que estas direcciones IP pueden cambiar. En este caso, "dinámico" solo significa que no tiene que especificar la dirección IP en una solicitud PUT. Especifique el conjunto estático mediante el uso de la subred. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>¿Cómo se puede implementar un conjunto de escalado de máquinas virtuales en una red virtual de Azure existente? 

Para implementar un conjunto de escalado de máquinas virtuales en una red virtual de Azure existente, consulte el documento sobre [implementación de un conjunto de escalado de máquinas virtuales en una red virtual existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>¿Cómo se agrega la dirección IP de la primera máquina virtual de un conjunto de escalado de máquinas virtuales en la salida de una plantilla?

Para agregar la dirección IP de la primera máquina virtual de un conjunto de escalado de máquinas virtuales en la salida de una plantilla, consulte [AAzure Resource Manager: Get virtual machine scale sets private IPs](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips) (Azure Resource Manager: obtener las IP privadas de los conjuntos de escalado de máquinas virtuales).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>¿Puedo usar conjuntos de escalado con redes aceleradas?

Sí. Para usar las redes aceleradas, establezca enableAcceleratedNetworking en true en los ajustes de networkInterfaceConfigurations de su conjunto de escalado. Por ejemplo:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>¿Cómo puedo configurar los servidores DNS usados por un conjunto de escalado?

Para crear un conjunto de escalado de máquina virtual con una configuración de DNS personalizada, agregue un paquete JSON dnsSettings a la sección de networkInterfaceConfigurations del conjunto de escalado. Ejemplo:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>¿Cómo puedo configurar conjunto de escalado para asignar una dirección IP pública a cada máquina virtual?

Para crear un conjunto de escalado de máquina virtual que asigne una dirección IP pública a cada máquina virtual, asegúrese de que la versión de API del recurso Microsoft.Compute/virtualMAchineScaleSets es 2017-03-30 y agregue un paquete JSON _publicipaddressconfiguration_ a la sección ipConfigurations del conjunto de escalado. Ejemplo:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>¿Puedo configurar un conjunto de escalado para trabajar con varias instancias de Application Gateway?

Sí. Puede agregar el identificador de recurso de varios grupos de direcciones back-end de Application Gateway a la lista _applicationGatewayBackendAddressPools_ de la sección _ipConfigurations_, en el perfil de red del conjunto de escalado.

## <a name="scale"></a>Escala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>¿En qué casos podría crear un conjunto de escalado de máquinas virtuales con menos de dos máquinas virtuales?

Una de las razones para crear un conjunto de escalado de máquinas virtuales con menos de dos máquinas virtuales sería usar las propiedades elásticas de un conjunto de escalado de máquinas virtuales. Por ejemplo, podría implementar un conjunto de escalado de máquinas virtuales con cero máquinas virtuales a fin de definir su infraestructura sin pagar por los costos de ejecución de máquina virtual. Luego, cuando esté listo para implementar máquinas virtuales, aumente la "capacidad" del conjunto de escalado de máquinas virtuales hasta el recuento de instancia de producción.

Otra razón por la que podría crear un conjunto de escalado de máquinas virtuales con menos de dos máquinas virtuales es si le preocupa menos la disponibilidad que el uso de un conjunto de disponibilidad con máquinas virtuales discretas. Los conjuntos de escalado de máquinas virtuales le proporcionan una forma de trabajar con unidades de proceso indiscriminadas que son fungibles. Esta uniformidad es un diferenciador clave entre los conjuntos de escalado de máquinas virtuales y los conjuntos de disponibilidad. Muchas cargas de trabajo sin estado no realizan seguimiento de unidades individuales. Si baja la carga de trabajo, puede reducir verticalmente a una unidad de proceso y escalar verticalmente a varias cuando aumente la carga de trabajo.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>¿Cómo se cambia el número de máquinas virtuales en un conjunto de escalado de máquinas virtuales?

Para cambiar el número de máquinas virtuales en un conjunto de escalado de máquinas virtuales en Azure Portal, en la sección de propiedades del conjunto de escalado de máquinas virtuales, haga clic en la hoja "Escalado" y use el control deslizante. Para conocer otras maneras de cambiar el recuento de instancias, consulte [Change the instance count of a virtual machine scale set](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/) (Cambio del recuento de instancias de un conjunto de escalado de máquinas virtuales).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>¿Cómo puedo definir alertas personalizadas para cuando se alcanzan determinados umbrales?

Dispone de cierta flexibilidad en la manera de controlar las alertas de umbrales especificados. Por ejemplo, puede definir webhooks personalizados. El ejemplo del siguiente webhook procede de una plantilla de Resource Manager:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

En este ejemplo, una alerta va a Pagerduty.com cuando se alcanza un umbral.



## <a name="patching-and-operations"></a>Aplicación de revisiones y operaciones

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>¿Cómo creo un conjunto de escalado en un grupo de recursos existente?

Todavía no es posible crear conjuntos de escalado en un grupo de recursos existente desde Azure Portal, pero puede especificar un grupo de recursos existente al implementar un conjunto de escalado desde una plantilla de Azure Resource Manager. También puede especificar un grupo de recursos existente al crear un conjunto de escalado con Azure PowerShell o CLI.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>¿Podemos mover un conjunto de escalado a otro grupo de recursos?

Sí, se pueden mover recursos del conjunto de escalado a una nueva suscripción o a un nuevo grupo de recursos.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>¿Cómo actualizo mi conjunto de escalado de máquinas virtuales a una nueva imagen? ¿Cómo administro la aplicación de revisiones?

Para actualizar su conjunto de escalado de máquinas virtuales con una nueva imagen y para administrar la aplicación de revisiones consulte [Actualización de un conjunto de escalado de máquinas virtuales](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>¿Puedo usar la operación de restablecimiento de la imagen inicial para restablecer una máquina virtual sin cambiar la imagen? (Es decir, quiero restablecer una máquina virtual a la configuración de fábrica en lugar de a una nueva imagen).

Sí, puede usar la operación de restablecimiento de la imagen inicial para restablecer una máquina virtual sin cambiar la imagen. Sin embargo, si el conjunto de escalado de máquinas virtuales hace referencia a una imagen de plataforma con `version = latest`, la máquina virtual se puede actualizar a una imagen del SO posterior cuando llame a `reimage`.

Para más información, consulte el artículo sobre la [administración de todas las máquinas virtuales de un conjunto de escalado de máquinas virtuales](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-oms-operations-management-suite"></a>¿Es posible integrar conjuntos de escalado con Azure OMS (Operations Management Suite)?

Sí, puede hacerlo instalando la extensión de OMS en las máquinas virtuales del conjunto de escalado. A continuación se ofrece un ejemplo de CLI de Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Puede encontrar el workspaceId y la workspaceKey requeridos en el portal de OMS. En la página Información general, haga clic en el icono Configuración. Haga clic en la pestaña Orígenes conectados en la parte superior.

Nota: Si establece _upgradePolicy_ del conjunto de escalado en Manual, debe aplicar la extensión a todas las máquinas virtuales del conjunto mediante una llamada a la actualización. En CLI, esto sería _az vmss update-instances_.

## <a name="troubleshooting"></a>solución de problemas

### <a name="how-do-i-turn-on-boot-diagnostics"></a>¿Cómo se activa el diagnóstico de arranque?

Para activar el diagnóstico de arranque, en primer lugar, cree una cuenta de almacenamiento. Luego coloque este bloque JSON en el elemento **virtualMachineProfile** del conjunto de escalado de máquinas virtuales y actualice el conjunto:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Cuando se crea una nueva máquina virtual, el elemento InstanceView de la máquina virtual, muestran los detalles de la captura de pantalla, etc. Este es un ejemplo:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Propiedades de máquina virtual

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>¿Cómo obtengo información de propiedades de cada máquina virtual sin tener que realizar varias llamadas? Por ejemplo, ¿cómo obtendría el dominio de error para cada una de las 100 máquinas virtuales del conjunto de escalado de máquinas virtuales?

Para informarse sobre propiedad de cada máquina virtual sin realizar varias llamadas, puede llamar a `ListVMInstanceViews` realizando un `GET` de API de REST en el siguiente URI de recurso:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>¿Puedo pasar diferentes argumentos de extensión a diferentes máquinas virtuales de un conjunto de escalado de máquinas virtuales?

No, no puede pasar diferentes argumentos de extensión a diferentes máquinas virtuales de un conjunto de escalado de máquinas virtuales. De todas formas, las extensiones pueden actuar en función de propiedades únicas de la máquina virtual en la que se ejecutan, como el nombre de la máquina. Además, las extensiones pueden consultar metadatos de instancias en http://169.254.169.254 para más información sobre la máquina virtual.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>¿Por qué hay huecos entre los nombres de máquina virtual de mi conjunto de escalado de máquinas virtuales y los identificadores de máquina virtual? Por ejemplo: 0, 1, 3...

Hay huecos entre los nombres de máquina virtual del conjunto de escalado de máquinas virtuales y el identificador de la máquina virtual porque la propiedad **overprovision** del conjunto de escalado de máquinas virtuales está establecida en el valor predeterminado de **true**. Si la propiedad overprovision se establece en **true**, se crean más máquinas de las solicitadas. Las máquinas virtuales adicionales se eliminan a continuación. En este caso, lo que consigue es una mayor confiabilidad en la implementación a cambio de reglas de traducción de direcciones de red (NAT) contiguas y de nomenclatura contiguas. 

Puede establecer esta propiedad en **false**. Para conjuntos de escalado de máquinas virtuales pequeños, esto no afecta significativamente a confiabilidad de la implementación.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>¿Cuál es la diferencia entre eliminar una máquina virtual de un conjunto de escalado y desasignar la máquina virtual? ¿Cuándo debo elegir una u otra opción?

La principal diferencia entre eliminar una máquina virtual en un conjunto de escalado de máquinas virtuales y cancelar la asignación de la máquina virtual es que `deallocate` no elimina los discos duros virtuales (VHD). Hay costos de almacenamiento asociados con la ejecución de `stop deallocate`. Puede usar uno de los dos por una de las siguientes razones:

- Quiere dejar de pagar por los costos de proceso pero mantener el estado del disco de las máquinas virtuales.
- Quiere iniciar un conjunto de máquinas virtuales más rápidamente de lo que puede escalar horizontalmente un conjunto de escalado de máquinas virtuales.
  - Relacionado con este escenario, puede haber creado su propio motor de escalado automático y quiere un escalado completo más rápido.
- Tiene un conjunto de escalado de máquinas virtuales que se distribuye de forma irregular a través de dominios de error o dominios de actualización. Esto puede ser porque eliminó de forma selectiva las máquinas virtuales, o porque se eliminaron las máquinas virtuales después proveer en exceso. Ejecutar `stop deallocate` seguido de `start` en el conjunto de escalado de máquinas virtuales distribuye de manera uniforme las máquinas virtuales a través de dominios de error o dominios de actualización.

