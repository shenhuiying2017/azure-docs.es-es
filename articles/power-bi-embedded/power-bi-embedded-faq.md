<properties
   pageTitle="P+F"
   description="P+F"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Preguntas más frecuentes sobre Power BI Embedded

1. **¿Qué se ha anunciado sobre Power BI en la conferencia Build?**

    Microsoft anunció la disponibilidad de la versión preliminar pública del servicio Microsoft Power BI Embedded.

2.	**¿Qué es Microsoft Power BI Embedded?**

    Microsoft Power BI Embedded es un servicio de Azure que permite a los desarrolladores de aplicaciones insertar impresionantes informes y visualizaciones completamente interactivos en las aplicaciones utilizadas por los clientes sin el tiempo y los gastos de tener que crear sus propios controles desde el principio. Puede elegir entre una amplia variedad de visualizaciones de datos predefinidas o crear fácilmente visualizaciones personalizadas que satisfagan las necesidades únicas de su aplicación. Power BI Embedded está destinado a los fabricantes de software independiente (ISV) o a los desarrolladores de aplicaciones que desean ofrecer a sus clientes inteligencia empresarial como parte de su aplicación de mayor tamaño.

3.	**¿Quién querría usar Microsoft Power BI Embedded y por qué?**

    Microsoft Power BI Embedded está pensado para los desarrolladores de aplicaciones que desean ofrecer a sus usuarios sorprendentes experiencias de visualización de datos interactiva en cualquiera de sus dispositivos sin tener que crearlas ellos mismos. Con Power BI Embedded, los desarrolladores pueden proporcionar vistas siempre actualizadas con **consulta directa**. Los desarrolladores también pueden implementar, administrar y automatizar Power BI mediante programación con las API de ARM de Azure y las API de Power BI. Al igual que ocurre con todo lo de Power BI, el servicio incrustado se escala automáticamente para satisfacer las necesidades y el uso de la aplicación. El servicio Power BI Embedded presenta un modelo de precios basado en el consumo de pago por uso. Para más información sobre los precios, consulte [¿Cómo se calcular el precio de este servicio?](#price)

4.	**¿Cómo se relaciona Power BI Embedded con el servicio Power BI?**

    Power BI Embedded y el servicio Power BI son ofertas diferentes. Power BI Embedded se caracteriza por un modelo de facturación basado en el consumo, se implementa mediante el Portal de Azure y está diseñado para permitir a los ISV insertar visualizaciones de datos en las aplicaciones para su uso por parte de los clientes. El servicio Power BI se factura e implementa mediante el portal de O365 y es una oferta de inteligencia empresarial independiente y de uso general destinada principalmente al uso empresarial interno. Para más información sobre el servicio Power BI, visite [powerbi.com](www.powerbi.com).

5.	**¿Cómo mejora Power BI Embedded mi aplicación?**

    Las aplicaciones son bastante más eficaces cuando se aprovechan las visualizaciones de datos para informar de las decisiones de los usuarios directamente en la aplicación. Power BI Embedded le permite mejorar su aplicación con visualizaciones de datos completas, interactivas y siempre actualizadas, para que pueda aumentar la utilidad de su aplicación y la satisfacción y la fidelidad de los usuarios, y proporcionar análisis contextuales con facilidad en cualquier dispositivo.

6.	**¿Existen reglas o restricciones sobre el uso de Power BI Embedded en las aplicaciones?**

    Puede usar el servicio Power BI Embedded en una aplicación que desarrolle solo si la aplicación cumple los siguientes requisitos: 1) agrega una funcionalidad primaria e importante a nuestro servicio y no es en buena medida un sustituto de ningún servicio Power BI y 2) si se proporciona solamente para usuarios externos. No puede usar el servicio Power BI Embedded en aplicaciones empresariales internas.

7.	**¿Se puede usar Power BI Embedded para crear aplicaciones internas?**

    No, Power BI Embedded solo está previsto para su uso por usuarios externos y no puede usarse en aplicaciones empresariales internas. Por ello, para insertar contenido de Power BI a fin de poder utilizarlo en aplicaciones empresariales internas, debe usar el servicio Power BI, y todos los usuarios que consuman dicho contenido deben disponer de una licencia de suscripción de usuario válida Power BI Free o Power BI Pro. Puede encontrar más información sobre la inserción interna mediante el servicio Power BI en [https://dev.powerbi.com](https://dev.powerbi.com).

8.	**¿Está disponible globalmente este servicio?**

    El servicio Power BI Embedded está disponible en Norteamérica a partir de nuestro anuncio en la conferencia BUILD 2016 (en el Centro de datos del Centro y Sur de EE. UU.). Esperamos extender este servicio al resto de centros de datos de Azure muy poco tiempo después.

9.	**¿Cuál es el SLA disponible para el servicio?**

    Power BI Embedded está ya disponible como una servicio de Azure en versión preliminar sin un SLA formal. Un SLA se proporciona cuando el servicio pasa de versión preliminar a disponibilidad con carácter general.

<a name="price"/>
10.	**¿Cómo se fija el precio de este servicio?**

    Power BI Embedded está disponible actualmente en versión preliminar y sin ningún costo hasta el 1 de mayo de 2016. A partir del 1 de mayo de 2016, el servicio se cobrará por representación. Los clientes pueden comprar el servicio mediante dos vehículos de licencia principales: el programa de suscripción en línea de Microsoft (MOSP) o el programa de Enterprise VL.

    Puede usar el servicio Power BI Embedded en una aplicación que desarrolle solo si la aplicación cumple estos requisitos: 1) agrega una funcionalidad primaria e importante a nuestro servicio y no es en buena medida un sustituto de ningún servicio Power BI y 2) si se proporciona solamente para usuarios externos. No puede usar el servicio Power BI Embedded en aplicaciones empresariales internas.

    ![](media\power-bi-embedded-faq\price.png)

11.	**¿Qué es una representación y cómo se factura?**

    Una representación se produce cuando una visualización se muestra a un usuario final que necesita realizar una consulta al servicio. El servicio Power BI intenta almacenar en caché el contenido presentado cuando es posible con el fin de reducir el número de representaciones que se facturará a su aplicación. Sin embargo, las acciones del usuario, como el filtrado, pueden dar lugar a una consulta a nuestro servicio, lo que constituye una nueva representación.

    Por ejemplo, si un usuario ve un informe que contiene cuatro elementos visuales, esto podría dar lugar a cuatro representaciones. Si el usuario actualiza el informe y se envían más consultas al servicio, se generarían cuatro representaciones más. El propietario del servicio controla en qué medida los usuarios finales pueden realizar consultas nuevas que generen representaciones de pago con el fin de limitar la exposición a costos y reducir los costos en escenarios de datos estáticos.

    Las representaciones se facturan por cada 1000 unidades. La facturación se prorratea por cantidades de representación inferiores a 1000. Los clientes reciben 1000 representaciones gratuitas al mes. Los clientes que realicen adquisiciones sujetas a contratos de licencia por volumen deben ponerse en contacto con su asociado o vendedor de Microsoft para que les proporcione información sobre precios.

12.	**¿Se ofrecen herramientas o instrucciones para ayudarme a estimar cuántas representaciones debo esperar? ¿Cómo sabré cuántas representaciones se han realizado?**

    El Portal de Azure proporcionará información de facturación detallada sobre el número de representaciones que se han realizado con su suscripción.

13.	**¿Necesito una suscripción de Power BI para desarrollar aplicaciones con Power BI Embedded? ¿Cómo empiezo?**

    Como desarrollador de aplicaciones, no necesita tener una suscripción a Power BI para crear los informes y las visualizaciones que quiera usar en la aplicación. Necesita una suscripción a Microsoft Azure y la aplicación gratuita Power BI Desktop.

    Consulte la documentación del servicio para más información sobre cómo utilizar el servicio Power BI Embedded.

14.	**Tengo una suscripción a Azure. ¿Puedo usar Power BI Embedded con mi suscripción actual?**

    Sí. Puede usar su suscripción de Azure existente para aprovisionar y usar el servicio Microsoft Power BI Embedded.

15.	**¿Necesitan los usuarios finales de mi aplicación una licencia de Power BI?**

    No. Los usuarios finales de su aplicación no tienen que comprar una suscripción de Power BI aparte para acceder a las visualizaciones de datos en aplicación. En el modelo de Power BI Embedded, al proveedor de la aplicación se le factura el servicio mediante el medidor de consumo de Azure. Consulte la [página de precios y licencia](http://go.microsoft.com/fwlink/?LinkId=760527).

16.	**¿Cómo funciona la autenticación de usuarios con Power BI Embedded?**

    El servicio Power BI Embedded usa tokens de aplicación para la autenticación y la autorización en lugar de la autenticación explícita de usuario final. En el modelo de token de aplicación, la aplicación administra la autenticación y la autorización de sus usuarios finales. Luego, cuando es necesario, la aplicación crea y envía los tokens de aplicación que indican a nuestro servicio que represente el informe solicitado. En este diseño no es necesario que la aplicación use Azure Active Directory para la autenticación y la autorización de usuarios, aunque puede hacerlo. Para más información sobre los tokens de aplicación, consulte la página de documentación [Get started with Microsoft Power BI Embedded sample](https://azure.microsoft.com/documentation/articles/power-bi-embedded-get-started-sample/#key-flow) (Introducción a Microsoft Power BI Embedded: ejemplo).

17.	**¿Qué orígenes de datos se admiten actualmente con Power BI Embedded?**

    Durante la versión preliminar pública del servicio, vamos a permitir el acceso a orígenes de datos de nube que usan credenciales básicas mediante consulta directa. Esto significa que ahora mismo se admiten orígenes como Base de datos SQL de Azure, HDInsight Spark y Azure SQL DW. Agregaremos compatibilidad con otros orígenes de datos y tipos de acceso en los próximos meses. Anunciaremos nuevos orígenes de datos admitidos en el sitio para desarrolladores de Power BI en [http://dev.powerbi.com](http://dev.powerbi.com/).

18.	**¿Cómo funciona el modelo de arrendamiento de Power BI Embedded?**

    En el modelo de Power BI Embedded, no hay ningún requisito explícito de que los clientes sean inquilinos de Azure Active Directory (Azure AD). Puede elegir que Azure AD sea un requisito para sus clientes, o no. Como resultado, la arquitectura de la aplicación y la infraestructura son lo que determina el modelo de arrendamiento necesario para Power BI Embedded.

    Los desarrolladores o empleados que trabajan en la aplicación deberán tener una cuenta de usuario de Azure AD para poder administrar su suscripción de Azure y las colecciones de áreas de trabajo mediante el Portal de Azure. Las API de programación que permiten a los desarrolladores importar informes, modificar cadenas de conexión y obtener direcciones URL insertadas usan en su lugar tokens de aplicación para la autenticación y, como consecuencia, no necesitan Azure AD. Encontrará detalles sobre cómo usar nuestras API y el Portal de Azure en la [página de documentación de Power BI Embedded en azure.com](https://azure.microsoft.com/documentation/services/power-bi-embedded/).

19.	**¿Dónde puedo obtener más información?**

    Puede visitar la [página de documentación de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkId=760526). Puede mantenerse informado sobre este servicio, visitando el [blog para desarrolladores de Power BI](http://blogs.msdn.com/powerbidev) o el centro para desarrolladores de Power BI en dev.powerbi.com. También puede realizar preguntas en [Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi).

20.	**¿Cómo empiezo?**

    ¡Puede empezar ahora de forma gratuita! Si tiene una suscripción a Azure, ahora puede aprovisionar Power BI Embedded directamente desde el Portal de Azure. También puede crear una [cuenta de Azure gratuita](https://azure.microsoft.com/free/). Después de aprovisionar el servicio Power BI Embedded, puede usar fácilmente las API de REST de Power BI directamente, o usar el SDK para desarrolladores disponible en [GitHub](http://go.microsoft.com/fwlink/?LinkID=746472). Se proporcionan ejemplos de cómo aprovechar el SDK para desarrolladores.

## Consulte también

- [¿Qué es Microsoft Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md)
- [Get started with Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md) (Introducción a la versión preliminar de Microsoft Power BI Embedded)

<!---HONumber=AcomDC_0406_2016-->