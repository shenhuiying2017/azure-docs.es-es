<properties 
	pageTitle="Implementación de ensayo en Sitios web Azure de Microsoft" 
	description="Obtenga información acerca de cómo usar la publicación de ensayo en sitios web de Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/9/2014" 
	ms.author="cephalin"/>

<a name="Overview"></a>
#Implementación de ensayo en Sitios web Microsoft Azure#
Al implementar su aplicación en Sitios web Azure, puede hacerlo en un espacio de implementación separado en lugar de en el espacio de producción predeterminado, que son en realidad sitios activos con sus propios nombres de host. Esta opción está disponible en el plan de hospedaje **Estándar**. Además, puede intercambiar los sitios y las configuraciones de los sitios entre dos espacios de implementación, incluido el de producción. La implementación de la aplicación en un espacio de implementación ofrece las ventajas siguientes:

- Puede validar los cambios en el sitio web en un espacio de implementación de ensayo antes de intercambiarlo con el espacio de producción.

- Después del intercambio, el espacio con el sitio de ensayo anterior ahora ocupa el sitio de producción anterior. Si los cambios intercambiados en el espacio de producción no son los esperados, puede realizar el mismo intercambio inmediatamente para volver a obtener el "último sitio en buen estado". 
 
- La implementación de un sitio en un espacio en primer lugar y su intercambio con el de la producción garantiza que todas las instancias del espacio estén correctas antes de colocarse en producción. Esto elimina tiempos de inactividad a la hora de implementar el sitio. El redireccionamiento del tráfico es impecable y no se anulan las solicitudes como consecuencia de las operaciones de cambio. 

Para cada sitio web del plan **Estándar** se admiten cuatro espacios de implementación, además del espacio de producción. 

## Tabla de contenido ##
- [Incorporación de una ranura de implementación a un sitio web](#Add)
- [Acerca de la configuración de las ranuras de implementación](#AboutConfiguration)
- [Cambio de las ranuras de implementación](#Swap)
- [Reversión de un sitio de producción a ensayo](#Rollback)
- [Eliminación de una ranura de sitio](#Delete)
- [cmdlets de Azure PowerShell para espacios de sitio](#PowerShell)
- [Comandos de la interfaz de la línea de comandos entre plataformas (xplat-cli) de Azure para espacios de sitio](#CLI)

<a name="Add"></a>
##Incorporación de un espacio de implementación a un sitio web##

Para habilitar el uso de varios espacios de implementación, el sitio web debe ejecutarse en el plan de hospedaje **Estándar**. 

1. En la página Inicio rápido o en la sección Vista rápida de la página Panel de su sitio web, haga clic en **Agregar un nuevo espacio de implementación**. 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [AZURE.NOTE]
	> Si el sitio web no está todavía en modo **Standard**, recibirá el mensaje **Para habilitar la publicación de ensayo, debe estar en el modo Standard**. Llegados a este punto, tiene la opción de seleccionar **Actualizar** e ir a la pestaña **Escalar** de su sitio web antes de continuar.
	
2. En el cuadro de diálogo **Agregar un nuevo espacio de implementación**, asigne un nombre al espacio y seleccione si la configuración del sitio web se debe clonar de otro espacio de implementación. Haga clic en la marca de verificación para continuar. 
	
	![Configuration Source][ConfigurationSource1]
	
	La primera vez cree un espacio, solo tendrá dos opciones: clonar la configuración del espacio en producción predeterminado o no. 
	
	Después de haber creado varios espacios, podrá clonar la configuración de un espacio que no sea el de producción:
	
	![Configuration sources][MultipleConfigurationSources]

5. En la lista de sitios web, expanda la marca situada a la izquierda del nombre del sitio web para que aparezca la ranura de implementación. Tendrá el nombre de su sitio de web seguido del nombre del espacio de implementación. 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. Al hacer clic en el nombre del espacio de sitio de implementación, se abrirá una página con un conjunto de pestañas, como en cualquier otro sitio web. <strong><i>your-website-name</i>(<i>deployment-slot-name</i>)</strong> aparecerá en la parte superior de la página del portal para recordarle que está viendo el espacio del sitio de implementación.
	
	![Deployment Slot Title][StagingTitle]
	
5. Haga clic en la dirección URL del sitio en la vista de panel. Observe que el espacio de implementación tiene su propio nombre de host y que se trata de un sitio activo. Para limitar el acceso público al espacio de implementación, consulte [Sitios web Azure: bloquear acceso web a espacios de implementación que no sean de producción](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

	-	 

No hay contenido. Puede implementar el espacio desde una rama de repositorio diferente o desde un repositorio completamente diferente. También puede cambiar la configuración del espacio. Utilice el perfil de publicación o las credenciales de implementación asociadas al espacio de implementación para ver las actualizaciones del contenido.  Por ejemplo, puede [publicar en este espacio mediante Git](http://azure.microsoft.com/es-es/documentation/articles/web-sites-publish-source-control/).

<a name="AboutConfiguration"></a>
##Acerca de la configuración para espacios de implementación##
Cuando crea un clon de la configuración de otro espacio de implementación, la configuración clonada se puede editar. Las listas siguientes muestran la configuración que cambiará cuando intercambie los espacios.

**Configuración que se modificará en el cambio de espacios**:

- Configuración general
- Cadenas de conexión
- Asignaciones de controlador
- Configuración de supervisión y diagnóstico

**Configuración que no se modificará en el cambio de espacios**:

- Extremos de publicación
- Nombres de dominio personalizados
- Certificados SSL y enlaces
- Configuración de escala

**Notas**:

- Varios espacios de implementación solo están disponibles para los sitios con el plan de hospedaje **Estándar**.

- Cuando el sitio tiene varios espacios, no es posible cambiar el plan de hospedaje.

- Un espacio de implementación que pretenda cambiar a producción tiene que estar configurado tal y como quiera que esté en producción.

- De forma predeterminada, una ranura de implementación apuntará a la misma base de datos que el sitio de producción. No obstante, puede configurar la ranura de implementación para que apunte a una base de datos diferente cambiando las cadenas de conexión de la base de datos de la ranura de implementación. A continuación, puede restaurar las cadenas de conexión de la base de datos original en la ranura de implementación justo antes de cambiarla a producción.


<a name="Swap"></a>
##Cambio de los espacios de implementación##

1. Para intercambiar espacios de implementación, seleccione el espacio de implementación de la lista de sitios web que desee intercambiar y haga clic en el botón **Intercambiar** de la barra de comandos. 
	
	![Swap Button][SwapButtonBar]
	
2. Aparecerá el cuadro de diálogo Swap Deployments. El cuadro de diálogo le permite elegir qué ranura de sitio debe ser el origen y qué sitio debe ser el destino.
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. Haga clic en la marca de verificación para terminar la operación. Cuando termine la operación, las ranuras de sitio se habrán cambiado.


<a name="Rollback"></a>
##Reversión de un sitio de producción a ensayo##
Si se identifican errores en producción después del intercambio de espacios, revierta los espacios a sus estados anteriores. Para ello, intercambie los mismos dos espacios inmediatamente. 

<a name="Delete"></a>
##Eliminación de un espacio de sitio##

En la barra de comandos situada al final de la página del Portal de Sitios web Azure, haga clic en **Eliminar**. Tendrá la opción de eliminar el sitio web y todos las ranuras de implementación, o bien eliminar solo la ranura de implementación. 

![Delete a Site Slot][DeleteStagingSiteButton]

**Notas**:

- El escalado no está disponible para los espacios que no sean de producción. Solo está disponible para los espacios de producción.

- No se admite la administración de recursos vinculados en los espacios que no sean de producción. 

- Puede seguir publicando directamente en su espacio de producción si lo desea.

- De forma predeterminada, sus espacios de implementación (sitios) comparten los mismos recursos que los espacios de producción (sitios) y se ejecutan en las mismas máquinas virtuales. Si realiza pruebas de carga en una ranura de ensayo, su entorno de producción experimentará una carga similar. 
	
	> [AZURE.NOTE]  Solo en el [Portal de vista previa de Azure](https://portal.azure.com) puede evitar este impacto potencial en un espacio de producción si mueve temporalmente el espacio de no producción a un plan de hospedaje web diferente. Tenga en cuenta que, antes de poder intercambiar el espacio de prueba con el de producción, los espacios de prueba y producción deben compartir nuevamente el plan de hospedaje web.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##cmdlets de Azure PowerShell para ranuras de sitio 

Azure PowerShell es un módulo que proporciona cmdlets para administrar Azure mediante Windows PowerShell, incluyendo la compatibilidad para administrar los espacios de implementación de Sitios web Azure. 

- Para obtener información acerca de cómo instalar y configurar Azure PowerShell y cómo autenticar Azure PowerShell con su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell).  

- Para mostrar los cmdlets disponibles para Sitios web Azure en PowerShell, llame  `help AzureWebsite`. 

----------

###Get-AzureWebsite
El cmdlet **Get-AzureWebsite** presenta información acerca de los sitios web de Azure para la suscripción actual, como en el ejemplo siguiente. 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
Puede crear un espacio de sitio para cualquier sitio web en modo estándar usando el cmdlet **New-AzureWebsite** y especificando los nombres del sitio y el espacio. Indique también la misma región que el sitio para la creación de la ranura de implementación, como en el ejemplo siguiente. 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
Puede usar el cmdlet **Publish-AzureWebsiteProject** para la implementación del contenido, como en el ejemplo siguiente. 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
Después de aplicar las actualizaciones del contenido y la configuración al nuevo espacio, puede validar las actualizaciones examinando el espacio mediante el cmdlet **Show-AzureWebsite**, como en el ejemplo siguiente.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
El cmdlet **Switch-AzureWebsiteSlot** puede realizar una operación de cambio para que el espacio de implementación actualizado se convierta en el sitio de producción, como en el ejemplo siguiente. El sitio de producción no experimentará tiempos de inactividad ni arranques en frío. 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
Si ya no necesita un espacio de implementación, se puede eliminar usando el cmdlet **Remove-AzureWebsite**, como en el ejemplo siguiente.

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##Comandos de la interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) para ranuras de sitio

La interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) proporciona comandos de varias plataformas para trabajar con Azure, incluyendo la compatibilidad para administrar espacios de implementación en Sitios web Azure. 

- Para obtener instrucciones acerca de cómo instalar y configurar la xplat-cli, incluyendo la información acerca de cómo conectar la xplat-cli a su suscripción de Azure, consulte [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure](http://www.windowsazure.com/es-es/documentation/articles/xplat-cli). 

-  Para mostrar los comandos disponibles para Sitios web Azure en la xplat-cli, llame  `azure site -h`. 

----------
###azure site list
Para obtener información acerca de los sitios web de Azure de la suscripción actual, llame a **azure site list**, como en el ejemplo siguiente.
 
`azure site list siteslotstest`

----------
###azure site create
Para crear un espacio de sitio para cualquier sitio web en modo estándar, llame a **azure site create** y especifique el nombre del sitio existente y el del espacio que se vaya a crear, como en el ejemplo siguiente.

`azure site create siteslotstest --slot staging`

Para habilitar el control del origen en el nuevo espacio, use la opción **--git**, como en el ejemplo siguiente.
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
Para hacer que el espacio de implementación actualizado se convierta en el sitio de producción, utilice el comando **azure site swap** para realizar una operación de intercambio, como en el ejemplo siguiente. El sitio de producción no experimentará tiempos de inactividad ni arranques en frío. 

`azure site swap siteslotstest`

----------
###azure site delete
Para eliminar un espacio de implementación que ya no sea necesario, utilice el comando **azure site delete**, como en el ejemplo siguiente.

`azure site delete siteslotstest --slot staging`

----------
## Pasos siguientes ##
[Sitios web Azure: bloqueo del acceso web a espacios de implementación que no sean de producción.](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Evaluación gratuita de Microsoft Azure](http://azure.microsoft.com/es-es/pricing/free-trial/)


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




<!--HONumber=42-->
