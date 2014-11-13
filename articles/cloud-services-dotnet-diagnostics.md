<properties urlDisplayName="Diagnostics" pageTitle="Uso del diagn&oacute;stico (.NET) - Gu&iacute;a de caracter&iacute;sticas de Azure" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Aprenda a utilizar datos de diagn&oacute;stico en Azure para realizar tareas de depuraci&oacute;n, medici&oacute;n de rendimiento, supervisi&oacute;n, an&aacute;lisis de tr&aacute;fico y m&aacute;s." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Habilitaci&oacute;n de Diagn&oacute;sticos en Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure

Diagnósticos de Azure 1.2 le permite recopilar datos de diagnóstico desde un rol de trabajo,, un rol web o una máquina virtual que se ejecute en Azure. En esta guía se describe cómo usar Diagnósticos de Azure 1.2. Para obtener instrucciones más detalladas acerca de la creación de una estrategia de registro y seguimiento, así como del uso de los diagnósticos y de otras técnicas para solucionar problemas, consulte [Troubleshooting Best Practices for Developing Azure Applications][Troubleshooting Best Practices for Developing Azure Applications].

## Tabla de contenido

-   [Información general][Información general]
-   [Habilitar diagnósticos en un rol de trabajo][Habilitar diagnósticos en un rol de trabajo]
-   [Habilitar diagnósticos en una máquina virtual][Habilitar diagnósticos en una máquina virtual]
-   [Archivo de configuración y esquema de ejemplo][Archivo de configuración y esquema de ejemplo]
-   [Solución de problemas][Solución de problemas]
-   [Preguntas más frecuentes][Preguntas más frecuentes]
-   [Comparación entre Diagnósticos de Azure 1.0 y 1.2][Comparación entre Diagnósticos de Azure 1.0 y 1.2]
-   [Recursos adicionales][Recursos adicionales]

## <a name="overview"></a><span class="short-header">Información general</span>Información general

Diagnósticos de Azure 1.2 es una extensión de Azure que le permite recopilar datos de telemetría de diagnóstico desde un rol de trabajo,, un rol web o una máquina virtual que se ejecute en Azure. Los datos de telemetría se almacenan en una cuenta de almacenamiento de Azure y se pueden usar para la depuración y solución de problemas, medición del rendimiento, supervisión del uso de recursos, análisis del tráfico y planeación de la capacidad, y auditoría.

Si ha usado la versión 1.0 de Diagnósticos en el pasado, hay tres diferencias notables en comparación con Diagnósticos 1.2:

1.  Diagnósticos 1.2 se puede implementar en máquinas virtuales, así como en servicios en la nube.
2.  Diagnósticos 1.0 forma parte del SDK de Azure y se implementa al implementar su servicio en la nube. Diagnósticos 1.2 es una extensión y se implementa por separado.
3.  Diagnósticos 1.2 permite recopilar eventos EventSource de ETW y .NET.

Para obtener una comparación más detallada, vea [Comparación entre Diagnósticos de Azure 1.0 y 1.2][Comparación entre Diagnósticos de Azure 1.0 y 1.2] al final de este artículo.

Diagnósticos de Azure puede recopilar los tipos de telemetría siguientes:

|------------------------------------------------------|-------------------------------------------------------------------------------|
| **Origen de datos**                                  | **Descripción**                                                               |
| Registros IIS                                        | Información sobre los sitios web de IIS.                                      |
| Registros de infraestructura de diagnóstico de Azure | Información sobre los propios Diagnósticos.                                   |
| Registros de solicitudes con error de IIS            | Información sobre las solicitudes erróneas a un sitio o aplicación de IIS.    |
| Registros de eventos de Windows                      | Información enviada al sistema de registro de eventos de Windows.             |
| Contadores de rendimiento                            | Sistema operativo y contadores de rendimiento personalizados.                 |
| Volcados de memoria                                  | Información sobre el estado del proceso en caso de bloqueo de una aplicación. |
| Registros de errores personalizados                  | Archivos creados por su aplicación o servicio.                                |
| .NET EventSource                                     | Eventos generados por su código con la [clase EventSource][clase EventSource]de .NET.          |
| ETW basado en manifiesto                             | Eventos de ETW generados por cualquier proceso.                               |

## <a name="worker-role"></a><span class="short-header">Habilitar Diagnósticos en un rol de trabajo</span>Cómo habilitar diagnósticos en un rol de trabajo

En este tutorial se describe cómo implementar un rol de trabajo de Azure que emite datos de telemetría con la clase EventSource de .NET. Diagnósticos de Azure se usa para recopilar datos de telemetría y almacenarla en una cuenta de almacenamiento de Azure. Al crear un rol de trabajo, Visual Studio habilita automáticamente Diagnósticos 1.0 como parte de la solución. En las instrucciones siguientes se describe el proceso para crear el rol de trabajo, deshabilitar Diagnósticos 1.0 de la solución e implementar Diagnósticos 1.2 en el rol de trabajo.

### Requisitos previos

En este artículo se supone que tiene una suscripción a Azure y usa Visual Studio 2013 con el SDK de Azure. Si no dispone de una suscripción de Azure, puede registrarse para la [prueba gratuita][prueba gratuita]. Asegúrese de [instalar y configurar la versión 0.8.7 o una versión posterior de Azure PowerShell][instalar y configurar la versión 0.8.7 o una versión posterior de Azure PowerShell].

### Paso 1: Creación de roles de trabajo

1.  Inicie **Visual Studio 2013**.
2.  Cree un nuevo proyecto de **Servicio en la nube de Azure** desde la plantilla **Nube** que se dirija a .NET Framework 4.5. Asigne al proyecto el nombre “WadExample”.
3.  Seleccione **Rol de trabajo**.
4.  En el **Explorador de soluciones**, haga doble clic en el archivo de propiedades **WorkerRole1**.
5.  En la pestaña **Configuración**, desactive **Habilitar Diagnósticos** para deshabilitar Diagnósticos 1.0.
6.  Compile la solución para comprobar que no hay errores.

### Paso 2: Instrumentación del código

Reemplace el contenido de WorkerRole.cs por el código siguiente. La clase SampleEventSourceWriter, heredada de la [Clase EventSource][clase EventSource], implementa cuatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** y **HighFreq**. El primer parámetro del método **WriteEvent** define el identificador del evento respectivo. El método Run implementa un bucle infinito que llama a cada uno de los métodos de registro implementados en la clase **SampleEventSourceWriter** cada 10 segundos.

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Diagnostics;
    using System.Diagnostics.Tracing;
    using System.Net;
    using System.Threading;

    namespace WorkerRole1
    {
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections 
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
    }

### Paso 3: Implementación de roles de trabajo

1.  Implemente su rol de trabajo en Azure desde Visual Studio seleccionando el proyecto **WadExample** y **Publicar** en el menú **Compilar**.
2.  Elija su suscripción.
3.  En el cuadro de diálogo **Configuración de publicación de Microsoft Azure**, seleccione **<create new…>**.
4.  En el cuadro de diálogo **Crear servicio en la nube y cuenta de almacenamiento**, escriba un **Nombre** (por ejemplo, “WadExample”) y seleccione una región o un grupo de afinidad.
5.  Establezca el **Entorno** en **Ensayo**.
6.  Modifique cualquier otro parámetro de **Configuración** según sea necesario y haga clic en **Publicar**.
7.  Una vez finalizada la implementación, compruebe en el Portal de Azure que el servicio en la nube está en el estado **En ejecución**.

### Paso 4: Creación del archivo de configuración de Diagnósticos e instalación de la extensión

1.  Descargue la definición del esquema del archivo de configuración público ejecutando el comando de PowerShell siguiente:
2.  (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

3.  Agregue un archivo XML al proyecto **WorkerRole1** haciendo clic con el botón secundario en el proyecto **WorkerRole1** y seleccione **Agregar** -\> **Nuevo elemento…** -\> **Elementos de Visual C#** -\> **Datos** -\> **Archivo XML**. Asigne al archivo el nombre “WadExample.xml”.

    ![CloudServices\_diag\_add\_xml][CloudServices\_diag\_add\_xml]

4.  Asocie WadConfig.xsd al archivo de configuración. Asegúrese de que la ventana del editor de WadExample es la ventana activa. Presione **F4** para abrir la ventana **Propiedades**. Haga clic en la propiedad **Esquemas** de la ventana **Propiedades**. Haga clic en **...** en la propiedad **Esquemas**. Haga clic en el botón **Agregar...** y vaya a la ubicación en la que ha guardado el archivo XSD y seleccione el archivo WadConfig.xsd. Haga clic en **Aceptar**.
5.  Reemplace el contenido del archivo de configuración WadExample.xml por el siguiente archivo XM y guarde el archivo. Este archivo de configuración define un par de contadores de rendimiento para recopilar: uno para el uso de la CPU y uno para el uso de la memoria. A continuación, la configuración define los cuatro eventos correspondientes a los métodos de la clase SampleEventSourceWriter.

        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>

### Paso 5: Instalación de Diagnósticos en roles de trabajo

Los cmdlets de PowerShell para administrar Diagnósticos en un rol web o de trabajo son: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension y Remove-AzureServiceDiagnosticsExtension.

1.  Abra Windows Azure PowerShell.
2.  Ejecute el script para instalar Diagnósticos en su rol de trabajo (reemplace *StorageAccountKey* con la clave de cuenta de almacenamiento para la clave de almacenamiento wadexample):

        $storage_name = "wadexample"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
        $service_name="wadexample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging

### Paso 6: Consultar los datos de telemetría

En el **Explorador de servidores** de Visual Studio, vaya a la cuenta de almacenamiento wadexample. Cuando el servicio en la nube lleve ejecutándose unos 5 minutos, debería ver las tablas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** y **WADSetOtherTable**. Haga doble clic en una de las tablas para ver la telemetría que se ha recopilado.
 ![CloudServices\_diag\_tables][CloudServices\_diag\_tables]

## <a name="virtual-machine"></a><span class="short-header">Habilitar Diagnósticos en una máquina virtual</span>Cómo habilitar diagnósticos en una máquina virtual

En este tutorial se describe cómo instalar Diagnósticos de forma remota en una máquina virtual de Azure desde un equipo de desarrollo. También aprenderá a implementar una aplicación que se ejecuta en esa máquina virtual de Azure y emite datos de telemetría con la [clase EventSource][clase EventSource] de .NET. Diagnósticos de Azure se usa para recopilar la telemetría y almacenarla en una cuenta de almacenamiento de Azure.

### Requisitos previos

En este tutorial se supone que tiene una suscripción a Azure y usa Visual Studio 2013 con el SDK de Azure. Si no dispone de una suscripción de Azure, puede registrarse para la [prueba gratuita][prueba gratuita]. Asegúrese de [instalar y configurar la versión 0.8.7 o una versión posterior de Azure PowerShell][instalar y configurar la versión 0.8.7 o una versión posterior de Azure PowerShell].

### Paso 1: Creación de máquinas virtuales

1.  En el equipo del desarrollador, inicie Visual Studio 2013.
2.  En el **Explorador de servidores** de Visual Studio Server, haga clic con el botón secundario en **Windows Azure** y después seleccione **Máquina virtual** -\> **Crear máquina virtual**.
3.  Seleccione su suscripción de Azure en el cuadro de diálogo **Elegir una suscripción** y haga clic en **Siguiente**.
4.  Seleccione **Windows Server 2012 R2 Datacenter** en el cuadro de diálogo **Seleccionar una imagen de máquina virtual** y haga clic en **Siguiente**.
5.  En **Configuración básica de máquina virtual**, establezca el nombre de la máquina virtual en “wadexample”. Establezca su nombre de usuario y contraseña de administrador y haga clic en **Siguiente**.
6.  En el cuadro de diálogo **Configuración del servicio en la nube**, cree un nuevo servicio en la nube denominado “wadexampleVM”. Cree una nueva cuenta de almacenamiento con el nombre “wadexample” y haga clic en **Siguiente**.
7.  Haga clic en **Crear**.

### Paso 2: Creación de aplicaciones

1.  En el equipo del desarrollador, inicie Visual Studio 2013.
2.  Cree una aplicación de consola de Visual C# nueva dirigida a .NET Framework 4.5. Asigne al proyecto el nombre “WadExampleVM”.
    ![CloudServices\_diag\_new\_project][CloudServices\_diag\_new\_project]
3.  Reemplace el contenido de Program.cs por el código siguiente. La clase **SampleEventSourceWriter** implementa cuatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** y **HighFreq**. El primer parámetro del método WriteEvent define el identificador del evento respectivo. El método Run implementa un bucle infinito que llama a cada uno de los métodos de registro implementados en la clase **SampleEventSourceWriter** cada 10 segundos.

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
        {
        sealed class SampleEventSourceWriter : EventSource
        {
            public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
            public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
            public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
            public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
            public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

        }

        enum MyColor
        {
            Red,
            Blue,
            Green
        }

        [Flags]
        enum MyFlags
        {
            Flag1 = 1,
            Flag2 = 2,
            Flag3 = 4
        }

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
        }
        }

4.  Guarde el archivo y seleccione **Compilar solución** en el menú **Compilar** para compilar el código.

### Paso 3: Implementación de aplicaciones

1.  Haga clic con el botón secundario en el proyecto **WadExampleVM** del **Explorador de soluciones** y seleccione **Abrir carpeta** en el Explorador de archivos.
2.  Vaya a la carpeta *bin\\Debug* y copie todos los archivos (WadExampleVM.\*)
3.  En el **Explorador de servidores**, haga clic con el botón secundario en la máquina virtual y elija **Conectar utilizando Escritorio remoto**.
4.  Una vez conectado a la máquina virtual, cree una carpeta con el nombre WadExampleVM y pegue los archivos de la aplicación en la carpeta.
5.  Inicie la aplicación WadExampleVM.exe. Debería ver una ventana de consola en blanco.

### Paso 4: Creación de la configuración de Diagnósticos e instalación de la extensión

1.  Descargue la definición del esquema del archivo de configuración público en su equipo de desarrollo ejecutando el comando de PowerShell siguiente:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.  Abra un nuevo archivo XML en Visual Studio, ya sea en un proyecto que ya tenga abierto o en una instancia de Visual Studio sin proyectos abiertos. En Visual Studio, seleccione **Agregar** -\> **Nuevo elemento…** -\> **Elementos de Visual C#** -\> **Datos** -\> **Archivo XML**. Asigne al archivo el nombre “WadExample.xml”
3.  Asocie WadConfig.xsd al archivo de configuración. Asegúrese de que la ventana del editor de WadExample es la ventana activa. Presione **F4** para abrir la ventana **Propiedades**. Haga clic en la propiedad **Esquemas** de la ventana **Propiedades**. Haga clic en **...** en la propiedad **Esquemas**. Haga clic en el botón **Agregar...** y vaya a la ubicación en la que ha guardado el archivo XSD y seleccione el archivo WadConfig.xsd. Haga clic en **Aceptar**.
4.  Reemplace el contenido del archivo de configuración WadExample.xml por el siguiente archivo XM y guarde el archivo. Este archivo de configuración define un par de contadores de rendimiento para recopilar: uno para el uso de la CPU y uno para el uso de la memoria. A continuación, la configuración define los cuatro eventos correspondientes a los métodos de la clase SampleEventSourceWriter.

        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>

### Paso 5: Instalación de Diagnósticos de forma remota en la máquina virtual de Azure

Los cmdlets de PowerShell para administrar Diagnósticos en una máquina virtual son: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension y Remove-AzureVMDiagnosticsExtension.

1.  En el equipo del desarrollador, abra Windows Azure PowerShell.
2.  Ejecute el script para instalar de forma remota Diagnósticos en su máquina virtual (reemplace *StorageAccountKey* con la clave de cuenta de almacenamiento para la clave de almacenamiento wadexample):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### Paso 6: Consultar los datos de telemetría

En el **Explorador de servidores** de Visual Studio, vaya a la cuenta de almacenamiento wadexample. Cuando la máquina virtual lleve ejecutándose unos 5 minutos, debería ver las tablas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** y **WADSetOtherTable**. Haga doble clic en una de las tablas para ver la telemetría que se ha recopilado.
 ![CloudServices\_diag\_wadexamplevm\_tables][CloudServices\_diag\_wadexamplevm\_tables]

## <a name="configuration-file-schema"></a><span class="short-header">Esquema y archivo de configuración de ejemplo</span>

El archivo de configuración de Diagnósticos define valores que se usan para inicializar la configuración de diagnóstico al iniciar el monitor de diagnósticos. El archivo de configuración de ejemplo y la documentación detallada se ubican aquí: [Esquema de configuración de Diagnósticos de Azure 1.2][Esquema de configuración de Diagnósticos de Azure 1.2].

## <a name="troubleshooting"></a><span class="short-header">Solución de problemas</span>Solución de problemas

### Diagnósticos de Azure no se inicia

Diagnósticos está formado por dos componentes: Un complemento del agente invitado y el agente de supervisión. Los archivos de registro para el complemento del agente invitado se ubican en el archivo:

*%SystemDrive%\\ WindowsAzure\\Logs\\Plugins\\Microsoft.Azure.Diagnostics.PaaSDiagnostics\\<diagnosticsversion>*\\CommandExecution.log

El complemento devuelve los códigos de error siguientes:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Código de salida</strong></td>
<td align="left"><strong>Descripción</strong></td>
</tr>
<tr class="even">
<td align="left">0</td>
<td align="left">Correcta.</td>
</tr>
<tr class="odd">
<td align="left">-1</td>
<td align="left">Error genérico.</td>
</tr>
<tr class="even">
<td align="left">-2</td>
<td align="left"><p>No se puede cargar el archivo rcf.</p>
<p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual.</p></td>
</tr>
<tr class="odd">
<td align="left">-3</td>
<td align="left"><p>No se puede cargar el archivo de configuración de Diagnósticos.</p>
<p>Solución: Esto se debe a que un archivo de configuración no supera la validación del esquema. La solución es proporcionar un archivo de configuración que cumpla el esquema.</p></td>
</tr>
<tr class="even">
<td align="left">-4</td>
<td align="left"><p>Ya hay otra instancia de Diagnósticos del agente de supervisión que usa el directorio de recursos local.</p>
<p>Solución: Especifique un valor distinto para <strong>LocalResourceDirectory</strong>.</p></td>
</tr>
<tr class="odd">
<td align="left">-6</td>
<td align="left"><p>El iniciador del complemento del agente invitado intentó iniciar Diagnósticos con una línea de comandos no válida.</p>
<p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual.</p></td>
</tr>
<tr class="even">
<td align="left">-10</td>
<td align="left">El complemento Diagnósticos se cerró con una excepción no controlada.</td>
</tr>
<tr class="odd">
<td align="left">-11</td>
<td align="left"><p>El agente invitado no pudo crear el proceso encargado de iniciar y supervisar al agente de supervisión.</p>
<p>Solución: Compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos.</p></td>
</tr>
<tr class="even">
<td align="left">-101</td>
<td align="left"><p>Argumentos no válidos al llamar al complemento Diagnósticos.</p>
<p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual.</p></td>
</tr>
<tr class="odd">
<td align="left">-102</td>
<td align="left"><p>El proceso del complemento no se puede iniciar por sí mismo.</p>
<p>Solución: Compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos.</p></td>
</tr>
<tr class="even">
<td align="left">-103</td>
<td align="left"><p>El proceso del complemento no se puede iniciar por sí mismo. Concretamente, no puede crear el objeto del registrador.</p>
<p>Solución: Compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos.</p></td>
</tr>
<tr class="odd">
<td align="left">-104</td>
<td align="left"><p>No se puede cargar el archivo rcf proporcionado por el agente invitado.</p>
<p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual.</p></td>
</tr>
<tr class="even">
<td align="left">-105</td>
<td align="left"><p>El complemento Diagnósticos no puede abrir el archivo de configuración de Diagnósticos.</p>
<p>Este es un error interno que solo debería ocurrir si el complemento Diagnósticos se invoca manualmente de forma incorrecta en la máquina virtual.</p></td>
</tr>
<tr class="odd">
<td align="left">-106</td>
<td align="left"><p>No se puede leer el archivo de configuración de Diagnósticos.</p>
<p>Solución: Esto se debe a que un archivo de configuración no supera la validación del esquema. Por lo tanto, la solución es proporcionar un archivo de configuración que cumpla el esquema. Puede ver que el archivo XML se entrega a la extensión Diagnósticos en la carpeta <em>%SystemDrive%\WindowsAzure\Config</em> de la máquina virtual. Abra el archivo XML en cuestión y busque <strong>Microsoft.Azure.Diagnostics</strong> y, a continuación, en el campo <strong>xmlCfg</strong>. Los datos están codificados en base 64, por lo que tendrá que <a href="http://www.bing.com/search?q=base64+decoder">descodificarlos</a> para ver el archivo XML que cargó Diagnósticos.</p></td>
</tr>
<tr class="even">
<td align="left">-107</td>
<td align="left"><p>El directorio de recursos pasado al agente de supervisión no es válido.</p>
<p>Este es un error interno que solo debería ocurrir si el agente se invoca manualmente de forma incorrecta en la máquina virtual.</p></td>
</tr>
<tr class="odd">
<td align="left">-108</td>
<td align="left"><p>No se puede convertir el archivo de configuración de Diagnósticos al archivo de configuración del agente de supervisión.</p>
<p>Este es un error interno que solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido.</p></td>
</tr>
<tr class="even">
<td align="left">-110</td>
<td align="left"><p>Error de configuración general de Diagnósticos.</p>
<p>Este es un error interno que solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido.</p></td>
</tr>
<tr class="odd">
<td align="left">-111</td>
<td align="left"><p>No se puede iniciar el agente de supervisión.</p>
<p>Solución: Compruebe que hay suficientes recursos del sistema disponibles.</p></td>
</tr>
<tr class="even">
<td align="left">-112</td>
<td align="left">Error general</td>
</tr>
</tbody>
</table>

### Los datos de diagnóstico no se registran en el almacenamiento

La causa más común de la falta de datos de evento es la definición incorrecta de la información de la cuenta de almacenamiento.

Solución: Corrija el archivo de configuración de Diagnósticos y reinstale Diagnósticos.
Antes de que los datos de evento se carguen en la cuenta de almacenamiento, se almacenan en la carpeta. Vea arriba los detalles sobre **LocalResourceDirectory**.

Si no hay archivos en esta carpeta, el agente de supervisión no se puede iniciar. Esto suele estar causado por un archivo de configuración no válido y se debe notificar en el CommandExecution.log. Si el agente de supervisión recopila correctamente datos de evento, verá archivos .tsf para cada evento definido en el archivo de configuración.

El agente de supervisión registra los errores que experimenta en el archivo MaEventTable.tsf. Para inspeccionar el contenido de este archivo, ejecute el comando siguiente:

        %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics.2.0.0\Monitor\x64\table2csv maeventtable.tsf

La herramienta genera un archivo con el nombre maeventtable.csv que puede abrir y cuyos registros de errores puede inspeccionar.

## <a name="faq"></a><span class="short-header">P+F</span>Preguntas más frecuentes

Tenga en cuenta las siguientes preguntas más frecuentes y sus respuestas:

**P:** ¡Cómo actualizado mi solución de Visual Studio de Diagnósticos de Azure 1.0 a Diagnósticos de Azure 1.1?

**R:** La actualización de la solución de Visual Studio de Diagnósticos 1.0 a Diagnósticos 1.1 (o posterior) es un proceso manual:

-   Deshabilite Diagnósticos en la solución de Visual Studio para evitar que Diagnósticos 1.0 se implemente con su rol.
-   Si su código usa el agente de escucha, deberá modificar el código para usar EventSource de .NET. Diagnósticos 1.1 y las versiones posteriores no admiten el agente de escucha.
-   Modificar el proceso de instalación para instalar la extensión Diagnósticos 1.1

**P:** Si ya tengo instalada la extensión de Diagnósticos 1.1 en mi rol o máquina virtual, ¿cómo actualizado a Diagnósticos 1.2?

**R:** Si especificó “–Versión “1.*”" al instalar Diagnósticos 1.1, la próxima vez que se reinicie el rol o la máquina virtual, se actualizará automáticamente a la versión más reciente que coincida con la expresión regular “1.*” Si se especificó “–Versión “1.1”” al instalar Diagnósticos 1.1, puede actualizar a una versión más reciente volviendo a ejecutar el cmdlet Set- y especificando la versión que desea instalar.

**P:** ¿Cómo se denominan las tablas?

**R:** Las tablas se denominan teniendo en cuenta lo siguiente:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Aquí tiene un ejemplo:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Esto generará 4 tablas:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Evento**

</td>
<td>
**Nombre de la tabla**

</td>
</tr>
<tr>
<td>
provider=”prov1” \<Event id=”1” /\>

</td>
<td>
WADEvent+MD5(“prov1”)+”1”

</td>
</tr>
<tr>
<td>
provider=”prov1” \<Event id=”2” eventDestination=”dest1” /\>

</td>
<td>
WADdest1

</td>
</tr>
<tr>
<td>
provider=”prov1” \<DefaultEvents /\>

</td>
<td>
WADDefault+MD5(“prov1”)

</td>
</tr>
<tr>
<td>
provider=”prov2” \<DefaultEvents eventDestination=”dest2” /\>

</td>
<td>
WADdest2

</td>
</tr>
</table>
</tbody>
## <a name="comparing"></a><span class="short-header">Diagnósticos de Azure 1.0 frente a 1.2</span>Comparación entre Diagnósticos de Azure 1.0 y 1.2

En la tabla siguiente se comparan las características que admiten las versiones 1.0 y 1.1/1.2 de Diagnósticos de Azure:

|------------------------------|----------------------|--------------------------|
| **Tipos de roles admitidos** | **Diagnósticos 1.0** | **Diagnósticos 1.1/1.2** |
| Rol web                      | Sí                   | Sí                       |
| Rol de trabajo               | Sí                   | Sí                       |
| IaaS                         | No                   | Sí                       |

|--------------------------------------------------------------------------------------------------------------|----------------------|--------------------------|
| **Configuración e implementación**                                                                           | **Diagnósticos 1.0** | **Diagnósticos 1.1/1.2** |
| Integración con Visual Studio: integrado en la experiencia de desarrollo de web/trabajo de Azure.            | Sí                   | No                       |
| Scripts de PowerShell: scripts para administrar la instalación y la configuración de Diagnósticos en el rol. | Sí                   | Sí                       |

|------------------------------------------------------|---------------------------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------|--------------------------|
| **Origen de datos**                                  | **Recopilación predeterminada** | **Formato** | **Descripción**                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | **Diagnósticos 1.0** | **Diagnósticos 1.1/1.2** |
| Registros System.Diagnostics.Trace                   | Sí                              | Tabla       | Registra mensajes de seguimiento enviados desde el código el agente de escucha de seguimiento (se debe agregar un agente de escucha de seguimiento al archivo web.config o app.config). Los datos de registro se transferirán según el intervalo de transferencia scheduledTransferPeriod a la tabla de almacenamiento WADLogsTable.                                                                                                                                            | Sí                   | No (use EventSource)     |
| Registros IIS                                        | Sí                              | Blob        | Registra información sobre los sitios de IIS. Los datos de registro se transferirán según el intervalo de transferencia scheduledTransferPeriod al contenedor que especifique.                                                                                                                                                                                                                                                                                                  | Sí                   | Sí                       |
| Registros de infraestructura de diagnóstico de Azure | Sí                              | Tabla       | Registra información sobre la infraestructura de diagnóstico, el módulo RemoteAccess y el módulo RemoteForwarder. Los datos de registro se transferirán según el intervalo scheduledTransferPeriodtransfer a la tabla de almacenamiento WADDiagnosticInfrastructureLogsTable.                                                                                                                                                                                                   | Sí                   | Sí                       |
| Registros de solicitudes con error de IIS            | No                              | Blob        | Registra información sobre las solicitudes erróneas a un sitio o aplicación de IIS. También debe realizar la habilitación estableciendo opciones de seguimiento bajo system.WebServer en Web.config. Los datos de registro se transferirán según el intervalo de transferencia scheduledTransferPeriod al contenedor que especifique.                                                                                                                                           | Sí                   | Sí                       |
| Registros de eventos de Windows                      | No                              | Tabla       | Registra información acerca del funcionamiento del sistema operativo, la aplicación o el controlador. Los contadores de rendimiento deben especificarse explícitamente. Cuando se agregan, los datos de los contadores de rendimiento se transferirán según el intervalo de transferencia scheduledTransferPeriod a la tabla de almacenamiento WADPerformanceCountersTable.                                                                                                     | Sí                   | Sí                       |
| Contadores de rendimiento                            | No                              | Tabla       | Registra información acerca del funcionamiento del sistema operativo, la aplicación o el controlador. Los contadores de rendimiento deben especificarse explícitamente. Cuando se agregan, los datos de los contadores de rendimiento se transferirán según el intervalo de transferencia scheduledTransferPeriod a la tabla de almacenamiento WADPerformanceCountersTable.                                                                                                     | Sí                   | Sí                       |
| Volcados de memoria                                  | No                              | Blob        | Registra información sobre el estado del sistema operativo en caso de que se produzca un error del sistema. Los minivolcados de memoria se recopilan localmente. Se pueden habilitar los volcados completos. Los datos de registro se transferirán según el intervalo de transferencia scheduledTransferPeriod al contenedor que especifique. Puesto que ASP.NET controla la mayoría de las excepciones, normalmente solo es útil para un rol de trabajo o una máquina virtual. | Sí                   | Sí                       |
| Registros de errores personalizados                  | No                              | Blob        | Cuando se usan recursos de almacenamiento local, se pueden registrar y transferir inmediatamente datos personalizados al contenedor especificado.                                                                                                                                                                                                                                                                                                                               | Sí                   | Sí                       |
| EventSource                                          | No                              | Tabla       | Registra eventos generados por su código con la clase EventSourcede de .NET.                                                                                                                                                                                                                                                                                                                                                                                                    | No                   | Sí                       |
| ETW basado en manifiesto                             | No                              | Tabla       | Eventos de ETW generados por cualquier proceso.                                                                                                                                                                                                                                                                                                                                                                                                                                 | No                   | Sí                       |

## <a name="additional"></a><span class="short-header">Recursos adicionales</span>Recursos adicionales

-   [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure][Troubleshooting Best Practices for Developing Azure Applications]
-   [Recopilar datos de registro mediante Diagnósticos de Azure][Recopilar datos de registro mediante Diagnósticos de Azure]
-   [Depurar servicios en la nube][Depurar servicios en la nube]
-   [Configurar Diagnósticos de Azure][Configurar Diagnósticos de Azure]

  [Troubleshooting Best Practices for Developing Azure Applications]: http://msdn.microsoft.com/es-es/library/windowsazure/hh771389.aspx
  [Información general]: #overview
  [Habilitar diagnósticos en un rol de trabajo]: #worker-role
  [Habilitar diagnósticos en una máquina virtual]: #virtual-machine
  [Archivo de configuración y esquema de ejemplo]: #configuration-file-schema
  [Solución de problemas]: #troubleshooting
  [Preguntas más frecuentes]: #faq
  [Comparación entre Diagnósticos de Azure 1.0 y 1.2]: #comparing
  [Recursos adicionales]: #additional
  [clase EventSource]: http://msdn.microsoft.com/es-es/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  [prueba gratuita]: http://azure.microsoft.com/es-es/pricing/free-trial/
  [instalar y configurar la versión 0.8.7 o una versión posterior de Azure PowerShell]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/
  [CloudServices\_diag\_add\_xml]: ./media/cloud-services-dotnet-diagnostics/AddXmlFile.png
  [CloudServices\_diag\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleTables.png
  [CloudServices\_diag\_new\_project]: ./media/cloud-services-dotnet-diagnostics/NewProject.png
  [CloudServices\_diag\_wadexamplevm\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png
  [Esquema de configuración de Diagnósticos de Azure 1.2]: http://msdn.microsoft.com/es-es/library/azure/dn782207.aspx
  [Recopilar datos de registro mediante Diagnósticos de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433048.aspx
  [Depurar servicios en la nube]: http://msdn.microsoft.com/es-es/library/windowsazure/ee405479.aspx
  [Configurar Diagnósticos de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/dn186185.aspx
