<properties urlDisplayName="Continuous Delivery" pageTitle="Entrega continua para servicios en la nube con TFS en Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous delivery PowerShell" description="Learn how to set up continuous delivery for Azure cloud apps. Code samples for MSBuild command-line statements and PowerShell scripts." metaCanonical="" services="" documentationCenter="" title="Continuous Delivery for Cloud Services in Azure" authors="kempb" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="12/3/2014" ms.author="kempb" />

# Entrega continua para Servicios en la nube de Azure

El proceso que se describe en este artículo muestra cómo se configura la entrega continua para las aplicaciones en la nube de Azure. Este proceso le permite crear paquetes automáticamente e implementarlos en Azure después de cada comprobación de código. El proceso de compilación del paquete que se describe en este artículo es equivalente al comando Package en Visual Studio y los pasos de publicación son equivalentes al comando Publish en Visual Studio. El artículo abarca los métodos que usaría para crear un servidor de compilación con instrucciones de línea de comandos de MSBuild y scripts de Windows PowerShell; además, demuestra también cómo configurar de manera opcional las definiciones de Visual Studio Team Foundation Server - Team Build para usar los comandos de MSBuild y los scripts de PowerShell. El proceso se puede personalizar para su entorno de compilación y los entornos de destino de Azure.

Para hacerlo de manera más fácil, puede también usar Visual Studio Online, una versión de TFS que se hospeda en Azure. Para obtener más información, consulte [Entrega continua a Azure con Visual Studio Online][].

Antes de comenzar, debe publicar su aplicación desde Visual Studio. Esto asegurará que todos los recursos estén disponibles e inicializados cuando intente automatizar el proceso de publicación.

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Configurar el servidor de compilación][]
-   [Paso 2: Compilar un paquete con los comandos de MSBuild][]
-   [Paso 3: Compilar un paquete con TFS Team Build (opcional)][]
-   [Paso 4: Publicar un paquete con un script de PowerShell][]
-   [Paso 5: Publicar un paquete con TFS Team Build (opcional)][]

<h2> <a name="step1"> </a>Paso 1: Configurar el servidor de compilación</h2>

Antes de crear un paquete de Azure con MSBuild, debe instalar el software y las herramientas necesarias en el servidor de compilación.

No es necesario instalar Visual Studio en el servidor de compilación. Si desea usar el servicio de Team Foundation Build para administrar el servidor de compilación, siga las instrucciones que aparecen en el documento [Configurar el Servicio de Team Foundation Build][].

1.  En el servidor de compilación, instale [.NET Framework 4][], [.NET Framework 4.5][] o [.NET Framework 4.5.2][], que incluye MSBuild.
2.  Instale las [herramientas de creación de Azure][] (busque WindowsAzureAuthoringTools-x86.msi o WindowsAzureAuthoringTools-x64.msi, según el procesador del servidor de compilación). Es posible que las versiones más antiguas de los archivos tenga WindowsAzure en el nombre de archivo.
3. Instale las [Bibliotecas de Azure][] (busque WindowsAzureLibsForNet-x86.msi o WindowsAzureLibsForNet-x64.msi).
4.  Copie el archivo Microsoft.WebApplication.targets desde una instalación de Visual Studio al servidor de compilación. En un equipo con Visual Studio instalado, el archivo se encuentra en el directorio C:\Program Files (x86)\MSBuild\Microsoft\VisualStudio\v11.0\WebApplications (v12.0 para Visual Studio 2013). Debe copiarlo en el mismo directorio en el servidor de compilación.
5.  Instale las [Herramientas de Azure para Visual Studio][]. Busque MicrosoftAzureTools.VS110.exe para generar proyectos de Visual Studio 2012, MicrosoftAzureTools.VS120.exe para generar proyectos de Visual Studio 2013 y MicrosoftAzureTools.VS140.exe para generar proyectos de Visual Studio 2015 Preview.

<h2><a name="step2"> </a>Paso 2: Compilar un paquete con los comandos de MSBuild</h2>

En esta sección se describe cómo construir un comando de MSBuild que compila un paquete de Azure. Ejecute este paso en el servidor de compilación para verificar que todo esté correctamente configurado y que el comando de MSBuild haga lo que usted desea. Puede agregar esta línea de comandos a los scripts de compilación existentes en el servidor de compilación o puede usar la línea de comandos en una definición de compilación de TFS, como se describe en la siguiente sección. Para obtener más información acerca de los parámetros de línea de comandos y MSBuild, consulte [Referencia de la línea de comandos de MSBuild][].

1.  Si Visual Studio está instalado en el servidor de compilación, haga clic en **Inicio**, **Todos los programas** y, a continuación, busque y haga clic en **Símbolo del sistema de Visual Studio** en la carpeta **Visual Studio Tools**.

    Si Visual Studio no está instalado en el servidor de compilación, abra un símbolo del sistema y asegúrese de que se pueda tener acceso a MSBuild.exe en la ruta. MSBuild se instala con .NET Framework en la ruta %WINDIR%\Microsoft.NET\Framework\*Versión*. Por ejemplo, para agregar MSBuild.exe a la variable de entorno PATH cuando tiene instalado .NET Framework 4, escriba el siguiente comando en el símbolo del sistema:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  En el símbolo del sistema, navegue hasta la carpeta que contiene el archivo de proyecto de Microsoft Azure que desea compilar.

3.  Ejecute msbuild con la opción /target:Publish como se muestra en el ejemplo siguiente:

        MSBuild /target:Publish

    Esta opción se puede abreviar como /t:Publish. La opción /t:Publish en MSBuild no debe confundirse con los comandos de publicación disponibles en Visual Studio cuando tiene instalado el SDK de Azure. La opción /t:Publish solo compila los paquetes de Azure. No implementa los paquetes como lo hacen los comandos de publicación en Visual Studio.

    De manera opcional, puede especificar el nombre del proyecto como un parámetro de MSBuild. Si no se especifica, se utiliza el directorio actual. Para obtener más información acerca de las opciones de línea de comandos de MSBuild, consulte Referencia de la línea de comandos de MSBuild][1].

4.  Busque el resultado. De manera predeterminada, este comando crea un directorio en relación con la carpeta raíz para el proyecto, como por ejemplo *ProjectDir*\\bin\\*Configuration*\\app.publish\\. Al compilar un proyecto de Azure, se generan dos archivos: el archivo del paquete mismo y el archivo de configuración que lo acompaña:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    De manera predeterminada, cada proyecto de Azure incluye un archivo de configuración del servicio (archivo .cscfg) para las compilaciones locales (depuración) y otro para las compilaciones en la nube (ensayo o producción), pero puede agregar o quitar archivos de configuración del servicio según sea necesario. Cuando compila un paquete dentro de Visual Studio, se le preguntará cuál archivo de configuración del servicio se debe incluir junto con el paquete.

5.  Especifique el archivo de configuración del servicio. Cuando compila un paquete con MSBuild, el archivo de configuración del servicio local se incluye de manera predeterminada. Para incluir un archivo de configuración del servicio diferente, establezca la propiedad TargetProfile del comando de MSBuild, como se muestra en el siguiente ejemplo:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Especifique la ubicación para el resultado. Establezca la ruta usando la opción /p:PublishDir=*Directory*\\, incluido el separador de barra diagonal inversa final, como en el siguiente ejemplo:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Después de que haya construido y probado una línea de comandos de MSBuild adecuada para compilar sus proyectos y combinarlos en un paquete de Azure, puede agregar esta línea de comandos a sus scripts de compilación. Si su servidor de compilación usa scripts personalizados, este proceso dependerá de los detalles de su proceso personalizado de compilación. Si va a usar TFS como un entorno de compilación, entonces puede seguir las instrucciones del paso siguiente para agregar la compilación del paquete de Azure a su proceso de compilación.

<h2> <a name="step3"> </a>Paso 3: Compilar un paquete con TFS Team Build (opcional)</h2>

Si tiene Team Foundation Server (TFS) configurado como un controlador de compilación y el servidor de compilación configurado como una máquina de compilación de TFS, entonces puede configurar una compilación automatizada para su paquete de Azure. Para obtener información acerca de como configurar y usar el servidor de Team Foundation como un sistema de compilación, consulte [Información sobre el sistema de compilación de Team Foundation][]. En particular, el procedimiento siguiente asume que ha configurado el servidor de compilación, tal como se ha descrito en [Configuración de una máquina de compilación][] y que ha creado un proyecto de equipo y un proyecto de servicio en la nube en el proyecto en equipo.

Para configurar TFS a fin de compilar paquetes de Azure, realice los siguientes pasos:

1.  En Visual Studio de su equipo de desarrollo, en el menú Ver, seleccione **Team Explorer** o elija Ctrl+\\, Ctrl+M. En la ventana de Team Explorer, expanda el nodo **Compilaciones** o elija la página **Compilaciones** y seleccione **Nueva definición de compilación**:

    ![][0]

2.  Haga clic en la pestaña **Desencadenador** y especifique las condiciones deseadas para cuando desea que se compile el paquete. Por ejemplo, especifique **Integración continua** para compilar el paquete cada vez que se produce una comprobación del control de código fuente.

3.	Elija la pestaña **Configuración de origen** y asegúrese que la carpeta del proyecto mostrada en la columna **Carpeta de control de código fuente** y que el estado es **Activo**.

4.  Seleccione la pestaña **Valores predeterminados de compilación** y, en el controlador de compilación, verifique el nombre del servidor de compilación.  Además, seleccione la opción **Copiar el resultado de la compilación en la siguiente carpeta** de entrega y especifique la ubicación de destino deseada.

5.  Haga clic en la pestaña **Proceso**. En la pestaña Proceso, elija la plantilla predeterminada, en **Compilación**, seleccione el proyecto si no está ya seleccionado y expanda la sección **Avanzado** en la sección **Compilación** de la cuadrícula. 

6.  Seleccione **Argumentos de MSBuild** y establezca los argumentos adecuados de la línea de comandos de MSBuild como se describe en el paso 2 anterior. Por ejemplo, escriba **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** para compilar un paquete y copiar los archivos del paquete en la ubicación
    \\\\myserver\\drops\\:

    ![][2]

    **Nota:** La copia de los archivos en un recurso compartido público facilita la implementación manual de los paquetes desde su equipo de desarrollo.

5.  Pruebe el éxito del paso de compilación protegiendo un cambio en su proyecto o ponga en cola una compilación nueva. Para poner en cola una compilación nueva, en Team Explorer, haga clic con el botón secundario en **Todas las definiciones de compilación** y, a continuación, seleccione **Poner nueva compilación en cola**.

<h2> <a name="step4"> </a>Paso 4: Publicar un paquete con un script de PowerShell</h2>

En esta sección se describen los pasos para construir un script de Windows PowerShell que publicará el resultado del paquete de aplicaciones en la nube en Azure con la ayuda de parámetros opcionales. Este script puede llamarse después del paso de compilación en su automatización de compilación personalizada. También puede llamarse desde las actividades de flujo de trabajo de la Plantilla de procesos en Visual Studio TFS Team Build.

1.  Instale los [cmdlets de Azure PowerShell][] (v0.6.1 o superior).     Durante la fase de instalación de cmdlet, seleccione instalar como complemento. Tenga en cuenta que esta versión oficialmente compatible reemplaza la versión anterior que se ofrece mediante CodePlex, aunque las versiones anteriores tenían la numeración 2.x.x.

2.  Inicie Azure PowerShell mediante el menú Inicio o la página de inicio. Si inicia de esta manera, se cargarán los cmdlets de Azure PowerShell.

3.  A la solicitud de PowerShell, verifique que se carguen los cmdlets de PowerShell al escribir el comando parcial Get-Azure y, a continuación, presionar la pestaña para completar la instrucción.

    Si presiona la tecla de tabulación repetidamente, verá varios comandos de Azure PowerShell.

4.  Verifique que se puede conectar a su suscripción de Azure al importar su información de suscripción desde el archivo .publishsettings.

    Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings

    A continuación, proporcione el comando

    Get-AzureSubscription

    De esta manera, podrá ver la información sobre su suscripción. Verifique que todo esté correcto.

4.  Guarde la plantilla de script que se proporciona al [final de este artículo ][]en su carpeta de scripts como c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Revise la sección de parámetros del script. Agregue o modifique cualquiera de los valores predeterminados. Estos valores siempre pueden omitirse al pasar parámetros explícitos.

6.  Asegúrese de que haya cuentas de almacenamiento y de servicio en la nube válidas creadas en su suscripción que se puedan abordar mediante el script de publicación. Se usará la cuenta de almacenamiento (almacenamiento de blobs) para cargar y almacenar temporalmente el paquete de implementación y el archivo de configuración mientras se crea la implementación.

    -   Para crear un servicio en la nube nuevo, puede llamar a este script o usar el Portal de administración de Azure. El nombre del servicio en la nube se usará como prefijo en un nombre de dominio completo y, por este motivo, debe ser único. 
            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Para crear una cuenta de almacenamiento nueva, puede llamar a este script o usar el Portal de administración de Azure. El nombre de la cuenta de almacenamiento se usará como prefijo en un nombre de dominio completo y, por este motivo, debe ser único. Puede intentar usar el mismo nombre que el servicio en la nube.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Llame al script directamente desde Azure PowerShell, o conecte este script a su automatización de compilación de host que se produce después de la compilación del paquete.

    **ADVERTENCIA:** El script siempre eliminará o reemplazará sus implementaciones actuales de manera predeterminada si se detectan. Esto es necesario para habilitar la entrega continua desde la automatización donde no es posible una solicitud del usuario.

    **Escenario de ejemplo 1:** implementación continua en el entorno de ensayo de un servicio:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    A esto le sigue generalmente una verificación de ejecución de prueba y un intercambio de VIP. El intercambio de VIP se puede realizar mediante el Portal de administración de Azure o con el cmdlet Move-Deployment.

    **Escenario de ejemplo 2:** implementación continua en el entorno de producción de un servicio de prueba dedicado

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Escritorio remoto:**

    Si se habilita Escritorio remoto en su proyecto de Azure, necesitará realizar algunos pasos únicos adicionales para asegurarse de que se cargue el certificado de servicio en la nube correcto en todos los servicios en la nube abordados por este script.

    Busque los valores de huella digital del certificado que esperan sus roles. Los valores de huella digital son visibles en la sección Certificados del archivo de configuración de la nube (es decir, ServiceConfiguration.Cloud.cscfg). También son visibles en el cuadro de diálogo Configuración de Escritorio remoto en Visual Studio cuando muestra las opciones y ve el certificado seleccionado.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Cargue los certificados de Escritorio remoto como un paso de configuración único usando el siguiente script de cmdlet:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Por ejemplo:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    De manera alternativa, puede exportar el archivo de certificado PFX con una clave privada y cargar los certificados en cada servicio en la nube objetivo mediante el Portal de administración de Azure. Lea el siguiente artículo para obtener más información:
    [http://msdn.microsoft.com/en-us/library/windowsazure/gg443832.aspx][].

    **Actualizar implementación frente a Eliminar implementación -> Nueva implementación**

    El script realizará de manera predeterminada una actualización de la implementación ($enableDeploymentUpgrade = 1) cuando no se pasa ningún parámetro o el valor 1 se pasa de manera explícita. Para las instancias simples, esto tiene la ventaja de tardar menos tiempo que una implementación completa. Para las instancias que requieren una alta disponibilidad, esto también tiene la ventaja de dejar algunas instancias en ejecución mientras otras se actualizan (recorriendo su dominio de actualización), además de que no se eliminará su VIP.

    La actualización de la implementación se puede deshabilitar en el script ($enableDeploymentUpgrade = 0) o al pasar -enableDeploymentUpgrade 0 como un parámetro, el cual alterará el comportamiento del script para primero eliminar cualquier implementación existente y luego crear una implementación nueva.

    **Advertencia:** El script siempre eliminará o reemplazará sus implementaciones actuales de manera predeterminada si se detectan. Esto es necesario para habilitar la entrega continua desde la automatización donde no es posible una solicitud del usuario/operador.

<h2><a name="step5"> </a>Paso 5: Publicar un paquete con TFS Team Build (opcional)</h2>

Este paso conectará TFS Team Build al script que se creó en el paso 4, el cual controla la publicación de la compilación del paquete en Azure. Esto conlleva modificar la Plantilla de proceso que usó su definición de compilación, de modo que ejecuta una actividad de publicación al final del flujo de trabajo. La actividad de publicación ejecutará su comando de PowerShell al pasar parámetros desde la compilación. El resultado de los objetivos de MSBuild y el script de publicación se canalizarán al resultado de la compilación estándar.

1.  Edite la definición de compilación a cargo de la implementación continua.

2.  Seleccione la pestaña **Proceso**.

3.	Siga [estas instrucciones](http://msdn.microsoft.com/library/dd647551.aspx) para agregar un proyecto de actividad a la plantilla del proceso de compilación, descargue la plantilla predeterminada, agréguela al proyecto y protéjala. Asigne un nuevo nombre a la plantilla del proceso de compilación, como AzureBuildProcessTemplate.

3.  Vuelva a la pestaña **Proceso** y utilice **Mostrar detalles** para mostrar una lista de las plantillas de proceso de compilación disponibles. Elija el botón **Nuevo...** y desplácese al proyecto que acaba de agregar y protéjalo. Localice la plantilla que acaba de crear y haga clic en **Aceptar**.

4.  Abra la Plantilla de proceso seleccionada para editar. Puede abrirla directamente en el diseñador de flujo de trabajo o en el editor XML para trabajar con el archivo XAML.

5.  Agregue la siguiente lista de argumentos nuevos como elementos de línea separados en la pestaña de argumentos del diseñador de flujo de trabajo. Todos los argumentos deben tener la dirección=In y el tipo=String. Estos se usarán para que los parámetros fluyan desde la definición de compilación hasta el flujo de trabajo, los cuales, posteriormente, se acostumbran a llamar al script de publicación.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][3]

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

    1.  Comience agregando una actividad de instrucción If para verificar un archivo de script válido. Establezca la condición según este valor:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  En el caso Then de la instrucción If, agregue una actividad de secuencia nueva. Establezca el nombre para mostrar en 'Iniciar publicación'

    3.  Con la secuencia Iniciar publicación todavía seleccionada, agregue la siguiente lista de variables nuevas como elementos de línea separados en la pestaña de variables del diseñador de flujo de trabajo. Todas las variables deben tener el tipo de variable =Cadena y el ámbito =Iniciar publicación. Estos se usarán para que los parámetros fluyan desde la definición de compilación hasta el flujo de trabajo, los cuales, posteriormente, se acostumbran a llamar al script de publicación.

        - SubscriptionDataFilePath, de tipo Cadena

        - PublishScriptFilePath, de tipo Cadena

            ![][4]

    4.  Si está utilizando TFS 2012 o anterior, agregue una actividad ConvertWorkspaceItem al comienzo de la nueva secuencia. Si está utilizando TFS 2013 o posterior, agregue una actividad GetLocalPath al comienzo de la nueva secuencia. Para una actividad ConvertWorkspaceItem, establezca las propiedades de la forma siguiente: Direction=ServerToLocal, DisplayName='Convert publish         script filename', Input=' PublishScriptLocation',         Result='PublishScriptFilePath', Workspace='Workspace'. Para una actividad GetLocalPath, establezca la propiedad IncomingPath en 'PublishScriptLocation' y el resultado en 'PublishScriptFilePath'. Esta actividad convierte la ruta al script de publicación desde las ubicaciones del servidor TFS (si corresponde) hasta una ruta de disco local estándar.

    5.  Si está utilizando TFS 2012 o anterior, agregue otra actividad ConvertWorkspaceItem al final de la nueva secuencia. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Si está utilizando TFS 2013 o posterior, agregue otro GetLocalPath. IncomingPath='SubscriptionDataFileLocation' y Result='SubscriptionDataFilePath.'

    6.  Agregue una actividad InvokeProcess al final de la secuencia nueva.         Esta actividad llama a PowerShell.exe con los argumentos pasados por la definición de compilación.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName =Ejecutar script de publicación

        3.  FileName = "PowerShell" (incluir las comillas)

        4.  OutputEncoding=
            System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  En el cuadro de texto de la sección **Controlar salida estándar** de InvokeProcess, establezca el valor del cuadro de texto en 'data'. Esta es una variable para almacenar los datos de salida estándar.

    8.  Agregue una actividad WriteBuildMessage justo debajo de la sección**Controlar salida estándar**. Establezca Importance =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' y Message='data'. Esto asegura que la salida estándar del script se escriba en la salida de la compilación.

    9.  En el cuadro de texto de la sección **Controlar salida de errores** de InvokeProcess, establezca el valor del cuadro de texto en 'data'. Esta es una variable para almacenar los datos de error estándar.

    10. Agregue una actividad WriteBuildError justo debajo de la sección**Controlar salida de errores**. Establezca el mensaje='data'. Esto asegura que los errores estándar del script se escriban en la salida de error de compilación.

	11. Corrija los errores, que se indican por signos de exclamación azules. Desplace el ratón sobre
		los signos de exclamación para obtener una sugerencia del error. Guarde el flujo de trabajo para 
		borrar los errores.

    El resultado final de las actividades de flujo de trabajo de publicación se verán en el diseñador de la manera siguiente:

    ![][5]

    El resultado final de las actividades de flujo de trabajo de publicación se verán en el archivo XAML de la manera siguiente:

		<If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
	        <If.Then>
	          <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
	            <Sequence.Variables>
	              <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
	              <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
	            </Sequence.Variables>
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
	            <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
	              <mtbwa:InvokeProcess.ErrorDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.ErrorDataReceived>
	              <mtbwa:InvokeProcess.OutputDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.OutputDataReceived>
	            </mtbwa:InvokeProcess>
	          </Sequence>
	        </If.Then>
	      </If>
	    </Sequence>


7.  Guarde el flujo de trabajo de la plantilla del proceso de compilación y proteja este archivo.

8.  Edite la definición de compilación (ciérrela si estuviera abierta) y seleccione el botón **Nuevo** si todavía no ve la nueva plantilla en la lista de plantillas del proceso.

9.  Establezca los valores de propiedad del parámetro en la sección Misc como a continuación:

    1.  CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'   
        *Este valor procede de:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'   
        *Este valor procede de: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'   
        *Use el nombre del servicios en la nube adecuado aquí*

    5.  Entorno = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'   
        *Use el nombre de cuenta de almacenamiento adecuado aquí*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. Guarde los cambios en la Definición de compilación.

11. Ponga una compilación en cola para ejecutar la compilación del paquete y la publicación. Si tiene un desencadenador establecido en Integración continua, ejecutará este comportamiento en cada protección.

### <a name="script"> </a>Plantilla de script PublishCloudService.ps1

<pre>
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
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
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

	write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
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
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
</pre>

## Pasos siguientes

Para habilitar la depuración remota al usar la entrega continua, consulte [estas instrucciones.](http://go.microsoft.com/fwlink/p/?LinkID=402354). 

  [Entrega continua a Azure con Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso/
  [Paso 1: Configurar el servidor de compilación]: #step1
  [Paso 2: Compilar un paquete con los comandos de MSBuild]: #step2
  [Paso 3: Compilar un paquete con TFS Team Build (opcional)]: #step3
  [Paso 4: Publicar un paquete con un script de PowerShell]: #step4
  [Paso 5: Publicar un paquete con TFS Team Build (opcional)]: #step5
  [Servicio de Team Foundation Build]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4,5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [.NET Framework 4.5.2]: http://go.microsoft.com/fwlink/?LinkId=521668
  [Herramientas de creación de Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Bibliotecas de Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Herramientas de Azure para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Referencia de la línea de comandos de MSBuild]: http://msdn.microsoft.com/en-us/library/ms164311(v=VS.90).aspx
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [Información sobre el sistema de Team Foundation Build]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Configuración de una máquina de compilación]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [Cmdlets de Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=256262
  [el archivo .publishsettings]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [al final de este artículo]: #script
  
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png

<!--HONumber=35.1-->
