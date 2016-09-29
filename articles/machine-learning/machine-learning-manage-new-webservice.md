<properties
	pageTitle="Administración de servicios web nuevos de Aprendizaje automático | Microsoft Azure"
	description="Administrar el acceso a las áreas de trabajo del aprendizaje automático de Azure, e implementar y administrar servicios web de la API del aprendizaje automático"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-donglo"/>


# Administración de servicios web nuevos de Aprendizaje automático

> [AZURE.NOTE] Los procedimientos descritos en este artículo son pertinentes para los servicios web nuevos de Aprendizaje automático de Azure. Si quiere obtener más información sobre cómo administrar los servicios web clásicos, consulte [Administración de un área de trabajo de Aprendizaje automático de Azure](machine-learning-manage-workspace.md).

Los servicios web nuevos de Aprendizaje automático pueden administrarse mediante el portal de servicios web de Aprendizaje automático de Microsoft Azure. Puede:

- Supervisar el uso del servicio web.
- Configurar la descripción, actualizar las claves del servicio web, actualizar la clave de la cuenta de almacenamiento, y habilitar o deshabilitar datos de ejemplo.
- Eliminar el servicio web.
- Crear, eliminar o actualizar planes de facturación.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> [AZURE.TIP] En Estudio de aprendizaje automático de Azure, en la pestaña **Servicios web**, puede agregar, actualizar o eliminar un servicio web de Aprendizaje automático.

Para administrar los servicios web, siga estos pasos:

1.	Inicie sesión en el [portal de servicios web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) con su cuenta de Microsoft Azure (use la que está asociada a la suscripción de Azure).
2.	En el menú, haga clic en **Servicios web**.

Se mostrará una lista de los servicios web implementados de la suscripción. Para administrar un servicio web, haga clic en el nombre en la lista para abrir la página del servicio web.

En la página Servicios web, puede llevar a cabo estas acciones:

- Hacer clic en el servicio web para administrarlo
- Hacer clic en el plan de facturación del servicio web para actualizarlo
- Eliminar un servicio web
- Copiar un servicio web en otra región
- Copiar un servicio web e implementarlo en otra región

Al hacer clic en un servicio web, se abrirá la página Inicio rápido del servicio web. Esta página tiene dos opciones de menú con las que puede administrase el servicio web:

- **PANEL**: permite ver el uso del servicio web.
- **CONFIGURAR**: permite agregar texto descriptivo, activar y desactivar el registro de errores, actualizar la clave de la cuenta de almacenamiento asociada con el servicio web, y habilitar y deshabilitar datos de ejemplo.

## Supervisión del uso del servicio web

Haga clic en la pestaña **Panel**.

En el panel puede ver el uso general del servicio web durante un periodo. Puede seleccionar el periodo para verlo en el menú desplegable Periodo situado en la esquina superior derecha de los gráficos de uso. El panel muestra la siguiente información:

- **Requests Over Time** (Solicitudes a lo largo de un periodo) muestra un gráfico de escalera del número de solicitudes durante el periodo seleccionado. Puede ayudarlo a identificar si se están experimentando picos de uso.
- **Request-Response Requests** (Solicitudes de solicitud-respuesta) muestra el número total de llamadas de solicitud-respuesta que ha recibido el servicio durante el periodo seleccionado y cuántas tienen errores.
- **Average Request-Response Compute Time** (Tiempo de proceso medio de solicitud-respuesta) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Batch Requests** (Solicitudes por lotes) muestra el número total de solicitudes por lotes que ha recibido el servicio en el periodo seleccionado y cuántas tienen errores.
- **Average Job Latency** (Latencia media de los trabajos) muestra un promedio del tiempo necesario para ejecutar las solicitudes recibidas.
- **Errores** muestra el número total de errores que se han producido en las llamadas al servicio web.
- **Services Costs** (Costos de los servicios) muestra los cargos correspondientes al plan de facturación asociado con el servicio.

## Configuración del servicio web ##

Haga clic en la opción de menú **CONFIGURAR**.

Puede actualizar las propiedades siguientes:

* **Descripción** permite escribir una descripción del servicio web.
* **Título** permite escribir un título para el servicio web
* **Claves** permite alternar las claves de API primarias y secundarias.
* **Clave de cuenta de almacenamiento** permite actualizar la clave de la cuenta de almacenamiento asociada con los cambios del servicio web.
* **Habilitar datos de ejemplo** permite proporcionar datos de ejemplo que pueden usarse para probar el servicio de solicitud-respuesta. Si ha creado el servicio web en Estudio de aprendizaje automático de Microsoft Azure, los datos de ejemplo se toman de los utilizados para entrenar el modelo. Si ha creado el servicio mediante programación, los datos se toman de los de ejemplo proporcionados como parte del paquete JSON.

## Administración de planes de facturación

Haga clic en la opción de menú **Planes** de la página Inicio rápido de los servicios web. También puede hacer clic en el plan asociado con el servicio web específico para administrar dicho plan.

* **Nuevo** permite crear un nuevo plan.
* **Add/Remove Plan instance** (Agregar/eliminar instancia de plan) permite escalar horizontalmente un plan existente para agregar capacidad.
* **Upgrade/DownGrade** (Actualizar/cambiar a una versión anterior) permite escalar verticalmente un plan existente para agregar capacidad.
* **Eliminar** permite eliminar un plan.

Haga clic en un plan para ver su panel. El panel proporciona una instantánea o el uso del plan durante un periodo seleccionado. Haga clic en el menú desplegable **Período** situado en la esquina superior derecha de la información del panel para seleccionar el periodo.

El panel de planes proporciona la siguiente información:

* **Descripción del plan** muestra información sobre los costos y la capacidad asociados con el plan.
* **Plan Usage** (Uso del plan) muestra el número de transacciones y las horas de proceso que se han cobrado en el plan.
* **Servicios web** muestra el número de servicios web que usan este plan.
* **Top Web Service By Calls** (Principales servicios web por llamadas) muestra los cuatro principales servicios web que están realizando llamadas que se cobran el plan.
* **Top Web Services by Compute Hrs** (Principales servicios web por horas de proceso) muestra los cuatro principales servicios web que están usando recursos de proceso que se cobran el plan.

<!---HONumber=AcomDC_0914_2016-->