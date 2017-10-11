---
title: "Administración de secretos en aplicaciones de Service Fabric | Microsoft Docs"
description: "En este artículo se describe cómo proteger los valores de secreto en una aplicación de Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: d71924cda8bb3bffbe221946d80dba150359e38e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="managing-secrets-in-service-fabric-applications"></a>Administración de secretos en aplicaciones de Service Fabric
Esta guía le lleva por los pasos para administrar secretos en una aplicación de Service Fabric. Los secretos pueden ser cualquier información confidencial, como cadenas de conexión de almacenamiento, contraseñas u otros valores que no se deben administrar en texto sin formato.

En este guía se usa el Almacén de claves de Azure para administrar las claves y los secretos. Sin embargo, el *uso* de secretos en una aplicación es independiente de la plataforma de nube para permitir que las aplicaciones se implementen en un clúster hospedado en cualquier parte. 

## <a name="overview"></a>Información general
La forma recomendada de administrar la configuración de servicio es mediante los [paquetes de configuración de servicio][config-package]. Los paquetes de configuración se actualizan mediante actualizaciones acumulativas administradas con validación de estado y reversión automática. Esto es preferible a la configuración global ya que reduce las posibilidades de una interrupción del servicio global. Los secretos cifrados no son ninguna excepción. Service Fabric presenta características integradas para cifrar y descifrar valores en un archivo Settings.xml de paquete de configuración mediante cifrado de certificados.

En el diagrama siguiente se ilustra el flujo básico para la administración de secretos en una aplicación de Service Fabric:

![información general de administración de secretos][overview]

Hay cuatro pasos principales en este flujo:

1. Obtener un certificado de cifrado de datos.
2. Instalar el certificado en el clúster.
3. Cifrar los valores de secreto al implementar una aplicación con el certificado e insértelos en un archivo de configuración Settings.xml del servicio.
4. Leer los valores cifrados de Settings.xml y usar el mismo certificado de cifrado para descifrarlos. 

[Azure Key Vault][key-vault-get-started] se usa aquí como ubicación de almacenamiento seguro para los certificados y como forma de obtener los certificados instalados en clústeres de Service Fabric en Azure. Si no va a implementar en Azure, no es necesario usar el Almacén de claves para administrar secretos en aplicaciones de Service Fabric.

## <a name="data-encipherment-certificate"></a>Certificado de cifrado de datos
Un certificado de cifrado de datos se utiliza estrictamente para el cifrado y el descifrado de los valores de configuración en un archivo Settings.xml del servicio y no se usa para la autenticación o la forma del texto cifrado. El certificado debe cumplir los siguientes requisitos:

* El certificado debe contener una clave privada.
* El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
* El uso de claves de certificado debe incluir el cifrado de datos (10), y no debe incluir la autenticación de servidor o la autenticación de cliente. 
  
  Por ejemplo, al crear un certificado autofirmado mediante PowerShell, la marca `KeyUsage` debe establecerse en `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalación del certificado en el clúster
Este certificado debe instalarse en cada nodo del clúster. Se utilizará en tiempo de ejecución para descifrar los valores almacenados en el archivo Settings.xml de un servicio. Consulte [cómo crear un clúster mediante Azure Resource Manager][service-fabric-cluster-creation-via-arm] para ver las instrucciones de configuración. 

## <a name="encrypt-application-secrets"></a>Cifrado de los secretos de aplicación
El SDK de Service Fabric incorpora funciones de cifrado y descifrado de secretos. Los valores de secreto se pueden cifrar en el momento de la compilación y luego descifrarse y leerse mediante programación en el código de servicio. 

El siguiente comando de PowerShell se usa para cifrar un secreto. Este comando solo cifra el valor; **no** firma el texto cifrado. Para producir texto cifrado para los valores de secreto, debe usar el mismo certificado de cifrado que está instalado en el clúster:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

La cadena de base64 resultante contiene tanto el texto cifrado del secreto como la información sobre el certificado que se usó para cifrarlo.  La cadena codificada en base-64 se puede insertar en un parámetro en el archivo de configuración Settings.xml del servicio con el atributo `IsEncrypted` establecido en `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>Inserción de secretos de aplicación en instancias de aplicación
Lo más conveniente es que la implementación en entornos diferentes sea lo más automatizada posible. Para lograr esto, se puede realizar el cifrado del secreto en un entorno de compilación y proporcionar los secretos cifrados como parámetros al crear instancias de aplicación.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Uso de parámetros reemplazables en Settings.xml
El archivo de configuración Settings.xml permite parámetros reemplazables que se pueden proporcionar en el momento de creación de una aplicación. En lugar de proporcionar un valor para el parámetro, use el atributo `MustOverride` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Para reemplazar los valores en el archivo Settings.xml, declare un parámetro de reemplazo para el servicio en ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Ahora, el valor se puede especificar como un *parámetro de aplicación* al crear una instancia de la aplicación. La creación de una instancia de aplicación se puede generar mediante un script con PowerShell, o escribirse en C#, para facilitar la integración en un proceso de compilación.

Mediante PowerShell, el parámetro se proporciona al comando `New-ServiceFabricApplication` como una [tabla hash](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Con C#, los parámetros de aplicación se especifican en `ApplicationDescription` como `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-secrets-from-service-code"></a>Descifrado de secretos desde el código de servicio
Los servicios de Service Fabric se ejecutan en SERVICIO DE RED de forma predeterminada en Windows y no tienen acceso a los certificados instalados en el nodo sin algún tipo de configuración adicional.

Al usar un certificado de cifrado de datos, deberá asegurarse de que la cuenta de SERVICIO DE RED o la cuenta de usuario con la que se ejecuta el servicio, tengan acceso a la clave privada del certificado. Service Fabric administrará la concesión del acceso para su servicio automáticamente si lo ha configurado para hacerlo así. Esta configuración se puede realizar en ApplicationManifest.xml definiendo usuarios y directivas de seguridad para los certificados. En el ejemplo siguiente, a la cuenta de SERVICIO DE RED se le concede acceso de lectura a un certificado definido por su huella digital:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> Cuando se copia una huella digital de certificado del complemento de almacén de certificados de Windows, se coloca un carácter invisible al comienzo de la cadena de huella digital. Este carácter invisible puede producir un error al intentar encontrar un certificado mediante la huella digital, así que asegúrese de eliminarlo.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>Uso de secretos de aplicación en el código de servicio
La API de acceso a los valores de configuración de Settings.xml en un paquete de configuración permite descifrar fácilmente los valores que tienen el atributo `IsEncrypted` establecido en `true`. Dado que el texto cifrado contiene información sobre el certificado usado para el cifrado, no es necesario buscar manualmente el certificado. Simplemente se debe instalar en el nodo en el que se ejecuta el servicio. Basta con llamar al método `DecryptValue()` para recuperar el valor de secreto original:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la [ejecución de aplicaciones con distintos permisos de seguridad](service-fabric-application-runas-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-model.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
