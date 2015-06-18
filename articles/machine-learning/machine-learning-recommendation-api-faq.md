<properties 
	pageTitle="Preguntas más frecuentes (P+F) sobre cómo configurar y utilizar la API de recomendaciones de Aprendizaje automático | Azure" 
	description="Preguntas frecuentes de la API de RECOMENDACIONES de Microsoft creada con el aprendizaje automático de Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2014" 
	ms.author="jaymathe"/> 

# Preguntas más frecuentes (P+F) sobre cómo configurar y utilizar la API de recomendaciones de Aprendizaje automático
Versión 1.0

Tabla de contenido

* ¿Qué son las recomendaciones?	
* ¿Qué puedo hacer con las recomendaciones?	
* ¿Existen limitaciones durante el período de lanzamiento?	
* Compra y facturación	
	* ¿Cuánto cuestan las recomendaciones durante el período de lanzamiento?	
	* ¿Hay algún coste asociado con el seguimiento de las recomendaciones o con el almacenamiento de la actividad del usuario?	
	* ¿Tienen las recomendaciones una prueba gratuita?	
	* ¿Cuándo termina el período de lanzamiento?	
	* ¿Cuánto costará cuando esté disponible con carácter general?	
	* ¿Cuánto se me facturará por las recomendaciones?	
	* ¿Cómo actualizo a los servicios de nivel superior?	
	* ¿Cuándo finalizará mi suscripción a las recomendaciones?	
	* ¿Cómo cancelo mi suscripción a las recomendaciones?	
* Introducción a las recomendaciones	
	* ¿Me van a servir las recomendaciones?	
	* ¿Cuáles son los requisitos previos para configurar las recomendaciones?	
	* ¿Cómo configuro las recomendaciones por primera vez?	
	* ¿Dónde puedo encontrar documentación de API?	
	* ¿Qué opciones dispongo para cargar datos de uso y de catálogo en las recomendaciones?	
* Mantenimiento y soporte técnico	
	* ¿Qué volumen pueden tener mis datos?	
	* ¿Dónde puedo obtener soporte técnico para las recomendaciones?	
	* ¿Dónde puedo encontrar los términos de uso?	
* Información legal	


## ¿Qué son las recomendaciones?
Para las organizaciones y empresas que dependen de las recomendaciones para la venta cruzada y oportunidades de venta a sus clientes, las recomendaciones de Aprendizaje automático de Microsoft Azure son un motor de recomendaciones de autoservicio en Azure. Es una implementación del filtrado de colaboración mediante la factorización matricial como algoritmo básico. Es accesible a los desarrolladores de aplicaciones como una API a través de una interfaz HTTP de RESTful. Las recomendaciones están actualmente en versión preliminar pública, a punto de convertirse en un servicio de disponibilidad general.

## ¿Qué puedo hacer con las recomendaciones?
Las recomendaciones toman como entrada un elemento o un conjunto de elementos y devuelven una lista de recomendaciones pertinentes. Por ejemplo: un cliente de un minorista en línea hace clic en un producto. El minorista en línea envía ese producto como entrada a las recomendaciones, recibe una lista de productos y decide cuál de estos productos se mostrará al cliente. Puede que desee utilizar las recomendaciones para optimizar la tienda en línea o incluso para informar al departamento de ventas interno o centro de llamadas.


## ¿Existen limitaciones durante el período de lanzamiento?

* Número máximo de modelos por suscripción: 10
* Número máximo de elementos que puede contener un catálogo: 100,000
* El tamaño máximo de datos que puede enviarse en POST (por ejemplo, importar datos de catálogo, importar datos de uso) es de 200 MB
* El número de transacciones por segundo para una compilación de modelo de recomendación que no esté activa es de ~2TPS; solo la compilación de modelo de recomendación que está activa puede almacenar hasta 20TPS

## Compra y facturación 
Aquí encontrará más información acerca de las suscripciones.

###¿Cuánto cuestan las recomendaciones durante el período de lanzamiento?
Las recomendaciones son un servicio basado en suscripción. El pago se realiza en función del volumen de transacciones al mes. Durante el período de inicio el servicio es gratuito y está restringido a 100 000 transacciones al mes.

###¿Hay algún coste asociado con el seguimiento de las recomendaciones o con el almacenamiento de la actividad del usuario?
No en este momento.

###¿Tienen las recomendaciones una prueba gratuita?
Durante el período de inicio el servicio es gratuito y está restringido a 100 000 transacciones al mes.

###¿Cuándo termina el período de lanzamiento?
Está previsto que el período de inicio y sus precios duren varios meses. Notificaremos a nuestros usuarios suscritos sobre los próximos cambios en el estado y el precio.

###¿Cuánto costará cuando esté disponible con carácter general?
Los precios de Recomendaciones cuando esté disponible con carácter general están aún por determinar. En caso de un cambio en el precio, se les notificará a los suscriptores.

###¿Cuánto se me facturará por las recomendaciones?
Una suscripción de pago es cualquier suscripción para la que hay que pagar una cuota mensual. Al comprar una suscripción de pago, se le cobrará inmediatamente por el uso del primer mes. Se le cobrará la cantidad que se muestra al lado de la oferta en la página de suscripción (además de los impuestos correspondientes). Este cargo mensual se realiza cada mes en la misma fecha que la compra original hasta que se cancela la suscripción. Haga clic aquí para obtener más información acerca de las suscripciones.

###¿Cómo actualizo a los servicios de nivel superior?
Durante el período de lanzamiento, el servicio se limita a 100 000 transacciones por mes y no se puede actualizar a un nivel superior.

Cuando las recomendaciones están disponibles con carácter general, si observa que su suscripción no proporcionará suficientes transacciones, puede actualizar a una suscripción con un límite superior de transacciones.

Al actualizar una suscripción:

* Las transacciones que permanecen en su suscripción anterior no se agregan a la nueva suscripción. 
* Pagará el precio completo de la nueva suscripción, aunque tenga transacciones no utilizadas en su suscripción anterior.

Proceso para actualizar una suscripción

* Inicie sesión en Marketplace.
* Haga clic en la pestaña Mis datos.
* Haga clic en Usar junto a la suscripción que desea actualizar. 
* En el panel derecho se muestran todas las suscripciones disponibles con su suscripción atenuada. Haga clic en el botón de opción de la suscripción que desea actualizar.
* Si desea actualizar, haga clic en Aceptar en el cuadro de diálogo. Si no desea actualizar, haga clic en Cancelar.

IMPORTANTE: Lea detenidamente el cuadro de diálogo antes de actualizar, ya que hay implicaciones de facturación y uso.

Haga clic [aquí](http://msdn.microsoft.com/library/gg312164.aspx) para obtener más información acerca de las suscripciones.

###¿Cuándo finalizará mi suscripción a las recomendaciones?
La suscripción finalizará cuando la cancele. Si desea cancelar las suscripciones, consulte las instrucciones siguientes.

###¿Cómo cancelo mi suscripción a las recomendaciones?
Para cancelar la suscripción, siga estos pasos. Si su suscripción actual es una suscripción de pago, la suscripción seguirá en vigor hasta el final del período de facturación actual. Si necesita que la cancelación sea afectiva inmediatamente, póngase en contacto con nosotros [aquí](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

No hay ningún reembolso si cancela antes del final del período de facturación ni por transacciones no utilizadas en un período de facturación.

* Inicie sesión en Marketplace.
* Haga clic en la pestaña Mis datos.
* Busque la suscripción que desea cancelar.
* Haga clic en Cancelar a la derecha del nombre y del estado del conjunto de datos. Puede usar esta suscripción hasta que llegue al final del período de facturación actual o se alcance el límite de la transacción, lo que ocurra primero.

Si desea cancelar su suscripción inmediatamente, para poder adquirir una nueva suscripción, haga clic [aquí](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn) para presentar una incidencia a nuestro soporte al cliente.


## Introducción a las recomendaciones

###¿Me van a servir las recomendaciones? 
Las recomendaciones de Aprendizaje automático de Microsoft Azure se aconsejan para aquellas organizaciones y empresas que dependen de recomendaciones para la venta cruzada y oportunidades de venta a sus clientes. Si tiene un sitio web orientado a clientes, un equipo de ventas, un equipo de ventas interno o un centro de llamadas y si ofrece un catálogo varias docenas de productos o servicio, su balance pueden beneficiarse de utilizar las recomendaciones. La experimentación con recomendaciones está pensada para ser bastante sencilla. La versión actual basada en API no requiere conocimientos básicos de programación. En caso de que necesite asistencia, póngase en contacto con el proveedor que ha desarrollado su sitio web. Si cuenta con un departamento de TI interno o un desarrollador interno, deben conseguir que las recomendaciones trabajen para usted. 

###¿Cuáles son los requisitos previos para configurar las recomendaciones?
Las recomendaciones requieren que cuente con un registro de opciones de usuario en relación a su catálogo. Si no dispone de un registro de este tipo y cuenta con un sitio web orientado al cliente, las recomendaciones pueden recopilar la actividad de los usuarios por usted. Las recomendaciones también requieren un catálogo de sus productos o servicios. Si no tiene un catálogo, las recomendaciones podrán utilizar los datos de uso de clientes reales y elaborar un catálogo. Un catálogo "implícito" no incluirá artículos que no estaban "notificados" como parte de transacciones de usuario.

###¿Cómo configuro las recomendaciones por primera vez?
Después de suscribirse a las recomendaciones, debe usar la documentación de API   [aquí](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ) para configurar el servicio.

###¿Dónde puedo encontrar documentación de API? 
La documentación de API está [aquí](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ).

###¿Qué opciones dispongo para cargar datos de uso y de catálogo en las recomendaciones?
Tiene dos opciones para cargar los datos de uso y del catálogo: exportar estos datos desde el sistema CRM u otros registros y cargarlos en las recomendaciones, o puede agregar etiquetas al sitio web que realizará el seguimiento de las actividades del usuario. Si opta por esto último, los datos se almacenan en Azure.

##Mantenimiento y soporte técnico

###¿Qué volumen pueden tener mis datos?
Cada conjunto de datos puede contener hasta 100 000 elementos del catálogo y hasta 2048 MB de datos de uso.
Además, una suscripción puede contener hasta 10 conjuntos de datos (modelos).

###¿Dónde puedo obtener soporte técnico para las recomendaciones?
El soporte técnico está disponible [aquí](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MachineLearning).

###¿Dónde puedo encontrar los términos de uso?

Los términos de uso están disponibles [aquí](https://datamarket.azure.com/dataset/amla/recommendations#terms).

#Información legal
Este documento se proporciona "como está". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. 
Algunos ejemplos mencionados se proporcionan únicamente con fines ilustrativos y son ficticios. No se pretende ninguna asociación o conexión real ni debe deducirse. 
Este documento no proporciona ningún derecho legal a la propiedad intelectual de ningún producto de Microsoft. Puede copiar y usar este documento con fines internos y de referencia. 
(c) 2014 Microsoft. Todos los derechos reservados. 



<!--HONumber=46--> 

<!--HONumber=46--> 
 