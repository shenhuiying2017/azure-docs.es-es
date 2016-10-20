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
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# Conexión a un clúster seguro sin AAD
Cuando un cliente se conecta a un nodo del clúster de Service Fabric, se puede autenticar al cliente y establecer la comunicación segura mediante la seguridad basada en certificados. Esta autenticación garantiza que solo los usuarios autorizados pueden tener acceso al clúster, y a las aplicaciones implementadas, y realizar tareas de administración. Con anterioridad se debe haber habilitado la seguridad basada en certificados en el clúster cuando se creó este. Deben utilizarse, al menos, dos certificados para proteger el clúster, uno para el certificado de servidor y clúster, y otro para el acceso de cliente. Se recomienda utilizar también certificados secundarios adicionales y certificados de acceso de cliente. Para más información sobre escenarios de seguridad de clúster, consulte [Protección de un clúster de Service Fabric](service-fabric-cluster-security.md).

Para proteger la comunicación entre un cliente y un nodo del clúster mediante la seguridad basada en certificados, primero debe obtener e instalar el certificado de cliente. El certificado se puede instalar en el almacén personal (Mi) del equipo local o en el almacén personal del usuario actual. También necesitará la huella digital del certificado de servidor para que el cliente pueda autenticar el clúster.


Ejecute el siguiente cmdlet de PowerShell para configurar el certificado de cliente en el equipo que usará para acceder al clúster.

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
<a id="connectsecureclustercli"></a>
## Conexión a un clúster seguro mediante la CLI de Azure sin AAD

Los siguientes comandos de la CLI de Azure describen cómo conectarse a un clúster seguro. Los detalles del certificado deben corresponder a un certificado de los nodos del clúster.
 
Si el certificado tiene entidades de certificación (CA), debe agregar el parámetro `--ca-cert-path` como en el ejemplo siguiente:

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si tiene varias entidades de certificación, use una coma como delimitador.

 
Si el nombre común del certificado no coincide con el punto de conexión de la conexión, puede usar el parámetro `--strict-ssl-false` para omitir la comprobación.

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```
 
Si desea omitir la comprobación de la entidad de certificación, puede agregar el parámetro ``--reject-unauthorized-false`` como se muestra en el siguiente comando:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```
 
Para conectarse a un clúster protegido con un certificado autofirmado, utilice el siguiente comando de eliminación de la comprobación de la entidad de certificación y la comprobación de nombre común:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

Después de conectarse, debería poder ejecutar otros comandos de la CLI para interactuar con el clúster.

<a id="connectsecurecluster"></a>
## Conexión a un clúster seguro mediante PowerShell sin AAD

Ejecute el siguiente comando de PowerShell para conectarse a un clúster seguro. Los detalles del certificado deben corresponder a un certificado de los nodos del clúster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* es la huella digital del certificado de servidor instalado en los nodos del clúster. *FindValue* es la huella digital del certificado de cliente de administración. Cuando los parámetros están rellenos, el comando es parecido al del ejemplo siguiente:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```




## Conexión a un clúster seguro mediante las API de FabricClient

Para más información sobre las API de FabricClient, consulte [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Los nodos del clúster deben tener certificados válidos cuyo nombre común o nombre DNS de SAN aparezca en la [propiedad RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) establecida en [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). La realización de este proceso permite la autenticación mutua entre el cliente y los nodos del clúster.

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
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

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
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

<!---HONumber=AcomDC_0928_2016-->