---
title: "P+F sobre los conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga respuestas a preguntas frecuentes sobre los conjuntos de escalado de máquinas virtuales."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 850459a79c723de0eb3249cfe9ea19bc988ca318
ms.lasthandoff: 03/18/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>P+F sobre los conjuntos de escalado de máquinas virtuales de Azure

Este artículo contiene respuestas a preguntas frecuentes sobre los conjuntos de escalado.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>¿Cuáles son los procedimientos recomendados para el escalado automático de Azure?

Sí. Consulte https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices.

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>¿Dónde puedo encontrar los nombres de métricas para el escalado automático mediante métricas basadas en host?

https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/.

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>¿Hay algún ejemplo de escalado automático basado en una longitud de cola y un tema de Service Bus?

Sí. Consulte:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/.

Para colas de Service Bus:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Para colas de almacenamiento:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Reemplace estos valores de ejemplo por los identificadores URI adecuados.


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>¿Debemos realizar el escalado automático con métricas basadas en host o usar una extensión de diagnóstico?

Puede crear una configuración de escalado automático en una máquina virtual para usar las métricas de nivel de host, o usar las métricas basadas en SO invitado.

Consulte la lista de métricas admitidas: https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics. Este es un ejemplo completo de conjuntos de escalado (en este caso hemos usado la métrica de CPU de nivel de host y una métrica de recuento de mensajes):

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets.

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>¿Cómo puedo establecer reglas de alerta en un conjunto de escalado?

Puede crear alertas sobre métricas de conjuntos de escalado mediante PS o la CLI. Consulte:

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts.

El valor de TargetResourceId del conjunto de escalado tiene este aspecto: /subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Puede elegir cualquier contador de rendimiento de máquina virtual como métrica sobre la que establecer la alerta:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os.

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os.

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>¿Cómo puedo configurar el escalado automático en un conjunto de escalado mediante PowerShell?

Consulte https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/.




## <a name="certificates"></a>Certificados

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>¿Cómo se envía de forma segura un certificado en la máquina virtual?  ¿Existe un ejemplo de aprovisionamiento de un conjunto de escalado para ejecutar un sitio web donde el SSL del sitio web se envía de forma segura a partir de una configuración de certificado?  La operación común de rotación de certificados equivaldría a una operación de actualización de la configuración.

Se admite la instalación de certificados de cliente directamente en el almacén de certificados de Windows desde su almacén de claves.

En el contexto de conjuntos de escalado...

https://msdn.microsoft.com/library/mt589035.aspx.

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

Admite Windows y Linux.

### <a name="self-signed-certificate-example"></a>Ejemplo de certificado autofirmado:

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>Creación de un certificado autofirmado en una instancia de KeyVault

Una manera de crear un certificado autofirmado en una instancia de KeyVault es usar las instrucciones de este artículo de Service Fabric: https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/.

Los comandos de PowerShell:

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

El comando anterior le proporciona la entrada para la plantilla de Resource Manager.

#### <a name="change-resource-manager-template"></a>Cambio de la plantilla de Resource Manager

Agregue esta propiedad a "virtualMachineProfile" como parte del recurso del conjunto de escalado:

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
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>¿Hay una manera de especificar un par de claves SSH que quiero usar para la autenticación de SSH con un conjunto de escalado de Linux desde una plantilla de Resource Manager?  

La API de REST de osProfile es parecida al caso de la máquina virtual normal:
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration.
 
Incluya `osProfile` en su plantilla como en el ejemplo siguiente:

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
 
Este bloque JSON se usa en la siguiente plantilla de inicio rápido:
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json.
 
Examine también el perfil de SO en esta plantilla:
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json.

### <a name="how-do-i-remove-deprecated-certificates"></a>¿Cómo se quitan los certificados en desuso? 

Debe quitar el certificado antiguo de la lista de certificados del almacén, pero dejar todos los certificados que quiere que permanezcan en la máquina. Con esto, no se quita el certificado de todas las máquinas virtuales, pero tampoco lo agrega a las nuevas máquinas virtuales que se crean en el conjunto de escalado. Para quitar el certificado de las máquinas virtuales existentes, debe escribir una extensión de script personalizado que quite los certificados del almacén de certificados manualmente.
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>¿Cómo tomo una clave pública SSH existente y la inyecto en la capa SSH del conjunto de escalado durante el aprovisionamiento?  Quisiera almacenar los valores de clave pública SSH en Azure Key Vault y luego usarlos en la plantilla de Resource Manager.

Si solo va a proporcionar a las máquinas virtuales una clave pública SSH, no hay razón para colocar las claves públicas en el almacén de claves ya que no son secretas.
 
Puede proporcionar claves públicas SSH en texto sin formato al crear una máquina virtual Linux.
Aquí puede encontrar un ejemplo:

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json.
 
Concretamente:

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
Nombre del elemento de linuxConfiguration | Obligatorio | Tipo | Descripción
--- | --- | --- | --- |  ---
ssh | No | Colección | Especifica la configuración de la clave SSH para un sistema operativo Linux.
path | Sí | String | Especifica la ruta de acceso del archivo de Linux en el que se deben colocar las claves o el certificado SSH.
keyData | Sí | String | Especifica una clave pública SSH codificada en base64.
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>Cuando ejecuto Update-AzureRmVmss después de varios certificados desde la misma instancia de KeyVault, recibo el siguiente error:
 
Update-AzureRmVmss: muestra secretos que contienen instancias repetidas de /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, lo que no se permite. ¿Por qué no puedo agregar dos certificados desde la misma instancia de KeyVault?
 
Este comportamiento puede suceder si intenta agregar el mismo almacén dos veces en lugar de un nuevo valor de vaultCertificate para el valor de sourceVault existente. Add-AzureRmVmssSecret no funciona correctamente para agregar secretos adicionales.
 
Si quiere agregar más secretos desde el mismo almacén de claves, debe actualizar la lista $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Puede ver la estructura de entrada esperada aquí: https://msdn.microsoft.com/library/azure/mt589035.aspx.
 
Deberá buscar el secreto en el objeto de conjunto de escalado que tenga el mismo almacén de claves contenedor. Luego, debe agregar la referencia del certificado (la dirección URL junto con el nombre del secreto) a la lista asociada con el almacén.

Nota: Actualmente no se admite la eliminación de certificados de las máquinas virtuales mediante las API de conjunto de escalado.
 
Las nuevas máquinas virtuales no tendrán el certificado antiguo, pero las que tenían el certificado ya implementado lo seguirán teniendo.
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>¿Existe actualmente alguna manera de insertar certificados en el conjunto de escalado sin proporcionar la contraseña cuando el certificado está en SecretStore?

No es necesario codificar las contraseñas en los scripts; puede recuperarlas dinámicamente con cualquier permiso con el que se ejecute el script de implementación que tenga. Si tiene un script que va a mover un certificado del almacén de secretos al almacén de claves, el comando get certificate del almacén de secretos también genera la contraseña del archivo pfx.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>¿Cómo funciona la propiedad de secretos de virtualMachineProfile.osProfile de un conjunto de escalado? ¿Por qué hace falta sourceVault cuando se tiene que especificar el URI absoluto para un certificado con certificateUrl? 

Una referencia de certificado de WinRM debe estar presente en la propiedad de secretos del perfil de sistema operativo. 

La finalidad de indicar el almacén de origen es poder exigir las directivas de ACL que existen en CSM. Sin especificar el almacén de origen, los usuarios que no tengan permisos para implementar secretos o acceder a ellos en un almacén de claves podrían hacerlo mediante CRP. Las ACL existen incluso para recursos que no existen.

Si proporcionó un identificador de sourceVault incorrecto pero una dirección URL de almacén de claves válida, notificaremos un error cuando sondee la operación.
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>Si agrego secretos a un conjunto de escalado existente, ¿se inyectarán en instancias existentes o solo en las nuevas? 

Los certificados se agregan a todas las máquinas virtuales, incluso a las ya existentes. Si la propiedad upgradePolicy del conjunto de escalado está establecida en "Manual", el certificado se agrega a la máquina virtual al realizar una actualización manual en ella.
 
### <a name="where-do-certificates-go-for-linux-vms"></a>¿Dónde van los certificados para las máquinas virtuales Linux?

Consulte https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/.
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>¿Cómo se agrega un nuevo certificado de almacén a un nuevo objeto de certificado?

Si quiere agregar un certificado de almacén al secreto existente, que debe ser el único objeto de secreto, puede hacerlo como en el siguiente ejemplo de PowerShell:
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>¿Qué ocurre con los certificados si restablece la imagen inicial de una máquina virtual?

Si restablece la imagen inicial de una máquina virtual, los certificados desaparecen porque con esta acción se elimina por completo el disco del SO. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>¿Qué ocurre si se elimina un certificado del almacén de claves?

Si se elimina el secreto del almacén de claves y deja de desasignar todas las máquinas virtuales y luego las inicia de nuevo, se encontrará con un error. Este error se debe a la necesidad de CRP de recuperar los secretos de Key Vault pero ser incapaz de hacerlo. En este escenario, puede eliminar los certificados del modelo de conjunto de escalado. 

El componente de CRP no conserva los secretos de cliente. Si detiene la desasignación de todas las máquinas virtuales del conjunto de escalado, la caché se elimina. En este escenario, los secretos se recuperan del almacén de claves.

Este problema no se encuentra en el escalado horizontal porque hay una copia en caché del secreto en la estructura (al menos en el modelo de único inquilino de estructura).
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>¿Por qué tenemos que especificar la ubicación exacta de la dirección URL del certificado, como se hace referencia aquí: según https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/? 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>.
 
Según la documentación de KeyVault, la API de REST get-secret debe devolver la versión más reciente del secreto si no se especifica la versión:
 
Método | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Reemplace {secret-name} por el nombre y {secret-version} por la versión del secreto que quiere recuperar. Se puede excluir la versión del secreto, en cuyo caso se recupera la versión actual.
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>¿Por qué se debe especificar la versión del certificado al usar el almacén de claves?

El motivo de este requisito es aclarar al usuario qué certificado se implementa en sus máquinas virtuales.

Si crea una máquina virtual y luego actualiza el secreto en el almacén de claves, ese nuevo certificado no se descarga en las máquinas virtuales. Pero parecerá que las máquinas virtuales hacen referencia a él y las nuevas máquinas virtuales obtendrán el nuevo secreto. Para evitar esta confusión, es necesario que haga referencia a una versión de secreto explícita.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>Mi equipo funciona con varios certificados que se distribuyen como claves públicas .cer. ¿Cuál es el enfoque recomendado para la implementación de estos certificados en un conjunto de escalado?

Puede generar un archivo pfx que solo contenga archivos .cer, con X509ContentType = Pfx. Por ejemplo, cargue el archivo .cer como un objeto x509Certificate2 en C# o PowerShell y mediante la llamada a este método: https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx.

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>No veo una opción para que los usuarios pasen certificados como cadenas base64 que proporcionan la mayoría de los otros proveedores de recursos.

Puede extraer la dirección URL con la versión más reciente en una plantilla de Resource Manager para emular el comportamiento que describe. Puede incluir la siguiente propiedad JSON en la plantilla de Resource Manager:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>¿Tenemos que incluir los certificados de objetos JSON en almacenes de claves?

Se trata de un requisito de las máquinas virtuales y los conjuntos de escalado. También se admite el tipo de contenido application/x-pkcs12. Las instrucciones se encuentran aquí: http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/.
 
Actualmente no se admiten archivos .cer; debe exportarlos en contenedores pfx.





## <a name="compliance"></a>Cumplimiento normativo

### <a name="are-scale-sets-pci-compliant"></a>¿Son los conjuntos de escalado compatibles con PCI?

Los conjuntos de escalado son una capa de API delgada encima del proveedor de recursos de proceso, todo lo cual es una parte del área "Plataforma de procesos" dentro del árbol de servicios de Azure.

Por lo tanto, desde la perspectiva del cumplimiento, los conjuntos de escalado son una pieza fundamental de la plataforma de procesos de Azure. Como tales, comparten el mismo equipo, herramientas, procesos, metodología de implementación, controles de seguridad, JIT, características de supervisión, alertas, etc. que el proveedor de recursos de Compute (CRP) propiamente dicho.  Los conjuntos de escalado son compatibles con PCI porque el proveedor de recursos de Compute forma parte de la atestación actual de PCI DSS:

Para más información, consulte: [https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI).






## <a name="extensions"></a>Extensiones

### <a name="how-do-you-delete-a-scale-set-extension"></a>¿Cómo se elimina una extensión de conjunto de escalado?

A continuación se muestra un ejemplo con PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
El valor de extensionName se puede encontrar en `$vmss`.
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>¿Existe un ejemplo de plantilla de conjunto de escalado que se integre con OMS?

Examine este segundo ejemplo:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric.
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>Las extensiones parecen ejecutarse en paralelo en conjuntos de escalado, lo que hace que la extensión de script personalizado produzca error. ¿Qué puedo hacer para solucionar este comportamiento?

Consulte https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/. 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>¿Cómo restablezco la contraseña para máquinas virtuales del conjunto de escalado?

Use extensiones de acceso de máquina virtual.

A continuación se muestra un ejemplo con PowerShell:

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>¿Cómo agrego una extensión a todas las máquinas virtuales del conjunto de escalado?

- Si la directiva de actualización se establece en automática, al volver a implementar la plantilla con las nuevas propiedades de extensión se actualiza cada máquina virtual.
- Si la directiva de actualización se establece en manual, debe actualizar la extensión y luego realizar una acción manualUpdate en todas las instancias.
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>Si las extensiones asociadas con un conjunto de escalado existente están actualizadas, ¿afectarán a las máquinas virtuales ya existentes? (es decir, ¿se mostrarán las máquinas virtuales como no coincidentes con el modelo de conjunto de escalado)? ¿O se ignorarán? Cuando se recuperan los servicios de una máquina existente, se restablece su imagen inicial, etc., ¿se ejecutarán los scripts que están configurados actualmente en el conjunto de escalado o se usarán los que se configuraron la primera vez que se creó la máquina?

- Si se actualiza la definición de extensión en el modelo de conjunto de escalado, las máquinas virtuales se actualizarán si upgradePolicy se establece en automático, y se marcarán como no coincidentes con el modelo si upgradePolicy se establece en manual. 

- Si se ha recuperado el servicio de una máquina virtual existente, aparecerá como un reinicio y las extensiones no se ejecutarán de nuevo. Si se restablece su imagen inicial, será como sustituir la unidad de SO por la imagen de origen y se ejecutará cualquier especialización del modelo más reciente, como la extensiones.
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>¿Cómo consigo que un conjunto de escalado se una a un dominio de AD?

Podría definir una extensión como esta mediante JsonADDomainExtension, por ejemplo:
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
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
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Mi extensión de conjunto de escalado está intentando instalar algo que requiere un reinicio, por ejemplo: "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools".

Podría usar la extensión de DSC. Si el sistema operativo es 2012 R2, Azure se extrae en el programa de instalación de WMF5.0, se reinicia y continúa con la configuración. 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>¿Cómo puedo habilitar el software antimalware en mi conjunto de escalado?

Este es un ejemplo con PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>Tengo que ejecutar un script personalizado hospedado en una cuenta de almacenamiento privada. No tengo problemas cuando el almacenamiento es público pero cuando intento usar una firma de acceso compartido (SAS) recibo el error: "Faltan los parámetros obligatorios para la firma de acceso compartido". Sé que ese vínculo + SAS funciona bien desde mi explorador local.

Debe configurar las opciones protegidas con la clave y el nombre de la cuenta de almacenamiento para que este escenario funcione. Consulte https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings.







## <a name="networking"></a>Redes
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>¿Cómo hago un intercambio de VIP para conjuntos de escalado de la misma suscripción y la misma región?

Consulte: https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/. 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for"></a>¿Para qué sirve la propiedad resourceGuid en una NIC?

Es un identificador único. Las capas inferiores registrarán este identificador en algún momento en el futuro. 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>¿Cómo especifico un intervalo de direcciones IP privadas para la asignación de direcciones IP privadas?

Las direcciones IP se seleccionan de una subred que especifique. 

El método de asignación de las direcciones IP del conjunto de escalado es siempre "Dinámico". Eso no significa que se puedan cambiar estas direcciones IP, sino que no se especifica la dirección IP en la solicitud PUT. En otras palabras, especifica el conjunto estático mediante la subred. 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>¿Cómo se implementa un conjunto de escalado en una VNET existente? 

Consulte https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet. 

### <a name="how-do-i-add-a-scale-sets-first-vms-ip-address-to-the-output-of-a-template"></a>¿Cómo se agrega la dirección IP de la primera máquina virtual del conjunto de escalado a la salida de una plantilla?

Consulte: http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips



## <a name="scale"></a>Escala

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>¿Por qué crearía en algún momento un conjunto de escalado con menos de 2 máquinas virtuales?

Un motivo sería usar las propiedades estáticas de un conjunto de escalado. Por ejemplo, podría implementar un conjunto de escalado con cero máquinas virtuales a fin de definir su infraestructura sin pagar por los costos de ejecución de la máquina virtual. Luego, cuando esté listo para implementar máquinas virtuales, lo hace aumentando la "capacidad" del conjunto de escalado hasta el recuento de instancias de producción.

Otro motivo sería cuando hace algo con su conjunto de escalado donde no le preocupa la disponibilidad en el mismo sentido que el uso de un conjunto de disponibilidad con máquinas virtuales discretas. Los conjuntos de escalado agregan una forma de trabajar con unidades de proceso indiscriminadas que son intercambiables. Esta uniformidad es un diferenciador clave entre conjuntos de escalado y conjuntos de disponibilidad. A muchas cargas de trabajo sin estado no les preocupan las unidades individuales, y pueden reducirse verticalmente a una unidad de proceso si la carga de trabajo desciende y luego volver a muchas de estas unidades cuando la carga de trabajo aumenta.

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>¿Cómo cambio el número de máquinas virtuales de un conjunto de escalado?

Consulte: https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/.

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>¿Cómo puedo definir alertas personalizadas para cuando se alcanzan determinados umbrales?

Dispone de cierta flexibilidad sobre el modo de gestionar las alertas; por ejemplo, puede definir webhooks personalizados como este desde una plantilla de Resource Manager:
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
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

En este ejemplo, una alerta va a Pagerduty cuando se alcanza un umbral.



## <a name="troubleshooting"></a>Solución de problemas

### <a name="how-do-i-enable-boot-diagnostics"></a>¿Cómo se habilita el diagnóstico de arranque?

Cree una cuenta de almacenamiento y coloque este bloque JSON en el elemento virtualMachineProfile del conjunto de escalado. Luego, actualice el conjunto de escalado:
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

A continuación, cuando se crea una nueva máquina virtual en el elemento InstanceView de la máquina virtual, se muestran los detalles de la captura de pantalla, etc. Por ejemplo:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>Actualizaciones

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>¿Cómo actualizo mi conjunto de escalado a una nueva imagen y administro la aplicación de revisiones?

Consulte: https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>¿Se puede usar la operación de restablecimiento de la imagen inicial para restablecer una máquina virtual sin cambiar la imagen? (es decir, ¿restablecer una máquina virtual a la configuración de fábrica en lugar de a una nueva imagen)?

Sí. Consulte https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set.

Sin embargo, si el conjunto de escalado hace referencia a una imagen de plataforma con version = "latest", la máquina virtual se puede actualizar a una imagen de SO posterior cuando llame al restablecimiento de imagen inicial.







## <a name="vm-properties"></a>Propiedades de máquina virtual

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>¿Cómo obtengo información de propiedades de cada máquina virtual sin tener que realizar varias llamadas? Por ejemplo, ¿cómo obtengo el dominio de error para cada máquina virtual en mi conjunto de escalado 100?

Puede llamar a ListVMInstanceViews mediante una API de REST `GET` en el siguiente identificador URI de recursos:

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>¿Hay alguna manera de pasar diferentes argumentos de extensión a diferentes máquinas virtuales de un conjunto de escalado?

No, pero las extensiones pueden actuar en función de propiedades únicas de la máquina virtual en la que se ejecutan, como el nombre de la máquina. Además, las extensiones pueden consultar metadatos de instancias en http://169.254.169.254 para obtener más información.

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>¿Por qué hay huecos entre los nombres de máquina virtual de mi conjunto de escalado y los identificadores de máquina virtual? Por ejemplo: 0, 1, 3...

Hay huecos porque la propiedad de sobreaprovisionamiento del conjunto de escalado está establecida en el valor predeterminado de true. Cuando esto sucede, se crean más máquinas virtuales que las solicitadas, y las máquinas virtuales adicionales se eliminan posteriormente. Lo que consigue es una mayor confiabilidad en la implementación a cambio de reglas NAT contiguas y de nomenclatura contiguas. Puede establecer esta propiedad en false, y para conjuntos de escalado pequeños no habrá mucha diferencia en la confiabilidad de la implementación.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>¿Cuál es la diferencia entre eliminar una máquina virtual de un conjunto de escalado y desasignar la máquina virtual? ¿Cuándo debo elegir una u otra opción?

La principal diferencia es que la desasignación no elimina los discos duros virtuales, por lo que hay costos de almacenamiento asociados con detener esta operación. Algunas de las razones de elegir una u otra solución serían:

- Quiere dejar de pagar por los procesos pero mantener el estado del disco de las máquinas virtuales.
- Quiere iniciar un conjunto de máquinas virtuales más rápido que escalando horizontalmente un conjunto de escalado.
  - Relacionado con este escenario, creó su propio motor de escalado automático y quiere un escalado de extremo a extremo más rápido.
  - Tiene un conjunto de escalado que está distribuido de forma desigual entre FD/UD (debido a la eliminación selectiva de máquinas virtuales o a máquinas virtuales que se eliminan tras el sobreaprovisionamiento). Dejar de desasignar y luego iniciar el conjunto de escalado distribuirá uniformemente las máquinas virtuales entre FD/UD.








 
   

