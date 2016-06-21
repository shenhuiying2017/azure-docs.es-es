<properties
   pageTitle="Protección de un clúster de Service Fabric con certificados| Microsoft Azure"
   description="En este artículo se describe cómo proteger un clúster de Service Fabric con certificados X.509."
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
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Protección de clústeres de Service Fabric de Azure mediante certificados

Un clúster de Azure Service Fabric es un recurso que usted posee. Para evitar el acceso no autorizado al recurso, debe protegerlo, especialmente cuando se están ejecutando en él cargas de trabajo de producción. Para configurar un clúster de Service Fabric seguro que emplea certificados X.509, necesita, al menos, un certificado de este tipo, que se carga en el Almacén de claves de Azure y se utiliza en el proceso de creación del clúster.

Este artículo corresponde al [paso 3 (Configuración de seguridad)](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) del proceso de creación del clúster. Para obtener más información sobre cómo Service Fabric usa los certificados X.509, consulte [Protección de un clúster de Service Fabric](service-fabric-cluster-security.md).

Hay tres pasos diferentes:

1. Adquirir el certificado X.509.
2. Cargar el certificado en el Almacén de claves de Azure.
3. Proporcionar la ubicación y los detalles del certificado para el proceso de creación del clúster de Service Fabric.

## Paso 1: Adquisición del(los) certificado(s) X.509

Para los clústeres que ejecutan cargas de trabajo de producción, debe usar un certificado X.509 firmado por una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) con el fin de proteger el clúster. Para obtener más información sobre cómo obtener estos certificados, consulte [Cómo obtener un certificado (WCF)](http://msdn.microsoft.com/library/aa702761.aspx).

En los clústeres que se usan solo con fines de prueba, puede usar un certificado autofirmado. En el paso 2.5 a continuación se explica cómo hacerlo.

## Paso 2: Carga del certificado X.509 en el Almacén de claves

Se trata de un proceso complejo, por lo que hemos cargado un módulo de PowerShell en un repositorio Git para que se encargue de ello.

### Paso 2.1
Asegúrese de que la versión 1.0 o posterior de Azure PowerShell esté instalada en la máquina. Si no lo ha hecho antes, siga los pasos que se describen en [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

### Paso 2.2
Copia la carpeta *ServiceFabricRPHelpers* de este [repositorio Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) en el equipo.

### Paso 2.3
Abra una ventana de PowerShell y vaya al directorio donde descargó el módulo. Después, impórtelo mediante el siguiente comando.

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### Paso 2.4
Si está utilizando un certificado que ya ha obtenido, siga el procedimiento que se describe en este paso. De lo contrario, vaya al paso 2.5, que explica cómo crear e implementar el certificado autofirmado en el Almacén de claves.

Puede utilizar un grupo de recursos y almacén de claves que ya tenga para almacenar el certificado, o bien crear un nuevo grupo de recursos o almacén de claves si aún no existen. Si usa un almacén de claves existente, debe configurarse para permitir la implementación mediante el uso de este script.

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Para cargar el certificado en el grupo de recursos y el almacén de claves, ejecute el siguiente script. Si no existen, se crearán el grupo de recursos y el almacén de claves.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Este es un script relleno como ejemplo.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Tras la ejecución correcta del script, obtendrá una salida como la siguiente, que necesitará para el paso 3 (Configuración de un clúster seguro).

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

Ahora tiene la información necesaria para configurar un clúster seguro. Vaya al paso 3.

### Paso 2.5
Si *no* tiene un certificado y desea crear uno nuevo autofirmado y cargarlo en el almacén de claves, siga estos pasos. Los certificados autofirmados solo deben utilizarse con los clústeres de prueba y no con de producto.

Puede utilizar un grupo de recursos y almacén de claves que ya tenga para almacenar el certificado, o bien crear un nuevo grupo de recursos o almacén de claves si aún no existen. Si usa un almacén de claves existente, debe configurarse para permitir la implementación mediante el uso de este script.

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

El siguiente script siguiente creará un nuevo grupo de recursos o almacén de claves si aún no existen, creará un certificado autofirmado, lo cargará en el almacén de claves y generará el nuevo certificado en *OutputPath*.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
La cadena *DnsName* especifica uno o varios nombres DNS para colocar en la extensión de nombre alternativo del sujeto del certificado cuando no se especifica un certificado para copiar mediante el parámetro CloneCert. El primer nombre DNS también se guarda como nombre del sujeto. Si no se especifica ningún certificado de firma, el primer nombre DNS también se guarda como nombre del emisor. El cmdlet *Invoke-AddCertToKeyVault* usa [cmdlet New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633.aspx) para crear el certificado autofirmado.

Este es un script relleno como ejemplo.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Después de realizar correctamente el script, obtendrá una salida como la siguiente. La necesitará para el paso 3.

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## Paso 3: Configuración de un clúster seguro

Una vez cargados los certificados en el Almacén de claves de Azure, puede crear un clúster protegido con dichos certificados. Este paso se corresponde al [paso 3 (configuración de seguridad)](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) del proceso de creación del clúster y muestra cómo establecer las opciones de configuración de seguridad.

>[AZURE.NOTE]
Los certificados necesarios se especifican en el nivel de tipo de nodo, en Configuración de seguridad. Debe especificarlos para cada tipo de nodo que tenga en el clúster. Aunque este documento le guía en el modo de hacerlo mediante el portal, puede hacer lo mismo mediante una plantilla del Administrador de recursos de Azure.

![Captura de pantalla de las configuraciones de seguridad en el Portal de Azure][SecurityConfigurations_01]

### Parámetros obligatorios

- **Modo de seguridad**: seleccione **Certificado X509** para configurar un clúster protegido con certificados X.509.
- **Nivel de protección de clúster**: consulte este [documento sobre niveles de protección](https://msdn.microsoft.com/library/aa347692.aspx) para entender lo que significa cada uno de estos valores. Aunque aquí permitimos tres valores (EncryptAndSign,Sign y None), es mejor mantener el valor predeterminado de EncryptAndSign a menos que sepa lo que hace.
- **Almacén de claves**: hace referencia al identificador de recurso del almacén de claves. Debe tener este formato:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **Dirección URL del certificado**: hace referencia a la dirección URL de la ubicación en el almacén de claves donde se cargó el certificado. Debe tener este formato:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Huella digital del certificado**: hace referencia a la huella digital del certificado que se encuentra en la dirección URL que especificó anteriormente.

### Parámetros opcionales

 Puede especificar certificados adicionales para los equipos cliente que utiliza para realizar operaciones en el clúster. De forma predeterminada, la huella digital especificada en los parámetros obligatorios se agrega a la lista de huellas digitales autorizadas que tienen permiso para realizar operaciones de cliente.

**Cliente de administración**: esta información se utiliza para validar que el cliente que se conecta al punto de conexión de administración del clúster presenta la credencial correcta para realizar acciones administrativas y de solo lectura en el clúster. Puede especificar que desea autorizar más de un certificado para las operaciones de administración.

- **Autorizar por**: indica a Service Fabric si se debe buscar este certificado mediante el nombre del sujeto o la huella digital. El uso del nombre del sujeto para autorizar no es una buena práctica de seguridad, pero agrega flexibilidad.
- **Nombre del sujeto**: solo es necesario si especificó que la autorización es mediante nombre del sujeto.
- **Huella digital del emisor**: proporciona un nivel adicional de comprobación que el servidor puede realizar cuando un cliente presenta sus credenciales al servidor.

**Cliente de solo lectura**: esta información se utiliza para validar que el cliente que se conecta al punto de conexión de administración del clúster presenta la credencial correcta para realizar acciones de solo lectura en el clúster. Puede especificar que desea autorizar más de un certificado para las operaciones de solo lectura.

- **Autorizar por**: indica a Service Fabric si se debe buscar este certificado mediante el nombre del sujeto o la huella digital. El uso del nombre del sujeto para autorizar no es una buena práctica de seguridad, pero agrega flexibilidad.
- **Nombre del sujeto**: solo es necesario si especificó que la autorización es mediante nombre del sujeto.
- **Huella digital del emisor**: proporciona un nivel adicional de comprobación que el servidor puede realizar cuando un cliente presenta sus credenciales al servidor.

## Pasos siguientes
Después de configurar la seguridad de los certificados del clúster, reanude el proceso de creación del clúster descrito en el [paso 4 (finalización de la creación del clúster](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation)).

Cuando el clúster se haya creado con seguridad basada en certificados, más adelante podrá [actualizar un certificado](service-fabric-cluster-security-update-certs-azure.md).


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0608_2016-->