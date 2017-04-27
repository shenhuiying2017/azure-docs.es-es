---
title: "Guía para vendedores de Microsoft Azure Marketplace | Microsoft Docs"
description: "Esta guía está diseñada para usuarios empresariales y administradores de productos de proveedores de software independientes (ISV) que están interesados en vender su imagen de máquina virtual con certificación de Azure a desarrolladores y profesionales de TI."
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: aa6ae2b7e6ad0c6f91503d026ec8fd6933b6cfbb
ms.lasthandoff: 04/12/2017


---
# <a name="azure-marketplace-sellers-guide"></a>Guía para vendedores de Azure Marketplace

Bienvenido a la guía para vendedores de Microsoft Azure Marketplace. Esta guía está diseñada para usuarios empresariales y administradores de productos de proveedores de software independientes (ISV) que están interesados en vender su imagen de máquina virtual con certificación de Azure a desarrolladores y profesionales de TI. Dado que los clientes de Azure se reparten por todo el mundo, [Azure Marketplace](https://azuremarketplace.microsoft.com/) puede conseguir un gran alcance y exposición para sus productos.


> [!NOTE]
> Si está interesado en vender sus productos terminados de software como-servicio (SaaS) a usuarios empresariales, puede investigar las opciones para que aparezcan en [AppSource](https://appsource.microsoft.com).

Al final de esta guía, sabrá dónde encontrar información más detallada sobre lo siguiente:

- ¿Qué es Azure Marketplace?
- ¿Cómo puedo determinar si mi producto se ajusta a Azure Marketplace?
- ¿Qué ventajas ofrece vender en Azure Marketplace?
- ¿Cuáles son los requisitos previos (técnicos y no técnicos) para vender en Azure Marketplace?
- ¿Cómo creo discos duros virtuales (VHD) compatibles con Azure?
- ¿Cómo solicito ser vendedor y cómo me registro para ello?
- ¿Cómo creo y publico mi oferta?
- ¿Cómo salgo al mercado y busco recursos disponibles?
- ¿Qué informes y perspectivas ofrece Azure Marketplace?
- ¿Dónde puedo obtener ayuda y soporte técnico?

Comencemos.

## <a name="whats-the-azure-marketplace"></a>¿Qué es Azure Marketplace?

Azure Marketplace es una tienda de aplicaciones y servicios en línea que permite a los fabricantes de software independientes (ISV) —desde start-ups hasta empresas consolidadas— ofrecer sus soluciones a clientes de Azure de todo el mundo. Azure Marketplace constituye la mejor forma para usted, publicador de Azure, de distribuir y vender su imagen de máquina virtual innovadora a otros desarrolladores, ISV y profesionales de TI que desean desarrollar rápidamente sus aplicaciones basadas en la nube y sus soluciones móviles en Azure. Azure Marketplace admite una amplia gama de ofertas, como aplicaciones de análisis integral de datos con procesamiento de la información, almacenamiento de datos y capas de análisis, o aplicaciones de comercio electrónico en capas (datos, servicio e Internet).

Los clientes en la nube afrontan diferentes desafíos a la hora de buscar las soluciones adecuadas que se ajusten a sus necesidades únicas. Azure Marketplace proporciona un mecanismo para resolver estos desafíos y conectar a los clientes con soluciones innovadoras de ISV, tal como se describe en la tabla siguiente.

| Necesidad del cliente | Solución de Azure Marketplace |
| --- | --- |
| Demanda una funcionalidad de plataforma en la nube adicional que satisfaga las necesidades empresariales y técnicas | Ofrece una cartera creciente de servicios y aplicaciones complementarias en Azure |
| Le resulta difícil detectar la aplicación o el servicio correcto | Proporciona una tienda integral donde descubrir, buscar y comprar aplicaciones y servicios |
| Necesita un mecanismo de implementación escalable para servicios y aplicaciones de otros fabricantes | Permite la creación y la configuración de implementaciones escalables para servicios y aplicaciones de terceros |
| Requiere nuevas aplicaciones y servicios para integrar las soluciones existentes y trabajar con ellas | Integra con facilidad servicios y aplicaciones de terceros con las soluciones existentes en Azure |

Azure Marketplace aporta la calidad, la elección y la fortaleza del ecosistema de asociados de Azure a los clientes internacionales. Entre las principales ventajas se incluyen las siguientes:

- Ubicación unificada para ofertas basadas en Azure de Microsoft y asociados
- Más de 5000 ofertas
- Experiencia de plataforma integrada
- Configuración, implementación y administración simplificada

## <a name="is-the-azure-marketplace-right-for-my-business"></a>¿Es Azure Marketplace adecuado para mi negocio?

Es posible que en este momento simplemente se esté preguntando si Azure Marketplace es lo que más se ajusta a su negocio y, de ser así, qué podría obtener de esta plataforma. Azure Marketplace crea nuevas oportunidades de ventas para ofrecer a los clientes el software y los servicios que están disponibles en Azure:

- **Vender en nuevos mercados de clientes con una amplia cartera de soluciones en Azure**: se trata de una oportunidad de incrementar las ventas y conseguir ventas cruzadas de las ofertas de Marketplace con servicios de Azure que están disponibles para los clientes del Programa de suscripción en línea de Microsoft (MOSP) y del Contrato Microsoft Enterprise. Puede incorporar fácilmente las ofertas de Marketplace a la solución de clientes y el escenario de Azure.
- **Mejorar el valor para el negocio y aumentar las transacciones con cuentas de clientes nuevas y existentes**: Azure Marketplace puede ayudar a aumentar las transacciones, solucionar los puntos críticos de los clientes al trasladar las cargas de trabajo a la nube e incrementar la rentabilidad de las operaciones. En resumen, puede aumentar el valor del negocio mediante la venta de soluciones completas y abordar las lagunas críticas de la plataforma en la nube de Azure para satisfacer los requisitos del cliente.
- **Recurrir a una gama más amplia de clientes potenciales mediante la venta de servicios y aplicaciones de Marketplace**: Azure Marketplace puede facilitar la búsqueda y la retención de nuevos clientes. En la actualidad, muchas empresas necesitan trasladar cargas de trabajo a la nube y adaptarse a los entornos de infraestructura cambiantes. Puede proporcionar los servicios y aplicaciones adecuados para ayudarles a salvar las distancias.
- **Complementar y ampliar la funcionalidad de Azure mediante la agrupación de ofertas de Marketplace con servicios de Azure**: Azure Marketplace puede ayudarle a enmarcar las conversaciones basadas en escenarios con sus clientes, lo que le permite establecer contactos de forma real y concreta. También puede resolver carencias específicas de la plataforma y las necesidades del cliente tratando las soluciones integrales relevantes. Por último, mediante la venta de paquetes de soluciones, puede obtener el máximo partido del ecosistema de la plataforma de Azure para solucionar casi cualquier problema del cliente y aumentar las ventas.

## <a name="what39s-the-customer-base-for-the-azure-marketplace"></a>¿Cuál es la base de clientes para Azure Marketplace?

Los clientes de Azure Marketplace son variados. Dado que Azure tiene una de las bases de clientes de más rápido crecimiento de todos los proveedores de nube, podrá tener acceso a profesionales de TI y desarrolladores en permutaciones casi innumerables: desde empresas recién creadas hasta empresas bien establecidas, de todas las industrias, en el sector público y privado.

## <a name="how-does-the-azure-marketplace-work"></a>¿Cómo funciona Azure Marketplace?

Resulta bastante sencillo: una vez aprobada su solicitud, debe crear su imagen de máquina virtual con certificación de Azure y publicarla en Azure Marketplace, donde los clientes de Azure pueden buscar, comprar e implementar el producto en minutos. Mejor todavía: los clientes implementan la solución con confianza, sabiendo que el entorno está configurado para el éxito en Azure y la infraestructura está lista en unos pocos minutos.

El portal Cloud Partner Portal (CPP) es el concentrador para crear su oferta en Azure Marketplace. Las imágenes de máquina virtual están preconfiguradas con un sistema operativo completamente instalado y una o varias aplicaciones. Para certificar la imagen de manera que esté lista para la publicación, tendrá que cumplir ciertos requisitos previos que se abordarán más adelante en la siguiente sección de esta guía.


## <a name="whats-next"></a>Pasos siguientes

Si ha llegado hasta este punto de la guía, probablemente se esté diciendo: &quot;Creo que Azure Marketplace _es realmente_ la mejor opción para mi producto, ¿pero por dónde empiezo?&quot; En esta sección vamos a dar respuesta a esa pregunta. A continuación encontrará información acerca de cómo ponerse en marcha en Azure Marketplace: conseguir la certificación de Azure, obtener aprobación para vender su producto y crear su oferta en el CPP (ilustración 1).

![Proceso para vender en Azure Marketplace](./media/cloud-partner-portal-seller-guide/processforselling.png)

Ilustración 1: proceso para vender en Azure Marketplace

A un alto nivel, tendrá que cumplir una serie de requisitos previos técnicos y no técnicos y preparar la imagen de máquina virtual. A continuación, podrá proponer el producto y registrarse como vendedor. Por último, agregará contenido de marketing y lo enviará para su publicación. Podrá revisar su oferta en un entorno de ensayo o de vista previa antes de publicarla definitivamente en Azure Marketplace.

La primera vez que cree una oferta para Azure Marketplace, debería prever unas _cuatro semanas_ para su incorporación básica. Si es posible, comience unas _seis semanas_ antes del lanzamiento de la oferta para dedicar más tiempo a las consideraciones de los medios y la publicación.

## <a name="how-do-i-become-azure-certified"></a>¿Cómo obtengo la certificación de Azure?

El primer paso del proceso de creación de su oferta para Azure Marketplace es obtener la certificación de Azure. Esto implica compilar información de la empresa, aceptar las directivas de participación, descargar las herramientas necesarias y generar componentes técnicos (ilustración 2).

![Requisitos para obtener la certificación de Azure](./media/cloud-partner-portal-seller-guide/azurecertified.png)

Ilustración 2: requisitos para obtener la certificación de Azure

### <a name="technical-prerequisites"></a>Requisitos previos técnicos

Asegúrese de revisar detenidamente todos los requisitos previos técnicos y cumplir con ellos antes del inicio de la oferta. Necesitará acceso a Windows o Linux, así como discos duros virtuales compatibles con Azure vinculados a las herramientas de prueba.

Si bien puede desarrollar discos duros virtuales de Azure en la nube o de manera local, se recomienda que desarrolle su disco duro virtual de Azure directamente en la nube mediante el Protocolo de escritorio remoto. Sin embargo, como último recurso, es posible descargar un disco duro virtual y desarrollarlo mediante una [infraestructura local](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise).

Para obtener más información, consulte la página de [requisitos previos técnicos](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites).

### <a name="non-technical-prerequisites"></a>Requisitos previos no técnicos

Para formar parte de Azure Marketplace, debe cumplir algunos requisitos previos no técnicos. Primero, revise y acepte los términos de las [directivas de participación de Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/). Tenga en cuenta que el software y los servicios ofrecidos en Azure Marketplace deben cumplir al menos uno de los siguientes requisitos:

- **Ejecutarse en Microsoft Azure:** la función principal del software o servicio debe ejecutarse en Microsoft Azure.
- **Implementarse en Microsoft Azure**: debe explicar en la información de descripción cómo se implementa el software o servicio en Microsoft Azure.
- **Integrarse en un servicio de Microsoft Azure o ampliarlo**: debe indicar en la información de descripción _cuál_ es el servicio de Azure que amplía el software o servicio o en el que se integra, así como la _forma_ en que lo hacen.

También debe cumplir estos requisitos empresariales, como se describe en las directivas de participación de Azure Marketplace:

- La empresa (o sus subsidiarias) deben encontrarse en uno de los países de origen de venta admitidos por Azure Marketplace.
- El producto debe contar con una licencia que sea compatible con los modelos de facturación admitidos por Azure Marketplace.
- Usted es el responsable de poner el soporte técnico a disposición de los clientes de forma comercialmente razonable, ya sea de forma gratuita, de pago o a través del soporte técnico de la comunidad.
- Debe tener licencias para el software y las dependencias de software de terceros.
- Debe proporcionar contenido que cumpla los criterios para que su oferta se publique en [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) y en el Portal de administración de Azure.

Por último, tendrá que aceptar los [Términos de uso](https://azure.microsoft.com/support/legal/website-terms-of-use/), la [Declaración de privacidad de Microsoft](http://www.microsoft.com/privacystatement/default.aspx) y el [Contrato del programa Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/). 

Para conocer las preguntas más frecuentes, consulte la sección de [Preguntas más frecuentes de Marketplace](https://azure.microsoft.com/marketplace/faq/).

### <a name="virtual-machine-image-preparation"></a>Preparación de imagen de máquina virtual

Ahora que ha revisado todos los requisitos previos y completado las tareas necesarias, puede continuar con la creación de su oferta de imagen de máquina virtual como se detalla en [Virtual machine image publishing guide](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)(Guía de publicación de imágenes de máquina virtual).

Esta guía le ayudará a preparar el disco duro virtual que se va a implementar en Azure Marketplace, que se convertirá en la base de la SKU. 

> [!NOTE]
> El proceso difiere en función de si la SKU que va a proporcionar está basada en Linux o en Windows.

> [!NOTE]
>  Este proceso puede realizarse en paralelo con la [creación y el registro de cuentas](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration), que se describe a continuación.

### <a name="azure-certification"></a>Certificación de Azure

La adquisición del estado de _certificación de Azure_ representa la finalización correcta del proceso de incorporación. Este estado genera confianza en los clientes porque implica que los profesionales de TI y los desarrolladores están adquiriendo soluciones de calidad que se ejecutan en tecnología de Azure o se compilan con esta por medio de un asociado de confianza. Las soluciones de certificación de Azure incluyen lo siguiente:

- Investigación global
- Determinación de la compatibilidad con la plataforma Azure
- Cumplimiento de seguridad de imagen en línea
- Ningún virus o malware
- Modelo de facturación admitido

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>¿Cómo puedo proponer mi producto y obtener la aprobación?

Ahora es el momento de obtener la aprobación para vender su producto en Azure Marketplace (ilustración 3). Microsoft facilita el proceso de propuesta de su producto, completado del proceso de publicación y registro como vendedor.

![aprobación para vender en Azure Marketplace](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

Ilustración 3: pasos para obtener la aprobación para vender en Azure Marketplace

El primer paso hacia la aprobación es [proponer](https://createopportunity.azurewebsites.net/) el producto antes del registro y la publicación. Tenga en cuenta que la aprobación puede tardar _hasta tres días hábiles_.

Una vez conseguida la aprobación, recibirá lo siguiente:

- Correo electrónico con un código de promoción que exonera de la tasa de solicitud de 99 $ por el Centro de desarrollo y un perfil en el CPP.
- Preaprobación técnica del estado de certificación de Azure, junto con la opción de crear una oferta y certificar el disco duro virtual (la solicitud del Centro de desarrollo debe aprobarse antes de poder crear la oferta).
- Instrucciones para publicar los detalles de la cuenta del portal y una visión general del proceso de publicación.
- Posibilidad de optar a una llamada con el equipo de incorporación de Microsoft para guiar el proceso y formular preguntas.
- Capacidad de publicar una segunda oferta. Las ofertas publicadas en un segundo momento no tienen que pasar por el proceso de aprobación. Pueden ir directamente al CPP, pero aún será necesario que las máquinas virtuales se certifiquen mediante el proceso de publicación.
- Instrucciones sobre cómo se solicita ayuda con la publicación (las preguntas se deben dirigir al [vínculo de soporte técnico](https://support.microsoft.com/getsupport?wf=0&amp;tenant=ClassicCommercial&amp;oaspworkflow=start_1.0.0.0&amp;locale=en-us&amp;supportregion=en-us&amp;pesid=15635&amp;ccsid=636233723471685249) del publicador de Marketplace).

Por último, [registrará la cuenta](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration) como vendedor de Microsoft. La aprobación y la investigación pueden tardar _hasta dos semanas_, así que invierta este tiempo en la creación de su oferta de Azure Marketplace en el CPP.

## <a name="how-do-i-create-my-offer-in-the-cloud-partner-portal"></a>¿Cómo creo mi oferta en Cloud Partner Portal?

Ahora está listo para crear su oferta y certificar la imagen de máquina virtual. Para ello, deberá usar la plataforma Cloud Partner Portal (ilustración 4). Ahora puede considerar el CPP como el concentrador para [publicar y administrar la solución](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-getting-started).

![nueva oferta mediante Cloud Partner Portal](./media/cloud-partner-portal-seller-guide/newoffersetup.png)

Ilustración 4: configuración de una oferta nueva mediante Cloud Partner Portal

## <a name="what-about-best-practices"></a>¿Cuáles son los procedimientos recomendados?

Para ayudarle a sacar el máximo partido de su función de vendedor en Azure Marketplace, hemos desarrollado algunas herramientas y procedimientos recomendados que pueden tener una gran repercusión sin tomar demasiado tiempo.

## <a name="azure-test-drives"></a>Versiones de prueba de Azure

[Las versiones de prueba de Azure](https://azure.microsoft.com/marketplace/test-drives/) representan una excelente manera de mostrar su producto a los clientes potenciales y darles la posibilidad de probarlo antes de adquirirlo. Esto puede ayudar a aumentar la tasa de conversión y generar clientes potenciales altamente cualificados.

Después de proporcionar su información de contacto, los clientes pueden acceder a su experiencia de versión de prueba creada previamente: una práctica prueba autodidacta de las principales características y ventajas de su producto en un escenario de implementación real. De esta manera, las versiones de prueba de Azure permiten que su producto cobre vida justo ante los ojos de los clientes.

Actualmente, la publicación de una versión de prueba solo está disponible en el portal de publicación clásico. Puede encontrar documentación acerca de cómo hacerlo navegando [aquí](https://github.com/Azure/AzureTestDrive/wiki) en _How to Publish a New Test Drive_ (Publicación de una nueva versión de prueba).

Obtenga más información sobre las versiones de prueba de Azure [aquí](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

## <a name="go-to-market-checklist"></a>Lista de comprobación de salida al mercado

Obtenga más información sobre nuestros [programas de salida al mercado](https://partner.microsoft.com/go-to-market/) que pueden ayudarle a ampliar el alcance global de su organización. También puede aprovechar los recursos en el [centro de marketing para asociados](http://smartpartnermarketing.microsoft.com/isv).

Antes de su lanzamiento, se recomienda realizar algunas acciones para dar un impulso a su oferta de Azure Marketplace. Averigüe si está listo para comercializar su oferta con una lista de comprobación rápida:

- **He anunciado que mi oferta está disponible en Azure Marketplace** mediante una publicación de blog, el envío de mensajes de correo electrónico o la emisión de un comunicado de prensa.
- **Estoy promocionando mi oferta en mi propio sitio web**, que deriva a los clientes a mi oferta en Azure Marketplace, donde pueden aprender, explorar, probar, adquirir e implementar.
- **He publicado una versión de prueba** para que los clientes puedan experimentar mi oferta de manera real en Azure durante un descanso.
- **He habilitado la generación de clientes potenciales a petición** de manera que cada vez que un cliente hace clic para implementar la aplicación, recibo su nombre y su información de contacto.
- **He conectado con el administrador de socios comerciales** de Microsoft (en caso de tenerlo) para explorar oportunidades adicionales.

## <a name="what-about-reports"></a>¿Y qué ocurre con los informes?

Azure Marketplace ofrece informes sobre los pedidos, el uso y los clientes que están accesibles a través del [portal de informes de publicador*](https://reports.azure.com) de Azure Marketplace (ilustración 5). Los datos sin procesar se proporcionan en una tabla navegable y se pueden descargar como un archivo CSV o XLS.

![Acceso a informes a través del portal de informes de publicador](./media/cloud-partner-portal-seller-guide/accessingreports.png)

Ilustración 5: acceso a informes a través del portal de informes de publicador

Esto [vídeo](https://player.vimeo.com/video/200859918) ofrece una vista rápida de características y las ventajas de los informes, como:

- Tipos de informes: instantánea resumen de pedidos, uso y tendencias de clientes en la página de inicio
- Pedidos detallados, uso y datos de clientes
- Los pedidos y el uso se muestran como un resumen mensual o como una vista de tendencia de seis meses
- Varias perspectivas que se muestran como un estándar
- Uso/pedidos por:

-
  - Mercados
  - Canal
  - Ofertas en tendencia
  - Tipo de licencia de Marketplace

Los informes detallados muestran información del cliente, como el nombre de la empresa, la información de contacto y la ubicación geográfica detallada hasta el código postal, así que puede tener detalles de cortesía con sus vendedores. En la lista siguiente se incluyen los atributos específicos que proporcionamos acerca de sus clientes:

- Vendedor
- Nombre
- Apellidos
- Email
- CompanyName
- Fecha de transacción
- SubscriptionName
- AzureSubscriptionId
- Nombre de instancia en la nube
- Número de pedidos
- Región del país del cliente
- Ciudad del cliente
- Cultura de comunicación del cliente
- Código postal del cliente

También le ofrecemos formación a través de una demostración grabada, un glosario y documentación de ayuda. Si necesita ayuda o soporte con los informes, puede abrir una [incidencia de soporte técnico](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=636233723471685249).

