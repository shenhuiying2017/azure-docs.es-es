---
title: "Creación de un clúster de Service Fabric en Azure | Microsoft Docs"
description: "Aprenda a crear un clúster de Service Fabric de Windows o Linux en Azure mediante PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 80c5a2a43302e1cc8ec3b4298eb393a1861252d3
ms.contentlocale: es-es
ms.lasthandoff: 08/16/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Creación de un clúster seguro en Azure mediante PowerShell
En este tutorial se muestra cómo crear un clúster de Service Fabric (Windows o Linux) que se ejecuta en Azure. Cuando haya terminado, tendrá un clúster que se ejecuta en la nube en el que puede implementar aplicaciones.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de un clúster de Service Fabric en Azure mediante PowerShell
> * Protección del clúster con un certificado X.509
> * Conexión a un clúster con PowerShell
> * Eliminación de un clúster

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale el [SDK de Service Fabric y el módulo de PowerShell](service-fabric-get-started.md).
- Instale la [versión 4.1 o superior del módulo de Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

El procedimiento siguiente crea un clúster de versión preliminar de Service Fabric de un solo nodo (máquina virtual única), protegido mediante un certificado autofirmado que se crea con el clúster y que, luego, se coloca en un almacén de claves. Los clústeres de un único nodo no se pueden escalar más allá de una máquina virtual y los clústeres de versión preliminar no se pueden actualizar a versiones más recientes.

Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).
Para más información sobre la creación de clústeres de Service Fabric, consulte el artículo [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="create-the-cluster-using-azure-powershell"></a>Creación del clúster con Azure PowerShell
1. Descargue una copia local de la plantilla de Azure Resource Manager y el archivo de parámetros de este repositorio de GitHub: [plantilla de Azure Resource Manager para Service Fabric](https://aka.ms/securepreviewonelineclustertemplate).  *azuredeploy.json* es la plantilla de Azure Resource Manager que define un clúster de Service Fabric. *azuredeploy.parameters.json* es un archivo de parámetros para personalizar la implementación del clúster.

2. Personalice los parámetros siguientes en el archivo de parámetros *azuredeploy.parameters.json*:

   | Parámetro       | Descripción | Valor sugerido |
   | --------------- | ----------- | --------------- |
   | clusterLocation | Región de Azure en la que se implementa el clúster. | *Por ejemplo, westeurope, eastasia, eastus* |
   | clusterName     | Nombre del clúster que desea crear. | *Por ejemplo, bobs-sfpreviewcluster* |
   | adminUserName   | Cuenta de administrador local en las máquinas virtuales del clúster. | *Cualquier nombre de usuario válido de Windows Server* |
   | adminPassword   | Contraseña de la cuenta de administrador local en las máquinas virtuales del clúster. | *Cualquier contraseña válida de Windows Server* |
   | clusterCodeVersion | La versión de Service Fabric que se ejecutará. (255.255.X.255 son versiones preliminares). | **255.255.5718.255** |
   | vmInstanceCount | El número de máquinas virtuales en el clúster (puede ser 1 o 3−99). | **1** | *Para un clúster de versión preliminar, especifique solo una máquina virtual*. |

3. Abra una consola de PowerShell, inicie sesión en Azure y seleccione la suscripción en la que desea implementar el clúster:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. Cree y cifre una contraseña para el certificado que utilizará Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. Cree el clúster y su certificado ejecutando el siguiente comando:

   ```powershell
      New-AzureRmServiceFabricCluster `
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >El parámetro `-CertificateSubjectName` debe alinearse con el parámetro clusterName, que se ha especificado en el archivo de parámetros. Asimismo, el dominio debe enlazarse con la región de Azure que elija, como: `clustername.eastus.cloudapp.azure.com`.

Una vez finalizada la configuración, se envía la información sobre el clúster creado en Azure. También se copia el certificado de clúster en el directorio -CertificateOutputFolder de la ruta de acceso especificada mediante este parámetro. Este certificado se necesita para acceder a Service Fabric Explorer y ver el estado del clúster.

Anote la dirección URL del clúster, que puede ser similar a la siguiente dirección URL: *https://mycluster.westeurope.cloudapp.azure.com:19080*.

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>Modificación del certificado y acceso a Service Fabric Explorer ##

1. Haga doble clic en el certificado para abrir el Asistente para importar certificados.

2. Utilice los valores predeterminados, pero asegúrese de activar la casilla **Marcar esta clave como exportable** en el paso de **protección de clave privada**. Visual Studio debe exportar el certificado al configurar Azure Container Registry para la autenticación de clúster de Service Fabric más adelante en este tutorial.

3. Ahora puede abrir Service Fabric Explorer en un explorador. Para ello, vaya a la URL de **ManagementEndpoint** del clúster utilizando un explorador web y seleccione el certificado que guardó en la máquina.

>[!NOTE]
>Al abrir Service Fabric Explorer, verá un error de certificado, ya que está usando un certificado autofirmado. En Edge, debe hacer clic en *Detalles* y, luego, en el vínculo *Acceder a la página web*. En Chrome, debe hacer clic en *Avanzadas* y, luego, en el vínculo *continuar*.

>[!NOTE]
>Si se produce un error en la creación del clúster, siempre puede volver a ejecutar el comando, lo que actualiza los recursos ya implementados. Si se creó un certificado como parte de la implementación con errores, se genera uno nuevo. Para solucionar problemas de creación del clúster, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="connect-to-the-secure-cluster"></a>Conexión al clúster seguro
Conéctese al clúster mediante el módulo PowerShell de Service Fabric instalado con el SDK de Service Fabric.  En primer lugar, instale el certificado en el almacén personal (Mi) del usuario actual en el equipo.  Ejecute el siguiente comando de PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Ahora está listo para conectarse a su clúster seguro.

El módulo PowerShell de **Service Fabric** proporciona muchos cmdlets para administrar clústeres, aplicaciones y servicios de Service Fabric.  Use el cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para conectarse al clúster seguro. Los detalles de la huella digital de certificado y del punto de conexión se encuentran en la salida de un paso anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Compruebe que está conectado y que el estado del clúster es correcto mediante el cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Un clúster está formado por muchos otros recursos de Azure, además del propio recurso del clúster. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos.

Inicie sesión en Azure y seleccione el identificador de suscripción con el que quiere quitar el clúster.  Para encontrar el identificador de suscripción, inicie sesión en [Azure Portal](http://portal.azure.com). Elimine el grupo de recursos y todos los recursos de clúster mediante el cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de un clúster de Service Fabric en Azure
> * Protección del clúster con un certificado X.509
> * Conexión a un clúster con PowerShell
> * Eliminación de un clúster

A continuación, avance hasta el siguiente tutorial para aprender a implementar una aplicación existente.
> [!div class="nextstepaction"]
> [Implementación de una aplicación .NET con Docker Compose](service-fabric-host-app-in-a-container.md)

