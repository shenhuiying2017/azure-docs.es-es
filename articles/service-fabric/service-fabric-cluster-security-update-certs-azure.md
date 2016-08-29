<properties
   pageTitle="Adición, sustitución y eliminación de certificados usados en un clúster de Service Fabric de Azure | Microsoft Azure"
   description="En este artículo se describe cómo cargar un certificado de clúster secundario y sustituir el antiguo certificado principal."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="chackdan"/>

# Agregar o quitar certificados para un clúster de Service Fabric de Azure

Se recomienda leer [Escenarios de seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md) para familiarizarse con cómo Service Fabric usa certificados X.509. Debe entender qué es un certificado de clúster y para qué se usa antes de seguir avanzando.

Al configurar la seguridad mediante certificados durante la creación del clúster, Service Fabric le permite especificar dos certificados de clúster: uno principal y uno secundario. Consulte [Creación de un clúster de Service Fabric en el Portal de Azure](service-fabric-cluster-creation-via-portal.md) o [Creación de un clúster de Azure a través de Azure Resource Manager](service-fabric-cluster-creation-via-Resource Manager.md) para obtener más información. Si se implementa a través de Resource Manager y se especifica un único certificado de clúster, este se utilizará como el certificado principal. Después de la creación del clúster, puede agregar un nuevo certificado como elemento secundario.

>[AZURE.NOTE] Para que un clúster sea seguro, siempre deberá tener implementado al menos un certificado principal o secundario válido (no revocado ni caducado), o el clúster dejará de funcionar. Cuando falten 90 días para la caducidad de todos los certificados válidos, el sistema genera un seguimiento de advertencias y un evento de estado de advertencia en el nodo. Actualmente, Service Fabric no envía ningún mensaje de correo electrónico ni cualquier otra notificación sobre este tema.


## Adición de un certificado secundario mediante el portal
Para agregar otro certificado como secundario, debe cargar el certificado en un Almacén de claves de Azure y luego implementarlo en las máquinas virtuales del clúster. Para obtener más información, consulte [Deploy certificates to VMs from a customer-managed Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) (Implementación de certificados en máquinas virtuales desde un Almacén de claves administrado por el cliente).

1. Consulte [Carga del certificado X.509 en el Almacén de claves](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault) para obtener instrucciones de este proceso.

2. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y vaya al recurso del clúster al que desee agregar el certificado.
3. En **Configuración**, haga clic en **Seguridad** para abrir la hoja de seguridad del clúster.
4. Haga clic en el botón **+ Certificado** en la parte superior de la hoja para obtener acceso a la hoja **Agregar certificado**.
5. Seleccione Huella digital del certificado secundario en la lista desplegable y rellene la huella digital del certificado secundario que haya cargado en el almacén de claves.

>[AZURE.NOTE]
A diferencia del flujo de trabajo de creación de clúster, aquí no se tienen en cuenta los detalles de la información del almacén de claves, ya que se supone que en el momento en que se encuentre en esta hoja ya habrá implementado el certificado a las máquinas virtuales y el certificado ya estará disponible en el almacén de certificados local en la instancia de VMSS.

Haga clic en **Certificado**. Se inicia una implementación y se mostrará una barra de estado azul en la hoja de seguridad del clúster.

![Screen shot of certificate thumbprints in the portal][SecurityConfigurations\_02]

Cuando la implementación finalice correctamente, podrá usar tanto el certificado principal como el secundario para realizar operaciones de administración en el clúster.

![Screen shot of certificate deployment in progress][SecurityConfigurations\_03]

Esta captura de pantalla muestra el aspecto de la hoja de seguridad una vez completada la implementación.

![Screen shot of certificate thumbprints after deployment][SecurityConfigurations\_08]


Ahora puede usar el nuevo certificado recién agregado para conectarse y realizar operaciones en el clúster.

>[AZURE.NOTE]
Actualmente no hay ninguna manera de intercambiar los certificados principal y secundario certificados en el portal. Esta característica está en preparación. Siempre que haya un certificado válido de clúster, el clúster funcionará correctamente.

## Adición de un certificado secundario y cambiarlo para que sea el principal mediante Powershell de Resource Manager

Estos pasos suponen que está familiarizado con el funcionamiento de Resource Manager, que ha implementado al menos un clúster de Service Fabric mediante una plantilla de Resource Manager, y que tiene a mano la plantilla que utiliza para configurar el clúster. También se supone que está familiarizado con el uso de JSON.

>[AZURE.NOTE]
Si necesita una plantilla de ejemplo y parámetros que usar como guía o como punto de partida, descárguelos desde este [git-repo]. (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

#### Edición de la plantilla de Resource Manager 

Si estaba utilizando el ejemplo del [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para seguir el artículo, encontrará estos cambios en el ejemplo 5-VM-1-NodeTypes-Secure\_Step2.JSON. Uso de 5-VM-1-NodeTypes-Secure\_Step1.JSON para implementar un clúster seguro

1. Abra la plantilla de Resource Manager utilizada para implementar el clúster.
2. Agregue un nuevo parámetro "secCertificateThumbprint" de tipo "string". Si está usando la plantilla de Resource Manager descargada desde el portal durante la creación o desde las plantillas de inicio rápido, solo tiene que buscar ese parámetro; ya debería estar definido.
3. Busque la definición de recursos "Microsoft.ServiceFabric/clusters". En las propiedades, encontrará una etiqueta JSON "Certificate", similar al siguiente fragmento de código JSON.
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
``` 

4. Agregue una nueva etiqueta "thumbprintSecondary" y asígnele un valor "[parameters('secCertificateThumbprint')]".

Ahora la definición de recursos debería ser similar a lo siguiente (dependiendo del origen de la plantilla, puede que no sea exactamente igual que este fragmento de código). Como se puede ver a continuación, aquí se especifica un nuevo certificado como principal y se mueve el principal actual como secundario. Esto provoca la sustitución del certificado actual por el nuevo certificado en un paso de implementación.

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### Edición del archivo de plantilla para reflejar los nuevos parámetros agregados anteriormente

Si estaba utilizando el ejemplo de [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para seguir el artículo, puede empezar a hacer estos cambios en la muestra 5-VM-1-NodeTypes-Secure\_Step2.JSON.


Edite el archivo con el parámetro de plantilla de Resource Manager, agregue los nuevos parámetros para secCertificate, intercambie los detalles del certificado principal existente con los del secundario y reemplace los detalles del certificado principal con los del nuevo certificado.

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### Implementación de la plantilla en Azure

1. Ahora está preparado para implementar la plantilla en Azure. Abra un símbolo del sistema de Azure PS versión 1 o superior.
2. Inicie sesión en su cuenta de Azure y seleccione la suscripción de Azure específica. Este es un paso importante para personas que tienen acceso a más de una suscripción.


```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Pruebe la plantilla antes de implementarla. Utilice el grupo de recursos en el que esté implementado el clúster actualmente.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implemente la plantilla en el grupo de recursos. Utilice el grupo de recursos en el que esté implementado el clúster actualmente. Ejecute el comando New-AzureRmResourceGroupDeployment. No es necesario especificar el modo, ya que el valor predeterminado es **incremental**.

>[AZURE.NOTE]
Si establece el modo completo, podría eliminar accidentalmente los recursos que no estén en la plantilla. Por ello no lo utilice en este escenario.
   

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Este es un ejemplo ya rellenado del mismo powershell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Una vez completada la implementación, conecte el clúster mediante el nuevo certificado y realice algunas consultas. Siempre que sea capaz de ello. Después, puede eliminar el antiguo certificado principal.

Si está utilizando un certificado autofirmado, no olvide importarlo en su almacén de certificados local TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Como referencia rápida, este es el comando para conectarse a un clúster seguro
```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Como referencia rápida, este es el comando para obtener el estado del clúster
```powershell
Get-ServiceFabricClusterHealth 
```
 
## Eliminación del certificado anterior mediante el portal
Este es el proceso para quitar un certificado antiguo de modo que el clúster no lo utilice:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y vaya a la configuración de seguridad del clúster.
2. Haga clic con el botón derecho en el certificado que desea quitar.
3. Seleccione Eliminar y siga las indicaciones.

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## Pasos siguientes
Lea estos artículos para más información sobre la administración de clúster:

- [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](service-fabric-cluster-upgrade.md)
- [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png [SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png [SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png [SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png

<!---HONumber=AcomDC_0817_2016-->