<properties
   pageTitle="Autenticación del acceso de cliente a un clúster | Microsoft Azure"
   description="Se describe cómo autenticar el acceso de cliente a un clúster de Service Fabric mediante certificados y cómo proteger la comunicación entre los clientes y un clúster."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="ryanwi"/>

# Conexión a un clúster seguro
Cuando un cliente se conecta a un nodo del clúster de Service Fabric, se puede autenticar al cliente y establecer la comunicación segura mediante la seguridad basada en certificados. Esto garantiza que solo los usuarios autorizados pueden tener acceso al clúster, y a las aplicaciones implementan, y realizar tareas de administración. Con anterioridad se debe haber habilitado la seguridad basada en certificados en el clúster cuando se creó este. Para más información sobre escenarios de seguridad de clúster, consulte [Protección de un clúster de Service Fabric](service-fabric-cluster-security.md).

Para proteger la comunicación entre un cliente y un nodo del clúster mediante la seguridad basada en certificados, primero debe obtener e instalar el certificado de cliente en el almacén personal (Mi) en el equipo local o en el almacén Personal del usuario actual.

Ejecute el siguiente cmdlet de PowerShell para configurar el certificado en el equipo que usará para acceder al clúster.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Si es un certificado autofirmado, debe importarlo al almacén "TrustedPeople" de la máquina para poder usar este certificado para conectarse a un clúster seguro.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

<a id="connectsecurecluster"></a>
## Conexión a un clúster seguro con PowerShell

Ejecute el siguiente comando de PowerShell para conectarse a un clúster seguro. Los detalles del certificado deben corresponder a un certificado de los nodos del clúster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Por ejemplo, el comando de PowerShell anterior debe ser similar al siguiente:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
          -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
          -StoreLocation CurrentUser -StoreName My
```

## Conexión a un clúster seguro mediante las API de FabricClient
La siguiente clase [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Los nodos del clúster deben tener certificados válidos cuyo nombre común o nombre DNS de SAN aparezca en la [propiedad RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) establecida en [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Esto permite la autenticación mutua entre el cliente y el nodo del clúster.

```csharp
string thumb = "C179E609BBF0B227844342535142306F3913D6ED";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(thumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string thumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```


## Pasos siguientes

- [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](service-fabric-cluster-upgrade.md)
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introducción al modelo de estado de Service Fabric](service-fabric-health-introduction.md)
- [Seguridad de aplicaciones y RunAs](service-fabric-application-runas-security.md)

<!---HONumber=AcomDC_0615_2016-->