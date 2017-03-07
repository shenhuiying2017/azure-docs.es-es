---
title: "Creación de un clúster de Azure Service Fabric a partir de una plantilla | Microsoft Docs"
description: "En este artículo se describe cómo configurar un clúster seguro de Service Fabric en Azure mediante Azure Resource Manager, Azure Key Vault y Azure Active Directory (Azure AD) para la autenticación de clientes."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: b80ee30379e9aac207cfe420cae17da57ea123a5
ms.openlocfilehash: 9159f40fed17e52e6576efa1ea7e8a2dee98728e
ms.lasthandoff: 03/02/2017


---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Creación de un clúster de Service Fabric con Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal de Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Esta guía paso a paso le lleva por la configuración de un clúster seguro de Azure Service Fabric en Azure con Azure Resource Manager. Sabemos que el artículo es largo. No obstante, a menos que ya esté familiarizado con el contenido, asegúrese de seguir cada paso con cuidado.

La guía abarca los siguientes procedimientos:

* Configuración de un almacén de claves de Azure para cargar certificados de seguridad de clúster y aplicación
* Creación de un clúster protegido en Azure con Azure Resource Manager
* Autenticación de usuarios con Azure Active Directory (Azure AD) para la administración del clúster

Un clúster seguro es el que impide el acceso no autorizado a las operaciones de administración. Esto incluye la implementación, la actualización y la eliminación de aplicaciones y servicios, y los datos que contienen. Un clúster no seguro es un clúster al que cualquiera puede conectarse en cualquier momento y realizar operaciones de administración. Aunque se puede crear un clúster no seguro, se recomienda firmemente crear uno seguro desde el principio. Los clústeres no seguros no se pueden proteger en un momento posterior, por lo que se debe crear uno nuevo.

El concepto de creación de clústeres seguros es el mismo, tanto si son de Linux como de Windows. Para más información y scripts auxiliares para crear clústeres seguros de Linux, consulte [Creating secure clusters on Linux](#secure-linux-clusters) (Creación de clústeres seguros en Linux).

## <a name="sign-in-to-your-azure-account"></a>Inicio de sesión en la cuenta de Azure.
En esta guía se usa [Azure PowerShell][azure-powershell]. Al iniciar una nueva sesión de PowerShell, inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.

Inicio de sesión en la cuenta de Azure

```powershell
Login-AzureRmAccount
```

Seleccione su suscripción:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Configuración de un almacén de claves
En esta sección se habla de la creación de un almacén de claves para un clúster de Service Fabric en Azure y aplicaciones de Service Fabric. Para una guía completa sobre Azure Key Vault, consulte [Introducción a Azure Key Vault][key-vault-get-started].

Service Fabric usa certificados X.509 para proteger un clúster y proporcionar características de seguridad de las aplicaciones. Azure Key Vault se usa para administrar certificados para clústeres de Service Fabric en Azure. Cuando un clúster se implementa en Azure, el proveedor de recursos de Azure responsable de crear clústeres de Service Fabric extrae los certificados de Key Vault y los instala en las máquinas virtuales del clúster.

En el diagrama siguiente se ilustra la relación entre Azure Key Vault, un clúster de Service Fabric y el proveedor de recursos de Azure que usa los certificados almacenados en el almacén de claves cuando se crea el clúster:

![Diagrama de instalación del certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
El primer paso es crear un grupo de recursos específico para el almacén de claves. Se recomienda colocar el almacén de claves en su propio grupo de recursos. Esto le permite quitar los grupos de recursos de proceso y almacenamiento, incluido el grupo de recursos que tiene el clúster de Service Fabric, sin perder las claves y los secretos. El grupo de recursos que contiene el almacén de claves _debe estar en la misma región_ que el clúster que lo usa.

Si tiene previsto implementar clústeres en varias regiones, se recomienda denominar el grupo de recursos y el almacén de claves de manera que indique la región a la que pertenecen.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
El resultado debe ser similar al siguiente:

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>Cree un almacén de claves en el nuevo grupo de recursos
El almacén de claves _debe estar habilitado para la implementación_ de forma que el proveedor de recursos de proceso pueda recibir de él certificados e instalarlos en instancias de máquina virtual:

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

El resultado debe ser similar al siguiente:

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>Uso de un almacén de claves existente

Para usar un almacén de claves existente, _debe estar habilitado para la implementación_, de forma que el proveedor de recursos de proceso pueda recibir de él certificados e instalarlos en nodos de clúster:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Introducción de certificados al almacén de claves

Los certificados se usan en Service Fabric para proporcionar autenticación y cifrado con el fin de proteger diversos aspectos de un clúster y sus aplicaciones. Para obtener más información sobre el modo en que se usan los certificados en Service Fabric, consulte los [Escenarios de seguridad de los clústeres de Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificado de clúster y servidor (obligatorio)
Este certificado es necesario para proteger un clúster e impedir el acceso no autorizado. Ofrece seguridad al clúster de dos maneras:

* Autenticación del clúster: se autentica la comunicación de nodo a nodo para la federación del clúster. Solo los nodos que pueden probar su identidad con este certificado pueden unirse al clúster.
* Autenticación del servidor: los puntos de conexión de administración del clúster se autentican en un cliente de administración, de forma que este sabe que está hablando con el clúster real. Este certificado proporciona también SSL para la API de administración de HTTPS y para Service Fabric Explorer sobre HTTPS.

Para servir a estos propósitos, el certificado debe cumplir los siguientes requisitos:

* El certificado debe contener una clave privada.
* El certificado debe crearse para el intercambio de claves, que se pueda exportar a un archivo Personal Information Exchange (.pfx).
* El nombre de sujeto del certificado debe coincidir con el dominio usado para acceder al clúster de Service Fabric. Esta coincidencia es necesaria para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio .cloudapp.azure.com. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

### <a name="application-certificates-optional"></a>Certificados de aplicación (opcionales)
Se puede instalar un número cualquiera de certificados adicionales en un clúster para proteger la aplicación. Antes de crear el clúster, tenga en cuenta los escenarios de seguridad de las aplicaciones que requieren que se instale un certificado en los nodos, por ejemplo:

* Cifrado y descifrado de los valores de configuración de aplicación.
* Cifrado de datos entre nodos durante la replicación.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formato de certificados para el uso del proveedor de recursos de Azure
Puede agregar y usar archivos de claves privadas (.pfx) directamente a través del almacén de claves. Sin embargo, el proveedor de recursos de proceso de Azure requiere que las claves se almacenen en un formato especial de notación de objetos JavaScript (JSON). El formato incluye el archivo .pfx como cadena con codificación de base 64 y la contraseña de clave privada. Para cumplir estos requisitos, las claves deben estar colocadas en una cadena JSON y luego almacenarse como "secretos" en el almacén de claves.

Para facilitar este proceso, hay un módulo de PowerShell [disponible en GitHub][service-fabric-rp-helpers]. Para usar el módulo, haga lo siguiente:

1. Descargue todo el contenido del repositorio en un directorio local.
2. Vaya al directorio local.
2. Importe el módulo ServiceFabricRPHelpers en la ventana de PowerShell:

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

El comando `Invoke-AddCertToKeyVault` de este módulo de PowerShell convierte automáticamente una clave privada de certificado en una cadena JSON y la carga en el almacén de claves. Use el comando para agregar el certificado de clúster y cualquier certificado de aplicación adicional al almacén de claves. Repita este paso con cualquier certificado adicional que quiera instalar en el clúster.

#### <a name="uploading-an-existing-certificate"></a>Carga de un certificado existente

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Si se produce un error, como el que se muestra aquí, suele significar que tiene un conflicto con la dirección URL del recurso. Para resolverlo, cambie el nombre del almacén de claves.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Una vez resuelto, el resultado debería tener este aspecto:

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>Se necesitan las tres cadenas anteriores, CertificateThumbprint, SourceVault y CertificateURL, para configurar un clúster seguro de Service Fabric y obtener los certificados de aplicación que quizá esté usando para la seguridad de la aplicación. Si no guarda las cadenas, puede ser difícil recuperarlos mediante una consulta al almacén de claves más adelante.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>Creación de un certificado autofirmado para cargarlo en el almacén de claves

Si ya ha cargado los certificados en el almacén de claves, omita este paso. Este paso es para generar un nuevo certificado autofirmado y cargarlo en el almacén de claves. Después de cambiar los parámetro del script siguiente y ejecutarlo, se le solicitará una contraseña de certificado.  

```powershell

$ResouceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Si se produce un error, como el que se muestra aquí, suele significar que tiene un conflicto con la dirección URL del recurso. Para resolver el conflicto, cambie el nombre del almacén de claves, el nombre del grupo de recursos, etc.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Una vez resuelto, el resultado debería tener este aspecto:

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>Se necesitan las tres cadenas anteriores, CertificateThumbprint, SourceVault y CertificateURL, para configurar un clúster seguro de Service Fabric y obtener los certificados de aplicación que quizá esté usando para la seguridad de la aplicación. Si no guarda las cadenas, puede ser difícil recuperarlos mediante una consulta al almacén de claves más adelante.

 En este punto, debería tener los siguientes elementos en su lugar:

* El grupo de recursos del almacén de claves.
* El almacén de claves y su dirección URL (llamado SourceVault en la salida de PowerShell anterior).
* El certificado de autenticación de servidor de clúster y su dirección URL en el almacén de claves.
* Los certificados de aplicación y sus direcciones URL en el almacén de claves.


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configuración de Azure Active Directory para la autenticación de cliente

Azure AD permite a las organizaciones (conocidas como inquilinos) administrar el acceso de los usuarios a las aplicaciones. Las aplicaciones se dividen en las que tienen interfaz de usuario de inicio de sesión basada en web y las que tienen una experiencia de cliente nativa. En este artículo se supone que ya ha creado un inquilino. En caso de que no lo haya hecho, lea [Obtención de un inquilino de Azure Active Directory][active-directory-howto-tenant].

Un clúster de Service Fabric ofrece diversos puntos de entrada a su funcionalidad de administración, como [Service Fabric Explorer][service-fabric-visualizing-your-cluster] y [Visual Studio][service-fabric-manage-application-in-visual-studio] basados en web. Como resultado, creará dos aplicaciones de Azure AD para controlar el acceso al clúster, una aplicación web y otra nativa.

Para simplificar algunos de los pasos necesarios para configurar Azure AD con un clúster de Service Fabric, hemos creado un conjunto de scripts de Windows PowerShell.

> [!NOTE]
> Debe realizar los pasos siguientes antes de crear el clúster. Dado que los scripts esperan los puntos de conexión y los nombres de los clústeres, los valores deben planearse y no se pueden usar los que ya haya creado.

1. [Descargue los scripts][sf-aad-ps-script-download] en su equipo.
2. Haga clic con el botón derecho en el archivo zip, seleccione **Propiedades** y la casilla **Desbloquear**, y haga clic en **Aplicar**.
3. Extraiga el archivo ZIP.
4. Ejecute `SetupApplications.ps1` y proporcione los parámetros TenantId, ClusterName y WebApplicationReplyUrl. Por ejemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Ejecute el comando `Get-AzureSubscription` de PowerShell para encontrar su TenantId. Al ejecutar este comando se muestra el valor de TenantId para cada suscripción.

    El valor de ClusterName se usa como prefijo en las aplicaciones de Azure AD que crea el script. No es necesario que coincida exactamente con el nombre del clúster real. Está diseñado solo para facilitar la asignación de artefactos de Azure AD al clúster de Service Fabric con el que se utilizan.

    El valor de WebApplicationReplyUrl es el punto de conexión predeterminado al que Azure AD devuelve a los usuarios cuando terminan el inicio de sesión. Establézcalo como el punto de conexión de Service Fabric Explorer para el clúster, que de manera predeterminada es el siguiente:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Se le pedirá que inicie sesión en una cuenta con privilegios administrativos para el inquilino de Azure AD. Una vez iniciada la sesión, el script creará las aplicaciones web y nativa para representar el clúster de Service Fabric. Si examina las aplicaciones del inquilino en el [Portal de Azure clásico][azure-classic-portal], debería ver dos entradas nuevas:

   * *ClusterName*\_Clúster
   * *ClusterName*\_Cliente

   El script imprimirá el código JSON que necesita la plantilla de Azure Resource Manager al crear el clúster en la sección siguiente, por lo que es buena idea mantener abierta la ventana de PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Creación de una plantilla de Resource Manager para el clúster de Service Fabric
En esta sección, se usarán las salidas de los comandos de PowerShell anteriores en una plantilla de Resource Manager para el clúster de Service Fabric.

Las plantillas de ejemplo de Resource Manager están disponibles en la [Galería de plantillas de inicio de rápido de Azure en GitHub][azure-quickstart-templates]. Estas plantillas se pueden usar como punto de partida para crear la plantilla de clúster.

### <a name="create-the-resource-manager-template"></a>Creación de la plantilla de Resource Manager
En esta guía se usa la plantilla de ejemplo de [clúster seguro de&5; nodos][service-fabric-secure-cluster-5-node-1-nodetype] y los parámetros de plantilla. Descargue `azuredeploy.json` y `azuredeploy.parameters.json` en su equipo y abra ambos archivos en el editor de texto de su elección.

### <a name="add-certificates"></a>Adición de certificados
Para agregar certificados a una plantilla de Resource Manager para el clúster, haga referencia al almacén de claves que contiene las claves de certificado. Se recomienda colocar los valores del almacén de claves en un archivo de parámetros de plantilla de Resource Manager. De este modo se puede reutilizar el archivo de plantilla de Resource Manager y se mantiene libre de valores específicos a una implementación.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Incorporación de todos los certificados a osProfile del conjunto de escalado de máquinas virtuales
Todos los certificados instalados en el clúster deben estar configurados en la sección osProfile del recurso del conjunto de escalado (Microsoft.Compute/virtualMachineScaleSets). Con ello se indica al proveedor de recursos que instale el certificado en las máquinas virtuales. Esta instalación incluye tanto el certificado del clúster como los certificados de seguridad de la aplicación que planee usar para las aplicaciones:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Configuración del certificado de clúster de Service Fabric
El certificado de autenticación del clúster debe estar configurado tanto en el recurso del clúster de Service Fabric (Microsoft.ServiceFabric/clusters) como en la extensión de Service Fabric para conjuntos de escalado de máquinas virtuales en el recurso de conjuntos de escalado de máquinas virtuales. Esta disposición permite al proveedor de recursos de Service Fabric configurarlo para su uso en la autenticación del clúster y la autenticación del servidor en los puntos de conexión de administración.

##### <a name="virtual-machine-scale-set-resource"></a>Recurso de conjunto de escalado de máquinas virtuales:
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Recurso de Service Fabric:
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Inserción de la configuración de Azure AD
La configuración de Azure AD que creó anteriormente se puede insertar directamente en la plantilla de Resource Manager. Sin embargo, se recomienda que primero extraiga los valores a un archivo de parámetros para poder reutilizar la plantilla de Resource Manager, sin necesidad de valores específicos de una implementación.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="a-configure-arm-aconfigure-resource-manager-template-parameters"></a><a "configure-arm" ></a>Configuración de los parámetros de plantilla de Resource Manager
Por último, utilice los valores de salida del almacén de claves y los comandos de PowerShell de Azure AD para rellenar el archivo de parámetros:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
En este punto, debería tener los siguientes elementos en su lugar:

* Grupo de recursos del almacén de claves
  * Almacén de claves
  * Certificado de autenticación de servidor de clúster
  * Certificado de cifrado de datos
* Inquilino de Azure Active Directory
  * Aplicación de Azure AD para la administración basada en web y Service Fabric Explorer
  * Aplicación de Azure AD para la administración de cliente nativo
  * Usuarios y roles asignados
* Plantilla de Resource Manager para el clúster de Service Fabric
  * Certificados configurados mediante el almacén de claves
  * Azure Active Directory configurado

En el diagrama siguiente se ilustra el lugar que ocupa el almacén de claves y la configuración de Azure AD en la plantilla de Resource Manager.

![Asignación de dependencias de Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Creación de clústeres
Ahora está listo para crear el clúster con la [implementación de la plantilla de recursos de Azure][resource-group-template-deploy].

#### <a name="test-it"></a>Pruébelo.
Utilice el siguiente comando de PowerShell para probar la plantilla de Resource Manager con un archivo de parámetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Impleméntelo.
Si la prueba de la plantilla de Resource Manager es satisfactoria, use el siguiente comando de PowerShell para implementar dicha plantilla con un archivo de parámetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Asignación de usuarios a roles
Una vez que haya creado las aplicaciones para representar el clúster, debe asignar los usuarios a los roles compatibles con Service Fabric: solo lectura y administrador. Puede asignar los roles mediante el [Portal de Azure clásico][azure-classic-portal].

1. En Azure Portal, vaya a su inquilino y seleccione **Aplicaciones**.
2. Elija la aplicación web, cuyo nombre se parece a `myTestCluster_Cluster`.
3. Haga clic en la pestaña **Usuarios** .
4. Seleccione un usuario para asignar y haga clic en el botón **Asignar** situado en la parte inferior de la pantalla.

    ![Botón para asignar usuarios a roles][assign-users-to-roles-button]
5. Seleccione el rol que quiere asignar al usuario.

    ![Cuadro de diálogo "Asignar usuarios"][assign-users-to-roles-dialog]

> [!NOTE]
> Para más información sobre los roles de Service Fabric, consulte [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md).
>
>

 <a name="secure-linux-cluster"></a>

## <a name="create-secure-clusters-on-linux"></a>Creación de clústeres seguros en Linux
Para facilitar el proceso, se ha proporcionado un [script auxiliar](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Antes de usar este script auxiliar, asegúrese de que ya tiene la interfaz de la línea de comandos (CLI) de Azure instalada y se encuentra en la ruta de acceso. Asegúrese de que el script tiene permisos de ejecución ejecutando `chmod +x cert_helper.py` después de descargarlo. El primer paso es iniciar sesión en su cuenta de Azure mediante la CLI con el comando `azure login`. Después de iniciar sesión en su cuenta de Azure, use el script auxiliar con su certificado firmado por la entidad de certificación, como se muestra en el siguiente comando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

El parámetro -ifile puede tomar un archivo .pfx o un .pem como entrada, con el tipo de certificado (pfx o pem, o ss si es un certificado autofirmado).
El parámetro -h imprime el texto de ayuda.


Este comando devuelve las siguientes tres cadenas como salida:

* SourceVaultID, el identificador para el nuevo KeyVault ResourceGroup que creó para usted
* CertificateUrl para acceder al certificado
* CertificateThumbprint, que se utiliza para la autenticación

En el ejemplo siguiente se muestra cómo utilizar el comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
La ejecución del comando anterior le proporciona las tres cadenas como sigue:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

El nombre de sujeto del certificado debe coincidir con el dominio usado para acceder al clúster de Service Fabric. Esta coincidencia es necesaria para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio `.cloudapp.azure.com`. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

Estos nombres de sujeto son las entradas necesarias para crear un clúster seguro de Service Fabric (sin Azure AD) tal y como se describe en [Configuración de los parámetros de plantilla de Resource Manager](#configure-arm). Puede conectarse al clúster seguro mediante las instrucciones de [autenticación del acceso de cliente a un clúster](service-fabric-connect-to-secure-cluster.md). Los clústeres de versión preliminar de Linux no admiten la autenticación de Azure AD. Puede asignar roles de administrador y cliente como se indica en la sección [Asignación de usuarios a roles](#assign-roles). Al especificar los roles de administrador y cliente para un clúster de versión preliminar de Linux, tendrá que proporcionar huellas digitales de certificado para la autenticación. (No proporciona el nombre del sujeto, ya que no se realiza ninguna validación o revocación de la cadena en esta versión preliminar).

Si desea usar un certificado autofirmado para pruebas, puede usar el mismo script para generar uno. A continuación, puede cargar el certificado en el almacén de claves al proporcionar la marca `ss` en lugar de proporcionar la ruta de acceso y el nombre del certificado. Por ejemplo, observe el siguiente comando para crear y cargar un certificado autofirmado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Este comando devuelve las mismas tres cadenas: SourceVault, CertificateUrl y CertificateThumbprint. A continuación, puede usar las cadenas para crear un clúster de Linux seguro y una ubicación donde colocar el certificado autofirmado. Necesita el certificado autofirmado para conectarse al clúster. Puede conectarse al clúster seguro mediante las instrucciones de [autenticación del acceso de cliente a un clúster](service-fabric-connect-to-secure-cluster.md).

El nombre de sujeto del certificado debe coincidir con el dominio usado para acceder al clúster de Service Fabric. Esta coincidencia es necesaria para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio `.cloudapp.azure.com`. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

Puede rellenar los parámetros del script auxiliar en Azure Portal, como se describe en la sección [Creación de un clúster en Azure Portal](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal).

## <a name="next-steps"></a>Pasos siguientes
En este punto, tiene un clúster seguro con autenticación de Azure Active Directory que proporciona autenticación de administración. Después, [conéctese al clúster](service-fabric-connect-to-secure-cluster.md) y aprenda a [administrar secretos de aplicación](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Solución de problemas de configuración de Azure Active Directory para la autenticación de cliente
Si experimenta algún problema al configurar Azure AD para la autenticación de cliente, revise las posibles soluciones de esta sección.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer le solicita que seleccione el certificado
#### <a name="problem"></a>Problema
Después de conectarse correctamente a Azure AD en Service Fabric Explorer, el explorador vuelve a la página principal, pero un mensaje le pide que seleccione un certificado.

![Cuadro de diálogo de selección de certificado de SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
Al usuario no se le ha asignado un rol en la aplicación de clúster de Azure AD. Por tanto, se produce un error en la autenticación de Azure AD en el clúster de Service Fabric. Service Fabric Explorer recurre a la autenticación de certificado.

#### <a name="solution"></a>Solución
Siga las instrucciones de configuración de Azure AD y de asignación de roles de usuario. Además, se recomienda activar "Asignación de usuario necesaria para acceder a la aplicación", como hace `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Error de conexión con PowerShell: "Las credenciales especificadas no son válidas"
#### <a name="problem"></a>Problema
Cuando usa PowerShell para conectarse al clúster mediante el modo de seguridad "AzureActiveDirectory", después de iniciar sesión correctamente en Azure AD, se produce un error de conexión con el mensaje "Las credenciales especificadas no son válidas".

#### <a name="solution"></a>Solución
Esta solución es la mismo que la anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer devuelve un error al iniciar sesión: "AADSTS50011"
#### <a name="problem"></a>Problema
Al intentar iniciar sesión en Azure AD en Service Fabric Explorer, la página devuelve el error: "AADSTS50011: The reply address &lt;url&gt; does not match the reply addresses configured for the application: &lt;guid&gt;" (AADSTS50011: La dirección <url> de respuesta no coincide con las direcciones de respuesta configuradas para la aplicación: <guid>).

![La dirección de respuesta SFX no coincide][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
La aplicación del clúster (web) que representa Service Fabric Explorer intenta la autenticación en Azure AD y, como parte de la solicitud, proporciona la dirección URL de retorno de redireccionamiento. Pero no aparece en la lista **URL DE RESPUESTA** de la aplicación Azure AD.

#### <a name="solution"></a>Solución
En la pestaña **Configurar** de la aplicación del clúster (web), agregue la dirección URL de Service Fabric Explorer a la lista **URL DE RESPUESTA** o reemplace uno de los elementos de la lista. Cuando haya terminado, guarde los cambios.

![Dirección URL de respuesta de la aplicación web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conexión del clúster mediante la autenticación de Azure AD a través de PowerShell
Para conectar el clúster de Service Fabric, use el siguiente ejemplo de comando de PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para más información sobre el cmdlet Connect-ServiceFabricCluster, consulte [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>¿Se puede reutilizar el mismo inquilino de Azure AD para varios clústeres?
Sí. Pero recuerde agregar la dirección URL de Service Fabric Explorer a la aplicación del clúster (web). De lo contrario, Service Fabric Explorer no funciona.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>¿Por qué todavía necesito el certificado de servidor con Azure AD habilitado?
FabricClient y FabricGateway realizan una autenticación mutua. Durante la autenticación de Azure AD, la integración de Azure AD proporciona la identidad del cliente al servidor y el certificado de servidor se utiliza para comprobar la identidad del servidor. Para más información sobre cómo funciona el certificado en Service Fabric, consulte [Certificados X.509 y Service Fabric][x509-certificates-and-service-fabric].

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

