<properties linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Websites" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="9/9/2014" ms.author="cephalin" />

<a name="Overview"></a>

# Implementación preconfigurada en los sitios web de Microsoft Azure

Al implementar su aplicación en Sitios web de Azure, puede hacerlo en un espacio de implementación separado en lugar del espacio de producción predeterminado, que contiene los sitios activos con sus propios nombres de host. Esta opción está disponible en el plan de hospedaje **Estándar**. Además, puede intercambiar los sitios y las configuraciones de los sitios entre dos espacios de implementación, incluido el de producción. La implementación de la aplicación en un espacio de implementación ofrece las ventajas siguientes:

-   Puede validar los cambios en el sitio web en un espacio de implementación de ensayo antes de intercambiarlo con el espacio de producción.

-   Después del intercambio, el espacio con el sitio de ensayo anterior ahora ocupa el sitio de producción anterior. Si los cambios intercambiados en el espacio de producción no son los esperados, puede realizar el mismo intercambio inmediatamente para volver a obtener el "último sitio en buen estado".

-   La implementación de un sitio en un espacio en primer lugar y su intercambio con el de la producción garantiza que todas las instancias del espacio estén correctas antes de colocarse en producción. Esto elimina tiempos de inactividad a la hora de implementar el sitio. El redireccionamiento del tráfico es impecable y no se anulan las solicitudes como consecuencia de las operaciones de cambio.

Para cada sitio web del plan **Estándar** se admiten cuatro espacios de implementación, además del espacio de producción.

## Tabla de contenido

-   [Incorporación de una ranura de implementación a un sitio web][Incorporación de una ranura de implementación a un sitio web]
-   [Acerca de la configuración de las ranuras de implementación][Acerca de la configuración de las ranuras de implementación]
-   [Cambio de las ranuras de implementación][Cambio de las ranuras de implementación]
-   [Reversión de un sitio de producción a ensayo][Reversión de un sitio de producción a ensayo]
-   [Eliminación de una ranura de sitio][Eliminación de una ranura de sitio]
-   [cmdlets de Azure PowerShell para ranuras de sitio][cmdlets de Azure PowerShell para ranuras de sitio]
-   [Comandos de la interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) para ranuras de sitio][Comandos de la interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) para ranuras de sitio]

<a name="Add"></a>

## Incorporación de una ranura de implementación a un sitio web

Para habilitar el uso de varios espacios de implementación, el sitio web debe ejecutarse en el plan de hospedaje **Estándar**.

1.  En la página Quick Start o en la sección Quick Glance de la página Panel de su sitio web, haga clic en **Add a new deployment slot**.

    ![Agregar una nueva ranura de implementación][Agregar una nueva ranura de implementación]

    > [WACOM.NOTE]
    > Si el sitio web no está todavía en modo **estándar**, recibirá el mensaje **You must be in the standard mode to enable staged publishing**. Llegados a este punto, tiene la opción de seleccionar **Upgrade** e ir a la pestaña **Scale** de su sitio web antes de continuar.

2.  En el cuadro de diálogo **Agregar nuevo espacio de implementación**, asigne un nombre al espacio y seleccione si la configuración del sitio web se debe clonar de otro espacio de implementación. Haga clic en la marca de verificación para continuar.

    ![Origen de configuración][Origen de configuración]

    La primera vez que crea un espacio, solo tendrá dos opciones: clonar la configuración del espacio predeterminado en producción o no clonar nada.

    Después de haber creado varios espacios, podrá clonar la configuración de un espacio que no sea el de producción:

    ![Orígenes de configuración][Orígenes de configuración]

3.  En la lista de sitios web, expanda la marca situada a la izquierda del nombre del sitio web para que aparezca la ranura de implementación. Tendrá el nombre de su sitio de web seguido del nombre del espacio de implementación.

    ![Lista de sitios con ranura de implementación][Lista de sitios con ranura de implementación]

4.  Al hacer clic en el nombre de la ranura de implementación de sitio, se abrirá una página con un conjunto de pestañas, tal y como ocurre con cualquier otro sitio web. Aparecerá ***su nombre de sitio web*(*nombre de ranura de implementación*)** en la parte superior de la página del portal para recordarle que está visualizando la ranura de implementación del sitio.

    ![Título de la ranura de implementación][Título de la ranura de implementación]

5.  Haga clic en la dirección URL del sitio en la vista de panel. Observe que el espacio de implementación tiene su propio nombre de host y que se trata de un sitio activo. Para limitar el acceso público al espacio de implementación, consulte [Sitios web de Azure: bloquear acceso web a espacios de implementación que no sean de producción][Sitios web de Azure: bloquear acceso web a espacios de implementación que no sean de producción].

    -   

No hay contenido. Puede implementar el espacio desde una rama de repositorio diferente o desde un repositorio completamente diferente. También puede cambiar la configuración del espacio. Utilice el perfil de publicación o las credenciales de implementación asociadas al espacio de implementación para ver las actualizaciones del contenido. Por ejemplo, puede [publicar en este espacio mediante Git][publicar en este espacio mediante Git].

<a name="AboutConfiguration"></a>

## Acerca de la configuración de las ranuras de implementación

Cuando crea un clon de la configuración de otro espacio de implementación, la configuración clonada se puede editar. Las listas siguientes muestran la configuración que cambiará cuando intercambie los espacios.

**Configuración que se modificará en el cambio de ranuras**:

-   Configuración general
-   Cadenas de conexión
-   Asignaciones de controlador
-   Configuración de supervisión y diagnóstico

**Configuración que no se modificará en el cambio de ranuras**:

-   Extremos de publicación
-   Nombres de dominio personalizados
-   Certificados SSL y enlaces
-   Configuración de escala

**Notas**:

-   Varios espacios de implementación solo están disponibles para los sitios con el plan de hospedaje **Estándar**.

-   Cuando el sitio tiene varios espacios, no es posible cambiar el plan de hospedaje.

-   Un espacio de implementación que pretenda cambiar a producción tiene que estar configurado tal y como quiera que esté en producción.

-   De forma predeterminada, una ranura de implementación apuntará a la misma base de datos que el sitio de producción. No obstante, puede configurar la ranura de implementación para que apunte a una base de datos diferente cambiando las cadenas de conexión de la base de datos de la ranura de implementación. A continuación, puede restaurar las cadenas de conexión de la base de datos original en la ranura de implementación justo antes de cambiarla a producción.

<a name="Swap"></a>

## Cambio de las ranuras de implementación

1.  Para intercambiar espacios de implementación, seleccione el espacio de implementación de la lista de sitios web que desee intercambiar y haga clic en el botón **Intercambiar** de la barra de comandos.

    ![Botón de cambio][Botón de cambio]

2.  Aparecerá el cuadro de diálogo Swap Deployments. El cuadro de diálogo le permite elegir qué ranura de sitio debe ser el origen y qué sitio debe ser el destino.

    ![Cuadro de diálogo para cambiar implementaciones][Cuadro de diálogo para cambiar implementaciones]

3.  Haga clic en la marca de verificación para terminar la operación. Cuando termine la operación, las ranuras de sitio se habrán cambiado.

<a name="Rollback"></a>

## Reversión de un sitio de producción a ensayo

Si se identifican errores en producción después del intercambio de espacios, revierta los espacios a sus estados anteriores. Para ello, intercambie los mismos dos espacios inmediatamente.

<a name="Delete"></a>

## Eliminación de una ranura de sitio

En la barra de comandos situada al final de la página del Portal de Sitios web Azure, haga clic en **Delete**. Tendrá la opción de eliminar el sitio web y todos las ranuras de implementación, o bien eliminar solo la ranura de implementación.

![Eliminar una ranura de sitio][Eliminar una ranura de sitio]

**Notas**:

-   El escalado no está disponible para los espacios que no sean de producción. Solo está disponible para los espacios de producción.

-   No se admite la administración de recursos vinculados en los espacios que no sean de producción.

-   Puede seguir publicando directamente en su espacio de producción si lo desea.

-   De forma predeterminada, sus espacios de implementación (sitios) comparten los mismos recursos que los espacios de producción (sitios) y se ejecutan en las mismas máquinas virtuales. Si realiza pruebas de carga en una ranura de ensayo, su entorno de producción experimentará una carga similar.

    > [WACOM.NOTE] Solo en el [Portal de Azure en vista previa][Portal de Azure en vista previa], puede evitar este impacto potencial en un espacio de producción si mueve temporalmente el espacio de no producción a un plan de hospedaje web diferente. Tenga en cuenta que, antes de poder intercambiar el espacio de prueba con el de producción, los espacios de prueba y producción deben compartir nuevamente el plan de hospedaje web.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## cmdlets de Azure PowerShell para ranuras de sitio

Azure PowerShell es un módulo que proporciona cmdlets para administrar Azure mediante Windows PowerShell, incluyendo la compatibilidad para administrar las ranuras de implementación de Sitios web Azure.

-   Para obtener información acerca de cómo instalar y configurar Azure PowerShell y de cómo autenticar Azure PowerShell con su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

-   Para mostrar los cmdlets disponibles para Sitios web Azure en PowerShell, llame `help AzureWebsite`.

------------------------------------------------------------------------

### Get-AzureWebsite

El cmdlet **Get-AzureWebsite** presenta información acerca de los sitios web de Azure para la suscripción actual, como en el ejemplo siguiente.

`Get-AzureWebsite siteslotstest`

------------------------------------------------------------------------

### New-AzureWebsite

Puede crear una ranura de sitio para cualquier sitio web en modo estándar usando el cmdlet **New-AzureWebsite** y especificando los nombres del sitio y la ranura. Indique también la misma región que el sitio para la creación de la ranura de implementación, como en el ejemplo siguiente.

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

------------------------------------------------------------------------

### Publish-AzureWebsiteProject

Puede usar el cmdlet **Publish-AzureWebsiteProject** para la implementación del contenido, como en el ejemplo siguiente.

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

------------------------------------------------------------------------

### Show-AzureWebsite

Después de aplicar las actualizaciones del contenido y la configuración a la nueva ranura, puede validar las actualizaciones examinando la ranura mediante el cmdlet **Show-AzureWebsite**, como en el ejemplo siguiente.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

------------------------------------------------------------------------

### Switch-AzureWebsiteSlot

El cmdlet **Switch-AzureWebsiteSlot** puede realizar una operación de cambio para que la ranura de implementación actualizada se convierta en el sitio de producción, como en el ejemplo siguiente. El sitio de producción no experimentará tiempos de inactividad ni arranques en frío.

`Switch-AzureWebsiteSlot -Name siteslotstest`

------------------------------------------------------------------------

### Remove-AzureWebsite

Si ya no necesita una ranura de implementación, se puede eliminar usando el cmdlet **Remove-AzureWebsite**, como en el ejemplo siguiente.

`Remove-AzureWebsite -Name siteslotstest -Slot staging`

------------------------------------------------------------------------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>

## Comandos de la interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) para ranuras de sitio

La interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) proporciona comandos de varias plataformas para trabajar con Azure, incluyendo la compatibilidad para administrar ranuras de implementación en Sitios web Azure.

-   Para obtener instrucciones acerca de cómo instalar y configurar la xplat-cli, incluyendo la información acerca de cómo conectar la xplat-cli a su suscripción de Azure, consulte [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure][Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure].

-   Para mostrar los comandos disponibles para Sitios web Azure en la xplat-cli, llame `azure site -h`.

------------------------------------------------------------------------

### azure site list

Para obtener información acerca de los sitios web de Azure de la suscripción actual, llame a **azure site list**, como en el ejemplo siguiente.

`azure site list siteslotstest`

------------------------------------------------------------------------

### azure site create

Para crear una ranura de sitio para cualquier sitio web en modo estándar, llame a **azure site create** y especifique el nombre del sitio existente y el de la ranura que se vaya a crear, como en el ejemplo siguiente.

`azure site create siteslotstest --slot staging`

Para habilitar el control del origen en la nueva ranura, use la opción **--git**, como en el ejemplo siguiente.

`azure site create --git siteslotstest --slot staging`

------------------------------------------------------------------------

### azure site swap

Para hacer que el espacio de implementación actualizado se convierta en el sitio de producción, utilice el comando **azure site swap** para realizar una operación de intercambio, como en el ejemplo siguiente. El sitio de producción no experimentará tiempos de inactividad ni arranques en frío.

`azure site swap siteslotstest`

------------------------------------------------------------------------

### azure site delete

Para eliminar un espacio de implementación que ya no sea necesario, utilice el comando **azure site delete**, como en el ejemplo siguiente.

`azure site delete siteslotstest --slot staging`

------------------------------------------------------------------------

## Pasos siguientes

[Sitios web de Azure: bloquear acceso web a espacios de implementación que no sean de producción.][Sitios web de Azure: bloquear acceso web a espacios de implementación que no sean de producción]

[Evaluación gratuita de Microsoft Azure][Evaluación gratuita de Microsoft Azure]

<!-- IMAGES -->

  [Incorporación de una ranura de implementación a un sitio web]: #Add
  [Acerca de la configuración de las ranuras de implementación]: #AboutConfiguration
  [Cambio de las ranuras de implementación]: #Swap
  [Reversión de un sitio de producción a ensayo]: #Rollback
  [Eliminación de una ranura de sitio]: #Delete
  [cmdlets de Azure PowerShell para ranuras de sitio]: #PowerShell
  [Comandos de la interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) para ranuras de sitio]: #CLI
  [Agregar una nueva ranura de implementación]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
  [Origen de configuración]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
  [Orígenes de configuración]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
  [Lista de sitios con ranura de implementación]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
  [Título de la ranura de implementación]: ./media/web-sites-staged-publishing/StagingTitle.png
  [Sitios web de Azure: bloquear acceso web a espacios de implementación que no sean de producción]: http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/
  [publicar en este espacio mediante Git]: http://azure.microsoft.com/es-es/documentation/articles/web-sites-publish-source-control/
  [Botón de cambio]: ./media/web-sites-staged-publishing/SwapButtonBar.png
  [Cuadro de diálogo para cambiar implementaciones]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
  [Eliminar una ranura de sitio]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
  [Portal de Azure en vista previa]: https://portal.azure.com
  [Instalación y configuración de Azure PowerShell]: http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell
  [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure]: http://www.windowsazure.com/es-es/documentation/articles/xplat-cli
  [Evaluación gratuita de Microsoft Azure]: http://azure.microsoft.com/es-es/pricing/free-trial/
