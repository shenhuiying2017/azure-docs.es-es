<properties
   pageTitle="Integración continua para Service Fabric | Microsoft Azure"
   description="Obtenga información general sobre cómo configurar la integración continua para una aplicación de Service Fabric mediante Visual Studio Team Services (VSTS)."
   services="service-fabric"
   documentationCenter="na"
   authors="mthalman-msft"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/29/2016"
   ms.author="mthalman" />

# Configuración de la integración continua de una aplicación de Service Fabric mediante Visual Studio Team Services

En este artículo se describen los pasos para configurar la integración continua para una aplicación de Azure Service Fabric mediante Visual Studio Team Services (VSTS), a fin de asegurarse de que la aplicación se pueda compilar, empaquetar e implementar de una manera automática. Tenga en cuenta que en estas instrucciones se vuelve a crear el clúster desde cero cada vez.

Este documento refleja el procedimiento actual y se espera que cambie con el tiempo.

## Requisitos previos

Para comenzar, configure el proyecto en Visual Studio Team Services:

1. Si aún no lo ha hecho, cree una cuenta de Team Services mediante su [cuenta de Microsoft](http://www.microsoft.com/account).

2. Cree un nuevo proyecto en Team Services con la cuenta de Microsoft.

3. Inserte el código fuente de la aplicación de Service Fabric nueva o existente en este proyecto.

Si desea obtener más información sobre cómo trabajar con proyectos de Team Services, consulte [Conexión a Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

## Creación de una entidad de servicio

### Configuración de la autenticación para la automatización

Antes de poder configurar la máquina de compilación, deberá crear una [entidad de servicio](../resource-group-create-service-principal-portal.md) que el agente de compilación usará para autenticarse en Azure. También tendrá que crear un certificado y cargarlo en un Almacén de claves de Azure, ya que el Almacén de claves no admite la autenticación de entidad de servicio. Puede realizar estos pasos desde cualquier equipo. El equipo de desarrollo es una buena opción.

### Instalación de Azure PowerShell e inicio de sesión

1.  Instale PowerShellGet.

    a. Si ejecuta Windows 10 con las actualizaciones más recientes, puede omitir este paso (PowerShellGet ya está instalado).

    b. De lo contrario, instale [Windows Management Framework 5.0](https://aka.ms/wmf5download), que incluye PowerShellGet.

2.	Instale y actualice el módulo AzureRM. Si tiene instalada una versión anterior de Azure PowerShell, quítela:

    a. Haga clic con el botón derecho en Inicio y, luego, seleccione **Agregar o quitar programas**.

    b. Busque "Azure PowerShell" y desinstálelo.

    c. Abra un símbolo del sistema de PowerShell como administrador.

    d. Instale el módulo AzureRM mediante el comando `Install-Module AzureRM`.

3.	Deshabilite o habilite la recopilación de datos de Azure.

    Los cmdlets de Azure le solicitarán que incluya o excluya la recopilación de datos hasta que realice una elección. Estos mensajes bloquearán la automatización mientras se espera la entrada del usuario. Para suprimirlos, realice una elección con antelación mediante la ejecución de los siguientes comandos:

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Inicie sesión en Azure PowerShell:

    a. Ejecute el comando `Login-AzureRmAccount`.

    b. En el cuadro de diálogo que aparece, escriba sus credenciales de Azure.

    c. Ejecute el comando `Get-AzureRmSubscription`.

    d. Busque la suscripción que desea usar.

    e. Ejecute el comando `Select-AzureRmSubscription -SubscriptionId <ID for your subscription>`.

### Creación de una entidad de servicio

1. Siga [estas instrucciones](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para crear una entidad de servicio y el punto de conexión de servicio para el proyecto.

2. Tenga en cuenta los valores que se imprimen al final de la salida del script. Los necesitará para configurar la definición de compilación.

### Creación de un certificado y su carga en un nuevo Almacén de claves de Azure

>[AZURE.NOTE] Este script de ejemplo genera un certificado autofirmado, lo que no es una práctica segura y solo es aceptable para la experimentación. Siga las directrices de su organización para obtener un certificado legítimo en su lugar. Estas instrucciones también usan un solo certificado tanto para el servidor como para el cliente. En producción, debe utilizar los certificados de cliente y servidor independientes.

1. Descargue y extraiga [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) en una carpeta de esta máquina.

2. En un símbolo del sistema de administrador de PowerShell, cambie al directorio `<extracted zip>/Manual`.

3. Ejecute el script `CreateAndUpload-Certificate.ps1` de PowerShell con los parámetros siguientes:

| Parámetro | Valor |
| --- | --- |
| KeyVaultLocation | Cualquier valor. Este parámetro debe coincidir con la ubicación en la que planea crear el clúster. |
| CertificateSecretName | Cualquier valor. |
| CertificateDnsName | Debe coincidir con el nombre DNS del clúster. Ejemplo: `mycluster.westus.cloudapp.azure.com` |
| SecureCertificatePassword | Cualquier valor. Este parámetro se usa al importar el certificado en la máquina de compilación. |
| KeyVaultName | Cualquier valor. |
| KeyVaultResourceGroupName | Cualquier valor. Sin embargo, no use el nombre del grupo de recursos que tiene previsto emplear para el clúster. |
| PfxFileOutputPath| Cualquier valor. Este archivo se usa para importar el certificado en la máquina de compilación. |

Cuando finaliza el script, genera los tres valores siguientes. Anote estos valores, ya que se usan como variables de compilación.

 - `ServiceFabricCertificateThumbprint`
 - `ServiceFabricKeyVaultId`
 - `ServiceFabricCertificateSecretId`

## Configuración de la máquina de compilación

### Instalación de Visual Studio 2015

Si ya ha aprovisionado una máquina (o tiene pensado proporcionar la suya propia), instale [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) en la máquina seleccionada.

Si aún no tiene una máquina, puede aprovisionar rápidamente una máquina virtual (VM) de Azure con Visual Studio 2015 preinstalado. Para ello, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Seleccione el comando **Nuevo** en la esquina superior izquierda de la pantalla.

3. Seleccione **Marketplace**.

4. Busque **Visual Studio 2015**.

5. Seleccione **Proceso** > **Máquina virtual** > **De la galería**.

6. Seleccione la imagen **Visual Studio Enterprise 2015 Update 2 con herramientas universales de Windows y Azure SDK 2.9 en Windows Server 2012 R2**.

    >[AZURE.NOTE] El SDK de Azure no es un componente obligatorio, pero actualmente no hay ninguna imagen disponible que solo tenga instalado Visual Studio 2015.

7.	Siga las instrucciones que aparecen en el cuadro de diálogo para crear la máquina virtual.

### Instalación del SDK de Service Fabric

Instale el [SDK de Service Fabric](service-fabric-get-started.md#install-the-runtime-sdk-and-tools) en la máquina.

### Azure PowerShell

Para instalar Azure PowerShell, siga los pasos en que aparecen en la sección anterior, "Instalación de Azure PowerShell e inicio de sesión". Omita el paso "Inicio de sesión en Azure PowerShell".

### Registre los módulos de Azure PowerShell con la cuenta de servicio de red.

>[AZURE.NOTE] Hágalo *antes* de iniciar el agente de compilación. De lo contrario, no seleccionará la nueva variable de entorno.

1. Presione la tecla del logotipo de Windows + R, escriba **regedit** y presione Entrar.

2. Haga clic con el botón derecho en el nodo `HKEY_Users\.Default\Environment` y luego seleccione **Nuevo** > **Valor de cadena expandible**.

3. Escriba `PSModulePath` para el nombre y `%PROGRAMFILES%\WindowsPowerShell\Modules` para el valor. Reemplace `%PROGRAMFILES%` por el valor de la variable de entorno `PROGRAMFILES`.

### Importación del certificado de automatización

1.	Importe el certificado en la máquina de compilación. Para ello, siga estos pasos:

    a. Copie el archivo PFX creado mediante el script CreateAndUpload Certificate.ps1 en la máquina de compilación.

    b. Abra un símbolo del sistema de PowerShell como administrador y ejecute los siguientes comandos, con la contraseña que transmitió anteriormente a `CreateAndUpload-Certificate.ps1`.

    ```powershell
    $password = Read-Host -AsSecureString
    Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
    ```

2.	Ejecute el Administrador de certificados:

    a. Abra el Panel de control en Windows. Haga clic con el botón derecho en Inicio y luego seleccione **Panel de control**.

    b. Busque **Certificado**.

    c. Seleccione **Herramientas administrativas** > **Administrar certificados de equipo**.

3.	Conceda permiso a la cuenta de servicio de red para usar su certificado de automatización:

    a. En **Certificados - Equipo Local**, expanda **Personal** y luego seleccione **Certificados**.

    b. Busque su certificado en la lista.

    c. Haga clic con el botón derecho en el certificado y luego seleccione **Todas las tareas** > **Administrar claves privadas**.

    d. Seleccione el botón **Agregar**, escriba **Servicio de red** y luego seleccione **Comprobar nombres**.

    e. Seleccione **Aceptar**.

    ![Captura de pantalla de los pasos para otorgar permiso a la cuenta de servicio local](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

4.  Copie el certificado a la carpeta `Trusted People`.

    a. El certificado se importó a **Personal/Certificados**, pero necesitamos agregarlo a **Personas de confianza**. Haga clic con el botón derecho en el certificado y seleccione **Copiar**. A continuación, haga clic con el botón derecho en la carpeta **Personas de confianza** y seleccione **Pegar**.

### Registro del agente de compilación

1.	Descargue agent.zip. Para ello, siga estos pasos:

    a. Inicie sesión en el proyecto del equipo, por ejemplo, ****https://[your-VSTS-account-name].visualstudio.com**.

    b. Seleccione el icono de engranaje que se encuentra en la esquina superior derecha de la pantalla.

    c. Seleccione la pestaña **Grupos de agentes**.

    d. Seleccione **Descargar agente** para descargar el archivo agent.zip.

    >[AZURE.NOTE] Si la descarga no se inicia, compruebe el bloqueador de elementos emergentes.

    e. Copie agent.zip en la máquina de compilación que creó anteriormente.

    f. Descomprima agent.zip en `C:\agent` (o en cualquier ubicación con una ruta de acceso corta) en la máquina de compilación.

    >[AZURE.NOTE] Si tiene pensado usar servicios web de ASP.NET 5, se recomienda elegir el nombre más corto posible para esta carpeta a fin de evitar que aparezcan errores **PathTooLongExceptions** durante la implementación. Cuando se libera el núcleo de ASP.NET, mitigará este problema.

2.	En un símbolo del sistema de administrador, ejecute `C:\agent\ConfigureAgent.cmd`. El script le pide que especifique los parámetros siguientes:

|Parámetro|Valor|
|---|---|
|Nombre del agente|Acepte el valor predeterminado, `Agent-[machine name]`.|
|Dirección URL de TFS|Escriba la dirección URL del proyecto de equipo, por ejemplo, `https://[your-VSTS-account-name].visualstudio.com`.|
|Grupo de agentes|Escriba el nombre del grupo de agentes. (Si no ha creado un grupo de agentes, acepte el valor predeterminado).|
|Carpeta de trabajo|Acepte el valor predeterminado. Esta es la carpeta donde el agente de compilación compilará realmente la aplicación. Si tiene pensado usar servicios web de ASP.NET 5, se recomienda elegir el nombre más corto posible para esta carpeta a fin de evitar que aparezcan errores PathTooLongExceptions durante la implementación.|
|¿Se instala como servicio de Windows?|El valor predeterminado es N. Cambie el valor a **Y**.|
|Cuenta de usuario para ejecutar el servicio|El valor predeterminado es `NT AUTHORITY\LOCAL SERVICE`. Cambie el valor predeterminado a `NT AUTHORITY\NetworkService`.|
|Contraseña para `NT AUTHORITY\Network Service`|La cuenta de servicio de red no tiene contraseña, pero rechazará las contraseñas en blanco. Escriba cualquier cadena no vacía para la contraseña (todo lo que escriba se omitirá).|
|¿Quitar la configuración de un agente existente?|Acepte el valor predeterminado, **N**.|

3.  Cuando se le soliciten credenciales, escriba las de la cuenta de Microsoft que tenga derechos sobre el proyecto de equipo.

4.  Compruebe que el agente de compilación se registró y configure sus funcionalidades. Para ello, siga estos pasos:

    a. Vuelva al explorador web, (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`), y actualice la página.

    b. Seleccione el grupo de agentes que seleccionó al ejecutar anteriormente ConfigureAgent.ps1.

    c. Compruebe que el agente de compilación aparece en la lista y que tiene el estado resaltado en verde. Si está resaltado en rojo, el agente de compilación está teniendo problemas para conectarse a Team Services.

    ![Una captura de pantalla que muestra el estado del agente de compilación](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)

    d. Seleccione el agente de compilación y luego seleccione la pestaña **Funcionalidades **.

    e. Agregue una funcionalidad llamada **azureps** con cualquier valor. Esto indica a VSTS que esta máquina tiene instalado Azure PowerShell, que es necesario para usar algunas de las tareas de compilación proporcionadas por VSTS.


## Creación de la definición de compilación

>[AZURE.NOTE] La definición de compilación que se crea a partir de estas instrucciones no admite múltiples compilaciones simultáneas, incluso en máquinas distintas. Esto se debe a que cada compilación competiría por el mismo clúster o grupo de recursos. Si desea ejecutar varios agentes de compilación, deberá modificar las siguientes instrucciones o scripts para impedir esta interferencia.

### Adición de una plantilla de Service Fabric Azure Resource Manager a la aplicación

1. Descargue `azuredeploy.json` y `azuredeploy.parameters.json` de [este ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

2. Abra `azuredeploy.parameters.json` y edite los parámetros siguientes:

    |Parámetro|Valor|
    |---|---|
    |clusterLocation|Debe coincidir con la ubicación del almacén de claves. Ejemplo: `westus`|
    |clusterName|Debe coincidir con el nombre DNS del certificado. Por ejemplo, si el nombre DNS del certificado es `mycluster.westus.cloudapp.net`, `clusterName` debe ser `mycluster`.|
    |adminPassword|Entre 8 y 123 caracteres, con al menos 3 de los siguientes tipos de caracteres: mayúsculas, minúsculas, numérico y especial.|
    |certificateThumbprint|Desde la salida de `CreateAndUpload-Certificate.ps1`|
    |sourceVaultValue|Desde la salida de `CreateAndUpload-Certificate.ps1`|
    |certificateUrlValue|Desde la salida de `CreateAndUpload-Certificate.ps1`|

3. Agregue los nuevos archivos al control de origen y realice la inserción en VSTS.

>[AZURE.NOTE] Si utiliza un certificado diferente para administrar el clúster de Service Fabric, repita los pasos descritos en Importación del certificado de automatización, con dicho certificado.

### Creación de la definición de compilación.

1.	Cree una definición de compilación vacía. Para ello, siga estos pasos:

    a. Abra el proyecto en Visual Studio Team Services.

    b. Seleccione la pestaña **Compilar**.

    c. Seleccione el signo **+** verde para crear una nueva definición de compilación.

    d. Seleccione **Vacía** y luego seleccione **Siguiente**.

    e. Compruebe que se seleccionan el repositorio y la rama derechos.

    f. Marque la casilla **Integración continua** para garantizar que esta compilación se desencadena cada vez que se actualiza la rama.

    g. Seleccione la cola del agente en que se registra el agente de compilación.

2.	En la pestaña **Variables**, cree las siguientes variables con estos valores.

    |Variable|Valor|Secret|Permitir durante el tiempo en cola|
    |---|---|---|---|
    |BuildConfiguration|Lanzamiento||X|
    |BuildPlatform|x64||||

3.  Guarde la definición de compilación y asígnele un nombre. Puede cambiar este nombre más adelante si lo desea.

### Incorporación de un paso "Restaurar paquetes NuGet"

1. En la pestaña **Compilar**, elija el comando **Agregar paso de compilación...**.

2. Elija **Paquete** > **Instalador de NuGet**

3. Elija el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Restaurar paquetes NuGet**.

4. Elija el botón **…** junto al campo **Solución** y luego elija su archivo .sln.

5. Guarde la definición de compilación.

### Adición de un paso "Compilar"

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Compilar** > **MSBuild**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y, luego, cambie su nombre por **Compilar**.

4. Seleccione estos valores:

    |Nombre de configuración|Valor|
    |---|---|
    |Solución|Haga clic en el botón **...** y seleccione el archivo `.sln` para la solución.|
    |Plataforma|`$(BuildPlatform)`|
    |Configuración|`$(BuildConfiguration)`|

5.	Guarde la definición de compilación.

### Adición de un paso "Empaquetar"

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Compilar** > **MSBuild**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y, luego, cambie su nombre por **Empaquetar**.

4. Seleccione estos valores:

    |Nombre de configuración|Valor|
    |---|---|
    |Solución|Haga clic en el botón **...** y seleccione el archivo `.sfproj` del proyecto de la aplicación.|
    |Plataforma|`$(BuildPlatform)`|
    |Configuración|`$(BuildConfiguration)`|
    |Argumentos de MSBuild|`/t:Package`|

5.	Guarde la definición de compilación.

### <a name="RemoveClusterResourceGroup"></a> Adición de un paso "Quitar grupo de recursos del clúster"

Si una compilación anterior no se limpió después (por ejemplo, si se canceló antes de que se pudiera limpiar), puede haber un grupo de recursos existente que causa conflicto con el nuevo. Para evitar conflictos, limpie cualquier grupo de recursos sobrante (y sus recursos asociados) antes de crear uno nuevo.

>[AZURE.NOTE] Omita este paso si desea crear y reutilizar el mismo clúster para cada compilación.

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Implementar** > **Implementación de un grupo de recursos de Azure**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Quitar grupo de recursos del clúster**.

4. Seleccione estos valores:

    |Nombre de configuración|Valor|
    |---|---|
    |AzureConnectionType|**Administrador de recursos de Azure**|
    |Suscripción de Azure RM|Seleccione el punto de conexión que creó en la sección **Creación de una entidad de servicio**.|
    |Acción|**Eliminación de un grupo de recursos**|
    |El grupos de recursos|Escriba cualquier nombre sin usar. Debe usar el mismo nombre en el paso siguiente.|
    |Continuar en caso de error|Este paso generará un error si el grupo de recursos no existe. Habilite **Continuar en caso de error** en la sección **Opciones de control** para evitar esta situación.|

5.	Guarde la definición de compilación.

### Adición de un paso "Aprovisionamiento de un clúster seguro"

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Implementar** > **Implementación de un grupo de recursos de Azure**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y después cambie su nombre por **Aprovisionamiento de un clúster seguro**.

4. Seleccione estos valores:

    |Nombre de configuración|Valor|
    |---|---|
    |AzureConnectionType|**Administrador de recursos de Azure**|
    |Suscripción de Azure RM|Seleccione el punto de conexión que creó en la sección **Creación de una entidad de servicio**.|
    |Acción|**Creación o actualización del grupo de recursos**|
    |El grupos de recursos|Debe coincidir con el nombre que usó en el paso anterior.|
    |Ubicación|Debe coincidir con la ubicación del almacén de claves.|
    |Plantilla|Haga clic en el botón **…** y seleccione `azuredeploy.json`.|
    |Parámetros de plantilla|Haga clic en el botón **…** y seleccione `azuredeploy.parameters.json`|

5.	Guarde la definición de compilación.

### Agregue un paso de "Implementación"

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Utilidad** > **PowerShell**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Implementar**.

4. Seleccione estos valores (reemplace los valores de PublishProfile y ApplicationPackagePath por las rutas de acceso reales):

    |Nombre de configuración|Valor|
    |---|---|
    |Tipo|**Ruta de acceso de archivo**|
    |Nombre de archivo de script|Haga clic en el botón **...** y vaya hasta el directorio **Scripts** dentro del proyecto de aplicación. Seleccione `Deploy-FabricApplication.ps1`.|
    |Argumentos|`-PublishProfileFile path/to/MySolution/MyApplicationProject/PublishProfiles/MyPublishProfile.xml -ApplicationPackagePath path/to/MySolution/MyApplicationProject/pkg/$(BuildConfiguration)`|

>[AZURE.NOTE] Una manera fácil de crear un archivo xml de perfil de publicación de trabajo es crearlo en Visual Studio, como se muestra aquí: https://azure.microsoft.com/es-ES/documentation/articles/service-fabric-publish-app-remote-cluster

>[AZURE.NOTE] Si desea admitir la implementación de la aplicación en un clúster sobrescribiendo la aplicación existente en lugar de actualizarla, agregue este argumento de Powershell: '-OverwriteBehavior SameAppTypeAndVersion'. Además, asegúrese de que el perfil de publicación seleccionado no está configurado para permitir una actualización. Esto quitará primero cualquier ApplicationType existente antes de instalar la versión más reciente.

5.	Guarde la definición de compilación.

### Adición de un paso de "comprobación"

1. Este paso es opcional la primera vez que se configura esta definición de compilación. No obstante, después de ejecutar satisfactoriamente una compilación y garantizar la exactitud de los demás pasos de compilación, puede insertar aquí su propio paso de comprobación. Esto sería específico de la aplicación, con el propósito de comprobar la exactitud de la aplicación implementada en el clúster.
  
### Adición de un paso final de "limpieza"

1. Siga las mismas instrucciones que en el [paso Adición de un paso "Quitar grupo de recursos del clúster"](#RemoveClusterResourceGroup). De esta forma se limpiarán todos los recursos de Azure aprovisionados durante la compilación.

### Pruébelo

Seleccione **Poner compilación en cola** para iniciar una compilación. También se desencadenarán compilaciones tras la inserción o protección.

## Soluciones alternativas

En las instrucciones anteriores se crea un nuevo clúster para cada compilación y se quita al final de esta. Si, por el contrario, prefiere que cada compilación realice una actualización de la aplicación (a un clúster existente), siga estos pasos:

1.	Cree manualmente un clúster de prueba mediante el Portal de Azure o Azure PowerShell siguiendo [estas instrucciones](service-fabric-cluster-creation-via-portal.md).

2.	Configure el perfil de publicación para admitir la actualización de la aplicación siguiendo [estas instrucciones](service-fabric-visualstudio-configure-upgrade.md).

4.	Quite los pasos de compilación **Quitar grupo de recursos del clúster** y **Aprovisionamiento del clúster** de la definición de compilación.

## Pasos siguientes

Para obtener más información sobre la integración continua con las aplicaciones de Service Fabric, lea los artículos siguientes:

 - [Documentación de compilación: página principal](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [Implementación de un agente de compilación](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [Creación y configuración de una definición de compilación](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0615_2016-->