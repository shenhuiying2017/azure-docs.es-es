---
title: Entrega continua para Cloud Services con Visual Studio Online | Microsoft Docs
description: "Obtenga información acerca de cómo configurar la entrega continua para aplicaciones en la nube de Azure sin guardar la clave de almacenamiento de la información de diagnóstico en los archivos de configuración de servicio"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 148b2959-c5db-4e4a-a7e9-fccb252e7e8a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 7e70f92d4d1333ca6cbac5876e5ccbc763bd915c

---
# <a name="securely-save-cloud-services-diagnostics-storage-key-and-setup-continuous-integration-and-deployment-to-azure-using-visual-studio-online"></a>Guarde de forma segura la clave de almacenamiento de la información de diagnóstico de Cloud Services y configure la integración e implementación continua en Azure mediante Visual Studio Online
 Es una práctica común para abrir proyectos de código fuente hoy en día. Guardar los secretos de aplicación en archivos de configuración ya no es una práctica segura, ya que se producen vulnerabilidades de seguridad procedentes de los secretos que se filtran desde los controles de código fuente públicos. Almacenar el secreto como texto no cifrado en un archivo de una canalización de integración continua no es seguro ya que los servidores de compilación podrían convertirse en recursos compartidos en el entorno en la nube. Este artículo explica cómo Visual Studio y Visual Studio Online mitiga los problemas de seguridad durante el desarrollo y el proceso de integración continua.

## <a name="remove-diagnostics-storage-key-secret-in-project-configuration-file"></a>Eliminación del secreto de la clave de almacenamiento de la información de diagnóstico del archivo de configuración de proyecto
La extensión de diagnósticos de Cloud Services requiere Azure Storage para guardar los resultados de diagnóstico. Anteriormente, la cadena de conexión de almacenamiento se especificaba en los archivos de configuración (.cscfg) de Cloud Services y podría incorporarse al control de código fuente. En la versión más reciente de Azure SDK se ha cambiado el comportamiento de forma que solo se almacena una cadena de conexión parcial y la clave se sustituye por un token. Los siguientes pasos describen el funcionamiento de las nuevas herramientas de Cloud Services:

### <a name="1-open-the-role-designer"></a>1. Apertura del diseñador de roles
* Haga doble clic o haga clic con el botón secundario en un rol de Cloud Services para abrir el diseñador de roles

![Abra el diseñador de roles][0]

### <a name="2-under-diagnostics-section-a-new-check-box-dont-remove-storage-key-secret-from-project-is-added"></a>2. En la sección de diagnósticos, se ha agregado una nueva casilla de verificación "Don’t remove storage key secret from project" (No quitar secreto de la clave de almacenamiento del proyecto)
* Si está utilizando el emulador de almacenamiento local, esta casilla está deshabilitada porque no hay ningún secreto que administrar para la cadena de conexión local, que es UseDevelopmentStorage=true.

![La cadena de conexión del emulador de almacenamiento local no es un secreto][1]

* Si va a crear un nuevo proyecto, esta casilla de verificación estará desactivada de forma predeterminada. Esto da como resultado que la sección de clave de almacenamiento de la cadena de conexión de almacenamiento seleccionada se sustituye por un token. El valor del token se encuentra en la carpeta AppData Roaming actual del usuario, por ejemplo: C:\Users\contosouser\AppData\Roaming\Microsoft\CloudService

> Tenga en cuenta que la carpeta user\AppData tiene un acceso controlado mediante el inicio de sesión de usuario y se considera un lugar seguro para almacenar secretos de desarrollo.
> 
> 

![La clave de almacenamiento se guarda en la carpeta del perfil de usuario][2]

### <a name="3-select-a-diagnostics-storage-account"></a>3. Selección de la cuenta de almacenamiento de información de diagnóstico
* Seleccione una cuenta de almacenamiento en el cuadro de diálogo que se inicia haciendo clic en el botón "..." . Observe cómo la cadena de conexión de almacenamiento generada no tendrá la clave de cuenta de almacenamiento.
* Por ejemplo: DefaultEndpointsProtocol=https;AccountName=contosostorage;AccountKey=$(*clouddiagstrg.key*)

### <a name="4-debugging-the-project"></a>4.    Depuración del proyecto
* Presione F5 para empezar la depuración en Visual Studio. Todo debería funcionar de la misma manera que antes.
  ![Inicie la depuración localmente][3]

### <a name="5-publish-project-from-visual-studio"></a>5. Publicación del proyecto desde Visual Studio
* Inicie el cuadro de diálogo Publicar y continúe con las instrucciones de inicio de sesión para publicar la aplicación en Azure.

### <a name="6-additional-information"></a>6. Información adicional
> Nota: El panel de configuración del diseñador de roles permanecerá igual que está ahora. Si desea utilizar la característica de administración de secretos para diagnósticos, vaya a la pestaña de configuraciones.
> 
> 

![Incorporación de la configuración][4]

> Nota: Si está habilitada, se almacenará la clave de Application Insights como texto sin formato. La clave solo se utiliza para los contenidos de carga, de modo que no se ve comprometida la seguridad de ningún dato confidencial.
> 
> 

## <a name="build-and-publish-a-cloud-services-project-using-visual-studio-online-task-templates"></a>Compilación y publicación de un proyecto de Cloud Services mediante plantillas de tareas en Visual Studio Online
* Los pasos siguientes muestran cómo configurar la integración continua para proyectos de Cloud Services con tareas de Visual Studio Online:
  ### <a name="1-obtain-a-vso-account"></a>1.    Obtenga una cuenta de VSO
* [Creación de una cuenta de Visual Studio Online][Creación de una cuenta de Visual Studio Online] si aún no tiene una
* [Creación de un proyecto de equipo][Creación de un proyecto de equipo] en su cuenta de Visual Studio Online

### <a name="2-setup-source-control-in-visual-studio"></a>2.    Configuración del control de código fuente en Visual Studio
* Conéctese a un proyecto de equipo

![Conéctese a un proyecto de equipo][5]

![Seleccione el proyecto de equipo al que conectarse][6]

* Agregue el proyecto al control de código fuente

![Agregue el proyecto al control de código fuente][7]

![Asigne el proyecto a una carpeta de control de código fuente][8]

* Compruebe el proyecto desde Team Explorer

![Proteja un proyecto en el control de código fuente][9]

### <a name="3-configure-build-process"></a>3.    Configuración del proceso de compilación
* Vaya al proyecto de equipo y agregue un nuevo proceso de compilación, Templates

![Agregue una nueva compilación][10]

* Seleccione una tarea de compilación

![Agregue una tarea de compilación][11]

![Seleccione la plantilla de tarea de compilación de Visual Studio][12]

* Edite la entrada de la tarea de compilación. Personalice los parámetros de compilación según sus necesidades

![Configure la tarea de compilación][13]

`/t:Publish /p:TargetProfile=$(targetProfile) /p:DebugType=None /p:SkipInvalidConfigurations=true /p:OutputPath=bin\ /p:PublishDir="$(build.artifactstagingdirectory)\\"`

* Configure las variables de compilación

![Configure las variables de compilación][14]

* Agregue una tarea para cargar el destino de compilación

![Elija publicar tarea de destino de compilación][15]

![Configure la tarea de publicación de destino de compilación][16]

* Ejecute la compilación

![Ponga en cola la nueva compilación][17]

![Vea el resumen de compilación][18]

* Si la compilación se ha realizado correctamente, verá un resultado parecido al siguiente

![Resultado de la compilación][19]

### <a name="4-configure-release-process"></a>4.    Configuración del proceso de lanzamiento
* Cree una nueva versión

![Cree una nueva versión][20]

* Seleccione la tarea de implementación de Azure Cloud Services

![Seleccione la tarea de implementación de Azure Cloud Services][21]

* Como la clave de la cuenta de almacenamiento no está activada en el control de código fuente, es preciso especificar la clave secreta para establecer las extensiones de diagnóstico. Expanda la sección **Opciones avanzadas para Creación de un nuevo servicio** y edite la entrada de parámetro de **claves de cuenta de almacenamiento de información de diagnósticos**. Esta entrada ocupa varias líneas de par clave-valor con el formato **[RoleName]:$(StorageAccountKey)**

> Nota: Si la cuenta de almacenamiento de información de diagnóstico pertenece a la misma suscripción en la que publicará la aplicación de Cloud Services, no es necesario que introduzca la clave de la entrada de la tarea de implementación. La implementación obtendrá mediante programación la información de almacenamiento de la suscripción
> 
> 

![Configuración de la tarea de implementación de Azure Cloud Services][22]

* Use variables de compilación de secretos para guardar las claves de almacenamiento. Para enmascarar una variable como secreto, haga clic en el icono de bloqueo en el lado derecho de la entrada Variables

![Guardar las claves de almacenamiento en variables de compilación de secreto][23]

* Cree una versión e implemente el proyecto en Azure

![Cree una nueva versión][24]

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el establecimiento de extensiones de diagnóstico para Azure Cloud Services, consulte [Habilitar el diagnóstico en Azure Cloud Services mediante PowerShell][Habilitar el diagnóstico en Azure Cloud Services mediante PowerShell]

[Creación de una cuenta de Visual Studio Online]:https://www.visualstudio.com/team-services/
[Creación de un proyecto de equipo]: https://www.visualstudio.com/it-it/docs/setup-admin/team-services/connect-to-visual-studio-team-services
[Habilitar el diagnóstico en Azure Cloud Services mediante PowerShell]:https://azure.microsoft.com/en-us/documentation/articles/cloud-services-diagnostics-powershell/

[0]: ./media/cloud-services-vs-ci/vs-01.png
[1]: ./media/cloud-services-vs-ci/vs-02.png
[2]: ./media/cloud-services-vs-ci/file-01.png
[3]: ./media/cloud-services-vs-ci/vs-03.png
[4]: ./media/cloud-services-vs-ci/vs-04.png
[5]: ./media/cloud-services-vs-ci/vs-05.png
[6]: ./media/cloud-services-vs-ci/vs-06.png
[7]: ./media/cloud-services-vs-ci/vs-07.png
[8]: ./media/cloud-services-vs-ci/vs-08.png
[9]: ./media/cloud-services-vs-ci/vs-09.png
[10]: ./media/cloud-services-vs-ci/vso-01.png
[11]: ./media/cloud-services-vs-ci/vso-02.png
[12]: ./media/cloud-services-vs-ci/vso-03.png
[13]: ./media/cloud-services-vs-ci/vso-04.png
[14]: ./media/cloud-services-vs-ci/vso-05.png
[15]: ./media/cloud-services-vs-ci/vso-06.png
[16]: ./media/cloud-services-vs-ci/vso-07.png
[17]: ./media/cloud-services-vs-ci/vso-08.png
[18]: ./media/cloud-services-vs-ci/vso-09.png
[19]: ./media/cloud-services-vs-ci/vso-10.png
[20]: ./media/cloud-services-vs-ci/vso-11.png
[21]: ./media/cloud-services-vs-ci/vso-12.png
[22]: ./media/cloud-services-vs-ci/vso-13.png
[23]: ./media/cloud-services-vs-ci/vso-14.png
[24]: ./media/cloud-services-vs-ci/vso-15.png



<!--HONumber=Nov16_HO3-->


