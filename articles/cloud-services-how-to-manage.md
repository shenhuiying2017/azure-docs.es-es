<properties urlDisplayName="How to manage" pageTitle="Administración de un servicio en la nube - Azure" metaKeywords="Azure manage cloud services, Azure Management Portal cloud services" description="Vea cómo administrar servicios en la nube en el Portal de administración de Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Manage Cloud Services" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />





#Administración de servicios en la nube

En el área **Servicios en la nube** del Portal de administración de Azure, puede actualizar un rol de servicio o una implementación, pasar su servicio en la nube de ensayo a producción, vincular recursos con su servicio en la nube de modo que pueda ver las dependencias de los recursos y escalar los recursos juntos, además de eliminar un servicio en la nube o una implementación.

##Tabla de contenido##

* [Actualización del rol de servicio en la nube o implementación](#updaterole)
* [Intercambio de implementaciones para pasar un servicio en la nube de ensayo a producción](#swap)
* [Vinculación de un recurso a un servicio en la nube](#linkresources)
* [Eliminación de implementaciones y un servicio en la nube](#deletecloudservice)


<h2><a id="updaterole"></a>Reproducción de rol de servicio en la nube o implementación</h2>

Si necesita actualizar el código de la aplicación para su servicio en la nube, use **Actualizar** en el panel, en la página **Servicios en la nube** o **Instancias**. Puede actualizar un solo rol o todos los roles. Necesitará cargar un paquete de servicio nuevo y el archivo de configuración de servicio.

1. En el [Portal de administración de Azure](https://manage.windowsazure.com/), en el panel, página **Servicios en la nube** o en la página **Instancias**, haga clic en **Actualizar**.

	![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. En **Etiqueta de implementación**, escriba un nombre para identificar la implementación (por ejemplo, mycloudservice4). Encontrará la etiqueta de la implementación debajo de **Inicio rápido** en el panel.

3. En **Paquete**, utilice **Examinar** para cargar el archivo de paquete del servicio (.cspkg).

4. En **Configuración**, utilice **Examinar** para cargar el archivo de configuración del servicio (.cspkg).

5. En **Rol**, seleccione **All** si desea actualizar todos los roles en el servicio en la nube. Para actualizar un rol, seleccione el rol que desea actualizar. Aunque seleccione un rol específico para actualizar, las actualizaciones en el archivo de configuración del servicio se aplican a todos los roles.

6. Si la actualización cambia el número de roles o el tamaño de algún rol, active la casilla **Permitir actualizar si cambian los tamaños de rol o el número de roles** para que la actualización continúe. 

	Tenga presente que si cambia el tamaño de un rol (es decir, el tamaño de una máquina virtual que hospeda una instancia de rol) o la cantidad de roles, se debe volver a crear una imagen de la instancia de rol (máquina virtual) y se perderán todos los datos locales.

7. Si cualquier rol de servicio tiene solo una instancia de rol, active la casilla **Actualizar aunque uno o más roles contengan una única instancia** para permitir que la actualización continúe. 

	Azure solo puede garantizar un 99,95 % de disponibilidad del servicio durante una actualización del servicio en la nube si cada rol tiene al menos dos instancias de rol (máquinas virtu**ales). Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza.

8. Haga clic en **Aceptar** (marca de verificación) para iniciar la actualización del servicio.



<h2><a id="swap"></a>Reproducción de implementaciones para pasar un servicio en la nube de ensayo a producción</h2>

Use **Intercambiar** para pasar una implementación de ensayo de un servicio en la nube a producción. Cuando decide implementar una nueva versión de un servicio en la nube, puede ensayar y probar la nueva versión en su entorno de ensayo del servicio en la nube mientras los clientes usan la versión actual en la producción. Cuando esté listo para promover la nueva versión de la producción, puede usar **Intercambiar** para cambiar las direcciones URL que dirigen a las dos implementaciones. 

Puede intercambiar implementaciones desde la página **Servicios en la nube** o el panel.

1. En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**.

2. En la lista de servicios en la nube, haga clic en el servicio en la nube para seleccionarlo.

3. Haga clic en **Intercambiar**.

	Se abre la siguiente solicitud de confirmación.

	![Cloud Services Swap](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Después de verificar la información de la implementación, haga clic en **Sí** para intercambiar las implementaciones.

	El intercambio de implementaciones ocurre rápidamente porque lo único que cambia son las direcciones IP virtuales (VIP) de las implementaciones.

	Para ahorrar en los costes de proceso, puede eliminar la implementación en el entorno de ensayo cuando esté seguro de que la nueva implementación de producción se realiza según lo esperado.

<h2><a id="linkresources"></a>Reproducción de recurso a un servicio en la nube</h2>

Para ver las dependencias de su servicio en la nube en otros recursos, puede vincular una instancia de Base de datos SQL de Azure o una cuenta de almacenamiento con el servicio en la nube. Puede vincular y desvincular recursos en la página **Recursos vinculados** y después supervisar su uso en el panel de servicios en la nube. Si una cuenta de almacenamiento vinculada tiene la supervisión activada, pues supervisar el total de solicitudes en el panel del servicio en la nube.

Use **Vincular** para vincular una instancia de Base de datos SQL nueva o existente o una cuenta de almacenamiento con su servicio en la nube. Posteriormente, puede escalar la base de datos junto con el rol del servicio en la nube que la está usando en la página **Escala**. (Una cuenta de almacenamiento escala automáticamente a medida que aumenta el uso). Para obtener más información, consulte [Escalación de un servicio en la nube y recursos vinculados](../cloud-services-how-to-scale/). 

Puede también supervisar, administrar y escalar la base de datos en el nodo **Bases de datos** del Portal de administración. 

"Vincular" un recurso en este sentido no conecta la aplicación con el recurso. Si crea una base de datos nueva usando **Vincular**, necesitará agregar cadenas de conexión al código de su aplicación y luego actualizar el servicio en la nube. También necesitará agregar cadenas de conexión si su aplicación usa recursos en una cuenta de almacenamiento vinculada.

El siguiente procedimiento describe cómo vincular una instancia de Base de datos SQL nueva, implementada en un servidor de Base de datos SQL nuevo, con un servicio en la nube.

###Para vincular una instancia de Base de datos SQL con un servicio en la nube###

1. En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2. Haga clic en **Recursos vinculados**.

	Se abre la página **Recursos vinculados**.

	![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Haga clic en **Vincular un recurso** o en **Vincular**.

	Se inicia el asistente para **Vincular recursos**.

	![Link Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Haga clic en **Crear un nuevo recurso** o **Vincular un recurso existente**.

5. Seleccione el tipo de recurso que desea vincular. En el [Portal de administración](http://manage.windowsazure.com/)Haga clic en **Base de datos SQL**. (El Portal de administración de vista previa no admite la vinculación de una cuenta de almacenamiento con un servicio en la nube).

6. Para completar la configuración de la base de datos, siga las instrucciones de ayuda para el área **Bases de datos SQL** del Portal de administración.

	Puede seguir el progreso de la operación de vinculación en el área de mensajes.

	![Link Progress](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

	Cuando se completa la vinculación, puede supervisar el estado del recurso vinculado en el panel del servicio en la nube. Para obtener información sobre la escalación de una Base de datos SQL vinculada, consulte [Escalación de un servicio en la nube y recursos vinculados](../cloud-services-how-to-scale/).

###Para desvincular un recurso vinculado###

1. En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2. Haga clic en **Recursos vinculados** y, a continuación, seleccione el recurso.

3. Haga clic en **Desvincular**. Luego, haga clic en **Sí** en la solicitud de confirmación.

	Desvincular una Base de datos SQL no tiene efecto en la base de datos ni en las conexiones de la aplicación con la base de datos. Todavía puede administrar la base de datos en el área **Bases de datos de SQL** del Portal de administración.



<h2><a id="deletecloudservice"></a>Reproducción de implementaciones y un servicio en la nube</h2>

Antes de que pueda eliminar un servicio en la nube, debe eliminar cada implementación existente.

Para ahorrar en los costes de proceso, puede eliminar la implementación de ensayo después de verificar que la implementación de la producción esté funcionando según lo esperado. Se le cobrarán los costes de proceso de las instancias de rol aunque un servicio en la nube no esté funcionando.

Use el siguiente procedimiento para eliminar una implementación o su servicio en la nube. 

1. En el [Portal de administración](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**.

2. Seleccione el servicio en la nube y, a continuación, haga clic en **Eliminar**. (Para seleccionar un servicio en la nube sin abrir el panel, haga clic en cualquier parte, salvo en el nombre de la entrada del servicio en la nube).

	Si tiene una implementación en ensayo o producción, verá un menú de opciones similar al que se muestra a continuación en la parte inferior de la ventana. Antes de que puede eliminar el servicio en la nube, debe eliminar toda implementación existente.

	![Delete Menu](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Para eliminar una implementación, haga clic en **Eliminar implementación de producción** o **Eliminar implementación de ensayo**. Luego, haga clic en **Sí** en la solicitud de confirmación. 

4. Si planifica eliminar el servicio en la nube, repita el paso 3, si es necesario, para eliminar la otra implementación.

5. Para eliminar el servicio en la nube, haga clic en **Eliminar servicio en la nube**. Luego, haga clic en **Sí** en la solicitud de confirmación.

> [WACOM.NOTE]
> Si se configura una supervisión detallada para su servicio en la nube, Azure no elimina los datos de supervisión de la cuenta de almacenamiento al eliminar el servicio en la nube. Tendrá que eliminar los datos manualmente. Para obtener información sobre dónde buscar las tablas métricas, consulte "Acceso a los los datos de supervisión detallada fuera del Portal de administración" en <a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-how-to-monitor/">Supervisión de servicios en la nube</a>.


<!--HONumber=35.1-->
