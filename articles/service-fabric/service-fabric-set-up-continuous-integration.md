<properties
   pageTitle="Integración continua para Service Fabric | Microsoft Azure"
   description="Obtenga información general sobre cómo configurar la integración continua para una aplicación de Service Fabric mediante Visual Studio Online (VSO)"
   services="service-fabric"
   documentationCenter="na"
   authors="cawa"
   manager="paulyuk"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/16/2015"
   ms.author="cawa" />

# Configuración de la integración continua de una aplicación de Service Fabric mediante Visual Studio Online (VSO)

En este artículo se le guiará en el proceso de configurar la integración continua (CI) para una aplicación de Service Fabric mediante Visual Studio Online (VSO) para que la aplicación se pueda compilar, empaquetar e implementar de una manera automática. Tenga en cuenta que en este documento se refleja la experiencia actual y se prevé que cambie a medida que progrese el desarrollo. Además, en estas instrucciones se vuelve a crear el clúster desde cero cada vez.

## Requisitos previos

Para comenzar, configure el proyecto en Visual Studio Online.

1. Si aún no lo ha hecho, cree una cuenta de VSO mediante su [cuenta Microsoft](http://www.microsoft.com/account).
2. Cree un nuevo proyecto en VSO con la cuenta Microsoft.
3. Inserte el código fuente de la aplicación de Service Fabric nueva o existente en este proyecto.

Consulte [Conexión a Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) para obtener más información sobre cómo trabajar con proyectos de VSO.

## Pasos de configuración

### Configuración de la autenticación para la automatización

Antes de poder configurar la máquina de compilación, debe crear una "entidad de servicio" que usa la automatización para autenticarse en Azure. También deberá crear un certificado y cargarlo en un almacén de claves, dado que el Almacén de claves de Azure no admite la autenticación de entidades de servicio. Puede realizar estos pasos desde cualquier equipo. El equipo de desarrollo es una buena opción.

### Instalación de Azure PowerShell e inicio de sesión

1.	Instale Azure PowerShell.
    - Instale PowerShellGet. Para ello, instale [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), que incluye PowerShellGet.
    >[AZURE.NOTE]Si ejecuta Windows 10 con las actualizaciones más recientes, puede omitir este paso.

1.	Instale y actualice el módulo AzureRM.
    1.  Si tiene instalada una versión anterior de Azure PowerShell, quítela. Haga clic con el botón derecho en Inicio y seleccione "Agregar o quitar programas". Busque "Azure PowerShell" y desinstálelo.
    1.  Abra un símbolo del sistema de PowerShell.
    1.	Instale el módulo "AzureRM" mediante el comando `Install-Module AzureRM`.
    1.	Actualice el módulo "AzureRM" mediante el comando `Update-AzureRM`.
1.	Deshabilite o habilite la recopilación de datos de Azure.

    Los cmdlets de Azure le solicitarán que incluya o excluya la recopilación de datos hasta que realice una elección. Estos mensajes bloquearán la automatización mientras se espera la entrada del usuario. Para suprimirlos, realice una elección con antelación mediante uno de los siguientes comandos:

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

1.	Inicie sesión en Azure PowerShell.
    1. Inicie un símbolo del sistema de administrador de PowerShell y ejecute el comando `Login-AzureRMAccount`.
    1. En el cuadro de diálogo que aparece, escriba sus credenciales de Azure.
    1. Ejecute el comando `Get-AzureRmSubscription`.
    1. Busque la suscripción que desea usar y luego ejecute el comando `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`. "

### Creación de una entidad de servicio

1.	Extraiga [ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773) a una carpeta de esta máquina.
1.	En un símbolo del sistema de administrador de PowerShell, cambie al directorio en el que extrajo ServiceFabricCIManualScripts.zip.
1.	Elija una contraseña para la entidad de servicio con el comando siguiente. Recuerde esta contraseña, ya que se usará como variable de compilación.

    ```
    $password = Read-Host -AsSecureString
    ```
1.	Ejecute el script de PowerShell Create-ServicePrincipal.ps1 con los parámetros siguientes:

    |Parámetro|Valor|
    |---|---|
    |ServicePrincipalDisplayName|Cualquier nombre.|
    |ServicePrincipalHomePage|Cualquier URI. No tiene que existir realmente.|
    |ServicePrincipalIdentifierUri|Cualquier URI único. No tiene que existir realmente.|
    |ServicePrincipalSecurePassword|$password|

    Cuando finaliza el script, genera los tres valores siguientes. Anote los valores, ya que se usan como variables de compilación.

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### Creación de un certificado y su carga en un nuevo Almacén de claves de Azure

1.	En un símbolo del sistema de administrador de PowerShell, cambie al directorio en el que extrajo ServiceFabricCIManualScripts.zip.
1.	Ejecute el script de PowerShell CreateAndUpload-Certificate.ps1 con los parámetros siguientes:

    |Parámetro|Valor|
    |---|---|
    |ServiceFabricKeyVaultLocation|Cualquier valor. Debe coincidir con la ubicación en la que planea crear el clúster.|
    |ServiceFabricCertificateSecretName|Cualquier valor.|
    |ServiceFabricSecureCertificatePassword|Cualquier valor. Se usa al importar el certificado en la máquina de compilación.|
    |ServiceFabricKeyVaultResourceGroupName|Cualquier valor. Sin embargo, no use el nombre del grupo de recursos que tiene previsto emplear para el clúster.|
    |ServiceFabricKeyVaultName|Cualquier valor.|
    |ServiceFabricPfxFileOutputPath|Cualquier valor. Este archivo se usa para importar el certificado en la máquina de compilación.|

    Cuando finaliza el script, genera los tres valores siguientes. Anote estos valores, ya que se usan como variables de compilación.

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## Configuración de la máquina de compilación

Tenga en cuenta que la definición de compilación que se crea a partir de estas instrucciones no admite compilaciones en paralelo, incluso en máquinas distintas. (Esto se debe a que cada compilación competiría por el mismo clúster o grupo de recursos). Si desea ejecutar varios agentes de compilación, deberá modificar las siguientes instrucciones o scripts para impedir esta interferencia.

### Instalación de Visual Studio 2015

1.	Si ya ha aprovisionado una máquina (o tiene pensado proporcionar la suya propia), instale [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) en esa máquina.
2.	Si aún no tiene una máquina, puede aprovisionar rápidamente una máquina virtual (VM) de Azure con Visual Studio 2015 preinstalado. Para ello, siga estos pasos:
    1.	Inicie sesión en el [Portal de administración de Azure](http://portal.azure.com).
    1.	Elija el comando **Nuevo** en la esquina superior izquierda de la pantalla.
    1.	Elija **Marketplace**.
    1.	Busque **Visual Studio 2015**.
    1.	Elija **Proceso** > **Máquina virtual** > **Desde la galería**.
    1.	Elija la imagen **Visual Studio Enterprise 2015 con Azure SDK 2.7 en Windows Server 2012 R2**.

        >[AZURE.NOTE]El SDK de Azure no es un componente necesario, pero no hay ninguna imagen disponible que solo tenga instalado Visual Studio 2015.

    1.	Siga las instrucciones que aparecen en el cuadro de diálogo para crear la máquina virtual. (Se recomienda que elija una máquina virtual de la serie D para el mejor rendimiento del disco y la CPU).

### Instalación del SDK de Service Fabric

Instale el [SDK de Service Fabric](https://azure.microsoft.com/campaigns/service-fabric/).

### Registro del repositorio de NuGet del SDK de Service Fabric

1.	Cree el directorio `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` en la máquina de compilación (si aún no existe).
2.	Si un archivo denominado NuGet.config ya existe en este directorio, ábralo y agregue el siguiente elemento como elemento secundario de `<packageSources>`.

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	Si NuGet.config no existe, créelo con el siguiente contenido. Reemplace `<path to service fabric SDK>` por la ruta de acceso al SDK de Service Fabric en la máquina de compilación.
>[AZURE.NOTE]De forma predeterminada, `<path to service fabric SDK>` es `%ProgramFiles%\Microsoft SDKs\Service Fabric`.

    NuGet.config:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### Azure PowerShell

Para instalar Azure PowerShell, siga los pasos descritos en la sección anterior **Instalación de Azure PowerShell e inicio de sesión**. Omita la subsección **Inicio de sesión en Azure PowerShell**.

### Registre los módulos de Azure PowerShell con la cuenta de servicio local.

1. Presione Win + R y luego escriba **regedit** y presione ENTRAR.
2. Haga clic con el botón derecho en el nodo `HKEY_Users\.Default\Environment` y seleccione **Nuevo > Valor de cadena expandible**.
3. Escriba `PSModulePath` para el nombre y `%PROGRAMFILES%\WindowsPowerShell\Modules` para el valor.

>[AZURE.NOTE]Haga esto *antes* de iniciar el agente de compilación, de lo contrario no se seleccionará la nueva variable de entorno.

### Importación del certificado de Automatización

1.	Importe el certificado en la máquina de compilación. Para ello, siga estos pasos:
    1. Copie el archivo PFX creado mediante el script CreateAndUpload Certificate.ps1 en la máquina de compilación.
    1. Abra una cuenta de administrador de PowerShell y emita los siguientes comandos, con la contraseña que se pasó anteriormente a GenerateCertificate.ps1.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

1.	Ejecute el Administrador de certificados.
    1. Abra el panel de control de Windows. Haga clic con el botón derecho en Inicio y elija **Panel de Control**.
    1. Busque **Certificado**.
    1. Elija **Herramientas administrativas** > **Administrar certificados de equipo**.


1.	Conceda permiso a la cuenta de servicio local para usar su certificado de Automatización.
    1.	En **Certificados - Equipo Local**, expanda **Personal** y elija **Certificados**.
    1.	Busque su certificado en la lista.
    1.	Haga clic con el botón derecho en el certificado y luego elija **Todas las tareas** > **Administrar claves privadas**.
    1.	Elija el botón **Agregar**, escriba **Servicio Local** y elija **Comprobar nombres**.
    1.	Elija el botón **Aceptar** y luego cierre el Administrador de certificados.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Registro del agente de compilación

1.	Descargue agent.zip. Para ello, siga estos pasos:
    1.	Inicie sesión en su proyecto de equipo, por ejemplo, ****https://[your-VSO-account-name].visualstudio.com**.
1.	Elija el icono de "engranaje" en la esquina superior derecha de la pantalla.
    1.	En el Panel de control, elija la pestaña **Grupos de agentes**.
    1.	Elija **Descargar agente** para descargar el archivo agent.zip.
    1.	Copie agent.zip en la máquina de compilación que creó anteriormente.
    1.	Descomprima agent.zip en `C:\agent` (o en cualquier ubicación con una ruta corta) en la máquina de compilación.

        >[AZURE.NOTE]Si tiene pensado crear servicios web de ASP.NET 5, se recomienda que elija el nombre más corto posible para esta carpeta a fin de evitar que aparezcan errores **PathTooLongExceptions** durante la implementación.

1.	Desde un símbolo del sistema de administrador de PowerShell, ejecute `C:\agent\ConfigureAgent.ps1`. El script le pide que especifique los parámetros siguientes:

    |Parámetro|Valor|
    |---|---|
    |Nombre del agente|Acepte el valor predeterminado, `Agent-[machine name]`.
    |Dirección URL de TFS|Escriba la dirección URL de su proyecto de equipo, por ejemplo, `https://[your-VSO-account-name].visualstudio.com`.
    |Grupo de agentes|Escriba el nombre del grupo de agentes. (Si no ha creado un grupo de agentes, acepte el valor predeterminado).|
    |Carpeta de trabajo|Acepte el valor predeterminado. Esta es la carpeta donde el agente de compilación compilará realmente la aplicación. Nota: Si tiene pensado crear servicios web de ASP.NET 5, se recomienda que elija el nombre más corto posible para esta carpeta a fin de evitar que aparezcan errores PathTooLongExceptions durante la implementación.|
    |¿Se instala como servicio de Windows?|El valor predeterminado es N. Cambie el valor a S.|
    |Cuenta de usuario para ejecutar el servicio|Acepte el valor predeterminado, `NT AUTHORITY\LocalService)`.|
    |¿Quitar la configuración de un agente existente?|Acepte el valor predeterminado, **N**.|

1. Se le pedirán las credenciales. Escriba las credenciales de su cuenta Microsoft que tenga derechos para el proyecto de equipo.
1. Compruebe que el agente de compilación se ha registrado. Para ello, siga estos pasos:

    1. Vuelva al explorador web (debe estar en la página `https://[your-VSO-account-name].visualstudio.com/_admin/_AgentPool`) y luego actualice la página.
    1. Elija el grupo de agentes que seleccionó al ejecutar anteriormente ConfigureAgent.ps1.
    1. Compruebe que el agente de compilación aparece en la lista y que tiene el estado resaltado en verde. Si está resaltado en rojo, el agente de compilación está teniendo problemas para conectarse a VSO.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Configuración de la definición de compilación

### Agregue los scripts de integración continua al control de código fuente de la aplicación.

1.	Extraiga [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775) en cualquier carpeta de control de código fuente.
1.	Proteja los archivos resultantes.

### Creación de la definición de compilación.

1.	Cree una definición de compilación vacía. Para ello, siga estos pasos:
    1.	Abra el proyecto en Visual Studio Online.
    1.	Elija la pestaña **Compilar**.
    1.	Elija el signo **+** verde para crear una nueva definición de compilación.
    1.	Elija **Vacía** y luego elija el botón **Siguiente**.
    1.  Compruebe que se seleccionan el repositorio y la rama derechos.
    1.  Seleccione la cola del agente en la que registró el agente de compilación y active la casilla **Integración continua**.
1.	En la pestaña **Variables**, cree las siguientes variables con estos valores.

    |Variable|Valor|Secret|Permitir durante el tiempo en cola|
    |---|---|---|---|
    |BuildConfiguration|Lanzamiento||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|La contraseña que pasó a CreateServicePrincipal.ps1|X||
    |ServicePrincipalId|Desde la salida de CreateServicePrincipal.ps1|||
    |ServicePrincipalTenantId|Desde la salida de CreateServicePrincipal.ps1|||
    |ServicePrincipalSubscriptionId|Desde la salida de CreateServicePrincipal.ps1|||
    |ServiceFabricCertificateThumbprint|Desde la salida de GenerateCertificate.ps1|||
    |ServiceFabricKeyVaultId|Desde la salida de GenerateCertificate.ps1|||
    |ServiceFabricCertificateSecretId|Desde la salida de GenerateCertificate.ps1|||
    |ServiceFabricClusterResourceGroupName|Cualquier nombre que desee.|||
    |ServiceFabricClusterName|Cualquier nombre que desee.|||
    |ServiceFabricClusterLocation|Cualquier nombre que coincida con la ubicación de su almacén de claves.|||
    |ServiceFabricClusterAdminPassword|Cualquier nombre que desee.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Nota: Se omitirá el punto de conexión del perfil de publicación. En su lugar se usará el punto de conexión del clúster temporal.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Debe ser la carpeta que contiene el archivo .sfproj.||||

1.	En la pestaña **Desencadenadores**, seleccione las opciones **Integración continua** y **Cambios en lote**.
1.	En la pestaña **General**, elija la cola en la que registró los agentes de compilación.
1.	Guarde la definición de compilación y asígnele un nombre. (Puede cambiar este nombre más adelante si lo desea).

### Incorporación de un paso "Compilar"

1.	En la pestaña **Compilar**, elija el comando **Agregar paso de compilación...**.
2.	Elija **Compilar** > **MSBuild**.
3.	Elija el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Compilar**.
4.	Elija el botón **…** junto al campo **Solución** y luego elija su archivo .sln.
5.	Escriba `$(BuildPlatform)` para **Plataforma**.
6.	Escriba `$(BuildConfiguration)` para **Configuración**.
7.	Active la casilla **Restaurar paquetes de NuGet** (si aún no está activada).
8.	Guarde la definición de compilación.

### Incorporación de un paso "Empaquetar"

1.	En la pestaña **Compilar**, elija el comando **Agregar paso de compilación...**.
2.	Elija **Compilar** > **MSBuild**.
3.	Elija el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Empaquetar**.
4.	Elija el botón **…** junto al campo **Solución** y luego seleccione el archivo .sfproj del proyecto de la aplicación.
5.	Escriba `$(BuildPlatform)` para **Plataforma**.
6.	Escriba `$(BuildConfiguration)` para **Configuración**.
7.	Escriba `/t:Package` para **Argumentos de MSBuild**.
8.	Desactive la casilla **Restaurar paquetes de NuGet** (si aún no está desactivada).
9.	Guarde la definición de compilación.

### Incorporación de un paso "Quitar grupo de recursos del clúster"

Si una compilación anterior no se limpió después (por ejemplo, si se canceló antes de que se pudiera limpiar), puede haber un grupo de recursos existente que causa conflicto con el nuevo. Para evitar conflictos, limpie cualquier grupo de recursos sobrante (y sus recursos asociados) antes de crear uno nuevo.

1.	En la pestaña **Compilar**, elija el comando **Agregar paso de compilación...**.
2.	Elija **Utilidad** > **PowerShell**.
3.	Elija el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Quitar grupo de recursos del clúster**.
4.	Elija el comando **...** junto a **Nombre de archivo de script**. Vaya a donde extrajo los scripts de automatización y luego elija **Remove-ClusterResourceGroup.ps1**.
5.	Para **Argumentos**, escriba `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.
6.	Guarde la definición de compilación.

### Incorporación de un paso "Aprovisionar e implementar para proteger el clúster"

1.	En la pestaña **Compilar**, elija el comando **Agregar paso de compilación...**.
2.	Elija **Utilidad** > **PowerShell**.
3.	Elija el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Aprovisionar e implementar para proteger el clúster**.
4.	Elija el botón **...** junto a **Nombre de archivo de script**. Vaya a donde extrajo los scripts de automatización y luego elija **ProvisionAndDeploy-SecureCluster.ps1**.
5.	Para "Argumentos", escriba `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`
6.	Guarde la definición de compilación.

### Incorporación de un paso "Quitar grupo de recursos del clúster"

Ahora que ha terminado con el clúster temporal, debe limpiarlo. Si no lo hace, le seguirán cobrando por él. Este paso quita el grupo de recursos, lo que elimina el clúster y todos los demás recursos del grupo.

1.	En la pestaña **Compilar**, elija el comando **Agregar paso de compilación...**.
1.	Elija **Utilidad** > **PowerShell**.
1.	Elija el icono de lápiz junto al nombre del paso de compilación y luego cambie su nombre por **Quitar grupo de recursos del clúster**.
1.	Elija el botón **...** junto a **Nombre de archivo de script**. Vaya a donde extrajo los scripts de automatización y luego elija **RemoveClusterResourceGroup.ps1**.
1.	Para "Argumentos", escriba `-ServicePrincipalPassword "$(ServicePrincipalPassword)`
1.	En **Opciones de control**, active la casilla **Ejecutar siempre**.
1.	Guarde la definición de compilación.

### ¡Pruébelo!

Elija el comando **Poner compilación en cola** para iniciar una compilación. También se desencadenarán compilaciones tras la protección.


## Soluciones alternativas

En las instrucciones anteriores se crea un nuevo clúster para cada compilación y se quita al final de esta. Si, por el contrario, prefiere que cada compilación realice una actualización de la aplicación (a un clúster existente), siga estos pasos.

1.	Cree manualmente un clúster de prueba mediante el Portal de administración de Azure o Azure PowerShell. Puede usar como referencia el script "ProvisionAndDeploy SecureCluster.ps1".
1.	Configure el perfil de publicación para admitir la actualización de la aplicación siguiendo [estas instrucciones](service-fabric-visualstudio-configure-upgrade.md).

1.	Reemplace el paso **Aprovisionar e implementar para proteger el clúster** por un paso que llame a Deploy-FabricApplication.ps1 directamente (y pásele su perfil de publicación).
1.	Quite los dos pasos de compilación **Quitar grupo de recursos del clúster** de la definición de compilación.

## Pasos siguientes

Para obtener más información sobre la integración continua con aplicaciones de Service Fabric, lea los siguientes artículos. - [Página principal de documentación sobre compilación](https://msdn.microsoft.com/Library/vs/alm/Build/overview) - [Implementación de un agente de compilación](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows) - [Creación y configuración de una definición de compilación](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_1203_2015-->