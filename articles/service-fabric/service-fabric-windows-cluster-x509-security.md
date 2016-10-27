<properties
   pageTitle="Conexión a un clúster privado seguro | Microsoft Azure"
   description="En este artículo se describe cómo proteger la comunicación en el clúster independiente o privado, así como entre los clientes y el clúster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>


# <a name="secure-a-standalone-cluster-on-windows-using-x.509-certificates"></a>Protección de un clúster independiente en Windows mediante certificados X.509

Este artículo describe cómo proteger la comunicación entre los diversos nodos del clúster de Windows independiente, así cómo autenticar a los clientes que se conectan a él, mediante certificados X.509. Esto garantiza que solo los usuarios autorizados pueden tener acceso al clúster y a las aplicaciones implementadas, y realizar tareas de administración.  La seguridad basada en certificados se debe haber habilitado en el clúster al crearlo.  

Para más información sobre la seguridad de clúster, como la seguridad de nodo a nodo, la seguridad de cliente a nodo y el control de acceso basado en roles, consulte [Escenarios de seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need?"></a>¿Qué certificados necesitará?

Para empezar, [descargue el paquete de clúster independiente](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) en uno de los nodos del clúster. En el paquete descargado, encontrará el archivo **ClusterConfig.X509.MultiMachine.json** . Abra el archivo y revise la sección **security** en la sección **properties**:

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

En esta sección se describen los certificados que necesita para proteger el clúster de Windows independiente. Para habilitar la seguridad basada en certificados, establezca los valores de **ClusterCredentialType** y **ServerCredentialType** en *X509*.

>[AZURE.NOTE] Una [huella digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) es la identidad principal de un certificado. Consulte [Cómo recuperar la huella digital de un certificado](https://msdn.microsoft.com/library/ms734695.aspx) para averiguar la huella digital de los certificados que cree.

En la siguiente tabla se enumeran los certificados que va a necesitar en su instalación del clúster:

|**Valor de CertificateInformation**|**Descripción**|
|-----------------------|--------------------------|
|ClusterCertificate|Este certificado es necesario para proteger la comunicación entre los nodos de un clúster. Puede utilizar dos certificados diferentes, uno principal y otro secundario para la actualización. Establezca la huella digital del certificado principal en la sección **Thumbprint** y la del secundario en la variable**ThumbprintSecondary**.|
|ServerCertificate|Este certificado se presenta al cliente cuando intenta conectarse a este clúster. Para mayor comodidad, puede utilizar el mismo certificado para *ClusterCertificate* y *ServerCertificate*. Puede utilizar dos certificados de servidor diferentes, uno principal y otro secundario para la actualización. Establezca la huella digital del certificado principal en la sección **Thumbprint** y la del secundario en la variable**ThumbprintSecondary**. |
|ClientCertificateThumbprints|Se trata de un conjunto de certificados que desea instalar en los clientes autenticados. Puede tener varios certificados de cliente diferentes instalados en los equipos a los que desea permitir el acceso al clúster. Establece la huella digital de cada certificado en la variable **CertificateThumbprint** . Si establece **IsAdmin** en *True*, el cliente con este certificado instalado puede realizar actividades de administración en el clúster. Si **IsAdmin** es *false*, el cliente con este certificado solo puede realizar las acciones permitidas para los derechos de acceso de usuario, normalmente de solo lectura. Para más información sobre roles, consulte [Control de acceso basado en roles (RBAC)](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Establezca el nombre común del primer certificado de cliente para **CertificateCommonName**. **CertificateIssuerThumbprint** es la huella digital del emisor de este certificado. Consulte [Trabajar con certificados](https://msdn.microsoft.com/library/ms731899.aspx) para más información sobre los nombres comunes y el emisor.|
|HttpApplicationGatewayCertificate|Se trata de un certificado opcional que se puede especificar si desea proteger la puerta de enlace de la aplicación Http. Asegúrese de que reverseProxyEndpointPort está establecido en nodeTypes si usa este certificado.|

Este es un ejemplo de configuración del clúster en el que se han proporcionado los certificados de clúster, servidor y cliente.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x.509-certificates"></a>Adquisición de certificados X.509
Para proteger la comunicación en el clúster, primero deberá obtener certificados X.509 para los nodos del clúster. Además, para limitar la conexión a este clúster a los equipos o usuarios autorizados, debe obtener e instalar certificados para los equipos cliente.

Para los clústeres que ejecutan cargas de trabajo de producción, debe usar un certificado X.509 firmado por una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) con el fin de proteger el clúster. Para más información sobre cómo obtener estos certificados, consulte [Cómo obtener un certificado (WCF)](http://msdn.microsoft.com/library/aa702761.aspx).

En los clústeres que se usan con fines de prueba, puede usar un certificado autofirmado.

## <a name="optional:-create-a-self-signed-certificate"></a>Opcional: Creación de un certificado autofirmado
Una forma de crear un certificado autofirmado que se puede proteger correctamente es usar el script *CertSetup.ps1* de la carpeta del SDK de Service Fabric en el directorio *C:\Archivos de programa\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Edite este archivo y utilícelo para crear un certificado con un nombre adecuado.

Ahora exporte el certificado a un archivo PFX con una contraseña protegida. Primero debe obtener la huella digital del certificado. Ejecute la aplicación certmgr.exe. Vaya a la carpeta **Equipo local\Personal** y busque el certificado recién creado. Haga doble clic en el certificado para abrirlo, seleccione la pestaña *Detalles* y desplácese hacia abajo hasta el campo *Huella digital*. Copie el valor de la huella digital en el comando de PowerShell siguiente, sin espacios.  Cambie el valor *$pswd* por una contraseña segura para protegerlo y ejecute PowerShell:

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Para ver los detalles de un certificado instalado en el equipo, puede ejecutar el siguiente comando de PowerShell:

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Como alternativa, si tiene una suscripción de Azure, consulte la sección [Add certificates to Key Vault](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault) (Agregar certificados a Key Vault).

## <a name="install-the-certificates"></a>Instalación de los certificados
Cuando tenga los certificados, puede instalarlos en los nodos del clúster. Los nodos deben tener instalada la versión más reciente de Windows PowerShell 3.x. Debe repetir estos pasos en cada nodo, para los certificados del clúster y el servidor y cualquier certificado secundario.

1. Copie los archivos .pfx en el nodo.
2. Abra una ventana de PowerShell como administrador y ejecute el siguiente comando. Reemplace *$pswd* por la contraseña que usó para crear este certificado. Reemplace *$PfxFilePath* por la ruta de acceso completa del archivo .pfx copiado en este nodo.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. A continuación, debe establecer el control de acceso de este certificado para que el proceso Service Fabric, que se ejecuta en la cuenta de servicio de red, pueda usarlo al ejecutar el script siguiente. Proporcione la huella digital del certificado y especifique "SERVICIO DE RED" para la cuenta de servicio. Puede comprobar que las ACL del certificado son correctas con la herramienta certmgr.exe y mediante Administrar claves privadas en el certificado.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
Connect-ServiceFabricCluster -ConnectionEndpoint 10.7.0.4:19000 -KeepAliveIntervalInSec 10 -X509Credential -ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 -FindType FindByThumbprint -FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 -StoreLocation CurrentUser -StoreName My
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
Connect-ServiceFabricCluster Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```



<!--HONumber=Oct16_HO2-->


