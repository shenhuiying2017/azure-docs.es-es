<properties linkid="mobile-services-dotnet-backend-hybrid-connections-get-started" urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg" />

# Conexión a un SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas

Cuando una empresa realiza la transición a la nube, a menudo debe dejar algunos activos en el sitio local, ya sea por motivos técnicos, de cumplimiento o de seguridad. Con los Servicios móviles puede crear fácilmente una capa de movilidad hospedada en la nube encima de estos activos y, al mismo tiempo, conectarse a ellos de forma segura en sus instalaciones mediante conexiones híbridas. Entre los activos admitidos se incluye cualquier recurso que se ejecute en un puerto TCP estático, como Microsoft SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados.

En este tutorial, aprenderá a modificar un servicio móvil back-end de .NET para usar una base de datos SQL Server local en lugar de la base de datos SQL de Azure aprovisionada con su servicio. Aunque los servicios móviles back-end de JavaScript admiten conexiones híbridas, en este tema solo se tratan los servicios móviles back-end de .NET.

Este tema le guiará a través de estos pasos básicos:

1.  [Requisitos previos][Requisitos previos]
2.  [Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local][Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local]
3.  [Creación de una conexión híbrida][Creación de una conexión híbrida]
4.  [Instalación del Administrador de conexiones híbridas local para completar la conexión][Instalación del Administrador de conexiones híbridas local para completar la conexión]
5.  [Modificación de un servicio móvil para usar la conexión][Modificación de un servicio móvil para usar la conexión]

<a name="Prerequisites"></a>

## Requisitos previos

Para completar este tutorial, necesitará los siguientes productos. Todos están disponibles en versión gratuita, así que puede comenzar a desarrollar para Azure completamente gratis.

-   **Visual Studio 2013**: para descargar una versión de evaluación gratuita de Visual Studio 2013, consulte [Descargas de Visual Studio][Descargas de Visual Studio]. Instale esta aplicación antes de continuar.

-   **SQL Server 2014 Express with Tools**: descargue Microsoft SQL Server Express de forma gratuita en la [página de bases de datos de Plataforma web de Microsoft][página de bases de datos de Plataforma web de Microsoft]. Elija la versión **Express** (no LocalDB). La versión **Express with Tools** incluye SQL Server Management Studio, que usará en este tutorial.

También necesitará una máquina local que se conectará a Azure mediante conexiones híbridas. Esa máquina debe cumplir los siguientes criterios:

-   Poder conectarse a Azure a través del puerto 5671
-   Poder comunicarse con el *nombre de host*:*número de puerto* del recurso local al que desea conectarse. El recurso puede estar o no hospedado en la misma máquina.

<a name="InstallSQL"></a>

## Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local

Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Las instancias predeterminadas de SQL Server usan el puerto estático 1433, mientras que las instancias con nombre no.

Para obtener instrucciones detalladas acerca de cómo configurar SQL Server de forma que cumpla las condiciones descritas anteriormente, consulte [Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local][1]. Si ya tiene SQL Server instalado en una configuración y en un entorno que cumple las condiciones descritas anteriormente, puede continuar y empezar con [Creación de una base de datos de SQL Server local][Creación de una base de datos de SQL Server local].

Para los fines de este tutorial, vamos a suponer que el nombre de la base de datos es **OnPremisesDB**, que se ejecuta en el puerto **1433** y que el nombre de host de la máquina es **onPremisesServer**.

<a name="CreateHC"></a>

## Creación de una conexión híbrida

1.  En la máquina local, inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].

2.  En la parte inferior del panel de navegación, seleccione **+NUEVO** y luego seleccione **Servicios de aplicaciones**, **Servicio de BizTalk** y después **Creación personalizada**.

    ![Create BizTalk Service][Create BizTalk Service]

3.  Proporcione un **Nombre del servicio de BizTalk** y seleccione una **Edición**.

    ![Configure new BizTalk Service][Configure new BizTalk Service]

    Este tutorial usa **móvil1**. Deberá proporcionar un nombre exclusivo para su nuevo servicio de BizTalk.

4.  Después de crear el servicio de BizTalk, seleccione la pestaña **Conexiones híbridas** y luego haga clic en **Agregar**.

    ![Add Hybrid Connection][Add Hybrid Connection]

    De esta forma, se creará una nueva conexión híbrida.

5.  Proporcione un **Nombre** y un **Nombre de host** para su conexión híbrida y defina el **Puerto** en `1433`.

    ![Configure Hybrid Connection][Configure Hybrid Connection]

    El nombre de host es el nombre del servidor local. De esta manera se configura la conexión híbrida para el acceso a SQL Server que se ejecuta en el puerto 1433.

6.  Una vez que se crea la nueva conexión, aparece en la siguiente tabla.

    ![Hybrid Connection successfully created][Hybrid Connection successfully created]

    El estado de la nueva conexión muestra **Configuración local incompleta**.

Ahora, será necesario instalar el Administrador de conexiones híbridas en el equipo local.

<a name="InstallHCM"></a>

## Instalación del Administrador de conexiones híbridas local para completar la conexión

El Administrador de conexiones híbridas permite que su máquina local se conecte a Azure y retransmita el tráfico TCP. Este software se debe instalar en el equipo local al que intenta acceder desde Azure.

1.  Seleccione la conexión que acaba de crear y, en la barra inferior, haga clic en **Configuración local**.

    ![On-Premises Setup][On-Premises Setup]

2.  Haga clic en **Instalar y configurar**.

    ![Install Hybrid Connection Manager][Install Hybrid Connection Manager]

    De esta forma, se instala una instancia personalizada del Administrador de conexiones, que ya está configurado para funcionar con la conexión híbrida que acaba de crear.

3.  Realice el resto de los pasos de configuración del Administrador de conexiones.

    ![Hybrid Connection Manager setup][Hybrid Connection Manager setup]

    Cuando finalice la instalación, el estado de la conexión híbrida cambia a **1 instancia conectada**. Puede que tenga que actualizar el explorador y esperar unos minutos. La configuración local ahora está completa.

    ![Hybrid Connection connected][Hybrid Connection connected]

<a name="CreateService"></a>

## Modificación de un servicio móvil para usar la conexión

### Asociación de la conexión híbrida con un servicio

1.  En la pestaña **Servicios móviles** del portal, seleccione un servicio móvil existente o cree uno nuevo.

    > [WACOM.NOTE]Asegúrese de seleccionar un servicio que se haya creado con el back-end de .NET o cree un nuevo servicio móvil back-end de .NET. Para aprender a crear un nuevo servicio móvil back-end de .NET, consulte [Introducción a los Servicios móviles][Introducción a los Servicios móviles]

2.  En la pestaña **Configurar** del servicio móvil, busque la sección **Conexiones híbridas** y seleccione **Agregar conexión híbrida**.

    ![Associate Hybrid Connection][Associate Hybrid Connection]

3.  Seleccione la conexión híbrida que acabamos de crear en la pestaña Servicios de BizTalk y presione **Aceptar**.

    ![Pick associated Hybrid Connection][Pick associated Hybrid Connection]

El servicio móvil está ahora asociado con la nueva conexión híbrida.

### Actualización del servicio para usar la cadena de conexión local

Finalmente, debemos crear una configuración de aplicación para almacenar el valor de la cadena de conexión en nuestro servidor SQL Server local. Luego, tendremos que modificar el servicio móvil para que use la nueva cadena de conexión.

1.  En la pestaña **Configurar** de **Configuración de aplicaciones**, agregue una nueva configuración de aplicación llamada `onPremisesDatabase` con un valor como `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`.

    ![App setting for connection string][App setting for connection string]

    Reemplace `{password}` por la contraseña segura de su base de datos local.

2.  Presione **Guardar** para guardar la conexión híbrida y la configuración de aplicación que acaba de crear.

3.  En Visual Studio 2013, abra el proyecto que define el servicio móvil basado en .NET.

    Para aprender a descargar el proyecto de back-end de .NET, consulte [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

4.  En el Explorador de soluciones, expanda la carpeta **Modelos** y abra el archivo de modelo de datos que termina con *Context.cs*.

5.  Modifique el constructor de instancia **DbContext** para que se asemeje al siguiente snippet:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base(ConfigurationManager.AppSettings["onPremisesDatabase"])
            {
            }

            // snipped
        }

    El servicio usará ahora la nueva cadena de conexión híbrida que se define en la configuración de aplicación de Azure.

6.  Publique los cambios y use una aplicación cliente conectada a su servicio móvil para invocar algunas operaciones de generación de cambios en la base de datos.

7.  Abra SQL Management Studio en el equipo local donde se ejecuta SQL Server y luego, en el Explorador de objetos, expanda la base de datos **OnPremisesDB** y expanda **Tablas**.

8.  Haga clic con el botón secundario en la tabla **hybridService1.TodoItems** y elija **Seleccionar las 1000 filas principales** para ver los resultados.

    ![SQL Management Studio][SQL Management Studio]

El servicio móvil ha transferido los cambios generados en la aplicación a la base de datos local.

## Otras referencias

-   [Sitio web de conexiones híbridas][Sitio web de conexiones híbridas]
-   [Introducción a las conexiones híbridas][Introducción a las conexiones híbridas]
-   [Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas][Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas]

<!-- IMAGES -->

  [Requisitos previos]: #Prerequisites
  [Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local]: #InstallSQL
  [Creación de una conexión híbrida]: #CreateHC
  [Instalación del Administrador de conexiones híbridas local para completar la conexión]: #InstallHCM
  [Modificación de un servicio móvil para usar la conexión]: #CreateService
  [Descargas de Visual Studio]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [página de bases de datos de Plataforma web de Microsoft]: http://www.microsoft.com/web/platform/database.aspx
  [1]: /es-es/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL
  [Creación de una base de datos de SQL Server local]: /es-es/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB
  [Portal de administración de Azure]: http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409
  [Create BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
  [Configure new BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
  [Add Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
  [Configure Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
  [Hybrid Connection successfully created]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
  [On-Premises Setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
  [Install Hybrid Connection Manager]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
  [Hybrid Connection Manager setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
  [Hybrid Connection connected]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Associate Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
  [Pick associated Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
  [App setting for connection string]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
  [SQL Management Studio]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
  [Sitio web de conexiones híbridas]: http://azure.microsoft.com/es-es/services/biztalk-services/
  [Introducción a las conexiones híbridas]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Servicios de BizTalk: pestañas Panel, Monitor, Escala, Configurar y Conexiones híbridas]: http://azure.microsoft.com/es-es/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
