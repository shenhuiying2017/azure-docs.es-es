<properties
   pageTitle="P+F"
   description="Preguntas más frecuentes sobre Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# Preguntas más frecuentes sobre Power BI Embedded

## ¿Cuál es el anuncio más reciente acerca de Power BI Embedded?

El servicio Microsoft Power BI Embedded ahora está disponible con carácter general con el SLA de disponibilidad general. Para más información acerca de esta versión, consulte las [novedades](power-bi-embedded-whats-new.md).

## ¿Qué es Microsoft Power BI Embedded?

Microsoft Power BI Embedded ya está disponible con carácter general. Power BI Embedded es un servicio de Azure que permite a los desarrolladores de aplicaciones insertar impresionantes informes y visualizaciones completamente interactivos en las aplicaciones utilizadas por los clientes sin el tiempo y los gastos de tener que crear sus propios controles desde el principio. Ahora Power BI Embedded está disponible con SLA en nueve centros de datos de todo el mundo. También hemos mejorado funcionalidades en el servicio, como la compatibilidad con seguridad de los datos a través de la seguridad de nivel de fila (RLS) en Power BI Embedded para el filtrado avanzado. También hemos simplificado y actualizado el modelo de precios de Power BI Embedded. Para más información, consulte [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/).

## ¿Quién querría usar Microsoft Power BI Embedded y por qué?

Microsoft Power BI Embedded está pensado para los desarrolladores de aplicaciones que desean ofrecer a sus usuarios sorprendentes experiencias de visualización de datos interactiva en cualquiera de sus dispositivos sin tener que crearlas ellos mismos. Con Power BI Embedded, los desarrolladores pueden proporcionar vistas siempre actualizadas con consulta directa. Los desarrolladores también pueden implementar, administrar y automatizar Power BI mediante programación con las API de ARM de Azure y las API de Power BI. Al igual que ocurre con todo lo de Power BI, el servicio incrustado se escala automáticamente para satisfacer las necesidades y el uso de la aplicación. El servicio Power BI Embedded presenta un modelo de precios basado en el consumo de pago por uso.

## ¿Cómo se relaciona Power BI Embedded con el servicio Power BI?

Power BI Embedded y el servicio Power BI son ofertas diferentes. Power BI Embedded se caracteriza por un modelo de facturación basado en el consumo, se implementa mediante el Portal de Azure y está diseñado para permitir a los ISV insertar visualizaciones de datos en las aplicaciones para su uso por parte de los clientes. El servicio Power BI se factura e implementa mediante el portal de O365 y es una oferta de inteligencia empresarial independiente y de uso general destinada principalmente al uso empresarial interno. Para más información sobre el servicio Power BI, consulte [www.powerbi.com](https://powerbi.microsoft.com).

## ¿Cómo mejora Power BI Embedded mi aplicación?

Las aplicaciones son bastante más eficaces cuando se aprovechan las impresionantes visualizaciones de datos interactivas para informar de las decisiones de los usuarios directamente en la aplicación. Power BI Embedded le permite mejorar su aplicación con visualizaciones de datos completas, interactivas y siempre actualizadas, para que pueda aumentar la utilidad de su aplicación y la satisfacción y la fidelidad de los usuarios, y proporcionar análisis contextuales con facilidad en cualquier dispositivo.

## ¿Existen reglas o restricciones sobre el uso de Power BI Embedded en las aplicaciones?

Power BI Embedded está diseñado para aquellas aplicaciones proporcionadas para el uso de terceros. Si desea usar el servicio Power BI Embedded para crear una aplicación empresarial interna, cada uno de sus usuarios internos necesitará una USL de Power BI Pro y se cobrará a su organización su consumo del servicio Power BI Embedded además de sus tarifas de USL de Power BI Pro. Para evitar incurrir tanto las tarifas de USL de Power BI Pro como los gastos de consumo de las aplicaciones internas, el servicio Power BI ofrece sus propias funcionalidades de inserción de contenido fuera de Power BI Embedded sin costo adicional para los titulares de Power BI USL (dev.powerbi.com).

## ¿Se puede usar Power BI Embedded para crear aplicaciones internas?

No, Power BI Embedded solo está previsto para su uso por usuarios externos y no puede usarse en aplicaciones empresariales internas. Por ello, para insertar contenido de Power BI a fin de poder utilizarlo en aplicaciones empresariales internas, debe usar el servicio Power BI, y todos los usuarios que consuman dicho contenido deben disponer de una licencia de suscripción de usuario válida Power BI Free o Power BI Pro. Puede encontrar más información sobre la integración de aplicaciones internas mediante el servicio Power BI en [https://dev.powerbi.com](https://dev.powerbi.com).

## ¿Está disponible globalmente este servicio?

El servicio Power BI Embedded ahora está disponible ahora en la mayoría de los centros de datos. Siempre puede comprobar la disponibilidad más reciente [aquí](https://azure.microsoft.com/status/).

## ¿Cuál es el SLA disponible para el servicio?

Power BI Embedded con Acuerdo de Nivel de Servicio estándar de Azure. Para más información, consulte [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/).

## ¿Cómo se fija el precio de este servicio?

Consulte los [precios de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760527) para más información.

## ¿Qué es una representación y cómo se factura?

>[AZURE.NOTE] Los precios con descuento por representación se ofrecieron durante la versión preliminar de Power BI Embedded y, debido a los comentarios de los usuarios, se interrumpirán a favor de precios por sesión. La transición de precios por representación a precios por sesión entrará en vigor el 1 de septiembre de 2016.

Una representación es un elemento visual que se muestra a un usuario final a raíz de una consulta al servicio. Por ejemplo, si un usuario ve un informe que contiene cuatro elementos visuales, se mostrarán cuatro representaciones. Si el usuario actualiza el informe y se envían más consultas al servicio, se generarán cuatro representaciones más. El propietario del servicio controla en qué medida los usuarios finales pueden realizar consultas nuevas que generen representaciones de pago con el fin de limitar la exposición a costos y reducir los costos en escenarios de datos estáticos.

Las representaciones se facturan por cada 1000 unidades. La facturación se prorratea por cantidades de representación inferiores a 1000. Los clientes reciben 1000 representaciones gratuitas al mes. Los clientes que realicen adquisiciones sujetas a contratos de licencia por volumen deben ponerse en contacto con su asociado o vendedor de Microsoft para que les proporcione información sobre precios.

## ¿Qué es una sesión de informe y cómo se factura?

Una sesión es un conjunto de interacciones entre un usuario final y un informe de Power BI Embedded. Cada vez que se muestra a un usuario un informe de Power BI Embedded, se inicia una sesión y se cobrará una sesión al titular de la suscripción. Las sesiones se facturan según una tarifa plana, independiente del número de elementos visuales en un informe o de la frecuencia con que se actualiza el contenido del informe. Una sesión finaliza cuando el usuario cierra el informe o cuando se agota el tiempo de espera de la sesión después de una hora.

## ¿Se ofrecen herramientas o instrucciones para ayudarme a calcular cuántas representaciones o sesiones debo esperar? ¿Cómo sabré cuántas representaciones se han realizado?

El Portal de Azure proporcionará información de facturación detallada sobre el número de representaciones o sesiones de informe que se han realizado con su suscripción.

## ¿Necesito una suscripción de Power BI para desarrollar aplicaciones con Power BI Embedded? ¿Cómo empiezo?

Como desarrollador de aplicaciones, no necesita tener una suscripción a Power BI para crear los informes y las visualizaciones que quiera usar en la aplicación. Necesita una suscripción a Microsoft Azure y la aplicación gratuita Power BI Desktop.

Consulte la documentación del servicio para más información sobre cómo utilizar el servicio Power BI Embedded.

## Tengo una suscripción a Azure. ¿Puedo usar Power BI Embedded con mi suscripción actual?

Sí. Puede usar su suscripción de Azure existente para aprovisionar y usar el servicio Microsoft Power BI Embedded.

## ¿Necesitan los usuarios finales de mi aplicación una licencia de Power BI?

No. Los usuarios finales de su aplicación no tienen que comprar una suscripción de Power BI por separado para acceder a las visualizaciones de datos en aplicación. En el modelo de Power BI Embedded, al proveedor de la aplicación se le factura el servicio mediante el medidor de consumo de Azure. Consulte la [página de precios y licencia](http://go.microsoft.com/fwlink/?LinkId=760527).

## ¿Cómo funciona la autenticación de usuarios con Power BI Embedded?

El servicio Power BI Embedded usa tokens de aplicación para la autenticación y la autorización en lugar de la autenticación explícita de usuario final. En el modelo de token de aplicación, la aplicación administra la autenticación y la autorización de sus usuarios finales. A continuación, cuando sea necesario, la aplicación crea

y envía los tokens de aplicación que indican a nuestro servicio que represente el informe solicitado. En este diseño no es necesario que la aplicación use Azure AD para la autenticación y la autorización de usuarios, aunque puede hacerlo. Puede aprender más sobre los tokens de aplicación [aquí](power-bi-embedded-app-token-flow.md). También presentamos la característica de seguridad de nivel de fila (RLS) en Power BI Embedded para escenarios de filtrado de seguridad avanzada.

## ¿Qué orígenes de datos se admiten actualmente con Power BI Embedded?

Vamos a permitir el acceso a orígenes de datos de nube que usan credenciales básicas mediante consulta directa. Esto significa que ahora mismo se admiten orígenes como Base de datos SQL de Azure y Azure SQL DW. Agregaremos compatibilidad con otros orígenes de datos y tipos de acceso en los próximos meses. Anunciaremos nuevos orígenes de datos admitidos en el sitio para desarrolladores de Power BI en [https://dev.powerbi.com](https://dev.powerbi.com/).

## ¿Cómo funciona el modelo de arrendamiento de Power BI Embedded?

En el modelo de Power BI Embedded, no hay ningún requisito explícito de que los clientes sean inquilinos de Azure AD. Puede elegir que Azure AD sea un requisito para sus clientes, o no. Como resultado, la arquitectura de la aplicación y la infraestructura son lo que determina el modelo de arrendamiento necesario para Power BI Embedded.

Los desarrolladores o empleados que trabajan en la aplicación deberán tener una cuenta de usuario de AAD para poder administrar su suscripción de Azure y las colecciones de áreas de trabajo mediante el Portal de Azure. Las API de programación que permiten a los desarrolladores importar informes, modificar cadenas de conexión y obtener direcciones URL insertadas aprovechan en su lugar tokens de aplicación para la autenticación y, como consecuencia, no necesitan AAD. Encontrará información sobre cómo usar nuestras API y el Portal de Azure en la [documentación de Power BI Embedded](https://azure.microsoft.com/documentation/services/power-bi-embedded/) en Azure.com.

## ¿Dónde puedo obtener más información?

Puede visitar la [página de documentación de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760526). Puede mantenerse informado sobre este servicio, visitando el [blog para desarrolladores de Power BI](http://blogs.msdn.com/powerbidev) o el centro para desarrolladores de Power BI en dev.powerbi.com. También puede formular preguntas en [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi).

## ¿Cómo empiezo?

¡Puede empezar ahora de forma gratuita! Si tiene una suscripción a Azure, ahora puede aprovisionar Power BI Embedded directamente desde el Portal de Azure. También puede crear una [cuenta de Azure gratis](https://azure.microsoft.com/free/). Después de aprovisionar el servicio Power BI Embedded, puede usar fácilmente las API de REST de Power BI directamente, o bien usar el SDK para desarrolladores disponible en [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472). Se proporcionan ejemplos de cómo aprovechar el SDK para desarrolladores.

## Consulte también

- [Qué es Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introducción a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0713_2016-->