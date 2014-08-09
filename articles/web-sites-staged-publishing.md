<properties  linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

# Implementación preconfigurada en los sitios web de Microsoft Azure

## Tabla de contenido

* [Introducción](#Overview)
* [Incorporación de una ranura de implementación a un sitio web](#Add)
* [Acerca de la configuración de las ranuras de   implementación](#AboutConfiguration)
* [Cambio de las ranuras de implementación](#Swap)
* [Reversión de un sitio de producción a ensayo](#Rollback)
* [Eliminación de una ranura de sitio](#Delete)
* [cmdlets de Azure PowerShell para ranuras de sitio](#PowerShell)
* [Comandos de la interfaz de la línea de comandos de Azure de varias   plataformas (xplat-cli) para ranuras de sitio](#CLI)

<a name="Overview"></a>
## Introducción ##
La opción para crear ranuras de sitio para sitios en modo Standard que se ejecutan en sitios web de Microsoft Azure permite flujos de trabajo de implementación de ensayo. Cree una ranura de sitio en desarrollo o ensayo para cada sitio de producción predeterminado (que se convertirá en una ranura de producción) y cambie estas ranuras sin tiempos de inactividad. La implementación de ensayo es muy valiosa para las siguientes situaciones:

* **Validación previa a la implementación**: Después de implementar el contenido o la configuración en una ranura de sitio de ensayo, puede validar los cambios antes de pasarlos a producción.

* **Creación e integración del contenido del sitio**: Puede agregar paulatinamente actualizaciones del contenido a su ranura de implementación de ensayo y, a continuación, cambiar la ranura de implementación a producción cuando las actualizaciones se hayan completado.

* **Reversión de un sitio de producción**: Si los cambios pasados a producción no son los previstos, puede recuperar el contenido original para producción de forma inmediata.

Microsoft Azure prepara todas las instancias de la ranura del sitio de origen antes del cambio a producción, eliminando los arranques en frío al implementar el contenido. El redireccionamiento del tráfico es impecable y no se anulan las solicitudes como consecuencia de las operaciones de cambio. Actualmente, solo se admite una ranura de implementación además de la ranura de producción predeterminada por cada sitio web en modo estándar.

<a name="Add"></a>
## Incorporación de una ranura de implementación a un sitio web ##

El sito debe ejecutarse en modo estándar para permitir la creación de una ranura de sitio.

1.  En la página Quick Start o en la sección Quick Glance de la página Panel de su sitio web, haga clic en **Add a new deployment slot**.
    
    ![Agregar una nueva ranura de implementación](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)


   
    > [WACOM.NOTE]
    > Si el sitio web no está todavía en modo estándar, recibirá el mensaje **You must be in the standard mode to enable staged publishing**. Llegados a este punto, tiene la opción de seleccionar **Upgrade** e ir a la pestańa **Scale** de su sitio web antes de continuar.

2.  Aparecerá el cuadro de diálogo **Add New Deployment Slot**.
    
    ![Cuadro de diálogo para agregar una nueva ranura de implementación](./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png)
    
    Proporcione un nombre para la ranura de implementación. El nombre no puede superar los 60 caracteres alfanuméricos. No se admiten caracteres especiales ni espacios.

3.  En la lista de sitios web, expanda la marca situada a la izquierda del nombre del sitio web para que aparezca la ranura de implementación. Tendrá el nombre de su sitio de producción seguido del nombre de la ranura de producción que ha proporcionado entre paréntesis.
    
    ![Lista de sitios con ranura de implementación](./media/web-sites-staged-publishing/SiteListWithStagedSite.png)

4.  Al seleccionar el nombre de la ranura de implementación de sitio, se  abrirá una página con un conjunto de pestańas, tal y como ocurre con cualquier otro sitio web. Aparecerá ***su nombre de sitio web*(*nombre de ranura de implementación*)** en la parte superior de la página del portal para recordarle que está visualizando la ranura de implementación del sitio.
    
    ![Título de la ranura de implementación](./media/web-sites-staged-publishing/StagingTitle.png)

Ahora puede actualizar el contenido y la configuración de la ranura de implementación del sitio. Utilice el perfil de publicación o las credenciales de implementación asociadas a la ranura de implementación del sitio para ver las actualizaciones del contenido.

<a name="AboutConfiguration"></a>
## Acerca de la configuración de las ranuras de implementación ##
Cuando se crea una ranura de implementación, su configuración se clona desde el sitio de producción de forma predeterminada. Se puede modificar la configuración de todas las ranuras del sitio.

**Configuración que se modificará en el cambio de ranuras**:

* Configuración general
* Cadenas de conexión
* Asignaciones de controlador
* Configuración de supervisión y diagnóstico

**Configuración que no se modificará en el cambio de ranuras**:

* Extremos de publicación
* Nombres de dominio personalizados
* Certificados SSL y enlaces
* Configuración de escala

**Notas**:

* Las ranuras de implementación solo están disponibles para sitios en modo estándar.

* Si cambia un sitio al modo libre, compartido o básico, ya no podrá cambiarse.

* Las ranuras de implementación que pretenda cambiar a producción tienen que estar configuradas tal y como quiera que estén en producción.

* De forma predeterminada, una ranura de implementación apuntará a la misma base de datos que el sitio de producción. No obstante, puede configurar la ranura de implementación para que apunte a una base de datos diferente cambiando las cadenas de conexión de la base de datos de la ranura de implementación. A continuación, puede restaurar las cadenas de conexión de la base de datos original en la ranura de implementación justo antes de cambiarla a producción.

<a name="Swap"></a>
## Cambio de las ranuras de implementación ##

1.  Para cambiar las ranuras de implementación, seleccione la ranura de implementación de la lista de sitios web y haga clic en el botón **Swap** de la barra de comandos.
    
    ![Botón de cambio](./media/web-sites-staged-publishing/SwapButtonBar.png)

2.  Aparecerá el cuadro de diálogo Swap Deployments. El cuadro de diálogo le permite elegir qué ranura de sitio debe ser el origen y qué sitio debe ser el destino.
    
    ![Cuadro de diálogo para cambiar implementaciones](./media/web-sites-staged-publishing/SwapDeploymentsDialog.png)

3.  Haga clic en la marca de verificación para terminar la operación. Cuando termine la operación, las ranuras de sitio se habrán cambiado.

<a name="Rollback"></a>
## Reversión de un sitio de producción a ensayo ##
Si se identifican errores en el contenido o la configuración que ha pasado a producción, puede cambiar simplemente una ranura de implementación (su anterior sitio de producción) de nuevo a producción y, a continuación, realizar revisiones en la nueva versión de su sitio mientras está en modo de ensayo.

> [WACOM.NOTE]
> Para que una reversión se realice correctamente, la ranura de implementación de sitio debe tener aún intactos el contenido y la configuración del anterior sitio de producción.

<a name="Delete"></a>
## Eliminación de una ranura de sitio ##

En la barra de comandos situada al final de la página del Portal de Sitios web Azure, haga clic en **Delete**. Tendrá la opción de eliminar el sitio web y todos las ranuras de implementación, o bien eliminar solo la ranura de implementación.

![Eliminar una ranura desitio](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

Después de responder **Yes** al mensaje de confirmación, se eliminará una ranura o todas, dependiendo de la opción que haya elegido.

**Notas**:

* El escalado no está disponible para las ranuras de sitio que no sean de producción. Solo está disponible para las ranuras de sitios de producción.

* No se admite la administración de recursos vinculados en las ranuras de sitio que no sean de producción.

* Puede seguir publicando directamente en su sitio de producción si lo desea.

* Actualmente, sus ranuras de implementación (sitios) comparten los mismos recursos que las ranuras de producción (sitios) y se ejecutan en las mismas máquinas virtuales. Si realiza pruebas de carga en una ranura de ensayo, su entorno de producción experimentará una carga
  similar.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## cmdlets de Azure PowerShell para ranuras de sitio

Azure PowerShell es un módulo que proporciona cmdlets para administrar Azure mediante Windows PowerShell, incluyendo la compatibilidad para administrar las ranuras de implementación de Sitios web Azure.

* Para obtener información acerca de cómo instalar y configurar Azure PowerShell y de cómo autenticar Azure PowerShell con su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell][1].

* Para mostrar los cmdlets disponibles para Sitios web Azure en PowerShell, llame a `help AzureWebsite`.

* * *

### Get-AzureWebsite

El cmdlet **Get-AzureWebsite** presenta información acerca de los sitios web de Azure para la suscripción actual, como en el ejemplo siguiente.

`Get-AzureWebsite siteslotstest`

* * *

### New-AzureWebsite

Puede crear una ranura de sitio para cualquier sitio web en modo estándar usando el cmdlet **New-AzureWebsite** y especificando los nombres del sitio y la ranura. Indique también la misma región que el sitio para la creación de la ranura de implementación, como en el ejemplo siguiente.

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

* * *

### Publish-AzureWebsiteProject

Puede usar el cmdlet **Publish-AzureWebsiteProject** para la implementación del contenido, como en el ejemplo siguiente.

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

* * *

### Show-AzureWebsite

Después de aplicar las actualizaciones del contenido y la configuración a la nueva ranura, puede validar las actualizaciones examinando la ranura mediante el cmdlet **Show-AzureWebsite**, como en el ejemplo siguiente.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

* * *

### Switch-AzureWebsiteSlot

El cmdlet **Switch-AzureWebsiteSlot** puede realizar una operación de cambio para que la ranura de implementación actualizada se convierta en el sitio de producción, como en el ejemplo siguiente. El sitio de producción no experimentará tiempos de inactividad ni arranques en frío.

`Switch-AzureWebsiteSlot -Name siteslotstest`

* * *

### Remove-AzureWebsite

Si ya no necesita una ranura de implementación, se puede eliminar usando el cmdlet **Remove-AzureWebsite**, como en el ejemplo siguiente.

`Remove-AzureWebsite -Name siteslotstest -Slot staging`

* * *

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## Comandos de la interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) para ranuras de sitio

La interfaz de la línea de comandos de Azure de varias plataformas (xplat-cli) proporciona comandos de varias plataformas para trabajar con Azure, incluyendo la compatibilidad para administrar ranuras de implementación en Sitios web Azure.

* Para obtener instrucciones acerca de cómo instalar y configurar la xplat-cli, incluyendo la información acerca de cómo conectar la xplat-cli a su suscripción de Azure, consulte [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure][2].

* Para mostrar los comandos disponibles para Sitios web Azure en la xplat-cli, llame a `azure site -h`.

* * *

### Lista de sitios de azure
Para obtener información acerca de los sitios web de Azure de la suscripción actual, llame a **azure site list**, como en el ejemplo siguiente.

`azure site list siteslotstest`

* * *

### Creación de un sitio de azure
Para crear una ranura de sitio para cualquier sitio web en modo estándar, llame a **azure site create** y especifique el nombre del sitio existente y el de la ranura que se vaya a crear, como en el ejemplo siguiente.

`azure site create siteslotstest --slot staging`

Para habilitar el control del origen en la nueva ranura, use la opción **--git**, como en el ejemplo siguiente.

`azure site create –-git siteslotstest --slot staging`

* * *

### Cambio del sitio de azure
Para hacer que la ranura de implementación actualizada se convierta en el sitio de producción, utilice el comando **azure site swap** para realizar una operación de cambio, como en el
ejemplo siguiente. El sitio de producción no experimentará tiempos de inactividad ni arranques en frío.

`azure site swap siteslotstest`

* * *

### Eliminación de un sitio de azure Para
eliminar una ranura de implementación que ya no sea necesaria, utilice el comando **azure site delete**, como en el ejemplo siguiente.

`azure site delete siteslotstest --slot staging`

* * *

<!-- IMAGES -->



[1]: http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell
[2]: http://www.windowsazure.com/es-es/documentation/articles/xplat-cli