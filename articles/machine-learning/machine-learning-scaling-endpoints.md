<properties 
	pageTitle="Escalado de extremos de API | Microsoft Azure" 
	description="Ampliación de extremos de servicio web en Aprendizaje automático de Azure" 
	services="machine-learning"
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="06/29/2015"
	ms.author="himad"/>


# Escalado de extremos de API

Los extremos del servicio web de Aprendizaje automático de Azure tienen niveles de limitación seleccionables para hacer coincidir con la velocidad a la que se utilizará el extremo.

Existen dos factores que controlan la cantidad de limitación en un extremo: los niveles de limitación Bajo o Alto. Solo los clientes que pagan tiene permiso para establecer el nivel de limitación en Alto con el número máximo de llamadas simultáneas: 4 para el límite de nivel bajo; 20-200 para el límite de nivel alto.


Las API sincrónicas se usan normalmente en situaciones en las que se desea una latencia baja. Latencia aquí implica el tiempo que tarda la API en completar una solicitud, sin contar los retrasos de red. Supongamos que tiene una API con una latencia de 50 ms. Para utilizar totalmente la capacidad disponible con nivel de limitación alto y un número máximo de llamadas simultáneas de 20, debe llamar a esta API 20 * 1000/50 = 400 veces por segundo. Al ampliar esto aún más, un número máximo de llamadas simultáneas de 200 le permitirá llamar a la API 4000 veces por segundo, suponiendo una latencia de 50 ms.

Si tiene previsto llamar a la API con una carga mayor de lo que permitirán las llamadas máximas simultáneas de 200, debe crear varios extremos en el mismo servicio web y distribuir aleatoriamente la carga entre todos ellos.

Tenga en cuenta que usar un recuento de simultaneidad muy alto puede ser perjudicial si no se llega a la API con una tasa elevada en consecuencia. Puede que vea tiempos de espera esporádicos o picos de latencia si pone una carga relativamente baja en una API configurada para una carga elevada.

Tenga en cuenta que modificar los ajustes de límite solo afecta al comportamiento de la API sincrónica (RRS). Debe ajustar estos valores si ve respuestas frecuentes de 503 servicio no disponible en la API sincrónica.

La interfaz de usuario de administración permite alternar el nivel de limitación. Para cambiar un número de simultaneidad personalizado a nivel alto de limitación, use la API de extremo de revisión.

- Abra manage.windowsazure.com.
- Vaya a la pestaña Aprendizaje automático.
- Haga clic en su área de trabajo.
- Navegue hasta el servicio web que tiene su extremo ![Vaya al servicio web.](./media/machine-learning-scaling-endpoints/figure-1.png).

- Haga clic en el extremo y, a continuación, haga clic en la ficha Configurar ![Vaya a Configuración de extremo.](./media/machine-learning-scaling-endpoints/figure-2.png).


- Cambie el nivel de limitación a Alto y haga clic en Guardar.


 

<!---HONumber=August15_HO6-->