<properties linkid="dev-net-common-tasks-continuous-delivery" urlDisplayName="Continuous Delivery" pageTitle="Continuous delivery for cloud services with TFS in Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous delivery PowerShell" description="Learn how to set up continuous delivery for Azure cloud apps. Code samples for MSBuild command-line statements and PowerShell scripts." metaCanonical="" services="" documentationCenter="" title="Continuous Delivery for Cloud Services in Azure" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Entrega continua para Servicios en la nube de Azure

En el proceso que se describe en este artículo se muestra cómo se configura la entrega
continua para las aplicaciones en la nube de Azure. Este proceso le permite crear
paquetes automáticamente e implementarlos en Azure
después de cada protección de código. El proceso de compilación del paquete que se describe en este
artículo es equivalente al comando Package en Visual Studio y los
pasos de publicación son equivalentes al comando Publish en Visual Studio.
El artículo abarca los métodos que usaría para crear un servidor de compilación
con instrucciones de línea de comandos de MSBuild y scripts de Windows PowerShell; además,
también se demuestra cómo configurar de manera opcional las definiciones de Visual Studio Team
Foundation Server - Team Build para usar los comandos de MSBuild
y los scripts de PowerShell. El proceso se puede personalizar para su entorno de
compilación y los entornos de destino de Azure.

Para hacerlo de manera más fácil, puede también usar Visual Studio Online, una versión de TFS que se hospeda en Azure. Para obtener más información, consulte [Entrega continua a Azure con Visual Studio Online][].

Antes de comenzar, debe publicar su aplicación desde Visual Studio.
Esto asegurará que todos los recursos estén disponibles e inicializados cuando
intente automatizar el proceso de publicación.

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Configurar el servidor de compilación][]
-   [Paso 2: Compilar un paquete con los comandos de MSBuild][]
-   [Paso 3: Compilar un paquete con TFS Team Build (opcional)][]
-   [Paso 4: Publicar un paquete con un script de PowerShell][]
-   [Paso 5: Publicar un paquete con TFS Team Build (opcional)][]

## <a name="step1"> </a><span class="short-header">Configurar el servidor de compilación</span>Paso 1: Configurar el servidor de compilación

Antes de crear un paquete de Azure con MSBuild, debe
instalar el software y las herramientas necesarios en el servidor de compilación.

No es necesario instalar Visual Studio en el servidor de compilación. Si
desea usar el servicio de Team Foundation Build para administrar el servidor
de compilación, siga las instrucciones que aparecen en el documento [Configurar el Servicio de Team Foundation Build][]
.

1.  En el servidor de compilación, instale [.NET Framework 4][] o [.NET Framework 4.5][], el cual
    incluye MSBuild.
2.  Instale las [herramientas de creación de Azure][] (en inglés) (busque
    WindowsAzureAuthoringTools-x86.msi o WindowsAzureAuthoringTools-x64.msi,
    según el procesador del servidor de compilación).
3.  Instale las [Bibliotecas de Azure][] (busque WindowsAzureLibsForNet-x86.msi o WindowsAzureLibsForNet-x64.msi).
4.  Copie el archivo Microsoft.WebApplication.targets desde una instalación
    de Visual Studio al servidor de compilación. En un equipo con Visual Studio
    instalado, el archivo se encuentra en el directorio C:\\Program Files
    (x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (v12.0
    para Visual Studio 2013). Debe
    copiarlo en el mismo directorio en el servidor de compilación.
5.  Instale [Azure Tools para Visual Studio][Bibliotecas de Azure].
    Busque WindowsAzureTools.VS120.exe para compilar los proyectos de Visual Studio 2012 y WindowsAzureTools.VS120.exe para compilar los proyectos de Visual Studio 2013.

## <a name="step2"> </a><span class="short-header">Compilar un paquete con MSBuild</span>Paso 2: Compilar un paquete con los comandos de MSBuild

En esta sección se describe cómo construir un comando de MSBuild que compila un
paquete de Azure. Ejecute este paso en el servidor de
compilación para verificar que todo esté correctamente configurado y que el comando
de MSBuild haga lo que usted desea. Puede agregar esta línea de comandos a los scripts de compilación
existentes en el servidor de compilación o puede usar la línea de comandos en una definición de compilación de
TFS, como se describe en la siguiente sección. Para obtener más
información acerca de los parámetros de línea de comandos y MSBuild, consulte [Referencia de la línea de comandos de MSBuild][].

1.  Si Visual Studio está instalado en el servidor de compilación, haga clic en
    **Inicio**, **Todos los programas** y, a continuación, busque y haga clic en
    **Visual Studio Commmand Prompt** en la carpeta **Visual Studio Tools**
    .

    Si Visual Studio no está instalado en el servidor de compilación, abra
    un símbolo del sistema y asegúrese de que se pueda tener acceso a MSBuild.exe en la
    ruta. MSBuild se instala con .NET Framework en la ruta
     %WINDIR%\\Microsoft.NET\\Framework\\*Versión*. Por ejemplo, para
    agregar MSBuild.exe a la variable de entorno PATH cuando tiene instalado .NET
    Framework 4, escriba el siguiente comando en el símbolo del
    sistema:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework.0"

2.  En el símbolo del sistema, vaya a la carpeta que contiene el archivo de proyecto de
    Azure que desea compilar.

3.  Ejecute msbuild con la opción /target:Publish como se muestra en el ejemplo
    siguiente:

        MSBuild /target:Publish

    Esta opción se puede abreviar como /t:Publish. La opción /t:Publish
    en MSBuild no debe confundirse con los comandos de publicación
    disponibles en Visual Studio cuando tiene instalado
    el SDK de Azure. La opción /t:Publish solo compila los paquetes de
    Azure. No implementa los paquetes como lo hacen los comandos
    de publicación en Visual Studio.

    De manera opcional, puede especificar el nombre del proyecto como un parámetro
    de MSBuild. Si no se especifica, se utiliza el directorio actual. Para obtener más
    información acerca de las opciones de línea de comandos de MSBuild, consulte [Referencia de la línea
    de comandos de MSBuild][].

4.  Busque el resultado. De manera predeterminada, este comando crea un directorio en
    relación con la carpeta raíz para el proyecto, como por ejemplo
    *ProjectDir*\\bin\\*Configuration*\\app.publish\\. Al
    compilar un proyecto de Azure se generan dos archivos: el archivo del
    paquete propiamente dicho y el archivo de configuración complementario:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    De manera predeterminada, cada proyecto de Azure incluye un
    archivo de configuración del servicio (archivo .cscfg) para las compilaciones locales
    (depuración) y otro para las compilaciones en la nube (ensayo o producción), pero puede
    agregar o quitar archivos de configuración del servicio según sea necesario. Al
    compilar un paquete dentro de Visual Studio, se le preguntará qué
    archivo de configuración del servicio se debe incluir junto con el paquete.

5.  Especifique el archivo de configuración del servicio. Cuando compila un paquete con
    MSBuild, el archivo de configuración del servicio local se incluye
    de manera predeterminada. Para incluir un archivo de configuración del servicio diferente, establezca la propiedad
    TargetProfile del comando de MSBuild, como se muestra en el siguiente
    ejemplo:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Especifique la ubicación para el resultado. Establezca la ruta usando la
    opción /p:PublishDir=*Directory*\\, incluido el separador de barra diagonal
    inversa final, como en el siguiente ejemplo:

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    Después de que haya construido y probado una línea de
    comandos de MSBuild adecuada para compilar sus proyectos y combinarlos en un paquete de Azure,
    puede agregar esta línea de comandos a sus scripts de compilación. Si su servidor de
    compilación usa scripts personalizados, este proceso dependerá de
    los detalles de su proceso personalizado de compilación. Si va a usar TFS como un
    entorno de compilación, entonces puede seguir las instrucciones del paso
    siguiente para agregar la compilación del paquete de Azure a su proceso de compilación.

## <a name="step3"> </a><span class="short-header">Compilar un paquete con TFS </span>Paso 3: Compilar un paquete con TFS Team Build (opcional)

Si tiene Team Foundation Server (TFS) configurado como un controlador de compilación
y el servidor de compilación configurado como una máquina de compilación de TFS, puede configurar
una compilación automatizada para su paquete de Azure. Para obtener información sobre
cómo configurar y usar el servidor de Team Foundation como un sistema de compilación, consulte
[Información sobre el sistema de compilación de Team Foundation][]. En concreto, el
siguiente procedimiento supone que ya configuró el servidor de compilación
como se describe en [Configuración de una máquina de compilación][].

Para configurar TFS a fin de compilar paquetes de Azure, siga estos
pasos:

1.  En Visual Studio de su equipo de desarrollo, en el menú
    Ver, seleccione **Team Explorer** o seleccione Ctrl+\\, Ctrl+M. En la
    ventana de Team Explorer, expanda el nodo **Compilaciones**, haga clic con el botón secundario en **Todas
    las definiciones de compilación** y, a continuación, haga clic en **Nueva definición de compilación**:

    ![][]

2.  Haga clic en la pestaña **Proceso**. En la pestaña Proceso, seleccione la plantilla
    predeterminada, en Elementos para compilar, seleccione el proyecto y
    expanda la sección **Avanzado** en la cuadrícula.

3.  Seleccione **Argumentos de MSBuild** y establezca los argumentos adecuados de la línea de comandos de
    MSBuild como se describe en el paso 2 anterior. Por ejemplo,
    escriba **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** para compilar un
    paquete y copiar los archivos del paquete en la ubicación
    \\\\myserver\\drops\\:

    ![][1]

    **Nota:** La copia de los archivos en un recurso compartido público facilita
    la implementación manual de los paquetes desde su equipo de desarrollo.

4.  Haga clic en la pestaña **Desencadenador** y especifique las condiciones deseadas para
    cuando desea que se compile el paquete. Por ejemplo, especifique
    **Integración continua** para compilar el paquete cada vez que se produce una protección del control de
    código fuente.

5.  Seleccione la pestaña **Valores predeterminados de compilación** y, en el controlador de compilación, compruebe
    el nombre del servidor de compilación. Además, seleccione la opción **Copiar el resultado
    de la compilación en la siguiente carpeta de entrega** y especifique la ubicación de destino
    deseada.

6.  Pruebe el éxito del paso de compilación protegiendo un cambio en
    su proyecto o ponga en cola una compilación nueva. Para poner en cola una compilación nueva, en
    Team Explorer, haga clic con el botón secundario en **Todas las definiciones de compilación** y, a continuación,
    seleccione **Poner nueva compilación en cola**.

## <a name="step4"> </a><span class="short-header">Publicar con Powershell</span>Paso 4: Publicar un paquete con un script de PowerShell

En esta sección se describen los pasos para construir un script de Windows PowerShell
que publicará el resultado del paquete de aplicaciones en la nube en Azure con la ayuda
de parámetros opcionales. Se puede llamar a este script después del paso de compilación en
su automatización de compilación personalizada. También puede llamarse desde las actividades de flujo de trabajo de la Plantilla de
procesos en Visual Studio TFS Team Build.

1.  Instale los [cmdlets de Azure PowerShell][] (v0.6.1 o superior).
    Durante la fase de instalación de cmdlet, seleccione instalar como complemento. Tenga en cuenta
    que esta versión oficialmente compatible reemplaza la versión anterior que se ofrece
    mediante CodePlex, aunque las versiones anteriores tenían la numeración 2.x.x.

2.  Inicie Azure PowerShell mediante el menú Inicio. Si inicia de esta manera,
    se cargarán los cmdlets de Azure PowerShell.

3.  A la solicitud de PowerShell, compruebe que se cargan los cmdlets de PowerShell
    escribiendo el comando parcial Get-Azure y, a continuación, presione la pestaña para completar
    la instrucción.

    Debería ser capaz de ver varios comandos de Azure PowerShell.

4.  Compruebe que se puede conectar a su suscripción de Azure importando
    la información de suscripción desde el archivo .publishsettings.

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings

    A continuación, proporcione el comando

    Get-AzureSubscription

    De esta manera, podrá ver la información sobre su suscripción. Verifique que todo esté correcto.

5.  Guarde la plantilla de script que se proporciona al [final de este artículo][] en su
    carpeta de scripts como
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

6.  Revise la sección de parámetros del script. Agregue o modifique cualquiera
    de los valores predeterminados. Estos valores siempre pueden invalidarse al pasar
    parámetros explícitos.

7.  Asegúrese de que haya cuentas de almacenamiento y de servicio en la nube válidas creadas
    en su suscripción que se puedan abordar mediante el script de publicación. Se usará la
    cuenta de almacenamiento (almacenamiento de blobs) para cargar y
    almacenar temporalmente el paquete de implementación y el archivo de configuración mientras se crea
    la implementación.

    -   Para crear un servicio en la nube nuevo, puede llamar a este script o usar
        el Portal de administración de Azure. El nombre del servicio en la nube
        se usará como prefijo en un nombre de dominio completo y,
        por este motivo, debe ser único.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Para crear una cuenta de almacenamiento nueva, puede llamar a este script o usar
        el Portal de administración de Azure. El nombre de la cuenta de almacenamiento
        se usará como prefijo en un nombre de dominio completo y,
        por este motivo, debe ser único. Puede intentar usar el mismo nombre que el
        servicio en la nube.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

8.  Llame al script directamente desde Azure PowerShell, o conecte este
    script a su automatización de compilación de host que se produce después de la compilación del
    paquete.

    **ADVERTENCIA:** El script siempre eliminará o reemplazará sus implementaciones actuales
    de manera predeterminada si se detectan. Esto es necesario para
    habilitar la entrega continua desde la automatización donde no es posible una solicitud
    del usuario.

    **Escenario de ejemplo 1:** implementación continua en el entorno de
    ensayo de un servicio:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    A esto le sigue generalmente una comprobación de ejecución de prueba y un intercambio de
    VIP. El intercambio de VIP se puede realizar mediante el Portal de administración
    de Azure o con el cmdlet Move-Deployment.

    **Escenario de ejemplo 2:** implementación continua en el entorno de
    producción de un servicio de prueba dedicado:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Escritorio remoto:**

    Si se habilita Escritorio remoto en su proyecto de Azure,
    necesitará realizar algunos pasos únicos adicionales para
    asegurarse de que se cargue el certificado de servicio en la nube correcto en todos los servicios en la nube
    abordados por este script.

    Busque los valores de huella digital del certificado que esperan sus roles. Los
    valores de huella digital son visibles en la sección Certificados del
    archivo de configuración de la nube (es decir, ServiceConfiguration.Cloud.cscfg). También
    se ven en el cuadro de diálogo Configuración de Escritorio remoto en Visual
    Studio al mostrar las opciones y ver el certificado seleccionado.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Cargue los certificados de Escritorio remoto como un paso de configuración único
    con el siguiente script de cmdlet:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MYAdd-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)lt;THUMBPRINT>)

    Por ejemplo:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    De manera alternativa, puede exportar el archivo de certificado PFX con una clave
    privada y cargar los certificados en cada servicio en la nube objetivo mediante el
    Portal de administración de Azure. Lea el siguiente artículo para obtener
    más información:
    [<http://msdn.microsoft.com/es-es/library/windowsazure/gg443832.aspx>][].

    **Actualizar implementación frente a Eliminar implementación -\> Nueva implementación**

    El script realizará de manera predeterminada una actualización de la implementación
    ($enableDeploymentUpgrade = 1) cuando no se pasa ningún parámetro o el
    valor 1 se pasa de manera explícita. Para las instancias simples, esto tiene
    la ventaja de tardar menos tiempo que una implementación completa. Para las instancias
    que requieren una alta disponibilidad, esto también tiene la ventaja de
    dejar algunas instancias en ejecución mientras otras se actualizan (recorriendo
    su dominio de actualización), además de que no se eliminará su VIP.

    La actualización de la implementación se puede deshabilitar en el script
    ($enableDeploymentUpgrade = 0) o al pasar
    -enableDeploymentUpgrade 0 como un parámetro, el cual alterará el
    comportamiento del script para eliminar primero cualquier implementación existente y luego
     crear una implementación nueva.

    **Advertencia:** El script siempre eliminará o reemplazará sus implementaciones actuales
    de manera predeterminada si se detectan. Esto es necesario para
    habilitar la entrega continua desde la automatización donde no es posible una solicitud
    del usuario/operador.

## <a name="step5"> </a><span class="short-header">Publicar con TFS </span>Paso 5: Publicar un paquete con TFS Team Build (opcional)

Este paso conectará TFS Team Build al script que se creó en el paso 4,
el cual controla la publicación de la compilación del paquete en Azure. Esto
conlleva modificar la Plantilla de proceso que usó su definición de compilación
de modo que ejecute una actividad de publicación al final del flujo de trabajo. La actividad de
publicación ejecutará su comando de PowerShell al pasar parámetros
desde la compilación. El resultado de los objetivos de MSBuild y el script de publicación se
canalizarán al resultado de la compilación estándar.

1.  Edite la definición de compilación a cargo de la implementación continua.

2.  Seleccione la pestaña **Proceso**.

3.  Cree una plantilla de proceso nueva haciendo clic en el botón Nuevo... en
    el área de Selección de plantilla del proceso de la pestaña. Se cargará el cuadro de diálogo
    Nueva plantilla de proceso de compilación. En este cuadro de diálogo, asegúrese de seleccionar
    Copiar un archivo XAML existente y vaya hasta ProcessTemplate
    para copiar si desea cambiar la base desde el valor predeterminado. Asigne
    un nombre a la plantilla nueva como DefaultTemplateAzure.

4.  Abra la Plantilla de proceso seleccionada para editar. Puede abrirla
    directamente en el diseñador de flujo de trabajo o en el editor XML para trabajar con el
    archivo XAML.

5.  Agregue la siguiente lista de argumentos nuevos como elementos de línea separados en
    la pestaña de argumentos del diseñador de flujo de trabajo. Todos los argumentos deben
    tener la dirección=In y el tipo=String. Estos se usarán para que los
    parámetros fluyan desde la definición de compilación hasta el flujo de trabajo, los cuales, posteriormente,
    se acostumbran a llamar al script de publicación.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][2]

    El archivo XAML correspondiente se parece a este:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Agregue una secuencia nueva al final de Ejecutar en el agente:

    1.  Comience agregando una actividad de instrucción If para comprobar
        un archivo de script válido. Establezca la condición según este valor:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  En el caso Then de la instrucción If, agregue una actividad de secuencia
        nueva. Establezca el nombre para mostrar en 'Iniciar publicación'

    3.  Con la secuencia Iniciar publicación todavía seleccionada, agregue la
        siguiente lista de variables nuevas como elementos de línea separados en la pestaña de
        variables del diseñador de flujo de trabajo. Todas las variables deben
        tener el tipo de variable=String y el ámbito=Start publish. Estos se usarán para que los
        parámetros fluyan desde la definición de compilación hasta el
        flujo de trabajo, los cuales, posteriormente, se acostumbran a llamar al script de publicación.

        -   SubscriptionDataFilePath, de tipo Cadena

        -   PublishScriptFilePath, de tipo Cadena

            ![][3]

    4.  Agregue una actividad ConvertWorkspaceItem al comienzo de la secuencia
        nueva. Direction=ServerToLocal, DisplayName='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'. Esta
        actividad convierte la ruta al script de publicación desde las ubicaciones del
        servidor TFS (si corresponde) hasta una ruta de disco local estándar.

    5.  Agregue otra actividad ConvertWorkspaceItem al final de la secuencia
        nueva. Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        Result= 'SubscriptionDataFilePath', Workspace='Workspace'.

    6.  Agregue una actividad InvokeProcess al final de la secuencia nueva.
        Esta actividad llama a PowerShell.exe con los argumentos pasados por
        la definición de compilación.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Ejecutar script de publicación'

        3.  FileName = "PowerShell"

        4.  OutputEncoding=
            'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  En el cuadro de texto de la sección Controlar salida estándar de
        InvokeProcess, establezca el valor del cuadro de texto en 'data'. Esta es una
        variable para almacenar los datos de salida estándar.

    8.  Agregue una actividad WriteBuildMessage justo debajo de la sección de salida
        estándar. Establezca la importancia =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'
        y el mensaje ='data'. Esto asegura que la salida estándar del
        script se escriba en la salida de la compilación.

    9.  En el cuadro de texto de la sección Controlar error estándar de
        InvokeProcess, establezca el valor del cuadro de texto en 'data'. Esta es una
        variable para almacenar los datos de error estándar.

    10. Agregue una actividad WriteBuildError justo debajo de la sección de salida
        estándar. Establezca el mensaje='data'. Esto asegura que los
        errores estándar del script se escriban en la salida de error de compilación.

    El resultado final de las actividades de flujo de trabajo de publicación se verá
    en el diseñador de la manera siguiente:

    ![][4]

    El resultado final de las actividades de flujo de trabajo de publicación se verá
    en el archivo XAML de la manera siguiente:

            </TryCatch>
              <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap:VirtualizedContainerService.HintSize="1539,552">
                <If.Then>
                  <Sequence DisplayName="Start publish" sap:VirtualizedContainerService.HintSize="297,446">
                    <Sequence.Variables>
                      <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                      <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                    </Sequence.Variables>
                    <sap:WorkflowViewStateService.ViewState>
                      <scg:Dictionary x:TypeArguments="x:String, x:Object">
                        <x:Boolean x:Key="IsExpanded">True</x:Boolean>
                      </scg:Dictionary>
                    </sap:WorkflowViewStateService.ViewState>
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription data file filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                    <mtbwa:InvokeProcess Arguments="[String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="Execute publish script" FileName="PowerShell" sap:VirtualizedContainerService.HintSize="234,198">
                      <mtbwa:InvokeProcess.ErrorDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildError sap:VirtualizedContainerService.HintSize="200,22" Message="[data]" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.ErrorDataReceived>
                      <mtbwa:InvokeProcess.OutputDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildMessage sap:VirtualizedContainerService.HintSize="200,22" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.OutputDataReceived>
                    </mtbwa:InvokeProcess>
                  </Sequence>
                </If.Then>
              </If>
            </mtbwa:AgentScope>
            <mtbwa:InvokeForReason DisplayName="Check In Gated Changes for CheckInShelveset Builds" sap:VirtualizedContainerService.HintSize="1370,146" Reason="CheckInShelveset">
              <mtbwa:CheckInGatedChanges DisplayName="Check In Gated Changes" sap:VirtualizedContainerService.HintSize="200,22" />
            </mtbwa:InvokeForReason>
          </Sequence>
        </Activity>

7.  Guarde el flujo de trabajo DefaultTemplateAzure y proteja este archivo.

8.  Seleccione la plantilla de proceso DefaultTemplateAzure en su
    definición de compilación. Haga clic en Actualizar o seleccione el botón Nuevo si todavía no ve
    este archivo en la lista de Plantillas de proceso.

9.  Establezca los valores de propiedad del parámetro en la sección Misc como a continuación:

    1.  CloudConfigLocation = 'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
         *Este valor se obtiene de:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation =
        'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
        *Este valor se obtiene de: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation =
        'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
        *Use el nombre de servicio en la nube adecuado aquí*

    5.  Entorno = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
        *Use el nombre de cuenta de almacenamiento adecuado aquí*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][5]

10. Guarde los cambios en la Definición de compilación.

11. Ponga una compilación en cola para ejecutar la compilación del paquete y la publicación. Si tiene
    un desencadenador establecido en Implementación continua, ejecutará este
    comportamiento en cada protección.

### <a name="script"> </a>Plantilla de script PublishCloudService.ps1

    Param(  $serviceName = "",
            $storageAccountName = "",
            $packageLocation = "",
            $cloudConfigLocation = "",
            $environment = "Staging",
            $deploymentLabel = "ContinuousDeploy to $servicename",
            $timeStampFormat = "g",
            $alwaysDeleteExistingDeployments = 1,
            $enableDeploymentUpgrade = 1,
            $selectedsubscription = "default",
            $subscriptionDataFile = ""
         )
          

    function Publish()
    {
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
        if ($a[0] -ne $null)
        {
            Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
        }
        #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
        if ($deployment.Name -ne $null)
        {
            switch ($alwaysDeleteExistingDeployments)
            {
                1 
                {
                    switch ($enableDeploymentUpgrade)
                    {
                        1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                            UpgradeDeployment
                        }
                        0  #Delete then create new deployment
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Eliminando implementación".
                            DeleteDeployment
                            CreateNewDeployment
                            
                        }
                    } # switch ($enableDeploymentUpgrade)
                }
                0
                {
                    Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                    exit
                }
            } #switch ($alwaysDeleteExistingDeployments)
        } else {
                CreateNewDeployment
        }
    }

    function CreateNewDeployment()
    {
        write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

        $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
            
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid

        write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"
        
        StartInstances
    }

    function UpgradeDeployment()
    {
        write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

        # perform Update-Deployment
        $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
        
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid
        
        write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
    }

    function DeleteDeployment()
    {

        write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

        #WARNING - always deletes with force
        $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

        write-progress -id 2 -activity "Deleting Deployment: Completado-completed -Status $removeDeployment
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"
        
    }

    function StartInstances()
    {
        write-progress -id 4 -activity "Starting Instances" -status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $runstatus = $deployment.Status

        if ($runstatus -ne 'Running') 
        {
            $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
        }
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $oldStatusStr = @("") * $deployment.RoleInstanceList.Count
        
        while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
        {
            $i = 1
            foreach ($roleInstance in $deployment.RoleInstanceList)
            {
                $instanceName = $roleInstance.InstanceName
                $instanceStatus = $roleInstance.InstanceStatus

                if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
                {
                    $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
                }

                write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
                $i = $i + 1
            }

            sleep -Seconds 1

            $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        }

        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            $i = $i + 1
        }
        
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $opstat = $deployment.Status 
        
        write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
    }

    function AllInstancesRunning($roleInstanceList)
    {
        foreach ($roleInstance in $roleInstanceList)
        {
            if ($roleInstance.InstanceStatus -ne "ReadyRole")
            {
                return $false
            }
        }
        
        return $true
    }

    #configure powershell with Azure 1.7 modules
    Import-Module Azure

    #configure powershell with publishsettings for your subscription
    $pubsettings = $subscriptionDataFile
    Import-AzurePublishSettingsFile $pubsettings
    Set-AzureSubscription -CurrentStorageAccount $storageAccountName -SubscriptionName $selectedsubscription

    #set remaining environment variables for Azure cmdlets
    $subscription = Get-AzureSubscription $selectedsubscription
    $subscriptionname = $subscription.subscriptionname
    $subscriptionid = $subscription.subscriptionid
    $slot = $environment

    #main driver - publish & write progress to activity log
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
    Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

    Publicar

    $deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
    $deploymentUrl = $deployment.Url

    Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."

## Pasos siguientes

Para habilitar la depuración remota al usar la entrega continua, vea [estas instrucciones][].

  [Entrega continua a Azure con Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso/
  [Paso 1: Configurar el servidor de compilación]: #step1
  [Paso 2: Compilar un paquete con los comandos de MSBuild]: #step2
  [Paso 3: Compilar un paquete con TFS Team Build (opcional)]: #step3
  [Paso 4: Publicar un paquete con un script de PowerShell]: #step4
  [Paso 5: Publicar un paquete con TFS Team Build (opcional)]: #step5
  [Configurar el Servicio de Team Foundation Build]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [herramientas de creación de Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Bibliotecas de Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Referencia de la línea de comandos de MSBuild]: http://msdn.microsoft.com/es-es/library/ms164311(v=VS.90).aspx
  [Referencia de la línea
  de comandos de MSBuild]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [Información sobre el sistema de compilación de Team Foundation]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Configuración de una máquina de compilación]: http://go.microsoft.com/fwlink/?LinkId=238799
  []: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [1]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [cmdlets de Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=256262
  [final de este artículo]: #script
  [2]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
  [estas instrucciones]: http://go.microsoft.com/fwlink/p/?LinkID=402354
