<properties urlDisplayName="Migrate from IIS to Azure Websites with Migration Assistant" pageTitle="Migración de Sitios web de IIS a Sitios web Azure mediante el Asistente para migración"metaKeywords =" Sitios web Azure, migración, migrar, IIS" description="Muestra cómo usar el Asistente para la migración de sitios web de Azure para migrar rápidamente sitios web de IIS existentes a sitios web de Azure" metaCanonical="" services="web-sites" documentationCenter="" title="Migrate your IIS Websites to Azure Websites using the Migration Assistant" authors="cephalin,anwestg"  solutions="" writer="cephalin" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="cephalin" />

# Migración de Sitios web de IIS a Sitios web Azure mediante el Asistente para migración #
Puede migrar fácilmente a Sitios web Azure sus sitios web existentes que se ejecutan en Internet Information Service (IIS) 6 o posterior. El [Asistente para migración de Sitios web Azure](https://www.movemetothecloud.net/) puede analizar la instalación del servidor IIS, identificar qué sitios se pueden migrar a Sitios web Azure, destacar los elementos que no se pueden migrar o que no se admiten en la plataforma y, a continuación, migrar los sitios web y las bases de datos asociadas a Azure.

>[WACOM.NOTE]Windows Server 2003 llegará al fin de su periodo de soporte técnico el 14 de julio de 2015. Si actualmente tiene sus sitios web en un servidor IIS de Windows Server 2003, Sitios web Azure representa un método de bajo riesgo, bajo coste y baja fricción para mantener sus sitios web en línea y el Asistente para migración de Sitios web Azure puede ayudar a automatizar el proceso de migración en su lugar. 

## Elementos comprobados durante el análisis de compatibilidad ##
El Asistente para migración de Sitios web Azure crea un informe sobre el estado de preparación para identificar las causas potenciales de problemas o las cuestiones que podrían imposibilitar una migración correcta desde IIS local a Sitios web Azure. Algunos de los elementos claves que se deben tener en cuenta son:

-	Enlaces de puerto: Sitios web Azure solo admite el puerto 80 para el tráfico HTTP y el puerto 443 para el tráfico HTTPS. Otras configuraciones de puerto se ignorarán y el tráfico se enrutará a 80 o 443.
-	Autenticación: Sitios web Azure admite la autenticación anónima de forma predeterminada y la autenticación de formularios cuando una aplicación lo especifique. Puede utilizarse la autenticación de Windows solo mediante la integración con Azure Active Directory y ADFS. Actualmente no se admiten otras formas de autenticación, como la autenticación básica. 
-	Caché global de ensamblados (GAC): la GAC no se admite en Sitios web Azure. Si la aplicación hace referencia a ensamblados que se implementan normalmente en la GAC, tendrá que implementar en la carpeta bin de aplicación en Sitios web Azure. 
-	Modo de compatibilidad IIS5: esto no es compatible con Sitios web Azure. 
-	Grupos de aplicaciones: en Sitios web Azure, cada sitio y sus aplicaciones secundarias se ejecutan en el mismo grupo de aplicaciones. Si el sitio tiene varias aplicaciones secundarias con varios grupos de aplicaciones, se deben consolidar en un único grupo de aplicaciones con la misma configuración o migrar cada aplicación a un sitio web independiente.
-	Componentes COM: Sitios web Azure no permite el registro de componentes COM en la plataforma. Si los sitios web o aplicaciones utilizan algún componente COM, debe volver a escribirlos en código administrado e implementarlos con el sitio web o la aplicación.
-	Filtros ISAPI: Sitios web Azure puede admitir el uso de los filtros ISAPI. Debe hacer lo siguiente:
	-	implementar los archivos DLL con su sitio web 
	-	registrar los archivos DLL mediante [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
	-	colocar un archivo applicationHost.xdt en la raíz del sitio con el siguiente contenido:

			?xml version="1.0"?
			<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
			<configSections>
			    <sectionGroup name="system.webServer">
			      <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
			    </sectionGroup>
			  </configSections>
			</configuration>

		Para obtener más ejemplos de cómo utilizar las transformaciones de documentos XML con su sitio web, consulte [Transformación de su sitio web de Microsoft Azure](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-	Otros componentes, como SharePoint, extensiones de servidor de FrontPage (FPSE), FTP o certificados SSL, no se migrarán.

## Utilización del Asistente para migración de Sitios web Azure ##
En esta sección se analiza un ejemplo para migrar algunos sitios web que utilizan una base de datos de SQL Server y se ejecutan en un equipo de Windows Server 2003 R2 (IIS 6.0) local:

1.	En el servidor IIS o en el equipo cliente, navegue hasta [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/) 

	![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.	Instale el Asistente para la migración de Sitios web Azure haciendo clic en el botón **IIS Server dedicado**. Próximamente se pondrán más opciones a disposición de los usuarios. 
4.	Haga clic en el botón **Herramienta de instalación** para instalar el Asistente para migración de Sitios web Azure en su equipo.

	![](./media/web-sites-migration-from-iis-server/install-page.png)

	>[WACOM.NOTE] También puede hacer clic en **Descargar para instalación sin conexión** para descargar un archivo comprimido para la instalación en servidores no conectados a Internet. O bien, puede hacer clic en **Cargar un informe existente sobre el estado de preparación de la migración**, que es una opción avanzada para trabajar con un informe sobre el estado de preparación de la migración existente que ha generado previamente (se explica más adelante).

5.	En la pantalla **Instalación de la aplicación**, haga clic en **Instalar** para instalar en el equipo. También instalará las dependencias correspondientes como Web Deploy, DacFX e IIS, si es necesario. 

	![](./media/web-sites-migration-from-iis-server/install-progress.png)

	Una vez instalado, el Asistente para migración de Sitios web Azure se inicia automáticamente.
  
6.	Seleccione **Migración de sitios y bases de datos desde un servidor remoto a Azure**. Escriba las credenciales administrativas para el servidor remoto y haga clic en **Continuar**. 

	![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

	Por supuesto, puede optar por migrar desde el servidor local. La opción remota resulta útil cuando desea migrar sitios web desde un servidor IIS de producción.
 
	En este punto, la herramienta de migración inspeccionará la configuración del servidor IIS, como sitios, aplicaciones, grupos de aplicaciones y dependencias para identificar sitios web candidatos para la migración. 

8.	La captura de pantalla siguiente muestra tres sitios web: **Sitio web predeterminado**, **TimeTracker** y **CommerceNet4**. Todos ellos tienen una base de datos asociada que deseamos migrar. Seleccione todos los sitios que desea evaluar y, a continuación, haga clic en **Siguiente**.

	![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.	Haga clic en **Cargar** para cargar el informe sobre el estado de preparación. Si hace clic en **Guardar el archivo localmente**, puede ejecutar la herramienta de migración de nuevo más tarde y cargar el informe sobre el estado de preparación guardado como se indicó anteriormente.

	![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
	Una vez que cargue el informe sobre el estado de preparación, Azure realiza análisis de preparación y muestra los resultados. Lea los detalles de evaluación para cada sitio web y asegúrese de que los entiende y de que ha solucionado todos los problemas antes de continuar. 
 
	![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12.	Haga clic en **Iniciar migración** para iniciar la migración. Se le redirigirá ahora a Azure para iniciar sesión en su cuenta. Es importante que inicie una sesión con una cuenta que tenga una suscripción activa de Azure. Si no tiene una cuenta de Azure, puede registrarse aquí para obtener una evaluación gratuita. 

13.	Seleccione la cuenta del inquilino, la suscripción de Azure y la región que se va a utilizar para los sitios web y las bases de datos de Azure migrados y, a continuación, haga clic en **Iniciar migración**. Puede seleccionar los sitios web para migrar más tarde.

	![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14.	En la siguiente pantalla puede realizar cambios en la configuración predeterminada de migración, como:

	- utilizar una Base de datos SQL de Azure existente o crear una nueva Base de datos SQL de Azure y configurar sus credenciales
	- seleccionar los sitios web para migrar
	- definir nombres para los sitios web de Azure y sus bases de datos SQL vinculadas
	- personalizar la configuración global y la configuración a nivel de sitio

	La captura de pantalla siguiente muestra todos los sitios web seleccionados para la migración con la configuración predeterminada.

	![](./media/web-sites-migration-from-iis-server/migration-settings.png)

	>[WACOM.NOTE] La casilla de verificación **Habilitar Azure Active Directory** en la configuración personalizada integra el sitio web de Azure con [Azure Active Directory](http://azure.microsoft.com/es-es/documentation/articles/active-directory-whatis/) (el **directorio predeterminado**). Para obtener más información sobre la sincronización de Azure Active Directory con su Active Directory local, vea [Integración de directorios](http://msdn.microsoft.com/library/jj573653).

16.	 Una vez realizados todos los cambios deseados, haga clic en **Crear** para iniciar el proceso de migración. La herramienta de migración creará la Base de datos SQL Azure y el sitio web de Azure y, a continuación, publicará el contenido del sitio web y las bases de datos. El proceso de migración se muestra claramente en la herramienta de migración y verá una pantalla de resumen al final que detalla los sitios migrados, si se realizaron con éxito y los vínculos a los sitios web de Azure recién creados. 

	Si se produce un error durante la migración, la herramienta de migración indicará claramente el error y revertirá los cambios. También podrá enviar el informe de errores directamente al equipo de ingeniería haciendo clic en el botón **Enviar informe de errores**, con la captura de la pila de llamadas de error y el cuerpo del mensaje de la compilación. 

	![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

	Si migra correctamente sin errores, también puede hacer clic en el botón **Enviar comentarios** para hacer llegar sus comentarios directamente. 
 
20.	Haga clic en los vínculos a los sitios web de Azure y compruebe que la migración se ha realizado correctamente.


