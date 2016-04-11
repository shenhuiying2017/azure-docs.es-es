<properties
   pageTitle="Integración continua para Service Fabric | Microsoft Azure"
   description="Obtenga información general sobre cómo configurar la integración continua para una aplicación de Service Fabric mediante Visual Studio Team Services (VSTS)."
   services="service-fabric"
   documentationCenter="na"
   authors="cawams"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/27/2016"
   ms.author="cawa" />

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

1.	Instale Azure PowerShell.
2. Instale PowerShellGet. Para ello, instale [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), que incluye PowerShellGet.

    >[AZURE.NOTE] Si ejecuta Windows 10 con las actualizaciones más recientes, puede omitir este paso.

3.	Instale y actualice el módulo AzureRM. Si tiene instalada una versión anterior de Azure PowerShell, quítela:

    a. Haga clic con el botón derecho en Inicio y, luego, seleccione **Agregar o quitar programas**.

    b. Busque "Azure PowerShell" y desinstálelo.

    c. Abra un símbolo del sistema de PowerShell.

    d. Instale el módulo AzureRM mediante el comando `Install-Module AzureRM`.

    e. Actualice el módulo AzureRM mediante el comando `Update-AzureRM`.

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

1.	Descargue y extraiga [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) en una carpeta de esta máquina.

2.	En un símbolo del sistema de administrador de PowerShell, cambie al directorio `Powershell\Manual` en el archivo extraído.

3.	Elija una contraseña para la entidad de servicio con el comando siguiente. Recuerde esta contraseña, ya que se usará como variable de compilación.

    ```
    $password = Read-Host -AsSecureString
    ```
4.	Ejecute el script de PowerShell Create-ServicePrincipal.ps1 con los parámetros siguientes:

|Parámetro|Valor|
|---|---|
|DisplayName|Cualquier nombre.|
|Homepage|Cualquier URI. No tiene que existir realmente.|
|IdentifierUri|Cualquier URI único. No tiene que existir realmente.|
|SecurePassword|$password|

Cuando finaliza el script, genera los tres valores siguientes. Anote los valores, ya que se usan como variables de compilación.

 - `ServicePrincipalId`
 - `ServicePrincipalTenantId`
 - `ServicePrincipalSubscriptionId`

### Creación de un certificado y su carga en una nueva instancia del Almacén de claves de Azure

>[AZURE.NOTE] Este script de ejemplo genera un certificado autofirmado, lo que no es una práctica segura y solo es aceptable para la experimentación. Siga las directrices de su organización para obtener un certificado legítimo en su lugar.

1.	En un símbolo del sistema de administrador de PowerShell, cambie al directorio desde el que extrajo `Manual.zip`.

2.	Ejecute el script `CreateAndUpload-Certificate.ps1` de PowerShell con los parámetros siguientes:

| Parámetro | Valor |
| --- | --- |
| KeyVaultLocation | Cualquier valor. Este parámetro debe coincidir con la ubicación en la que planea crear el clúster. |
| CertificateSecretName | Cualquier valor. |
| SecureCertificatePassword | Cualquier valor. Este parámetro se usa al importar el certificado en la máquina de compilación. |
| KeyVaultResourceGroupName | Cualquier valor. Sin embargo, no use el nombre del grupo de recursos que tiene previsto emplear para el clúster. |
| KeyVaultName | Cualquier valor. |
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

5. Seleccione **Proceso** > **Máquina virtual** > **Desde la galería**.

6. Seleccione la imagen **Visual Studio Enterprise 2015 Update 1 con Azure SDK 2.8 en Windows Server 2012 R2**.

    >[AZURE.NOTE] El SDK de Azure no es un componente obligatorio, pero actualmente no hay ninguna imagen disponible que solo tenga instalado Visual Studio 2015.

7.	Siga las instrucciones que aparecen en el cuadro de diálogo para crear la máquina virtual.

### Instalación del SDK de Service Fabric

Instale el [SDK de Service Fabric](https://azure.microsoft.com/campaigns/service-fabric/) en su equipo.

### Azure PowerShell

Para instalar Azure PowerShell, siga los pasos en que aparecen en la sección anterior, "Instalación de Azure PowerShell e inicio de sesión". Omita el paso "Inicio de sesión en Azure PowerShell".

### Registre los módulos de Azure PowerShell con la cuenta de servicio local.

>[AZURE.NOTE] Hágalo *antes* de iniciar el agente de compilación. De lo contrario, no seleccionará la nueva variable de entorno.

1. Presione la tecla del logotipo de Windows + R, escriba **regedit** y presione Intro.

2. Haga clic con el botón derecho en el nodo `HKEY_Users\.Default\Environment` y, luego, seleccione **Nuevo** > **Valor de cadena expandible**.

3. Escriba `PSModulePath` para el nombre y `%PROGRAMFILES%\WindowsPowerShell\Modules` para el valor. Reemplace `%PROGRAMFILES%` por el valor de la variable de entorno `PROGRAMFILES`.

### Importación del certificado de automatización

1.	Importe el certificado en la máquina de compilación. Para ello, siga estos pasos:

    a. Copie el archivo PFX creado mediante el script CreateAndUpload Certificate.ps1 en la máquina de compilación.

    b. Abra un símbolo del sistema de PowerShell como administrador y ejecute los siguientes comandos, con la contraseña que transmitió anteriormente a `CreateAndUpload-Certificate.ps1`.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	Ejecute el Administrador de certificados:

    a. Abra el Panel de control en Windows. Haga clic con el botón derecho en Inicio y, luego, seleccione **Panel de control**.

    b. Busque **Certificado**.

    c. Seleccione **Herramientas administrativas** > **Administrar certificados de equipo**.

3.	Conceda permiso a la cuenta de servicio local para usar su certificado de automatización:

    a. En **Certificados - Equipo Local**, expanda **Personal** y, luego, seleccione **Certificados**.

    b. Busque su certificado en la lista.

    c. Haga clic con el botón derecho en el certificado y, luego, seleccione **Todas las tareas** > **Administrar claves privadas**.

    d. Seleccione el botón **Agregar**, escriba **Servicio local** y, luego, seleccione **Comprobar nombres**.

    e. Seleccione **Aceptar** y, luego, cierre el Administrador de certificados.

 ![Captura de pantalla de los pasos para otorgar permiso a la cuenta de servicio local](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Registro del agente de compilación

1.	Descargue agent.zip. Para ello, siga estos pasos:

    a. Inicie sesión en el proyecto del equipo, como ****https://[your-VSTS-account-name].visualstudio.com**.

    b. Seleccione el icono de engranaje que se encuentra en la esquina superior derecha de la pantalla.

    c. Seleccione la pestaña **Grupos de agente**.

    d. Seleccione **Descargar agente** para descargar el archivo agent.zip.

    e. Copie agent.zip en la máquina de compilación que creó anteriormente.

    f. Descomprima agent.zip en `C:\agent` (o en cualquier ubicación con una ruta de acceso corta) en la máquina de compilación.

    >[AZURE.NOTE] Si tiene pensado usar servicios web de ASP.NET 5, se recomienda elegir el nombre más corto posible para esta carpeta a fin de evitar que aparezcan errores **PathTooLongExceptions** durante la implementación.

2.	En un símbolo del sistema de administrador, ejecute `C:\agent\ConfigureAgent.cmd`. El script le pide que especifique los parámetros siguientes:

|Parámetro|Valor|
|---|---|
|Nombre del agente|Acepte el valor predeterminado, `Agent-[machine name]`.|
|Dirección URL de TFS|Escriba la dirección URL del proyecto de equipo, por ejemplo, `https://[your-VSTS-account-name].visualstudio.com`.|
|Grupo de agentes|Escriba el nombre del grupo de agentes. (Si no ha creado un grupo de agentes, acepte el valor predeterminado).|
|Carpeta de trabajo|Acepte el valor predeterminado. Esta es la carpeta donde el agente de compilación compilará realmente la aplicación. Si tiene pensado usar servicios web de ASP.NET 5, se recomienda elegir el nombre más corto posible para esta carpeta a fin de evitar que aparezcan errores PathTooLongExceptions durante la implementación.|
|¿Se instala como servicio de Windows?|El valor predeterminado es N. Cambie el valor a **Y**.|
|Cuenta de usuario para ejecutar el servicio|Acepte el valor predeterminado, `NT AUTHORITY\LocalService`.|
|¿Quitar la configuración de un agente existente?|Acepte el valor predeterminado, **N**.|

3.  Cuando se le soliciten credenciales, escriba las de la cuenta de Microsoft que tenga derechos sobre el proyecto de equipo.

4.  Compruebe que el agente de compilación se ha registrado. Para ello, siga estos pasos:

    a. Vuelva al explorador web, (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`), y actualice la página.

    b. Seleccione el grupo de agentes que seleccionó al ejecutar anteriormente ConfigureAgent.ps1.

    c. Compruebe que el agente de compilación aparece en la lista y que tiene el estado resaltado en verde. Si está resaltado en rojo, el agente de compilación está teniendo problemas para conectarse a Team Services.

 ![Una captura de pantalla que muestra el estado del agente de compilación](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Creación de la definición de compilación

>[AZURE.NOTE] La definición de compilación que se crea a partir de estas instrucciones no admite múltiples compilaciones simultáneas, incluso en máquinas distintas. Esto se debe a que cada compilación competiría por el mismo clúster o grupo de recursos. Si desea ejecutar varios agentes de compilación, deberá modificar las siguientes instrucciones o scripts para impedir esta interferencia.

### Adición de los scripts de integración continua al control de código fuente de la aplicación

1.	Extraiga [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) en cualquier carpeta de su máquina. Copie el contenido de `Powershell\Automation` en cualquier carpeta de control de código fuente.

2.	Proteja los archivos resultantes.

### Creación de la definición de compilación.

1.	Cree una definición de compilación vacía. Para ello, siga estos pasos:

    a. Abra el proyecto en Visual Studio Team Services.

    b. Seleccione la pestaña **Compilar**.

    c. Seleccione el signo **+** verde para crear una nueva definición de compilación.

    d. Seleccione **Vacía** y, luego, seleccione **Siguiente**.

    e. Compruebe que se seleccionan el repositorio y la rama derechos.

    f. Seleccione la cola del agente en la que registró el agente de compilación y, luego, active la casilla **Integración continua**.

2.	En la pestaña **Variables**, cree las siguientes variables con estos valores.

|Variable|Valor|Secret|Permitir durante el tiempo en cola|
|---|---|---|---|
|BuildConfiguration|Lanzamiento||X|
|BuildPlatform|x64|||
|ServicePrincipalPassword|La contraseña que pasó a CreateServicePrincipal.ps1.|X||
|ServicePrincipalId|Desde la salida de CreateServicePrincipal.ps1.|||
|ServicePrincipalTenantId|Desde la salida de CreateServicePrincipal.ps1.|||
|ServicePrincipalSubscriptionId|Desde la salida de CreateServicePrincipal.ps1.|||
|ServiceFabricCertificateThumbprint|Desde la salida de GenerateCertificate.ps1.|||
|ServiceFabricKeyVaultId|Desde la salida de GenerateCertificate.ps1.|||
|ServiceFabricCertificateSecretId|Desde la salida de GenerateCertificate.ps1.|||
|ServiceFabricClusterName|Cualquier nombre que desee.|||
|ServiceFabricClusterResourceGroupName|Cualquier nombre que desee.|||
|ServiceFabricClusterLocation|Cualquier nombre que coincida con la ubicación de su almacén de claves.|||
|ServiceFabricClusterAdminPassword|Cualquier nombre que desee.|X||
|ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
|ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Se omitirá el punto de conexión del perfil de publicación. En su lugar se usará el punto de conexión del clúster temporal.|||
|ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
|ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Debe ser la carpeta que contiene el archivo .sfproj.||||

3.  Guarde la definición de compilación y asígnele un nombre. (Puede cambiar este nombre más adelante si lo desea).

### Adición de un paso "Compilar"

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Compilar** > **MSBuild**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y, luego, cambie su nombre por **Compilar**.

4.	Seleccione el botón **…** junto al campo **Solución** y, luego, seleccione su archivo .sln.

5.	Escriba `$(BuildPlatform)` para **Plataforma**.

6.	Escriba `$(BuildConfiguration)` para **Configuración**.

7.	Active la casilla **Restaurar paquetes de NuGet** (si aún no está activada).

8.	Guarde la definición de compilación.

### Adición de un paso "Empaquetar"

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Compilar** > **MSBuild**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y, luego, cambie su nombre por **Empaquetar**.

4.	Seleccione el botón **…** junto al campo **Solución** y, luego, seleccione el archivo .sfproj del proyecto de la aplicación.

5.	Escriba `$(BuildPlatform)` para **Plataforma**.

6.	Escriba `$(BuildConfiguration)` para **Configuración**.

7.	Escriba `/t:Package` para **Argumentos de MSBuild**.

8.	Desactive la casilla **Restaurar paquetes de NuGet** (si aún no está desactivada).

9.	Guarde la definición de compilación.

### Adición de un paso "Quitar grupo de recursos del clúster"

Si una compilación anterior no se limpió después (por ejemplo, si se canceló antes de que se pudiera limpiar), puede haber un grupo de recursos existente que causa conflicto con el nuevo. Para evitar conflictos, limpie cualquier grupo de recursos sobrante (y sus recursos asociados) antes de crear uno nuevo.

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Utilidad** > **PowerShell**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y, luego, cambie su nombre por **Quitar grupo de recursos del clúster**.

4.	Seleccione el comando **…** junto a **Nombre de archivo de script**. Vaya a donde extrajo los scripts de automatización y, luego, seleccione **Remove-ClusterResourceGroup.ps1**.

5.	Para **Argumentos**, escriba `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.

6.	Guarde la definición de compilación.

### Adición de un paso "Aprovisionar e implementar para proteger el clúster"

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Utilidad** > **PowerShell**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y, luego, cambie su nombre por **Aprovisionar e implementar para proteger el clúster**.

4.	Seleccione el botón **…** junto a **Nombre de archivo de script**. Vaya a donde extrajo los scripts de automatización y, luego, seleccione **ProvisionAndDeploy-SecureCluster.ps1**.

5.	Para **Argumentos**, escriba `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`.

6.	Guarde la definición de compilación.

### Adición de un paso "Quitar grupo de recursos del clúster"

Ahora que ha terminado con el clúster temporal, debe limpiarlo. Si no lo hace, le seguirán cobrando por él. Este paso quita el grupo de recursos, lo que elimina el clúster y todos los demás recursos del grupo.

>[AZURE.NOTE] Hay una diferencia entre este paso y el anterior, "Quitar grupo de recursos del clúster": este debe tener activado **Ejecutar siempre**.

1.	En la pestaña **Compilar**, seleccione el comando **Agregar paso de compilación…**.

2.	Seleccione **Utilidad** > **PowerShell**.

3.	Seleccione el icono de lápiz junto al nombre del paso de compilación y, luego, cambie su nombre por **Quitar grupo de recursos del clúster**.

4.	Seleccione el botón **…** junto a **Nombre de archivo de script**. Vaya a donde extrajo los scripts de automatización y, luego, seleccione **RemoveClusterResourceGroup.ps1**.

5.	Para **Argumentos**, escriba `-ServicePrincipalPassword "$(ServicePrincipalPassword)`."

6.	En **Opciones de control**, active la casilla **Ejecutar siempre**.

7.	Guarde la definición de compilación.

### Pruébelo

Seleccione **Poner compilación en cola** para iniciar una compilación. También se desencadenarán compilaciones tras la inserción o protección.


## Soluciones alternativas

En las instrucciones anteriores se crea un nuevo clúster para cada compilación y se quita al final de esta. Si, por el contrario, prefiere que cada compilación realice una actualización de la aplicación (a un clúster existente), siga estos pasos:

1.	Cree manualmente un clúster de prueba mediante el Portal de Azure o Azure PowerShell. Puede usar como referencia el script `ProvisionAndDeploy-SecureCluster.ps1`.

2.	Configure el perfil de publicación para admitir la actualización de la aplicación siguiendo [estas instrucciones](service-fabric-visualstudio-configure-upgrade.md).

3.	Reemplace el paso **Aprovisionar e implementar para proteger el clúster** por un paso que llame a Deploy-FabricApplication.ps1 directamente (y páselo a su perfil de publicación).

4.	Quite los dos pasos de compilación **Quitar grupo de recursos del clúster** de la definición de compilación.

## Pasos siguientes

Para obtener más información sobre la integración continua con las aplicaciones de Service Fabric, lea los artículos siguientes:

 - [Documentación de compilación: página principal](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [Implementación de un agente de compilación](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [Creación y configuración de una definición de compilación](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0330_2016-->