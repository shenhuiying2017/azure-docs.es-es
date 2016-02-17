<properties
   pageTitle="Protección de un clúster de Service Fabric | Microsoft Azure"
   description="Cómo proteger un clúster de Service Fabric ¿Cuáles son las opciones?"
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
   ms.date="02/01/2016"
   ms.author="chackdan"/>

# Protección de un clúster de Service Fabric

Un clúster de Azure Service Fabric es un recurso que usted posee. Para evitar el acceso no autorizado al recurso, debe protegerlo, especialmente cuando se están ejecutando en él cargas de trabajo de producción. Este artículo le guiará por el proceso de proteger un clúster de Service Fabric.

##  Escenarios de seguridad de clúster

Service Fabric proporciona seguridad para los escenarios siguientes:

1. **Seguridad de nodo a nodo:** protege la comunicación entre las máquinas virtuales y los equipos del clúster. De esta forma se garantiza que solo los equipos que están autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster.

	![Diagrama de comunicación de nodo a nodo][Node-to-Node]

2. **Seguridad de cliente a nodo:** protege la comunicación entre un cliente de Service Fabric y los nodos individuales del clúster. Este tipo de seguridad autentica y protege las comunicaciones de cliente, lo que garantiza que solo los usuarios autorizados pueden acceder al clúster y a las aplicaciones implementadas en él. Los clientes se identifican exclusivamente mediante sus credenciales de seguridad de Windows o las credenciales de seguridad del certificado.

	![Diagrama de comunicación de cliente a nodo][Client-to-Node]

	En la seguridad de nodo a nodo o de cliente a nodo, puede usar [certificados de seguridad](https://msdn.microsoft.com/library/ff649801.aspx) o [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx). Las opciones de la seguridad de nodo a nodo o de cliente a nodo son independientes entre sí, y pueden ser iguales o diferentes.

	Azure Service Fabric usa certificados de servidor X509 que se especifican como parte de las configuraciones del tipo de nodo cuando se crea un clúster. Al final de este artículo, se proporciona una descripción rápida de qué son estos certificados y cómo se pueden adquirir o crear.

3. **Control de acceso basado en rol (RBAC):** restringe las operaciones de administración en el clúster a un conjunto determinado de certificados.


## Protección de un clúster de Service Fabric mediante certificados.

Para configurar un clúster de Service Fabric seguro, necesitará al menos un certificado de servidor X509. Después hay que cargarlo carga en el almacén de claves de Azure y usarlo en el proceso de creación de un clúster.

Hay tres pasos diferentes:

1. Adquirir el certificado X.509.
2. Cargar el certificado en el Almacén de claves de Azure.
3. Proporcionar la ubicación y los detalles del certificado para el proceso de creación del clúster de Service Fabric.

### Paso 1: Adquisición del(los) certificado(s) X.509

1. Para los clústeres que ejecutan cargas de trabajo de producción, debe usar un certificado X509 firmado por una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) para proteger el clúster. Para obtener más información sobre cómo obtener estos certificados, visite [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Para los clústeres que se usan solo con fines de prueba, puede usar un certificado autofirmado. 


### Paso 2: Carga del certificado X.509 en el Almacén de claves

Se trata de un proceso complejo, por lo que hemos cargado un módulo de PowerShell en un repositorio Git para que se encargue de ello.

**Paso 2.1**: Copie esta carpeta en la máquina desde este [repositorio Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Paso 2.2**: Asegúrese de que Azure SDK 1.0+ está instalado en su equipo. Si no ha hecho esto antes, sugiero encarecidamente que siga los pasos descritos en [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

**Paso 2.3**: Después de haber hecho lo anterior, abra una ventana de Powershell e importe ServiceFabricRPHelpers.psm, es decir, el módulo descargado en el paso 2.1.

```
Remove-Module ServiceFabricRPHelpers
```

Copie el ejemplo siguiente y cambie la ruta de acceso a .psm1 para que coincida con la ruta de acceso en la máquina.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**Paso 2.4**: Si está utilizando un certificado que ya ha adquirido, siga estos pasos. De lo contrario, vaya al paso 2.5. En el paso 2.5 recorrerá los pasos sobre cómo crear e implementar el certificado autofirmado en el almacén de claves.

Inicie sesión en la cuenta de Azure. Si este código de PowerShell presenta un error por algún motivo, debe comprobar si Azure PowerShell está instalado correctamente en el equipo.

```
Login-AzureRmAccount
```

El siguiente script crea un nuevo grupo de recursos o un almacén si aún no existe.

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Este es un script relleno como ejemplo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

Después de ejecutar el script correctamente, obtendrá un resultado como el siguiente. Tome nota, porque lo necesitará para el paso 3 (Configuración de un clúster seguro).

1. **Huella digital del certificado**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **Almacén de origen**/Id. de recurso en el almacén de claves: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL de certificado**/URL a la ubicación del certificado en el almacén de claves: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

Ahora tiene la información necesaria para configurar un clúster seguro. Vaya al paso 3.

**Paso 2.5**: Si *no* tiene un certificado y desea crear un nuevo certificado autofirmado y cargarlo en el almacén de claves, siga estos pasos.

Inicie sesión en la cuenta de Azure. Si este código de PowerShell presenta un error por algún motivo, debe comprobar si Azure PowerShell está instalado correctamente en el equipo.

```
Login-AzureRmAccount
```

El siguiente script crea un nuevo grupo de recursos o un Almacén de claves si no existen ya.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

El valor de OutputPath que dio al script contendrá el nuevo certificado autofirmado que el script cargó en el almacén de claves.

>[AZURE.NOTE] La cadena DnsName especifica uno o más nombres DNS para colocar en la extensión de nombre alternativo del sujeto del certificado cuando no se especifica un certificado para copiar mediante el parámetro CloneCert. El primer nombre DNS también se guarda como nombre del sujeto. Si no se especifica ningún certificado de firma, el primer nombre DNS también se guarda como nombre del emisor.

Puede leer más sobre la creación de un certificado autofirmado en [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Este es un script relleno como ejemplo.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Puesto que es un certificado autofirmado, debe importarlo al almacén "TrustedPeople" de la máquina, para poder usar este certificado para conectarse a un clúster seguro.

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Después de realizar correctamente el script, obtendrá una salida como la siguiente. La necesitará para el paso 3.

**Huella digital de certificado** : 64881409F4D86498C88EEC3697310C15F8F1540F **SourceVault** /Id. de recurso del almacén de claves: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc **Dirección URL del certificado** /dirección URL a la ubicación del certificado en el almacén de claves: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### Paso 3: Configuración de un clúster seguro

Siga los pasos descritos en [Proceso de creación de un clúster de Service Fabric](service-fabric-cluster-creation-via-portal.md) hasta que llegue a la sección Configuraciones de seguridad. A continuación, vaya a las instrucciones que se muestran aquí para realizar las configuraciones de seguridad:

>[AZURE.NOTE] 
1\. Los certificados que hay que usar se especifican en el nivel NodeType, en las configuraciones de seguridad. 2. Debe especificarlos para cada NodeType que tenga en el clúster. 3. Aunque este documento muestra cómo hacer esto con el portal, puede hacer lo mismo con una plantilla ARM.

![Captura de pantalla de las configuraciones de seguridad en el Portal de Azure][SecurityConfigurations_01]

Parámetros obligatorios:

- **Modo de seguridad** Seleccione **Certificado X509**. Eso indica a Service Fabric que desea configurar un clúster seguro.
- **Nivel de protección de clúster.** Consulte este [documento sobre niveles de protección](https://msdn.microsoft.com/library/aa347692.aspx) para comprender lo que significa cada uno de estos valores. Aunque aquí permitimos tres valores (EncryptAndSign,Sign y None), es mejor mantener el valor predeterminado de EncryptAndSign a menos que sepa lo que hace.
- **Almacén de origen.** Hace referencia al identificador de recurso del Almacén de claves. Debe tener este formato:

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **Dirección URL del certificado.** Hace referencia a la dirección URL de la ubicación en el Almacén de claves donde se cargó el certificado. Debe tener este formato:

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Huella digital de certificado.** Hace referencia a la huella digital del certificado que se encuentra en la dirección URL que especificó anteriormente.

Parámetros opcionales:

 - Puede especificar certificados adicionales para los equipos cliente que utiliza para realizar operaciones en el clúster. De forma predeterminada, la huella digital especificada en los parámetros obligatorios se agrega a la lista de huellas digitales autorizadas que tienen permiso para realizar operaciones de cliente.

Cliente de administración: esta información se utiliza para validar que el cliente que se conecta al punto de conexión de administración del clúster presenta la credencial correcta para realizar acciones administrativas y de solo lectura en el clúster. Puede especificar que desea autorizar más de un certificado para las operaciones de administración.

- **Autorizar por.** Indica a Service Fabric si se debe buscar este certificado mediante el nombre del sujeto o la huella digital. El uso del nombre del sujeto para autorizar no es una buena práctica de seguridad, pero agrega flexibilidad.
- **Nombre del sujeto.** Solo es necesario si ha especificado que la autorización se realiza por el nombre del sujeto.
- **Huella digital del emisor** Proporciona un nivel adicional de comprobación que el servidor puede realizar cuando un cliente presenta sus credenciales al servidor.



Cliente de solo lectura: esta información se utiliza para validar que el cliente que se conecta al punto de conexión de administración del clúster presenta la credencial correcta para realizar acciones de solo lectura en el clúster. Puede especificar que desea autorizar más de un certificado para las operaciones de solo lectura.

- **Autorizar por.** Indica a Service Fabric si se debe buscar este certificado mediante el nombre del sujeto o la huella digital. El uso del nombre del sujeto para autorizar no es una buena práctica de seguridad, pero agrega flexibilidad.
- **Nombre del sujeto.** Solo es necesario si ha especificado que la autorización se realiza por el nombre del sujeto.
- **Huella digital del emisor.** Proporciona un nivel adicional de comprobación que el servidor puede realizar cuando un cliente presenta sus credenciales al servidor.

## Actualización de los certificados en el clúster

Service Fabric permite especificar dos certificados, uno principal y otro secundario. De forma predeterminada, el que especificó durante la creación es el principal. Para agregar otro certificado, necesita implementar ese certificado en las máquinas virtuales del clúster. En el paso 2 anterior de este documento se describe cómo cargar un certificado nuevo en el almacén de claves. Puede usar el mismo almacén de claves, como hizo con el primer certificado. Para más información, vea [Deploy certificates to VMs from a customer-managed Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) (Implementación de certificados en máquinas virtuales desde un Almacén de claves administrado por el cliente).

Una vez finalizada la operación, utilice el portal o el Administrador de recursos para indicar a Service Fabric que tiene un certificado secundario que se puede usar también. Todo lo que necesita es una huella digital.

**Este es el proceso para incorporar un certificado nuevo** para utilizarlo en el clúster: vaya al portal, busque el recurso de clúster al que desea agregar este certificado, haga clic en la configuración del certificado, especifique la huella digital del certificado secundario y presione Guardar. Se iniciará una implementación y, cuando se complete correctamente, podrá usar tanto el certificado principal como el secundario para realizar operaciones de administración en el clúster.

![Captura de pantalla de huellas digitales de certificados en el portal][SecurityConfigurations_02]

**Este es el proceso para quitar los certificados antiguos** para que el clúster no los utilice; para ello, vaya al portal, vaya a la configuración de seguridad de los clústeres y quite uno de los certificados. Asegúrese de presionar Guardar después de quitarlo para que se inicie una nueva implementación. Una vez completada la implementación, el certificado eliminado ya no podrá usarse para conectarse al clúster.

Nota: Para un clúster seguro, siempre necesitará tener implementado al menos un certificado (principal o secundario) válido (no revocado ni expirado); de lo contrario, no podrá tener acceso al clúster.


## 
Detalles sobre los tipos de certificados utilizados por Service Fabric.

## Certificados X.509

Los certificados digitales X509 se usan habitualmente para autenticar a clientes y servidores y para cifrar y firmar mensajes digitalmente. Para más detalles sobre estos certificados, vaya a [Trabajar con certificados](http://msdn.microsoft.com/library/ms731899.aspx) en la biblioteca de MSDN.

>[AZURE.NOTE]
1\. Los certificados usados en clústeres que ejecutan cargas de trabajo de producción deberán crearse mediante un servicio de certificados de Windows Server correctamente configurado u obtenerse mediante una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) autorizada. 2. No use nunca en producción certificados temporales o de pruebas creados con herramientas como MakeCert.exe. 3. En los clústeres que se usan solo con fines de prueba, puede usar un certificado autofirmado.

## Certificados de servidor y certificados de cliente

**Certificados de servidor X.509**

Los certificados de servidor tienen la tarea principal de autenticar un servidor (nodo) en los clientes o de autenticar un servidor (nodo) en un servidor (nodo). Una de las comprobaciones iniciales cuando un nodo o un cliente autentica un nodo consiste en comprobar el valor del nombre común en el campo Sujeto. Este nombre común o uno de los nombres alternativos del sujeto del certificado debe estar presente en la lista de nombres comunes permitidos.

En el siguiente artículo se describe cómo generar certificados con nombres alternativos de sujeto (SAN): [Cómo agregar un nombre alternativo del sujeto a un certificado LDAP seguro](http://support.microsoft.com/kb/931351).

>[AZURE.NOTE] El campo de sujeto puede contener varios valores, cada uno con un prefijo de inicialización para indicar el tipo de valor. Normalmente, la inicialización es "CN" para nombre común, por ejemplo, "CN = www.contoso.com". El campo Sujeto también puede estar en blanco. Si el campo opcional Nombre alternativo de sujeto está relleno, debe contener tanto el nombre común del certificado como una entrada por nombre alternativo de sujeto. Estos se especifican como valores de nombre DNS.

El valor del campo Propósitos planteados del certificado debe incluir un valor apropiado, como "Autenticación de servidor" o "Autenticación de cliente".

**Certificados de cliente**

Los certificados de cliente normalmente no los emite una entidad de certificación de terceros. En su lugar, el almacén Personal de la ubicación del usuario actual contiene normalmente los certificados de cliente colocados ahí por una entidad de certificación raíz, con un propósito planteado de "Autenticación de cliente". El cliente puede usar este tipo de certificado cuando se requiere autenticación mutua.

>[AZURE.NOTE] Todas las operaciones de administración en el clúster de Service Fabric requieren certificados de servidor. No se pueden usar certificados de cliente para la administración.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
- [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](service-fabric-cluster-upgrade.md)
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introducción al modelo de estado de Service Fabric](service-fabric-health-introduction.md)
- [Seguridad de aplicaciones y RunAs](service-fabric-application-runas-security.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!----HONumber=AcomDC_0204_2016-->