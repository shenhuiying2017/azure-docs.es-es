<properties 
   pageTitle="Administración de extremos en el Administrador de tráfico"
   description="Este artículo le ayudará a agregar, quitar, habilitar y deshabilitar extremos del Administrador de tráfico."
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Adición, deshabilitación, habilitación o eliminación de extremos

Sitios web de Azure ya proporcionan la funcionalidad de equilibrio de carga de conmutación por error y de round robin para sitios web en un centro de datos, independientemente del modo del sitio web. El Administrador de tráfico permite especificar el equilibrio de carga de conmutación por error y de round robin para sitios web y servicios en la nube en distintos centros de datos. El primer paso necesario para proporcionar esa funcionalidad es agregar el extremo del sitio web o del servicio en la nube al Administrador de tráfico.

>[AZURE.NOTE] No se pueden agregar ubicaciones externas o perfiles del Administrador de tráfico como extremos mediante el uso del Portal de administración. Debe utilizar la API de REST [Creación de definición](http://go.microsoft.com/fwlink/p/?LinkId=400772) o Windows PowerShell [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

También puede deshabilitar los extremos individuales que forman parte de un perfil del Administrador de tráfico. Los extremos incluyen servicios en la nube y sitios web. Al deshabilitar un extremo, se deja como parte del perfil, pero el perfil actúa como si el extremo no estuviera incluido en él. Esta acción es muy útil para quitar temporalmente un extremo que se encuentre en modo de mantenimiento o que se vaya a implementar. Cuando el extremo vuelva a estar en funcionamiento, se puede habilitar

>[AZURE.NOTE] Deshabilitar un extremo no tiene nada que ver con su estado de implementación en Azure. Seguirá activo un extremo correcto que podrá recibir tráfico incluso cuando se deshabilite en el Administrador de tráfico. Además, al deshabilitar un extremo de un perfil, no se afecta al estado en otro perfil.

## Para agregar un extremo de servicio en la nube o sitio web


1. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración del extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que forman parte de la configuración.
3. En la parte inferior de la página, haga clic en **Agregar** para obtener acceso a la página **Agregar extremos del servicio**. De manera predeterminada, la página muestra los servicios en la nube en **Extremos del servicio**.
4. Para los servicios en la nube, seleccione los servicios en la lista para habilitarlos como extremos de este perfil. Si borra el nombre del servicio en la nube, este se eliminará de la lista de extremos.
5. Para los sitios web, haga clic en la lista desplegable **Tipo de servicio** y, a continuación, seleccione **Sitio web**.
6. Seleccione los sitios web de la lista para agregarlos como extremos para este perfil. Si borra el nombre del servicio en la nube, este se eliminará de la lista de extremos. Tenga en cuenta que solo puede seleccionar un único sitio web por centro de datos de Azure (también conocido como región). Si selecciona un sitio web en un centro de datos que hospeda varios sitios web, al seleccionar el primer sitio web, el resto de sitios del mismo centro de datos no se podrá seleccionar. Tenga en cuenta, asimismo, que solo se enumeran sitios web estándar.
7. Una vez seleccionados los extremos de este perfil, haga clic en la marca de verificación, situada abajo a la derecha, para guardar los cambios.

>[AZURE.NOTE] Si va a utilizar el *Failover* método de equilibrio de carga, después de agregar o de quitar un extremo, asegúrese de ajustar la Lista de prioridad de conmutación por error, en la página Configuración, para que refleje el orden de conmutación por error que desee especificar. Para obtener más información, consulte [Configuración del equilibrio de carga de conmutación por error](traffic-manager-configure-failover-load-balancing.md).

## Para deshabilitar un extremo

1. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración del extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que se incluyen en la configuración. 
3. Haga clic en el extremo que desea deshabilitar y, a continuación, haga clic en **Deshabilitar** en la parte inferior de la página.
4. El tráfico dejará de fluir hacia el extremo basado en el período de vida (TTL) de DNS configurado para el nombre de dominio del Administrador de tráfico. Puede cambiar el valor de TTL desde la página de configuración del perfil del Administrador de tráfico.

## Para habilitar un extremo

1. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración del extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que se incluyen en la configuración.
3. Haga clic en el extremo que desea habilitar y, a continuación, haga clic en **Habilitar** en la parte inferior de la página.
4. El tráfico comenzará a fluir al servicio de nuevo como indica el perfil.

## Para eliminar un extremo del servicio en la nube o del sitio web


1. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración del extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que forman parte de la configuración.
3. En la página Extremos, haga clic en el nombre del extremo que desea eliminar del perfil.
4. En la parte inferior de la página, haga clic en **Eliminar**.

>[AZURE.NOTE] No se pueden eliminar ubicaciones externas o perfiles del Administrador de tráfico como extremos mediante el uso del Portal de administración. Debe usar Windows PowerShell. Para obtener más información, consulte [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Otras referencias

[Información general sobre Administrador de tráfico](traffic-manager-overview.md)

[Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md)

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

<!--HONumber=49-->