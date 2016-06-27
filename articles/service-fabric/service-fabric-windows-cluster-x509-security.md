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
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# Protección de un clúster de Windows independiente mediante certificados

Este artículo describe cómo proteger la comunicación entre los diversos nodos del clúster de Windows independiente, así cómo autenticar a los clientes que se conectan a él, mediante certificados X.509. Esto garantiza que solo los usuarios autorizados pueden tener acceso al clúster, y a las aplicaciones implementan, y realizar tareas de administración. La seguridad basada en certificados se debe haber habilitado en el clúster al crearlo. Para más información sobre la seguridad de nodo a nodo, la seguridad de cliente a nodo y el control de acceso basado en roles, consulte [Protección de un clúster de Service Fabric](service-fabric-cluster-security.md).

## ¿Qué certificados necesitará?

Para empezar, [descargue el paquete de clúster independiente](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) a uno de los nodos del clúster. En el paquete descargado, encontrará un archivo **ClusterConfig.X509.json**. Abra el archivo en modo *Editar* y revise la sección **security** en la pestaña **properties**:

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
        }
    },

En esta sección se describen todos los certificados que necesitará para proteger el clúster de Windows independiente. Habilite la seguridad basada en certificados estableciendo los valores de **ClusterCredentialType** y **ServerCredentialType** en *X509*.

>[AZURE.NOTE] Una [huella digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) es la identidad principal de un certificado. Lea [Cómo recuperar la huella digital de un certificado] (https://msdn.microsoft.com/library/ms734695(v=vs.110).aspx) para averiguar la huella digital de los certificados que crea.

En la siguiente tabla se enumeran los certificados reales que va a necesitar en su instalación del clúster:

|**Valor de CertificateInformation**|**Descripción**|
|-----------------------|--------------------------|
|ClusterCertificate|Este certificado es necesario para proteger la comunicación entre los nodos de un clúster. Puede utilizar dos certificados diferentes, uno principal y otro secundario para la conmutación por error. Establece la huella digital del certificado principal en la sección **Thumbprint** y la del secundario en la variable **ThumbprintSecondary**.|
|ServerCertificate|Este certificado se presenta al cliente cuando intenta conectarse a este clúster. Para mayor comodidad, puede utilizar el mismo certificado para *ClusterCertificate* y *ServerCertificate*. Puede utilizar dos certificados de servidor diferentes, uno principal y otro secundario para la conmutación por error. Establece la huella digital del certificado principal en la sección **Thumbprint** y la del secundario en la variable **ThumbprintSecondary**. |
|ClientCertificateThumbprints|Se trata de un conjunto de certificados que desea instalar en los clientes autenticados. Puede tener varios certificados de cliente diferentes instalados en los equipos a los que desea permitir el acceso al clúster y las aplicaciones se ejecutan en ellos. Establece la huella digital de cada certificado en la variable **CertificateThumbprint**. Si establece **IsAdmin** en *true*, el cliente con este certificado instalado puede realizar diversas actividades de administración del clúster. Si **IsAdmin** es *false*, solo puede acceder a las aplicaciones que se ejecutan en el clúster.|
|ClientCertificateCommonNames|Establezca el nombre común del primer certificado de cliente para **CertificateCommonName**. **CertificateIssuerThumbprint** es la huella digital del emisor de este certificado. Lea [Trabajar con certificados] (https://msdn.microsoft.com/library/ms731899(v=vs.110).aspx) para más información acerca de los nombres comunes y el emisor.|


## Instalación de los certificados

Para proteger la comunicación entre el clúster, primero deberá obtener certificados X.509 para los nodos del clúster. Además, para limitar la conexión a este clúster a los equipos o usuarios autorizados, debe obtener e instalar certificados para estos equipos cliente potenciales. Lea [Cómo obtener un certificado (WCF)](https://msdn.microsoft.com/library/aa702761.aspx) para conocer los pasos para obtener los certificados X.509. Debe crear un certificado **.pfx** para poder guardar su clave privada. Como alternativa, si tiene una suscripción de Azure, puede seguir la sección [Adquisición del(los) certificado(s) X.509](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts) para crear los certificados. Una vez que tenga este certificado, puede instalarlo en los nodos del clúster mediante los pasos siguientes. Tenga en cuenta que en los pasos se considera que los nodos ya tienen la versión más reciente de Windows PowerShell 3.x instalada. Debe repetir estos pasos en cada nodo, para los certificados del clúster y el servidor y cualquier certificado secundario para cada uno de ellos.

- Copie el archivo .pfx en el nodo.

- Abra una ventana de PowerShell como administrador y ejecute el siguiente comando:
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

Reemplace *$password* por la contraseña que usó para crear este certificado. Reemplace $PfxFilePath por la ruta de acceso completa del archivo .pfx copiado en este nodo.

- Establezca el control de acceso de este certificado para que el proceso Service Fabric pueda usar al ejecutar el script siguiente:

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

Use estos pasos para instalar también los certificados de cliente en los equipos a los que desea permitir el acceso al clúster.


## Pasos siguientes

Después de configurar la sección **security** del archivo ClusterConfig.X509.json, puede continuar con la sección [Creación del clúster](service-fabric-cluster-creation-for-windows-server.md#createcluster) para configurar los nodos y crear el clúster independiente. Recuerde usar el archivo **ClusterConfig.X509.json** al crear el clúster. Por ejemplo, el comando podría ser similar al siguiente:

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


Una vez que el clúster de Windows independiente se ejecuta correctamente y que ha configurado los clientes autenticados para conectarse a él, siga la sección [Conexión a un clúster seguro con PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) para conectarse a él.

<!---HONumber=AcomDC_0615_2016-->