<properties pageTitle="Cuotas y limitaciones de Servicios multimedia" description="En este tema se describen las cuotas y limitaciones relacionadas con los Servicios multimedia de Microsoft Azure." services="media-services" documentationCenter="" authors="Juliako" manager="dwrede" />

<tags ms.service="media-services" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="media" ms.date="01/23/2015" ms.author="juliako" />

# Cuotas y limitaciones

En este tema se describen las cuotas y limitaciones relacionadas con los Servicios multimedia de Microsoft Azure.

## Cuotas y limitaciones

- El número máximo de recursos permitidos en su cuenta de Servicios multimedia: 1.000.000. 

- El número máximo de tareas encadenadas por trabajo: 30.

- El número máximo de recursos permitidos en una tarea: 50 recursos por tarea.
 
- El número máximo de recursos por trabajo: 100.
 
- Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados.
 
- El número máximo de trabajos de su cuenta no debe superar los 50.000.
 
- Puede eliminar los trabajos antiguos con **IJob.Delete** o con la solicitud HTTP **DELETE**. Para obtener más información, consulte [Límite de registro de trabajos del Codificador multimedia de Azure Media ](http://blogs.msdn.com/b/randomnumber/archive/2014/05/05/job-record-limit-for-windows-azure-media-encoder.aspx) y [Administración de recursos](https://msdn.microsoft.com/es-es/library/azure/dn642436.aspx). 
 
- Al realizar una solicitud para obtener una lista de las entidades Job, se devolverá un máximo de 1.000 por solicitud. Si necesita realizar un seguimiento de todos los trabajos enviados, puede usar top y skip, tal como se describe en [Opciones de consulta del sistema de OData](http://msdn.microsoft.com/library/gg309461.aspx).  


- No puede tener más de cinco localizadores únicos asociados a un recurso determinado a la vez. 
	
	**Nota**
	Los localizadores no están diseñados para administrar el control de acceso por el usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM).

- No puede tener más de 25 cuentas de Servicios multimedia en una sola suscripción.

- El mecanismo de limitación de Servicios multimedia restringe el uso de recursos en las aplicaciones que realizan un número excesivo de solicitudes al servicio. El servicio puede devolver el código de estado HTTP de servicio no disponible (503). Para obtener más información, consulte la descripción del error 503 en el tema [Códigos de error de los Servicios multimedia de Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx).

- De forma predeterminada, puede agregar hasta 5 canales activos a su cuenta de Servicios multimedia. 

	También puede iniciar hasta 5 canales al mismo tiempo. Para solicitar un límite superior, vea la sección *How to request a higher limit for updatable quotas* siguiente.

- De forma predeterminada, puede agregar hasta 50 programas (en estado detenido) a un solo canal. 

	Solo puede tener hasta tres programas en estado de ejecución al mismo tiempo. Para solicitar un límite superior, vea la sección *How to request a higher limit for updatable quotas* siguiente.

- De forma predeterminada, cada cuenta de Servicios multimedia puede tener hasta dos extremos de streaming (orígenes). 

	También puede tener hasta dos extremos de streaming en estado de ejecución al mismo tiempo.

	Puede escalar cada extremo de streaming hasta 10 unidades de streaming. Para solicitar un límite superior, vea la sección *How to request a higher limit for updatable quotas* siguiente.


- De forma predeterminada, cada cuenta de Servicios multimedia puede escalar hasta 25 unidades de codificación. Para obtener más información, consulte Escalación de Servicios multimedia. Para solicitar un límite superior, consulte Cómo solicitar un límite superior para las cuotas actualizables.
	
	**Importante**
	No cree más cuentas de Servicios multimedia para aumentar los límites; en su lugar, envíe una incidencia de soporte técnico.

## Cómo solicitar un límite superior para las cuotas actualizables

### Cuotas actualizables

Puede solicitar la actualización de los límites de las cuotas siguientes abriendo una incidencia de soporte técnico.
- Unidades de codificación

- Canales activos (en estado detenido y en ejecución)
 
- Extremos de streaming (en estado detenido y en ejecución)
 
- Unidades de streaming

### Abrir una incidencia de soporte técnico

Para abrir una incidencia de soporte técnico, haga lo siguiente:

1. Haga clic en [Obtener soporte técnico](https://manage.windowsazure.com/?getsupport=true). Si no ha iniciado sesión, se le solicitará que especifique sus credenciales.

1. Seleccione su suscripción.
 
1. En el tipo de soporte, seleccione "Técnico".
 
1. Haga clic en "Crear incidencia". 
 
1. Seleccione "Servicios multimedia de Microsoft Azure" en la lista de productos que se muestran en la página siguiente.
 
1. Seleccione un "Tipo de problema" que sea pertinente en relación con su problema.
 
1. Haga clic en Continue.
 
1. Siga las instrucciones de la página siguiente y, a continuación, especifique los detalles sobre cuántas unidades reservadas de codificación o de streaming necesita. 
 
1. Haga clic en Submit para abrir la incidencia.
 
<!--HONumber=45--> 
