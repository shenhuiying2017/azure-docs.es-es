<properties 
	pageTitle="¿Qué son las aplicaciones lógicas?" 
	description="Obtenga más información acerca de las Aplicaciones lógicas del Servicio de aplicaciones" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="07/12/2016"
	ms.author="klam"/>

#¿Qué son las aplicaciones lógicas?

Las aplicaciones lógicas son una manera de simplificar e implementar integraciones escalables y flujos de trabajo en la nube. Proporciona un diseñador visual para modelar y automatizar el proceso en una serie de pasos denominada flujo de trabajo. Hay [muchos conectores](../connectors/apis-list.md) en la nube y locales para la integración rápida en servicios y protocolos. Una aplicación lógica comienza con un desencadenador (como cuando se agrega una cuenta a Dynamics CRM) y la activación puede empezar muchas combinaciones, acciones, conversiones y lógicas de condiciones.

Algunas ventajas de Logic Apps:

- Ahorro de tiempo al diseñar procesos complejos gracias a las herramientas de diseño que se entienden fácilmente
- Implementación inigualable de patrones y flujos de trabajo, que de lo contrario, serían difíciles de implementar en el código
- Introducción rápida desde plantillas
- Personalización de aplicaciones lógicas con API, código y acciones propios
- Conexión y sincronización de sistemas dispares locales y en la nube
- Generación del servidor de BizTalk, administración de API, funciones de Azure y Bus de servicio de Azure compatible con integración de primera clase

Aplicaciones lógicas es una iPaaS (plataforma de integración como servicio) totalmente administrada que permite a los desarrolladores despreocuparse del hospedaje, la escalabilidad, la disponibilidad y la administración. Las aplicaciones lógicas se escalan automáticamente según las necesidades.

![Diseñador de aplicación de flujo](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Como se ha mencionado, con Logic Apps puede automatizar procesos empresariales. Estos son algunos ejemplos:
 
* Desplazamiento de archivos cargados en un servidor FTP a Almacenamiento de Azure
* Procesamiento y redirección de pedidos de sistemas locales y en la nube
* Supervisión de todos los tweets sobre un tema específico, análisis de la opinión, y creación de alertas y tareas para los elementos que necesitan seguimiento.

Todos estos tipos de escenarios se pueden configurar desde el diseñador visual y sin escribir ni una sola línea de código. Comience a [crear su aplicación lógica ahora][create]. Una vez escritas, las aplicaciones lógicas [se implementan rápidamente y se vuelven a configurar](app-service-logic-create-deploy-template.md) en varios entornos y regiones.

## ¿Por qué aplicaciones lógicas?

Aplicaciones lógicas ofrece velocidad y escalabilidad en el espacio de integración empresarial. La facilidad de uso del diseñador, la variedad de desencadenadores y acciones, y las potentes herramientas de administración facilitan como nunca la centralización de las API. Las empresas avanzan hacia la digitalización y Aplicaciones lógicas ayuda a conectar sistemas antiguos con los nuevos.

Además, con nuestra [cuenta de Enterprise Integration][biztalk], puede realizar el escalado a escenarios de integración consolidados con la eficacia de la [mensajería XML][xml], la [administración de asociados comerciales][tpm] y mucho más.

- **Herramientas de diseño fáciles de usar**: las aplicaciones lógicas pueden diseñarse de principio a fin en el explorador o con las herramientas de Visual Studio. Se empieza con un desencadenador: desde una simple programación hasta la generación de una publicación de GitHub. A continuación, se coordina cualquier número de acciones mediante la sofisticada galería de conectores.

- **Conexión sencilla de API**: incluso las tareas de composición que se describen fácilmente son difíciles de implementar en el código. Logic Apps facilita la conexión de sistemas dispares. ¿Desea conectar su solución de marketing en la nube a su sistema de facturación local? ¿Quiere centralizar la mensajería a través de las API y los sistemas con un Bus de servicio empresarial? Las aplicaciones lógicas son la forma más rápida y más confiable para ofrecer soluciones a estos problemas.

- **Introducción rápida desde plantillas**: para ayudarle a empezar, hemos dispuesto una [galería de plantillas][templates] que permiten crear rápidamente algunas soluciones comunes. Desde soluciones B2B avanzadas hasta una conectividad de SaaS simple e incluso algunas demostraciones: la galería es la introducción más rápida a la eficacia de las aplicaciones lógicas.

- **Extensibilidad incorporada**: ¿no ve el conector que necesita? Aplicaciones lógicas está diseñado para funcionar con sus propias API y código; puede crear fácilmente su propia aplicación de API para usarla como conector personalizado o llamar a una [función de Azure](https://functions.azure.com) para ejecutar fragmentos de código según los necesite.

- **Eficacia de integración real**: empiece por algo sencillo y crezca a medida que sea necesario. Las aplicaciones lógicas pueden aprovechar con facilidad la eficacia de BizTalk, la solución de integración líder del sector de Microsoft para permitir a los profesionales de integración crear las soluciones que necesitan. Más información sobre [Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## Conceptos de aplicaciones lógicas

Las siguientes son algunas de las principales partes que componen la experiencia de aplicaciones lógicas.

- **Flujo de trabajo**: las aplicaciones lógicas ofrecen una forma gráfica para modelar los procesos de negocio como una serie de pasos o un flujo de trabajo.
- **Conectores administrados**: las aplicaciones lógicas necesitan acceder a datos y servicios. Los conectores administrados se crean específicamente para ayudarle cuando se conecte y trabaje con datos. Vea la lista de conectores disponibles ahora en [conectores administrados][managedapis].
- **Desencadenadores**: algunos conectores administrados también pueden hacer de desencadenador. Un desencadenador inicia una nueva instancia de un flujo de trabajo de acuerdo con un evento específico, como la llegada de un correo electrónico o un cambio en la cuenta de almacenamiento de Azure.
-  **Acciones**: cada paso después de la llamada a la acción de un desencadenador en un flujo de trabajo. Cada acción normalmente se asigna a una operación en el conector administrado o en las aplicaciones de API personalizadas.
- **Enterprise Integration Pack**: para escenarios más avanzados de integración, Aplicaciones lógicas incluye funcionalidades de BizTalk. BizTalk es la plataforma de integración líder del sector de Microsoft. Los conectores de Enterprise Integration Pack permiten incluir fácilmente la validación, la transformación y mucho más en sus flujos de trabajo de aplicaciones lógicas.

## Introducción  

- Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica][create].
- [Ejemplos de aplicaciones lógicas y escenarios comunes](app-service-logic-examples-and-scenarios.md)
- [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694)
- [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md

<!---HONumber=AcomDC_0803_2016-->