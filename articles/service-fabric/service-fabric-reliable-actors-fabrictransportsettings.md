---
title: "Descripción general de la configuración de FabricTransport de Azure Service Fabric Reliable Actors | Microsoft Docs"
description: "Obtenga información sobre cómo definir la configuración de comunicación de Azure Service Fabric Actor."
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: a395d0653a72883b1901824ba25d40f673276d3f
ms.openlocfilehash: 0b43ba67ea30772054c513b47c31642baaa8b9e7


---
# <a name="configuring-fabrictransport-settings-for-reliable-actors"></a>Configuración de FabricTransport de Reliable Actors

Aquí está la lista de valores que el usuario puede configurar [FabrictTansportSettings](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)

Puede modificar la configuración predeterminada de FabricTransport de las maneras siguientes.

1.  Usando el atributo de ensamblado [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute)

  Este atributo se debe aplicar en el cliente y al ensamblado de servicio de actor.
  En el ejemplo siguiente se muestra cómo cambiar el valor de la configuración de OperationTimeout FabricTransport de forma predeterminada.

  ```csharp
     using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   En el segundo ejemplo se cambian de forma predeterminada los valores de FabricTransport MaxMessageSize y OperationTimeoutInSeconds.

    ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
    ```

2. Usando un [paquete de configuración](service-fabric-application-model.md):

  * Configuración de FabricTransport para el servicio de actor

    Agregue una sección TransportSettings en el archivo settings.xml.

    * SectionName: De forma predeterminada, el código de actor busca SectionName como "&lt;ActorName&gt;TransportSettings." Si no se encuentra, busca sectionName como "TransportSettings."

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

  * Configuración de FabricTransport para el ensamblado de cliente de actor

    Si el cliente no se está ejecutando como parte de un servicio, puede crear un archivo &lt;client_name.settings.xml&gt; en la misma ubicación donde se encuentra client_name.exe. A continuación, cree una sección TransportSettings en ese archivo. SectionName debe ser "TransportSettings."

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```



<!--HONumber=Nov16_HO4-->


