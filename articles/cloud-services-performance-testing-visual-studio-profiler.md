<properties linkid="dev-net-common-tasks-profiling-in-compute-emulator" urldisplayname="Team Foundation Service" headerexpose="" pageTitle="Profiling a Cloud Service Locally in the Compute Emulator" metakeywords="" footerexpose="" description="" umbraconavihide="0" disquscomments="1" title="Testing the Performance of a Cloud Service Locally in the Azure Compute Emulator Using the Visual Studio Profiler" authors="ghogen" manager="douge" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# Prueba del rendimiento de un servicio en la nube de manera local en el emulador de proceso de Azure con el generador de perfiles de Visual Studio

Hay disponible una variedad de aplicaciones y técnicas para realizar pruebas del rendimiento de los servicios en la nube.
Al publicar un servicio en la nube en Azure, puede usar Visual Studio para recopilar datos para la generación de
perfiles y luego analizarlos localmente, como se describe en [Probar el rendimiento de un servicio en la nube][Probar el rendimiento de un servicio en la nube].
También puede usar los diagnósticos para hacer un seguimiento de una variedad de
, tal como se describe en [Uso de contadores de rendimiento en Azure][Uso de contadores de rendimiento en Azure].
También es posible que desee generar un perfil de la aplicación localmente en el emulador de proceso antes de implementarlo en la nube.

Este artículo abarca el método de muestreo de CPU de la generación de perfiles, que se puede realizar localmente en el emulador. El muestreo de CPU es un método para generar perfiles que no es muy intrusivo. A un intervalo de muestreo designado, el generador de perfiles realiza una instantánea de la pila de llamadas. Los datos se recopilan por un lapso de tiempo y se muestran en un informe. Este método de generación de perfiles tiende a indicar dónde se está realizando la mayoría del trabajo de la CPU en una aplicación informáticamente intensiva. Esto le da la oportunidad de centrarse en la "ruta de acceso activa" donde su aplicación pasa la mayor parte del tiempo.

## Requisitos previos

Puede ejecutar el generador de perfiles de manera local solo si tiene Visual Studio Premium o Visual Studio Ultimate.

## En este artículo:

-   [Paso 1: Configuración de Visual Studio para la generación de perfiles][Paso 1: Configuración de Visual Studio para la generación de perfiles]

-   [Paso 2: Asociación a un proceso][Paso 2: Asociación a un proceso]

-   [Paso 3: Vista de informes de generación de perfiles][Paso 3: Vista de informes de generación de perfiles]

-   [Paso 4: Realización de cambios y comparación del rendimiento][Paso 4: Realización de cambios y comparación del rendimiento]

-   [Solución de problemas][Solución de problemas]

-   [Pasos siguientes][Pasos siguientes]

## <a name="step1"> </a>Paso 1: Configuración de Visual Studio para la generación de perfiles

Primero, existen unas pocas opciones de configuración de Visual Studio que podrían ser útiles para la generación de perfiles. Para que los informes de generación de perfiles tengan sentido, necesitará símbolos (archivos .pdb) para su aplicación y también símbolos para las bibliotecas del sistema. Necesitará asegurarse de que hace referencia a los servidores de símbolos disponibles. Para hacer esto, en el menú **Herramientas** en Visual Studio, seleccione **Opciones**, luego **Depuración** y, a continuación, **Símbolos**. Asegúrese de que los servidores de símbolos de Microsoft aparezcan en **Ubicaciones del archivo de símbolos (.pdb)**. Puede también hacer referencia a <http://referencesource.microsoft.com/symbols> el cual podría tener archivos de símbolo adicionales.

![][4]

Si lo desea, puede simplificar los informes que genera el generador de perfiles al configurar Solo mi código. Si Solo mi código está habilitado, las pilas de llamadas de función se simplifican de modo que las llamadas que son completamente internas para las bibliotecas y el .NET Framework están ocultas de los informes. En el menú **Herramientas**, seleccione **Opciones**. A continuación, expanda el nodo **Herramientas de rendimiento** y seleccione **General**. Seleccione la casilla **Habilitar Solo mi código para informes del generador de perfiles**.

![][1]

Puede usar estas instrucciones con un proyecto existente o con un proyecto nuevo. Si crea un proyecto nuevo para aprobar las técnicas que se describen a continuación, seleccione un proyecto de C# **Servicio de nube de Azure** y seleccione un **Rol web** y un **Rol de trabajo**.

![][2]

Para propósitos de ejemplo, agregue parte del código al proyecto que tarda
demasiado tiempo y demuestra un problema de rendimiento obvio. Por ejemplo, agregue el siguiente código a un proyecto de rol de trabajo:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = ""
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Llame a este código desde el método de ejecución en la clase derivada de RoleEntryPoint del rol de trabajo.

    public override void Run()
    {
        while (true)
        {
            Concatenator.Concatenate(10000);
        }
    }

Compile y ejecute su servicio en la nube localmente con la configuración de solución establecida en **Liberar**. Esto asegura que todos los archivos y carpetas se
crean para ejecutar la aplicación localmente y asegura que se inicien todos los emuladores.

## <a name="step2"> </a>Paso 2: Asociación a un proceso

En vez de generar un perfil en la aplicación al iniciarla desde Visual Studio 2010 IDE, debe asociar el generador de perfiles a un proceso en ejecución.

Para asociar el generador de perfiles a un proceso, en el menú **Analizar**, seleccione **Generador de perfiles** y **Asociar/Desasociar**.

![][3]

Para un rol de trabajo, busque el proceso WaWorkerHost.exe.

![][4]

Si la carpeta de su proyecto se encuentra en una unidad de red, el generador de perfiles le pedirá proporcionar otra ubicación para guardar los informes de generación de perfiles.

Se puede también asociar a un rol web asociándose al archivo WaIISHost.exe.
 Si hay varios procesos de rol de trabajo en su aplicación, necesita usar processID para distinguirlos. Puede consultar el processID de manera programática al tener acceso al objeto del proceso. Por ejemplo, si agrega este código al método Run de la clase derivada de RoleEntryPoint en un rol, puede ver el
registro en la interfaz de usuario del emulador de proceso para conocer el proceso al que debe conectarse.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Para ver el registro, inicie la interfaz de usuario del emulador de proceso.

![][5]

Abra la ventana de consola de registro del rol de trabajo en la interfaz de usuario del emulador de proceso al hacer clic en la barra de título de la ventana de la consola. Puede ver el identificador de proceso en el registro.

![][6]

Después de que se haya asociado, realice los pasos en la interfaz de usuario de su aplicación (si es necesario) para reproducir el escenario.

Cuando desee detener la generación de perfiles, seleccione el vínculo **Detener generación de perfiles**.

![][7]

## <a name="step3"> </a>Paso 3: Vista de informes de rendimiento

Aparece el informe de rendimiento de la aplicación.

En este punto, el generador de perfiles detiene su ejecución, guarda los datos en un archivo .vsp y exhibe un informe
que muestra un análisis de estos datos.

![][8]

Si ve String.wstrcpy en la ruta de acceso activa, haga clic en Solo mi código para cambiar la vista a fin de mostrar el código de usuario solamente. Si ve String.Concat, intente presionar el botón Mostrar todo el código.

Debería ver el método Concatenate y String.Concat que ocupan una gran parte
del tiempo de ejecución.

![][9]

Si agregó el código de concatenación de cadena en este artículo, debería ver una advertencia en la lista de tareas para esta. Es posible que también vea una advertencia de que hay una cantidad excesiva de elementos no utilizados, lo que se debe a la cantidad de cadenas que se desplegaron.

![][10]

## <a name="step4"> </a>Paso 4: Realización de cambios y comparación del rendimiento

Puede también comparar el rendimiento antes y después de un cambio en el código. Edite el código para reemplazar la operación de concatenación de cadena con el uso de StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Realice otra ejecución de rendimiento y, a continuación, compárelo. En el Explorador de rendimiento, si las ejecuciones se encuentran en la misma sesión, simplemente puede seleccionar ambos informes, abrir el menú de acceso directo y seleccionar **Comparar informes de rendimiento**. Si desea realizar una comparación con una ejecución en otra sesión de rendimiento, abra el menú **Analizar** y seleccione **Comparar informes de rendimiento**. En el cuadro de diálogo que aparece, especifique ambos archivos.

![][11]

Los informes resaltan las diferencias entre las dos ejecuciones.

![][12]

¡Enhorabuena! Ya ha empezado a usar el generador de perfiles.

## <a name="troubleshooting"> </a> Solución de problemas

-   Asegúrese de que va a generar un perfil de una compilación de versión.

-   Si la opción Asociar/Desasociar no está habilitada en el menú del Generador de perfiles, ejecute el Asistente de rendimiento.

-   Use la interfaz de usuario del emulador de proceso para ver el estado de la aplicación.

-   Si tiene problemas al iniciar aplicaciones en el emulador o asociar el generador de perfiles, apague el emulador de proceso y reinícielo. Si el problema no se soluciona, intente reiniciar. Este problema se produce si usa el emulador de proceso para suspender y quitar implementaciones en ejecución.

-   Si ha utilizado cualquiera de los comandos de generación
    de perfiles desde la línea de comandos, especialmente la configuración global, asegúrese de que se haya llamado a VSPerfClrEnv /globaloff y que VsPerfMon.exe se haya apagado.

-   Si durante el muestreo ve el mensaje "PRF0025: No se recopilaron datos", compruebe que el proceso al que se asoció tiene actividad de CPU. Es posible que las aplicaciones que no están realizando ningún trabajo informático no produzcan datos de muestreo. También es posible que el proceso haya finalizado antes de que se haya realizado muestreo alguno. Compruebe que el método de ejecución de un rol para el cual está generando un perfil no termine.

## <a name="nextSteps"> </a>Pasos siguientes

La instrumentación de binarios de Azure en el emulador no es compatible en el generador de perfiles de Visual Studio 2010; sin embargo, si desea probar la asignación de memoria, puede seleccionar esta opción al generar perfiles. También puede seleccionar una generación de perfiles de concurrencia, la cual le ayuda a determinar si los subprocesos están desperdiciando tiempo al competir por bloqueos, o la generación de perfiles de interacción de capa, que le ayuda a hacer un seguimiento de los problemas de rendimiento cuando se interactúa entre las capas de una aplicación, con mayor frecuencia entre la capa de datos y un rol de trabajo. Puede ver las consultas de la base de datos que genera la aplicación y usar los datos de generación de perfiles para mejorar el uso que hace de la base de datos. Para obtener información acerca de la generación de perfiles de interacción de capa, consulte [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010][Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010].

  [Probar el rendimiento de un servicio en la nube]: http://msdn.microsoft.com/es-es/library/windowsazure/hh369930.aspx
  [Uso de contadores de rendimiento en Azure]: http://www.windowsazure.com/es-es/develop/net/common-tasks/performance-profiling
  [Paso 1: Configuración de Visual Studio para la generación de perfiles]: #step1
  [Paso 2: Asociación a un proceso]: #step2
  [Paso 3: Vista de informes de generación de perfiles]: #step3
  [Paso 4: Realización de cambios y comparación del rendimiento]: #step4
  [Solución de problemas]: #troubleshooting
  [Pasos siguientes]: #nextSteps
 
  [1]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
  [2]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
  [3]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
  [4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
  [5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
  [6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
  [7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
  [8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
  [9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
  [10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png
  [11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
  [12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
  [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
