---
title: "Administración de un área de trabajo de Aprendizaje automático de Azure | Microsoft Docs"
description: "Administrar el acceso a las áreas de trabajo del aprendizaje automático de Azure, e implementar y administrar servicios web de la API del aprendizaje automático"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: cf0cd5202288a9da55e0f8409a51ea904eeb2d55
ms.openlocfilehash: 775ef958846ad2a780abb761d1c3934e671d5f1c
ms.lasthandoff: 03/01/2017


---
# <a name="manage-an-azure-machine-learning-workspace"></a>Administración de un área de trabajo de Aprendizaje automático de Azure

> [!NOTE]
> Para obtener información acerca de cómo administrar servicios web en el portal Servicios web Machine Learning, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](machine-learning-manage-new-webservice.md).
> 
> 

Las áreas de trabajo de Machine Learning se pueden administrar en Azure Portal o en el Portal de Azure clásico.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Uso del Portal de Azure

Para administrar un área de trabajo en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) mediante una cuenta de administrador de la suscripción de Azure.
2. En el cuadro de búsqueda de la parte superior de la página, escriba "áreas de trabajo de Machine Learning" y, después, seleccione **Áreas de trabajo de Machine Learning**.
3. Haga clic en el área de trabajo que desea administrar.

Además de la información de administración de recursos estándar y de las opciones disponibles, puede:

- Ver **Propiedades**: esta página muestra la información del área de trabajo y de los recursos, y puede cambiar la suscripción y el grupo de recursos con el que esta área de trabajo está conectado.
- **Resincronizar las claves de almacenamiento**: el área de trabajo mantiene las claves de la cuenta de almacenamiento. Si la cuenta de almacenamiento cambia las claves, puede hacer clic en **Resincronizar claves** para sincronizar las claves con el área de trabajo.

Para administrar los servicios web asociados a esta área de trabajo, use el Portal de servicios web Machine Learning. Para obtener una información más completa, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](machine-learning-manage-new-webservice.md).

> [!NOTE]
> Para implementar o administrar nuevos servicios web, es preciso que se le haya asignado un rol de colaborador o administrador en la suscripción en la que se implementa el servicio web. Si invita a otro usuario a un área de trabajo de Machine Learning, debe asignarle un rol de colaborador o administrador en la suscripción para que pueda implementar o administrar servicios web. 

## <a name="use-the-azure-classic-portal"></a>Uso del Portal de Azure clásico

Mediante el Portal de Azure clásico, puede administrar las áreas de trabajo de Aprendizaje automático para realizar las siguientes tareas:

* Supervisar el uso del área de trabajo
* Configurar el área de trabajo para permitir o denegar el acceso
* Administrar servicios web creados en el área de trabajo
* Eliminar el área de trabajo

Además, la pestaña Panel muestra una descripción general del uso del área de trabajo y una vista rápida de la información que contiene.  

> [!TIP]
> En Azure Machine Learning Studio, en la pestaña **SERVICIOS WEB**, puede agregar, actualizar o eliminar un servicio web Machine Learning.
> 
> 

Para administrar un área de trabajo en el Portal de Azure clásico:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con su cuenta de Microsoft Azure (use la cuenta que está asociada a la suscripción de Azure).
2. En el panel de servicios de Microsoft Azure, haga clic en **APRENDIZAJE AUTOMÁTICO**.
3. Haga clic en el área de trabajo que desea administrar.

La página del área de trabajo tiene tres pestañas:

* **Panel** : permite ver el uso y la información del área de trabajo.
* **Configurar** : permite administrar el acceso al área de trabajo.
* **SERVICIOS WEB**: permite administrar los servicios web que se han publicado desde esta área de trabajo.

### <a name="to-monitor-how-the-workspace-is-being-used"></a>Para supervisar el uso del área de trabajo
Haga clic en la pestaña **Panel** .

En el panel, puede ver el uso general del área de trabajo y obtener una vista rápida de su información.

* El gráfico **Proceso** muestra los recursos de proceso que se usan en el área de trabajo. Puede cambiar la vista para mostrar valores relativos o absolutos, y puede cambiar el período de tiempo que se muestra en el gráfico.
* **Información general del uso** : muestra el almacenamiento de Azure que usa el área de trabajo.
* **Vista rápida** : proporciona un resumen de la información del área de trabajo y vínculos útiles.

> [!NOTE]
> El vínculo **Iniciar sesión en ML Studio** permite abrir Machine Learning Studio mediante la cuenta Microsoft en la que haya iniciado la sesión actual. La cuenta de Microsoft que usó para iniciar sesión en el Portal de Azure clásico para crear un área de trabajo no tiene automáticamente permiso para abrir el área de trabajo. Para abrir un área de trabajo, debe iniciar sesión en la cuenta de Microsoft que se definió como propietaria del área de trabajo. También puede hacerlo si recibe una invitación del propietario para unirse al área de trabajo.
> 
> 

### <a name="to-grant-or-suspend-access-for-users"></a>Concesión o suspensión del acceso de los usuarios
Haga clic en la pestaña **Configurar** .

En la pestaña de configuración puede realizar las acciones siguientes:

* Suspender el acceso al área de trabajo de Aprendizaje automático haciendo clic en Denegar. Los usuarios ya no podrán abrir el área de trabajo en Estudio de aprendizaje automático. Para restaurar el acceso, haga clic en Permitir.

Para administrar cuentas adicionales que dispongan de acceso al área de trabajo en Machine Learning Studio, haga clic en **Iniciar sesión en ML Studio** en la pestaña **PANEL** (consulte la nota anterior respecto a **Iniciar sesión en ML Studio**). De esta manera, se abre el área de trabajo en Estudio de aprendizaje automático. Desde aquí, haga clic en la pestaña **CONFIGURACIÓN** y luego en **USUARIOS**. Puede hacer clic en **INVITAR MÁS USUARIOS** para permitir el acceso a otros usuarios al área de trabajo. También puede seleccionar un usuario y hacer clic en **QUITAR**.

### <a name="to-manage-web-services-in-this-workspace"></a>Para administrar servicios web en esta área de trabajo
Haga clic en la ficha **Servicios web** .

Esto muestra una lista de servicios web publicados desde esta área de trabajo.
Para administrar un servicio web, haga clic en el nombre en la lista y se abrirá la página correspondiente.

Un servicio web puede tener uno o varios puntos de conexión definidos.

* Puede definir más puntos de conexión, además del punto de conexión "predeterminado". Para agregar un punto de conexión, haga clic en **Manage Endpoints** (Administrar puntos de conexión) en la parte inferior del panel para abrir el portal Servicios web Azure Machine Learning.
* Para eliminar un punto de conexión (no se puede eliminar el punto de conexión "Predeterminado"), haga clic en la casilla situada al principio de la fila del punto de conexión y luego en **ELIMINAR**. De esta manera, se elimina el punto de conexión del servicio web.
  
  > [!NOTE]
  > Si una aplicación está utilizando el extremo del servicio web cuando se elimina dicho extremo, la aplicación recibirá un error la próxima vez que intente acceder al servicio.
  > 
  > 

Haga clic en el nombre de un punto de conexión de servicio web para abrirlo. 

En el panel puede ver el uso general del servicio web durante un periodo. Puede seleccionar el periodo para verlo en el menú desplegable Periodo situado en la esquina superior derecha de los gráficos de uso. El panel muestra la siguiente información:

* **Requests Over Time** (Solicitudes a lo largo de un periodo) muestra un gráfico de escalera del número de solicitudes durante el periodo seleccionado. Puede ayudarlo a identificar si se están experimentando picos de uso.
* **Request-Response Requests** (Solicitudes de solicitud-respuesta) muestra el número total de llamadas de solicitud-respuesta que ha recibido el servicio durante el periodo seleccionado y cuántas tienen errores.
* **Average Request-Response Compute Time** (Tiempo de proceso medio de solicitud-respuesta) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
* **Batch Requests** (Solicitudes por lotes) muestra el número total de solicitudes por lotes que ha recibido el servicio en el periodo seleccionado y cuántas tienen errores.
* **Average Job Latency** (Latencia media de los trabajos) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
* **Errores** muestra el número total de errores que se han producido en las llamadas al servicio web.
* **Services Costs** (Costos de los servicios) muestra los cargos correspondientes al plan de facturación asociado con el servicio.

En la página Configurar puede actualizar las propiedades siguientes:

* **Descripción** permite escribir una descripción del servicio web. La descripción es un campo obligatorio.
* **Registro** permite habilitar o deshabilitar el registro de errores en el punto de conexión. Para obtener más información sobre el registro, consulte [Habilitación del registro para los servicios web Machine Learning](machine-learning-web-services-logging.md).
* **Habilitar datos de ejemplo** permite ofrecer datos de ejemplo que pueden usarse para probar el servicio de solicitud-respuesta. Si ha creado el servicio web en Estudio de aprendizaje automático de Microsoft Azure, los datos de ejemplo se toman de los utilizados para entrenar el modelo. Si ha creado el servicio mediante programación, los datos se extraen de los datos de ejemplo ofrecidos como parte del paquete JSON.


