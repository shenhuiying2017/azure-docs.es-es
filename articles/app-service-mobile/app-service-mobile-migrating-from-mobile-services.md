---
title: "Migración desde Servicios móviles a una Aplicación móvil del Servicio de aplicaciones"
description: "Obtenga información acerca de cómo migrar fácilmente la aplicación Servicios móviles a una Aplicación móvil del Servicio de aplicaciones"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: f3d89c627f462c9e34b2ff067972be56f5bed32f
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="article-top"></a>Migración del servicio móvil de Azure existente al Servicio de aplicaciones de Azure
Con la [disponibilidad general del Servicio de aplicaciones de Azure], los sitios de Servicios móviles de Azure se pueden migrar fácilmente in situ para aprovechar todas las características del Servicio de aplicaciones de Azure.  En este documento se explica lo que puede esperar al migrar su sitio de Servicios móviles de Azure al Servicio de aplicaciones de Azure.

## <a name="what-does-migration-do"></a>Qué repercusiones tiene la migración para su sitio
La migración de su servicio móvil de Azure convierte dicho servicio en [Azure App Service] sin que ello afecte al código.  Las instancias de Notification Hubs, la conexión de datos SQL, la configuración de la autenticación, los trabajos programados y el nombre de dominio permanecen sin cambios.  Los clientes móviles que usan el servicio móvil de Azure siguen funcionando con normalidad.  La migración reinicia el servicio una vez que se ha transferido a Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por qué debe migrar el sitio
Microsoft recomienda migrar el servicio móvil de Azure para aprovechar las características del Servicio de aplicaciones de Azure, como por ejemplo:

* Las nuevas características de host, como [WebJobs] y [nombres de dominio personalizados].
* Supervisión y solución de problemas con New Relic o [Application Insights].
* Herramientas integradas de DevOps, entre las que se incluyen [ranuras de ensayo], reversión y pruebas en producción.
* [Escalado automático], equilibrio de carga y [supervisión del rendimiento].

Para más información sobre las ventajas del Servicio de aplicaciones de Azure, consulte el tema [Servicios móviles frente al Servicio de aplicaciones].

## <a name="before-you-begin"></a>Antes de empezar
Antes de empezar cualquier trabajo importante en su sitio, debe hacer una copia de seguridad de los scripts y la base de datos SQL de Mobile Service.

## <a name="migrating-site"></a>Migración de los sitios
El proceso de migración migra todos los sitios de una sola región de Azure.

Para migrar el sitio:

1. Inicie sesión en el [Portal de Azure clásico].
2. Seleccione un servicio móvil en la región que desea migrar.
3. Haga clic en el botón **Migrar al Servicio de aplicaciones**.

   ![El botón migrar][0]
4. Lea el cuadro de diálogo Migrar al Servicio de aplicaciones.
5. Escriba el nombre del servicio móvil en el cuadro correspondiente.  Por ejemplo, si el nombre de dominio es contoso.azure-mobile.net, escriba *contoso* en el cuadro proporcionado.
6. Haga clic en el botón de la marca.

Supervise el estado de la migración en el monitor de actividad. El sitio aparece en la lista de los que *migran* del Portal de Azure clásico.

  ![Monitor de actividad de migración][1]

Cada migración puede tardar entre 3 y 15 minutos por cada servicio móvil migrado.  El sitio permanece disponible durante la migración.
El sitio se reinicia al final del proceso de migración.  El sitio no está disponible durante el proceso de reinicio, que puede durar un par de segundos.

## <a name="finalizing-migration"></a>Finalización de la migración
Planee la prueba del sitio desde un cliente móvil al final del proceso de migración.  Asegúrese de que puede realizar todas las acciones comunes de cliente sin que el cliente móvil experimente cambios.  

### <a name="update-app-service-tier"></a>Selección de un plan de tarifa adecuado del Servicio de aplicaciones
Después de migrar al Servicio de aplicaciones de Azure, dispone de una mayor flexibilidad en los precios.

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. La hoja Configuración se abre de forma predeterminada.
4. Haga clic en **Plan de App Service** en el menú Configuración.
5. Haga clic en el icono **Plan de tarifa**.
6. Haga clic en el icono adecuado para sus requisitos y luego en **Seleccionar**.  Puede que deba hacer clic en **Ver todo** para ver los planes de tarifa disponibles.

Como punto de partida, se recomiendan los siguientes planes:

| Plan de tarifa del servicio móvil | Plan de tarifa del Servicio de aplicaciones |
|:--- |:--- |
| Gratuito |F1 Gratis |
| Básica |Básico B1 |
| Estándar |S1 Estándar |

Hay una flexibilidad considerable en la elección del plan de tarifa adecuado para su aplicación.  Consulte [Precios de Servicio de aplicaciones] para obtener detalles sobre los precios de su nuevo Servicio de aplicaciones.

> [!TIP]
> El nivel Estándar de App Service contiene acceso a muchas características que puede usar, entre las que se incluyen las [ranuras de ensayo], las copias de seguridad automáticas y el escalado automático.  Examine las nuevas funciones mientras está ahí.
>
>

### <a name="review-migration-scheduler-jobs"></a>Revisión de los trabajos del Programador migrados
Los trabajos del Programador no estarán visibles hasta unos 30 minutos después de la migración.  Los trabajos programados se siguen ejecutando en segundo plano.
Para ver los trabajos programados cuando vuelven a estar visibles:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Examinar >**, escriba **Programación** en el cuadro *Filtro* y luego seleccione **Colecciones de Scheduler**.

Existe un número limitado de trabajos de Programador gratuitos que están disponibles después de la migración.  Revise su uso y los [planes de Azure Scheduler].

### <a name="configure-cors"></a>Configuración de CORS si es necesario
El uso compartido de recursos entre orígenes es una técnica que permite que un sitio web acceda a una API web en un dominio diferente.  Si usa Azure Mobile Services con un sitio web asociado, será preciso que configure CORS como parte de la migración.  Si accede a Azure Mobile Services exclusivamente desde dispositivos móviles, no es necesario configurar CORS, salvo en casos excepcionales.

La configuración de CORS migrada está disponible como la configuración de aplicación **MS_CrossDomainWhitelist**.  Para migrar el sitio a las instalaciones de CORS del Servicio de aplicaciones:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. La hoja Configuración se abre de forma predeterminada.
4. Haga clic en **CORS** en el menú API.
5. Especifique los orígenes permitidos en el cuadro que aparece y presione ENTRAR después de cada uno de ellos.
6. Cuando la lista de orígenes permitidos sea correcta, haga clic en el botón Guardar.

> [!TIP]
> Una de las ventajas de usar un Servicio de aplicaciones de Azure es que puede ejecutar su sitio web y el servicio móvil en el mismo sitio.  Para más información, consulte la sección [Pasos siguientes](#next-steps).
>
>

### <a name="download-publish-profile"></a>Descarga de un nuevo perfil de publicación
El perfil de publicación del sitio cambia al migrar al Servicio de aplicaciones de Azure.  Si va a publicar el sitio desde dentro de Visual Studio, necesita un perfil de publicación nuevo.  Para descargar el nuevo perfil de publicación:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. Haga clic en **Obtener perfil de publicación**.

El archivo PublishSettings se descarga en su equipo.  Normalmente se llama *nombre_del_sitio*.PublishSettings.  Importe la configuración de publicación en el proyecto existente:

1. Abra Visual Studio y el proyecto de Servicio móvil de Azure.
2. Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Publicar...**
3. Haga clic en **Importar**
4. Haga clic en **Examinar** y seleccione el archivo de configuración de publicación descargado.  Haga clic en **Aceptar**
5. Haga clic en **Validar conexión** para asegurarse que la configuración de publicación funciona.
6. Haga clic en **Publicar** para publicar el sitio.

## <a name="working-with-your-site"></a>Migración posterior al sitio
Empiece a trabajar con la nueva instancia de App Service en la fase posterior a la migración de [Azure Portal].  Las siguientes son algunas notas sobre operaciones específicas que se suelen realizar en el [Portal de Azure clásico], junto con su equivalente del Servicio de aplicaciones.

### <a name="publishing-your-site"></a>Descarga y publicación del sitio migrado
El sitio está disponible a través de git o ftp, y se puede volver a publicar con varios mecanismos diferentes, como WebDeploy, TFS, Mercurial, GitHub y FTP.  Las credenciales de implementación se migran con el resto del sitio.  Si no estableció las credenciales de implementación o no las recuerda, puede restablecerlas:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. La hoja Configuración se abre de forma predeterminada.
4. Haga clic en **Credenciales de implementación** en el menú PUBLICACIÓN.
5. Escriba las nuevas credenciales de implementación en los cuadros correspondientes y haga clic en el botón Guardar.

Dichas credenciales se pueden usar para clonar el sitio con git o configurar implementaciones automatizadas desde GitHub, TFS o Mercurial.  Para más información, consulte la [documentación de implementación de Azure App Service].

### <a name="appsettings"></a>Configuración de aplicación
La mayoría de las configuraciones de un servicio móvil migrado están disponible a través de Configuración de aplicación.  En [Azure Portal] puede obtener una lista de las opciones de la aplicación.
Para ver o cambiar la configuración de aplicación:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. La hoja Configuración se abre de forma predeterminada.
4. Haga clic en **Configuración de aplicación** en el menú GENERAL.
5. Desplácese hasta la sección Configuración de aplicación y busque su configuración de aplicación.
6. Haga clic en el valor de la configuración de aplicación para editarlo.  Haga clic en **Guardar** para guardar el valor.

Puede actualizar varias configuraciones de aplicación al mismo tiempo.

> [!TIP]
> Observará que hay dos opciones de la aplicación con el mismo valor.  Por ejemplo, puede ver *ApplicationKey* y *MS\_ApplicationKey*.  Actualice ambas al mismo tiempo.
>
>

### <a name="authentication"></a>Autenticación
Todas las configuraciones de autenticación están disponibles como configuración de aplicación en su sitio migrado.  Para actualizar la configuración de autenticación, debe modificar la configuración de aplicación adecuada.  En la siguiente tabla se muestra la configuración de aplicación adecuada para el proveedor de autenticación:

| Proveedor | Id. de cliente | Secreto del cliente | Otras configuraciones |
|:--- |:--- |:--- |:--- |
| Cuenta Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Nota: **MS\_AadTenants** se almacena como una lista de dominios de inquilino separados por coma (los campos "Inquilinos permitidos" del Portal de Mobile Services).

> [!WARNING]
> **No utilice los mecanismos de autenticación del menú Configuración.**
>
> Azure App Service proporciona un sistema de autenticación y autorización "sin código" independiente en el menú Configuración de *Autenticación y autorización*, y la opción (en desuso) *Autenticación móvil* del menú Configuración.  Estas opciones no son compatibles con un servicio móvil de Azure migrado.  Puede [actualizar su sitio](app-service-mobile-net-upgrading-from-mobile-services.md) para aprovechar la autenticación del Servicio de aplicaciones de Azure.
>
>

### <a name="easytables"></a>Datos
La pestaña *Datos* de Mobile Services se ha reemplazado por *Tablas fáciles* en Azure Portal.  Para tener acceso a Tablas fáciles:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. La hoja Configuración se abre de forma predeterminada.
4. Haga clic en **Tablas fáciles** en el menú MÓVIL.

Para agregar una tabla, haga clic en el botón **Agregar** o acceda a sus tablas existentes haciendo clic en el nombre de una tabla.  En esta hoja se pueden realizar varias operaciones, entre las que se incluyen:

* Cambiar los permisos de tabla
* Editar los scripts operativos
* Administrar el esquema de tabla
* Eliminar la tabla
* Borrar el contenido de la tabla
* Eliminar filas específicas de la tabla

### <a name="easyapis"></a>API
La pestaña *API* de Mobile Services se ha reemplazado por *API fáciles* en Azure Portal.  Para obtener acceso a las API fáciles:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. La hoja Configuración se abre de forma predeterminada.
4. Haga clic en **API fáciles** en el menú MÓVIL.

Las API migradas ya aparecen en la lista de la hoja.  También puede agregar una API desde esta hoja.  Para administrar una API concreta, haga clic en ella.
Desde la nueva hoja, puede ajustar los permisos y editar los scripts de la API.

### <a name="on-demand-jobs"></a>Trabajos del Programador
Todos los trabajos de Programador están disponibles a través de la sección de colecciones de trabajo de Programador.  Para acceder a los trabajos de Programador:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Examinar >**, escriba **Programación** en el cuadro *Filtro* y luego seleccione **Colecciones de Scheduler**.
3. Seleccione la colección de trabajos para su sitio.  Se denomina *nombre_del_sitio*-Jobs.
4. Haga clic en **Configuración**.
5. Haga clic en **Trabajos del Programador** en ADMINISTRAR.

Los trabajos programados se enumeran con la frecuencia que especificó antes de la migración.  Los trabajos a petición se deshabilitan.  Para ejecutar un trabajo a petición:

1. Seleccione el trabajo que desee ejecutar.
2. Si es necesario, haga clic en **Habilitar** para habilitar el trabajo.
3. Haga clic en **Configuración** y después en **Programar**.
4. Seleccione **Una vez** como periodicidad y haga clic en **Guardar**

Los trabajos a petición se encuentran en `App_Data/config/scripts/scheduler post-migration`.  Es recomendable convertir todos los trabajos a petición a [WebJobs] o [Functions].  Escriba los nuevos trabajos del programador como [WebJobs] o [Functions].

### <a name="notification-hubs"></a>Centros de notificaciones
Los Servicios móviles usan Centros de notificaciones para las notificaciones push.  Las siguientes configuraciones de aplicación se usan para vincular el centro de notificaciones al servicio móvil tras la migración:

| Configuración de aplicación | DESCRIPCIÓN |
|:--- |:--- |
| **MS\_PushEntityNamespace** |El espacio de nombres del centro de notificaciones. |
| **MS\_NotificationHubName** |El nombre del centro de notificaciones. |
| **MS\_NotificationHubConnectionString** |La cadena de conexión del centro de notificaciones |
| **MS\_NamespaceName** |Un alias para MS_PushEntityNamespace |

El Centro de notificaciones se administra mediante [Azure Portal].  Anote el nombre del centro de notificaciones (puede encontrarlo mediante la configuración de aplicación):

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Examinar**> y luego **Notification Hubs**
3. Haga clic en el nombre del Centro de notificaciones asociado al servicio móvil.

> [!NOTE]
> Si el Centro de notificaciones es del tipo "Mixto", no está visible.  Los centros de notificaciones mixtos usan características de Centros de notificaciones y características heredadas de Bus de servicio.  [Convierta los espacios de nombres mixtos] antes de continuar.  Una vez finalizada la conversión, el Centro de notificaciones aparece en [Azure Portal].
>
>

Para más información, revise la documentación de [Centros de notificaciones] .

> [!TIP]
> Las características de administración de Notification Hubs en [Azure Portal] se encuentran aún en versión preliminar.  El [Portal de Azure clásico] sigue estando disponible para administrar todos los centros de notificaciones.
>
>

### <a name="legacy-push"></a>Configuración de inserción heredada
Si configuró Inserción en el servicio móvil antes de la introducción en Notification Hubs, usa *inserción heredada*.  Si utiliza Inserción y no ve un Centro de notificaciones en la configuración, es probable que utilice *inserción heredada*.  Esta característica se migra con todas las demás.  Pero se recomienda que actualice a los Centros de notificaciones poco después de completar la migración.

Mientras tanto, toda la configuración de inserción heredada (con la excepción notable de los certificados de APN) está disponible en Configuración de aplicación.  Para actualizar el certificado de APNS, reemplace el archivo adecuado en el sistema de archivos.

### <a name="app-settings"></a>Otra configuración de aplicación
La siguiente configuración de aplicación adicional se migra desde el servicio móvil y está disponible en *Configuración* > *App Configuración*:

| Configuración de aplicación | DESCRIPCIÓN |
|:--- |:--- |
| **MS\_MobileServiceName** |El nombre de la aplicación. |
| **MS\_MobileServiceDomainSuffix** |El prefijo del dominio. es decir, azure-mobile.net |
| **MS\_ApplicationKey** |La clave de aplicación. |
| **MS\_MasterKey** |La clave maestra de aplicación. |

La clave de la aplicación y la clave maestra son idénticas a las claves de aplicación del servicio móvil.  En concreto, los clientes móviles envían la clave de aplicación para validar su uso de la API móvil.

### <a name="cliequivalents"></a>Equivalentes de línea de comandos
Ya no puede usar el comando *azure mobile* para administrar el sitio de Azure Mobile Services.  En su lugar, muchas de las funciones se han reemplazado por el comando *azure site*.  Utilice la siguiente tabla para buscar equivalentes para los comandos más comunes:

| Comando de *Azure Mobile* | Comando equivalente del *sitio de Azure* |
|:--- |:--- |
| mobile locations |site location list |
| mobile list |site list |
| mobile show *name* |site show *name* |
| mobile restart *name* |site restart *name* |
| mobile redeploy *name* |site deployment redeploy *commitId* *name* |
| mobile key set *name* *type* *value* |site appsetting delete *key* *name* <br/> site appsetting add *key*=*value* *name* |
| mobile config list *name* |site appsetting list *name* |
| mobile config get *name* *key* |site appsetting show *key* *name* |
| mobile config set *name* *key* |site appsetting delete *key* *name* <br/> site appsetting add *key*=*value* *name* |
| mobile domain list *name* |site domain list *name* |
| mobile domain add *name* *domain* |site domain add *domain* *name* |
| mobile domain delete *name* |site domain delete *domain* *name* |
| mobile scale show *name* |site show *name* |
| mobile scale change *name* |site scale mode *mode* *name* <br /> site scale instances *instances* *name* |
| mobile appsetting list *name* |site appsetting list *name* |
| mobile appsetting add *name* *key* *value* |site appsetting add *key*=*value* *name* |
| mobile appsetting delete *name* *key* |site appsetting delete *key* *name* |
| mobile appsetting show *name* *key* |site appsetting delete *key* *name* |

Para actualizar la configuración de autenticación o de notificaciones push, actualice la configuración de aplicación adecuada.
Edite los archivos y publique su sitio mediante ftp o git.

### <a name="diagnostics"></a>Diagnósticos y registro
El registro de diagnóstico está normalmente deshabilitado en un Servicio de aplicaciones de Azure.  Para habilitar el registro de diagnóstico:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. La hoja Configuración se abre de forma predeterminada.
4. Seleccione **Registros de diagnóstico** en el menú de CARACTERÍSTICAS.
5. Haga clic en **ACTIVAR** en los registros siguientes: **Registro de la aplicación (Filesystem)**, **Mensajes de error detallados** y **Seguimiento de solicitudes con error**
6. Haga clic en **Sistema de archivos** en el registro de servidor web.
7. Haga clic en **Guardar**

Para ver los registros:

1. Inicie sesión en el [Azure Portal].
2. Seleccione **Todos los recursos** o **App Services**, y luego haga clic en el nombre del servicio móvil migrado.
3. Haga clic en el botón **Herramientas**
4. Seleccione **Secuencia de registro** en el menú de RESPETAR.

Los registros se muestran en la ventana a medida que se generan.  También puede descargar los registros para analizarlos posteriormente mediante sus credenciales de implementación. Para más información, consulte el documento [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure].

## <a name="known-issues"></a>Problemas conocidos
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>La eliminación de un clon de aplicaciones móviles migradas provoca una interrupción del sitio
Si clona un servicio móvil migrado mediante Azure PowerShell y después elimina el clon, se quita la entrada DNS del servicio de producción.  Deja de ser posible acceder al sitio desde Internet.  

Resolución: si desea clonar un sitio, hágalo a través del portal.

### <a name="changing-webconfig-does-not-work"></a>El cambio de Web.config no funciona
Si tiene un sitio de ASP.NET, los cambios realizados en el archivo `Web.config` no se aplican.  El Servicio de aplicaciones de Azure crea un archivo `Web.config` adecuado durante el inicio para admitir el tiempo de ejecución de Servicios móviles.  Puede reemplazar determinadas configuraciones (como los encabezados personalizados) mediante un archivo de transformación XML.  Cree un archivo llamado `applicationHost.xdt`: este archivo debe terminar en el directorio `D:\home\site` en el Servicio de Azure.  Cargue el archivo `applicationHost.xdt` a través de un script de implementación personalizado o directamente mediante Kudu.  A continuación se muestra un documento de ejemplo:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Para más información, consulte el documento [Ejemplos de transformación de XDT] en GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Los Servicios móviles migrados no se pueden agregar al Administrador de tráfico
Cuando se crea un perfil de Traffic Manager, no se puede elegir directamente un servicio móvil migrado para el perfil.  Use un "punto de conexión externo".  El punto de conexión externo solo puede agregarse a través de PowerShell.  Para más información, consulte el [tutorial de Traffic Manager](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Pasos siguientes
Ahora que la aplicación se ha migrado a App Service, hay aún más características que puede utilizar:

* Las [ranuras de ensayo] de implementación le permiten ensayar cambios en el sitio y ejecutar pruebas A/B.
* [WebJobs] cuenta con un sustituto para los trabajos programados a petición.
* Para [implementar de forma continuada] el sitio, vincúlelo a GitHub, TFS o Mercurial.
* Puede usar [Application Insights] para supervisar el sitio.
* Proporcionar servicio a un sitio web y a una API móvil con el mismo código.

### <a name="upgrading-your-site"></a>Actualización del sitio de Servicios móviles al SDK de Aplicaciones móviles
* Para los proyectos de servidor basados en Node.js, el nuevo [SDK de Node.js de Mobile Apps] proporciona varias características nuevas. Por ejemplo, ahora puede desarrollar y depurar localmente, usar cualquier versión de Node.js posterior a la 0.10 y personalizar con cualquier middleware de Express.js.
* En el caso de los proyectos de servidor basados en .NET, los nuevos [paquetes de NuGet del SDK de Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) tienen más flexibilidad en las dependencias de NuGet.  Estos paquetes admiten la nueva autenticación de App Service y se crean con cualquier proyecto de ASP.NET. Para más información sobre la actualización, consulte [Actualización del servicio móvil de .NET existente a Servicio de aplicaciones](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Precios de Servicio de aplicaciones]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Escalado automático]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-web-overview.md
[Portal de Azure clásico]: https://manage.windowsazure.com
[Azure Portal]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[planes de Azure Scheduler]: ../scheduler/scheduler-plans-billing.md
[implementar de forma continuada]: ../app-service/app-service-continuous-deployment.md
[Convierta los espacios de nombres mixtos]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nombres de dominio personalizados]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidad general del Servicio de aplicaciones de Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure]: ../app-service/web-sites-enable-diagnostic-log.md
[SDK de Node.js de Mobile Apps]: https://github.com/azure/azure-mobile-apps-node
[Servicios móviles frente al Servicio de aplicaciones]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Centros de notificaciones]: ../notification-hubs/notification-hubs-push-notification-overview.md
[supervisión del rendimiento]: ../app-service/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[ranuras de ensayo]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[Ejemplos de transformación de XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Functions]: ../azure-functions/functions-overview.md
