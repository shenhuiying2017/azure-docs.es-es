<properties 
	pageTitle="Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas - Servicios móviles de Azure" 
	description="Obtenga información acerca de cómo conectarse a un SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas" 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

  
# Conexión a un servidor SQL Server local desde un servicio móvil de Azure mediante Conexiones híbridas 

Cuando una empresa realiza la transición a la nube, a menudo debe dejar algunos activos en la instalación local, ya sea por motivos técnicos, de cumplimiento o de seguridad. Con los Servicios móviles puede crear fácilmente una capa de movilidad hospedada en la nube encima de estos activos y, al mismo tiempo, conectarse a ellos de forma segura en sus instalaciones mediante conexiones híbridas. Entre los activos admitidos se incluye cualquier recurso que se ejecute en un puerto TCP estático, como Microsoft SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados. 

En este tutorial, aprenderá a modificar un servicio móvil back-end de .NET para usar una base de datos SQL Server local en lugar de la base de datos SQL de Azure aprovisionada con su servicio. Aunque los servicios móviles back-end de JavaScript admiten conexiones híbridas, en este tema solo se tratan los servicios móviles back-end de .NET.

Este tema le guiará a través de estos pasos básicos:

1. [Requisitos previos](#Prerequisites)
2. [Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local](#InstallSQL)
3. [Creación de una conexión híbrida](#CreateHC)
4. [Instalación del Administrador de conexiones híbridas local para realizar la conexión](#InstallHCM)
5. [Modificación de un servicio móvil para usar la conexión](#CreateService)

<a name="Prerequisites"></a>
##Requisitos previos##
Para completar este tutorial, necesitará los siguientes productos. Todos están disponibles en versión gratuita, así que puede comenzar a desarrollar para Azure completamente gratis.

- **Visual Studio 2013**: para descargar una versión de evaluación gratuita de Visual Studio 2013, vea [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale esta aplicación antes de continuar.

- **SQL Server 2014 Express with Tools**: descargue Microsoft SQL Server Express de forma gratuita en la [página de bases de datos de Plataforma web de Microsoft](http://www.microsoft.com/web/platform/database.aspx). Elija la versión **Express** (no LocalDB). La versión **Express with Tools** incluye SQL Server Management Studio, que usará en este tutorial.

También necesitará una máquina local que se conectará a Azure mediante conexiones híbridas. Esa máquina debe cumplir los siguientes criterios:

- Poder conectarse a Azure a través del puerto 5671
- Poder establecer contacto con el *hostname*:*portnumber* del recurso local al que desea conectarse. El recurso puede estar o no hospedado en la misma máquina. 

<a name="InstallSQL"></a>
## Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local

Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrida, es necesario habilitar TCP/IP en un puerto estático. Las instancias predeterminadas de SQL Server usan el puerto estático 1433, mientras que las instancias con nombre no. 

Para obtener instrucciones detalladas sobre cómo configurar SQL Server de forma que cumpla las condiciones descritas anteriormente, vea [Instalación de SQL Server Express, habilitación de TCP/IP y creación de una base de datos SQL Server local](web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL.md). Si ya tiene instalado SQL Server en una configuración y en un entorno que cumple las condiciones anteriores, puede seguir adelante y comenzar con [Creación de una base de datos SQL Server local](web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB.md). 

Para los fines de este tutorial, supondremos que el nombre de la base de datos es **OnPremisesDB**, que se ejecuta en el puerto **1433** y que el nombre de host de la máquina es **onPremisesServer**.

<a name="CreateHC"></a>
## Creación de una conexión híbrida
1. En la máquina local, inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409).

2. En la parte inferior del panel de navegación, seleccione **+NUEVO**, **Servicios de aplicaciones**, **Servicios de BizTalk** y después **Creación personalizada**

	![Create BizTalk Service][CreateBTS]

3. Proporcione un **Nombre del servicio de BizTalk** y seleccione una **Edición**. 

	![Configure new BizTalk Service][ConfigureBTS]

	Este tutorial usa **móvil1**. Deberá proporcionar un nombre exclusivo para su nuevo servicio de BizTalk.

4. Después de crear el servicio de BizTalk, seleccione la pestaña **Conexiones híbridas** y luego haga clic en **Agregar**.

	![Add Hybrid Connection][AddHC]

	De esta forma, se creará una nueva conexión híbrida.

5. Proporcione un **Nombre** y un **Nombre de host** para su conexión híbrida y establezca el **Puerto** en `1433`. 
  
	![Configure Hybrid Connection][ConfigureHC]

	El nombre de host es el nombre del servidor local. De esta manera se configura la conexión híbrida para el acceso a SQL Server que se ejecuta en el puerto 1433.

6. Una vez que se crea la nueva conexión, aparece en la siguiente tabla. 
 
	![Hybrid Connection successfully created][HCCreated]
	
	El estado de la nueva conexión muestra que la **configuración local está incompleta**.

Ahora, será necesario instalar el Administrador de conexiones híbridas en el equipo local.

<a name="InstallHCM"></a>
## Instalación del Administrador de conexiones híbridas local para realizar la conexión

El Administrador de conexiones híbridas permite que su máquina local se conecte a Azure y retransmita el tráfico TCP. Este software se debe instalar en el equipo local al que intenta acceder desde Azure.

1. Seleccione la conexión que acaba de crear y, en la barra inferior, haga clic en **Configuración local**.

	![On-Premises Setup][DownloadHCM]

4. Haga clic en **Instalar y configurar**.

	![Install Hybrid Connection Manager][InstallHCM]

	De esta forma, se instala una instancia personalizada del Administrador de conexiones, que ya está configurado para funcionar con la conexión híbrida que acaba de crear.

3. Realice el resto de los pasos de configuración del Administrador de conexiones.

	![Hybrid Connection Manager setup][HCMSetup]

	Cuando finalice la instalación, el estado de la conexión híbrida cambia a **1 instancia conectada**. Puede que tenga que actualizar el explorador y esperar unos minutos. La configuración local ahora está completa.

	![Hybrid Connection connected][HCConnected]

<a name="CreateService"></a>
## Modificación de un servicio móvil para usar la conexión
### Asociación de la conexión híbrida con un servicio
1. En la pestaña **Servicios móviles** del portal, seleccione un servicio móvil existente o cree uno nuevo. 

	>[AZURE.NOTE]Asegúrese de seleccionar un servicio que se haya creado con el back-end de .NET o cree un nuevo servicio móvil back-end de .NET. Para aprender a crear un nuevo servicio móvil back-end de .NET, vea [Introducción a los Servicios móviles](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md) 

2. En la pestaña **Configurar** del servicio móvil, busque la sección **Conexiones híbridas** y seleccione **Agregar conexión híbrida**.

	![Associate Hybrid Connection][AssociateHC]

3. Seleccione la conexión híbrida que acabamos de crear en la pestaña Servicios de BizTalk y presione **Aceptar**. 

	![Pick associated Hybrid Connection][PickHC]

El servicio móvil está ahora asociado con la nueva conexión híbrida.

### Actualización del servicio para usar la cadena de conexión local
Finalmente, debemos crear una configuración de aplicación para almacenar el valor de la cadena de conexión en nuestro servidor SQL Server local. Luego, tendremos que modificar el servicio móvil para que use la nueva cadena de conexión. 

1. En la pestaña **Configurar** en **Cadenas de conexión**, agregue una nueva cadena de conexión llamada `OnPremisesDatabase` con un valor como `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`.

	![Connection string for on-premises database][ConnectionString]

	Sustituya `{password}` por la contraseña segura de su base de datos local.

2. Presione **Guardar** para guardar la conexión híbrida y la cadena de conexión que acabamos de crear.

3. En Visual Studio 2013, abra el proyecto que define el servicio móvil basado en .NET. 

	Para aprender a descargar el proyecto de back-end de .NET, vea [Introducción a los Servicios móviles](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md) .
 
4. En el Explorador de soluciones, expanda la carpeta **Modelos** y abra el archivo de modelo de datos que termina con *Context.cs*.

6. Modifique el constructor de instancia **DbContext** para que se asemeje al siguiente snippet:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDatabase")
            {
            }
        
            // snipped
        }

	El servicio usará ahora la nueva cadena de conexión híbrida definida en Azure.

5. Publique los cambios y use una aplicación cliente conectada a su servicio móvil para invocar algunas operaciones de generación de cambios en la base de datos.

6. Abra SQL Management Studio en el equipo local donde se ejecuta SQL Server y luego, en el Explorador de objetos, expanda la base de datos **OnPremisesDB** y expanda **Tablas**. 

9. Haga clic con el botón derecho en la tabla **hybridService1.TodoItems** y elija **Seleccionar las 1000 filas principales** para ver los resultados.

	![SQL Management Studio][SMS]

El servicio móvil ha transferido los cambios generados en la aplicación a la base de datos local.

##Otras referencias##
 
+ [Sitio web de conexiones híbridas](http://azure.microsoft.com/services/biztalk-services/)
+ [Introducción a las conexiones híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)
+ [Servicios de BizTalk: Pestañas Panel, Monitor, Escala, Configurar y Conexión híbrida](http://azure.microsoft.com/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

<!-- IMAGES -->
 
[CreateBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
[ConfigureBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
[AddHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
[ConfigureHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
[HCCreated]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
[InstallHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
[HCMSetup]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
[HCConnected]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
[AssociateHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
[PickHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
[ConnectionString]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
[SMS]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
[DownloadHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png


<!--HONumber=42-->
