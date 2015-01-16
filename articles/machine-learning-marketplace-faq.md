<properties title="FAQ for publishing and using Machine Learning apps in the Azure Marketplace" pageTitle="Preguntas más frecuentes sobre la publicación y utilización de aplicaciones de Aprendizaje automático en Azure Marketplace | Azure" description="Preguntas más frecuentes" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 

#Preguntas más frecuentes sobre la publicación y utilización de aplicaciones de Aprendizaje automático en Azure Marketplace

##Acerca del consumo en Marketplace


###Pregunta 1: Me aparece el error siguiente después de escribir la entrada del servicio web. ¿A qué se debe el error?
La solicitud ha ocasionado un tiempo de espera de back-end o un error de back-end. El equipo está investigando el problema. Lamentamos los inconvenientes. (500)
Respuesta: Los parámetros de entrada no pueden ajustarse al formato requerido para el servicio web específico. Consulte el vínculo correspondiente de la documentación para encontrar el formato correcto de los parámetros de entrada y las limitaciones de este servicio web.

###Pregunta 2: Si copio el vínculo de la API del servicio web que aparece en "Explorar este conjunto de datos" y lo pego en otra ventana del explorador / pestaña, ¿qué credenciales tengo que usar para obtener acceso a los resultados y cómo puedo verlos?
Respuesta: Debe usar la cuenta de Marketplace como nombre de usuario y la clave de la cuenta principal como contraseña. La clave de la cuenta principal se puede encontrar en la página "explorar este conjunto de datos" en la descripción del servicio web (haga clic en el botón "Mostrar"). El resultado se puede mostrar en el explorador o estar disponible para descargarlo, en función de qué explorador utilice.

###Pregunta 3: Aparece el error siguiente después de escribir la entrada para el servicio web en "explorar este conjunto de datos". ¿A qué se debe el error?
Se ha producido un error inesperado al procesar la solicitud. Vuelva a intentarlo.
Respuesta: Uno o varios parámetros de entrada del servicio web pueden haber excedido el límite de longitud al consumir el servicio web en la página "explorar este conjunto de datos" de Marketplace. Se puede llamar a los servicios con una longitud entrada mayor mediante la utilización de los métodos POST HTTP; el código de ejemplo se publica en los servicios de ejemplo descritos aquí: http://azure.microsoft.com/es-es/documentation/articles/machine-learning-r-csharp-web-service-examples/.

###

##Acerca de la publicación del Aprendizaje automático de Microsoft Azure en Marketplace

###Pregunta 1: ¿Por qué mis logotipos, imágenes y números de transacciones no se actualizan en mi servicio web? 
Respuesta: Hay un almacenamiento en caché de imágenes o logotipos en el portal de publicación y la actualización del nuevo logotipo o de la nueva imagen en el portal puede tardar hasta 10 días en completarse.

###Pregunta 2: ¿Por qué aparece un error en la "pestaña Detalles" del servicio web en la oferta de Marketplace?
Respuesta: Hay un problema conocido de Marketplace al conectarse al Aprendizaje automático de Microsoft Azure para obtener detalles del servicio. El equipo está trabajando para resolver este problema.

###Pregunta 3: ¿Por qué no funciona el código de ejemplo R en los servicios web del Aprendizaje automático de Microsoft Azure para consumir los servicios web en Marketplace?
Respuesta: Los sistemas de autenticación son diferentes al conectarse a servicios web del Aprendizaje automático de Microsoft Azure directamente en comparación con la conexión a estos servicios web a través de Marketplace. Los servicios de Marketplace son servicios de OData y se les puede llamar con los métodos GET o POST. 

###Pregunta 4: ¿Por qué los vínculos de soporte técnico de las ofertas del servicio web no se actualizan correctamente para algunas de mis ofertas?
Respuesta: Los vínculos de soporte técnico son globales por publicador y no por la oferta. 

###Pregunta 5: ¿Cómo puedo publicar un servicio web con el modo de entrada por lotes en Marketplace?
Respuesta: El modo de entrada por lotes no se admite actualmente en los servicios web de Marketplace.

###Pregunta 6: ¿Con quién debo comunicarme para obtener ayuda si tengo preguntas sobre cómo convertirme en un publicador de datos o si tengo problemas durante la publicación?
Respuesta: Póngase en contacto con el equipo de Marketplace en datamarketbd@microsoft.com para obtener más información.





