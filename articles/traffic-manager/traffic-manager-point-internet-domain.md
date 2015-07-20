<properties
   pageTitle="Hacer que un dominio de Internet de la empresa indique un dominio del Administrador de tráfico"
   description="Este artículo le ayudará a que el nombre de dominio de la empresa indique un nombre de dominio del Administrador de tráfico."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# un dominio de Internet de la compañía a un dominio del Administrador de tráfico

Para que el nombre de dominio de la empresa apunte a un nombre de dominio del Administrador de tráfico, modifique el registro de recursos DNS del servidor DNS de Internet para que use el tipo de registro CNAME, que asigna el nombre de dominio de la empresa al nombre de dominio del perfil de Administrador de tráfico. Puede ver el nombre de dominio del Administrador de tráfico en la sección **General** de la página Configuración del perfil del Administrador de tráfico.

Por ejemplo, para que el nombre de dominio de la compañía **www.contoso.com** indique el nombre de dominio del Administrador de tráfico **contoso.trafficmanager.net**, debe actualizar el registro de recursos DNS para que sea el siguiente:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Todas las solicitudes de tráfico de *www.contoso.com* se redirigirán ahora a *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT]No puede hacer que un dominio de segundo nivel como por ejemplo *contoso.com*, indique el dominio del Administrador de tráfico. Se trata de una limitación del protocolo DNS, que no permite registros CNAME para nombres de dominio de segundo nivel.

## Otras referencias

[Acerca de los métodos de equilibrio de carga del Administrador de tráfico](traffic-manager-load-balancing-methods.md)

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Información general sobre el Administrador de tráfico](traffic-manager-overview.md)
 

<!---HONumber=July15_HO2-->