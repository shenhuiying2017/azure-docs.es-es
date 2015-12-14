<properties
   pageTitle="Descripción de las directivas de seguridad RunAs de la aplicación Service Fabric | Microsoft Azure"
   description="Información general sobre cómo ejecutar una aplicación Service Fabric en un sistema y con cuentas de seguridad locales que incluye el punto SetupEntry en el que una aplicación necesita realizar alguna acción con privilegios antes de iniciarse"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/24/2015"
   ms.author="mfussell"/>

# RunAs: ejecución de una aplicación de Service Fabric con diferentes permisos de seguridad
Service Fabric proporciona la capacidad, conocida como "RunAs", para proteger las aplicaciones que se ejecutan en el clúster en distintas cuentas de usuario. También protege los recursos usados por las aplicaciones con la cuenta de usuario como archivos, directorios y certificados.

De forma predeterminada, las aplicaciones de Service Fabric se ejecutan bajo la cuenta que está ejecutando el proceso Fabric.exe. También proporciona la capacidad para ejecutar aplicaciones en una cuenta de usuario local especificada en el manifiesto de la aplicación. Los tipos de cuenta compatibles con RunAs son **LocalUser**, **NetworkService**, **LocalService** y **LocalSystem**.

> [AZURE.NOTE]Las cuentas de dominio son compatibles con las implementaciones de Windows Server en las que Active Directory está disponible.

Se pueden definir y crear grupos de usuarios agregando uno o más usuarios para poder administrarlos de forma conjunta. Esto es especialmente útil si hay varios usuarios para distintos puntos de entrada de servicio y deben tener ciertos privilegios comunes disponibles en el nivel de grupo.

## Configuración de la directiva RunAs para SetupEntryPoint

Como se describe en el [modelo de aplicación](service-fabric-application-model.md), **SetupEntryPoint** es un punto de entrada con privilegios que se ejecuta con las mismas credenciales que Service Fabric (normalmente, la cuenta *Network*) antes que cualquier otro punto de entrada. El archivo ejecutable especificado por **EntryPoint** suele ser el host de servicios de ejecución prolongada, por lo que tener un punto de entrada de configuración independiente evita tener que ejecutar el host de servicios con privilegios elevados durante largos períodos de tiempo. El archivo ejecutable especificado por **EntryPoint** se ejecuta después de salir **SetupEntryPoint** correctamente. El proceso resultante se supervisa y reinicia (comenzando de nuevo con **SetupEntryPoint**) si alguna vez finaliza o se bloquea.

A continuación, aparece un manifiesto de servicio que muestra el SetupEntryPoint y el EntryPoint principal del servicio.

~~~
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
~~~

### Configuración de la directiva de RunAs

Cuando haya configurado el servicio para que tenga un SetupEntryPoint, puede cambiar los permisos de seguridad que ejecuta en el manifiesto de aplicación. En el ejemplo siguiente se muestra cómo configurar el servicio para ejecutarse con privilegios de cuenta de administrador.

~~~
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
~~~

Cree primero una sección **Principals** con un nombre de usuario como, por ejemplo, SetupAdminUser. Esto indica que el usuario es miembro del grupo de administradores del sistema.

Justo debajo de la sección **ServiceManifestImport**, configure una directiva para aplicar esta entidad de seguridad al **SetupEntryPoint**. Esto indica a Service Fabric que, cuando se ejecute el archivo MySetup.bat, en realidad es RunAs con privilegios de administrador. Si *no* ha aplicado una directiva al punto de entrada principal, el código de MyServiceHost.exe se ejecutará en la cuenta NetworkService, que es la cuenta predeterminada en la que todos los puntos de entrada de servicio se ejecutan como RunAs.

Ahora agreguemos el archivo MySetup.bat al proyecto de Visual Studio para probar los privilegios de administrador. En Visual Studio, haga clic con el botón derecho en el proyecto de servicio y agregue un nuevo archivo llamado MySetup.bat. A continuación, es necesario asegurarse de que este archivo se incluye en el paquete de servicio, ya que esto no está especificado de forma predeterminada. Para asegurarse de que el archivo MySetup.bat se incluye en el paquete, seleccione el archivo, haga clic con el botón derecho para que aparezca el menú contextual, elija Propiedades y en el cuadro de diálogo correspondiente asegúrese de que la opción **Copiar en el directorio de salida** está establecida como **Copiar si es posterior**. Esto se muestra en la captura de pantalla siguiente.

![CopyToOutput de Visual Studio para el archivo por lotes de SetupEntryPoint][image1]

Ahora abra el archivo MySetup.bat y agregue los siguientes comandos.

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

A continuación, compile e implemente la solución en un clúster de desarrollo local. Una vez iniciado el servicio, tal como se muestra en el explorador de Service Fabric, puede ver que la ejecución de MySetup.bat fue correcta de dos maneras. Apertura de un símbolo del sistema de PowerShell y escritura

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

En segundo lugar, anote el nombre del nodo en el que el servicio se implementó e inició en el explorador de Service Fabric, por ejemplo, Nodo 1 y, a continuación, navegue hasta la carpeta de trabajo de la instancia de aplicación para buscar el archivo out.txt que muestra el valor de **TestVariable**. Por ejemplo, si se implementó en el nodo 2, puede ir a esta ruta de acceso para ver el valor de MyApplicationType

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  Inicio de comandos de PowerShell desde SetupEntryPoint
Para poder ejecutar PowerShell desde el punto **SetupEntryPoint** puede ejecutar PowerShell.exe en un archivo por lotes que apunte a un archivo de PowerShell. En primer lugar, agregue un archivo de PowerShell para el proyecto de servicio, por ejemplo, MySetup.ps1. Recuerde que debe establecer las propiedades como *Copiar si es posterior* para que este archivo también se incluya en el paquete de servicio. El ejemplo siguiente muestra un archivo por lotes de ejemplo para iniciar un archivo de PowerShell denominado MySetup.ps1 que establece una variable de entorno de sistema denominada *TestVariable*.

MySetup.bat para iniciar el archivo de PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

En el archivo de PowerShell, agregue el siguiente procedimiento para establecer una variable de entorno del sistema.

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

## Aplicación de RunAsPolicy a servicios
Anteriormente vio cómo aplicar la directiva RunAs a un SetupEntryPoint. Vamos a ir algo más allá para averiguar cómo crear diferentes entidades de seguridad que se puedan aplicar como directivas de servicio.

### Creación de grupos de usuarios locales
Se pueden definir y crear grupos de usuarios agregando uno o más usuarios a dicho grupo. Esto es especialmente útil si hay varios usuarios para distintos puntos de entrada de servicio y deben tener ciertos privilegios comunes disponibles en el nivel de grupo. El ejemplo siguiente muestra un grupo local denominado **LocalAdminGroup** con privilegios de administrador. Dos usuarios, Customer1 y Customer2 se convierten en miembros de este grupo local.

~~~
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
~~~

### Creación de usuarios locales
Puede crear un usuario local para proteger un servicio dentro de la aplicación. Cuando se especifica un tipo de cuenta LocalUser en la sección de entidades de seguridad del manifiesto de aplicación, Service Fabric crea cuentas de usuario locales en los equipos en los que se implementa la aplicación. Estas cuentas no tienen, de forma predeterminada, el mismo nombre que el especificado en la aplicación de manifiesto (por ejemplo "Customer3" en el ejemplo siguiente) pero, en su lugar, se generan dinámicamente y tienen contraseñas aleatorias.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (e.g. to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true and also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Asignación de directivas a los paquetes de código de servicio
La sección **RunAsPolicy** para **ServiceManifestImport** especifica la cuenta de la sección de entidades de seguridad que se debe usar para ejecutar un paquete de código y asocia paquetes de código desde el manifiesto de servicio con cuentas de usuario en la sección de entidades de seguridad. Puede especificarlo para los puntos de entrada Setup o Main o seleccionar Todos para aplicar esto a ambos. En el ejemplo siguiente se muestra la aplicación de diferentes directivas.

~~~
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Si no se especifica **EntryPointType**, el valor predeterminado se establece en EntryPointType =”Main”. Especificar un **SetupEntryPoint** es especialmente útil si desea ejecutar determinadas operaciones de configuración de privilegios elevados en una cuenta de sistema, mientras que el código de servicio real se puede ejecutar bajo una cuenta con menos privilegios.

### Aplicación de una directiva predeterminada para todos los paquetes de código de servicio
La sección **DefaultRunAsPolicy** se usa para especificar una cuenta de usuario predeterminada para todos los paquetes de código que no tienen **RunAsPolicy** específico definido. Si la mayoría de los paquetes código especificados en los manifiestos de servicio usados por una aplicación necesitan ejecutarse en el mismo usuario RunAs, la aplicación solo puede definir una directiva RunAs predeterminada con esa cuenta de usuario en lugar de especificar una directiva **RunAsPolicy** para cada paquete de código. Por ejemplo, en el ejemplo siguiente se muestra que si un paquete de código no tiene la directiva **RunAsPolicy** especificada, este deberá ejecutarse en la directiva MyDefaultAccount especificada en la sección de entidades de seguridad.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Asignación de la directiva SecurityAccessPolicy para los puntos de conexión http y https
Si se aplica una directiva RunAs a un servicio y el manifiesto de servicio declara recursos de puntos de conexión con el protocolo http, debe especificar una directiva **SecurityAccessPolicy** para asegurarse de que los puertos asignados a estos puntos de conexión aparecen correctamente en la ACL para la cuenta de usuario RunAs en la que se ejecuta el servicio. En caso contrario, http.sys no tendrá acceso al servicio y obtendrá error con las llamadas desde el cliente. En el ejemplo siguiente, la cuenta Customer3 se aplica a un punto de conexión denominado *ServiceEndpointName* asignándole así derechos de acceso completos.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Para puntos de conexión https debe indicar además el nombre del certificado para devolver al cliente con una directiva **EndpointBindingPolicy** en la que el certificado se define en la sección correspondiente del manifiesto de la aplicación.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Ejemplo completo de un manifiesto de aplicación
El siguiente manifiesto de aplicación muestra muchas de las diferentes configuraciones descritas anteriormente.

~~~
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
      <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_1203_2015-->