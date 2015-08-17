<properties 
   pageTitle="Configuracion del equilibrio de carga de rendimiento"
   description="Este artículo le ayudará a configurar el equilibrio de carga de rendimiento del Administrador de tráfico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Configuracion del equilibrio de carga de rendimiento

Al objeto de equilibrar la carga de los servicios en la nube y los sitios web (extremos) que se encuentran en distintos centros de datos de todo el mundo (también conocidos como regiones), puede dirigir el tráfico entrante al extremo con la latencia más baja desde el cliente que realiza la solicitud. Normalmente, el centro de datos con la latencia más baja corresponde a la distancia geográfica más cercana. El método de equilibrio de carga de rendimiento le permitirá hacer la distribución en función de la latencia más baja, pero no tiene en cuenta los cambios en tiempo real en la configuración de la red o en la carga. Para obtener más información acerca de los distintos métodos de equilibrio de carga del Administrador de tráfico de Azure, consulte [Acerca de los métodos de equilibrio de carga del Administrador de tráfico](traffic-manager-load-balancing-methods.md).

## Equilibre la carga del tráfico en función de la latencia más baja en un conjunto de extremos:

1. En el Portal de administración, en el panel izquierdo, haga clic en el icono **Administrador de tráfico** para abrir el panel del Administrador de tráfico. Si aún no ha creado su perfil de Administrador de tráfico, consulte [Administración de perfiles del Administrador de tráfico](traffic-manager-manage-profiles.md) para conocer el procedimiento de creación de un perfil básico del Administrador de tráfico.
2. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
3. En la página del perfil,haga clic en **Extremos** y compruebe que están presentes los extremos del servicio que desea incluir en la configuración. Para saber cómo agregar o quitar extremos del perfil, consulte [Administración de extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
4. En la página del perfil, haga clic en **Configurar** en la parte superior, para abrir la página de configuración.
5. En la **Configuración del método de equilibrio de carga**, compruebe que dicho método sea **Rendimiento*. Si no es así, haga clic en **Rendimiento** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** sea correcta. La supervisión garantiza que no se envíe tráfico a los extremos sin conexión. Para supervisar los extremos, debe especificar una ruta de acceso y un nombre de archivo. Tenga en cuenta que una barra diagonal “/“ es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado). Para obtener más información acerca de la supervisión, consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
7. Una vez que haya terminado de cambiar la configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Pruebe los cambios de la configuración. Para obtener más información, consulte [Comprobación de la configuración del Administrador de tráfico](traffic-manager-testing-settings.md).
9. Una vez que el perfil del Administrador de tráfico se haya configurado y esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico. Para obtener más información acerca del procedimiento, consulte [Seleccionar un dominio de la compañía en Internet para un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).

## Otras referencias

[Acerca de los métodos de equilibrio de carga del Administrador de tráfico](traffic-manager-load-balancing-methods.md)

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Información general sobre el Administrador de tráfico](../traffic-manmager-overview.md)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

 

<!---HONumber=August15_HO6-->