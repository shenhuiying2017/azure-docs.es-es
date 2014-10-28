<properties linkid="dev-nodejs-enablestaging" urlDisplayName="Staging Deployment" pageTitle="Stage a cloud service deployment (Node.js) - Azure" metaKeywords="Azure staging, Azure application staging, Azure test environment, Azure staging environment, Azure Virtual IP swap, Azure VIP swap" description="Learn how to deploy your Azure application to a staging environment, then deploy to a production environment using Virtual IP (VIP) swap." metaCanonical=" " services="cloud-services" documentationCenter="nodejs" title="Staging an Application in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Ensayo de una aplicación en Azure

Se puede implementar una aplicación en paquete en el entorno de ensayo de
Azure para probarla antes de que pase al
entorno de producción, lugar desde el que se podrá obtener acceso a la aplicación por Internet. El entorno
de ensayo es exactamente igual al entorno de producción, salvo que
solo puede tener acceso a la aplicación de ensayo con una URL confusa que se genera
mediante Azure. Una vez que haya comprobado que la
aplicación funciona correctamente, se puede implementar en el
entorno de producción mediante un intercambio de IP virtual (VIP).

<div class="dev-callout">
    <b>Nota:</b>
    <p>Los pasos descritos en este art&iacute;culo solo se aplican a las aplicaciones Node hospedadas como un servicio en la nube de Azure.</p>
    </div>

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Ensayo de una aplicación][Paso 1: Ensayo de una aplicación]
-   [Paso 2: Implementación de una aplicación en producción mediante el intercambio de VIP][Paso 2: Implementación de una aplicación en producción mediante el intercambio de VIP]

## <span id="step1"></span></a>Paso 1: Ensayo de una aplicación

Esta tarea trata cómo realizar el ensayo de una aplicación con **Windows
Azure PowerShell**.

1.  Para publicar un servicio, simplemente pase el parámetro **-Slot** al
    cmdlet **Publish-AzureServiceProject**.

    **Publish-AzureServiceProject -Slot staging**

2.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y seleccione **Servicios en la nube**. Una vez que el servicio en la nube se ha creado y que el estado de la columna **Staging** se ha actualizado a **Running**, haga clic en el nombre del servicio.

    ![portal que muestra un servicio en ejecución][portal que muestra un servicio en ejecución]

3.  Seleccione el **Panel** y, a continuación, **Staging**.

    ![panel de servicio en la nube][panel de servicio en la nube]

4.  Anote el valor de la entrada **Site URL** a la derecha. El nombre DNS es un identificador interno confuso que ha generado Azure.

    ![URL del sitio][URL del sitio]

Ahora puede comprobar que la aplicación funciona correctamente en el entorno de ensayo mediante la utilización de la URL del sitio de ensayo.

Para un escenario de actualización en el que la aplicación de ensayo es una
versión de actualización de una que ya se ha implementado en producción, puede
[actualizar la aplicación en el entorno de producción con el intercambio de
VIP][Paso 2: Implementación de una aplicación en producción mediante el intercambio de VIP].

## <span id="step2"></span></a>Paso 2: Actualización de una aplicación en producción mediante el intercambio de VIP

Una vez que haya comprobado la versión de actualización de una
aplicación en el entorno de ensayo, puede hacer que esté disponible
rápidamente en producción mediante el intercambio de las IP virtuales (VIP) de los entornos
de ensayo y producción.

<div class="dev-callout">
<b>Nota:</b>
<p>En este paso se supone que ya ha implementado una
aplicaci&oacute;n en producci&oacute;n y que ha almacenado provisionalmente la versi&oacute;n actualizada de la
aplicaci&oacute;n.</p>
</div>

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure],
    haga clic en **Servicios en la nube** y seleccione el nombre del servicio.

2.  En el **Panel**, seleccione **Staging** y, a continuación, haga clic en **Swap** en la parte inferior de la página. De este modo se abre el cuadro de diálogo
    Intercambio de VIP.

    ![cuadro de diálogo Intercambio de VIP][cuadro de diálogo Intercambio de VIP]

3.  Repase la información y, a continuación, haga clic en **OK**. Ambas implementaciones empiezan a
    actualizarse cuando la implementación de ensayo pasa a
    producción y la implementación de producción pasa a ensayo.

Ha realizado correctamente el ensayo de la implementación y la actualización de la
implementación de producción mediante el intercambio de las VIP con la implementación en ensayo.

## Recursos adicionales

-   [Implementar la actualización de un servicio en producción intercambiando las VIP][Implementar la actualización de un servicio en producción intercambiando las VIP]
-   [Información general sobre la administración de implementaciones en Azure][Información general sobre la administración de implementaciones en Azure]

  [Paso 1: Ensayo de una aplicación]: #step1
  [Paso 2: Implementación de una aplicación en producción mediante el intercambio de VIP]: #step2
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [portal que muestra un servicio en ejecución]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
  [panel de servicio en la nube]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [URL del sitio]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [cuadro de diálogo Intercambio de VIP]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Implementar la actualización de un servicio en producción intercambiando las VIP]: http://msdn.microsoft.com/es-es/library/windowsazure/ee517253.aspx
  [Información general sobre la administración de implementaciones en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh386336.aspx
