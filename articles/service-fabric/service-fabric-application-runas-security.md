---
title: "Información acerca de las directivas de seguridad de los microservicios de Azure | Microsoft Docs"
description: "Información general sobre cómo ejecutar una aplicación de Service Fabric en cuentas de seguridad locales y del sistema, incluido el punto SetupEntry en el que una aplicación necesita realizar alguna acción con privilegios antes de iniciarse"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: b2ff715d8225bd0a9c7f6108f8804cdfa3189cc8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>Configuración de directivas de seguridad para la aplicación
Azure Service Fabric le permite proteger aplicaciones que se ejecutan en distintas cuentas de usuario en el clúster. Service Fabric también protege los recursos que usan las aplicaciones en el momento de la implementación con la cuenta de usuario; por ejemplo, archivos, directorios y certificados. Esto aumenta la seguridad entre aplicaciones en ejecución, incluso en un entorno hospedado compartido.

De forma predeterminada, las aplicaciones de Service Fabric se ejecutan en la misma cuenta en que se ejecuta el proceso Fabric.exe. Service Fabric también permite ejecutar aplicaciones en una cuenta de usuario local o una cuenta de sistema local especificada en el manifiesto de la aplicación. Los tipos de cuenta de sistema local compatibles son **LocalUser**, **NetworkService**, **LocalService** y **LocalSystem**.

 Cuando ejecute Service Fabric en Windows Server en el centro de datos mediante el instalador independiente, puede usar cuentas de dominio de Active Directory, incluidas cuentas de servicio administradas de grupo.

Se pueden definir y crear grupos de usuarios, de modo que se puedan agregar uno o varios usuarios a cada grupo para poder administrarlos de forma conjunta. Esto es útil cuando hay varios usuarios para distintos puntos de entrada de servicio y es preciso que tengan ciertos privilegios comunes disponibles en el nivel de grupo.

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configuración de la directiva para un punto de entrada del programa de instalación del servicio
Tal como se describe en el [Application and service manifests](service-fabric-application-and-service-manifests.md) (Manifiestos de servicio y aplicación), el punto de entrada del programa de instalación, **SetupEntryPoint**, es un punto de entrada con privilegios que se ejecuta con las mismas credenciales que Service Fabric (normalmente, la cuenta *NetworkService*) antes que cualquier otro punto de entrada. El archivo ejecutable que se especifica con **EntryPoint** suele ser el host de servicio de ejecución prolongada. Por lo que tener un punto de entrada de configuración independiente evita tener que ejecutar el ejecutable del host de servicio con privilegios elevados durante largos períodos de tiempo. El archivo ejecutable especificado por **EntryPoint** se ejecuta después de salir de **SetupEntryPoint** correctamente. El proceso resultante se supervisa y reinicia, comenzando de nuevo con **SetupEntryPoint**si alguna vez finaliza o se bloquea.

A continuación aparece un ejemplo de manifiesto de servicio básico que muestra SetupEntryPoint y el EntryPoint principal del servicio.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>Configuración de la directiva mediante una cuenta local
Tras configurar el servicio para que tenga un punto de entrada de configuración, puede cambiar los permisos de seguridad que ejecuta en el manifiesto de aplicación. En el ejemplo siguiente se muestra cómo configurar el servicio para que se ejecute con privilegios de cuenta de administrador de usuarios.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
```

En primer lugar, cree una sección **Principals** con un nombre de usuario, como SetupAdminUser. Esto indica que el usuario es miembro del grupo de administradores del sistema.

Después, debajo de la sección **ServiceManifestImport**, configure una directiva para aplicar esta entidad de seguridad a **SetupEntryPoint**. Esto indica a Service Fabric que, cuando el archivo **MySetup.bat** se ejecute, debería ser `RunAs` con privilegios de administrador. Dado que *no* ha aplicado una directiva al punto de entrada principal, el código de **MyServiceHost.exe** se ejecuta en la cuenta **NetworkService** del sistema. Esta es la cuenta predeterminada como la que se ejecutan todos los puntos de entrada de servicio.

Ahora agreguemos el archivo MySetup.bat al proyecto de Visual Studio para probar los privilegios de administrador. En Visual Studio, haga clic con el botón derecho en el proyecto de servicio y agregue un nuevo archivo, MySetup.bat.

Después asegúrese de que el archivo MySetup.bat está incluido en el paquete de servicio. De forma predeterminada, no lo está. Seleccione el archivo, haga clic con el botón derecho para ver el menú contextual y elija **Propiedades**. En el cuadro de diálogo de propiedades, asegúrese de que **Copiar en el directorio de salida** está establecido en **Copiar si es posterior**. Vea la siguiente captura de pantalla.

![CopyToOutput de Visual Studio para el archivo por lotes de SetupEntryPoint][image1]

Ahora abra el archivo MySetup.bat y agregue los siguientes comandos:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Seguidamente, compile e implemente la solución en un clúster de desarrollo local. Una vez iniciado el servicio, tal como se muestra en Service Fabric Explorer, hay dos maneras de ver que MySetup.bat se ha ejecutado correctamente. Abra un símbolo del sistema de PowerShell y escriba:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Anote el nombre del nodo donde el servicio se ha implementado e iniciado en Service Fabric Explorer, por ejemplo, Node 2. Después, navegue hasta la carpeta de trabajo de la instancia de aplicación para buscar el archivo out.txt que muestra el valor de **TestVariable**. Por ejemplo, si este servicio se implementó en Node 2, puede ir a esta ruta de acceso para ver el valor de **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configuración de la directiva mediante cuentas de sistema locales
A menudo es preferible ejecutar el script de inicio mediante una cuenta de sistema local en lugar de usar una cuenta de administrador. Normalmente, el hecho de ejecutar la directiva RunAs como miembro del grupo Administradores no funciona bien, ya que las máquinas tienen el Control de acceso de usuario (UAC) habilitado de forma predeterminada. En estos casos, **la recomendación es ejecutar el SetupEntryPoint como LocalSystem y no como un usuario local agregado al grupo de administradores**. En el ejemplo siguiente se muestra cómo establecer SetupEntryPoint para que se ejecute como LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

En los clústeres de Linux, para ejecutar un servicio o el punto de entrada de configuración como **raíz**, puede especificar **AccountType** como **LocalSystem**.

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>Inicio de comandos de PowerShell desde un SetupEntryPoint
Para ejecutar PowerShell desde el punto **SetupEntryPoint**, puede ejecutar **PowerShell.exe** en un archivo por lotes que apunte al archivo de PowerShell. En primer lugar, agregue un archivo de PowerShell al proyecto de servicio, por ejemplo, **MySetup.ps1**. No olvide configurar la propiedad *Copiar si es posterior* para que el archivo se incluya también en el paquete de servicio. En el ejemplo siguiente se muestra un archivo por lotes de ejemplo que inicia un archivo de PowerShell denominado MySetup.ps1, que establece una variable de entorno del sistema denominada **TestVariable**.

MySetup.bat para iniciar un archivo de PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

En el archivo de PowerShell, agregue el siguiente procedimiento para establecer una variable de entorno del sistema.

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> De forma predeterminada, cuando se ejecuta el archivo por lotes, la búsqueda de archivos se realiza en la carpeta de aplicación denominada **work**. En este caso, cuando se ejecuta MySetup.bat queremos que encuentre el archivo MySetup.ps1 en la misma carpeta, que es la carpeta **paquete de código** de la aplicación. Para cambiar esta carpeta, configure la carpeta de trabajo:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="use-console-redirection-for-local-debugging"></a>Uso de la redirección de la consola para la depuración local
En ocasiones, resulta útil ver el resultado de la consola tras ejecutar un script con fines de depuración. Para ello, puede establecer una directiva de redirección de la consola que escriba la salida en un archivo. La salida del archivo se escribe en la carpeta de aplicación denominada **log** en el nodo donde se ha implementado y ejecutado la aplicación. (Vea dónde encontrar esto en el ejemplo anterior).

> [!WARNING]
> No use nunca la directiva de redirección de la consola en una aplicación implementada en producción, ya que esto puede afectar a la capacidad de conmutación por error de la aplicación. *Solo* debe usarla con fines de depuración y desarrollo local.  
> 
> 

En el ejemplo siguiente, se muestra cómo establecer la redirección de la consola con un valor de FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Si cambia el archivo MySetup.ps1 para escribir un comando **Echo**, este se escribirá en el archivo de salida con fines de depuración:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

**Después de haber depurado el script, quite inmediatamente esta directiva de redireccionamiento de consola**.

## <a name="configure-a-policy-for-service-code-packages"></a>Configuración de una directiva para los paquetes de código del servicio
En los pasos anteriores se explicaba cómo aplicar la directiva RunAs a SetupEntryPoint. Ahora se explicará más detalladamente cómo crear diferentes entidades de seguridad que se pueden aplicar como directivas de servicio.

### <a name="create-local-user-groups"></a>Creación de grupos de usuarios locales
Se pueden definir y crear grupos de usuarios que permitan agregar uno o varios usuarios a un grupo. Esto es útil si hay varios usuarios para distintos puntos de entrada de servicio y es preciso que tengan ciertos privilegios comunes disponibles en el nivel de grupo. En el ejemplo siguiente se muestra un grupo local denominado **LocalAdminGroup** con privilegios de administrador. Dos usuarios, Customer1 y Customer2, se convierten en miembros de este grupo local.

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

### <a name="create-local-users"></a>Creación de usuarios locales
Puede crear un usuario local para proteger un servicio dentro de la aplicación. Si se especifica un tipo de cuenta **LocalUser** en la sección de entidades de seguridad del manifiesto de aplicación, Service Fabric crea cuentas de usuario locales en las máquinas donde se implementa la aplicación. De forma predeterminada, los nombres de dichas cuentas no coinciden con los que se especifican en el manifiesto de aplicación (por ejemplo, Customer3 en el ejemplo siguiente). En su lugar, se generan dinámicamente y tienen contraseñas aleatorias.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Si una aplicación requiere que la cuenta de usuario y la contraseña coincidan en todas las máquinas (por ejemplo, para habilitar la autenticación NTLM), el manifiesto de clúster debe establecer NTLMAuthenticationEnabled en true. El manifiesto de clúster también debe especificar un NTLMAuthenticationPasswordSecret que se use para generar la misma contraseña en todos los equipos.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>Asignación de directivas a los paquetes de código de servicio
En la sección **RunAsPolicy** de **ServiceManifestImport**, se especifica la cuenta de la sección de entidades de seguridad que debe usarse para ejecutar un paquete de código. También se asocian los paquetes de código del manifiesto de servicio con las cuentas de usuario de la sección de entidades de seguridad. Puede especificarlo para los puntos de entrada de configuración o principales, o bien puede especificar `All` para que se aplique a ambos. En el ejemplo siguiente se muestra la aplicación de diferentes directivas:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Si no se especifica **EntryPointType** , el valor predeterminado se establece en EntryPointType =”Main”. El uso de **SetupEntryPoint** es especialmente útil si lo que se desea es ejecutar determinadas operaciones de instalación con privilegios elevados en una cuenta de sistema. El código de servicio real puede ejecutarse en una cuenta con menos privilegios.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicación de una directiva predeterminada a todos los paquetes de código de servicio
La sección **DefaultRunAsPolicy** se usa para especificar una cuenta de usuario predeterminada para todos los paquetes de código que no tienen definido ningún parámetro **RunAsPolicy** específico. Si la mayoría de los paquetes de código especificados en el manifiesto de servicio que usa una aplicación deben ejecutarse en el mismo usuario, la aplicación solo puede definir una directiva RunAs predeterminada con dicha cuenta de usuario. En el ejemplo siguiente, se especifica que si un paquete de código no tiene una directiva **RunAsPolicy** especificada, tendrá que ejecutarse en la directiva **MyDefaultAccount** especificada en la sección de entidades de seguridad.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>Uso de un usuario o un grupo de dominios de Active Directory
Para una instancia de Service Fabric que se instale en Windows Server mediante el instalador independiente, puede ejecutar el servicio con las credenciales de una cuenta de grupo o usuario de Active Directory. Esto se aplica a Active Directory local dentro del dominio y no a Azure Active Directory (AAD). Mediante el uso de un grupo o usuario de dominio puede tener acceso a otros recursos del dominio (por ejemplo, recursos compartidos de archivos) para los que se han concedido permisos.

El ejemplo siguiente muestra un usuario de Active Directory denominado *TestUser* con la contraseña de dominio cifrada mediante un certificado llamado *MyCert*. Puede usar el comando de PowerShell `Invoke-ServiceFabricEncryptText` para crear el texto cifrado secreto. Vea [Administración de secretos en aplicaciones de Service Fabric](service-fabric-application-secret-management.md) | Microsoft Azure.

La clave privada del certificado para descifrar la contraseña se debe implementar en la máquina local con un método fuera de banda (en Azure esto se realiza mediante Azure Resource Manager). Posteriormente, cuando Service Fabric implemente el paquete de servicio en la máquina, podrá descifrar el secreto y, junto con el nombre de usuario, autenticarse mediante Active Directory para ejecutarse con esas credenciales.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>Use una cuenta de servicio administrada de grupo.
En el caso de una instancia de Service Fabric instalada en Windows Server mediante el instalador independiente, puede ejecutar el servicio como una cuenta de servicio administrada de grupo (gMSA). Tenga en cuenta que esto se aplica a Active Directory local dentro del dominio y no para Azure Active Directory (AAD). Con una gMSA no hay ninguna contraseña ni contraseña cifrada almacenada en el `Application Manifest`.

En el ejemplo siguiente se muestra cómo crear una cuenta gMSA denominada *svc-Test$*, cómo implementar esa cuenta de servicio administrada en los nodos del clúster y cómo configurar la entidad de seguridad de usuario.

##### <a name="prerequisites"></a>Requisitos previos.
- El dominio necesita una clave raíz KDS.
- El dominio debe estar en un nivel funcional de Windows Server 2012 o superior.

##### <a name="example"></a>Ejemplo
1. Haga que un administrador de dominio de Active Directory cree una cuenta de servicio administrada de grupo mediante el commandlet `New-ADServiceAccount` y asegúrese de que `PrincipalsAllowedToRetrieveManagedPassword` incluya todos los nodos del clúster de Service Fabric. Tenga en cuenta que `AccountName`, `DnsHostName` y `ServicePrincipalName` deben ser únicos.
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. En cada uno de los nodos del clúster de Service Fabric (por ejemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale y pruebe la gMSA.
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. Configure la entidad de seguridad de usuario y RunAsPolicy para que hagan referencia al usuario.
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Asignación de una directiva de acceso de seguridad a los puntos de conexión HTTP y HTTPS
Si se aplica una directiva RunAs a un servicio y el manifiesto de servicio declara que hay recursos de puntos de conexión con el protocolo HTTP, es preciso especificar una directiva **SecurityAccessPolicy** para asegurarse de que los puertos asignados a dichos puntos de conexión aparezcan correctamente en la lista de control de acceso de la cuenta de usuario RunAs en la que se ejecuta el servicio. En caso contrario, **http.sys** no tendrá acceso al servicio y aparecerán errores en las llamadas del cliente. En el ejemplo siguiente se aplica la cuenta Customer1 a un punto de conexión denominado **EndpointName**, que le concede derechos de acceso total.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

En el caso del punto de conexión HTTPS, también es preciso indicar el nombre del certificado que se va a devolver al cliente. Para ello, puede utilizar **EndpointBindingPolicy**, con el certificado definido en una sección de certificados del manifiesto de aplicación.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Actualización de varias aplicaciones con puntos de conexión https
Debe tener cuidado de no usar el **mismo puerto** para distintas instancias de la misma aplicación cuando use http**s**. El motivo es que Service Fabric no podrá actualizar el certificado para una de las instancias de la aplicación. Por ejemplo, si la aplicación 1 o 2 desean actualizar sus certificados 1 a 2. Una vez realizada la actualización, es posible que Service Fabric borre el registro del certificado 1 con http.sys, aunque la otra aplicación lo siga utilizando. Para evitar esto, Service Fabric detecta que hay ya otra instancia de la aplicación registrada en el puerto con el certificado (debido a http.sys) y se produce un error en la operación.

Por lo tanto, Service Fabric no admite la actualización de dos servicios diferentes que usen **el mismo puerto** en diferentes instancias de la aplicación. En otras palabras, no puede utilizar el mismo certificado en diferentes servicios en el mismo puerto. Si necesita tener un certificado compartido en el mismo puerto, tiene que asegurarse de que los servicios se encuentren en distintos equipos con restricciones de posición. También puede considerar la posibilidad de utilizar puertos dinámicos de Service Fabric para cada servicio en cada instancia de la aplicación. 

Si visualiza un error de actualización con https, aparecerá una advertencia de error que indica que "La API de servidor HTTP de Windows no admite varios certificados para las aplicaciones que comparten un puerto".

## <a name="a-complete-application-manifest-example"></a>Ejemplo completo de un manifiesto de aplicación
El siguiente manifiesto de aplicación muestra muchos de los diferentes valores:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
