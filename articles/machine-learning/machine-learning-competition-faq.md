<properties
	pageTitle="Preguntas más frecuentes sobre la competición de Aprendizaje automático | Microsoft Azure"
	description="Preguntas más frecuentes sobre las competiciones de Aprendizaje automático de Microsoft Azure."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="haining;chlovel;garye"/>

# Preguntas más frecuentes sobre la competición de Aprendizaje automático de Microsoft Azure

**¿Dónde puedo exponer preguntas generales sobre la ciencia de datos?**

Utilice nuestro [foro de Aprendizaje automático de Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**¿Cómo puedo participar en una competición?**

Cree una cuenta gratuita o de pago en [Aprendizaje automático de Microsoft Azure](https://studio.azureml.net/?selectAccess=true&o=2%22%20\t%20%22_blank). Siga el tutorial de la competición y vea el vídeo breve.

**¿Es necesario ser un experto en datos para participar?**

No. De hecho, animamos a participar a los aficionados a los datos, a los curiosos y a cualquier persona que todavía no sea experta en ese campo. Hemos diseñado los documentos de ayuda para que todos los usuarios puedan competir.

**¿Cuánto durará?**

Puede acceder a la competición y crear un modelo de datos en cuestión de 10 a 15 minutos. El perfeccionamiento de los experimentos predictivos puede tardar más de 15 minutos, en función de su grado de familiarización con el aprendizaje automático.

**¿Cómo se calcula la puntuación? ¿Se calculará de forma diferente mientras se lleve a cabo la competición y una vez que se haya cerrado? ¿Cuáles son las puntuaciones públicas y privadas?**

1.  Todo el conjunto de datos se ha dividido al azar por estratificación entre datos de entrenamiento (un 60 %) y datos de prueba (el 40 % restante). La división aleatoria se estratifica en geografía + segmento + subgrupo para asegurarnos de que la distribución de las etiquetas de los datos de entrenamiento y los de pruebas sea coherente en cada región.  

2.  Los datos de entrenamiento se han cargado y se han proporcionado a los usuarios como parte del experimento inicial en la configuración del módulo Lector.

3.  Además, los datos de prueba se han dividido en datos de pruebas públicas y privadas, con la misma estratificación (un 60 % para las pruebas públicas y un 40 % para las pruebas privadas).

4.  Los datos de pruebas públicas se utilizan para el redondeo inicial de la puntuación. El resultado se conoce como puntuación pública y es el que obtiene en el historial de envío tras enviar la entrada. Esto se realiza cada vez que envía una entrada. Esta puntuación pública se utiliza para clasificarle en el marcador público.

5.  Los datos de pruebas privadas se utilizan para el redondeo final de la puntuación una vez finalizada la competición. Esto se conoce como puntuación privada.

6.  Para cada participante, se han seleccionado automáticamente las cinco entradas con las puntuaciones públicas más altas para calcular las puntuaciones privadas. Se ha seleccionado la entrada con la máxima puntuación privada para especificar la clasificación final, que es la que, en última instancia, determina los ganadores.

**¿Debo pagar por esto?**

No, puede configurar un área de trabajo gratuita de Aprendizaje automático de Microsoft Azure para competir. Recuerde que las cuentas de invitado no tienen la capacidad de guardar los datos, por lo que debe enviar la entrada antes de llegar a las 8 horas gratuitas.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**¿Cómo realizo la actualización a una suscripción de pago?**

Puede comprar o actualizar su suscripción desde esta página de Microsoft Azure Marketplace. Consulte [esta página](https://azure.microsoft.com/pricing/details/machine-learning/) de preguntas más frecuentes sobre precios.

**¿Qué es el botón Probar Belize?**

(Incluir las instrucciones del portal de servicios web y la captura de pantalla con los pasos para volver atrás)

**¿Qué sucede si gano una competición?**

Microsoft comprobará los resultados del marcador privado y, a continuación, nos pondremos en contacto con usted. Asegúrese de que la dirección de correo electrónico de su perfil de usuario está actualizada.

**¿Cómo recibiré el dinero del premio si gano una competición?**

Si ganado una competición, debe firmar un formulario de consentimiento, licencia y declaración de idoneidad. En este formulario vuelven a aparecer las normas de la competición. Los ganadores deben rellenar un formulario de impuestos W-9, o bien un formulario W-8BEN si no son contribuyentes de los Estados Unidos.

<!---HONumber=AcomDC_0309_2016-->