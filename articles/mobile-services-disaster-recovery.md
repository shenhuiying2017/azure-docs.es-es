<properties linkid="mobile-services-recovery-disaster" urlDisplayName="Recover your mobile service in the event of a disaster" pageTitle="Recover your mobile service in the event of a disaster - Azure Mobile Services" metaKeywords="" description="Learn how to recover your mobile service in the event of a disaster." metaCanonical="" services="" documentationCenter="Mobile" title="Recover your mobile service in the event of a disaster" authors="yavorg" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg" />

# Recuperación del servicio móvil en caso de un desastre

Cuando utiliza los Servicios móviles de Azure para implementar una aplicación, puede utilizar las funciones integradas para garantizar la continuidad de negocio en caso de problemas de disponibilidad, como errores de servidor, interrupciones de red, pérdida de datos y amplias pérdidas en instalaciones. Al implementar la aplicación mediante los Servicios móviles de Azure, saca partido de las múltiples capacidades de infraestructura y tolerancia a errores que tendría que diseñar, implementar y administrar si tuviera que implementar una solución tradicional local. Azure mitiga una gran cantidad de posibles errores a un coste reducido.

## <a name="prepare"></a><span class="short-header">Preparación</span>Preparación ante posibles desastres

Para facilitar la recuperación en caso de un problema de disponibilidad, puede prepararse por adelantado:

-   **Haga una copia de seguridad de los datos en la base de datos SQL de Servicios móviles de Azure**
    Los datos de la aplicación de Servicios móviles se almacenan en una Base de datos SQL de Azure. Le recomendamos que haga una copia de seguridad tal como se indica en [Continuidad de negocio de Base de datos SQL de Azure][Continuidad de negocio de Base de datos SQL de Azure].
-   **Haga una copia de seguridad de los scripts de servicios móviles**
    Recomendamos que almacene los scripts de los servicios móviles en un sistema de control de código fuente como [Team Foundation Service][Team Foundation Service] o [GitHub] y no depender únicamente de las copias del propio servicio móvil. Puede descargar los scripts en el portal de Azure, con la [característica de control de código fuente][característica de control de código fuente] de Servicios móviles o [mediante la herramienta de línea de comandos de Azure][mediante la herramienta de línea de comandos de Azure]. Preste atención a las características etiquetadas como "vista previa" en el portal, dado que la recuperación para dichos scripts no está garantizada y es posible que tenga que recuperarlos desde su propio original de control de código fuente.
-   **Reserve un servicio móvil secundario**
    En caso de que surja algún problema de disponibilidad con el servicio móvil, es posible que tenga que volver a implementarlo en otra región de Azure. Para garantizar que la capacidad está disponible (por ejemplo, en aquellas circunstancias raras como la pérdida de una región completa), le recomendamos crear un servicio móvil secundario en una región alternativa y establecer el modo en un nivel igual o superior al modo de su servicio principal. (Si el servicio principal se encuentra en modo compartido, puede hacer que el servicio secundario sea compartido o reservado. Pero si el principal es reservado, el secundario también debe ser reservado).

## <a name="watch"></a><span class="short-header">Inspección</span>Inspección de indicios de problemas

Estas circunstancias indican un problema que podría necesitar una operación de recuperación:

-   Las aplicaciones que están conectadas a su servicio móvil no pueden comunicarse con él durante un prolongado período de tiempo.
-   El estado del servicio móvil se muestra como **Incorrecto** en el [Portal de Azure][Portal de Azure].
-   Se muestra una pancarta con el nombre **Incorrecto** en la parte superior de cada pestaña del servicio móvil en el portal Azure y las operaciones de administración originan mensajes de error.
-   El [Panel de servicios de Azure][Panel de servicios de Azure] indica un problema de disponibilidad.

## <a name="recover"></a><span class="short-header">Recuperación</span>Recuperación de un desastre

Cuando surja un problema, utilice el Panel de servicios para obtener ayuda y actualizaciones.

Si se lo indica el Panel de servicios, ejecute los pasos siguientes para restaurar el servicio móvil en un estado de ejecución en una región de Azure alternativa. Si ya ha creado un servicio secundario, su capacidad se utilizará para restaurar el servicio principal. Como la dirección URL y la clave de aplicación del servicio principal no se han cambiado después de la recuperación, no tiene que actualizar ninguna aplicación que haga referencia al servicio.

Para recuperar el servicio móvil después de una interrupción del servicio:

1.  En el portal de Azure, asegúrese de que el estado del servicio se indica como **Incorrecto**.

2.  Si ya ha reservado un servicio móvil secundario, puede omitir este paso.

   Si todavía no ha reservado un servicio móvil secundario, cree uno ahora en otra región de Azure. Establezca su modo en un nivel igual o superior al modo de su servicio principal. (Si el servicio principal se encuentra en modo compartido, puede hacer que el servicio secundario sea compartido o reservado. Pero si el principal es reservado, el secundario también debe ser reservado).

1.  Configure las herramientas de línea de comandos de Azure para que actúen con la suscripción, tal como se describe en [Automatización de servicios móviles con herramientas de línea de comandos][mediante la herramienta de línea de comandos de Azure].

2.  Ahora puede utilizar el servicio secundario para recuperar el principal.

    <div class="dev-callout">

    <b>Importante</b><p>Cuando ejecuta el comando en este paso, el servicio secundario se elimina, de tal forma que su capacidad se puede utilizar para recuperar el servicio principal. Le recomendamos que haga una copia de seguridad de los scripts y configuraciones antes de ejecutar el comando, si desea conservarlos.</p>
    </div>

   Cuando esté preparado, ejecute el comando siguiente:

        azure mobile recover PrimaryService SecondaryService
        info:    Executing command mobile recover
        Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
        + Performing recovery
        + Cleaning up
        info:    Recovery complete
        info:    mobile recover command OK


    <div class="dev-callout"><b>Note</b>
    <p>It may take a few minutes after the command completes until you can see the changes in the portal.</p>
    </div>

1.  Compruebe que todos los scripts se han recuperado correctamente comparándolos con los originales en el control de código fuente. En la mayoría de los casos, los scripts se recuperan automáticamente sin pérdida de datos, pero si encuentra una discrepancia, puede recuperar dicho script manualmente.

2.  Asegúrese de que el servicio recuperado está comunicándose con la Base de datos SQL de Azure. El comando de recuperación recupera el servicio móvil, pero conserva la conexión con la base de datos original. Si el problema de la región principal de Azure también afecta a la base de datos, es posible que el servicio recuperado no se esté ejecutando correctamente. Puede utilizar el Panel de servicios de Azure para examinar el estado de la base de datos de una región específica. Si la base de datos original no está funcionando, puede recuperarla:

    -   Recupere la Base de datos SQL de Azure en la región de Azure donde acaba de recuperar el servicio móvil, tal como se describe en la guía[Continuidad de negocio de Base de datos SQL de Azure][Continuidad de negocio de Base de datos SQL de Azure].
    -   En el portal de Azure, en la pestaña **"Configurar"** del servicio móvil, elija "Cambiar base de datos" y, a continuación, seleccione la base de datos recientemente recuperada.

Ahora debería encontrarse en un estado donde el servicio móvil se haya recuperado en una nueva región de Azure y esté aceptando tráfico de las aplicaciones de la Tienda usando su URL original.





  [Continuidad de negocio de Base de datos SQL de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh852669.aspx
  [Team Foundation Service]: http://tfs.visualstudio.com/
  [característica de control de código fuente]: http://www.windowsazure.com/es-es/develop/mobile/tutorials/store-scripts-in-source-control/
  [mediante la herramienta de línea de comandos de Azure]: http://www.windowsazure.com/es-es/develop/mobile/tutorials/command-line-administration/
  [Portal de Azure]: http://manage.windowsazure.com/
  [Panel de servicios de Azure]: http://www.windowsazure.com/es-es/support/service-dashboard/
