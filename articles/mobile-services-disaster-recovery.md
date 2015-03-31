<properties 
	pageTitle="Recuperación del servicio móvil en caso de un desastre - Servicios móviles de Azure" 
	description="Obtenga información acerca de cómo recuperar su servicio móvil en caso de desastre." 
	services="mobile-services" 
	documentationCenter="" 
	authors="brettsam" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="brettsam"/>

# Recuperación del servicio móvil en caso de un desastre

Cuando utiliza los Servicios móviles de Azure para implementar una aplicación, puede utilizar las funciones integradas para garantizar la continuidad empresarial en caso de problemas de disponibilidad como, por ejemplo, errores de servidor, interrupciones de red, pérdida de datos y pérdida generalizada en instalaciones. Al implementar la aplicación mediante los Servicios móviles de Azure, saca partido de las múltiples capacidades de infraestructura y tolerancia a errores que tendría que diseñar, implementar y administrar si tuviera que implementar una solución tradicional local. Azure mitiga una gran cantidad de posibles errores a un coste reducido.

<h2><a name="prepare"></a>Preparación ante posibles desastres</h2>

Para facilitar la recuperación en caso de un problema de disponibilidad, puede prepararse por adelantado: 

+ **Copia de seguridad de los datos de la base de datos SQL del servicio móvil de Azure**
	<br/>Los datos de aplicación de servicio móvil se almacenan en una Base de datos SQL de Azure. Le recomendamos que haga una copia de seguridad tal como se indica en [Continuidad empresarial de Base de datos SQL de Azure][Guía sobre continuidad empresarial de Base de datos SQL de Azure].
+ **Copia de seguridad de los scripts del servicio móvil**
	<br/>Se recomienda que almacene las secuencias de comandos de servicio móvil en un sistema de control de código fuente como [Team Foundation Service] o [GitHub] y no confiar únicamente en las copias en el propio servicio móvil. Puede descargar los scripts en el portal de Azure, con la [característica de control de código fuente] de los Servicios móviles o con la [herramienta de línea de comandos de Azure]. Preste atención a las características etiquetadas como "vista previa" en el portal, dado que la recuperación para dichos scripts no está garantizada y es posible que tenga que recuperarlos desde su propio original de control de código fuente.
+ **Reserva de un servicio móvil secundario**
	<br/>Si se produce un problema de disponibilidad con el servicio móvil, tendrá que volver a implementarlo en otra región de Azure. Para garantizar que la capacidad está disponible (por ejemplo, en aquellas circunstancias raras como la pérdida de una región completa), le recomendamos crear un servicio móvil secundario en una región alternativa y establecer el modo en un nivel igual o superior al modo de su servicio principal. (Si el servicio principal se encuentra en modo compartido, puede hacer que el servicio secundario sea compartido o reservado. Pero si el principal es reservado, el secundario también debe ser reservado).


<h2><a name="watch"></a>Inspección de indicios de problemas</h2>

Estas circunstancias indican un problema que podría necesitar una operación de recuperación:

+ Las aplicaciones que están conectadas a su servicio móvil no pueden comunicarse con él durante un prolongado período de tiempo.
+ El estado del servicio móvil se muestra como **Incorrecto** en el [portal de Azure].
+ Se muestra un banner **Incorrecto** en la parte superior de cada pestaña del servicio móvil en el portal de Azure y las operaciones de administración originan mensajes de error.
+ El [Panel de servicios de Azure] indica un problema de disponibilidad.

<h2><a name="recover"></a>Recuperación de un desastre</h2>

Cuando surja un problema, utilice el Panel de servicios para obtener ayuda y actualizaciones.
 
Si se lo indica el Panel de servicios, ejecute los pasos siguientes para restaurar el servicio móvil a un estado de ejecución en otra región de Azure. Si ya ha creado un servicio secundario, su capacidad se utilizará para restaurar el servicio principal. Como la dirección URL y la clave de aplicación del servicio principal no se han cambiado después de la recuperación, no tiene que actualizar ninguna aplicación que haga referencia al servicio. 

Para recuperar el servicio móvil después de una interrupción del servicio:

1. En el portal de Azure, asegúrese de que el estado del servicio se indica como **Incorrecto**.

2. Si ya ha reservado un servicio móvil secundario, puede omitir este paso.

   Si todavía no ha reservado un servicio móvil secundario, cree uno ahora en otra región de Azure. Establezca su modo en un nivel igual o superior al modo de su servicio principal. (Si el servicio principal se encuentra en modo compartido, puede hacer que el servicio secundario sea compartido o reservado. Pero si el principal es reservado, el secundario también debe ser reservado).

3. Configure las herramientas de línea de comandos de Azure para que actúen con la suscripción, tal como se describe en [Automatización de servicios móviles con herramientas de línea de comandos].

4. Ahora puede utilizar el servicio secundario para recuperar el principal.

    > [AZURE.IMPORTANT] Cuando ejecuta el comando en este paso, el servicio secundario se elimina, de tal forma que su capacidad se puede utilizar para recuperar el servicio principal. Le recomendamos que haga una copia de seguridad de los scripts y configuraciones antes de ejecutar el comando, si desea conservarlos.
    
   Cuando esté preparado, ejecute el comando siguiente:

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK


	> [AZURE.NOTE] Pueden pasar unos minutos después de la finalización del comando hasta que pueda ver los cambios en el portal.

5. Compruebe que todos los scripts se han recuperado correctamente comparándolos con los originales en el control de código fuente. En la mayoría de los casos, los scripts se recuperan automáticamente sin pérdida de datos, pero si encuentra una discrepancia, puede recuperar dicho script manualmente.

6. Asegúrese de que el servicio recuperado está comunicándose con la Base de datos SQL de Azure. El comando de recuperación recupera el servicio móvil, pero conserva la conexión con la base de datos original. Si el problema de la región principal de Azure también afecta a la base de datos, es posible que el servicio recuperado no se esté ejecutando correctamente. Puede utilizar el Panel de servicios de Azure para examinar el estado de la base de datos de una región específica. Si la base de datos original no está funcionando, puede recuperarla:
	+ Recupere la Base de datos SQL de Azure en la región de Azure donde acaba de recuperar el servicio móvil, tal como se describe en la [Guía sobre continuidad empresarial de Base de datos SQL de Azure].
	+ En el portal de Azure, en la pestaña **"Configurar"** del servicio móvil, elija "Cambiar base de datos" y luego seleccione la base de datos recientemente recuperada.

Ahora debería encontrarse en un estado donde el servicio móvil se haya recuperado en una nueva región de Azure y esté aceptando tráfico de las aplicaciones de la Tienda usando su URL original.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Guía sobre continuidad empresarial de Base de datos SQL de Azure]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[característica de control de código fuente]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[herramienta de línea de comandos de Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[Portal de Azure]: http://manage.windowsazure.com/
[Panel de servicios de Azure]: http://www.windowsazure.com/support/service-dashboard/
[Automatización de servicios móviles con herramientas de línea de comandos]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!--HONumber=47-->
