<properties
   pageTitle="Escalado del servicio web | Microsoft Azure"
   description="Aprenda a aumentar la simultaneidad y a agregar nuevos puntos de conexión para escalar un servicio web."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="aprendizaje automático de azure, servicios web, operacionalización, escalado, punto de conexión, simultaneidad"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="07/06/2016"
   ms.author="neerajkh"/>

# Escalado de servicios web

>[AZURE.NOTE] En este tema se describen técnicas que se aplican a un servicio web clásico.

De manera predeterminada, cada servicio web publicado está configurado para admitir 20 solicitudes simultáneas y 200 solicitudes simultáneas como máximo. Aunque el Portal de Azure clásico proporciona una manera de establecer este valor, Aprendizaje automático de Azure optimiza automáticamente esta opción para brindar el mejor rendimiento para el servicio web y omite el valor del portal.

Si tiene previsto llamar a la API con una carga mayor de lo que permitirán las llamadas máximas simultáneas de 200, debe crear varios puntos de conexión en el mismo servicio web y distribuir aleatoriamente la carga entre todos ellos.

## Agregar puntos de conexión nuevos para el mismo servicio web

El escalado del servicio web es una tarea común, para admitir más de 200 solicitudes simultáneas, aumentar la disponibilidad a través de varios puntos de conexión o proporcionar un punto de conexión independiente para un consumidor distinto del servicio web. Puede aumentar la escala agregando más puntos de conexión para el mismo servicio web a través del [Portal de Azure clásico](https://manage.windowsazure.com/), tal y como se muestra en la ilustración siguiente:

Tenga en cuenta que usar un recuento de simultaneidad muy alto puede ser perjudicial si no se llega a la API con una tasa elevada en consecuencia. Puede que vea tiempos de espera esporádicos o picos de latencia si pone una carga relativamente baja en una API configurada para una carga elevada.

Las API sincrónicas se usan normalmente en situaciones en las que se desea una latencia baja. Latencia aquí implica el tiempo que tarda la API en completar una solicitud, sin contar los retrasos de red. Supongamos que tiene una API con una latencia de 50 ms. Para utilizar totalmente la capacidad disponible con nivel de limitación alto y un número máximo de llamadas simultáneas de 20, debe llamar a esta API 20 * 1000/50 = 400 veces por segundo. Al ampliar esto aún más, un número máximo de llamadas simultáneas de 200 le permitirá llamar a la API 4000 veces por segundo, lo que supone una latencia de 50 ms.

Vaya al [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en el icono Aprendizaje automático que se encuentra a la izquierda, seleccione el área de trabajo que se usa para publicar el servicio web, haga clic en el servicio web que desea, haga clic en **AGREGAR PUNTO DE CONEXIÓN** en el panel inferior y proporcione un nombre, una descripción y la simultaneidad deseada para el punto de conexión nuevo.

Para agregar puntos de conexión nuevos, consulte [Creación de puntos de conexión](machine-learning-create-endpoint.md).

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0720_2016-->