---
title: "Migración de una aplicación web de empresa al Servicio de aplicaciones de Azure"
description: "Muestra cómo utilizar el Asistente para migración de Aplicaciones web para migrar rápidamente sitios web de IIS existentes a Aplicaciones web del Servicio de aplicaciones de Azure"
services: app-service
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: 
ms.assetid: 2e846fc0-37cc-42e6-ac57-ff442ef16e85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 6fdee57d33b19569ef892d0d32ea7007fd69faaf
ms.lasthandoff: 03/23/2017


---
# <a name="migrate-an-enterprise-web-app-to-azure-app-service"></a>Migración de una aplicación web de empresa al Servicio de aplicaciones de Azure
Puede migrar fácilmente sus sitios web existentes que se ejecutan en Internet Information Service (IIS) 6 o posterior a [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). 

> [!IMPORTANT]
> El 14 de julio de 2015 Windows Server 2003 llegó al final de su soporte técnico. Si sus sitios web están alojados actualmente en un servidor IIS que es Windows Server 2003, Aplicaciones web es una forma con bajo riesgo, bajo costo y baja fricción de mantener sus sitios web en línea; el Asistente para migración de Aplicaciones web puede ayudar a automatizar el proceso de migración. 
> 
> 

[Asistente para migración de Aplicaciones web](https://www.movemetothecloud.net/) puede analizar la instalación del servidor IIS, identificar los sitios que se pueden migrar al Servicio de aplicaciones, resaltar cualquier elemento que no se pueda migrar o no se admita en la plataforma y, a continuación, migrar los sitios web y bases de datos asociadas a Azure.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="elements-verified-during-compatibility-analysis"></a>Elementos comprobados durante el análisis de compatibilidad
El Asistente para migración crea un informe de preparación para identificar las posibles causas de problemas de preocupación o bloqueo que pueden evitar una migración correcta de un IIS local a Aplicaciones web del Servicio de aplicaciones de Azure. Algunos de los elementos claves que se deben tener en cuenta son:

* Enlaces de puerto: Aplicaciones web solo admite el puerto 80 para el tráfico HTTP y el puerto 443 para el tráfico HTTPS. Se ignorarán otras configuraciones de puerto y el tráfico se redirigirá a 80 o 443. 
* Autenticación: Aplicaciones web admite la autenticación anónima de forma predeterminada y la autenticación de formularios cuando una aplicación lo especifique. Puede utilizarse la autenticación de Windows solo mediante la integración con Azure Active Directory y ADFS. Todos las demás formas de autenticación, por ejemplo la autenticación básica, no se admiten actualmente. 
* Caché global de ensamblados (GAC): la GAC no se admite en Aplicaciones web. Si la aplicación hace referencia a ensamblados que se implementan normalmente en la GAC, tendrá que implementarlos en la carpeta bin de la aplicación en Aplicaciones web. 
* Modo de compatibilidad con IIS5: no admitido en Aplicaciones web. 
* Grupos de aplicaciones: en Aplicaciones web, cada sitio y sus aplicaciones secundarias se ejecutan en el mismo grupo de aplicaciones. Si el sitio tiene varias aplicaciones secundarias con varios grupos de aplicaciones, se deben consolidar en un único grupo de aplicaciones con la misma configuración, o bien migrar cada aplicación a una aplicación web independiente.
* Componentes COM: Aplicaciones web no permite el registro de componentes COM en la plataforma. Si los sitios web o las aplicaciones utilizan algún componente COM, debe volver a escribirlos en código administrado e implementarlos con el sitio web o la aplicación.
* Filtros ISAPI: aplicaciones web compatibles con el uso de las extensiones ISAPI. Debe hacer lo siguiente:
  
  * implementar los archivos DLL con su aplicación web 
  * registrar los archivos DLL mediante [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
  * colocar un archivo applicationHost.xdt en la raíz del sitio con el contenido que se describe en la sección "Allowing arbitrary ISAPI extensions to be loaded" (Permitir la carga de extensiones ISAPI) [de este artículo](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples) 
    
  
    
    Para obtener más ejemplos de cómo utilizar las transformaciones de documentos XML con su sitio web, consulte [Transformación de su sitio web de Microsoft Azure](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).
* Otros componentes, como SharePoint, extensiones de servidor de FrontPage (FPSE), FTP o certificados SSL, no se migrarán.

## <a name="how-to-use-the-web-apps-migration-assistant"></a>Uso del Asistente para migración de Aplicaciones web
En esta sección se analiza un ejemplo para migrar algunos sitios web que utilizan una base de datos de SQL Server y se ejecutan en un equipo de Windows Server 2003 R2 (IIS 6.0) local:

1. En el servidor IIS o en el equipo cliente, navegue a [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/) 
   
   ![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)
2. Instale el Asistente para migración de Aplicaciones web mediante un clic en el botón **Servidor IIS dedicado** . Próximamente se pondrán más opciones a disposición de los usuarios. 
3. Haga clic en el botón **Herramienta de instalación** para instalar el Asistente de migración de Aplicaciones web en la máquina.
   
   ![](./media/web-sites-migration-from-iis-server/install-page.png)
   
   > [!NOTE]
   > También puede hacer clic en **Download for offline install** (Descargar para instalación sin conexión) para descargar un archivo ZIP que se instala en servidores sin conexión a internet. O bien, puede hacer clic en **Cargar un informe existente sobre el estado de preparación de la migración**, que es una opción avanzada para trabajar con un informe sobre el estado de preparación de la migración existente que ha generado previamente (se explica más adelante).
   > 
   > 
4. En la pantalla **Instalación de la aplicación**, haga clic en **Instalar** para instalar en el equipo. También instalará las dependencias correspondientes como Web Deploy, DacFX e IIS, si es necesario. 
   
   ![](./media/web-sites-migration-from-iis-server/install-progress.png)
   
   Una vez instalado, el Asistente para migración de Aplicaciones web se inicia automáticamente.
5. Seleccione **Migración de sitios y bases de datos desde un servidor remoto a Azure**. Escriba las credenciales administrativas para el servidor remoto y haga clic en **Continuar**. 
   
   ![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)
   
   Por supuesto, puede optar por migrar desde el servidor local. La opción remota resulta útil cuando desea migrar sitios web desde un servidor IIS de producción.
   
   En este punto, la herramienta de migración inspeccionará la configuración del servidor IIS, como sitios, aplicaciones, grupos de aplicaciones y dependencias para identificar sitios web candidatos para la migración. 
6. La captura de pantalla siguiente muestra tres sitios web: **sitio web predeterminado**, **TimeTracker** y **CommerceNet4**. Todos ellos tienen una base de datos asociada que deseamos migrar. Seleccione todos los sitios que desea evaluar y, a continuación, haga clic en **Siguiente**.
   
   ![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
7. Haga clic en **Cargar** para cargar el informe de preparación. Si hace clic en **Guardar el archivo localmente**, puede volver a ejecutar la herramienta de migración más tarde y cargar el informe de preparación guardado como se indicó anteriormente.
   
   ![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
   
   Una vez que cargue el informe sobre el estado de preparación, Azure realiza análisis de preparación y muestra los resultados. Lea los detalles de evaluación para cada sitio web y asegúrese de que los entiende y de que ha solucionado todos los problemas antes de continuar. 
   
   ![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)
8. Haga clic en **Iniciar migración** para comenzar la migración. Se le redirigirá ahora a Azure para que inicie sesión en su cuenta. Es importante que inicie sesión con una cuenta que tenga una suscripción activa de Azure. Si no tiene una cuenta de Azure, puede registrarse [aquí](https://azure.microsoft.com/pricing/free-trial/?WT.srch=1&WT.mc_ID=SEM_) para obtener una evaluación gratuita. 
9. Seleccione la cuenta de inquilino, la suscripción de Azure y la región que se van a utilizar para las bases de datos y las aplicaciones web de Azure migradas y haga clic en **Start Migration**(Iniciar migración). Puede seleccionar los sitios web que se migrarán más tarde.
   
   ![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)
10. En la siguiente pantalla puede realizar cambios en la configuración predeterminada de migración, como:
    
    * utilizar una Base de datos SQL de Azure existente o crear una nueva Base de datos SQL de Azure y configurar sus credenciales
    * seleccionar los sitios web para migrar
    * definir nombres para las aplicaciones web de Azure y sus bases de datos SQL vinculadas
    * personalizar la configuración global y la configuración a nivel de sitio
    
    La captura de pantalla siguiente muestra todos los sitios web seleccionados para la migración con la configuración predeterminada.
    
    ![](./media/web-sites-migration-from-iis-server/migration-settings.png)
    
    > [!NOTE]
    > La casilla **Habilitar Azure Active Directory** en la configuración personalizada integra la aplicación web de Azure con [Azure Active Directory](../active-directory/active-directory-whatis.md) (el **directorio predeterminado**). Para obtener más información sobre la sincronización de Azure Active Directory con su Active Directory local, consulte [Integración de directorios](http://msdn.microsoft.com/library/jj573653).
    > 
    > 
11. Una vez realizados todos los cambios deseados, haga clic en **Crear** para iniciar el proceso de migración. La herramienta de migración creará la Base de datos SQL Azure y la aplicación web de Azure y, a continuación, publicará el contenido del sitio web y las bases de datos. El proceso de migración se muestra claramente en la herramienta de migración y verá una pantalla de resumen al final que detalla los sitios migrados, si se realizaron correctamente, así como los vínculos a las aplicaciones web de Azure recién creadas. 
    
    Si se produce un error durante la migración, la herramienta de migración indicará claramente el error y revertirá los cambios. También podrá enviar el informe de errores directamente al equipo de ingeniería haciendo clic en el botón **Enviar informe de errores** , con la captura de la pila de llamadas de error y el cuerpo del mensaje de la compilación. 
    
    ![](./media/web-sites-migration-from-iis-server/migration-error-report.png)
    
    Si migra correctamente sin errores, también puede hacer clic en el botón **Enviar comentarios** para hacer llegar sus comentarios directamente. 
12. Haga clic en los vínculos a las aplicaciones web de Azure y compruebe que la migración se ha realizado correctamente.
13. Ahora puede administrar las aplicaciones web migradas en el Servicio de aplicaciones de Azure. Para ello, inicie sesión en el [Portal de Azure](https://portal.azure.com).
14. En el Portal de Azure, abra la hoja Aplicaciones web para ver sus sitios web migrados (se muestran como aplicaciones web) y, a continuación, haga clic en uno de ellos para empezar a administrar la aplicación web, como configurar publicaciones continuas, crear copias de seguridad, realizar un escalado automático y supervisar el uso o el rendimiento.
    
    ![](./media/web-sites-migration-from-iis-server/TimeTrackerMigrated.png)

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

## <a name="whats-changed"></a>Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)


