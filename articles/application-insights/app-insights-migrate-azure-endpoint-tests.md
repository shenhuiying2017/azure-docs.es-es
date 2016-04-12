<properties 
	pageTitle="Migración de la supervisión de punto de conexión de Azure a las pruebas de disponibilidad de Application Insights" 
	description="Hemos transformado las pruebas de supervisión de puntos de conexión de Azure en las nuevas pruebas de disponibilidad de Application Insights. El cambio se realizará el 4 de abril de 2016."
	services="application-insights" 
    documentationCenter=""
	authors="soubhagyadash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2016" 
	ms.author="awills"/>
 
# Sustitución de la supervisión de punto de conexión de Azure por las pruebas de disponibilidad de Application Insights

¿Utiliza [la supervisión de punto de conexión](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/) para las aplicaciones web de Azure? El 4 de abril de 2016, lo reemplazaremos por las nuevas y más eficaces [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md). Ya hemos creado las nuevas pruebas, aunque permanecerán deshabilitadas hasta el 4 de abril.

Puede editar las nuevas pruebas y hacer el cambio si así lo desea. Las encontrará en el [Portal de Azure](https://portal.azure.com) en el grupo de recursos Default-ApplicationInsights-CentralUS.


## ¿Qué son las pruebas de disponibilidad?

Las pruebas de disponibilidad son una característica de Azure que comprueba continuamente que todos los servicios o sitios web estén activos y en ejecución enviándole solicitudes HTTP (pruebas web de Visual Studio o de ping único) desde un máximo de 16 ubicaciones en todo el mundo.

En el [Portal de Azure clásico](https://manage.windowsazure.com), estas pruebas se denominaban supervisión de puntos de conexión. Eran de un ámbito más limitado. Las nuevas pruebas de disponibilidad ofrecen una mejora sustancial:

* Hasta 10 pruebas web de Visual Studio o pruebas de ping por cada recurso de Application Insights. 
* Hasta 16 ubicaciones en todo el mundo pueden enviar solicitudes de prueba a la aplicación web. Mayor control de los criterios de éxito de la prueba. 
* Comprueba todos los sitios o servicios web: no solo las aplicaciones web de Azure.
* Reintentos de pruebas: para reducir alertas de falsos positivos debido a problemas de red transitorios. 
* Webhooks puede recibir notificaciones de HTTP POST para las alertas.

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

Obtenga más información acerca de las [pruebas de disponibilidad aquí](app-insights-monitor-web-app-availability.md).

Las pruebas de disponibilidad forman parte de [Application Insights de Visual Studio](app-insights-overview.md), que es un servicio de análisis extensible para cualquier aplicación web.



## ¿Qué va a pasar con mis pruebas de puntos de conexión?

* Hemos copiado las pruebas de supervisión de puntos de conexión en las nuevas pruebas de disponibilidad de Application Insights. Se copiaron el 4 de marzo de 2016, por lo que no se habrán copiado las pruebas de puntos de conexión que haya creado desde entonces.
* Las nuevas pruebas de disponibilidad están deshabilitadas actualmente y las pruebas de puntos de conexión antiguas aún están en funcionamiento.
* Las reglas de alertas *no* se han migrado. Las nuevas pruebas se han configurado inicialmente con una regla predeterminada:
 * Se desencadena cuando más de una ubicación informa de errores en 5 minutos.
 * Envía un correo electrónico a los administradores de la suscripción.

En el [Portal de Azure](https://portal.azure.com) encontrará las pruebas migradas en el grupo de recursos "Default-ApplicationInsights-CentralUS". Los nombres de las pruebas tienen el prefijo "Migrated-".

## ¿Qué tengo que hacer?

* Si creó las pruebas heredadas después del 4 de marzo de 2016 (o por algún motivo no se migraron las pruebas), las nuevas pruebas de disponibilidad son [fáciles de configurar](app-insights-monitor-web-app-availability.md).

### Opción A: no hacer nada. Nosotros nos encargaremos.

**El 4 de abril,** haremos lo siguiente:

* Deshabilitar las antiguas pruebas de puntos de conexión.
* Habilitar las pruebas de disponibilidad migradas.

### Opción B: usted se encarga de administrar y habilitar las nuevas pruebas.

* Revise y edite las nuevas pruebas de disponibilidad en el nuevo [Portal de Azure](https://portal.azure.com). 
 * Revise los criterios de desencadenamiento
 * Revise los destinatarios de correo electrónico
* Habilite las nuevas pruebas
* Elimine las antiguas pruebas de puntos de conexión [en el portal clásico](https://manage.windowsazure.com). Se recomienda esta opción para evitar alertas duplicadas y para reducir la carga de tráfico de prueba en su sitio web. Si no lo hace, nosotros las eliminaremos el 4 de abril de 2016.


### Opción C: no participar.

Si no desea utilizar las pruebas de disponibilidad, puede eliminarlas en el [Portal de Azure](https://portal.azure.com). También hay un vínculo de cancelación de suscripción en la parte inferior de los correos electrónicos de notificación.

Aún así se eliminarán las antiguas pruebas de puntos de conexión el 4 de abril.

## ¿Cómo se pueden editar las nuevas pruebas?

Inicie sesión en el [Portal de Azure](https://portal.azure.com) y busque las pruebas web con el prefijo 'Migrated-':

![Elegir grupos de recursos, Default-ApplicationInsights-CentralUS y abrir las pruebas 'migradas'.](./media/app-insights-migrate-azure-endpoint-tests/20.png)

Edite y/o habilite la prueba:

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)


## ¿Por qué ocurre esto?

Un mejor servicio. El servicio de puntos de conexión anterior era mucho más reducido. Puede proporcionar solo dos direcciones URL para las pruebas de ping único desde 3 ubicaciones geográficas en una aplicación web o máquina virtual de Azure. Las nuevas pruebas pueden ejecutar pruebas web de varios pasos desde un máximo de 16 ubicaciones y se pueden especificar hasta 10 pruebas para una aplicación. Puede probar cualquier dirección URL: no tiene que ser un sitio de Azure.

Las nuevas pruebas se configuran independientemente de la aplicación web o máquina virtual que se esté probando.

Vamos a migrar las pruebas para garantizar que sigue teniendo el control sobre ellas mientras se utiliza el nuevo portal.

## ¿Qué es Application Insights?

Las nuevas pruebas de disponibilidad forman parte de [Application Insights de Visual Studio](app-insights-overview.md). Aquí puede ver un [vídeo de 2 minutos](http://go.microsoft.com/fwlink/?LinkID=733921).

## ¿Voy a pagar las nuevas pruebas?

Las pruebas migradas se configuran en un recurso de Application Insights en el plan gratis predeterminado. Esto permite la recopilación de hasta 5 millones de puntos de datos. Esto cubre fácilmente el volumen de datos que utilizarán las pruebas.

Por supuesto, si le gusta Application Insights y crea más pruebas de disponibilidad o adopta algunas más de sus características de diagnóstico y de supervisión de rendimiento, generará más puntos de datos. Aún así, el resultado solo sería que podría alcanzar la cuota máxima del plan gratis. No recibirá una factura a menos que decida participar en los planes Estándar o Premium.

[Más información sobre los precios de Application Insights y la supervisión de la cuota](app-insights-pricing.md).

## ¿Qué se migra y qué no?

Se conserva de las antiguas pruebas de puntos de conexión:

* La dirección URL del punto de conexión que se va a probar.
* Las ubicaciones geográficas desde donde se envían las solicitudes.
* La frecuencia de prueba sigue siendo 5 minutos.
* El tiempo de espera de prueba sigue siendo 30 segundos. 

No se migran:

* La regla de desencadenamiento de alerta. La regla que hemos configurado se desencadena cuando una ubicación informa de errores en 5 minutos.
* Los destinatarios de la alerta. Se enviarán correos electrónicos de notificación a los propietarios y copropietarios de la suscripción. 

## ¿Cómo encuentro las nuevas pruebas?

Ahora puede editar cualquiera de las nuevas pruebas si lo desea. Inicie sesión en el [Portal de Azure](https://portal.azure.com), abra **Grupos de recursos** y seleccione **Default-ApplicationInsights-CentralUS**. En ese grupo, encontrará las nuevas pruebas web. [Más información sobre las nuevas pruebas de disponibilidad](app-insights-monitor-web-app-availability.md).

Tenga en cuenta que los nuevos correos electrónicos de alerta se enviarán desde esta dirección: alertas de App Insights (ai-noreply@microsoft.com)

## ¿Qué sucederá si no hago nada?

Se aplicará la opción A. Habilitaremos las pruebas migradas y configuraremos las reglas de alerta predeterminadas como se mencionó anteriormente. Tendrá que agregar las reglas de alerta y los destinatarios personalizados como se mencionó anteriormente. Se deshabilitarán las pruebas heredadas de supervisión de puntos de conexión.

## ¿Dónde puedo proporcionar comentarios sobre las pruebas? 

Agradecemos sus comentarios. Por favor, [envíenos un correo electrónico](mailto:vsai@microsoft.com).

<!---HONumber=AcomDC_0316_2016-->