---
title: "Caso práctico de Azure SQL Database - GEP | Microsoft Docs"
description: "Sepa cómo GEP utiliza SQL Database para llegar a más clientes internacionales y lograr una mayor eficacia."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ae8bcb10-c251-4bac-b666-10a253918583
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: bced4e04f541dde58410e25fe0c3aa5493a5e5fd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure brinda a GEP alcance global y mayor eficacia
![Logotipo de GEP](./media/sql-database-implementation-gep/geplogo.png)

GEP ofrece software y servicios para que los líderes de compras de todo el mundo puedan optimizar los rendimientos financieros, las estrategias y las operaciones de sus negocios. Además de servicios de consultoría y administrados, la empresa ofrece SMART by GEP®, una completa plataforma de software de compras en la nube. Sin embargo, GEP tuvo problemas a la hora de tratar de integrar soluciones como SMART by GEP en sus propios centros de datos locales: las inversiones que tenía que realizar eran elevadas y habrían sido más cuantiosas aún debido a los requisitos normativos de otros países. Al migrar su infraestructura a la nube, GEP ha liberado recursos de TI, con lo que pueden preocuparse menos por las operaciones informáticas y centrarse más en el desarrollo de nuevas fuentes de valor para sus clientes internacionales.

## <a name="expanding-services-and-growth-by-using-azure"></a>Expansión de los servicios y crecimiento gracias al uso de Azure
A los clientes de SMART by GEP les encantan las características de la plataforma y su sencillez; pueden administrar sus procesos desde cualquier lugar, en cualquier momento y usando cualquier dispositivo, ya sea un portátil, una tableta o un teléfono. Al realizar la migración a Microsoft Azure, GEP ha podido dar respuesta al rápido crecimiento que están experimentando y a su potencial para expandirse a nuevos mercados. Según Dhananjay Nagalkar, vicepresidente de tecnología de GEP: "Microsoft Azure ha desempeñado un papel clave en el éxito de GEP, ya que nos permite escalar servicios con agilidad y rapidez, así como utilizar centros de datos regionales que nos ayudan a cumplir los requisitos normativos de nuestros clientes internacionales".

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>Las limitaciones de los centros de datos controlados por los usuarios
En 2013, los líderes de GEP reconocieron que necesitaban buscar una manera de garantizar escalabilidad y rendimiento, ya que su base de clientes estaba aumentando. Tal y como explica Nagalkar: "Para satisfacer esa demanda con los centros de datos que tenemos actualmente, hubiésemos tenido que expandir considerablemente nuestra infraestructura y nuestros recursos de TI. La inversión y el plazo de tiempo para llevarlo a cabo habrían sido mayúsculos". Las máquinas físicas y virtuales locales precisan numerosas tareas de configuración, administración, escalado, copias de seguridad y aplicación de revisiones a tal ritmo que GEP tendría que haber asumido un costo exorbitante. Por otro lado, las soluciones en la nube ofrecen tal simplicidad y comodidad que GEP pudo centrarse más en las tareas de desarrollo, en lugar de administrar una gran cantidad de operaciones de TI (que, para más inri, no paraban de aumentar). Nagalkar sabía que GEP podría reducir la sobrecarga en tareas de compras, configuración y administración migrando su infraestructura a la nube.

También necesitaba una manera de superar las barreras normativas que impedían que penetrara en algunos mercados internacionales. Por ejemplo, los datos de los clientes potenciales europeos de GEP tendrían que almacenarse en sus regiones geográficas. Para GEP no habría sido práctico crear varios centros de datos. "Las grandes inversiones en infraestructura y costos en mano de obra de TI habrían afectado de manera significa a los márgenes de beneficio", afirma Nagalkar. "Entonces, nos vimos obligados a rechazar a clientes potenciales que exigían que los datos se almacenasen en sus regiones".

Nagalkar sabía que una solución en la nube podría ser la respuesta a este dilema. Si GEP deja su infraestructura en manos de un proveedor de soluciones en la nube con presencia global, podría satisfacer mejor las necesidades de rendimiento y cumplimiento normativo de sus clientes almacenando los datos más cerca de las ubicaciones físicas de estos.

## <a name="finding-smooth-skies-in-the-cloud"></a>La búsqueda de una solución en la nube óptima
Nagalkar y su equipo exploraron varias opciones de soluciones en la nube, pero la mayoría estaban basadas en la infraestructura como servicio (IaaS), con lo que GEP habría tenido que realizar grandes inversiones en recursos de TI para configurar y administrar el servicio. La solución de plataforma como servicio (PaaS) de Azure se ajustaba mucho mejor a las necesidades de esta empresa.

"Gracias a Azure, GEP no tiene que realizar operaciones de administración de bases de datos, configuración de máquinas virtuales, aplicación de revisiones u otras tareas de gestión de infraestructuras", afirma Nagalkar. "En su lugar, podemos centrar nuestros recursos en lo que mejor se nos da: aprovechar nuestra experiencia en compras para crear software que brinde verdaderas ventajas a nuestros clientes". 

De hecho, gracias a la migración a Azure, GEP redujo su personal de operaciones de TI al mismo tiempo que consiguió proporcionar mejores funcionalidades a los clientes.

Al sacar partido de los centros de datos de Azure en todo el mundo, GEP puede ampliar con facilidad su presencia en Europa y Asia. Gracias a estos centros de datos en todo el mundo, GEP puede escalar servicios con rapidez, así como satisfacer las necesidades de almacenamiento de datos en las regiones de los clientes, lo que reduce la latencia y permite cumplir las normativas.

## <a name="smart-by-gep-architecture"></a>Arquitectura de SMART by GEP
GEP desarrolló SMART by GEP desde cero en Azure. Una motivación importante para GEP fue el aumento de escalabilidad, y la reducción del tiempo de inactividad y de los costos de mantenimiento que podría experimentar con Azure SQL Database en comparación con soluciones locales. Sin embargo, cuando migró su infraestructura a la nube, GEP descubrió nuevas oportunidades de desarrollo en la nube, como la creación rápida de prototipos y la ingeniería ajustada para responder mejor a las necesidades de los clientes. Al desarrollar la solución en Azure, GEP acaba con los quebraderos de cabeza que podrían encontrarse los desarrolladores en un entorno local por culpa de las licencias de software. El corazón de SMART by GEP es Azure SQL Database, aunque GEP emplea otros servicios de Azure para mejorar su solución de forma rápida y sencilla.

Figura 1 de ![Arquitectura de SMART by GEP](./media/sql-database-implementation-gep/figure1.png). Arquitectura de SMART by GEP

## <a name="structured-data"></a>Datos estructurados
En el centro de la aplicación SMART by GEP se encuentran las instancias de Azure SQL Database, que potencian la solución de administración de compras empresariales. Cuando GEP diseñó SMART by GEP, se percató de que Azure SQL Database sería el complemento perfecto para su arquitectura: una que permitiese a la empresa obtener el mayor grado de protección de datos y cumplir los requisitos normativos. GEP utiliza las diversas capas de protección de datos que ofrece Azure SQL Database, como las siguientes:

* Cifrado de datos en reposo mediante la tecnología Cifrado de datos transparente
* Protección de la autenticación integrando Azure SQL Database con Azure Active Directory
* Limitación del acceso a un subconjunto de datos adecuado con seguridad de nivel de fila
* Enmascaramiento de datos en tiempo real a través de directivas
* Seguimiento de eventos de base de datos mediante auditorías de Azure SQL Database

> "Podemos usar todas estas opciones sin realizar cambios importantes en el código y con un impacto mínimo en el rendimiento", explica Nagalkar.
> 
> 

Al utilizar Azure SQL Database, GEP obtiene automáticamente funcionalidades de recuperación ante desastres más eficaces que si se hubiesen diseñado de forma económica en un entorno local, gracias a las características de tolerancia a errores que integra Azure SQL Database. GEP utiliza la funcionalidad de replicación geográfica activa de Azure SQL Database, además de varias réplicas secundarias activas, legibles y en línea (grupos de disponibilidad AlwaysOn) en distintas regiones geográficas, con el objetivo de crear pares de alta disponibilidad. El fin de la replicación de los datos de SMART by GEP en diferentes regiones es que, en caso de que se produzca un desastre en una región entera, GEP puede recuperar fácilmente los datos de clientes con un objetivo de punto de recuperación (RPO) y un objetivo de tiempo de recuperación (RTO) mínimos.

Cada cliente de SMART by GEP tiene dos instancias de Azure SQL Database: una para procesar transacciones en línea (OLTP) y otra para realizar análisis (por ejemplo, análisis de informes y gastos de los clientes). Gracias a los grupos elásticos de Azure SQL Database, GEP puede administrar con facilidad miles de bases de datos de todo el mundo para responder a las impredecibles exigencias de recursos de bases de datos. Los grupos elásticos proporcionan a GEP una forma de garantizar que puedan escalarse las bases de datos de los clientes cuando sea necesario, sin tener que provocar el exceso o la falta de aprovisionamiento, además de poder controlar los costos. Además, como se trata de un servicio PaaS, GEP obtiene todas las nuevas características de Azure SQL Database con actualizaciones automáticas.

## <a name="unstructured-and-semi-structured-data"></a>Datos no estructurados y semiestructurados
Sin embargo, algunos datos de clientes de SMART by GEP necesitan guardarse en un almacenamiento estructurado de una forma menos rígida. Para este tipo de datos, GEP utiliza los servicios Azure Blob Storage, Azure Table Storage y Azure Redis Cache. Azure Blob Storage hospeda todos los datos adjuntos que los usuarios de SMART by GEP cargan en la aplicación. Aquí también guarda el contenido estático, como las hojas de estilos en cascada (CSS) y los archivos JavaScript.

GEP almacena los datos a los que los clientes no tienen acceso, como la información de registro de SMART by GEP, en Azure Table Storage, lo que les proporciona un almacenamiento ilimitado, eficaz y rentable, además de unos tiempos de recuperación breves, sin tener que preocuparse por configurar un esquema para los datos. GEP utiliza Azure Redis Cache para poder tener una caché principal.

## <a name="authentication-and-routing"></a>Autenticación y enrutamiento
Azure Access Control Service (ACS) proporciona a los usuarios de SMART by GEP una amplia variedad de opciones para iniciar sesión en el software. Este servicio puede federarse con cualquier proveedor de identidades que intercambie datos de autenticación utilizando el estándar Lenguaje de marcado de aserción de seguridad (SAML), como Active Directory Domain Services, Ping Identity, OneLogin o SiteMinder. Gracias a esto, GEP puede implementar el inicio de sesión único (SSO) para los clientes sin preocuparse por cómo almacenar las credenciales de estos y mantener directivas de contraseña de clientes.

Cuando hayan iniciado sesión, los clientes tendrán acceso a los recursos empresariales en SMART by GEP. GEP usa Azure Traffic Manager para redirigir las solicitudes procedentes de las sesiones del explorador y los dispositivos móviles de los clientes, además de para equilibrar la carga de dichos dispositivos.

## <a name="other-azure-services"></a>Otros servicios de Azure
GEP emplea muchos otros servicios de Azure para que SMART by GEP pueda responder a las necesidades de los clientes. Asimismo, utiliza servicios en la nube de Azure (roles web y de trabajo) para hospedar presentaciones de aplicaciones y los servicios de lógica empresarial protegidos. Los servicios en la nube permiten a los desarrolladores administrar la infraestructura como código (IAC) e implementar nuevas aplicaciones de SMART by GEP en solo una fracción del tiempo que se requería con los centros de datos locales, todo ello sin que tenga que participar el departamento de TI. Los desarrolladores de GEP pueden usar el entorno de ensayo de los servicios en la nube para probar nuevas versiones sin afectar a la implementación de producción actual. Cuando se prueben, GEP usa las características de intercambio de VIP de los servicios en la nube de Azure para mover en un minuto el código de ensayo al espacio de producción, lo que reduce el tiempo de inactividad de la implementación.

Para reducir la latencia de la aplicación, GEP utiliza Microsoft Azure Content Delivery Network (CDN) para colocar el contenido estático almacenado en Blob Storage de Azure (como los archivos CSS y JavaScript) en servidores perimetrales cerca de los usuarios. GEP usa Azure Service Bus para poder utilizar patrones de arquitectura de la aplicación, que abarcan desde la suscripción de publicación a la separación dinámica de consultas y comandos (CQRS) hasta la arquitectura superpuesta con comunicación asincrónica y de acoplamiento flexible. GEP usa Azure Media Services para mejorar su servicio de asistencia al cliente. También descubrió que podría publicar fácilmente vídeos de asistencia al usuario en Azure Media Services. Estos vídeos responden a preguntas habituales de los usuarios, lo que ayuda a aumentar su satisfacción con SMART by GEP, al mismo tiempo que se elimina una parte de la carga de tareas de soporte del personal de asistencia al cliente de GEP.

Para enviar los miles de mensajes de correos electrónicos transaccionales que genera diariamente SMART by GEP, la empresa utiliza la API de .NET de SendGrid para integrar la solución con Azure. Los desarrolladores GEP lo tienen muy fácil, ya que el complemento de SendGrid para Azure está disponibles en Azure Marketplace. Asimismo, podrían configurar SMART by GEP utilizando el paquete NuGet de SendGrid directamente en Microsoft Visual Studio. El equipo de TI de GEP puede supervisar el tráfico de correos electrónicos SendGrid del software desde Azure.

Finalmente, SMART by GEP usa Azure Virtual Machines —el servicio IaaS de Azure— para hospedar aplicaciones y servicios que no tenía sentido, en la fase de ingeniería, reemplazar por soluciones PaaS o de software como servicio (SaaS). Por ejemplo, GEP hospeda servicios de API de integración en las máquinas virtuales para realizar una integración de negocio a negocio (B2B) con los sistemas locales de planeación de recursos empresariales (ERP), como SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP y Lawson, y con las soluciones SaaS de los clientes con el fin de intercambiar de forma eficaz documentos de compras, como facturas.

> "Al desarrollar SMART by GEP en la nube de Microsoft Azure, hemos eliminado por completo la necesidad de utilizar recursos de TI locales, no solo en nuestra empresa, sino también en las operaciones de compras de nuestros clientes". 
> 
> Nagalkar Dhananjay, vicepresidente de soluciones tecnológicas
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Aumento de la satisfacción de los clientes sin ampliar los recursos de TI
Desde que GEP migró los centros de datos locales a Azure y desarrolló SMART by GEP desde cero en la plataforma de Azure, ha aumentado la escalabilidad y flexibilidad sin tener que ampliar su infraestructura ni el personal de TI. De hecho, la empresa no ha agregado recursos de TI desde hace más de cuatro años. Gracias al cómodo modelo de PaaS de Azure, GEP ha reducido su gasto en administración de operaciones y asistencia a proveedores. Como resultado, ha podido concentrar los recursos en el desarrollo de software. Además, gracias a que las tareas de desarrollo las realiza en la nube, los desarrolladores pueden probar rápidamente nuevas ideas sin tener que dedicar tiempo a coordinarse con el equipo de TI o a preocuparse por los requisitos de licencias locales. Azure SQL Database ayuda a GEP a garantizar de un modo más eficaz que sus clientes siempre obtengan unos niveles de rendimiento y servicio excepcionales.

## <a name="more-information"></a>Más información
* Página principal de GEP: [GEP](http://www.gep.com)
* Smart by GEP: [Smart by GEP](http://www.smartbygep.com)

## <a name="gep-contributors"></a>Colaboradores de GEP
* Huzaifa Matawala, director asociado y arquitecto de GEP
* Sathyan Narasingh, administrador de ingeniería de GEP
* Deepa Velukutty, arquitecto de bases de datos de GEP

