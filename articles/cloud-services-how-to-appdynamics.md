<properties linkid="manage-services-how-to-use-appdynamics" urlDisplayName="Monitor with AppDynamics" pageTitle="How to use AppDynamics with Azure" metaKeywords="" description="Learn how to use AppDynamics for Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How To Use AppDynamics for Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Uso de AppDynamics para Azure

En este tema se describe cómo empezar con AppDynamics para Azure.

## Tabla de contenido

-   [¿Qué es AppDynamics?][]
-   [Requisitos previos][]
-   [Registro para obtener una cuenta de AppDynamics][]
-   [Descarga de .NET Agent desde AppDynamics][]
-   [Incorporación de .NET Agent a los roles de Azure y modificación del inicio][]
-   [Publicación de la aplicación instrumentada con AppDynamics en Azure][]
-   [Supervisión de una aplicación][]

## <span id="what"></span></a>¿Qué es AppDynamics?

AppDynamics es una solución de supervisión del rendimiento de las aplicaciones que le ayuda a:

-   Identificar problemas, por ejemplo, las solicitudes de usuario lentas y detenidas, además de los errores, en un entorno de producción

-   Solucionar problemas y aislar la causa principal de dichos problemas

Hay dos componentes en AppDynamics:

-   Agente de servidor de aplicaciones: El agente .NET del servidor de aplicaciones recopila datos de los servidores. Se ejecuta un agente aparte en cada instancia de rol que desea supervisar. Puede descargar el agente desde el portal de AppDynamics.

-   Controlador de AppDynamics: El agente envía su información a un servicio hospedado del controlador de AppDynamics en Azure. Con una consola basada en un explorador web, se inicia sesión en el controlador para supervisar, analizar y solucionar problemas en la aplicación.

    ![Diagrama de AppDynamics][]

## <span id="prereq"></span></a>Requisitos previos

-   Visual Studio 2010 o posterior
-   Una solución de Visual Studio que se va a supervisar
-   SDK de Azure
-   Cuenta de Azure

## <span id="register"></span></a>Registro para obtener una cuenta de AppDynamics

Para registrarse a fin de obtener una cuenta de AppDynamics para Azure:

1.  Haga clic en **Try Free** o **Sign Up** para AppDynamics en el Azure Marketplace en [][]<https://datamarket.azure.com/browse/Applications></a>.

    Si selecciona **Sign Up**, recibirá una versión gratuita de AppDynamics Pro para Azure con funcionalidad completa, la cual se degrada después de 30 días a una versión gratuita de AppDynamics Lite para Azure con funcionalidad limitada. No es necesario que proporcione una tarjeta de crédito para esta opción. Puede actualizar a AppDynamics Pro para Azure en cualquier momento.

    Si selecciona **Try Free**, recibirá una versión gratuita de AppDynamics Pro para Azure con funcionalidad completa. Debe proporcionar una tarjeta de crédito para esta opción. Después de 30 días se cargará a su cuenta de crédito el uso continuado de AppDynamics Pro para Azure, a menos que cancele la suscripción.

    Necesita una licencia de agente para cada instancia de rol que desea supervisar. Por ejemplo, un sitio que ejecuta dos instancias de rol web y dos instancias de rol de trabajo requiere cuatro licencias de agente.

2.  En la página de registro, proporcione su información de usuario, una contraseña, la dirección de correo electrónico, el nombre de la empresa y el nombre de la aplicación que está supervisando tal y como la publicará con Azure.

3.  Haga clic en **Register Now**.

    Recibirá las credenciales de AppDynamics y la dirección URL del controlador de AppDynamics (host y puerto) asignada a su cuenta en un correo electrónico enviado a la dirección que proporcionó en la página de registro. Guarde esta información.

    Si ya tiene las credenciales de AppDynamics de otro producto, puede iniciar sesión con ellas.

    También se le proporcionará una página de inicio de la cuenta de AppDynamics.

    Llegará a la página de inicio de la cuenta de AppDynamics.

    La página de inicio de la cuenta de AppDynamics incluye:

    -   URL de controlador: desde la cual puede iniciar sesión en su cuenta en el servicio hospedado del controlador de AppDynamic

    -   Credenciales de AppDynamics, nombre de cuenta y clave de acceso

    -   Vínculo al sitio de descarga de AppDynamics: de los que descargar .NET Agent de AppDynamics

    -   Cantidad de días que quedan de la evaluación gratuita Pro

    -   Vínculos a los videos y documentación integrados de AppDynamics

    Puede tener acceso a la página de inicio de la cuenta de AppDynamics en cualquier momento si escribe la dirección URL en un explorador web e inicia sesión con las credenciales de AppDynamics.

## <span id="download"></span></a>Descarga de .NET Agent desde AppDynamics

1.  Diríjase al sitio de descarga de AppDynamics. La URL se encuentra en el correo electrónico de bienvenida y en la página de inicio de la cuenta de AppDynamics.

2.  Inicie sesión con el nombre de cuenta y la clave de acceso de AppDynamics.

3.  Descargue el archivo llamado AppDynamicsdotNetAgentSetup64.msi. No lo ejecute.

## <span id="addagent"></span></a>Incorporación de .NET Agent a los roles de Azure y modificación del inicio

Este paso instrumenta los roles de su solución de Visual Studio para la supervisión mediante AppDynamics. No hay ningún procedimiento de instalación del estilo del asistente de Windows tradicional necesario para usar AppDynamics para Azure.

1.  Cree un nuevo proyecto de Azure en Visual Studio o abra un proyecto de Azure existente.

2.  Si ha creado un nuevo proyecto, agregue los proyectos de rol web o rol de trabajo a la solución.

3.  Agregue el archivo .msi de .NET Agent descargado a cada proyecto de rol web y rol de trabajo que desea supervisar.

    Tenga en cuenta que mientras cada *proyecto de rol* tiene un solo archivo msi de .NET Agent adjunto, cada *instancia de rol* del proyecto requiere una licencia de .NET Agent aparte.

4.  Para cada proyecto de rol web y rol de trabajo que desea supervisar, agregue un archivo de texto llamado startup.cmd y pegue las siguientes líneas en él:

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  Para cada rol web y rol de trabajo que desea supervisar, establezca la propiedad **Copiar en el directorio de salida** para el archivo .msi del agente de AppDynamics y el archivo startup.cmd en **Copiar siempre**.

    ![Copiar siempre][]

6.  En el archivo ServiceDefinition.csdef del proyecto de Azure, agregue un elemento de tarea de inicio que invoque al archivo startup.cmd con los parámetros para cada elemento de WorkerRole y WebRole.

    Agregue las siguientes líneas:

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    donde:

    -   *su host de controlador* y *su puerto de controlador* son el host y el puerto del controlador asignados a su cuenta y *su nombre de cuenta* y *su clave de acceso* son las credenciales que le asignaron mediante AppDynamics. Esta información se proporciona en el correo electrónico que se envió cuando se registró con AppDynamics y también en la página de inicio de AppDynamic. Consulte [Registro para obtener una cuenta de AppDynamics][].

    -   *su nombre de aplicación* es el nombre que selecciona para la aplicación. Este nombre identificará la aplicación en la interfaz del controlador de AppDynamics.

    El archivo ServiceDefinition.csdef se parecerá al siguiente:

    ![Definición de servicio][]

## <a name="publish"></a>Publicación de la aplicación instrumentada con AppDynamics en Azure

Para cada proyecto de rol instrumentado por AppDynamics:

1.  En Visual Studio, seleccione el proyecto de rol.

2.  Seleccione Publish to Azure.

## <a name="monitor"></a>Supervisión de una aplicación

1.  Inicie sesión en el controlador de AppDynamics en la dirección URL proporcionada en su correo electrónico de bienvenida y en su página de inicio de la cuenta de AppDynamics.

2.  Envíe algunas solicitudes a su aplicación para que haya algo de tráfico para supervisar y espere unos minutos.

3.  En el controlador de AppDynamics, seleccione la aplicación.

4.  Supervise la aplicación.

## <a name="learn"></a>Más información

Consulte la página de inicio de la cuenta de AppDynamics para ver vínculos a documentación y videos.

Las últimas actualizaciones de este documento están en la versión wiki en [][1]<http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure></a>.

  [¿Qué es AppDynamics?]: #what
  [Requisitos previos]: #prereq
  [Registro para obtener una cuenta de AppDynamics]: #register
  [Descarga de .NET Agent desde AppDynamics]: #download
  [Incorporación de .NET Agent a los roles de Azure y modificación del inicio]: #addagent
  [Publicación de la aplicación instrumentada con AppDynamics en Azure]: #publish
  [Supervisión de una aplicación]: #monitor
  [Diagrama de AppDynamics]: ./media/cloud-services-how-to-appdynamics/addiagram.png
 
  [Copiar siempre]: ./media/cloud-services-how-to-appdynamics/adcopyalways.png
  [Definición de servicio]: ./media/cloud-services-how-to-appdynamics/adscreen.png
  [1]: http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure
