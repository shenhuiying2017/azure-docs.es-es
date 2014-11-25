<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Trabajo con un servicio móvil de backend .NET

En este artículo se proporciona información detallada y ejemplos de cómo trabajar con un proyecto de backend .NET de Visual Studio que define su servicio móvil en Servicios móviles de Azure. Este tema está dividido en las siguientes secciones:

-   [Introducción][Introducción]
-   [Operaciones de tabla][Operaciones de tabla]
    -   [Direccionamiento del operaciones de tabla][Direccionamiento del operaciones de tabla]
    -   [Direccionamiento del la respuesta predeterminada][Direccionamiento del la respuesta predeterminada]
    -   [Direccionamiento del execute success][Direccionamiento del execute success]
    -   [Direccionamiento del control de errores predeterminado][Direccionamiento del control de errores predeterminado]
    -   [Direccionamiento del parámetros personalizados][Direccionamiento del parámetros personalizados]
    -   [Direccionamiento del usuarios de tablas][Direccionamiento del usuarios de tablas]
-   [API personalizada][API personalizada]
    -   [Direccionamiento del una API personalizada][Direccionamiento del una API personalizada]
    -   [Direccionamiento del de métodos HTTP][Direccionamiento del de métodos HTTP]
    -   [Direccionamiento del y recepción de datos como XML][Direccionamiento del y recepción de datos como XML]
    -   [Direccionamiento del de usuarios y encabezados en una API personalizada][Direccionamiento del de usuarios y encabezados en una API personalizada]
    -   [Direccionamiento del de varias rutas en una API personalizada][Direccionamiento del de varias rutas en una API personalizada]
-   [Programador de trabajos][Programador de trabajos]
    -   [Direccionamiento del de scripts de trabajos programados][Programador de trabajos]
-   [Control de código fuente, código compartido y funciones auxiliares][Control de código fuente, código compartido y funciones auxiliares]
    -   [Direccionamiento del de módulos Node.js][Direccionamiento del de módulos Node.js]
    -   [Direccionamiento del de funciones auxiliares][Direccionamiento del de funciones auxiliares]
    -   [Direccionamiento del compartido de código mediante el control de código fuente][Direccionamiento del compartido de código mediante el control de código fuente]
    -   [Direccionamiento del de configuración de aplicaciones][Direccionamiento del de configuración de aplicaciones]
-   [Uso de la herramienta de la línea de comandos][Uso de la herramienta de la línea de comandos]
-   [Uso de tablas][Uso de tablas]
    -   [Direccionamiento del a tablas desde scripts][Direccionamiento del a tablas desde scripts]
    -   [Direccionamiento del de inserciones en masa][Direccionamiento del de inserciones en masa]
    -   [Direccionamiento del de tipos JSON a tipos de base de datos][Direccionamiento del de tipos JSON a tipos de base de datos]
    -   [Uso de Transact-SQL para obtener acceso a las tablas][Uso de Transact-SQL para obtener acceso a las tablas]
-   [Depuración y solución de problemas][Depuración y solución de problemas]
    -   [Direccionamiento del del resultado en los registros de servicios móviles][Direccionamiento del del resultado en los registros de servicios móviles]

## <a name="intro"></a>Introducción

En un servicio móvil de backend .NET, puede definir lógica empresarial personalizada como código de JavaScript que se almacena y ejecuta en el servidor. Este código de script del servidor se asigna a una de las siguientes funciones de servidor:

-   [Inserción, lectura, actualización o eliminación de operaciones en una tabla determinada][Operaciones de tabla].
-   [Trabajos programados][Programador de trabajos].
-   [Métodos HTTP definidos en una API personalizada][API personalizada].

La firma de la función principal del script del servidor depende del contexto en el que se use el script. También puede definir el código de script común como módulos nodes.js que se comparten entre scripts. Para obtener más información, consulte [Control de código fuente y código compartido][Control de código fuente, código compartido y funciones auxiliares].

Para obtener la descripción de funciones y objetos de script de servidor concretos, consulte [documentación de referencia de scripts de servidor de Servicios móviles].



  [Introducción]: #intro
  [Operaciones de tabla]: #table-scripts
  [Direccionamiento del operaciones de tabla]: #register-table-scripts
  [Direccionamiento del la respuesta predeterminada]: #override-response
  [Direccionamiento del execute success]: #override-success
  [Direccionamiento del control de errores predeterminado]: #override-error
  [Direccionamiento del parámetros personalizados]: #access-headers
  [Direccionamiento del usuarios de tablas]: #work-with-users
  [API personalizada]: #custom-api
  [Direccionamiento del una API personalizada]: #define-custom-api
  [Direccionamiento del de métodos HTTP]: #handle-methods
  [Direccionamiento del y recepción de datos como XML]: #api-return-xml
  [Direccionamiento del de usuarios y encabezados en una API personalizada]: #get-api-user
  [Direccionamiento del de varias rutas en una API personalizada]: #api-routes
  [Programador de trabajos]: #scheduler-scripts
  [Control de código fuente, código compartido y funciones auxiliares]: #shared-code
  [Direccionamiento del de módulos Node.js]: #modules-helper-functions
  [Direccionamiento del de funciones auxiliares]: #helper-functions
  [Direccionamiento del compartido de código mediante el control de código fuente]: #shared-code-source-control
  [Direccionamiento del de configuración de aplicaciones]: #app-settings
  [Uso de la herramienta de la línea de comandos]: #command-prompt
  [Uso de tablas]: #working-with-tables
  [Direccionamiento del a tablas desde scripts]: #access-tables
  [Direccionamiento del de inserciones en masa]: #bulk-inserts
  [Direccionamiento del de tipos JSON a tipos de base de datos]: #JSON-types
  [Uso de Transact-SQL para obtener acceso a las tablas]: #TSQL
  [Depuración y solución de problemas]: #debugging
  [Direccionamiento del del resultado en los registros de servicios móviles]: #write-to-logs
