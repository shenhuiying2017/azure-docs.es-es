<properties
   pageTitle="Administrar perfiles del Administrador de tráfico"
   description="Este artículo le ayudará a crear, deshabilitar, habilitar, eliminar y ver el historial de un perfil del Administrador de tráfico."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma" />

# Administrar un perfil del Administrador de tráfico

Use un perfil del Administrador de tráfico para especificar qué extremos de servicios en la nube o de sitio web supervisará el Administrador de tráfico, y qué método de equilibrio de carga quiere usar para distribuir las conexiones en esos extremos.

## Crear un perfil del Administrador de tráfico mediante Creación rápida

Puede crear rápidamente un perfil de Administrador de tráfico mediante **Creación rápida** en el Portal de administración. Creación rápida permite crear perfiles con valores de configuración básicos. Sin embargo, no puede usar Creación rápida para valores como, por ejemplo, el conjunto de extremos (servicios en la nube y sitios web), el orden de conmutación por error para el método de equilibrio de carga de conmutación por error o la configuración de supervisión. Después de crear el perfil, puede configurar estas opciones en el Portal de administración. El Administrador de tráfico admite un máximo de 200 extremos por perfil. Sin embargo, la mayoría de los escenarios de uso tan solo requieren un pequeño número de extremos. Consulte [Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx) para obtener una lista de procedimientos de configuración.

### Para crear un nuevo perfil del Administrador de tráfico

1. **Implemente en el entorno de producción sus servicios en la nube y sitios web.** Para obtener más información sobre los servicios en la nube, consulte [Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obtener más información sobre los servicios en la nube, consulte [Procedimientos recomendados](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Para obtener más información sobre sitios web, consulte [Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Inicie sesión en el Portal de administración.** Para crear un nuevo perfil de Administrador de tráfico, haga clic en **Nuevo** en la esquina inferior izquierda del portal, a continuación, haga clic en **Servicios de red**, en **Administrador de tráfico** y en **Creación rápida** para empezar a configurar el perfil.
3. **Configure el prefijo DNS.** Proporcione a su perfil de Administrador de tráfico un nombre de prefijo DNS único. Puede especificar solo el prefijo de un nombre de dominio de Administrador de tráfico.
4. **Seleccione la suscripción.** Seleccione la suscripción de Azure apropiada. Cada perfil está asociado a una sola suscripción. Si sólo tiene una suscripción, esta opción no aparecerá.
5. **Seleccione el método de equilibrio de carga.** Seleccione el método de equilibrio de carga en **Directiva de equilibrio de carga**. Para obtener información sobre métodos de equilibrio de carga, consulte [Acerca de los métodos de equilibrio de carga del Administrador de tráfico](traffic-manager-load-balancing-methods.md).
6. **Haga clic en “Crear” para crear un nuevo perfil**. Cuando haya finalizado la configuración del perfil, puede buscar el perfil en el panel Administrador de tráfico del Portal de administración.
7. **Configure los extremos, la supervisión y la configuración adicional en el Portal de administración.** Dado que sólo se pueden configurar valores básicos mediante la Creación rápida, es necesario configurar opciones adicionales tales como la lista de extremos y el orden de conmutación por error del extremo, con el fin de completar la configuración deseada. Consulte [Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx) para obtener una lista de procedimientos que le ayudarán a completar la configuración.

## Deshabilitar, habilitar o eliminar un perfil

Puede deshabilitar un perfil de Administrador de tráfico existente de para que no envíe solicitudes de usuario a sus extremos configurados. Cuando deshabilite un perfil de Administrador de tráfico, dicho perfil y la información que contiene se mantendrán intactos y pueden editarse en la interfaz del Administrador de tráfico. Si desea volver a habilitar el perfil, puede hacerlo fácilmente en el Portal de administración y los envíos se reanudarán. Cuando se crea un perfil de Administrador de tráfico en el Portal de administración, se habilita automáticamente. Si decide que un perfil ya no será necesario, puede eliminarlo.

### Para deshabilitar un perfil

1. Modifique el registro de recursos DNS en el servidor DNS de Internet para usar el tipo de registro adecuado y un puntero a otro nombre o a la dirección IP de una ubicación específica en Internet. En otras palabras, debe cambiar el registro de recursos DNS del servidor DNS de Internet para que ya no utilice un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. El tráfico dejará de dirigirse a los extremos a través de la configuración del perfil del Administrador de tráfico.
3. Seleccione el perfil que desea deshabilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en **Deshabilitar** en la parte inferior de la página.

### Para habilitar un perfil

1. Seleccione el perfil que desea habilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
2. Después de seleccionar el perfil, haga clic en **Habilitar** en la parte inferior de la página.
3. Modifique el registro de recursos DNS del servidor DNS de Internet para que use el tipo de registro CNAME, que asigna el nombre de dominio de la empresa al nombre de dominio del perfil del Administrador de tráfico. Para obtener más información, consulte [Seleccionar un dominio de Internet de la compañía para un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).
4. El tráfico comenzará a dirigirse de nuevo a los extremos.

### Para eliminar un perfil

1. Asegúrese de que el registro de recursos DNS del servidor DNS de Internet ya no usa un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. Seleccione el perfil que desea eliminar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en **Eliminar** en la parte inferior de la página.

## Ver el historial de cambios de perfiles del Administrador de tráfico

Puede ver el historial de cambios del perfil del Administrador de tráfico en el Portal de administración, en Servicios de administración.

### Para ver el historial de cambios del Administrador de tráfico

1. En el panel izquierdo del Portal de administración, haga clic en **Servicios de administración**.
2. En la página Servicios de administración, haga clic en **Registros de operaciones**.
3. En la página Registros de operaciones puede usar los filtros para ver el historial de cambios del perfil del Administrador de tráfico. Después de seleccionar las opciones de filtrado, haga clic en la marca de verificación para ver los resultados.
   - Para ver los cambios de todos los perfiles, seleccione la suscripción, un intervalo de tiempo y, a continuación, elija **Administrador de tráfico** en la lista desplegable **Tipo**.
   - Para filtrar por nombre de perfil, escriba el nombre del perfil en el campo **Nombre del servicio** o selecciónelo en la lista desplegable.
   - Para ver los detalles de cada cambio individual, seleccione la fila que contiene el cambio que desea ver y haga clic en **Detalles** en la parte inferior de la página. En la ventana **Detalles de la operación**, puede ver la representación XML del objeto de API que se creó o actualizó como parte de la operación y copiar el código XML en el Portapapeles.


## Otras referencias

[Información general sobre el Administrador de tráfico](traffic-manager-overview.md)

[Administrar extremos en el Administrador de tráfico](traffic-manager-endpoints.md)

[Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md)

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)
 

<!---HONumber=July15_HO2-->