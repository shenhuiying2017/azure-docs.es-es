<properties 
   pageTitle="Configuración del equilibrio de conmutación por error" 
   description="Este artículo le ayudará a configurar el equilibrio de carga de conmutación por error del Administrador de tráfico"
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

# Configuración del equilibrio de conmutación por error

En ocasiones, una organización desea proporcionar confiabilidad en sus servicios. Puede hacerlo mediante servicios de copia de seguridad en caso de que el servicio principal esté desactivado. Un patrón habitual de la conmutación por error del servicio es que proporciona un conjunto de servicios idénticos y que envía el tráfico a un servicio principal, mientras mantiene una lista configurada de uno o más servicios de copia de seguridad. Puede configurar este tipo de copia de seguridad con servicios en la nube y sitios web de Azure siguiendo los procedimientos que se indican a continuación.

Tenga en cuenta que Sitios web de Azure ya proporciona la funcionalidad de equilibrio de carga de conmutación por error para sitios web en un centro de datos (también conocido como región), independientemente del modo del sitio web. El Administrador de tráfico permite especificar el equilibrio de carga de conmutación por error para sitios web en distintos centros de datos.

## Para configurar el equilibrio de carga de conmutación por error:

1. En el Portal de administración, en el panel izquierdo, haga clic en el icono **Administrador de tráfico** para abrir el panel del Administrador de tráfico. Si aún no ha creado su perfil de Administrador de tráfico, consulte [Administración de perfiles del Administrador de tráfico](traffic-manager-manage-profiles.md) para conocer el procedimiento de creación de un perfil básico del Administrador de tráfico.
2. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
3. En la página del perfil, haga clic en **Extremos**, en la parte superior, y compruebe que estén presentes los servicios en la nube y sitios web (extremos) que desee incluir en la configuración. Para que conocer el procedimiento para agregar o quitar extremos, consulte [Administración de extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
4. En la página del perfil, haga clic en **Configurar**, en la parte superior, para abrir la página de configuración.
5. Para la **Configuración del método de equilibrio de carga**, compruebe que dicho método sea **Conmutación por error**. De no ser así, haga clic en **Conmutación por error** en la lista desplegable.
6. En **Lista de prioridad de conmutación por error**, ajuste el orden de la conmutación por error de los extremos. Cuando seleccione el método de equilibrio de carga **Conmutación por error**, el orden de los extremos seleccionados es importante. El extremo principal se encuentra en primer lugar. Utilice las flechas hacia arriba y hacia abajo para cambiar el orden según lo desee. Para obtener más información sobre cómo establecer la prioridad de conmutación por error con Windows PowerShell, consulte [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Compruebe que la **Configuración de supervisión** sea correcta. La supervisión garantiza que no se envíe tráfico a los extremos sin conexión. Para supervisar los extremos, debe especificar una ruta de acceso y un nombre de archivo. Tenga en cuenta que una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado). Para obtener más información acerca de la supervisión, consulte [Supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
8. Una vez que haya terminado de cambiar la configuración, haga clic en **Guardar** en la parte inferior de la página.
9. Pruebe los cambios de la configuración. Consulte [Comprobación de la configuración del Administrador de tráfico](traffic-manager-testing-settings.md)para obtener más información.
10. Una vez que el perfil del Administrador de tráfico se haya configurado y esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico. Para obtener más información acerca del procedimiento, consulte [Selección de un dominio de la compañía en Internet para un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).

## Otras referencias

[Acerca de los métodos de equilibrio de carga del Administrador de tráfico](traffic-manager-load-balancing-methods.md)

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Información general sobre Administrador de tráfico](../traffic-manmager-overview.md)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets del Administrador de recursos de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769):

<!--HONumber=49--> 