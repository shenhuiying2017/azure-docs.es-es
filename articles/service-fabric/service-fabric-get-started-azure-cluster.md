---
title: "Configuración de un clúster de Azure Service Fabric | Microsoft Docs"
description: "Inicio rápido: creación de un clúster de Windows o Linux Service Fabric en Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/05/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ad473a2c9006e2593a84364f03e3954a569adcab
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Creación del primer clúster de Service Fabric en Azure
Un [clúster de Service Fabric](service-fabric-deploy-anywhere.md) es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. En esta guía de inicio rápido le ayuda a crear un clúster de cinco nodos, que se ejecute en Windows o Linux, a través de [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) o [Azure Portal](http://portal.azure.com) en unos minutos.  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


## <a name="use-the-azure-portal"></a>Uso del Portal de Azure

Inicie sesión en Azure Portal en [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Creación de clústeres

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Proceso** en la hoja **Nuevo** y, a continuación, seleccione **Clúster de Service Fabric** en la hoja **Proceso**.
3. Rellene el formulario **Básico** de Service Fabric. Para **Sistema operativo**, seleccione la versión de Windows o Linux en la que desee que se ejecuten los nodos del clúster. El nombre de usuario y la contraseña que especifique aquí se usarán para iniciar sesión en la máquina virtual. En **Grupo de recursos**, cree uno. Un grupo de recursos es un contenedor lógico en el que se administran y crean los recursos de Azure. Cuando haya terminado, haga clic en **Aceptar**.

    ![Salida de instalación de clúster][cluster-setup-basics]

4. Rellene el formulario **Configuración de clúster**.  Para **Número de tipos de nodos**, escriba "1" y establezca [Nivel de durabilidad](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) en "Bronce".

5. Seleccione **Configurar cada tipo de nodo** y rellene el formulario **Configuración del tipo de nodo**. Los tipos de nodo definen el tamaño de las máquinas virtuales, número de máquinas virtuales, los puntos de conexión personalizados y otras opciones para las máquinas virtuales de ese tipo. Cada tipo de nodo definido se configura como un conjunto de escalado de máquinas virtuales independiente, que se usa para implementar y administrar máquinas virtuales como un conjunto. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad.  El primer tipo de nodo, o nodo principal, es donde los servicios del sistema de Service Fabric se hospedan y tienen que tener cinco o más máquinas virtuales.

    En cualquier implementación de producción, el [planeamiento de capacidad](service-fabric-cluster-capacity.md) es un paso importante.  Para este inicio rápido, sin embargo, no está ejecutando aplicaciones así que seleccione un tamaño de máquina virtual *DS1_v2 Estándar*.  Seleccione "Plata" para el [nivel de confiabilidad](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) y un capacidad de conjunto de escalado de máquinas virtuales inicial de 5.  

    Los puntos de conexión personalizados abrirán puertos en Azure Load Balancer para que pueda conectarse con aplicaciones que se ejecutan en el clúster.  Escriba "80, 8172" para abrir los puertos 80 y 8172.

    No marque la casilla **Configurar opciones avanzadas**, que se utiliza para personalizar los puntos de conexión de la administración de TCP o HTTP, intervalos de puertos de aplicación, [restricciones de posición](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) y [propiedades de capacidad](service-fabric-cluster-resource-manager-metrics.md).    

    Seleccione **Aceptar**.

6. En el formulario **Configuración de clúster**, establezca **Diagnósticos** en **Activar**.  Para este inicio rápido, no es necesario que escriba ninguna propiedad [configuración de tejido](service-fabric-cluster-fabric-settings.md).  En **Versión de Fabric**, seleccione el modo de actualización **Automático** para que Microsoft actualice automáticamente la versión del código de Fabric que se ejecuta en el clúster.  Establezca el modo en **Manual** si desea [elegir una versión compatible](service-fabric-cluster-upgrade.md) para actualizar. 

    ![Configuración del tipo de nodo][node-type-config]

    Seleccione **Aceptar**.

7. Rellene el formulario **Seguridad**.  Para este inicio rápido seleccione **No seguro**.  Se recomienda crear un clúster seguro para las cargas de producción, dado que cualquiera puede conectarse de forma anónima a un clúster no seguro y realizar operaciones de administración.  

    Los certificados se usan en Service Fabric para proporcionar autenticación y cifrado con el fin de proteger diversos aspectos de un clúster y sus aplicaciones. Para más información sobre el modo en que se usan los certificados en Service Fabric, consulte los [Escenarios de seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md).  Para habilitar la autenticación de usuario con Azure Active Directory o para configurar certificados para la seguridad de la aplicación, [cree un clúster desde una plantilla de Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Seleccione **Aceptar**.

8. Revise el resumen.  Si desea descargar una plantilla de Resource Manager compilada a partir de la configuración que ha especificado, seleccione **Descargar plantilla y parámetros**.  Seleccione **Crear** para crear el clúster.

    Puede ver el progreso de creación en las notificaciones. (Haga clic en el icono de la "campana" cerca de la barra de estado en la parte superior derecha de la pantalla). Si hizo clic en **Anclar a Panel de inicio** al crear el clúster, verá **Deploying Service Fabric Cluster** (Implementando clúster de Service Fabric) anclado al panel de **Inicio**.

### <a name="view-cluster-status"></a>Visualización del estado del clúster
Una vez creado el clúster, puede inspeccionarlo en la hoja **Información general** del portal. Ahora puede ver los detalles del clúster en el panel, incluido el punto de conexión público del clúster y un vínculo a Service Fabric Explorer.

![Estado del clúster][cluster-status]

### <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualización del clúster mediante Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) es una buena herramienta para visualizar el clúster y administrar aplicaciones.  Service Fabric Explorer es un servicio que se ejecuta en el clúster.  Acceda a él mediante un explorador web haciendo clic en el vínculo **Service Fabric Explorer** de la página del clúster **Información general** en el portal.  También puede escribir la dirección directamente en el explorador: [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

El panel del clúster proporciona información general del clúster, incluido un resumen de la aplicación y del estado del nodo. En la vista de nodos se muestra el diseño físico del clúster. Para un nodo determinado, puede comprobar qué aplicaciones tienen el código implementado en ese nodo.

![Service Fabric Explorer][service-fabric-explorer]

### <a name="connect-to-the-cluster-using-powershell"></a>Conexión a un clúster con PowerShell
Compruebe que el clúster se está ejecutando a través de una conexión usando PowerShell.  El módulo ServiceFabric PowerShell se instala con el [SDK de Service Fabric](service-fabric-get-started.md).  El cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) establece una conexión con el clúster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Consulte [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md) para ver ejemplos de conexión a un clúster. Después de conectarse al clúster, use el cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para mostrar una lista de nodos en el clúster e información sobre el estado de cada uno. **HealthState** debe ser *OK* para cada nodo.

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Eliminación del clúster
Un clúster de Service Fabric está formado por muchos otros recursos de Azure, además del recurso del clúster propiamente dicho. Así que, para eliminar completamente un clúster de Service Fabric, también debe eliminar todos los recursos que lo componen. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos. Si desea conocer otras formas de eliminar un clúster o de eliminar algunos (aunque no todos) de los recursos de un grupo de recursos, consulte [Eliminación de un clúster de Service Fabric](service-fabric-cluster-delete.md)

Eliminación de un grupo de recursos en Azure Portal:
1. Desplácese hasta el clúster de Service Fabric que quiere eliminar.
2. Haga clic en el nombre del **Grupo de recursos** en la página de información básica del clúster.
3. En la página **Resource Group Essentials** (Información básica del grupo de recursos) haga clic en **Eliminar** y siga las instrucciones que se indican en esa página para completar la eliminación del grupo de recursos.
    ![Eliminación del grupo de recursos][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-cluster"></a>Uso de Azure PowerShell para implementar un clúster seguro


1) Descargue la [versión 4.0 del módulo Azure PowerShell, o cualquier versión posterior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps), en el equipo.

2) Abra una ventana de Windows PowerShell y ejecute el comando siguiente. 
    
```powershell

Get-Command -Module AzureRM.ServiceFabric 
```

Debería ver una salida similar a la siguiente.

![ps-list][ps-list]

3) Inicie sesión en Azure y seleccione la suscripción en la que desea crear el clúster

```powershell

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId "Subcription ID" 

```

4) Ejecute el siguiente comando para crear un clúster seguro. No olvide personalizar los parámetros. 


````powershell

$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
$RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
$RDPuser="vmadmin"
$RGname="mycluster" # this is also the name of your cluster
$clusterloc="SouthCentralUS"
$subname="$RGname.$clusterloc.cloudapp.azure.com"
$certfolder="c:\mycertificates\"
$clustersize=1 # can take values 1, 3-99

New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder

````

El comando puede tardar entre 10 y 30 minutos en completarse y su resultado debe ser similar al siguiente. Dicho resultado tiene información acerca del certificado, el valor de KeyVault en que se cargó y la carpeta local donde se copia el certificado. 

![ps-out][ps-out]

5) Copie todo el resultado y guárdelo como archivo de texto, ya que tenemos que hacer referencia a él. Tome nota de la siguiente información del resultado.
 

- **CertificateSavedLocalPath**: c:\mycertificates\mycluster20170504141137.pfx
- **CertificateThumbprint**: C4C1E541AD512B8065280292A8BA6079C3F26F10
- **ManagementEndpoint**: https://mycluster.southcentralus.cloudapp.azure.com:19080
- **ClientConnectionEndpointPort**: 19000

### <a name="install-the-certificate-on-your-local-machine"></a>Instalación del certificado en un equipo local
  
Para conectarse al clúster, es preciso instalar el certificado en el almacén Personal del usuario actual. 

Ejecute el siguiente PowerShell

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

Ahora está listo para conectarse a su clúster seguro.

### <a name="connect-to-a-secure-cluster"></a>Conexión a un clúster seguro 

Ejecute el siguiente comando de PowerShell para conectarse a un clúster seguro. Los detalles del certificado deben corresponder a los de un certificado que se haya usado para configurar el clúster. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


En el ejemplo siguiente se muestran los parámetros completados: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Ejecute el siguiente comando para comprobar que está conectado y que el clúster es correcto.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="publish-your-apps-to-your-cluster-from-visual-studio"></a>Publicación de aplicaciones en un clúster desde Visual Studio

Ahora que ha configurado un clúster de Azure, puede publicar aplicaciones en él desde Visual Studio, para lo que debe seguir la información del documento [Publicación de una aplicación en un clúster remoto con Visual Studio](service-fabric-publish-app-remote-cluster.md). 

### <a name="remove-the-cluster"></a>Eliminación del clúster
Un clúster está formado por muchos otros recursos de Azure, además del propio recurso del clúster. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos. 

```powershell

Remove-AzureRmResourceGroup -Name $RGname -Force

```

## <a name="next-steps"></a>Pasos siguientes
Una vez configurado un clúster de desarrollo, pruebe lo siguiente:
* [Creación de un clúster seguro en el portal](service-fabric-cluster-creation-via-portal.md)
* [Crear un clúster a partir de una plantilla](service-fabric-cluster-creation-via-arm.md) 
* [Implemente aplicaciones mediante PowerShell](service-fabric-deploy-remove-applications.md).


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG

