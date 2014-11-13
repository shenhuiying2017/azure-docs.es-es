<properties urlDisplayName="How to scale" pageTitle="Escalado de un servicio en la nube - Azure" metaKeywords="Azure link resource, scaling cloud service" description="Vea c&oacute;mo escalar un servicio en la nube y recursos vinculados en Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Escalado de una aplicaci&oacute;n" authors="davidmu" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/21/2014" ms.author="davidmu" />

# Escalado de una aplicación

En la página Scale del Portal de administración de Azure, puede escalar su aplicación manualmente o puede establecer parámetros para escalarla automáticamente. Puede escalar aplicaciones que ejecutan roles web, roles de trabajo o máquinas virtuales. Para escalar una aplicación que ejecuta instancias de roles web o roles de trabajo, agregue o quite instancias de roles para acomodar la carga de trabajo.

Al escalar o reducir verticalmente una aplicación que ejecuta máquinas virtuales, no se crean nuevas máquinas ni se eliminan, sino que se activan o desactivan desde un conjunto de disponibilidad de máquinas que se crearon anteriormente. Puede especificar el escalado según el porcentaje promedio de uso de CPU o según la cantidad de mensajes en una cola.

Debe considerar la siguiente información antes de configurar el escalado para su aplicación:

-   Las máquinas virtuales que crea debe agregarlas a un conjunto de disponibilidad para escalar una aplicación que las utiliza. Las máquinas virtuales que agrega pueden inicialmente activarse o desactivarse, pero se activarán en una acción de escalado vertical y se desactivarán en una acción de reducción vertical. Para obtener más información sobre las máquinas virtuales y los conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales][Administración de la disponibilidad de las máquinas virtuales].
-   El uso de núcleos afecta el escalado. Las instancias de rol o las máquinas virtuales más grandes usan más núcleos. Solo puede escalar una aplicación dentro del límite de núcleos para su suscripción. Por ejemplo, si su suscripción tiene un límite de veinte núcleos y ejecuta una aplicación con dos máquinas virtuales de tamaño mediano (un total de cuatro núcleos), solo puede escalar verticalmente otras implementaciones de servicio en la nube en su suscripción con dieciséis núcleos. Todas las máquinas virtuales de un conjunto de disponibilidad que se usan al escalar una aplicación deben tener el mismo tamaño. Para obtener más información acerca del uso de núcleos y los tamaños de las máquinas, consulte [Virtual Machine and Cloud Service Sizes for Azure][Virtual Machine and Cloud Service Sizes for Azure].
-   Debe crear una cola y asociarla con un rol o conjunto de disponibilidad para poder escalar una aplicación según el umbral de un mensaje. Para obtener más información, consulte [Uso del servicio de almacenamiento en cola][Uso del servicio de almacenamiento en cola].
-   Puede escalar los recursos que están vinculados con su servicio en la nube. Para obtener más información acerca de los recursos de vinculación, consulte[Vinculación de un recurso a un servicio en la nube][Vinculación de un recurso a un servicio en la nube]
-   Para permitir una alta disponibilidad de la aplicación, debe asegurarse de que se implemente con dos o más instancias de rol o máquinas virtuales. Para obtener más información, consulte [Contratos de nivel de servicio][Contratos de nivel de servicio].

Puede realizar las siguientes acciones de escalado para un servicio en la nube:

-   [Escalado manual de una aplicación que ejecuta roles web o roles de trabajo][Escalado manual de una aplicación que ejecuta roles web o roles de trabajo]
-   [Escalado automático de una aplicación que ejecuta roles web, roles de trabajo o máquinas virtuales][Escalado automático de una aplicación que ejecuta roles web, roles de trabajo o máquinas virtuales]
-   [Escalado de recursos vinculados][Escalado de recursos vinculados]
-   [Programación del escalado de su aplicación][Programación del escalado de su aplicación]

## <span id="manualscale"></span></a>Escalado manual de una aplicación que ejecuta roles web o roles de trabajo

En la página Scale, puede aumentar o disminuir manualmente la cantidad de instancias en ejecución en un servicio en la nube.

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2.  Haga clic en **Scale**. El escalado automático está deshabitado de manera predeterminada para todos los roles, lo que significa que puede cambiar manualmente la cantidad de instancias que usa su aplicación.

    ![Página de escala][Página de escala]

3.  Cada rol en el servicio en la nube tiene un control deslizante para cambiar la cantidad de instancias que se van a usar. Para agregar una instancia de rol, arrastre la barra hacia la derecha. Para quitar una instancia, arrastre la barra hacia la izquierda.

    ![Rol de escalado][Rol de escalado]

    Solo puede aumentar la cantidad de instancias que se usan si la cantidad adecuada de núcleos está disponible para admitir las instancias. Los colores del control deslizante representan los núcleos usados y disponibles en su suscripción:

    -   Azul representa los núcleos usados por el rol seleccionado
    -   Gris representa los núcleos usados por todos los roles y las máquinas virtuales de la suscripción
    -   Gris claro representa los núcleos que están disponibles para usarlos en el escalado
    -   Rosado representa un cambio hecho que no se ha guardado

4.  Haga clic en **Save**. Las instancias de rol se agregaran o quitarán según las selecciones realizadas.

## <span id="autoscale"></span></a>Escalado automático de una aplicación que ejecuta roles web, roles de trabajo o máquinas virtuales

En la página Scale, puede configurar su servicio en la nube para aumentar o disminuir automáticamente la cantidad de instancias o máquinas virtuales que usa su aplicación. Puede configurar el escalado según los siguientes parámetros:

-   [Uso promedio de CPU][Uso promedio de CPU]: si el porcentaje promedio de uso de CPU está por encima o por debajo de los umbrales especificados, se crean o eliminan instancias de rol, o bien, las máquinas virtuales se activan o desactivan desde un conjunto de disponibilidad.
-   [Mensajes en cola][Mensajes en cola]: si la cantidad de mensajes en una cola está por encima o por debajo de un umbral específico, se crean o eliminan instancias de rol, o bien, las máquinas virtuales se activan o desactivan desde un conjunto de disponibilidad.

### <span id="averagecpu"></span></a>Uso promedio de CPU

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
2.  Haga clic en **Scale**.
3.  Desplácese hasta la sección correspondiente al rol o el conjunto de disponibilidad y, a continuación, haga clic en **CPU**. Esto permite el escalado automático de su aplicación, según el porcentaje promedio de recursos de CPU que utiliza.

    ![Escalado automático activado][Escalado automático activado]

4.  Cada rol o conjunto de disponibilidad tiene un control deslizante para cambiar la cantidad de instancias que se pueden usar. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la derecha hacia la derecha. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la izquierda hacia la izquierda.

    **Nota:** en la página Scale, **Instance** representa una instancia de rol o una instancia de una máquina virtual.

    ![Rango de instancias][Rango de instancias]

    La cantidad máxima de instancias está limitada por los núcleos que están disponibles en la suscripción. Los colores del control deslizante representan los núcleos usados y disponibles en su suscripción:

    -   Azul representa la cantidad máxima de núcleos que puede usar el rol.
    -   Gris oscuro representa los núcleos que usan todos los roles y las máquinas virtuales de la suscripción Cuando este valor coincide parcialmente con los núcleos usados por el rol, el color cambia a azul oscuro.
    -   Gris claro representa los núcleos que están disponibles para usarlos en el escalado.
    -   Rosado representa un cambio hecho que no se ha guardado.

5.  Se usa un control deslizante para especificar el rango de porcentaje promedio de uso de CPU. Cuando el porcentaje promedio de uso de CPU está por encima de la configuración máxima, se crean más instancias de rol o se activan más máquinas virtuales. Cuando el porcentaje promedio de uso de CPU está por debajo de la configuración mínima, se eliminan instancias de rol o se desactivan máquinas virtuales. Para establecer el porcentaje promedio máximo de CPU, arrastre la barra de la derecha hacia la derecha. Para establecer el porcentaje promedio mínimo de CPU, arrastre la barra de la izquierda hacia la izquierda.

    ![CPU objetivo][CPU objetivo]

6.  Puede especificar la cantidad de instancias para agregar o activar cada vez que su aplicación se escala verticalmente. Para aumentar la cantidad de instancias que se crean o activan cuando su aplicación se escala verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Escalado vertical de CPU][Escalado vertical de CPU]

7.  Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de escalado vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de escalado][Tiempo de escalado]

    Al calcular el porcentaje promedio de uso de CPU se incluyen todas las instancias; el promedio está basado en el uso durante la hora anterior. Según la cantidad de instancias que use su aplicación, la acción de escalado puede tardar más que el tiempo de espera especificado si el tiempo de espera se establece muy bajo. El tiempo mínimo entre las acciones de escalado es de cinco minutos. Las acciones de escalado no se pueden producir si alguna de las instancias se encuentra en estado de transición.

8.  Puede también especificar la cantidad de instancias que desea eliminar o desactivar cuando la aplicación se reduzca verticalmente. Para aumentar la cantidad de instancias que se eliminan o desactivan cuando su aplicación se reduce verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Reducción vertical de CPU][Reducción vertical de CPU]

    Si su aplicación puede tener aumentos repentinos en el uso de CPU, debe asegurarse de que tenga una cantidad de instancias mínima suficiente para administrarlas.

9.  Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de reducción vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de reducción][Tiempo de reducción]

10. Haga clic en **Save**. La acción de escalado puede tardar hasta cinco minutos en finalizar.

### <span id="queuemessages"></span></a>Mensajes en cola

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
2.  Haga clic en **Scale**.
3.  Desplácese hasta la sección correspondiente al rol o el conjunto de disponibilidad y, a continuación, haga clic en **Queue**. Esto permite el escalado automático de su aplicación según una cantidad objetivo de mensajes en cola.

    ![Cola de escalado][Cola de escalado]

4.  Cada rol o conjunto de disponibilidad en el servicio en la nube tiene un control deslizante para cambiar la cantidad de instancias que se pueden usar. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la derecha hacia la derecha. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la izquierda hacia la izquierda.

    ![Rango de cola][Rango de cola]

    **Nota:** en la página Scale, **Instance** representa una instancia de rol o una instancia de una máquina virtual.

    La cantidad máxima de instancias está limitada por los núcleos que están disponibles en la suscripción. Los colores del control deslizante representan los núcleos usados y disponibles en su suscripción:

    -   Azul representa la cantidad máxima de núcleos que puede usar el rol.
    -   Gris oscuro representa los núcleos que usan todos los roles y las máquinas virtuales de la suscripción Cuando este valor coincide parcialmente con los núcleos usados por el rol, el color cambia a azul oscuro.
    -   Gris claro representa los núcleos que están disponibles para usarlos en el escalado.
    -   Rosado representa un cambio hecho que no se ha guardado.

5.  Seleccione la cuenta de almacenamiento que se asocia con la cola que desea usar.

    ![Nombre de almacenamiento][Nombre de almacenamiento]

6.  Seleccione la cola.

    ![Nombre de cola][Nombre de cola]

7.  Especifique la cantidad de mensajes que espera que admita cada instancia. Las instancias escalan según la cantidad total de mensajes dividida por la cantidad objetivo de mensajes por máquina.

    ![Número de mensajes][Número de mensajes]

8.  Puede especificar la cantidad de instancias para agregar o activar cada vez que su aplicación se escala verticalmente. Para aumentar la cantidad de instancias que se agregan o activan cuando su aplicación se escala verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Escalado vertical de CPU][Escalado vertical de CPU]

9.  Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de escalado vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de escalado][Tiempo de escalado]

    El tiempo mínimo entre las acciones de escalado es de cinco minutos. Las acciones de escalado no se pueden producir si alguna de las instancias se encuentra en estado de transición.

10. Puede también especificar la cantidad de instancias que desea eliminar o no utilizar cuando la aplicación se reduzca verticalmente. Se usa un control deslizante para especificar el incremento del escalado. Para aumentar la cantidad de instancias que se eliminan o no se usan cuando su aplicación se reduce verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Reducción vertical de CPU][Reducción vertical de CPU]

11. Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de reducción vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de reducción][Tiempo de reducción]

12. Haga clic en **Save**. La acción de escalado puede tardar hasta cinco minutos en finalizar.

## <span id="scalelink"></span></a>Escalado de recursos vinculados

A menudo, cuando se escala un rol, es beneficioso también escalar la base de datos que la aplicación está usando. Si vincula la base de datos con el servicio en la nube, puede cambiar la edición de Base de datos SQL y cambiar el tamaño de la base de datos en la página Scale.

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
2.  Haga clic en **Scale**.
3.  En la sección Linked Resources, seleccione la edición que desea usar para la base de datos.

    ![Linked Resources][Linked Resources]

4.  Seleccione el tamaño de la base de datos.
5.  Haga clic en **Save** para actualizar los recursos vinculados.

## <span id="schedule"></span></a>Programación del escalado de su aplicación

Puede programar el escalado automático de su aplicación al configurar programas para diferentes horas. Las siguientes opciones están disponibles para el escalado automático:

-   **No schedule**: Esta es la opción predeterminada y permite que su aplicación se escale automáticamente de la misma manera todas las veces.

-   **Day and night**: Esta opción le permite especificar el escalado para horas específicas del día y la noche.

**Nota:** las programaciones no están actualmente disponibles para aplicaciones que usan máquinas virtuales.

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
2.  Haga clic en **Scale**.
3.  En la página Scale, haga clic en **set up schedule times**.

    ![Programar escalado][Programar escalado]

4.  Seleccione el tipo de programación de escalado que desea configurar.

5.  Especifique las horas de inicio y finalización del día y establezca la zona horaria. Para la programación del día y la noche, las horas representan el inicio y la finalización del día; el resto del tiempo representa la noche.

6.  Haga clic en la marca de verificación en la parte inferior de la página para guardar las programaciones.

7.  Después de guardar las programaciones, aparecerán en la lista. Puede seleccionar la programación de tiempo que desea usar y luego modificar su configuración de escalado. La configuración de escalado solo se aplicará durante la programación que seleccionó. Puede editar las programaciones al hacer clic en **set up schedule times**.

  [Administración de la disponibilidad de las máquinas virtuales]: http://www.windowsazure.com/es-es/manage/windows/common-tasks/manage-vm-availability/
  [Virtual Machine and Cloud Service Sizes for Azure]: http://msdn.microsoft.com/es-es/library/dn197896.aspx
  [Uso del servicio de almacenamiento en cola]: http://www.windowsazure.com/es-es/develop/net/how-to-guides/queue-service
  [Vinculación de un recurso a un servicio en la nube]: http://www.windowsazure.com/es-es/manage/services/cloud-services/how-to-manage-a-cloud-service/#linkresources
  [Contratos de nivel de servicio]: https://www.windowsazure.com/es-es/support/legal/sla/
  [Escalado manual de una aplicación que ejecuta roles web o roles de trabajo]: #manualscale
  [Escalado automático de una aplicación que ejecuta roles web, roles de trabajo o máquinas virtuales]: #autoscale
  [Escalado de recursos vinculados]: #scalelink
  [Programación del escalado de su aplicación]: #schedule
  [Portal de administración]: https://manage.windowsazure.com/
  [Página de escala]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
  [Rol de escalado]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
  [Uso promedio de CPU]: #averagecpu
  [Mensajes en cola]: #queuemessages
  [Escalado automático activado]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
  [Rango de instancias]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
  [CPU objetivo]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
  [Escalado vertical de CPU]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
  [Tiempo de escalado]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
  [Reducción vertical de CPU]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
  [Tiempo de reducción]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
  [Cola de escalado]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
  [Rango de cola]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
  [Nombre de almacenamiento]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
  [Nombre de cola]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
  [Número de mensajes]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
  [Linked Resources]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
  [Programar escalado]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png
