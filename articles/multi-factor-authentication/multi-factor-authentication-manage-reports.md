<properties 
	pageTitle="Informes de Azure Multi-Factor Authentication" 
	description="Aquí se describe cómo utilizar la característica de informes de Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2015" 
	ms.author="billmath"/>

# Informes en Azure Multi-Factor Authentication

Azure Multi-Factor Authentication proporciona varios tipos de informes que usted o su organización pueden usar. Puede tener acceso a estos informes a través del Portal de administración de Multi-Factor Authentication. La siguiente es una lista de los informes disponibles.

Acceda a los informes a través del Portal de administración de Azure

Nombre| Descripción
:------------- | :------------- | 
Uso | Los informes de uso muestran información sobre el uso general, resúmenes y detalles de usuario.
Estado del servidor|Este informe muestra el estado de los Servidores Multi-Factor Authentication asociados a su cuenta.
Historial de usuarios bloqueados|Estos informes muestran el historial de solicitudes para bloquear o desbloquear a los usuarios.
Historial de usuarios omitidos|Muestra el historial de solicitudes para omitir Multi-Factor Authentication para el número de teléfono de un usuario.
Alerta de fraude|Muestra un historial de las alertas de fraude enviadas durante el intervalo de fechas especificado.
En cola|Enumera los informes en cola para su procesamiento y su estado. Cuando el informe se haya completado, se proporciona un vínculo para descargar o ver el informe.

## Para ver los informes

1. Inicie sesión en [http://azure.microsoft.com](http://azure.microsoft.com)
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior, seleccione los proveedores de Multi-Factor Authentication. Aparecerá una lista de los proveedores de Multi-Factor Authentication.
4. Si tiene más de un proveedor de Multi-Factor Authentication, seleccione aquel en el que desea ver el informe de la alerta de fraude y haga clic en Administrar en la parte inferior de la página. Si sólo tiene uno, haga clic en Administrar. Se abrirá el Portal de administración de Azure Multi-Factor Authentication.
5. En el Portal de administración de Azure Multi-Factor Authentication, a la izquierda encontrará la lista Ver un informe Desde aquí puede seleccionar los informes que se han descrito anteriormente.


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Recursos adicionales**

* [Para los usuarios](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authenticaton en MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=Oct15_HO3-->