<properties 
	pageTitle="Administración de aplicaciones web mediante el Portal de Azure" 
	description="Información general de las tareas de administración de aplicaciones web en Microsoft Azure mediante el Portal de Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Administración de aplicaciones web mediante el Portal de Azure

En el [Portal de Azure](https://portal.azure.com), puede administrar las aplicaciones web a través de una serie de *hojas*, que son contenedores para los distintos componentes. (Para obtener información general del Portal de Azure, consulte la guía de cambios del [Portal](http://go.microsoft.com/fwlink/?LinkId=529715)).

Para ver la hoja de la aplicación web, haga clic en **Inicio** y, a continuación, en el nombre de la aplicación o haga clic en **Examinar** para ver todos los recursos de Azure.

![](./media/web-sites-manage/manage01.png)

La parte superior de la hoja tiene controles para algunas acciones comunes:

- **Configuración** para ver una lista de todos los ajustes de administración.

- **Examinar** para abrir la aplicación web en la ventana del explorador.

- Otros botones inician o detienen la aplicación, intercambian ranuras de implementación, eliminan la aplicación y así sucesivamente.

**Aspectos básicos** muestra información básica acerca de la aplicación, como la dirección URL, la ubicación, el plan del [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) y el estado actual (en ejecución, detenido, etc.).

Debajo de **Aspectos básicos** se encuentran las distintas partes que se pueden utilizar para supervisar, administrar e implementar la aplicación. Para personalizar el diseño de la hoja, haga clic con el botón secundario y seleccione **Personalizar** o **Agregar elementos**. El resto de este tema describe los elementos del diseño predeterminado.

## Supervisión

Debajo de **Supervisión** hay un gráfico que muestra las métricas para la aplicación web. Para configurar el gráfico, haga clic en **Editar**. Puede seleccionar el intervalo de tiempo y las métricas que desea mostrar. Las métricas disponibles incluyen el número de solicitudes, el tiempo medio de respuesta, los errores de servidor y el tiempo de CPU.

![](./media/web-sites-manage/manage02.png)

Para agregar una regla de alerta, haga clic en el gráfico y después haga clic en **Agregar alerta**. Una regla de alerta le avisa cuando una métrica determinada alcanza un determinado umbral. Por ejemplo, puede recibir alertas cuando el número de errores de servidor supera un valor durante un período de 5 minutos.

**Supervisión** también incluye elementos para configurar el análisis, configurar la supervisión de aplicaciones y crear pruebas de disponibilidad. Para obtener más información, [Conceptos básicos de supervisión para Aplicaciones web en Servicio de aplicaciones de Azure](web-sites-monitor.md).

## Uso

![](./media/web-sites-manage/manage03.png)

- **Almacenamiento del sistema de archivos** muestra cuánto almacenamiento de archivos está usando la aplicación.
- **Cuotas** muestra cómo la aplicación utiliza su cuota de uso de recursos. Haga clic en este elemento para ver los detalles.
- **Escala** le permite escalar el recuento de instancias y configurar la autoescala. Consulte [Escalación de una aplicación web en Servicio de aplicaciones de Azure](how-to-scale-websites.md).  
- **Gasto estimado** muestra una estimación del costo de la aplicación.
- **Nivel de precios** permite cambiar el nivel de precios.

## Operaciones

![](./media/web-sites-manage/manage04.png)

- **Eventos**. Haga clic para ver los registros de eventos.  
- **Reglas de alertas**. Haga clic para ver las reglas de alertas y agregar nuevas alertas.
- **Registros de streaming** (A). Haga clic para ver los registros de aplicaciones. Para habilitar los registros, vaya a **Configuración** y abra la hoja **Registros de diagnóstico**. 
- **Consola**. Haga clic para abrir una línea de comandos que se ejecuta dentro del portal. Puede utilizarla para ejecutar comandos como `mkdir` y `dir`.  
- **Explorador de procesos** (B). Haga clic para ver los procesos en ejecución en la aplicación, incluido el espacio de trabajo y el número de subprocesos.

## Implementación
 
![](./media/web-sites-manage/manage05.png)

- Configure la implementación continua. Consulte [Uso de Git para implementar Aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-publish-source-control.md).
- Ranuras de implementación. Consulte [Implementación en entornos de ensayo para las Aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-staged-publishing.md).
- Establezca las credenciales de implementación para Git o FTP. 

## Redes

- Conexión de la aplicación a una red virtual
- Incorporación de conexiones híbridas

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Pasos siguientes

- [Escalación de una aplicación web en el Servicio de aplicaciones de Azure](how-to-scale-websites.md)
- [Ejecución de tareas en segundo plano con WebJobs](web-sites-create-web-jobs.md)
- [Copias de seguridad y restauración](web-sites-backup.md) de [Aplicaciones web de Azure](web-sites-restore.md)
- [Aspectos básicos de supervisión para Aplicaciones web en Servicio de aplicaciones de Azure](web-sites-monitor.md)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=54-->