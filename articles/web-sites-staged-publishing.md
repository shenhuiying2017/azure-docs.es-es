<properties
	pageTitle="Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure"
	description="Aprenda a utilizar la publicación de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

<a name="Overview"></a>
# Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure

Al implementar la aplicación web en el [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714), puede implementar en un espacio de implementación independiente en lugar de en el espacio de producción predeterminado al ejecutar en el modo del plan del Servicio de aplicaciones **Estándar** o **Premium**. Los espacios de implementación son realmente aplicaciones web activas realmente con sus propios nombres de host. Los elementos de contenido y configuraciones de aplicaciones web se pueden intercambiar entre dos espacios de implementación, incluido el espacio de producción. La implementación de la aplicación en un espacio de implementación ofrece las ventajas siguientes:

- Puede validar los cambios en la aplicación web en un espacio de implementación de ensayo antes de intercambiarlo con el espacio de producción.

- La implementación de una aplicación web en un espacio en primer lugar y su intercambio con el de la producción garantiza que todas las instancias del espacio estén correctas antes de colocarse en producción. Esto elimina tiempos de inactividad a la hora de implementar la aplicación web. El redireccionamiento del tráfico es impecable y no se anulan las solicitudes como consecuencia de las operaciones de intercambio. Este flujo de trabajo completo puede automatizarse mediante la configuración de [Intercambio automático](#Configure-Auto-Swap-for-your-web-app) cuando no es necesaria la validación de intercambio previo.

- Después de un intercambio, el espacio con la aplicación web de ensayo anterior tiene ahora la aplicación web de producción anterior. Si los cambios intercambiados en el espacio de producción no son los esperados, puede realizar el mismo intercambio inmediatamente para volver a obtener el "último sitio en buen estado".

Cada modo del plan del Servicio de aplicaciones admite un número distinto de espacios de implementación. Para averiguar el número de espacios compatibles con el modo de la aplicación web, consulte [Detalles de precios de Servicio de aplicaciones](/pricing/details/app-service/). 

- Cuando la aplicación web tiene varios espacios, no puede cambiar el modo.

- El escalado no está disponible para los espacios que no son de producción.

- No se admite la administración de recursos vinculados en los espacios que no sean de producción.

	> [AZURE.NOTE] Solo en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) puede evitar este impacto potencial en un espacio de producción si mueve temporalmente el espacio de no producción a un modo del plan del Servicio de aplicaciones diferente. Tenga en cuenta que el espacio de no producción debe compartir una vez más el mismo modo con el espacio de producción antes de que pueda intercambiar los dos espacios.

<a name="Add"></a>
## Incorporación de un espacio de implementación a una aplicación web ##

La aplicación web debe estar ejecutándose en el modo **Estándar** o **Premium** para que pueda habilitar varios espacios de implementación.

1. En el [Portal de vista previa de Azure](https://portal.azure.com/), abra la hoja de la aplicación web.
2. Haga clic en **Espacios de implementación**. A continuación, en la hoja **Espacios de implementación**, haga clic en **Agregar espacio**.

	![Add a new deployment slot][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]
	> Si la aplicación web ya no está en el modo **Estándar** o **Premium**, recibirá un mensaje que indica los modos compatibles para habilitar la publicación de ensayo. Llegados a este punto, tiene la opción de seleccionar **Actualizar** e ir a la pestaña **Escalar** de la aplicación web antes de continuar.

2. En la hoja **Agregar espacio**, asigne un nombre al espacio y seleccione si la configuración de la aplicación web se debe clonar de otro espacio de implementación existente. Haga clic en la marca de verificación para continuar.

	![Configuration Source][ConfigurationSource1]

	La primera vez que agrega un espacio solo tendrá dos opciones: clonar la configuración del espacio predeterminado en producción o no.

	Después de haber creado varios espacios, podrá clonar la configuración de un espacio que no sea el de producción:

	![Configuration sources][MultipleConfigurationSources]

5. En la hoja **Espacios de implementación**, haga clic en el espacio de implementación a fin de abrir una hoja para el espacio, con un conjunto de métricas y configuración, al igual que cualquier otra aplicación web. <strong><i>nombre-aplicación-web</i>-<i>nombre-espacio-implementación</i></strong> aparecerá en la parte superior de la hoja para recordarle que está viendo el espacio de implementación.

	![Deployment Slot Title][StagingTitle]

5. Haga clic en la dirección URL de la aplicación en la hoja del espacio. Observe que el espacio de implementación tiene su propio nombre de host y que se trata también de una aplicación activa. Para limitar el acceso público al espacio de implementación, consulte [Aplicación web del Servicio de aplicaciones: bloquear acceso web a espacios de implementación que no son de producción](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

No hay ningún contenido después de la creación de un espacio de implementación. Puede implementar el espacio desde una rama de repositorio diferente o desde un repositorio completamente diferente. También puede cambiar la configuración del espacio. Utilice el perfil de publicación o las credenciales de implementación asociadas al espacio de implementación para ver las actualizaciones del contenido.  Por ejemplo, puede [publicar en este espacio mediante Git](web-sites-publish-source-control.md.

<a name="AboutConfiguration"></a>
## Configuración de espacios de implementación ##
Cuando crea un clon de la configuración de otro espacio de implementación, la configuración clonada se puede editar. Además, algunos elementos de configuración seguirán el contenido a través de un intercambio (no son específicos del espacio) mientras que otros elementos de configuración permanecerán en el mismo espacio después de un intercambio (son específicos del espacio). Las listas siguientes muestran la configuración que cambiará cuando intercambie los espacios.

**Configuraciones que se intercambian**:

- Configuración general: por ejemplo, versión de framework, 32 o 64 bits, Web Sockets
- Configuración de la aplicación (puede configurarse para ajustarse a un espacio)
- Cadenas de conexión (puede configurarse para ajustarse a un espacio)
- Asignaciones de controlador
- Configuración de supervisión y diagnóstico
- Contenido de WebJobs

**Configuraciones que no se intercambian**:

- Extremos de publicación
- Nombres de dominio personalizados
- Certificados SSL y enlaces
- Configuración de escala
- Programadores de WebJobs

Para establecer una configuración de aplicación o una cadena de conexión que se ajuste a un espacio (no intercambiado), obtenga acceso a la hoja **Configuración de la aplicación** para un espacio específico y seleccione el cuadro **Configuración de espacio** para los elementos de configuración que se deben ajustar al espacio. Tenga en cuenta que marcar un elemento de configuración como espacio específico tiene el efecto de establecer ese elemento como no intercambiable en todos los espacios de implementación asociados con la aplicación web.

![Slot settings][SlotSettings]

<a name="Swap"></a>
## Para intercambiar espacios de implementación ##

>[AZURE.IMPORTANT] Antes de colocar una aplicación web de un espacio de implementación en producción, asegúrese de que todos los valores específicos que no son de espacio están configurados exactamente como desea que estén en el destino de intercambio.

1. Para intercambiar los espacios de implementación, haga clic en el botón **Intercambiar** en la barra de comandos de la aplicación web o en la barra de comandos de un espacio de implementación. Asegúrese de que el origen y el destino del intercambio estén correctamente establecidos. Normalmente, el destino de intercambio suele ser el espacio de producción.  

	![Swap Button][SwapButtonBar]

3. Haga clic en **Aceptar** para completar la operación. Cuando termine la operación, los espacios de implementación se habrán intercambiado.

## Configuración del intercambio automático para la aplicación web ##

El intercambio automático optimiza los escenarios de DevOps donde desee implementar continuamente la aplicación web sin arranques en frío ni tiempos de inactividad para los clientes finales de la aplicación web. Cuando se configura un espacio de implementación para el intercambio automático en producción, cada vez que inserte una actualización de código para ese espacio, el Servicio de aplicaciones coloca automáticamente la aplicación web en producción, una vez que ya esté correcta en el espacio.

>[AZURE.IMPORTANT] Al habilitar el intercambio automático para un espacio, asegúrese de que la configuración de espacio sea exactamente la configuración prevista para el espacio de destino (normalmente el espacio de producción).

Es fácil configurar el intercambio automático para un espacio. Siga estos pasos:

1. En la hoja **Espacios de implementación**, seleccione un espacio que no sea de producción, haga clic en **Todas las configuraciones** para la hoja de ese espacio.  

	![][Autoswap1]

2. Haga clic en **Configuración de la aplicación**. Seleccione **Activado** para **Intercambio automático**, elija el espacio de destino deseado en **Espacio de intercambio automático** y haga clic en **Guardar** en la barra de comandos. Asegúrese de que la configuración del espacio sea exactamente la configuración prevista para el espacio de destino.

	La pestaña **Notificaciones** parpadeará en verde **CORRECTO** una vez completada la operación.

	![][Autoswap2]

	>[AZURE.NOTE] Para probar el intercambio automático para la aplicación web, puede seleccionar primero un espacio de destino que no sea de producción en **Espacio de intercambio automático** para familiarizarse con la característica.  

3. Ejecute una inserción de código para este espacio de implementación. El intercambio automático se realizará después de un breve tiempo y la actualización se reflejará en la dirección URL del espacio de destino.

<a name="Rollback"></a>
## Para revertir una aplicación de producción después de un intercambio ##
Si se identifican errores en producción después del intercambio de espacios, revierta los espacios a sus estados anteriores. Para ello, intercambie los mismos dos espacios inmediatamente.

<a name="Delete"></a>
## Para eliminar un espacio de implementación##

En la hoja de un espacio de implementación, haga clic en **Eliminar** en la barra de comandos.  

![Delete a Deployment Slot][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlets de PowerShell de Azure para espacios de implementación

PowerShell de Azure es un módulo que proporciona cmdlets para administrar Azure mediante Windows PowerShell, incluida la compatibilidad para administrar espacios de implementación de aplicaciones web del Servicio de aplicaciones de Azure.

- Para obtener información acerca de cómo instalar y configurar PowerShell de Azure y de cómo autenticar PowerShell de Azure con su suscripción de Azure, consulte [Instalación y configuración de PowerShell de Azure](install-configure-powershell.md).  

- Para mostrar los cmdlets disponibles para el Servicio de aplicaciones de Azure en la xplat-cli, llame a  `help AzureWebsite`.

----------

### Get-AzureWebsite
El cmdlet **Get-AzureWebsite** presenta información acerca de las aplicaciones web de Azure para la suscripción actual, como en el ejemplo siguiente.

`Get-AzureWebsite webappslotstest`

----------

### New-AzureWebsite
Puede crear un espacio de implementación mediante el cmdlet **New-AzureWebsite** y especificando los nombres de la aplicación web y el espacio. Indique también la misma región que la aplicación web para la creación del espacio de implementación, como en el ejemplo siguiente.

`New-AzureWebsite webappslotstest -Slot staging -Location "West US"`

----------

### Publish-AzureWebsiteProject
Puede usar el cmdlet **Publish-AzureWebsiteProject** para la implementación del contenido, como en el ejemplo siguiente.

`Publish-AzureWebsiteProject -Name webappslotstest -Slot staging -Package [path].zip`

----------

### Show-AzureWebsite
Después de aplicar las actualizaciones del contenido y de la configuración al nuevo espacio, puede validar las actualizaciones examinando el espacio mediante el cmdlet **Show-AzureWebsite**, como en el ejemplo siguiente.

`Show-AzureWebsite -Name webappslotstest -Slot staging`

----------

### Switch-AzureWebsiteSlot
El cmdlet **Switch-AzureWebsiteSlot** puede realizar una operación de intercambio para que el espacio de implementación actualizado se convierta en el sitio de producción, como en el ejemplo siguiente. La aplicación de producción no experimentará tiempos de inactividad ni arranques en frío.

`Switch-AzureWebsiteSlot -Name webappslotstest`

----------

### Remove-AzureWebsite
Si ya no necesita un espacio de implementación, se puede eliminar usando el cmdlet **Remove-AzureWebsite**, como en el ejemplo siguiente.

`Remove-AzureWebsite -Name webappslotstest -Slot staging`

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## Comandos de la interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) para espacios de implementación

La interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) proporciona comandos de varias plataformas para trabajar con Azure, incluida la compatibilidad para administrar espacios de implementación en Aplicaciones web.

- Para obtener instrucciones acerca de cómo instalar y configurar la xplat-cli, incluida la información acerca de cómo conectar la xplat-cli a su suscripción de Azure, consulte [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure](xplat-cli.md).

-  Para mostrar los comandos disponibles para el Servicio de aplicaciones de Azure en la xplat-cli, llame a  `azure site -h`.

----------
### azure site list
Para obtener información acerca de las aplicaciones web de Azure de la suscripción actual, llame a **azure site list**, como en el ejemplo siguiente.

`azure site list webappslotstest`

----------
### azure site create
Para crear un espacio de implementación, llame a **azure site create** y especifique el nombre de la aplicación web existente y el del espacio que se vaya a crear, como en el ejemplo siguiente.

`azure site create webappslotstest --slot staging`

Para habilitar el control de código fuente en el nuevo espacio, use la opción **--git**, como en el ejemplo siguiente.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
Para hacer que el espacio de implementación actualizado se convierta en la aplicación de producción, utilice el comando **azure site swap** para realizar una operación de intercambio, como en el ejemplo siguiente. La aplicación de producción no experimentará tiempos de inactividad ni arranques en frío.

`azure site swap webappslotstest`

----------
### azure site delete
Para eliminar un espacio de implementación que ya no sea necesario, utilice el comando **azure site delete**, como en el ejemplo siguiente.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de registrarse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Pasos siguientes ##
[Aplicación web del Servicio de aplicaciones de Azure: bloquear acceso web a espacios de implementación que no son de producción](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Evaluación gratuita de Microsoft Azure](/pricing/free-trial/)

## ¿Qué ha cambiado?
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal antiguo al nuevo, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

<!--HONumber=49-->