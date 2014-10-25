<properties linkid="mobile-services-dotnet-backend-how-to-troubleshoot" urlDisplayName="Troubleshoot the Mobile Services .NET Backend" pageTitle="Troubleshoot the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn how to diagnose and fix issues with your mobile services using the .NET backend" metaCanonical="" services="" documentationCenter="Mobile" title="Troubleshoot the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Solución de problemas en el backend .NET de Servicios móviles

El desarrollo con Servicios móviles suele ser sencillo y sin complicaciones, pero, aun así, pueden salir cosas mal a veces. En este tutorial se tratan algunas técnicas que le permitirán solucionar problemas comunes que pueden surgir con el extremo .NET de Servicios móviles.

1.  [Depuración de HTTP][]
2.  [Depuración en tiempo de ejecución][]
3.  [Análisis de registros de diagnóstico][]
4.  [Depuración de la resolución de ensamblados en la nube][]
5.  [Solución de problemas de migraciones con Entity Framework][]

<a name="HttpDebugging"></a>

## Depuración de HTTP

Cuando se desarrollan aplicaciones con Servicios móviles, lo normal es aprovechar las ventajas del SDK de cliente de Servicios móviles para la plataforma que utilice (Tienda Windows, iOS, Android, etc.). Sin embargo, a veces resulta útil ir al nivel HTTP y observar las llamadas de fila conforme tienen lugar en la red. Esta estrategia es especialmente útil cuando se depuran problemas de conectividad y serialización. El backend .NET de Servicios Móviles permite usar esta estrategia junto con la depuración de Visual Studio en modo local y remoto (más información en la próxima sección) para obtener una idea completa de la ruta que sigue una llamada HTTP antes de invocar el código del servicio.

Puede usar cualquier depurador HTTP para enviar e inspeccionar el tráfico HTTP. [Fiddler][] es una conocida herramienta que suelen usar los desarrolladores para este propósito. Con el fin de ponérselo fácil a los desarrolladores, Servicios móviles incluye un depurador de HTTP basado en web (también denominado "cliente de prueba") con su servicio móvil, lo que reduce la necesidad de herramientas externas. Si hospeda su servicio móvil de forma local, estará disponible en un URI similar a [][]<http://localhost:59233></a>. Si lo hospeda en la nube, el URI tendrá el formato [][1]<http://todo-list.azure-mobile.net></a>. Los pasos siguientes son válidos independientemente de dónde esté hospedado el servicio:

1.  Comience con un proyecto de servidor de Servicios móviles en **Visual Studio 2013 Update 2** o posterior. Si no tiene ninguno a mano, puede crearlo. Para ello, seleccione **Archivo**, **Nuevo**, **Proyecto**, elija el nodo **Nube** y después la plantilla **Servicios móviles de Windows Azure**.
2.  Presione **F5** para compilar y ejecutar el proyecto. En la página de inicio, seleccione **Probar**.

    > [WACOM.NOTE]
    > Si el servicio está hospedado en modo local, al hacer clic en el vínculo, se le dirigirá a la página siguiente. Sin embargo, si está hospedado en la nube, se le pedirá que proporcione un conjunto de credenciales. Esto es para asegurarse de que usuarios no autorizados no tienen acceso a información sobre su API y sus cargas. Para ver la página, debe iniciar sesión con un **nombre de usuario en blanco** y la **clave de la aplicación** como contraseña. La clave de la aplicación está disponible en el **Portal de administración de Azure**. Vaya al **Panel** de su servicio móvil y elija **Administrar claves**.
    >
    > ![Mensaje de autenticación para obtener acceso a la página de ayuda][]

3.  La página que ve (denominada "página de ayuda") muestra una lista de todas las API HTTP disponibles en su servicio móvil. Seleccione una de las API (si comenzó a usar la plantilla de proyecto de Servicios móviles en Visual Studio, debe ver **GET tables/TodoItem**).

    ![Página de ayuda][]

4.  La página resultante muestra la documentación y los ejemplos de JSON de la solicitud, y la respuesta que espera la API. Haga clic en el botón **Probar**.

    ![Página de prueba de una API][]

5.  Este es el "cliente de prueba", que le permite enviar una solicitud HTTP para probar su API. Seleccione **Enviar**.

    ![Cliente de prueba][]

6.  Verá la respuesta HTTP del servicio móvil directamente en la ventana del explorador.

    ![Cliente de prueba con respuesta HTTP][]

Ahora puede explorar cómodamente en su explorador web las diferentes API HTTP que expone su servicio móvil.

<a name="RuntimeDebugging"></a>

## Depuración en tiempo de ejecución

Una de las principales características del backend .NET es la capacidad para depurar el código del servicio de forma local, pero también para depurar el código en ejecución en el entorno de la nube. Siga estos pasos:

1.  Abra el proyecto de servicio móvil que desee depurar en **Visual Studio 2013 Update 2** o posterior.
2.  Configure la carga de símbolos. Navegue al menú **Depurar** y seleccione **Opciones y configuración**. Asegúrese de que la casilla **Habilitar Solo mi código** está desactivada y la casilla **Habilitar compatibilidad de servidor de origen** está activada.

    ![Configure la carga de símbolos][]

3.  Seleccione el nodo **Símbolos** a la izquierda y agregue una referencia al servidor (SymbolSource)[<http://symbolsource.org>] con el URI [][2]<http://srv.symbolsource.org/pdb/Public></a>. Los símbolos para el backend .NET de Servicios móviles están disponibles aquí con cada nueva versión.

    ![Configure el servidor de símbolos][]

4.  Establezca un punto de interrupción en el trozo de código que desea depurar. Por ejemplo, establezca un punto de interrupción en el método **GetAllTodoItems()** del controlador **TodoItemController** que viene con la plantilla de proyecto de Servicios móviles de Visual Studio.
5.  Presione **F5** para depurar el servicio de forma local. La primera carga puede ser lenta mientras Visual Studio descarga los símbolos para el backend .NET de Servicios móviles.
6.  Como se explica en la sección anterior sobre la depuración HTTP, utilice el cliente de prueba para enviar una solicitud HTTP al método donde estableció el punto de interrupción. Por ejemplo, para enviar una solicitud al método **GetAllTodoItems()**, seleccione **GET tables/TodoItem** en la página de ayuda, elija **Probar** y después **Enviar**.
7.  Visual Studio debe pararse en el punto de interrupción que ha establecido y debe haber disponible un seguimiento completo de la pila con el código fuente en la ventana **Pila de llamadas** de Visual Studio.

    ![Llegada a un punto de interrupción][]

8.  Ya puede publicar el servicio en Azure y estará disponible para depurarlo exactamente igual que como acabamos de ver. Publique el proyecto. Para ello, haga clic en él con el botón secundario en el **Explorador de soluciones** y elija **Publicar**.
9.  En la ficha **Configuración** del Asistente para publicación, seleccione la configuración **Depurar**. Esto hace que se publiquen los símbolos de depuración relevantes con el código.

    ![Opción Depurar en la publicación][]

10. Una vez que el servicio se ha publicado correctamente, abra el **Explorador de servidores** y expanda los nodos **Windows Azure** y **Servicios móviles**. Inicie sesión si es necesario.
11. Haga clic con el botón secundario en el servicio móvil que acaba de publicar y elija **Asociar depurador**.

    ![Attach debugger][]

12. Igual que hizo en el paso 6, envíe una solicitud HTTP para invocar el método donde estableció el punto de interrupción. Esta vez, utilice la página de ayuda y el cliente de prueba para el servicio móvil hospedado en Azure.
13. Visual Studio se detendrá en el punto de interrupción.

Ya tiene acceso a todo el potencial del depurador de Visual Studio, tanto si desarrolla en modo local como con el servicio móvil ejecutándose en Azure.

<a name="Logs"></a>

## Análisis de registros de diagnóstico

Conforme el servicio móvil controla las solicitudes de sus clientes, genera una gran variedad de información de diagnóstico muy útil, además de capturar las excepciones que encuentre. Por otro lado, puede instrumentar también el código del controlador con más registros para aprovechar las ventajas de la propiedad [**Log**][] disponible en la propiedad [**Services**][] de cada elemento [**TableController**][].

Cuando se hace depuración en modo local, los registros aparecen en la ventana **Resultados** de Visual Studio.

![Registros en la ventana Resultados de Visual Studio][]

Tras publicar el servicio en Azure, los registros de la instancia de servicio que se ejecuta en la nube están disponibles si hace clic con el botón secundario en el servicio móvil en el **Explorador de servidores** de Visual Studio y selecciona **Ver registros**.

![Registros en el Explorador de servidores de Visual Studio][]

Los mismos registros están disponibles también en el **Portal de administración de Azure** en la ficha **Registros** del servicio móvil.

![Registros en el Portal de administración de Azure][]

<a name="AssemblyResolution"></a>

## Depuración de la resolución de ensamblados en la nube

Al publicar el servicio móvil en Azure, se carga en el entorno de hospedaje de Servicios móviles, que garantiza actualizaciones y revisiones sin problemas de la canalización HTTP que hospeda el código del controlador. Esto incluye todos los ensamblados a los que hacen referencia los [paquetes NuGet del backend .NET][]: el equipo actualiza continuamente el servicio para que use las últimas versiones de esos ensamblados.

A veces se pueden introducir conflictos de versiones al hacer referencia a *versiones principales diferentes* de los ensamblados necesarios (se permiten versiones *secundarias* diferentes). Esto ocurre a menudo cuando NuGet le insta a actualizar a la última versión de uno de los paquetes que utiliza el backend .NET de Servicios móviles.

    > [WACOM.NOTE] Mobile Services is currently compatible only with ASP.NET 5.1; ASP.NET 5.2 is not presently supported. Upgrading your ASP.NET NuGet packages to 5.2.* may result in an error after deployment.

Si actualiza estos paquetes, cuando publique el servicio actualizado en Azure, verá una página de advertencia donde se indica el conflicto:

![Página de ayuda donde se indica un conflicto de carga de ensamblados][]

Esto irá acompañado del registro de un mensaje de excepción similar al siguiente en los registros del servicio:

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

Este problema es fácil de corregir: solo tiene que revertir a una versión admitida del ensamblado necesario y volver a publicar el servicio.

<a name="EFMigrations"></a>

## Solución de problemas de migraciones con Entity Framework

Cuando se usa el backend .NET de Servicios móviles con Base de datos SQL, se utiliza Entity Framework (EF) como tecnología de acceso a datos que permite consultar la base de datos y almacenar objetos en ella. Un aspecto importante que EF controla en nombre del desarrollador es el modo en que cambian las columnas de la base de datos (también conocidas como *esquema*) conforme cambian las clases de modelo especificadas en el código. Este proceso se conoce como [Migraciones de Code First][].

Las migraciones pueden ser complejas y pueden requerir que se mantenga el estado de la base de datos sincronizado con el modelo EF para que funcionen correctamente. Para obtener instrucciones sobre el modo de controlar las migraciones con un servicio móvil y los errores que pueden surgir, consulte [Modificación del modelo de datos de un servicio móvil del backend .NET][].

<!-- IMAGES -->

  [Depuración de HTTP]: #HttpDebugging
  [Depuración en tiempo de ejecución]: #RuntimeDebugging
  [Análisis de registros de diagnóstico]: #Logs
  [Depuración de la resolución de ensamblados en la nube]: #AssemblyResolution
  [Solución de problemas de migraciones con Entity Framework]: #EFMigrations
  [Fiddler]: http://www.telerik.com/fiddler
  []: http://localhost:59233
  [1]: http://todo-list.azure-mobile.net
  [Mensaje de autenticación para obtener acceso a la página de ayuda]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
  [Página de ayuda]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
  [Página de prueba de una API]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
  [Cliente de prueba]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
  [Cliente de prueba con respuesta HTTP]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
  [Configure la carga de símbolos]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
  [2]: http://srv.symbolsource.org/pdb/Public
  [Configure el servidor de símbolos]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
  [Llegada a un punto de interrupción]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
  [Opción Depurar en la publicación]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
  [Attach debugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
  [**Log**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx
  [**Services**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx
  [**TableController**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx
  [Registros en la ventana Resultados de Visual Studio]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
  [Registros en el Explorador de servidores de Visual Studio]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
  [Registros en el Portal de administración de Azure]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
  [paquetes NuGet del backend .NET]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [Página de ayuda donde se indica un conflicto de carga de ensamblados]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png
  [Migraciones de Code First]: http://msdn.microsoft.com/en-us/data/jj591621
  [Modificación del modelo de datos de un servicio móvil del backend .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/
