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
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: e5dd1ebd290c950c7f2bda3dae088f3ee7f836fd
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Creación de un clúster de Service Fabric con Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md)
> * [portal de Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Esta guía paso a paso le lleva por la configuración de un clúster seguro de Azure Service Fabric en Azure con Azure Resource Manager. Sabemos que el artículo es largo. No obstante, a menos que ya esté familiarizado con el contenido, asegúrese de seguir cada paso con cuidado.

La guía abarca los siguientes procedimientos:

* Conceptos principales que es necesario tener en cuenta antes de implementar un clúster de Service Fabric.
* Creación de un clúster en Azure mediante los módulos de Resource Manager de Service Fabric.
* Configuración de Azure Active Directory (Azure AD) para la autenticación de los usuarios que realizan operaciones de administración en el clúster.
* Creación de una plantilla personalizada de Azure Resource Manager para el clúster y su implementación.

## <a name="key-concepts-to-be-aware-of"></a>Conceptos principales que se deben tener en cuenta
En Azure, Service Fabric exige el uso de un certificado x509 para proteger el clúster y sus puntos de conexión. Los certificados se usan en Service Fabric para proporcionar autenticación y cifrado con el fin de proteger diversos aspectos de un clúster y sus aplicaciones. Para el acceso de cliente y la realización de operaciones de administración en el clúster, lo que incluye la implementación, la actualización y la eliminación de aplicaciones, servicios y los datos que contienen, puede usar certificados o credenciales de Azure Active Directory. El uso de Azure Active Directory es muy recomendable, ya que es la única manera de evitar compartir certificados en los clientes.  Para obtener más información sobre el modo en que se usan los certificados en Service Fabric, consulte los [Escenarios de seguridad de los clústeres de Service Fabric][service-fabric-cluster-security].

Service Fabric usa certificados X.509 para proteger un clúster y proporcionar características de seguridad de las aplicaciones. [Key Vault][key-vault-get-started] se usa para administrar certificados para clústeres de Service Fabric en Azure. 


### <a name="cluster-and-server-certificate-required"></a>Certificado de clúster y servidor (obligatorio)
Estos certificados (uno principal y, opcionalmente, uno secundario) son necesarios para proteger un clúster e impedir que se acceda a él sin autorización. Ofrece seguridad al clúster de dos maneras:

* **Autenticación del clúster:** se autentica la comunicación de nodo a nodo para la federación del clúster. Solo los nodos que pueden probar su identidad con este certificado pueden unirse al clúster.
* **Autenticación del servidor:** los puntos de conexión de administración del clúster se autentican en un cliente de administración, de forma que este sabe que está hablando con el clúster real y no con un intermediario. Este certificado proporciona también SSL para la API de administración de HTTPS y para Service Fabric Explorer sobre HTTPS.

Para servir a estos propósitos, el certificado debe cumplir los siguientes requisitos:

* El certificado debe contener una clave privada. Estos certificados suelen tener normalmente las extensiones .pfx o .pem  
* El certificado debe crearse para el intercambio de claves, que se pueda exportar a un archivo Personal Information Exchange (.pfx).
* El **nombre de sujeto del certificado debe coincidir con el dominio usado para acceder al clúster de Service Fabric**. Esta coincidencia es necesaria para proporcionar un certificado SSL al punto de conexión de administración HTTPS y a la utilidad Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio *.cloudapp.azure.com. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Configuración de Azure Active Directory para la autenticación de cliente (opcional, pero recomendado)

Azure AD permite a las organizaciones (conocidas como inquilinos) administrar el acceso de los usuarios a las aplicaciones. Las aplicaciones se dividen en las que tienen interfaz de usuario de inicio de sesión basada en web y las que tienen una experiencia de cliente nativa. En este artículo se supone que ya ha creado un inquilino. En caso de que no lo haya hecho, lea [Obtención de un inquilino de Azure Active Directory][active-directory-howto-tenant].

Un clúster de Service Fabric ofrece diversos puntos de entrada a su funcionalidad de administración, como [Service Fabric Explorer][service-fabric-visualizing-your-cluster] y [Visual Studio][service-fabric-manage-application-in-visual-studio] basados en web. Como resultado, creará dos aplicaciones de Azure AD para controlar el acceso al clúster, una aplicación web y otra nativa.

Más información sobre cómo configurarlas más adelante en el documento.

### <a name="application-certificates-optional"></a>Certificados de aplicación (opcionales)
Se puede instalar un número cualquiera de certificados adicionales en un clúster para proteger la aplicación. Antes de crear el clúster, tenga en cuenta los escenarios de seguridad de las aplicaciones que requieren que se instale un certificado en los nodos, por ejemplo:

* Cifrado y descifrado de los valores de configuración de aplicación.
* Cifrado de datos entre nodos durante la replicación.

El concepto de creación de clústeres seguros es el mismo, tanto si son de Linux como de Windows. 

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticación de cliente (opcional)
Se puede especificar cualquier número de certificados adicionales para las operaciones de cliente administrador o usuario. De forma predeterminada, el certificado de clúster tiene privilegios de cliente administrador. Estos certificados de cliente adicionales no se deben instalar en el clúster, solo es necesario especificar que se permiten en la configuración del clúster; no obstante, es necesario instalarlos en las máquinas cliente para conectarse al clúster y realizar las operaciones de administración.


## <a name="prerequisites"></a>Requisitos previos 
El concepto de creación de clústeres seguros es el mismo, tanto si son de Linux como de Windows. En esta guía se explica el uso de Azure PowerShell o la CLI de Azure para crear nuevos clústeres. Los requisitos previos son: 

-  [Azure PowerShell 4.1 y versiones posteriores][azure-powershell] o la [CLI de Azure 2.0 y versiones posteriores][azure-CLI].
-  En estos vínculos puede encontrar detalles sobre los módulos de Service Fabric, [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) y sobre el módulo de la CLI, [az sf](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest).


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Uso del módulo RM de Service Fabric para implementar un clúster

En este documento, se usará el módulo de PowerShell y de la CLI de RM de Service Fabric para implementar un clúster. El comando del módulo de PowerShell o de la CLI permite varios escenarios. Vamos a examinar cada uno de ellos. Seleccione aquel escenario que piense que satisface mejor sus necesidades. 

- Creación de un nuevo clúster, mediante un certificado autofirmado generado por el sistema
    - Uso de una plantilla de clúster predeterminada
    - Uso de una plantilla ya existente
- Creación de un nuevo clúster, mediante un certificado ya existente
    - Uso de una plantilla de clúster predeterminada
    - Uso de una plantilla ya existente

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Creación de un nuevo clúster, mediante un certificado autofirmado generado por el sistema

Si quiere que el sistema genere un certificado autofirmado y lo use para proteger el clúster, use el siguiente comando para crear el clúster. Este comando configura un certificado de clúster principal que se usa para la seguridad del clúster y para configurar el acceso de administrador para realizar operaciones de administración con ese certificado.

### <a name="login-in-to-azure"></a>Inicio de sesión en Azure

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Uso de la plantilla predeterminada 5 Node 1 nodetype que se incluye con el módulo para configurar el clúster

Use el siguiente comando para crear un clúster rápidamente, con solo los parámetros mínimos.

La plantilla que se usa está disponible en las [plantillas de ejemplo de Azure Service Fabric: plantilla de Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) y [plantilla de Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure).

Los comandos siguientes sirven para la creación de clústeres de Windows y Linux, solo es necesario especificar el sistema operativo correspondiente. Los comandos de PowerShell y de la CLI también generan el certificado en la carpeta de salida de certificados especificada. El comando toma también otros parámetros, como la SKU de máquina virtual.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!1"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Uso de la plantilla personalizada ya existente 

Si tiene que crear una plantilla personalizada para adaptarla a sus necesidades, es muy aconsejable comenzar con una de las ya disponibles en las [plantillas de ejemplo de Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Siga las instrucciones y explicaciones para [personalizar la plantilla del clúster][customize-your-cluster-template] en la sección que viene a continuación.

Si ya tiene una plantilla personalizada, asegúrese de comprobar doblemente que los tres parámetros relacionados con el certificado de la plantilla y el archivo de parámetros tengan los nombres siguientes y que los valores sean null como se indica a continuación.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Este es el comando equivalente de la CLI para hacer lo mismo. Cambie los valores de las instrucciones declare por los valores adecuados. La CLI admite todos los demás parámetros que admiten los comandos de PowerShell anteriores.

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Creación de un nuevo clúster, con el certificado que compró a una entidad de certificación o con uno ya existente

Si tiene un certificado que quiere usar para proteger el clúster, use el siguiente comando para crear el clúster.

Si es un certificado firmado por una entidad de certificación que terminará usándose también para otros fines, se recomienda proporcionar un grupo de recursos distinto específicamente para su almacén de claves. Se recomienda colocar el almacén de claves en su propio grupo de recursos. Esto le permite quitar los grupos de recursos de proceso y almacenamiento, incluido el grupo de recursos que tiene el clúster de Service Fabric, sin perder las claves y los secretos. **El grupo de recursos que contiene el almacén de claves_debe estar en la misma región_ que el clúster que lo usa.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Uso de la plantilla predeterminada 5 Node 1 nodetype que se incluye con el módulo
La plantilla que se usa está disponible en las [plantillas de Azure: plantilla de Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) y [plantilla de Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure).

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!1" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser

```

#### <a name="use-the-custom-template-that-you-have"></a>Uso de la plantilla personalizada ya existente 
Si tiene que crear una plantilla personalizada para adaptarla a sus necesidades, es muy aconsejable comenzar con una de las ya disponibles en las [plantillas de ejemplo de Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Siga las instrucciones y explicaciones para [personalizar la plantilla del clúster][customize-your-cluster-template] en la sección que viene a continuación.

Si ya tiene una plantilla personalizada, asegúrese de comprobar doblemente que los tres parámetros relacionados con el certificado de la plantilla y el archivo de parámetros tengan los nombres siguientes y que los valores sean null como se indica a continuación.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword #certPassword

```

Este es el comando equivalente de la CLI para hacer lo mismo. Cambie los valores de las instrucciones declare por los valores adecuados.

```CLI

declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-keyvault"></a>Uso de un puntero al secreto que ya se ha cargado en el almacén de claves

Para usar un almacén de claves existente, _debe estar habilitado para la implementación_, de forma que el proveedor de recursos de proceso pueda recibir de él certificados e instalarlos en nodos de clúster:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretID -TemplateFile $templateFile -ParameterFile $templateParmfile 

```
Este es el comando equivalente de la CLI para hacer lo mismo. Cambie los valores de las instrucciones declare por los valores adecuados.

```cli

declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifieraz $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

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

Se le pedirá que inicie sesión en una cuenta con privilegios administrativos para el inquilino de Azure AD. Una vez iniciada la sesión, el script creará las aplicaciones web y nativa para representar el clúster de Service Fabric. Si examina las aplicaciones del inquilino en [Azure Portal][azure-portal], debería ver dos entradas nuevas:

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

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Creación de una plantilla de Resource Manager para el clúster de Service Fabric
Esta sección es para los usuarios que quieren crear una plantilla personalizada de Resource Manager para un clúster de Service Fabric. Una vez que tenga una plantilla, puede volver atrás y usar lo módulos de PowerShell o de la CLI para implementarla. 

Las plantillas de ejemplo de Resource Manager están disponibles en los [ejemplos de Azure de GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Estas plantillas se pueden usar como punto de partida para crear la plantilla de clúster.

### <a name="create-the-resource-manager-template"></a>Creación de la plantilla de Resource Manager
En esta guía se usa la plantilla de ejemplo de [clúster seguro de 5 nodos][service-fabric-secure-cluster-5-node-1-nodetype] y los parámetros de plantilla. Descargue `azuredeploy.json` y `azuredeploy.parameters.json` en su equipo y abra ambos archivos en el editor de texto de su elección.

### <a name="add-certificates"></a>Adición de certificados
Para agregar certificados a una plantilla de Resource Manager para el clúster, haga referencia al almacén de claves que contiene las claves de certificado. Agregue esos parámetros y valores de almacén de claves en un archivo de parámetros de plantilla de Resource Manager (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Incorporación de todos los certificados a osProfile del conjunto de escalado de máquinas virtuales
Todos los certificados instalados en el clúster deben estar configurados en la sección osProfile del recurso del conjunto de escalado (Microsoft.Compute/virtualMachineScaleSets). Con ello se indica al proveedor de recursos que instale el certificado en las máquinas virtuales. Esta instalación incluye tanto el certificado del clúster como los certificados de seguridad de la aplicación que planee usar para las aplicaciones:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
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

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Agregue la información de certificado al recurso del conjunto de escalado de máquinas virtuales:
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
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

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Agregue la información de certificado al recurso de clúster de Service Fabric:
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
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

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adición de la configuración de Azure AD para usar Azure AD para el acceso de cliente

Para agregar la configuración de Azure AD a una plantilla de Resource Manager del clúster, haga referencia al almacén de claves que contiene las claves de certificado. Agregue esos parámetros y valores de Azure AD en un archivo de parámetros de plantilla de Resource Manager (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
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

### <a name="populate-the-parameter-file-with-the-values"></a>Rellene el archivo de parámetros con los valores.
Por último, use los valores de salida del almacén de claves y los comandos de PowerShell de Azure AD para rellenar el archivo de parámetros:

Si tiene previsto usar los módulos de PowerShell de RM de Service Fabric, no es necesario rellenar la información de certificado del clúster; si quiere que el sistema genere el certificado autofirmado para la seguridad del clúster, basta con mantener sus valores nulos. 

> [!NOTE]
> Para que los módulos de RM seleccionen y rellenen estos valores de parámetro vacíos, los nombres de parámetros deben coincidir en gran medida con los nombres siguientes.
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Si va a usar certificados de aplicación o usa un clúster existente que ha cargado en el almacén de claves, debe obtener esta información y rellenarla. 

Los módulos de RM no tiene la capacidad de generar automáticamente la configuración de Azure AD. Por ello, si tiene previsto usar Azure AD para el acceso de cliente, debe rellenarla.

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

### <a name="test-your-template"></a>Prueba de la plantilla  
Use el siguiente comando de PowerShell para probar la plantilla de Resource Manager con un archivo de parámetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Si se encuentra con problemas y recibe mensajes cifrados, use "-Depurar" como opción.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

En el diagrama siguiente se ilustra el lugar que ocupa el almacén de claves y la configuración de Azure AD en la plantilla de Resource Manager.

![Asignación de dependencias de Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Creación del clúster mediante la plantilla de recursos de Azure 

Ahora puede implementar el clúster siguiendo los pasos descritos anteriormente en el documento, o, si tiene rellenados los valores del archivo de parámetros, ahora está listo para crear el clúster mediante la [implementación directa de la plantilla de recursos de Azure][resource-group-template-deploy].

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Si se encuentra con problemas y recibe mensajes cifrados, use "-Depurar" como opción.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Asignación de usuarios a roles
Una vez que haya creado las aplicaciones para representar el clúster, debe asignar los usuarios a los roles compatibles con Service Fabric: solo lectura y administrador. Puede asignar los roles mediante [Azure Portal][azure-portal].

1. En Azure Portal, seleccione el inquilino en la esquina superior derecha.

    ![Botón Seleccionar inquilino][select-tenant-button]
2. Seleccione **Azure Active Directory** en la pestaña de la izquierda y, a continuación, seleccione "Aplicaciones empresariales".
3. Seleccione "Todas las aplicaciones" y, a continuación, busque y seleccione la aplicación web que se llama `myTestCluster_Cluster`.
4. Haga clic en la pestaña **Usuarios y grupos**.

    ![Pestaña Usuarios y grupos][users-and-groups-tab]
5. Haga clic en el botón **Agregar usuario** situado en la página nueva, seleccione un usuario y el rol que desea asignar y, a continuación, haga clic en el botón **Seleccionar** situado en la parte inferior de la página.

    ![Página Asignar usuarios a roles][assign-users-to-roles-page]
6. Haga clic en el botón **Asignar**, situado en la parte inferior de la página.

    ![Confirmación de Agregar asignación][assign-users-to-roles-confirm]

> [!NOTE]
> Para más información sobre los roles de Service Fabric, consulte [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Ayuda de solución de problemas para configurar Azure Active Directory
Configurar y usar Azure AD puede resultar complicado, así que a continuación se proporcionan algunas indicaciones de lo que puede hacer para depurar el problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer le solicita que seleccione el certificado
#### <a name="problem"></a>Problema
Después de conectarse correctamente a Azure AD en Service Fabric Explorer, el explorador vuelve a la página principal, pero un mensaje le pide que seleccione un certificado.

![Cuadro de diálogo de certificado SFX][sfx-select-certificate-dialog]

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
Seleccione "Registros de aplicaciones" en la página de AAD, seleccione la aplicación de clúster y, a continuación, seleccione el botón **URL de respuesta**. En la página "URL de respuesta", agregue la dirección URL de Service Fabric Explorer a la lista o reemplace algunos de los elementos de esta. Cuando haya terminado, guarde los cambios.

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

## <a name="next-steps"></a>pasos siguientes
En este punto, tiene un clúster seguro con autenticación de Azure Active Directory que proporciona autenticación de administración. Después, [conéctese al clúster](service-fabric-connect-to-secure-cluster.md) y aprenda a [administrar secretos de aplicación](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

