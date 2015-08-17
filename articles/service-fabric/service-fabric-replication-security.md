<properties
   pageTitle="Proteger el tráfico de replicación de servicios con estado en Azure Service Fabric"
   description="Cuando se habilita la replicación, servicios con estado replican su estado desde una réplica principal a réplicas secundarias y este tipo de tráfico debe protegerse frente a la interceptación y la alteración."
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# Proteger el tráfico de replicación

Cuando se habilita la replicación, los servicios con estado replican el estado en réplicas. Esta página trata acerca de cómo configurar la protección en dicho tráfico.

Hay dos tipos de configuración de seguridad compatibles:

- X 509: usa el certificado X509 para proteger el canal de comunicación. Se espera a los usuarios en claves privadas de certificado ACL para permitir que procesos de host de actor/servicio puedan utilizar las credenciales de certificado.
- Windows: utiliza la seguridad de windows (requiere Active Directory) para proteger el canal de comunicación.

En la sección siguiente se muestra cómo configurar los 2 tipos de configuración 2 anteriores. La configuración "CredentialType" determina el tipo que se está usando.

## CredentialType=X509

### Nombres de configuración

|Nombre|Comentarios|
|----|-------|
|StoreLocation|Ubicación de la tienda para encontrar el certificado: CurrentUser o LocalMachine|
|StoreName|Nombre de tienda en la que buscar el certificado|
|FindType|Identifica el tipo del valor proporcionado por el parámetro FindValue: FindBySubjectName o FindByThumbPrint|
|FindValue|Destino de búsqueda para buscar el certificado|
|AllowedCommonNames|Lista separada por comas de nombres comunes de certificados/nombres de dns utilizados por replicadores.|
|IssuerThumbprints|Lista separada por comas de huellas digitales del certificado del emisor. Cuando se especifica, se valida el certificado entrante si es emitido por una de las entradas de la lista. Siempre se realiza la validación de la cadena.|
|RemoteCertThumbprints|Lista separada por comas de huellas digitales de certificado utilizadas por replicadores.|
|ProtectionLevel|Indica cómo se protegen los datos: Sign, EncryptAndSign o None|

## CredentialType = Windows

### Nombres de configuración

|Nombre|Comentarios|
|----|-------|
|ServicePrincipalName|Nombre de entidad de servicio registrado para el servicio. Puede estar vacío si los procesos de host de servicio/actor se ejecutan como una cuenta de equipo (por ejemplo: NetworkService, LocalSystem, etc.)|
|WindowsIdentities|Lista de identidades de windows de todos los procesos de host de servicio/actor separada por comas.
|ProtectionLevel|Indica cómo se protegen los datos: Sign, EncryptAndSign o None|

## Muestras

### Muestra 1: CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### Muestra 2: CredentialType=Windows
Este fragmento de código muestra un ejemplo de cuando todos los procesos de host de servicio/actor se ejecutan como NetworkService o LocalSystem. ServicePrincipalName está vacío.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### Muestra 3: CredentialType=Windows
Este fragmento de código muestra un ejemplo cuando todos los procesos de host de servicio/actor se ejecutan como una cuenta de servicio administrada por grupo con un ServicePrincipalName válido.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=August15_HO6-->