<properties 
	pageTitle="¿Qué son las aplicaciones lógicas?" 
	description="Obtenga más información acerca de las Aplicaciones lógicas del Servicio de aplicaciones" 
	authors="joshtwist" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="jtwist"/>

#¿Qué son las aplicaciones lógicas?

El Servicio de aplicaciones de Azure es una plataforma totalmente administrada como una oferta de servicio (PaaS) para desarrolladores profesionales que ofrece un amplio conjunto de capacidades para la web, dispositivos móviles y escenarios de integración. Las Aplicaciones lógicas forman parte del conjunto de aplicaciones Servicio de aplicaciones y permiten a cualquier usuario técnico o el desarrollador automatizar la ejecución de procesos de negocio a través de un diseñador visual fácil de usar.

Lo mejor de todo, las aplicaciones lógicas pueden combinarse con aplicaciones API y conectores de nuestro marketplace para ayudar a resolver incluso escenarios de integración complicados de forma fácil.

![Flow app designer](./media/app-service-learn-about-flows-preview/Designer.png)

##SaaS, PaaS y expansión híbrida

La era de la nube trae un aumento vertiginoso en el uso de los sistemas de SaaS y PaaS, lo que aumenta el esfuerzo de los desarrolladores de todo el mundo. Mientras tanto, el trabajo pendientes de las aplicaciones informáticas se extendían y estas soluciones heterogéneas y distribuidas presentan nuevos desafíos de integración.  Además, las empresas necesitan aprovechar los datos y los servicios locales en sus soluciones, y necesitan hacerlo de forma segura.

La creación de soluciones que abarcan estos sistemas plantea retos no triviales que son lentos y proclives a errores para los equipos de desarrollo.

##¿Por qué aplicaciones lógicas?

Las aplicaciones lógicas permiten a los desarrolladores diseñar flujos de trabajo que articulan la intención a través de un desencadenador y una serie de pasos, que invocan a una aplicación de API del Servicio de aplicaciones, al tiempo que se ocupan de forma segura de la autenticación y los procedimientos recomendados, como ejecución duradera.

- **Herramientas de diseño fáciles de usar**: las aplicaciones lógicas pueden diseñarse de principio a fin en el explorador. Inicio con un desencadenador: desde una simple programación hasta siempre vez que aparezcas un tweet acerca de su compañía. A continuación, se coordina cualquier número de acciones mediante la sofisticada galería de conectores.

- **Crear fácilmente SaaS**: incluso las tareas de composición que son fáciles de describir son difíciles de implementar en el código. Las aplicaciones lógicas hacen muy fácil de conectar sistemas dispares. ¿Desea crear una tarea en el software CRM que se basa en la actividad de las cuentas de Facebook o Twitter? ¿Desea conectar su solución de marketing en la nube a su sistema de facturación local? Las aplicaciones lógicas son la forma más rápida y más confiable para ofrecer soluciones a estos problemas.

- **Introducción rápida desde plantillas**: para ayudarle a empezar, hemos proporcionado una galería de plantillas que permiten crear rápidamente algunas soluciones comunes. Desde soluciones avanzadas de BizTalk a la conectividad de SaaS simple e incluso algunos que sean solo 'for fun': la galería es la forma más rápida de comprender la eficacia de las aplicaciones lógicas.

- **Extensibilidad incorporada**: ¿no ve el conector que necesita? Las aplicaciones lógicas forman parte del conjunto de aplicaciones Servicio de aplicaciones y están diseñadas para trabajar con Aplicaciones de API; puede crear fácilmente su propia aplicación de API que se usará como un conector. Cree una nueva aplicación solo para usted, o bien compártala y rentabilícela en el marketplace.

- **Eficacia de integración real**: empiece `por algo sencillo y crezca a medida que sea necesario. Las aplicaciones lógicas pueden aprovechar con facilidad la eficacia de BizTalk, la solución de integración líder del sector de Microsoft para permitir a los profesionales de integración crear las soluciones que necesitan. Obtenga más información sobre las [capacidades de BizTalk proporcionadas con Servicios de aplicaciones][biztalk].

## Conceptos de aplicaciones lógicas

Las siguientes son algunas de las principales partes que componen la experiencia de aplicaciones lógicas. 

- **Flujo de trabajo**: las aplicaciones lógicas ofrecen una forma gráfica para modelar los procesos de negocio como una serie de pasos o un flujo de trabajo.
- **Conectores**: las aplicaciones lógicas necesitan tener acceso a datos y servicios. Un conector es un tipo especial de aplicación de API. Se crea específicamente para ayudarle cuando se conecta y trabaja con datos. Obtenga más información en [Qué son los conectores][biztalk].
- **Desencadenadores**: algunos conectores también pueden actuar como un desencadenador. Un desencadenador inicia una nueva instancia de un flujo de trabajo de acuerdo con un evento específico, como la llegada de un correo electrónico o un cambio en la cuenta de almacenamiento de Azure.
- **BizTalk**: para escenarios más avanzados de integración, Servicios de aplicaciones de Azure incluye capacidades de BizTalk. BizTalk es la plataforma de integración líder del sector de Microsoft. Las Aplicaciones de API de BizTalk permiten incluir fácilmente la validación, transformación, reglas y mucho más en sus flujos de trabajo de aplicaciones lógicas. Obtenga más información en [Qué son las Aplicaciones de API de BizTalk][biztalk].

## Introducción

Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica][create].

Para obtener más información sobre la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md




<!--HONumber=49-->