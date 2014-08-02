<properties linkid="manage-scenarios-how-to-scale-websites" urlDisplayName="How to scale" pageTitle="How to scale web sites - Microsoft Azure service management" metaKeywords="Azure scaling web sites" description="Learn how to scale web sites in Azure to use Free, Shared, Basic and Standard web hosting plans." metaCanonical="" services="web-sites" documentationCenter="" title="How to Scale Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Escalación de sitios web
========================

Para conseguir un mejor rendimiento en los sitios web de Microsoft Azure, puede usar el Portal de administración de Azure para escalar el modo del plan de hospedaje web de libre a compartido, básico o estándar.

La escalación de sitios web de Azure implica dos cosas: el cambio del modo del plan de hospedaje web a un mayor nivel de servicio y la configuración de determinados valores una vez que haya cambiado a un mayor nivel de servicio. En este artículo se incluyen ambos temas. Los niveles de servicio más altos como el modo estándar ofrecen una mayor robustez y flexibilidad en la determinación de cómo se usan los recursos en Azure.

El cambio de modos y la configuración de estos se realiza fácilmente en la pestaña Scale del portal de administración. Puede escalar o reducir verticalmente según sea necesario. Estos cambios tardan solo segundos en aplicarse y afectan a todos los sitios web en el plan de hospedaje web. No requieren que el código se cambie o que las aplicaciones tengan que volver a implementarse.

Para obtener información sobre los planes de hospedaje web, consulte [Azure Web Sites Web Hosting Plans](http://go.microsoft.com/fwlink/?LinkId=9845584). Para obtener información sobre el precio y las características de los planes de hospedaje web individuales, consulte [Detalles de precios de Sitios web](http://www.windowsazure.com/en-us/pricing/details/web-sites/).

> [WACOM.NOTE] Antes de cambiar un sitio web del modo de plan de hospedaje web **libre** al modo de plan de hospedaje web **básico** o **estándar**, primero debe quitar los límites de gasto establecidos para la suscripción al sitio web. Para ver o cambiar opciones para la suscripción a sitios web de Microsoft Azure, consulte [Microsoft Azure Subscriptions](http://go.microsoft.com/fwlink/?LinkID=235288).

En este artículo:

-   [Escalación al modo de plan compartido o básico](#scalingsharedorbasic)
-   [Escalación al modo de plan estándar](#scalingstandard)
-   [Escalación de una base de datos SQL Server conectada a su sitio](#ScalingSQLServer)
-   [Características del desarrollador](#devfeatures)
-   [Otras características](#OtherFeatures)

## Escalación al modo de plan compartido o básico

1.  En el explorador, abra el [Portal de administración](https://manage.windowsazure.com/).

2.  En la pestaña **Sitios web**, seleccione su sitio web.

    ![Selección de un sitio web](./media/web-sites-scale/01SelectWebSite.png)

3.  Haga clic en la pestaña **Scale**.

    ![Pestaña SCALE](./media/web-sites-scale/02SelectScaleTab.png)

4.  En la sección **Web Hosting Plan Mode**, seleccione **SHARED** o **BASIC**. En el ejemplo de la imagen se selecciona Basic.

    ![Elegir el plan de hospedaje web](./media/web-sites-scale/03aChooseWHP.png)

    La sección **Web Hosting Plan Sites** muestra una breve lista de sitios en el plan actual. Todos los sitios del plan actual se cambiarán al modo de plan de hospedaje web que seleccione.

5.  En la sección **Capacity**, seleccione **Instance Size**. Las opciones disponibles son **Small**, **Medium** o **Large**. La opción del tamaño de instancia no está disponible en el modo compartido. Para obtener más información sobre los tamaños de instancias, consulte [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

    ![Tamaño de instancia para el modo básico](./media/web-sites-scale/03bChooseBasicInstanceSize.png)

6.  Use el control deslizante para seleccionar el **recuento de instancias** que desee.

    ![Recuento de instancias para el modo básico](./media/web-sites-scale/04ChooseBasicInstanceCount.png)

7.  En la barra de comandos, haga clic en **Save**.

    ![Botón Save](./media/web-sites-scale/05SaveButton.png)

    > [WACOM.NOTE] Puede configurar y guardar los valores del **plan de hospedaje web**, **tamaño de instancia** y **recuento de instancias** de forma independiente si lo desea.

8.  Un mensaje de confirmación le recordará que los sitios en el mismo plan de hospedaje web, como el sitio web actual, también cambiarán al nuevo modo. Seleccione **Yes** para completar el cambio.

    En el ejemplo, el modo de plan se cambia a **básico**:

    ![Cambio de plan completo](./media/web-sites-scale/06BasicComplete.png)

## Escalación al modo de plan estándar

> [WACOM.NOTE] Antes de cambiar un plan de hospedaje web al modo estándar, debe quitar las limitaciones de gasto establecidas para la suscripción a sitios web de Microsoft Azure. De lo contrario, se arriesga a que su sitio no esté disponible si alcanza los límites antes de la finalización del período de facturación. Para ver o cambiar opciones para la suscripción a sitios web de Microsoft Azure, consulte [Microsoft Azure Subscriptions](http://go.microsoft.com/fwlink/?LinkID=235288).

1.  Para escalar al modo estándar, siga los mismos pasos iniciales que cuando se escala a compartido o básico y, a continuación, seleccione **Standard** en **Web Hosting Plan Mode**.

    ![Elegir el plan estándar](./media/web-sites-scale/07ChooseStandard.png)

    Como ocurría anteriormente, la sección **Web Hosting Plan Sites** muestra una breve lista de sitios en el plan actual. En este caso, todos los sitios del plan actual se cambiarán al modo estándar.

2.  Si selecciona **Standard**, se expande la sección **Capacity** para mostrar las opciones **Instance Size** e **Instance Count**, que también se encuentran disponibles en el modo básico. Las opciones **Edit Scale Settings for Schedule** y **Scale by Metric** solo están disponibles en el modo estándar.

    ![Sección Capacity en el modo estándar](./media/web-sites-scale/08CapacitySectionStandard.png)

3.  Configure el **tamaño de instancia**. Las opciones disponibles son **Small**, **Medium** o **Large**.

    ![Elegir el tamaño de instancia](./media/web-sites-scale/09ChooseInstanceSize.png)

    Para obtener más información sobre los tamaños de instancias, consulte [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

4.  Si desea escalar automáticamente (autoescalar) recursos según el horario diurno frente al nocturno, día de la semana frente a fin de semana o fechas y horas específicas, seleccione **Set up schedule times** en la opción **Edit Scale Settings for Schedule**.

    ![Configurar las horas programadas](./media/web-sites-scale/10SetUpScheduleTimesButton.png)

5.  El cuadro de diálogo **Set up schedule times** proporciona un número de opciones de configuración útiles.

    ![Cuadro de diálogo Set up schedule times](./media/web-sites-scale/11SetUpScheduleTimesDialog.png)

6.  En **Recurring Schedules**, seleccione **Differing scale between Day and Night** o **Differing Scale between Weekday and Weekend** para escalar recursos basados en programaciones de horario diurno y nocturno independientes o programas de días de la semana frente a fines de semana independientes.

    > [WACOM.NOTE] Para los propósitos de esta característica, el fin de semana comienza al final del viernes (8:00 p.m. de forma predeterminada) y finaliza a principios del lunes (8:00 a.m. de forma predeterminada). El perfil de fin de semana usa el mismo inicio y final de día que definirá en la opción **Time**.

7.  En **Time**, seleccione una fecha de inicio y fin para el día en incrementos de media hora, y una zona horaria. De forma predeterminada, el día comienza a las 8:00 a.m. y finaliza a las 8:00 p.m. Se respeta el horario de verano según la zona horaria que seleccione.

8.  En **Specific Dates**, puede crear uno o más plazos de tiempo con nombre para el que puede escalar recursos. Por ejemplo, es posible que desee proporcionar recursos adicionales para una venta o iniciar un evento durante el que puede tener picos largos en el tráfico web.

9.  Una vez que haya realizado su selección, haga clic en **OK** para cerrar el cuadro de diálogo **Schedule Times**.

10. La casilla **Edit Scale Settings for Schedule** muestra ahora programas configurables o eventos basados en los cambios que ha realizado. Seleccione una de las programaciones recurrentes o fechas específicas para configurarla.

	
	![Edit scale settings for schedule](EditScaleSettingsForSchedule)
	
	Ahora puede usar las características **Scale by Metric** e **Instance Count** para ajustar la escalación de recursos para cada programación que seleccione. 
	
11.  Para ajustar de forma dinámica el número de instancias que usa el sitio web si se cambia la carga, habilite la opción **Scale by Metric** seleccionando **CPU**.
	
	![Scale By Metric](ScaleByMetric)

	El gráfico muestra el número de instancias usado durante la semana pasada. Puede usar el gráfico para supervisar la actividad de escalación.

12.  **Scale by Metric** modifica la característica **Instance Count** para que pueda establecer el número mínimo y máximo de máquinas virtuales que usar para la escalación automática. Azure nunca sobrepasará los límites superiores e inferiores que establezca.

    ![Recuento de instancias](./media/web-sites-scale/14InstanceCount.png)

13.  **Scale by Metric** también habilita la opción **Target CPU** para que pueda especificar un rango objetivo para el uso de la CPU. Este rango representa el uso de la CPU medio para su sitio web. Azure agregará o quitará las instancias estándar para mantener su sitio web en este rango.

    ![CPU objetivo](./media/web-sites-scale/15TargetCPU.png)

    **Nota**: cuando se habilita **Scale by Metric**, Microsoft Azure comprueba la CPU del sitio web una vez cada cinco minutos y agrega instancias según corresponda en ese momento. Si el uso de la CPU es bajo, Microsoft Azure quitará las instancias una vez cada dos horas para garantizar que el sitio web sigue siendo eficiente. Normalmente, lo adecuado es establecer el recuento de instancia mínimo en 1. Sin embargo, si se produce un pico de uso repentino en el sitio web, asegúrese de que dispone de un número mínimo suficiente de instancias para administrar la carga. Por ejemplo, si se produce un pico repentino de tráfico durante el intervalo de 5 minutos, antes de que Microsoft Azure compruebe el uso de la CPU, es posible que el sitio no responda durante ese período. Si espera que se produzca una gran cantidad de tráfico repentino, establezca el recuento de instancia mínimo en un valor más alto para anticiparse a esas ráfagas.

14.  Una vez que finalice la realización de cambios de los elementos en **Edit Scale Settings for Schedule**, haga clic en el icono **Save** de la barra de comandos en la parte inferior de la página para guardar toda la configuración de la programación a la vez (no tiene que guardar cada programación individualmente).

## Escalación de una base de datos SQL Server conectada a su sitio 
Si dispone de una o más base de datos SQL Server vinculadas a su sitio web (con independencia del modo de plan de hospedaje web), aparecerán en la sección **Linked Resources** en la parte inferior de la página Scaling.

1.  Para escalar una de las bases de datos, en la sección **Linked Resources**, haga clic en el vínculo **Manage scale for this database** junto al nombre de la base de datos.

    ![Base de datos vinculada](./media/web-sites-scale/16LinkedResources.png)

2.  El vínculo le lleva a la pestaña SQL Server del Portal de administración de Azure, donde puede configurar la **edición** y el **tamaño máximo** de la base de datos:

    ![Escalar la base de datos SQL Server](./media/web-sites-scale/17ScaleDatabase.png)

    En **Edition**, seleccione **Web** o **Business** según la capacidad de almacenamiento que necesite. La edición **Web** ofrece un rango de capacidades más pequeñas y la edición **Business** un rango de capacidades mayores.

    El valor que seleccione para **Max Size** especifica un límite superior para la base de datos. Los cargos de base de datos se basan en la cantidad de datos que realmente almacena, por lo que cambiar la propiedad **Max Size** no afecta por sí misma a dichos cargos. Para obtener más información, consulte [Cuentas y facturación en Base de datos SQL de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=234930).

## Características del desarrollador
Según el modo de plan de hospedaje web, se encuentran disponibles las siguientes características orientadas al desarrollador:

**Valor de bits**

-   Los modos de planes básico y estándar son compatibles con aplicaciones de 32 y 64 bits.
-   Los modos de plan libre y compartido solo son compatibles con aplicaciones de 32 bits.

**Compatibilidad con el depurador**

-   Se ofrece compatibilidad con el depurador para los modos de plan de hospedaje web libre, compartido y básico en una conexión simultánea por aplicación.
-   Se ofrece compatibilidad con el depurador para el modo de plan de hospedaje web estándar en 5 conexiones simultáneas por aplicación.

## Otras características

**Supervisión de extremos web**

-   La supervisión de extremos web está disponible en los modos de plan de hospedaje básico y estándar. Para obtener más información sobre la supervisión de extremos web, consulte [Supervisión de sitios web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/).

-   Para obtener información detallada sobre las características restantes en los planes de hospedaje web, incluido el precio y las características de interés para todos los usuarios (incluidos los desarrolladores), consulte [Detalles de precios de Sitios web](http://www.windowsazure.com/en-us/pricing/details/web-sites/).

## Pasos siguientes 
-   Para obtener información sobre las prácticas recomendadas de sitios web de Azure, incluida la creación de una arquitectura resistente y escalable, consulte [Best Practices: Azure Websites (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

-   Para obtener información sobre el precio, soporte técnico y contrato de nivel de servicio, consulte los siguientes vínculos.

    [Detalles de precios de Transferencias de datos](http://www.windowsazure.com/en-us/pricing/details/data-transfers/)

    [Planes de soporte técnico de Azure](http://www.windowsazure.com/en-us/support/plans/)

    [Contratos de nivel de servicio](http://www.windowsazure.com/en-us/support/legal/sla/)

    [Detalles de precios de Base de datos SQL](http://www.windowsazure.com/en-us/pricing/details/sql-database/)

    [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=309169)

    [Detalles de precios de Sitios web](http://www.windowsazure.com/en-us/pricing/details/web-sites/)

    [Detalles de precios de Sitios web: Conexiones SSL](http://www.windowsazure.com/en-us/pricing/details/web-sites/#ssl-connections)

-   Los siguientes vínculos son enlaces a vídeos para la escalación de sitios web de Azure:

    [Cuándo escalar sitios web de Azure: con Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/azure-web-sites-free-vs-standard-scaling/)

    [Autoescalación de sitios web de Azure mediante CPU o programación: con Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/auto-scaling-azure-web-sites/)

    [Escalación de sitios web de Azure: con Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/how-azure-web-sites-scale/)


