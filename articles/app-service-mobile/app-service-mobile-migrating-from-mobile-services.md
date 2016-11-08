---
title: Migración desde Servicios móviles a una Aplicación móvil del Servicio de aplicaciones
description: Obtenga información acerca de cómo migrar fácilmente la aplicación Servicios móviles a una Aplicación móvil del Servicio de aplicaciones
services: app-service\mobile
documentationcenter: ''
author: adrianhall
manager: dwrede
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: adrianhall

---
# <a name="article-top"></a>Migración del servicio móvil de Azure existente al Servicio de aplicaciones de Azure
Con la [disponibilidad general del Servicio de aplicaciones de Azure], los sitios de Servicios móviles de Azure se pueden migrar fácilmente in situ para aprovechar todas las características del Servicio de aplicaciones de Azure. En este documento se explica lo que puede esperar al migrar su sitio de Servicios móviles de Azure al Servicio de aplicaciones de Azure.

## <a name="what-does-migration-do"></a>Qué repercusiones tiene la migración para su sitio
La migración de su servicio móvil de Azure convertirá el servicio móvil en una aplicación del [Servicio de aplicaciones de Azure] sin que el código se vea afectado de ninguna forma. Los Centros de notificaciones, la conexión de datos SQL, la configuración de la autenticación, los trabajos programados y el nombre de dominio permanecerán sin cambios. Los clientes móviles que usan el servicio móvil de Azure seguirán funcionando con normalidad. La migración reiniciará el servicio una vez que se haya transferido al Servicio de aplicaciones de Azure.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por qué debe migrar el sitio
Microsoft recomienda migrar el servicio móvil de Azure para aprovechar las características del Servicio de aplicaciones de Azure, como por ejemplo:

* Nuevas características de host, como [WebJobs] y [nombres de dominio personalizados].
* Conectividad a los recursos locales mediante una [red virtual] además de [conexiones híbridas].
* Supervisión y solución de problemas con New Relic o [Application Insights].
* Herramientas integradas de DevOps, lo que incluye [ranuras de ensayo], reversión y pruebas en producción.
* [Escalado automático], equilibrio de carga y [supervisión del rendimiento].

Para más información sobre las ventajas del Servicio de aplicaciones de Azure, consulte el tema [Servicios móviles frente al Servicio de aplicaciones].

## <a name="before-you-begin"></a>Antes de empezar
Antes de empezar cualquier trabajo importante en su sitio, debe [Hacer una copia de seguridad de los scripts y la base de datos SQL de su servicio móvil].

Si quiere probar el proceso de migración antes de migrar su sitio de producción, duplique el servicio móvil de Azure de producción dentro de una nueva [Región de Azure] \(junto con una copia del origen de datos) y pruebe la migración con la nueva dirección URL. También necesitará una implementación del cliente de prueba que apunte al sitio de prueba para probar correctamente el sitio migrado.

## <a name="migrating-site"></a>Migración de los sitios
El proceso de migración migrará todos los sitios comprendidos en una sola región de Azure.

Para migrar el sitio:

1. Inicie sesión en el [Portal de Azure clásico].
2. Seleccione un servicio móvil en la región que desea migrar.
3. Haga clic en el botón **Migrar al Servicio de aplicaciones**.
   
   ![El botón migrar][0]
4. Lea el cuadro de diálogo Migrar al Servicio de aplicaciones.
5. Escriba el nombre del servicio móvil en el cuadro correspondiente. Por ejemplo, si el nombre de dominio es contoso.azure-mobile.net, escriba entonces *contoso* en el cuadro proporcionado.
6. Haga clic en el botón de marca.

Puede supervisar el estado de la migración en el monitor de actividad y el sitio se mostrará como *migrando* en el Portal de Azure clásico.

  ![Monitor de actividad de migración][1]

Cada migración puede tardar entre 3 y 15 minutos por cada servicio móvil migrado. Su sitio seguirá estando disponible durante la migración, pero se reiniciará al final del proceso. El sitio no estará disponible durante el proceso de reinicio, que puede durar unos segundos.

## <a name="finalizing-migration"></a>Finalización de la migración
Al término del proceso de migración, debe planear la prueba del sitio desde un cliente móvil. Asegúrese de que puede realizar todas las acciones comunes de cliente sin que el cliente móvil experimente cambios. Además, debe asegurarse de que los cambios realizados para llevar a cabo la migración (como cambiar el plan de tarifa) se reviertan en caso necesario.

### <a name="update-app-service-tier"></a>Selección de un plan de tarifa adecuado del Servicio de aplicaciones
Después de migrar al Servicio de aplicaciones de Azure, dispone de una mayor flexibilidad en los precios.

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Se abrirá la hoja Configuración de forma predeterminada; si no, haga clic en **Configuración**.
4. Haga clic en **Plan del servicio de aplicaciones** en el menú Configuración.
5. Haga clic en el icono **Plan de tarifa**.
6. Haga clic en el icono adecuado para sus requisitos y luego haga clic en **Seleccionar**. Puede que deba hacer clic en **Ver todo** para ver los planes de tarifa disponibles.

Como punto de partida, se recomienda lo siguiente:

| Plan de tarifa del servicio móvil | Plan de tarifa del Servicio de aplicaciones |
|:--- |:--- |
| Gratuito |F1 Gratis |
| Básica |Básico B1 |
| Standard |S1 Estándar |

Tenga en cuenta que hay una flexibilidad considerable en la elección del plan de tarifa adecuado para su aplicación. Consulte [Precios de Servicio de aplicaciones] para obtener detalles sobre los precios de su nuevo Servicio de aplicaciones.

> [!TIP]
> El nivel Estándar del Servicio de aplicaciones contiene acceso a muchas características que querrá usar, como [ranuras de ensayo], copias de seguridad automáticas y ajuste de escala automático. Examine las nuevas funciones mientras está ahí.
> 
> 

### <a name="review-migration-scheduler-jobs"></a>Revisión de los trabajos del Programador migrados
Los trabajos del Programador no estarán visibles hasta unos 30 minutos después de la migración. Los trabajos programados se seguirán ejecutando en segundo plano. Para ver los trabajos programados:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Examinar >**, escriba **Programación** en el cuadro *Filtro* y luego seleccione **Colecciones de Programador**.

Existe un número limitado de trabajos de Programador gratuitos que están disponibles después de la migración. Revise su uso y los [planes del Programador de Azure].

### <a name="configure-cors"></a>Configuración de CORS si es necesario
El uso compartido de recursos entre orígenes es una técnica que permite que un sitio web acceda a una API web en un dominio diferente. Si estaba usando Servicios móviles de Azure con un sitio web asociado, deberá configurar CORS como parte de la migración. Si estaba accediendo a Servicios móviles de Azure exclusivamente desde dispositivos móviles, no es necesario configurar CORS salvo en casos excepcionales.

La configuración de CORS migrada está disponible como la configuración de aplicación **MS\_CrossDomainWhitelist**. Para migrar el sitio a las instalaciones de CORS del Servicio de aplicaciones:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Se abrirá la hoja Configuración de forma predeterminada; si no, haga clic en **Configuración**.
4. Haga clic en **CORS** en el menú de API.
5. Especifique los orígenes permitidos en el cuadro que aparece y presione ENTRAR después de cada uno de ellos.
6. Cuando la lista de orígenes permitidos sea correcta, haga clic en el botón Guardar.

Esta es una tarea opcional, pero se proporciona para una mejor experiencia de administración a partir de ahora.

> [!TIP]
> Una de las ventajas de usar un Servicio de aplicaciones de Azure es que puede ejecutar su sitio web y el servicio móvil en el mismo sitio. Para más información, consulte la sección [Pasos siguientes](#next-steps).
> 
> 

### <a name="download-publish-profile"></a>Descarga de un nuevo perfil de publicación
El perfil de publicación del sitio cambia al migrar al Servicio de aplicaciones de Azure. Si va a publicar el sitio desde dentro de Visual Studio necesitará un nuevo perfil de publicación. Para descargar el nuevo perfil de publicación:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Haga clic en **Obtener perfil de publicación**.

El archivo PublishSettings se descargará en su PC. Normalmente se llamará *nombre\_del\_sitio*.PublishSettings. Después, puede importar la configuración de publicación en el proyecto existente:

1. Abra Visual Studio y el proyecto de Servicio móvil de Azure.
2. Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Publicar...**.
3. Haga clic en **Importar**.
4. Haga clic en **Examinar** y seleccione el archivo de configuración de publicación descargado. Haga clic en **Aceptar**.
5. Haga clic en **Validar conexión** para asegurarse que la configuración de publicación funciona.
6. Haga clic en **Publicar** para publicar el sitio.

## <a name="working-with-your-site"></a>Migración posterior al sitio
Empezará a trabajar con su nuevo Servicio de aplicaciones en la migración posterior del [Portal de Azure]. Las siguientes son algunas notas sobre operaciones específicas que se suelen realizar en el [Portal de Azure clásico], junto con su equivalente del Servicio de aplicaciones.

### <a name="publishing-your-site"></a>Descarga y publicación del sitio migrado
El sitio está disponible a través de git o ftp y se puede volver a publicar con varios mecanismos diferentes, como WebDeploy, TFS, Mercurial, GitHub y FTP. Las credenciales de implementación se migran con el resto del sitio. Si no estableció las credenciales de implementación o no las recuerda, puede restablecerlas:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Se abrirá la hoja Configuración de forma predeterminada; si no, haga clic en **Configuración**.
4. Haga clic en **Credenciales de implementación** en el menú PUBLICACIÓN.
5. Escriba las nuevas credenciales de implementación en los cuadros correspondientes y luego haga clic en el botón Guardar.

Puede usar estas credenciales para clonar el sitio con git o configurar implementaciones automatizadas desde GitHub, TFS o Mercurial. Para más información, consulte la [documentación de implementación del Servicio de aplicaciones de Azure].

### <a name="appsettings"></a>Configuración de aplicación
La mayoría de las configuraciones de un servicio móvil migrado están disponible a través de Configuración de aplicación. Puede obtener una lista de la configuración de aplicación desde el [Portal de Azure]. Para ver o cambiar la configuración de aplicación:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Se abrirá la hoja Configuración de forma predeterminada; si no, haga clic en **Configuración**.
4. Haga clic en **Configuración de aplicación** en el menú GENERAL.
5. Desplácese hasta la sección Configuración de aplicación y busque su configuración de aplicación.
6. Haga clic en el valor de la configuración de aplicación para editar el valor. Haga clic en **Guardar** para guardar el valor.

Puede actualizar varias configuraciones de aplicación al mismo tiempo.

> [!TIP]
> Observará que hay dos configuraciones de aplicación con el mismo valor. Por ejemplo, verá *ApplicationKey* y *MS\_ApplicationKey*. Solo necesitará modificar la configuración con el prefijo **MS\_**. Sin embargo, es una buena idea actualizar ambas configuraciones de aplicación al mismo tiempo.
> 
> 

### <a name="authentication"></a>Autenticación
Todas las configuraciones de autenticación están disponibles como configuración de aplicación en su sitio migrado. Para actualizar la configuración de autenticación, debe modificar la configuración de aplicación adecuada. En la siguiente tabla se muestra la configuración de aplicación adecuada para el proveedor de autenticación:

| Proveedor | Id. de cliente | Secreto del cliente | Otras configuraciones |
|:--- |:--- |:--- |:--- |
| Cuenta Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Nota: **MS\_AadTenants** se almacena como una lista de dominios de inquilino separados por coma (los campos "Inquilinos permitidos" del Portal de Servicios móviles).

> [!WARNING]
> **No utilice los mecanismos de autenticación del menú Configuración.**
> 
> El Servicio de aplicaciones de Azure proporciona un sistema de autenticación y autorización "sin código" independiente en el menú de configuración *Autenticación y autorización* y la opción (en desuso) *Autenticación móvil* en el menú Configuración. Estas opciones no son compatibles con un servicio móvil de Azure migrado. Puede [actualizar su sitio](app-service-mobile-net-upgrading-from-mobile-services.md) para aprovechar la autenticación del Servicio de aplicaciones de Azure.
> 
> 

### <a name="easytables"></a>Datos
La pestaña *Datos* de Servicios móviles se ha reemplazado por *Tablas fáciles* dentro del Portal de Azure. Para tener acceso a Tablas fáciles:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Se abrirá la hoja Configuración de forma predeterminada; si no, haga clic en **Configuración**.
4. Haga clic en **Tablas fáciles** en el menú MÓVIL.

Para agregar una nueva tabla, haga clic en el botón **Agregar** o acceda a sus tablas existentes haciendo clic en el nombre de una tabla. Hay una serie de operaciones que puede realizar desde esta hoja, como por ejemplo:

* Cambiar los permisos de tabla
* Editar los scripts operativos
* Administrar el esquema de tabla
* Eliminar la tabla
* Borrar el contenido de la tabla
* Eliminar filas específicas de la tabla

### <a name="easyapis"></a>API
La pestaña *API* de Servicios móviles se ha reemplazado por *API fáciles* dentro del Portal de Azure. Para obtener acceso a las API fáciles:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Se abrirá la hoja Configuración de forma predeterminada; si no, haga clic en **Configuración**.
4. Haga clic en **API fáciles** en el menú MÓVIL.

Las API migradas ya aparecen en la hoja. También puede agregar una nueva API desde esta hoja. Para administrar una API específica, haga clic en la API. Desde la nueva hoja, puede ajustar los permisos y editar los scripts de la API.

### <a name="on-demand-jobs"></a>Trabajos del Programador
Todos los trabajos de Programador están disponibles a través de la sección de colecciones de trabajo de Programador. Para acceder a los trabajos de Programador:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Examinar >**, escriba **Programación** en el cuadro *Filtro* y luego seleccione **Colecciones de Programador**.
3. Seleccione la colección de trabajos para su sitio. Se denominará *nombre\_del\_sitio*-Jobs.
4. Haga clic en **Configuración**.
5. Haga clic en **Trabajos del Programador** en ADMINISTRAR.

Los trabajos programados se mostrarán con la frecuencia que especifique antes de la migración. Los trabajos a petición se deshabilitarán. Para ejecutar un trabajo a petición:

1. Seleccione el trabajo que desee ejecutar.
2. Si es necesario, haga clic en **Habilitar** para habilitar el trabajo.
3. Haga clic en **Configuración** y después en **Programar**.
4. Seleccione **Una vez** como periodicidad y haga clic en **Guardar**.

Los trabajos a petición se encuentran en `App_Data/config/scripts/scheduler post-migration`. Se recomienda convertir todos los trabajos a petición a [WebJobs]. Debe escribir los nuevos trabajos del Programador como [WebJobs].

### <a name="notification-hubs"></a>Centros de notificaciones
Los Servicios móviles usan Centros de notificaciones para las notificaciones push. Las siguientes configuraciones de aplicación se usan para vincular el centro de notificaciones al servicio móvil tras la migración:

| Configuración de aplicación | Descripción |
|:--- |:--- |
| **MS\_PushEntityNamespace** |El espacio de nombres del centro de notificaciones. |
| **MS\_NotificationHubName** |El nombre del centro de notificaciones. |
| **MS\_NotificationHubConnectionString** |La cadena de conexión del centro de notificaciones |
| **MS\_NamespaceName** |Un alias para MS\_PushEntityNamespace |

El centro de notificaciones se administrará mediante el [Portal de Azure]. Anote el nombre del centro de notificaciones (puede encontrarlo mediante la configuración de aplicación):

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Examinar**> y luego **Centros de notificaciones**.
3. Haga clic en el nombre del centro de notificaciones asociado al servicio móvil.

> [!NOTE]
> El centro de notificaciones no será visible si es de tipo "Mixto". Los centros de notificaciones mixtos usan características de Centros de notificaciones y características heredadas de Bus de servicio. Deberá [convertir los espacios de nombres mixtos]. Una vez finalizada la conversión, el centro de notificaciones aparecerá en el [Portal de Azure].
> 
> 

Para más información, revise la documentación de [Centros de notificaciones].

> [!TIP]
> Las características de administración de Centros de notificaciones en el [Portal de Azure] se encuentran aún en versión preliminar. El [Portal de Azure clásico] sigue estando disponible para administrar todos los centros de notificaciones.
> 
> 

### <a name="legacy-push"></a>Configuración de inserción heredada
Si configuró la inserción en el servicio móvil antes de la introducción en los Centros de notificaciones, está usando *inserción heredada*. Si utiliza la inserción y no ve un Centro de notificaciones en la configuración, es probable que esté utilizando *inserción heredada*. Esta característica se migrará con las demás características y sigue estando disponible. Pero se recomienda que actualice a los Centros de notificaciones poco después de completar la migración.

Mientras tanto, toda la configuración de inserción heredada (con la excepción notable de los certificados de APN) está disponible en Configuración de aplicación. El certificado de APN puede sustituirse por reemplazar el archivo adecuado en el sitio. Esto puede hacerse a través de cualquiera de las opciones de implementación disponibles para el Servicio de aplicaciones de Azure.

### <a name="app-settings"></a>Otra configuración de aplicación
La siguiente configuración de aplicación adicional se migra desde el servicio móvil y está disponible en *Configuración* > *Configuración de aplicación*:

| Configuración de aplicación | Descripción |
|:--- |:--- |
| **MS\_MobileServiceName** |El nombre de la aplicación. |
| **MS\_MobileServiceDomainSuffix** |El prefijo de dominio, es decir, azure-mobile.net. |
| **MS\_ApplicationKey** |La clave de aplicación. |
| **MS\_MasterKey** |La clave maestra de aplicación. |

La clave de aplicación y la clave maestra deben ser idénticas a las claves de aplicación del servicio móvil original. En concreto, los clientes móviles envían la clave de aplicación para validar su uso de la API móvil.

### <a name="cliequivalents"></a>Equivalentes de línea de comandos
Ya no podrá usar el comando *azure mobile* para administrar el sitio de Servicios móviles de Azure. Muchas de las funciones se han reemplazado por el comando *azure site*. Utilice la siguiente tabla para buscar equivalentes para los comandos más comunes:

| Comando *Azure Mobile* | Comando *Azure Site* equivalente |
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

Para actualizar la configuración de autenticación o de notificaciones push, actualice la configuración de aplicación adecuada. Edite los archivos y publique su sitio mediante ftp o git.

### <a name="diagnostics"></a>Diagnósticos y registro
El registro de diagnóstico está normalmente deshabilitado en un Servicio de aplicaciones de Azure. Para habilitar el registro de diagnóstico:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Se abrirá la hoja Configuración de forma predeterminada; si no, haga clic en **Configuración**.
4. Seleccione **Registros de diagnóstico** en el menú de CARACTERÍSTICAS.
5. Haga clic en **ACTIVAR** en los registros siguientes: **Registro de la aplicación (Filesystem)**, **Mensajes de error detallados** y **Seguimiento de solicitudes con error**.
6. Haga clic en **Sistema de archivos** en el registro de servidor web.
7. Haga clic en **Guardar**.

Para ver los registros:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y luego haga clic en el nombre del servicio móvil migrado.
3. Haga clic en el botón **Herramientas**.
4. Seleccione **Secuencia de registro** en el menú de RESPETAR.

Los registros se transmitirán a la ventana proporcionada cuando se generen. También puede descargar los registros para analizarlos posteriormente mediante sus credenciales de implementación. Consulte la documentación de [Registro] para más información.

## <a name="known-issues"></a>Problemas conocidos
### La eliminación de un clon de aplicaciones móviles migradas provocará una interrupción del sitio
Si clona el servicio móvil migrado con Azure PowerShell y después elimina el clon, se quitará la entrada DNS del servicio de producción. Como consecuencia de ello, el sitio dejará de estar accesible desde Internet.

Resolución: se está trabajando para corregir este problema. Si quiere clonar su sitio, hágalo a través del portal.

### Los cambios en web.config no funcionan
Si tiene un sitio ASP.NET, los cambios en el archivo `Web.config` no funcionarán. El Servicio de aplicaciones de Azure crea un archivo `Web.config` adecuado durante el inicio para admitir el tiempo de ejecución de Servicios móviles. Puede reemplazar determinadas configuraciones (como los encabezados personalizados) mediante un archivo de transformación XML. Cree un archivo llamado `applicationHost.xdt`: este archivo debe terminar en el directorio `D:\home\site` en el Servicio de Azure. Esto se consigue a través de un script de implementación personalizado o directamente mediante Kudu. A continuación se muestra un documento de ejemplo:

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

Para más información, consulte la documentación [XDT Transform Samples] \(Ejemplos de transformación de XDT) en GitHub.

### Los Servicios móviles migrados no se pueden agregar al Administrador de tráfico
Cuando se crea un perfil del Administrador de tráfico, no se puede elegir directamente un servicio móvil migrado al perfil. Se debe usar un "punto de conexión externo". El punto de conexión externo solo puede agregarse a través de PowerShell. Consulte el [tutorial del Administrador de tráfico](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) para más información.

## <a name="next-steps"></a>Pasos siguientes
Tenga en cuenta que como la aplicación se migra al Servicio de aplicaciones, hay incluso más características que puede aprovechar:

* Las [ranuras de ensayo] de implementación le permiten ensayar cambios en el sitio y ejecutar pruebas A/B.
* [WebJobs] ofrece una sustitución para trabajos programados a petición.
* Puede [implementar su sitio de forma continuada] si lo vincula a GitHub, TFS o Mercurial.
* Puede usar [Application Insights] para supervisar el sitio.
* Proporcionar servicio a un sitio web y a una API móvil con el mismo código.

### <a name="upgrading-your-site"></a>Actualización del sitio de Servicios móviles al SDK de Aplicaciones móviles
* Para los proyectos de servidor basados en Node.js, el nuevo [SDK para Node.js de Aplicaciones móviles] proporciona varias características nuevas. Por ejemplo, ahora puede desarrollar y depurar localmente, usar cualquier versión de Node.js posterior a la 0.10 y personalizar con cualquier middleware de Express.js.
* Para proyectos de servidor basados en .NET, los nuevos [paquetes NuGet del SDK de Aplicaciones móviles](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) tienen más flexibilidad en las dependencias de NuGet, son compatibles con las nuevas características de autenticación del Servicio de aplicaciones y se componen con cualquier proyecto de ASP.NET, incluido MVC. Para más información sobre la actualización, consulte [Actualización del servicio móvil de .NET existente a Servicio de aplicaciones](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Precios de Servicio de aplicaciones]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Escalado automático]: ../app-service-web/web-sites-scale.md
[Servicio de aplicaciones de Azure]: ../app-service/app-service-value-prop-what-is.md
[documentación de implementación del Servicio de aplicaciones de Azure]: ../app-service-web/web-sites-deploy.md
[Portal de Azure clásico]: https://manage.windowsazure.com
[Portal de Azure]: https://portal.azure.com
[Región de Azure]: https://azure.microsoft.com/regions/
[planes del Programador de Azure]: ../scheduler/scheduler-plans-billing.md
[implementar su sitio de forma continuada]: ../app-service-web/app-service-continuous-deployment.md
[convertir los espacios de nombres mixtos]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nombres de dominio personalizados]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidad general del Servicio de aplicaciones de Azure]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[conexiones híbridas]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Registro]: ../app-service-web/web-sites-enable-diagnostic-log.md
[SDK para Node.js de Aplicaciones móviles]: https://github.com/azure/azure-mobile-apps-node
[Servicios móviles frente al Servicio de aplicaciones]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Centros de notificaciones]: ../notification-hubs/notification-hubs-push-notification-overview.md
[supervisión del rendimiento]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Hacer una copia de seguridad de los scripts y la base de datos SQL de su servicio móvil]: ../mobile-services/mobile-services-disaster-recovery.md
[ranuras de ensayo]: ../app-service-web/web-sites-staged-publishing.md
[red virtual]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[XDT Transform Samples]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples

<!---HONumber=AcomDC_0907_2016-->