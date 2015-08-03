<properties 
	pageTitle="Operaciones de Azure AD Connect Health." 
	description="Página de Azure AD Connect Health que describe las operaciones adicionales que se pueden realizar una vez implementado Azure AD Connect Health." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>

# Operaciones de Azure AD Connect Health

El tema siguiente describe las distintas operaciones que se pueden realizar con Azure AD Connect Health.

## Notificaciones de correo electrónico
Puede configurar el Servicio de Azure AD Connect Health para enviar notificaciones por correo electrónico cuando se generen alertas que indiquen que el mantenimiento de la infraestructura del Servicio de federación no es correcto. Esto ocurrirá cuando se genere una alerta y también cuando se marque como resuelta. Siga las instrucciones que aparecen a continuación para configurar las notificaciones de correo electrónico. Tenga en cuenta que las notificaciones de correo electrónico están deshabilitadas de manera predeterminada.


### Para habilitar las notificaciones de correo electrónico de Azure AD Connect Health

1. Abra la hoja Alertas de la granja para la que desea recibir una notificación de correo electrónico.
1. Haga clic en el botón "Configuración de notificaciones" de la barra de acciones.
1. Ponga el modificador de la opción Notificación de correo electrónico en la posición ON (Activado).
1. Active la casilla para configurar que todos los administradores de inquilinos globales reciban notificaciones de correo electrónico.
1. Si desea recibir notificaciones de correo electrónico en otras direcciones de correo electrónico, puede especificarlas en los cuadros Destinatarios de correo electrónico adicionales. Para quitar una dirección de correo electrónico de esta lista, haga clic con el botón derecho en la entrada y seleccione Eliminar.
1. Para finalizar los cambios, haga clic en "Guardar". Los cambios entrarán en vigor solo después de seleccionar "Guardar".






## Eliminación de un servidor del Servicio de Azure AD Connect Health

En algunos casos, es posible que desee dejar de supervisar un servidor. Siga las instrucciones siguientes para quitar un servidor del Servicio de Azure AD Connect Health.

Cuando elimine un servidor, tenga en cuenta lo siguiente:

- Esta acción DETENDRÁ cualquier recopilación de datos de ese servidor. Este servidor se quitará del servicio de supervisión. Después de esta acción, no podrá ver nuevas alertas ni datos de análisis de uso o supervisión de este servidor.
- Esta acción NO desinstalará ni quitará el agente de mantenimiento del servidor. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en el servidor relacionados con el agente de mantenimiento.
- Esta acción NO eliminará los datos que ya se recopilaron desde este servidor. Los datos se eliminarán según lo estipulado en la Directiva de retención de datos de Microsoft Azure. 
- Después de realizar esta acción, si desea comenzar a supervisar nuevamente el mismo servidor, deberá desinstalar el agente de mantenimiento en este servidor y volver a instalarlo. 


	### Para eliminar un servidor del Servicio de Azure AD Connect Health
<ol>
1. Seleccione el nombre del servidor que se va a quitar para abrir la hoja Servidor en la hoja Lista de servidores. 
1. En la hoja Servidor, haga clic en el botón "Eliminar" de la barra de acciones.
1. Confirme la acción para eliminar el servidor; para ello, escriba el nombre del servidor en el cuadro de confirmación.
1. Haga clic en el botón "Eliminar".







## Eliminación de una instancia de servicio del Servicio de Azure AD Connect Health

En algunos casos, es posible que desee quitar una instancia de servicio. Siga las instrucciones siguientes para quitar una instancia de servicio del Servicio de Azure AD Connect Health.

Cuando elimine una instancia de servicio, tenga en cuenta lo siguiente:

- Esta acción quitará la instancia de servicio actual del servicio de supervisión. 
- Esta acción NO desinstalará ni quitará el agente de mantenimiento de ninguno de los servidores que se supervisaron como parte de esta instancia de servicio. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en los servidores relacionados con el agente de mantenimiento. 
- Todos los datos de esta instancia de servicio se eliminarán según lo estipulado en la Directiva de retención de datos de Microsoft Azure. 
- Después de realizar esta acción, si desea comenzar a supervisar el servicio, desinstale el agente de mantenimiento en todos los servidores que se supervisarán y vuelva a instalarlo. Después de realizar esta acción, si desea comenzar a supervisar nuevamente el mismo servidor, deberá desinstalar el agente de mantenimiento en este servidor y volver a instalarlo.







	### Para eliminar una instancia de servicio del Servicio de Azure AD Connect Health
<ol>
1. Seleccione el identificador del servicio (nombre de la granja) que desea quitar para abrir la Hoja Servicio en la hoja Lista de Servicios. 
1. En la hoja Servidor, haga clic en el botón "Eliminar" de la barra de acciones.
1. Confirme el nombre del servicio; para ello, escríbalo en el cuadro de confirmación (por ejemplo: sts.contoso.com). 
1. Haga clic en el botón "Eliminar".

<!---HONumber=July15_HO4-->