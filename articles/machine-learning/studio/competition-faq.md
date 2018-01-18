---
title: Preguntas frecuentes sobre las competiciones de Cortana Intelligence | Microsoft Docs
description: Preguntas frecuentes sobre las competiciones de Microsoft Cortana Intelligence.
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 9bac5154-a56c-4e78-9d67-34368b9d1624
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: haining;garye
ms.openlocfilehash: f7c839a8471dc54daebc47d0bb5a450358f5250d
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="microsoft-cortana-intelligence-competitions-faq"></a>Preguntas frecuentes sobre las competiciones de Microsoft Cortana Intelligence
**¿En qué consisten las competiciones de Cortana Intelligence?**

Las competiciones de Microsoft Cortana Intelligence unen a una comunidad global de aficionados a los datos para resolver de forma colectiva algunos de los problemas de la ciencia de datos más complejos del mundo. Las competiciones de Cortana Intelligence permiten a los aficionados a los datos de todo el mundo competir y generar modelos de ciencia de datos inteligentes y de alta precisión. Estas competiciones hospedadas están basadas en conjuntos de datos exclusivos que se han hecho públicos por primera vez. Los participantes pueden ganar premios u obtener reconocimiento a través de diez marcadores públicos principales. Puede acceder a la página principal de competiciones en [aka.ms/CIComp](http://aka.ms/CIComp).

**¿Con qué frecuencia publica Microsoft nuevas competiciones?**

Iniciaremos las competiciones propias de Microsoft de forma periódica, aproximadamente cada 8-12 semanas. 

**¿Dónde puedo exponer preguntas generales sobre la ciencia de datos?**

Para preguntas generales, puede usar el [foro de Microsoft Azure Machine Learning](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**¿Cómo puedo participar en una competición?**

Acceda a la página principal de las [competiciones](https://gallery.cortanaintelligence.com/competitions) a través de la [Galería de Azure AI](https://gallery.cortanaintelligence.com/) o vaya a [http://aka.ms/CIComp](http://aka.ms/CIComp). En la página principal se enumeran todas las competiciones en curso. Cada competición incluye instrucciones detalladas, así como las reglas de participación, información sobre los premios y la duración en su propia página de registro.

1. Busque la competición en la que le gustaría participar, lea todas las instrucciones y vea el vídeo tutorial. Después, haga clic en el botón **Enter Competition** (Entrar en la competición) y copie el experimento de inicio en el área de trabajo existente de Azure Machine Learning. Si ya no tiene acceso a ninguna área de trabajo, deberá crear una de antemano. Ejecute el experimento de inicio, observe las métricas de rendimiento y use su creatividad para mejorar el rendimiento del modelo. Probablemente empleará la mayor parte del tiempo en este paso.   

2. Cree un experimento predictivo a partir del modelo entrenado del experimento de inicio. Después, ajuste con cuidado el esquema de entrada y de salida del servicio web para asegurarse de que cumplen con el requisito especificado en la documentación de la competición. El documento del tutorial generalmente tiene instrucciones detalladas sobre ello. También puede ver el vídeo del tutorial, si está disponible.   

3. Implemente un servicio web a partir de su experimento predictivo. Pruebe el servicio web pulsando el botón **Test** (Probar) o con la plantilla de Excel que se genera automáticamente para asegurarse de que funciona correctamente.   

4. Envíe el servicio web como entrada de la competición y podrá ver la puntuación pública que obtiene en la página de la competición de la Galería de Azure AI. Si consigue aparecer en el marcador, celébrelo como se merece.  

Después de enviar una entrada correctamente, puede volver al experimento de inicio copiado. A continuación, itere y actualice el experimento predictivo, actualice el servicio web y envíe una nueva entrada.   

**¿Puedo usar herramientas de código abierto para participar en estas competiciones?**

Los participantes de la competición usan Azure Machine Learning Studio, un servicio basado en la nube dentro de Cortana Intelligence Suite, para desarrollar modelos de ciencia de datos y crear entradas para participar en la competición. Machine Learning Studio no solo proporciona una interfaz gráfica de usuario para crear experimentos de aprendizaje automático, sino que también permite aprovechar sus propios scripts de R o Python para la ejecución nativa. Los entornos de ejecución de R y Python en Studio incluyen un amplio conjunto de paquetes de código abierto de R y Python. También puede importar sus propios paquetes como parte del experimento. Studio también incluye un servicio integrado de Jupyter Notebook para que pueda llevar a cabo exploración de datos de estilo libre. Por supuesto, siempre puede descargar los conjuntos de datos usados en la competición y explorarlos en su herramienta favorita fuera de Machine Learning Studio. 

**¿Es necesario ser un experto en datos para participar?**

Nº De hecho, animamos a participar en el concurso a los aficionados a los datos, a los curiosos y a cualquier otro aspirante a científico de datos. Hemos diseñado los documentos de ayuda para que todos los usuarios puedan competir. El público de destino es el siguiente:

* **Desarrolladores de datos**, **científicos de datos**, **profesionales de BI** y **de análisis**: los responsables de producir el contenido de datos y análisis para que otros usuarios lo consuman.
* **Administradores de datos**: los usuarios que tengan conocimientos sobre los datos, lo que significan y cómo están diseñados para usarse y con qué propósito.
* **Estudiantes** & e**investigadores:** los usuarios que estén aprendiendo y adquiriendo habilidades relacionadas con los datos a través de programas académicos en universidades o participantes de los cursos en línea abiertos y masivos (MOOC).

**¿Puedo participar con mis compañeros y formar un equipo?**

La plataforma de competición no admite actualmente la participación de equipos. Todas las entradas de la competición están asociadas con una única identidad de usuario. 

**¿Tengo que pagar para participar en una competición?**

La participación en las competiciones es gratuita. Sin embargo, necesita tener acceso a un área de trabajo de Azure Machine Learning para participar. Puede crear una gratuita sin necesidad de usar una tarjeta de crédito iniciando sesión con una cuenta de Microsoft válida o una cuenta de Office 365. Si ya es un cliente de Azure o de Cortana Intelligence Suite, puede crear y usar un área de trabajo estándar con la misma suscripción de Azure. Si desea adquirir una suscripción de Azure, vaya a la página [Precios de Azure](https://azure.microsoft.com/pricing). Tenga en cuenta que se aplicarán las tasas estándar si se usa un área de trabajo estándar para construir experimentos. Para más información, vea la [información sobre precios de Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**¿Qué son las puntuaciones públicas y privadas?**

En la mayoría de las competiciones, recibirá una puntuación pública por cada envío que realice, generalmente 10-20 minutos después de cada envío. Sin embargo, una vez finalizada la competición, recibirá una puntuación privada que se usa para la clasificación final. 

Esto es lo que sucede:

* El conjunto de datos completo que se usa en la competición se divide al azar por estratificación entre datos de entrenamiento y datos de prueba (el resto). La división aleatoria se estratifica para asegurarnos de que la distribución de las etiquetas de los datos de entrenamiento y los de pruebas sea coherente.
* Los datos de entrenamiento se cargan y se proporcionan a los usuarios como parte del experimento inicial en la configuración del módulo Importar datos.
* Además, los datos de prueba se dividen en datos de pruebas públicas y privadas con la misma estratificación.
* Los datos de pruebas públicas se utilizan para la ronda inicial de puntuaciones. El resultado se conoce como la puntuación pública. Es lo que ve en el historial de envío cuando envía la entrada. Esta puntuación se calcula para cada entrada que se envía. Esta puntuación pública se utiliza para clasificarle en el marcador público.
* Los datos de pruebas privadas se utilizan para la ronda final de puntuación una vez finalizada la competición. Esto se conoce como puntuación privada. 
* Para cada participante se selecciona automáticamente un número fijo de entradas con las puntuaciones públicas más altas para entrar en la ronda de puntuación privada (este número puede variar en función de la competición). Finalmente, se selecciona la entrada con la puntuación privada más alta para entrar en la clasificación final, que en última instancia determina quiénes son los ganadores del premio.  

**¿Los clientes pueden hospedar una competición en nuestra plataforma?**

Agradecemos a las organizaciones de terceros que se asocien con nosotros y hospeden competiciones públicas y privadas en nuestra plataforma. Contamos con un equipo de incorporación de competiciones que estará encantado de analizar el proceso de incorporación para tales competiciones.  Póngase en contacto con nosotros en [compsupport@microsoft.com](mailto:compsupport@microsoft.com) para obtener más detalles. 

**¿Cuáles son las limitaciones de los envíos?**

En una competición típica, se puede limitar el número de entradas a las que se puedan enviar a un intervalo de 24 horas (de las 00:00:00 a las 23:59:59 hora UTC) y al número total de entradas que pueda enviar durante el tiempo que dure la competición. Recibirá los mensajes de error correspondientes cuando se supere una limitación. 

**¿Qué sucede si gano una competición?**

Microsoft comprobará los resultados del marcador privado y, a continuación, nos pondremos en contacto con usted. Asegúrese de que la dirección de correo electrónico de su perfil de usuario está actualizada.

**¿Cómo recibiré el dinero del premio si gano una competición?**

Si ganado una competición, debe firmar un formulario de consentimiento, licencia y declaración de idoneidad. En este formulario vuelven a aparecer las normas de la competición. Los ganadores deben rellenar un formulario de impuestos W-9, o bien un formulario W-8BEN si no son contribuyentes de los Estados Unidos. Nos pondremos en contacto con todos los ganadores como parte del proceso de desembolso de los premios y para ello usaremos el correo electrónico con el que se registraron. Consulte los [Términos y condiciones](http://aka.ms/comptermsandconditions) para obtener más detalles.

**¿Qué ocurre si más de una entrada recibe la misma puntuación?**

La hora de envío es el factor de desempate. La entrada que se haya enviado antes superará a la entrada que se haya enviado más tarde.

**¿Puedo participar usando el área de trabajo de invitado?**

Nº Debe utilizar un área de trabajo gratuito o estándar para participar. Puede abrir el experimento de inicio de la competición en un área de trabajo de invitado, pero no podrá crear una participación válida para su envío desde dicha área. 

**¿Puedo participar mediante un área de trabajo de cualquier región de Azure?**

Actualmente, la plataforma de la competición solo admite las participaciones enviadas desde un área de trabajo de la región de Azure correspondiente al **centro-sur de EE. UU.** Todas las áreas de trabajo residen en la región centro-sur de EE. UU., para que pueda enviar una entrada desde cualquier área de trabajo gratuita. Si decide usar un área de trabajo estándar, solo tiene que comprobar que se encuentra en la región centro-sur de EE. UU. de Azure; en caso contrario, el envío no se realizará correctamente. 

**¿Se conservan las participaciones y soluciones de las competiciones de los usuarios?**

Solo se conservan las entradas de los usuarios para fines de evaluación para identificar las soluciones ganadoras. Consulte los [Términos y condiciones](http://aka.ms/comptermsandconditions) para obtener información específica.

