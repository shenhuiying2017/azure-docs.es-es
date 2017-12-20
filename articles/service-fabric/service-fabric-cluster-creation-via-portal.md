---
title: "Creación de un clúster de Service Fabric en Azure Portal | Microsoft Azure"
description: "En este artículo se describe cómo configurar un clúster de Service Fabric seguro en Azure mediante Azure Portal y Azure Key Vault."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.openlocfilehash: be880efdcf1276252c76f27c2f2fd99edd606caa
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Creación de un clúster de Service Fabric en Azure mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md)
> * [Portal de Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Esta guía paso a paso le lleva por la configuración de un clúster de Service Fabric seguro en Azure mediante el Portal de Azure. Estos pasos son los siguientes:

* Configuración de Key Vault para administrar las claves de cara a la seguridad del clúster.
* Creación de un clúster protegido en Azure a través del Portal de Azure.
* Autenticación de los administradores mediante certificados.

> [!NOTE]
> Para información sobre opciones de seguridad más avanzadas, como la autenticación de usuarios con Azure Active Directory y la configuración de certificados para la seguridad de las aplicaciones, [cree el clúster mediante Azure Resource Manager][create-cluster-arm].
> 
> 

Un clúster seguro es un clúster que impide el acceso no autorizado a operaciones de administración, lo que incluye la implementación, actualización y eliminación de aplicaciones y servicios y los datos que contienen. Un clúster no seguro es un clúster al que cualquiera puede conectarse en cualquier momento y realizar operaciones de administración. Aunque se puede crear un clúster no seguro, se **recomienda firmemente crear uno seguro**. Un clúster no seguro **no se puede proteger en un momento posterior** -se debe crear uno nuevo.

Los conceptos son los mismos para crear clústeres seguros, tanto si se trata de clústeres de Linux como de Windows. Para más información y scripts de aplicaciones auxiliares para crear clústeres seguros de Linux, consulte [Creación de clústeres seguros](service-fabric-cluster-creation-via-arm.md). Los parámetros obtenidos por el script de la aplicación auxiliar pueden especificarse en el portal como se describe en la sección [Creación de un clúster en Azure Portal](#create-cluster-portal).

## <a name="configure-key-vault"></a>Configuración de Key Vault 
### <a name="log-in-to-azure"></a>Inicie sesión en Azure.
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

### <a name="set-up-key-vault"></a>Configuración de Key Vault
Esta parte de la guía le lleva por la creación de un almacén de claves para un clúster de Service Fabric de Azure y para aplicaciones de Service Fabric. Para obtener una guía completa sobre Key Vault, consulte [Introducción a Key Vault][key-vault-get-started].

Service Fabric usa certificados X.509 para proteger un clúster. Azure Key Vault se usa para administrar certificados para clústeres de Service Fabric en Azure. Cuando un clúster se implementa en Azure, el proveedor de recursos de Azure responsable de crear clústeres de Service Fabric extrae los certificados de Key Vault y los instala en las máquinas virtuales del clúster.

En el diagrama siguiente se ilustra la relación entre Key Vault, un clúster de Service Fabric y el proveedor de recursos de Azure que usa los certificados almacenados en Key Vault cuando se crea el clúster:

![Instalación del certificado][cluster-security-cert-installation]

#### <a name="create-a-resource-group"></a>Creación de un grupo de recursos
El primer paso es crear un nuevo grupo de recursos específicamente para Key Vault. Se recomienda colocar el almacén de claves en su propio grupo de recursos, así puede quitar los grupos de recursos de almacenamiento y de proceso, como el grupo de recursos que tiene el clúster de Service Fabric, sin perder sus claves y secretos. El grupo de recursos que tiene el almacén de claves debe estar en la misma región que el clúster que lo usa.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

#### <a name="create-key-vault"></a>Create Key Vault
Cree un almacén de claves en el nuevo grupo de recursos. El almacén de claves **debe estar habilitado para implementación** de forma que el proveedor de recursos de Service Fabric pueda recibir de él certificados e instalarlos en nodos del clúster:

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


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

Si ya tiene un almacén de claves, puede habilitarlo para implementación mediante alguno de estos métodos:

##### <a name="azure-powershell"></a>Azure PowerShell

```powershell
PS C:\Users\vturecek> Set-AzureRmKeyVaultAccessPolicy -VaultName 'myvault' -EnabledForDeployment
```

##### <a name="azure-cli"></a>CLI de Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


### <a name="add-certificates-to-key-vault"></a>Adición de certificados al almacén de claves
Los certificados se usan en Service Fabric para proporcionar autenticación y cifrado con el fin de proteger diversos aspectos de un clúster y sus aplicaciones. Para obtener más información sobre el modo en que se usan los certificados en Service Fabric, consulte los [Escenarios de seguridad de los clústeres de Service Fabric][service-fabric-cluster-security].

#### <a name="cluster-and-server-certificate-required"></a>Certificado de clúster y servidor (obligatorio)
Este certificado es necesario para proteger un clúster e impedir el acceso no autorizado. La seguridad adopta dos formas:

* **Autenticación del clúster:** se autentica la comunicación de nodo a nodo para la federación del clúster. Solo los nodos que pueden probar su identidad con este certificado pueden unirse al clúster.
* **Autenticación del servidor:** los puntos de conexión de administración del clúster se autentican en un cliente de administración, de forma que este sabe que está hablando con el clúster real. Este certificado proporciona también Capa de sockets seguros (SSL) para la API de administración de HTTPS y para Service Fabric Explorer sobre HTTPS.

Para servir a estos propósitos, el certificado debe cumplir los siguientes requisitos:

* El certificado debe contener una clave privada.
* El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
* El nombre de sujeto del certificado debe coincidir con el nombre del dominio usado para acceder al clúster de Service Fabric. Este es un requisito para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio `.cloudapp.azure.com` . Adquiera un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado que se usó para el clúster.

#### <a name="client-authentication-certificates"></a>Certificados de autenticación de cliente
Los certificados de cliente adicionales autentican a los administradores en las tareas de administración del clúster. Service Fabric tiene dos niveles de acceso: **administrador** y **usuario de solo lectura**. Se debe usar como mínimo un certificado para el acceso administrativo. Para accesos de nivel de usuario adicionales, se debe proporcionar un certificado diferente. Para obtener más información sobre los roles de acceso, consulte [Control de acceso basado en roles para clientes de Service Fabric][service-fabric-cluster-security-roles].

No es necesario cargar los certificados de autenticación de cliente en Key Vault para trabajar con Service Fabric. Estos certificados solo se deben proporcionar a los usuarios que están autorizados para la administración del clúster. 

> [!NOTE]
> Azure Active Directory es el método recomendado para autenticar a los clientes en las operaciones de administración del clúster. Para usar Azure Active Directory, debe [crear un clúster mediante Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificados de aplicación (opcionales)
Se puede instalar un número cualquiera de certificados adicionales en un clúster para proteger la aplicación. Antes de crear el clúster, tenga en cuenta los escenarios de seguridad de las aplicaciones que requieren que se instale un certificado en los nodos, por ejemplo:

* Cifrado y descifrado de los valores de configuración de aplicación
* Cifrado de datos entre nodos durante la replicación 

No se pueden configurar certificados de la aplicación al crear un clúster mediante el Portal de Azure. Para configurar certificados de aplicación en el momento de configuración del clúster, debe [crear un clúster mediante Azure Resource Manager][create-cluster-arm]. También puede agregar certificados de aplicación al clúster después de que se ha creado.

#### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formato de certificados para el uso del proveedor de recursos de Azure
Los archivos de clave privada (.pfx) se pueden agregar y usar directamente mediante Key Vault. Sin embargo, el proveedor de recursos de Azure requiere que las claves se almacenen en un formato JSON especial que incluye el .pfx como una cadena codificada con base-64 y la contraseña de clave privada. Para hacer frente a estos requisitos, las claves deben estar colocadas en una cadena JSON y luego almacenarse como *secretos* en Key Vault.

Para facilitar este proceso, hay un módulo de PowerShell [disponible en GitHub][service-fabric-rp-helpers]. Siga estos pasos para utilizar el módulo:

1. Descargue todo el contenido del repositorio en un directorio local. 
2. Importe el módulo en la ventana de PowerShell:

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

El comando `Invoke-AddCertToKeyVault` de este módulo de PowerShell convierte automáticamente una clave privada de certificado en una cadena JSON y la carga en Key Vault. Úsela para agregar el certificado de clúster y cualquier certificado de aplicación adicional a Key Vault. Repita este paso con cualquier certificado adicional que quiera instalar en el clúster.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
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

Estos son todos los requisitos previos de Key Vault para configurar una plantilla de Resource Manager para el clúster de Service Fabric que instala certificados para la autenticación de nodos, la seguridad de los puntos de conexión de administración y la autenticación, y cualquier característica de seguridad de aplicación adicional que use certificados X.509. En este punto, debería tener ahora la siguiente configuración en Azure:

* Grupo de recursos de Key Vault
  * Key Vault
    * Certificado de autenticación de servidor de clúster

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Creación de un clúster en el Portal de Azure
### <a name="search-for-the-service-fabric-cluster-resource"></a>Búsqueda del recurso de clúster de Service Fabric
![Busque la plantilla del clúster de Service Fabric en el Portal de Azure.][SearchforServiceFabricClusterTemplate]

1. Inicie sesión en [Azure Portal][azure-portal].
2. Haga clic en **+ Nuevo** para agregar una nueva plantilla de recursos. Busque la plantilla Clúster de Service Fabric en **Marketplace** debajo de **Todo**.
3. Seleccione **Clúster de Service Fabric** en la lista.
4. Vaya a la hoja **Clúster de Service Fabric** y haga clic en **Crear**.
5. La hoja **Crear clúster de Service Fabric** consta de los siguientes cuatro pasos.

#### <a name="1-basics"></a>1. Aspectos básicos
![Captura de pantalla de la creación de un grupo de recursos.][CreateRG]

En la hoja Básico, se deben proporcionar los datos básicos del clúster.

1. Escriba el nombre del clúster.
2. Elija un **nombre de usuario** y una **contraseña** para Escritorio remoto para las máquinas virtuales.
3. No olvide seleccionar la **suscripción** en la que desea que se implemente el clúster, especialmente si tiene varias suscripciones.
4. Cree un **nuevo grupo de recursos**. Es mejor asignarle el mismo nombre que el del clúster, pues resulta de utilidad para encontrarlos en el futuro, particularmente cuando está intentando realizar cambios en la implementación o eliminar el clúster.
   
   > [!NOTE]
   > Aunque puede decidir utilizar un grupo de recursos existente, es conveniente crear uno nuevo. Esto facilita la eliminación de los clústeres que no son necesarios.
   > 
   > 
5. Seleccione la **región** en la que quiere crear el clúster. Debe usar la misma región que aquella en la que se encuentra el almacén de claves.

#### <a name="2-cluster-configuration"></a>2. Configuración del clúster
![Creación de un tipo de nodo][CreateNodeType]

Configure los nodos del clúster. Los tipos de nodos definen los tamaños de máquina virtual, el número de máquinas virtuales y sus propiedades. El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal (el primero que se define en el portal) debe tener al menos cinco máquinas virtuales. Este es el tipo de nodo donde se encuentran los servicios del sistema de Service Fabric. No configure **Propiedades de ubicación** porque el sistema agrega automáticamente una propiedad de ubicación predeterminada de "NodeTypeName".

> [!NOTE]
> Un escenario común para varios tipos de nodos es una aplicación que contiene un servicio front-end y un servicio back-end. Quiere colocar el servicio front-end en máquinas virtuales más pequeñas (como D2) con puertos abiertos a Internet, pero el servicio back-end lo quiere poner en máquinas virtuales más grandes (como D4, D6, D15, etc.) sin puertos abiertos accesibles desde Internet.
> 
> 

1. Elija un nombre para el tipo de nodo (con una longitud de 1 a 12 caracteres y que contenga solamente letras y números).
2. El **tamaño** mínimo de máquinas virtuales para el tipo de nodo principal depende del nivel de **durabilidad** que elija para el clúster. El valor predeterminado del nivel de durabilidad es Bronze. Para obtener más información sobre la durabilidad, consulte [Elección de los niveles de confiabilidad y durabilidad del clúster de Service Fabric][service-fabric-cluster-capacity].
3. Seleccione el tamaño de máquina virtual y el plan de tarifa. Las máquinas virtuales de la serie D tienen unidades SSD y son muy recomendables para aplicaciones con estado. No utilice cualquier SKU de máquina virtual que tenga núcleos parciales o menos de 7 GB de capacidad de disco disponible. 
4. El **número** mínimo de máquinas virtuales para el tipo de nodo principal depende del nivel de **confiabilidad** que elija. El valor predeterminado del nivel de confiabilidad es Silver. Para obtener más información sobre la confiabilidad, consulte [Elección de los niveles de confiabilidad y durabilidad del clúster de Service Fabric][service-fabric-cluster-capacity].
5. Elija el número de máquinas virtuales del tipo de nodo. Puede escalar o reducir verticalmente el número de máquinas virtuales en un tipo de nodo más adelante, pero en el tipo de nodo principal, el número mínimo depende del nivel de confiabilidad que haya elegido. Otros tipos de nodo pueden tener un mínimo de 1 máquina virtual.
6. Configure los puntos de conexión personalizados. Este campo le permite especificar una lista de puertos separados por coma que quiere exponer mediante Azure Load Balancer a la Internet pública para sus aplicaciones. Por ejemplo, si planea implementar una aplicación web en el clúster, escriba aquí "80" para permitir el paso del tráfico del puerto 80 al clúster. Para obtener más información sobre los puntos de conexión, consulte la [comunicación con las aplicaciones][service-fabric-connect-and-communicate-with-services].
7. Configure los **diagnósticos**de clúster. De forma predeterminada, los diagnósticos se habilitan en el clúster para ayudar a solucionar los problemas. Si quiere deshabilitar los diagnósticos, cambie el botón de alternancia **Estado** a **Desactivado**. **No** se recomienda desactivar los diagnósticos.
8. Seleccione el modo de actualización de Service Fabric que desea establecer en el clúster. Seleccione **Automático**si desea que el sistema coja la última versión disponible automáticamente e intente actualizar el clúster con ella. Establezca el modo en **Manual**si desea elegir una versión compatible.

> [!NOTE]
> Se admiten solo los clústeres que ejecutan versiones compatibles de Service Fabric. Si selecciona el modo **Manual** , está aceptando la responsabilidad de actualizar el clúster a una versión compatible. Para obtener más información sobre el modo de actualización de Service Fabric, consulte el [documento de actualización de un clúster de Service Fabric.][service-fabric-cluster-upgrade]
> 
> 

#### <a name="3-security"></a>3. Seguridad
![Captura de pantalla de las configuraciones de seguridad del Portal de Azure.][SecurityConfigs]

El paso final consiste en proporcionar información del certificado para proteger el clúster mediante Key Vault y la información del certificado creada anteriormente.

* Rellene los campos del certificado principal con el resultado obtenido de cargar el **certificado del clúster** en Key Vault mediante el comando `Invoke-AddCertToKeyVault` de PowerShell.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* Marque el cuadro **Configurar opciones avanzadas** para insertar certificados de cliente para el **cliente de administración** y el **cliente de solo lectura**. En estos campos, inserte la huella digital del certificado de cliente de administración y la huella digital del certificado de cliente de solo lectura, si procede. Cuando los administradores intenten conectarse al clúster, se les concederá acceso únicamente si tienen un certificado con una huella digital que coincida con los valores de huella digital especificados aquí.  

#### <a name="4-summary"></a>4. Resumen

Para finalizar la creación del clúster, haga clic en **Resumen** para ver las configuraciones que ha proporcionado, o descargue la plantilla de Azure Resource Manager que se usará para implementar el clúster. Cuando haya proporcionado los valores de configuración obligatorios, se habilitará el botón **Aceptar** y podrá iniciar el proceso de creación del clúster al hacer clic en él.

Puede ver el progreso de creación en las notificaciones. (Haga clic en el icono de la "campana" cerca de la barra de estado en la parte superior derecha de la pantalla). Si hizo clic en **Anclar a Panel de inicio** al crear el clúster, verá **Deploying Service Fabric Cluster** (Implementando clúster de Service Fabric) anclado al panel de **Inicio**.

### <a name="view-your-cluster-status"></a>Visualización del estado del clúster
![Captura de pantalla de los detalles del clúster en el panel.][ClusterDashboard]

Una vez finalizado el clúster, puede inspeccionarlo en el portal:

1. Vaya a **Examinar** y haga clic en **Clústeres de Service Fabric**.
2. Busque su clúster y haga clic en él.
3. Ahora puede ver los detalles del clúster en el panel, incluido el punto de conexión público del clúster y un vínculo a Service Fabric Explorer.

En la sección **Node Monitor** (Monitor de nodo) de la hoja del panel del clúster se indica el número de máquinas virtuales correctas e incorrectas. Puede encontrar más detalles sobre el estado del clúster en [Introducción a la supervisión del mantenimiento de Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos en todo momento con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, normalmente no es seguro apagar todas las máquinas del clúster a menos que antes haya realizado una [copia de seguridad completa del estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conexión remota a una instancia de conjunto de escalado de máquinas virtuales (VMSS) o a un nodo del clúster
Cada uno de los tipos de nodos que especifica en el clúster da lugar a una configuración del conjunto de escalado de máquinas virtuales. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Pasos siguientes
En este punto, tiene un clúster seguro mediante certificados para la autenticación de administración. Después, [conéctese al clúster](service-fabric-connect-to-secure-cluster.md) y aprenda a [administrar secretos de aplicación](service-fabric-application-secret-management.md).  Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
