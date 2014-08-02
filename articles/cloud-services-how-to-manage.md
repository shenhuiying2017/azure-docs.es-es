<properties linkid="manage-services-how-to-manage-a-cloud-service" urlDisplayName="How to manage" pageTitle="How to manage a cloud service - Azure" metaKeywords="Azure manage cloud services, Azure Management Portal cloud services" description="Learn how to manage cloud services in the Azure Management Portal." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Manage Cloud Services" authors="ryanwi" solutions="" manager="" editor="" />

Administración de servicios en la nube
======================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

En el área **Servicios en la nube** del Portal de administración de Azure, puede actualizar un rol de servicio o una implementación, pasar su servicio en la nube de ensayo a producción, vincular recursos con su servicio en la nube de modo que pueda ver las dependencias de los recursos y escalar los recursos juntos, además de eliminar un servicio en la nube o una implementación.

Tabla de contenido
------------------

-   [Actualización de un rol de servicio en la nube o implementación](#updaterole)
-   [Intercambio de implementaciones para pasar un servicio en la nube de ensayo a producción](#swap)
-   [Vínculo de un recurso a un servicio en la nube](#linkresources)
-   [Eliminación de implementaciones y un servicio en la nube](#deletecloudservice)

Actualización de un rol de servicio en la nube o implementación
---------------------------------------------------------------

Si necesita actualizar el código de la aplicación para su servicio en la nube, use **Update** en el panel, en la página **Servicios en la nube** o **Instancias**. Puede actualizar un solo rol o todos los roles. Necesitará cargar un paquete de servicio nuevo y el archivo de configuración de servicio.

1.  En el [Portal de administración de Azure](https://manage.windowsazure.com/), en el panel, en la página **Servicios en la nube** o **Instancias**, haga clic en **Actualizar**.

    Se abre **Update Deployment**.

    ![Implementación de actualizaciones](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2.  En **Etiqueta de implementación**, escriba un nombre para identificar la implementación (por ejemplo, mycloudservicev2). Encontrará el nombre de la implementación debajo de **quick start** en el panel.

3.  En **Package file**, utilice **Browse** para cargar el archivo de paquete de servicio (.cspkg).

4.  En **Archivo de configuración**, utilice **Browse** para cargar el archivo de configuración (.cspkg).

5.  En **Rol**, seleccione **All** si desea actualizar todos los roles en el servicio en la nube. Para realizar la actualización de un rol, seleccione el rol que desea actualizar. Aunque seleccione un rol específico para actualizar, las actualizaciones en el archivo de configuración del servicio se aplican a todos los roles.

6.  Si la actualización va a cambiar la cantidad de roles o el tamaño de cualquier rol, seleccione la casilla **Allow update if role sizes or number of roles changes** para permitir que la actualización continúe.

    Tenga presente que si cambia el tamaño de un rol (es decir, el tamaño de una máquina virtual que hospeda una instancia de rol) o la cantidad de roles, se debe volver a crear una imagen de la instancia de rol (máquina virtual) y se perderán todos los datos locales.

7.  Si cualquier rol de servicio tiene solo una instancia de rol, seleccione la casilla **Update even if one or more role contain a single instance** para permitir que la actualización continúe.

    Azure solo puede garantizar un 99,95 % de disponibilidad del servicio durante una actualización del servicio en la nube si cada rol tiene al menos dos instancias de rol (máquinas virtuales). Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza.

8.  Haga clic en OK (marca de verificación) para iniciar la actualización del servicio.

Intercambio de implementaciones para pasar un servicio en la nube de ensayo a producción
----------------------------------------------------------------------------------------

Use **Swap** para pasar una implementación de ensayo de un servicio en la nube a producción Cuando decide implementar una nueva versión de un servicio en la nube, puede ensayar y probar la nueva versión en su entorno de ensayo del servicio en la nube mientras los clientes usan la versión actual en la producción. Cuando esté listo para promover la nueva versión de la producción, puede usar **Swap** para cambiar las direcciones URL que dirigen a las dos implementaciones.

Puede intercambiar implementaciones desde la página **Servicios en la nube** o el panel.

1.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**.

2.  En la lista de servicios en la nube, haga clic en el servicio en la nube para seleccionarlo.

3.  Haga clic en **Swap**.

    Se abre la siguiente solicitud de confirmación.

    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4.  Después de verificar la información de la implementación, haga clic en **Yes** para intercambiar las implementaciones.

    El intercambio de implementaciones ocurre rápidamente porque lo único que cambia son las direcciones IP virtuales (VIP) de las implementaciones.

    Para ahorrar en los costes de proceso, puede eliminar la implementación en el entorno de ensayo cuando esté seguro de que la nueva implementación de producción se realiza según lo esperado.

Vínculo de un recurso a un servicio en la nube
----------------------------------------------

Para ver las dependencias de su servicio en la nube en otros recursos, puede vincular una instancia de Base de datos SQL de Azure o una cuenta de almacenamiento con el servicio en la nube. Puede vincular y desvincular los recursos en la página **Linked Resources**. Luego, supervise su uso en el panel de servicio en la nube. Si una cuenta de almacenamiento vinculada tiene la supervisión activada, pues supervisar el total de solicitudes en el panel del servicio en la nube.

Use **Link** para vincular una instancia de Base de datos SQL nueva o existente o una cuenta de almacenamiento con su servicio en la nube. Posteriormente, puede escalar la base de datos junto con el rol del servicio en la nube que la está usando en la página **Scale**. (Una cuenta de almacenamiento escala automáticamente a medida que aumenta el uso). Para obtener más información, consulte [Escalación de un servicio en la nube y recursos vinculados](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-scale-a-cloud-service/).

Puede también supervisar, administrar y escalar la base de datos en el nodo **Bases de datos** del Portal de administración.

"Vincular" un recurso en este sentido no conecta la aplicación con el recurso. Si crea una base de datos nueva usando **Link**, necesitará agregar cadenas de conexión al código de su aplicación y luego actualizar el servicio en la nube. También necesitará agregar cadenas de conexión si su aplicación usa recursos en una cuenta de almacenamiento vinculada.

El siguiente procedimiento describe cómo vincular una instancia de Base de datos SQL nueva, implementada en un servidor de Base de datos SQL nuevo, con un servicio en la nube.

### Para vincular una instancia de Base de datos SQL con un servicio en la nube

1.  En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2.  Haga clic en **Linked Resources**.

    Se abre la página **Linked Resources**.

    ![Página de recursos vinculados](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3.  Haga clic en **Link a Resource** o en **Link**.

    Se inicia el asistente para **vincular recursos**.

    ![Vincular página1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4.  Haga clic en **Create a new resource** o **Link an existing resource**.

5.  Seleccione el tipo de recurso que desea vincular. En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Base de datos SQL**. (El Portal de administración de vista previa no admite la vinculación de una cuenta de almacenamiento con un servicio en la nube).

6.  Para completar la configuración de la base de datos, siga las instrucciones de ayuda para el área **Bases de datos SQL** del Portal de administración.

    Puede seguir el progreso de la operación de vinculación en el área de mensajes.

    ![Progreso de vinculación](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

    Cuando se completa la vinculación, puede supervisar el estado del recurso vinculado en el panel del servicio en la nube. Para obtener información sobre la escalación de una Base de datos SQL vinculada, consulte [Escalación de un servicio en la nube y recursos vinculados](../how-to-scale-a-cloud-service/).

### Para desvincular un recurso vinculado

1.  En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2.  Haga clic en **Linked Resources** y, a continuación, seleccione el recurso.

3.  Haga clic en **Unlink**. Luego, haga clic en **Yes** en la solicitud de confirmación.

    Desvincular una Base de datos SQL no tiene efecto en la base de datos ni en las conexiones de la aplicación con la base de datos. Todavía puede administrar la base de datos en el área **Bases de datos de SQL** del Portal de administración.

Eliminación de implementaciones y un servicio en la nube
--------------------------------------------------------

Antes de que pueda eliminar un servicio en la nube, debe eliminar cada implementación existente.

Para ahorrar en los costes de proceso, puede eliminar la implementación de ensayo después de verificar que la implementación de la producción esté funcionando según lo esperado. Se le cobrarán los costes de proceso de las instancias de rol aunque un servicio en la nube no esté funcionando.

Use el siguiente procedimiento para eliminar una implementación o su servicio en la nube.

1.  En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**.

2.  Seleccione el servicio en la nube y, a continuación, haga clic en **Delete**. (Para seleccionar un servicio en la nube sin abrir el panel, haga clic en cualquier parte, salvo en el nombre de la entrada del servicio en la nube).

    Si tiene una implementación en ensayo o producción, verá un menú de opciones similar al que se muestra a continuación en la parte inferior de la ventana. Antes de que puede eliminar el servicio en la nube, debe eliminar toda implementación existente.

    ![Menú para eliminar](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)

3.  Para eliminar una implementación, haga clic en **Delete production deployment** o **Delete staging deployment**. Luego, haga clic en **Yes** en la solicitud de confirmación.

4.  Si planifica eliminar el servicio en la nube, repita el paso 3, si es necesario, para eliminar la otra implementación.

5.  Para eliminar el servicio en la nube, haga clic en **Delete cloud service**. Luego, haga clic en **Yes** en la solicitud de confirmación.

> [WACOM.NOTE] Si se configura una supervisión detallada para su servicio en la nube, Azure no elimina los datos de supervisión de la cuenta de almacenamiento al eliminar el servicio en la nube. Tendrá que eliminar los datos manualmente. Para obtener información sobre dónde buscar las tablas métricas, consulte "Acceso a los datos de supervisión detallada fuera del Portal de administración" en [Supervisión de servicios en la nube](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

