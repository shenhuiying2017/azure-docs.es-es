<properties 
	pageTitle="Implementación de ensayo de un servicio en la nube (Node.js) - Azure" 
	description="Vea cómo implementar su aplicación de Azure en un entorno de ensayo y después en un entorno de producción mediante el intercambio de IP virtuales (VIP)." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# Ensayo de una aplicación en Azure

Se puede implementar una aplicación en paquete en el entorno de ensayo en
Azure para probarla antes de pasarla al entorno de
producción, en el cual es posible tener acceso a la aplicación a través de Internet. El
entorno de ensayo es exactamente igual al entorno de producción, excepto
en que solo puede tener acceso a la aplicación de ensayo con una dirección URL confusa
generada por Azure. Una vez que comprueba que la
aplicación funciona correctamente, es posible implementarla en el entorno de
producción a través de un intercambio de IP virtual (VIP).

> [AZURE.NOTE] Los pasos descritos en este artículo solo se aplican a las aplicaciones Node hospedadas como un servicio en la nube de Azure.

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Ensayo de una aplicación]
-   [Paso 2: Implementación de una aplicación en producción mediante el intercambio de VIP]

<h2><a id="step1"></a>Paso 1: Ensayo de una aplicación</h2>

Esta tarea trata cómo realizar el ensayo de una aplicación con **Microsoft
Azure PowerShell**.

1.  Para publicar un servicio, simplemente pase el parámetro **-Slot** al
    cmdlet **Publish-AzureServiceProject**.

    **Publish-AzureServiceProject -Slot staging**

2.  Inicie sesión en el [Portal de administración de Azure] y seleccione **Servicios en la nube**. Una vez que se crea el servicio en la nube y que el estado de la columna **Ensayo** se actualiza a **En ejecución**, haga clic en el nombre del servicio.

	![portal displaying a running service][cloud-service]

3.  Seleccione el **Panel** y, a continuación, seleccione **Ensayo**.

	![cloud service dashboard][cloud-service-dashboard]

4. Anote el valor de la entrada **Dirección URL del sitio** que aparece a la derecha. El nombre DNS es un identificador interno confuso que ha generado Azure.

    ![site url][cloud-service-staging-url]

Ahora puede comprobar que la aplicación funciona correctamente en el entorno de ensayo mediante la utilización de la URL del sitio de ensayo.

En un escenario de actualización, en el que la aplicación de ensayo es una
versión actualizada de una aplicación ya implementada en producción, puede
[actualizar la aplicación en el entorno de producción mediante el intercambio de las
VIP][Paso 2: Implementación de una aplicación en producción mediante el intercambio de VIP].

<h2><a id="step2"></a>Paso 2: Actualización de una aplicación en producción mediante el intercambio de VIP</h2>

Después de comprobar la versión actualizada de una aplicación en el
entorno de producción, puede ponerla rápidamente a disposición en el entorno de producción
mediante el intercambio de las IP virtuales (VIP) de los entornos de ensayo
y producción.

> [AZURE.NOTE] En este paso se supone que ya ha implementado una
aplicación en producción y que ha almacenado provisionalmente la versión actualizada de la
aplicación.

1.  Inicie sesión en el [Portal de administración de Azure],
    haga clic en **Servicios en la nube** y, a continuación, seleccione el nombre del servicio.

2.  En el **Panel**, seleccione **Ensayo** y haga clic en **Intercambiar** en la parte inferior de la página. De este modo se abre el cuadro de diálogo
    Intercambio de VIP.

    ![vip swap dialog][vip-swap-dialog]

3.  Revise la información y, a continuación, haga clic en **Aceptar**. Ambas implementaciones
    comienzan a actualizarse a medida que la implementación de ensayo cambia a producción
    y la implementación de producción cambia a ensayo.

Pasó correctamente una implementación a ensayo y actualizó correctamente una implementación
de producción al intercambiar VIP con la implementación en el entorno de ensayo.

## Recursos adicionales

- [Implementación de una actualización de servicio en producción intercambiando las VIP en Azure]
- [Información general de administración de implementaciones en Azure]

  [Paso 1: Ensayo de una aplicación]: #step1
  [Paso 2: Implementación de una aplicación en producción mediante el intercambio de VIP]: #step2
  [Portal de administración de Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Implementación de una actualización de servicio en producción intercambiando las VIP en Azure]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Información general de administración de implementaciones en Azure]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 
