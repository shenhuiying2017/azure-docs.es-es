---
title: Uso del portal de servicios web Azure Machine Learning | Microsoft Docs
description: "Administrar el acceso a las áreas de trabajo del aprendizaje automático de Azure, e implementar y administrar servicios web de la API del aprendizaje automático"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: jhubbard
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: v-donglo
ms.openlocfilehash: 2fc71d3a03ff978485104bcd1cd9391c1d5ee392
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Administración de un servicio web mediante el portal Servicios web Azure Machine Learning
Puede administrar los servicios web Machine Learning nuevos y clásicos mediante el portal Servicios web Microsoft Azure Machine Learning. Dado que los servicios web clásicos y nuevos se basan en tecnologías subyacentes distintas, cada uno de ellos presenta funciones de administración ligeramente diferentes.

En el portal Servicios web Machine Learning, puede realizar las siguientes acciones:

* Supervisar el uso del servicio web.
* Configurar la descripción, actualizar las claves del servicio web (solo en los nuevos), actualizar la clave de la cuenta de almacenamiento (solo en los nuevos), habilitar el registro y habilitar o deshabilitar datos de ejemplo
* Eliminar el servicio web.
* Crear, eliminar o actualizar planes de facturación (solo en los nuevos)
* Agregar y eliminar puntos de conexión (solo en los clásicos)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Permisos para administrar el administrador de nuevos recursos basados en servicios web

Los nuevos servicios web se implementan como recursos de Azure. Por lo tanto, debe tener los permisos correctos para implementar y administrar nuevos servicios web.  Para implementar o administrar nuevos servicios web, debe tener asignado un rol de colaborador o administrador en la suscripción en la que se implementa el servicio web. Si invita a otro usuario a un área de trabajo de Machine Learning, debe asignarle un rol de colaborador o administrador en la suscripción para que pueda implementar o administrar servicios web. 

Si el usuario no tiene los permisos correctos para acceder a recursos en el portal de servicios web Machine Learning de Azure, verá el siguiente error al tratar de implementar un servicio web:

*Error de implementación de servicio web. Esta cuenta no tiene suficientes derechos de acceso a la suscripción de Azure que contiene el área de trabajo. Para implementar un servicio web en Azure, la misma cuenta debe estar invitada al área de trabajo y tener acceso a la suscripción de Azure que contiene el área de trabajo*.

Para obtener más información sobre cómo crear un área de trabajo, consulte cómo [crear y compartir un área de trabajo de Azure Machine Learning](create-workspace.md).

Para obtener más información sobre cómo establecer permisos de acceso, consulte cómo [ver las asignaciones de acceso para usuarios y grupos en Azure Portal: versión preliminar pública](../../active-directory/role-based-access-control-manage-assignments.md).


## <a name="manage-new-web-services"></a>Administración de servicios web nuevos
Instrucciones para la administración de servicios web nuevos:

1. Inicie sesión en el portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) con su cuenta de Microsoft Azure (use la que está asociada a la suscripción de Azure).
2. En el menú, haga clic en **Servicios web**.

Se mostrará una lista de los servicios web implementados de la suscripción. 

Para administrar un servicio web, haga clic en Servicios web. En la página Servicios web, puede llevar a cabo estas acciones:

* Hacer clic en el servicio web para administrarlo
* Hacer clic en el plan de facturación del servicio web para actualizarlo
* Eliminar un servicio web
* Copiar un servicio web e implementarlo en otra región

Al hacer clic en un servicio web, se abrirá la correspondiente página Inicio rápido. Esta página tiene dos opciones de menú que le permiten administrar el servicio web:

* **PANEL**: permite ver el uso del servicio web.
* **CONFIGURAR**: permite agregar texto descriptivo, actualizar la clave de la cuenta de almacenamiento asociada con el servicio web, y habilitar y deshabilitar datos de ejemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Supervisión del uso del servicio web
Haga clic en la pestaña **Panel** .

En el panel puede ver el uso general del servicio web durante un periodo. Puede seleccionar el periodo para verlo en el menú desplegable Periodo situado en la esquina superior derecha de los gráficos de uso. El panel muestra la siguiente información:

* **Requests Over Time** (Solicitudes a lo largo de un periodo) muestra un gráfico de escalera del número de solicitudes durante el periodo seleccionado. Puede ayudarlo a identificar si se están experimentando picos de uso.
* **Request-Response Requests** (Solicitudes de solicitud-respuesta) muestra el número total de llamadas de solicitud-respuesta que ha recibido el servicio durante el periodo seleccionado y cuántas tienen errores.
* **Average Request-Response Compute Time** (Tiempo de proceso medio de solicitud-respuesta) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
* **Batch Requests** (Solicitudes por lotes) muestra el número total de solicitudes por lotes que ha recibido el servicio en el periodo seleccionado y cuántas tienen errores.
* **Average Job Latency** (Latencia media de los trabajos) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
* **Errores** muestra el número total de errores que se han producido en las llamadas al servicio web.
* **Services Costs** (Costos de los servicios) muestra los cargos correspondientes al plan de facturación asociado con el servicio.

### <a name="configuring-the-web-service"></a>Configuración del servicio web
Haga clic en la opción de menú **CONFIGURAR** .

Puede actualizar las propiedades siguientes:

* **Descripción** permite escribir una descripción del servicio web.
* **Título** permite escribir un título para el servicio web
* **Claves** permite alternar las claves de API primarias y secundarias.
* **Clave de cuenta de almacenamiento** permite actualizar la clave de la cuenta de almacenamiento asociada con los cambios del servicio web. 
* **Habilitar datos de ejemplo** permite ofrecer datos de ejemplo que pueden usarse para probar el servicio de solicitud-respuesta. Si ha creado el servicio web en Estudio de aprendizaje automático de Microsoft Azure, los datos de ejemplo se toman de los utilizados para entrenar el modelo. Si ha creado el servicio mediante programación, los datos se extraen de los datos de ejemplo ofrecidos como parte del paquete JSON.

### <a name="managing-billing-plans"></a>Administración de planes de facturación
Haga clic en la opción de menú **Planes** de la página Inicio rápido de los servicios web. También puede hacer clic en el plan asociado con el servicio web específico para administrar dicho plan.

* **Nuevo** permite crear un nuevo plan.
* **Add/Remove Plan instance** (Agregar/eliminar instancia de plan) permite escalar horizontalmente un plan existente para agregar capacidad.
* **Upgrade/DownGrade** (Actualizar/cambiar a una versión anterior) permite escalar verticalmente un plan existente para agregar capacidad.
* **Eliminar** permite eliminar un plan.

Haga clic en un plan para ver su panel. El panel proporciona una instantánea o el uso del plan durante un periodo seleccionado. Para seleccionar el periodo que desee ver, haga clic en el menú desplegable **Período** situado en la esquina superior derecha del panel. 

El panel de planes proporciona la siguiente información:

* **Descripción del plan** muestra información sobre los costos y la capacidad asociados con el plan.
* **Plan Usage** (Uso del plan) muestra el número de transacciones y las horas de proceso que se han cobrado en el plan.
* **Servicios web** muestra el número de servicios web que usan este plan.
* **Top web Service By Calls** (Principales servicios web por llamadas) muestra los cuatro principales servicios web que realizan llamadas que se cobran del plan.
* **Top web Services by Compute Hrs** (Principales servicios web por horas de proceso) muestra los cuatro principales servicios web que usan recursos de proceso que se cobran del plan.

## <a name="manage-classic-web-services"></a>Administración de servicios web clásicos
> [!NOTE]
> Los procedimientos de esta sección son pertinentes para la administración de los servicios web clásicos a través del portal Servicios web Azure Machine Learning. Para obtener información sobre cómo administrar servicios web clásicos mediante Machine Learning Studio y el Portal de Azure clásico, consulte [Administración de un área de trabajo de Azure Machine Learning](manage-workspace.md).
> 
> 

Instrucciones para administrar los servicios web clásicos:

1. Inicie sesión en el portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) con su cuenta de Microsoft Azure (use la que está asociada a la suscripción de Azure).
2. En el menú, haga clic en **Classic Web Services** (Servicios web clásicos).

Para administrar un servicio web clásico, haga clic en **Classic Web Services** (Servicios web clásicos). Desde la página Classic Web Services (Servicios web clásicos), puede llevar a cabo estas acciones:

* Hacer clic en el servicio web para ver los puntos de conexión asociados
* Eliminar un servicio web

Al administrar un servicio web clásico, cada uno de los puntos de conexión se administra por separado. Al hacer clic en un servicio web en la página Servicios web, se abre la lista de puntos de conexión asociados con el servicio. 

En la página de puntos de conexión del servicio web clásico puede agregar y eliminar los puntos de conexión del servicio. Para más información sobre la incorporación de puntos de conexión, consulte [Creación de puntos de conexión](create-endpoint.md).

Haga clic en uno de los puntos de conexión para abrir la página Inicio rápido del servicio web. En la página Inicio rápido hay dos opciones de menú que permiten administrar el servicio web:

* **PANEL**: permite ver el uso del servicio web.
* **CONFIGURAR**: permite agregar texto descriptivo, activar y desactivar el registro de errores, actualizar la clave de la cuenta de almacenamiento asociada con el servicio web, y habilitar y deshabilitar datos de ejemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Supervisión del uso del servicio web
Haga clic en la pestaña **Panel** .

En el panel puede ver el uso general del servicio web durante un periodo. Puede seleccionar el periodo para verlo en el menú desplegable Periodo situado en la esquina superior derecha de los gráficos de uso. El panel muestra la siguiente información:

* **Requests Over Time** (Solicitudes a lo largo de un periodo) muestra un gráfico de escalera del número de solicitudes durante el periodo seleccionado. Puede ayudarlo a identificar si se están experimentando picos de uso.
* **Request-Response Requests** (Solicitudes de solicitud-respuesta) muestra el número total de llamadas de solicitud-respuesta que ha recibido el servicio durante el periodo seleccionado y cuántas tienen errores.
* **Average Request-Response Compute Time** (Tiempo de proceso medio de solicitud-respuesta) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
* **Batch Requests** (Solicitudes por lotes) muestra el número total de solicitudes por lotes que ha recibido el servicio en el periodo seleccionado y cuántas tienen errores.
* **Average Job Latency** (Latencia media de los trabajos) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
* **Errores** muestra el número total de errores que se han producido en las llamadas al servicio web.
* **Services Costs** (Costos de los servicios) muestra los cargos correspondientes al plan de facturación asociado con el servicio.

### <a name="configuring-the-web-service"></a>Configuración del servicio web
Haga clic en la opción de menú **CONFIGURAR** .

Puede actualizar las propiedades siguientes:

* **Descripción** permite escribir una descripción del servicio web. La descripción es un campo obligatorio.
* **Registro** permite habilitar o deshabilitar el registro de errores en el punto de conexión. Para obtener más información sobre el registro, consulte [Habilitación del registro para los servicios web Machine Learning](web-services-logging.md).
* **Habilitar datos de ejemplo** permite ofrecer datos de ejemplo que pueden usarse para probar el servicio de solicitud-respuesta. Si ha creado el servicio web en Estudio de aprendizaje automático de Microsoft Azure, los datos de ejemplo se toman de los utilizados para entrenar el modelo. Si ha creado el servicio mediante programación, los datos se extraen de los datos de ejemplo ofrecidos como parte del paquete JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Concesión o suspensión del acceso por parte de los usuarios del portal a los servicios web
Mediante el Portal de Azure clásico, puede permitir o denegar el acceso a usuarios específicos.

### <a name="access-for-users-of-new-web-services"></a>Acceso por parte de los usuarios de los servicios web nuevos
Para permitir que otros usuarios puedan trabajar con los servicios web del portal Servicios web Azure Machine Learning, debe agregarlos como coadministradores en su suscripción de Azure.

Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con su cuenta de Microsoft Azure (use la cuenta que está asociada a la suscripción de Azure).

1. En el panel de navegación, haga clic en **Configuración** y luego en **Administradores**.
2. En la parte inferior de la ventana, haga clic en **Agregar**. 
3. En el cuadro de diálogo AGREGAR COADMINISTRADOR, escriba la dirección de correo electrónico de la persona a la que quiere agregar como coadministrador y luego seleccione la suscripción a la que quiere que el coadministrador tenga acceso.
4. Haga clic en **Guardar**.

### <a name="access-for-users-of-classic-web-services"></a>Acceso por parte de los usuarios de servicios web clásicos
Para administrar un área de trabajo:

Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) con su cuenta de Microsoft Azure (use la cuenta que está asociada a la suscripción de Azure).

1. En el panel de servicios de Microsoft Azure, haga clic en **APRENDIZAJE AUTOMÁTICO**.
2. Haga clic en el área de trabajo que desea administrar.
3. Haga clic en la pestaña **Configurar** .

En la pestaña Configuración, puede suspender el acceso al área de trabajo de Machine Learning haciendo clic en **DENEGAR**. Los usuarios ya no podrán abrir el área de trabajo en Estudio de aprendizaje automático. Para restaurar el acceso, haga clic en **PERMITIR**.

Para usuarios específicos:

Para administrar cuentas adicionales que dispongan de acceso al área de trabajo de Machine Learning Studio, haga clic en **Iniciar sesión en ML Studio** en la pestaña **PANEL**. De esta manera, se abre el área de trabajo en Estudio de aprendizaje automático. Desde aquí, haga clic en la pestaña **CONFIGURACIÓN** y luego en **USUARIOS**. Puede hacer clic en **INVITAR MÁS USUARIOS** para permitir el acceso a otros usuarios al área de trabajo. También puede seleccionar un usuario y hacer clic en **QUITAR**.

> [!NOTE]
> El vínculo **Iniciar sesión en ML Studio** permite abrir Machine Learning Studio mediante la cuenta Microsoft en la que haya iniciado la sesión actual. La cuenta de Microsoft que usó para iniciar sesión en el Portal de Azure clásico para crear un área de trabajo no tiene automáticamente permiso para abrir el área de trabajo. Para abrir un área de trabajo, debe iniciar sesión en la cuenta de Microsoft que se definió como propietaria del área de trabajo. También puede hacerlo si recibe una invitación del propietario para unirse al área de trabajo.
> 
> 

