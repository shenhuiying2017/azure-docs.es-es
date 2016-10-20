
<properties
   pageTitle="Creación de un clúster seguro de Service Fabric mediante Azure Resource Manager | Microsoft Azure"
   description="En este artículo se describe cómo configurar un clúster seguro de Service Fabric en Azure mediante Azure Resource Manager, el Almacén de claves de Azure y Azure Active Directory (AAD) para la autenticación de clientes."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# Creación de un clúster de Service Fabric en Azure mediante Azure Resource Manager

> [AZURE.SELECTOR]
- [Administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md)
- [Portal de Azure](service-fabric-cluster-creation-via-portal.md)

Esta guía paso a paso le lleva por la configuración de un clúster de Service Fabric de Azure seguro en Azure mediante Azure Resource Manager. Estos pasos son los siguientes:

 - Configurar el Almacén de claves para administrar las claves de seguridad del clúster y de las aplicaciones.
 - Crear un clúster protegido en Azure con Azure Resource Manager.
 - Autenticar a los usuarios con Azure Active Directory (AAD) para la administración del clúster.

Un clúster seguro es un clúster que impide el acceso no autorizado a operaciones de administración, lo que incluye la implementación, actualización y eliminación de aplicaciones y servicios y los datos que contienen. Un clúster no seguro es un clúster al que cualquiera puede conectarse en cualquier momento y realizar operaciones de administración. Aunque se puede crear un clúster no seguro, se **recomienda firmemente crear uno seguro**. Un clúster no seguro **no se puede proteger en un momento posterior** -se debe crear uno nuevo.

Los conceptos son los mismos para crear clústeres seguros, tanto si se trata de clústeres de Linux como de Windows. Para más información y scripts auxiliares para crear clústeres seguros de Linux, consulte [Creación de clústeres seguros en Linux](#secure-linux-clusters)

## Inicie sesión en Azure.
En esta guía se usa [Azure PowerShell][azure-powershell]. Al iniciar una nueva sesión de PowerShell, inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.

Inicie sesión en su cuenta de Azure:

```powershell
Login-AzureRmAccount
```

Seleccione su suscripción:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## Configuración del Almacén de claves

Esta sección le lleva por la creación de un servicio Key Vault para un clúster de Service Fabric en Azure y para aplicaciones de Service Fabric. Para obtener una guía completa sobre el Almacén de claves, consulte [Introducción al Almacén de claves de Azure][key-vault-get-started].

Service Fabric usa certificados X.509 para proteger un clúster y proporcionar características de seguridad de las aplicaciones. El Almacén de claves de Azure se usa para administrar certificados para clústeres de Service Fabric en Azure. Cuando un clúster se implementa en Azure, el proveedor de recursos de Azure responsable de crear clústeres de Service Fabric extrae los certificados del Almacén de claves y los instala en las máquinas virtuales del clúster.

En el diagrama siguiente se ilustra la relación entre el Almacén de claves, un clúster de Service Fabric y el proveedor de recursos de Azure que usa los certificados almacenados en el Almacén de claves cuando se crea el clúster:

![Instalación del certificado][cluster-security-cert-installation]

### Creación de un grupo de recursos

El primer paso es crear un grupo de recursos específicamente para el servicio Key Vault. Se recomienda colocar el servicio Key Vault en su propio grupo de recursos. Esto le permite quitar los grupos de recursos de proceso y almacenamiento, incluido el grupo de recursos que tiene el clúster de Service Fabric sin perder sus claves y secretos. El grupo de recursos que tiene el Almacén de claves debe estar en la misma región que el clúster que lo usa.

```powershell

	New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
	WARNING: The output object type of this cmdlet is going to be modified in a future release.
	
	ResourceGroupName : mycluster-keyvault
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### Create Key Vault 

Cree un Almacén de claves en el nuevo grupo de recursos. El Almacén de claves **debe estar habilitado para implementación** de forma que el proveedor de recursos de Service Fabric pueda recibir de él certificados e instalarlos en nodos del clúster:

```powershell

	New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
	
	
	Vault Name                       : myvault
	Resource Group Name              : mycluster-keyvault
	Location                         : West US
	Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
	Vault URI                        : https://myvault.vault.azure.net
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

Si tiene un Almacén de claves existente, puede habilitarlo para implementación mediante la CLI de Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## Adición de certificados al Almacén de claves

Los certificados se usan en Service Fabric para proporcionar autenticación y cifrado con el fin de proteger diversos aspectos de un clúster y sus aplicaciones. Para más información sobre el modo en que se usan los certificados en Service Fabric, consulte los [Escenarios de seguridad de los clústeres de Service Fabric][service-fabric-cluster-security].

### Certificado de clúster y servidor (obligatorio) 

Este certificado es necesario para proteger un clúster e impedir el acceso no autorizado. La seguridad adopta dos formas:
 
 - **Autenticación del clúster:** se autentica la comunicación de nodo a nodo para la federación del clúster. Solo los nodos que pueden probar su identidad con este certificado pueden unirse al clúster.
 - **Autenticación del servidor:** los puntos de conexión de administración del clúster se autentican en un cliente de administración, de forma que este sabe que está hablando con el clúster real. Este certificado proporciona también Capa de sockets seguros (SSL) para la API de administración de HTTPS y para Service Fabric Explorer sobre HTTPS.

Para servir a estos propósitos, el certificado debe cumplir los siguientes requisitos:

 - El certificado debe contener una clave privada.
 - El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
 - El nombre de sujeto del certificado debe coincidir con el nombre del dominio usado para acceder al clúster de Service Fabric. Esta coincidencia es un requisito para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio `.cloudapp.azure.com`. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una entidad de certificación, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado que se usó para el clúster.

### Certificados de aplicación (opcionales)

Se puede instalar un número cualquiera de certificados adicionales en un clúster para proteger la aplicación. Antes de crear el clúster, tenga en cuenta los escenarios de seguridad de las aplicaciones que requieren que se instale un certificado en los nodos, por ejemplo:

 - Cifrado y descifrado de los valores de configuración de aplicación
 - Cifrado de datos entre nodos durante la replicación

### Formato de certificados para el uso del proveedor de recursos de Azure

Los archivos de clave privada (.pfx) se pueden agregar y usar directamente mediante el Almacén de claves. Sin embargo, el proveedor de recursos de Azure requiere que las claves se almacenen en un formato JSON especial que incluye el .pfx como una cadena codificada con base-64 y la contraseña de clave privada. Para hacer frente a estos requisitos, las claves deben estar colocadas en una cadena JSON y luego almacenarse como *secretos* en el Almacén de claves.

Para facilitar este proceso, hay un módulo de PowerShell [disponible en GitHub][service-fabric-rp-helpers]. Siga estos pasos para utilizar el módulo:

  1. Descargue todo el contenido del repositorio en un directorio local.
  2. Importe el módulo en la ventana de PowerShell:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
El comando `Invoke-AddCertToKeyVault` de este módulo de PowerShell convierte automáticamente una clave privada de certificado en una cadena JSON y la carga en el Almacén de claves. Úsela para agregar el certificado de clúster y cualquier certificado de aplicación adicional al Almacén de claves. Repita este paso con cualquier certificado adicional que quiera instalar en el clúster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
	
	Switching context to SubscriptionId <guid>
	Ensuring ResourceGroup mycluster-keyvault in West US
	WARNING: The output object type of this cmdlet is going to be modified in a future release.
	Using existing valut myvault in West US
	Reading pfx file from C:\path\to\key.pfx
	Writing secret to myvault in vault myvault
	
	
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Las cadenas anteriores son todos los requisitos previos del servicio Key Vault para configurar una plantilla de Resource Manager para el clúster de Service Fabric que instala certificados para la autenticación de nodos, la seguridad de los puntos de conexión de administración y la autenticación, y cualquier característica de seguridad de aplicación adicional que use certificados X.509. En este punto, debería tener ahora la siguiente configuración en Azure:

 - Grupo de recursos del Almacén de claves
   - Almacén de claves
     - Certificado de autenticación de servidor de clúster
     - Certificados de aplicación

## Configuración de Azure Active Directory para la autenticación de cliente

AAD permite que las organizaciones (conocidas como inquilinos) administren el acceso de los usuarios a las aplicaciones, que se dividen en aplicaciones con interfaz de usuario de inicio de sesión basada en web y aplicaciones con experiencia de cliente nativa. En este documento, se supone que ya ha creado un inquilino. En caso de que no lo haya hecho, lea [Obtención de un inquilino de Azure Active Directory][active-directory-howto-tenant].

Un clúster de Service Fabric ofrece diversos puntos de entrada a su funcionalidad de administración, como [Service Fabric Explorer][service-fabric-visualizing-your-cluster] y [Visual Studio][service-fabric-manage-application-in-visual-studio] basados en web. Como resultado, creará dos aplicaciones de AAD para controlar el acceso al clúster, una aplicación web y una aplicación nativa.

Para simplificar algunos de los pasos necesarios para configurar AAD con un clúster de Service Fabric, hemos creado un conjunto de scripts de Windows PowerShell.

>[AZURE.NOTE] Debe realizar estos pasos *antes* de crear el clúster. Por eso, en los casos en los que los scripts esperan nombres de clúster y puntos de conexión, debe indicar los valores planeados, no los valores que ya haya creado.

1. [Descargue los scripts][sf-aad-ps-script-download] en su equipo.

2. Haga clic con el botón derecho en el archivo zip, elija **Propiedades** y luego active la casilla **Desbloquear**.

3. Extraiga el archivo ZIP.

4. Ejecute `SetupApplications.ps1` y proporcione TenantId, ClusterName y WebApplicationReplyUrl como parámetros. Por ejemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Encontrará el valor de **TenantId** si examina la dirección URL del inquilino en el Portal de Azure clásico. El GUID insertado en esa dirección URL es el TenantId. Por ejemplo:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    El valor de **ClusterName** se usará como prefijo para las aplicaciones de AAD creadas por el script. No es necesario que coincida exactamente con el nombre real del clúster, ya que su finalidad es facilitar la asignación de artefactos de AAD al clúster de Service Fabric con el que se usan.

    El valor de **WebApplicationReplyUrl** es el punto de conexión predeterminado al que AAD devolverá a los usuarios después de completar el proceso de inicio de sesión. Debe establecerlo en el punto de conexión de Service Fabric Explorer para el clúster, que de manera predeterminada es el siguiente:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Se le pedirá que inicie sesión en una cuenta que tenga privilegios administrativos para el inquilino de AAD. En cuanto lo haga, el script creará la web y las aplicaciones nativas para representar el clúster de Service Fabric. Si examina las aplicaciones del inquilino en el [Portal de Azure clásico][azure-classic-portal], debería ver dos entradas nuevas:

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    El script imprimirá el código Json requerido por la plantilla de Azure Resource Manager al crear el clúster en la sección siguiente, por lo que debe mantener abierta la ventana de PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## Creación de una plantilla de Resource Manager para el clúster de Service Fabric

En esta sección, se usará la salida de los comandos de PowerShell anteriores en una plantilla de Resource Manager para el clúster de Service Fabric.

Las plantillas de ejemplo de Resource Manager están disponibles en la [Galería de plantillas de inicio de rápido de Azure en GitHub][azure-quickstart-templates]. Estas plantillas se pueden usar como punto de partida para crear la plantilla de clúster.

### Creación de la plantilla de Resource Manager

En esta guía se usa la plantilla de ejemplo de [clúster seguro de 5 nodos][service-fabric-secure-cluster-5-node-1-nodetype-wad] y los parámetros de plantilla. Descargue `azuredeploy.json` y `azuredeploy.parameters.json` en su equipo y abra ambos archivos en el editor de texto de su elección.

### Adición de certificados

Para agregar certificados a una plantilla de Resource Manager para el clúster, haga referencia al Almacén de claves que contiene las claves de certificado. Se recomienda colocar estos valores del servicio Key Vault en un archivo de parámetros de plantilla de Resource Manager para que el archivo de plantilla de Resource Manager se pueda seguir usando, sin los valores específicos de una implementación.

#### Adición de todos los certificados a osProfile de VMSS

Todos los certificados que se tengan que instalar en el clúster deben estar configurados en la sección osProfile de VMSS (Microsoft.Compute/virtualMachineScaleSets). Con ello se indica al proveedor de recursos que instale el certificado en las máquinas virtuales. Esto incluye el certificado del clúster, así como los certificados de seguridad de la aplicación que planee usar para las aplicaciones:

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

#### Configuración del certificado de clúster de Service Fabric

El certificado de autenticación del clúster debe estar configurado también en el recurso del clúster de Service Fabric (Microsoft.ServiceFabric/clusters) y en la extensión de Service Fabric para VMSS en el recurso de VMSS. De esta forma, el proveedor de recursos de Service Fabric puede configurarlo para su uso en la autenticación del clúster y la autenticación del servidor en los puntos de conexión de administración.

##### Recurso de VMSS:

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

##### Recurso de Service Fabric:

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

### Inserción de la configuración de AAD

La configuración de AAD creada anteriormente se puede insertar directamente en la plantilla de Resource Manager; sin embargo, se recomienda extraer los valores en un archivo de parámetros para que la plantilla de Resource Manager se puede seguir usando, sin los valores específicos de una implementación.

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

### <a "configure-arm" ></a>Configuración de los parámetros de plantilla de Resource Manager

Por último, utilice los valores de salida del Almacén de claves y los comandos de PowerShell de AAD para rellenar el archivo de parámetros:

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
Llegados a este punto, debería tener lo siguiente:

 - Grupo de recursos del Almacén de claves
    - Almacén de claves
    - Certificado de autenticación de servidor de clúster
    - Certificado de cifrado de datos
 - Inquilino de Azure Active Directory
    - Aplicación de AAD para la administración basada en web y Service Fabric Explorer
    - Aplicación de AAD para administración de cliente nativo
    - Usuarios con roles asignados
 - Plantilla de Resource Manager para el clúster de Service Fabric
    - Certificados configurados mediante el Almacén de claves
    - Azure Active Directory configurado

En el diagrama siguiente se ilustra el lugar que ocupa el Almacén de claves y la configuración de AAD en la plantilla de Resource Manager.

![Asignación de dependencias de Resource Manager][cluster-security-arm-dependency-map]

## Creación de clústeres

Ahora está listo para crear el clúster con la [implementación de ARM][resource-group-template-deploy].

#### Pruébelo.

Utilice el siguiente comando de PowerShell para probar la plantilla de Resource Manager con un archivo de parámetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### Impleméntelo.

Si la prueba de la plantilla de Resource Manager es satisfactoria, use el siguiente comando de PowerShell para implementar dicha plantilla con un archivo de parámetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## Asignación de usuarios a roles

Una vez que haya creado las aplicaciones para representar el clúster, debe asignar los usuarios a los roles compatibles con Service Fabric: solo lectura y administrador. Puede hacerlo mediante el [Portal de Azure clásico][azure-classic-portal].

1. Vaya al inquilino y elija Aplicaciones.
2. Elija la aplicación web, que tiene un nombre parecido a `myTestCluster_Cluster`.
3. Haga clic en la pestaña Usuarios.
4. Elija un usuario para asignar y haga clic en el botón **Asignar** situado en la parte inferior de la pantalla.

    ![Botón para asignar usuarios a roles][assign-users-to-roles-button]

5. Seleccione el rol que quiere asignar al usuario.

    ![Asignación de usuarios a roles][assign-users-to-roles-dialog]

>[AZURE.NOTE] Para más información sobre los roles de Service Fabric, consulte [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a>
##  Creación de clústeres seguros en Linux

Para facilitar el proceso, se ha proporcionado un script de aplicación auxiliar [aquí](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Para utilizar este script de aplicación auxiliar, se supone que ya tiene instalada la CLI de Azure y se encuentra en la ruta de acceso. Asegúrese de que el script tiene permisos de ejecución ejecutando `chmod +x cert_helper.py` después de descargarlo. El primer paso es iniciar sesión en su cuenta de Azure mediante la CLI con el comando `azure login`. Después de iniciar sesión en su cuenta de Azure, use la aplicación auxiliar con su certificado firmado por la entidad de certificación, como se muestra en el siguiente comando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Este comando devuelve las siguientes tres cadenas como salida:

1. SourceVaultID, que es el identificador para el nuevo grupo KeyVault ResourceGroup que creó para usted.

2. CertificateUrl para acceder al certificado.

3. CertificateThumbprint, que se utiliza para la autenticación.


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

 El nombre de sujeto del certificado debe coincidir con el nombre del dominio usado para acceder al clúster de Service Fabric. Este es un requisito para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio `.cloudapp.azure.com`. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado que se usó para el clúster.

Estas son las entradas necesarias para crear un clúster de Service Fabric seguro (sin AAD) tal y como se describe en [Configuración de los parámetros de plantilla de Resource Manager](#configure-arm). Puede conectarse al clúster seguro mediante las instrucciones de [autenticación del acceso de cliente a un clúster](service-fabric-connect-to-secure-cluster.md). Los clústeres de vista previa de Linux no admiten la autenticación de AAD. Puede asignar roles de administrador y cliente como se indica en la sección [Asignación de roles a usuarios](#assign-roles). Al especificar los roles de administrador y cliente para un clúster de vista previa de Linux, tiene que proporcionar huellas digitales de certificados para la autenticación (en lugar del nombre del sujeto, ya que no se realiza ninguna validación ni revocación de cadena en esta versión preliminar).


Si desea usar un certificado autofirmado con fines de prueba, puede usar el mismo script para generar un certificado autofirmado y cargarlo en KeyVault, proporcionando la marca `ss` en lugar de proporcionar la ruta de acceso y el nombre del certificado. Por ejemplo, observe el siguiente comando para crear y cargar un certificado autofirmado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Este comando devuelve las mismas tres cadenas, SourceVault, CertificateUrl y CertificateThumbprint, que se utilizan para crear un clúster de Linux seguro, junto con la ubicación donde se colocó el certificado autofirmado. Necesitará el certificado autofirmado para conectarse al clúster. Puede conectarse al clúster seguro mediante las instrucciones de [autenticación del acceso de cliente a un clúster](service-fabric-connect-to-secure-cluster.md). El nombre de sujeto del certificado debe coincidir con el nombre del dominio usado para acceder al clúster de Service Fabric. Este es un requisito para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio `.cloudapp.azure.com`. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado que se usó para el clúster.

Los parámetros proporcionados por el script de la aplicación auxiliar pueden rellenarse en el portal como se describe en la sección [Creación de un clúster en Azure Portal](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## Pasos siguientes

En este punto, tiene un clúster seguro con autenticación de Azure Active Directory que proporciona autenticación de administración. Después, [conéctese al clúster](service-fabric-connect-to-secure-cluster.md) y aprenda a [administrar secretos de aplicación](service-fabric-application-secret-management.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[aad-graph-api-docs]: https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png

<!---HONumber=AcomDC_0928_2016-->