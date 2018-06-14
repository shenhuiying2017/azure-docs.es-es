---
title: Ejecución de un script cuando se inicia un servicio de Azure Service Fabric | Microsoft Docs
description: Aprenda a configurar una directiva para un punto de entrada del programa de instalación del servicio de Service Fabric y a ejecutar un script durante el inicio del servicio.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3fe22d8bb52fa5f45ce5f1cdc7b860d1ce295a71
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210501"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Ejecución de un script de inicio de servicio como cuenta de usuario local o del sistema
Antes de que se inicie el ejecutable de un servicio de Service Fabric, puede ser necesario llevar a cabo algún trabajo de configuración o instalación.  Por ejemplo, configurar las variables de entorno. Antes de que se inicie el ejecutable del servicio, puede especificar que se ejecute un script en el manifiesto del servicio. Mediante la configuración de una directiva RunAs para el punto de entrada del programa de instalación del servicio, puede cambiar la cuenta con la que se ejecuta el ejecutable de instalación.  Un punto de entrada del programa de instalación independiente le permite ejecutar una configuración con privilegios elevados durante un corto período de tiempo, de forma que no hay necesidad de ejecutar el ejecutable del host de servicios con privilegios elevados durante períodos de tiempo prolongados.

El punto de entrada del programa de instalación (**SetupEntryPoint** en el [manifiesto del servicio](service-fabric-application-and-service-manifests.md)) es un punto de entrada con privilegios que, de forma predeterminada, se ejecuta con las mismas credenciales que Service Fabric (normalmente la cuenta *NetworkService*) antes de cualquier otro punto de entrada. El archivo ejecutable que se especifica con **EntryPoint** suele ser el host de servicio de ejecución prolongada. El ejecutable **EntryPoint** se ejecuta después de que el ejecutable **SetupEntryPoint** se cierre correctamente. El proceso resultante se supervisa y reinicia, comenzando de nuevo con **SetupEntryPoint**si alguna vez finaliza o se bloquea. 

## <a name="configure-the-service-setup-entry-point"></a>Configuración del punto de entrada de instalación del servicio
El siguiente es un ejemplo de manifiesto de servicio simple para un servicio sin estado que especifica un script de instalación *MySetup.bat* en el servicio **SetupEntryPoint**.  **Arguments** se usa para pasar argumentos al script cuando se ejecuta.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configuración de la directiva para un punto de entrada del programa de instalación del servicio
De forma predeterminada, el ejecutable de punto de entrada del programa de instalación del servicio se ejecuta con las mismas credenciales que Service Fabric (normalmente la cuenta *NetworkService*).  En el manifiesto de aplicación, puede cambiar los permisos de seguridad para ejecutar el script de inicio con una cuenta del sistema local o una cuenta de administrador.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Configuración de la directiva mediante una cuenta del sistema local
El siguiente ejemplo de manifiesto de aplicación muestra cómo configurar el punto de entrada del programa de instalación del servicio para ejecutarse con la cuenta de administrador de usuarios (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
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

En primer lugar, cree una sección **Principals** con un nombre de usuario, como SetupAdminUser. La cuenta de usuario SetupAdminUser es un miembro del grupo de administradores del sistema.

Después, debajo de la sección **ServiceManifestImport**, configure una directiva para aplicar esta entidad de seguridad a **SetupEntryPoint**. Esta directiva indica a Service Fabric que cuando se ejecuta el archivo **MySetup.bat**, debería hacerlo como SetupAdminUser (con privilegios de administrador). Dado que *no* ha aplicado una directiva al punto de entrada principal, el código de **MyServiceHost.exe** se ejecuta con la cuenta **NetworkService** del sistema. Esta es la cuenta predeterminada como la que se ejecutan todos los puntos de entrada de servicio.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configuración de la directiva mediante cuentas de sistema locales
A menudo es preferible ejecutar el script de inicio mediante una cuenta del sistema local en lugar de usar una cuenta de administrador. Normalmente, el hecho de ejecutar la directiva RunAs como miembro del grupo Administradores no funciona bien, ya que los equipos tienen el Control de acceso de usuario (UAC) habilitado de forma predeterminada. En estos casos, la recomendación es ejecutar SetupEntryPoint como LocalSystem y no como un usuario local agregado al grupo Administradores. En el ejemplo siguiente se muestra cómo establecer SetupEntryPoint para que se ejecute como LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> En los clústeres de Linux, para ejecutar un servicio o el punto de entrada de configuración como **raíz**, puede especificar **AccountType** como **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Ejecución de un script desde el punto de entrada del programa de instalación
Ahora puede agregar un script de inicio al proyecto para ejecutarse con privilegios de administrador. 

En Visual Studio, haga clic con el botón derecho en el proyecto de servicio y agregue un nuevo archivo, *MySetup.bat*.

Después, asegúrese de que el archivo *MySetup.bat* está incluido en el paquete de servicio. De forma predeterminada, no lo está. Seleccione el archivo, haga clic con el botón derecho para ver el menú contextual y elija **Propiedades**. En el cuadro de diálogo de propiedades, asegúrese de que **Copiar en el directorio de salida** está establecido en **Copiar si es posterior**. Vea la siguiente captura de pantalla.

![CopyToOutput de Visual Studio para el archivo por lotes de SetupEntryPoint][image1]

Ahora, edite el archivo *MySetup.bat*, agregue una variable de entorno del sistema al siguiente conjunto de comandos y genere un archivo de texto:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Seguidamente, compile e implemente la solución en un clúster de desarrollo local. Una vez iniciado el servicio, tal como se muestra en [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), hay dos maneras de ver que MySetup.bat se ha ejecutado correctamente. Abra un símbolo del sistema de PowerShell y escriba:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Anote el nombre del nodo donde el servicio se ha implementado e iniciado en [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Por ejemplo, Nodo 2. Después, navegue hasta la carpeta de trabajo de la instancia de aplicación para buscar el archivo out.txt que muestra el valor de **TestVariable**. Por ejemplo, si este servicio se implementó en Node 2, puede ir a esta ruta de acceso para ver el valor de **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Ejecución de comandos de PowerShell desde un punto de entrada del programa de instalación
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Depuración de un script de inicio localmente mediante el redireccionamiento de consola
En ocasiones, resulta útil ver la salida de la consola tras ejecutar un script de instalación con fines de depuración. Puede establecer una directiva de redireccionamiento de consola en el punto de entrada del programa de instalación del manifiesto de servicio, que escribe la salida en un archivo. La salida del archivo se escribe en la carpeta de aplicación denominada **log** en el nodo del clúster donde se ha implementado y ejecutado la aplicación. 

> [!WARNING]
> No use nunca la directiva de redirección de la consola en una aplicación implementada en producción, ya que esto puede afectar a la capacidad de conmutación por error de la aplicación. *Solo* debe usarla con fines de depuración y desarrollo local.  
> 
> 

En el manifiesto de servicio siguiente, se muestra cómo establecer el redireccionamiento de consola con un valor de FileRetentionCount:

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

> [!WARNING]
> Después de haber depurado el script, quite inmediatamente esta directiva de redireccionamiento de consola.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre la seguridad de servicios y aplicaciones](service-fabric-application-and-service-security.md)
* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
