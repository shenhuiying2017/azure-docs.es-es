---
title: "Introducción a DoD de Azure Governmnet | Microsoft Docs"
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3


---
# <a name="department-of-defense-dod-in-azure-government"></a>Department of Defense (DoD) en Azure Government
## <a name="overview"></a>Información general
Las entidades del Departamento de Defensa (Department of Defense, DoD) estadounidense usan Azure Government para implementar una amplia gama de cargas de trabajo y soluciones, incluidas las cargas de trabajo tratadas en la <a href="http://iasecontent.disa.mil/cloud/SRG/index.html">DoD Cloud Computing Security Requirements Guide, Version 1, Release 2</a> (Guía de requisitos de seguridad de informática en la nube del DoD, versión 1, publicación 2) en niveles de impacto (Impact Level) 4 (L4) y 5 (L5).

Azure Government es el primer y único servicio en la nube comercial de hiperescala en recibir una autorización provisional del DoD para nivel de impacto 5 en el ámbito de la información de la agencia de sistemas de información de defensa (Defense Information Systems Agency). Además, las regiones de Azure Government dedicadas a cargas de trabajo de clientes del Departamento de Defensa estadounidense están ahora disponibles con carácter general.

Uno de los controladores clave para la migración de DoD a la nube es permitir a las organizaciones centrarse en sus misiones y minimizar las distracciones de crear y administrar soluciones de TI internas.

Las arquitecturas de la nube basadas en Azure Government permiten al personal de DoD centrarse en los objetivos de la misión y en la administración de servicios de mercancías de TI como SharePoint y otras cargas de trabajo de la aplicación.  Esto permite la realineación de los recursos de TI fundamentales para centrarse en el desarrollo de aplicaciones, el análisis y la seguridad cibernética.

La elasticidad y flexibilidad ofrecidas por Azure proporcionan grandes ventajas a los clientes de DoD. Resulta más sencillo, rápido y rentable escalar verticalmente una carga de trabajo en la nube que recorrer los procesos de adquisición de hardware y servicios tradicionales al trabajar de forma local o en centros de datos de DoD. Por ejemplo, proporcionar nuevo hardware multiservidor, incluso para un entorno de prueba, puede llevar muchos meses y requerir la aprobación de inversión de capital significativa. En cambio, al usar Azure, puede configurarse una migración de prueba para una carga de trabajo existente en cuestión de semanas o incluso días y de forma rentable (al completarse la prueba, el entorno puede desactivarse sin ningún gasto periódico).

Esta flexibilidad es significativa. Al migrar a Azure, los clientes de DoD no solo ahorran dinero; la nube ofrece nuevas oportunidades. Por ejemplo, es fácil establecer un entorno de prueba para obtener información sobre nuevas tecnologías. Puede migrar una aplicación y probarla en Azure antes de comprometerse a una implementación de producción en la nube. Los propietarios de la misión pueden explorar más opciones rentables de forma más sencilla y sin riesgo.

La seguridad es otro ámbito clave y, aunque cualquier implementación de la nube requiere un planeamiento adecuado para garantizar una entrega de servicio segura y confiable, en realidad, la mayoría de las cargas de trabajo basadas en la nube configuradas correctamente (incluidas las cargas de trabajo L4) de Azure Government serán más seguras que muchas implementaciones tradicionales en centros de datos y ubicaciones de DoD. Esto se debe a que las agencias de defensa tienen la experiencia de proteger físicamente todos los recursos; sin embargo, las áreas expuestas de TI presentan diversos desafíos. La seguridad cibernética es un espacio en constante cambio que requiere competencias específicas y la capacidad de desarrollar e implementar contramedidas con rapidez cuando sea necesario. Ahora, la plataforma Azure, tanto comercial como Government, proporciona soporte a cientos de miles de clientes, y esta escala permite a Microsoft detectar de forma rápida vectores de ataque en evolución y luego dirigir sus recursos hacia un rápido desarrollo e implementación de las defensas adecuadas.

## <a name="dod-region-qa"></a>Preguntas y respuestas sobre las regiones del DoD

### <a name="what-are-the-azure-government-dod-regions"></a>¿Cuáles son las regiones del DoD de Azure Government ? 
Las regiones US DoD este y US DoD central son regiones separadas físicamente de Microsoft Azure diseñadas para cumplir los requisitos de seguridad del Departamento de Defensa (DoD) estadounidense en lo relativo a la informática en la nube, en especial para los datos designados como nivel de impacto 5 del DoD según la DoD Cloud Computing Security Requirements Guide (Guía de requisitos de seguridad de informática en la nube del DoD, SRG).   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>¿Cuál es la diferencia entre Azure Government y las regiones del DoD de Azure Government? 
Azure Government es una nube de comunidad del gobierno estadounidense que presta servicios a clientes gubernamentales locales, estatales y federales, tribus, entidades sujetas a ITAR y proveedores de soluciones que desempeñan trabajos en su nombre. Todas las regiones de Azure Government están diseñadas y administradas para cumplir los requisitos de seguridad de los datos de nivel de impacto 5 del DoD y los estándares elevados de FedRAMP.

Las regiones DoD de Azure Government DoD están diseñadas para respaldar los requisitos de separación física de los datos de nivel de impacto 5 proporcionando una infraestructura de almacenamiento y computación dedicada que solo usan clientes del DoD.  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>¿Cuál es la diferencia entre los datos de los niveles de impacto 4 y 5?  
Los datos de nivel de impacto 4 son información controlada y no clasificada (CUI) entre la que puede figurar datos sujetos a un control de exportación, información privada, información sanitaria protegida y otros datos que requieran una designación de CUI explícita (p. ej., solo para uso oficial, confidencial para fuerzas y cuerpos de seguridad e información confidencial sobre seguridad).

Entre los datos de nivel de impacto 5 se incluye información controlada y no clasificada (CUI) que requiere un mayor grado de protección porque así lo ha considerado el propietario de la información, una ley pública o una regulación gubernamental.  Entre los datos de nivel de impacto 5 se incluyen sistemas de seguridad nacionales no clasificados.  Podrá encontrar más información sobre los niveles de impacto de la SRG, sus requisitos específicos y características en la sección 3 de la DoD Cloud Computing Security Requirements Guide (Guía de requisitos de seguridad de informática en la nube del DoD).  

### <a name="what-data-is-categorized-as-impact-level-5"></a>¿Qué datos se categorizan como de nivel de impacto 5? 
El nivel 5 se compone de información controlada y no clasificada (CUI) que requiere un mayor nivel de protección que el que otorga el nivel 4, ya que así lo considera necesario el propietario de dicha información, una ley pública u otras legislaciones gubernamentales. El nivel 5 también admite sistemas de seguridad nacionales (NSS) no clasificados.  Este nivel admite categorizaciones de información NSS y CUI basadas en CNSSI-1253 hasta una confidencialidad e integridad moderada (M-M-x).

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>¿Qué hace Microsoft diferente para respaldar los datos de nivel de impacto 5? 
Los datos de nivel de impacto 5, por definición, solo se pueden procesar en una infraestructura dedicada que garantice la separación física de los clientes del DoD de los inquilinos que no formen parte del gobierno federal.  Al proporcionar las regiones US DoD central y US DoD este, Microsoft presta un servicio exclusivo para los clientes del DoD que se ajusta a unos estándares superiores incluso a los requisitos exigidos por el DoD y supera el nivel de protección y funcionalidad que ofrece cualquier otra solución en la nube comercial de hiperescala.

### <a name="do-these-regions-support-classified-data-requirements"></a>¿Estas regiones admiten los requisitos de datos clasificados? 
Estas regiones del DoD de Azure Government solo admiten datos no clasificados hasta el nivel de impacto 5 (inclusive).  Los datos de nivel de impacto 6 se definen como información clasificada hasta la categoría de secreto.

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>¿Qué organismos del DoD pueden usar las regiones del DoD de Azure Government? 
Las regiones US DoD este y US DoD central se han creado para respaldar la base de clientes del Departamento de Defensa estadounidense.  En ella se incluye:
* La Oficina del Secretario de Defensa.
* La Junta de Jefes de Estado Mayor.
* El Estado Mayor conjunto.
* Las agencias de defensa.
* El Departamento de Actividades de Defensa sobre el Terreno.
* El Departamento del Ejército.
* El Departamento de la Marina (incluido el cuerpo de marines de EE. UU.).
* El Departamento de la Fuerza Aérea.
* La guardia costera de EE. UU.
* Los mandos de combate unificados.
* Otras oficinas, agencias, actividades y mandos bajo el control o la supervisión de cualquiera de las entidades aprobadas que figuran arriba.

### <a name="are-the-dod-regions-more-secure"></a>¿Son más seguras las regiones del DoD? 
Microsoft administra todos sus centros de datos de Azure y la infraestructura que los respalda de un modo conforme con los estándares locales e internacionales de seguridad y cumplimiento. Además, supera a todas las plataformas en la nube en cuanto a logros e inversión en medidas de cumplimiento.  Estas nuevas regiones del DoD ofrecerán garantías y compromisos específicos para cumplir los requisitos definidos en la SRG del DoD para informática en la nube.

### <a name="why-are-there-multiple-dod-regions"></a>¿Por qué hay varias regiones del DoD? 
Al contar con varias regiones del DoD, Microsoft proporciona a sus clientes la oportunidad de diseñar sus soluciones para escenarios de recuperación ante desastres entre las distintas regiones a fin de garantizar la continuidad empresarial y cumplir los requisitos de la acreditación del sistema.  Asimismo, los clientes pueden optimizar el rendimiento implementando soluciones en la región geográfica más cercana a su ubicación física.

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>¿Estas regiones del DoD están conectadas a NIPRNet? 
El DoD exige que los servicios en la nube comerciales empleados para CUI deben estar conectados a los clientes a través de un punto de acceso a la nube (CAP).  Por lo tanto, las regiones del DoD de Azure están conectadas a NIPRNet por medio de conexiones redundantes a varios CAP distribuidos por varias zonas geográficas.  Un CAP del DoD es un sistema de dispositivos de supervisión y protección de los límites de la red que ofrecen seguridad para los servicios y la red del sistema de información del DoD.

### <a name="what-does-general-availability-mean"></a>¿Qué significa "disponibilidad de carácter general"? 
Este concepto quiere decir que las regiones del DoD de Azure Government pueden usarse para procesar cargas de trabajo de producción y que se cumplirán los SLA con respaldo financiero para todos los servicios implementados en las regiones y también aquellos que estén disponibles generalmente.

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>¿Cómo adquiere un cliente del DoD servicios para el DoD de Azure Government? 
Las entidades pertinentes pueden adquirir los servicios para el DoD de Azure Government a través de los mismos canales de reventa de Azure Government.  De acuerdo con el compromiso de Microsoft con facilitar la adquisición, el planeamiento y la estimación del costo de los servicios en la nube, se incluirán los precios de las regiones del DoD de Azure Government en la calculadora de precios de Azure en el momento en que estos pasen a estar disponibles de forma general.  Los servicios del DoD de Azure Government se pueden escalar o reducir verticalmente con rapidez para adaptarse a la demanda, de forma que solo paga por lo que utiliza.
No hará falta realizar ninguna modificación contractual en el caso de los clientes con contrato Enterprise que ya usen Azure Government.  

### <a name="how-are-the-dod-regions-priced"></a>¿Cómo se fija el precio de las regiones del DoD? 
Las regiones del DoD presentan unos precios basados en las regiones.  Es decir, los costos de servicio para los clientes validados del DoD se basará en la región de Azure Government en la que ejecute sus cargas de trabajo.  Si desea obtener información más específica sobre los precios, póngase en contacto con su ejecutivo de cuentas de Microsoft.  Los precios de las regiones del DoD se proporcionarán mediante la calculadora de Azure.com en el futuro.

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>¿Cómo puede una organización del DoD validarse para las regiones del DoD de Azure Government? 
Para poder acceder a las regiones del DoD de Azure, los clientes deben completar un proceso de precualificación para comprobar su organización y el uso previsto del entorno de DoD de Azure.  Una vez que se haya completado satisfactoriamente el proceso de precualificación, Microsoft facilitará al solicitante del organismo más instrucciones para crear una suscripción, acceder al entorno y proporcionar un control del acceso basado en roles a otros miembros de la organización.

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>¿Pueden proveedores de software independientes y proveedores de soluciones que creen productos en Azure implementar soluciones en las regiones del DoD de Azure Government? 
Los proveedores de soluciones con ofertas de servicios en la nube creadas en Azure pueden administrar soluciones de un solo inquilino y de varios inquilinos exclusivas del DoD en las regiones del DoD de Azure Government.  Estos proveedores deben demostrar primero su idoneidad proporcionando pruebas documentales de un contrato con una entidad aprobada del DoD o contar con una carta de patrocinio de una de dichas entidades.  Los proveedores que ofrezcan servicios en las regiones del DoD de Azure Government deben incluir en su oferta defensa de la red informática, creación de informes de incidentes y personal selecto para administrar soluciones encargadas de información de nivel de impacto 5.  Es posible encontrar más directrices para los proveedores de soluciones en la DoD Cloud Computing Security Requirements Guide (Guía de requisitos de seguridad de informática en la nube del DoD).

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>¿Office 365 o Microsoft Dynamics 365 formarán parte de esta oferta? 
Microsoft proporciona servicios de Office 365 para el DoD en el nivel de impacto 5 junto con esta oferta.  Dynamics 365 tiene previsto ofrecer servicios de nivel de impacto 5 de las regiones del DoD de Azure en el futuro.

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>¿Cómo me conecto con las regiones del DoD una vez que cuente con una suscripción? 
Las regiones del DoD de Azure Government están disponibles en portal de administración de Azure Government.  Los clientes del DoD aprobados para su uso verán que las regiones les aparecerán como opciones disponibles a la hora de implementar los servicios disponibles.  Para obtener información general sobre cómo administrar sus suscripciones de Azure Government, consulte nuestra documentación.

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>¿Qué servicios forman parte de su ámbito de acreditación de nivel de impacto 5? 
Azure es un servicio permanente en el que se incorporan nuevos servicios y funciones cada semana, por lo que el número de servicios que entran dentro del ámbito se expande con regularidad.  Para consultar la información más actualizada, visite el<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Centro de confianza de Microsoft.


## <a name="next-steps"></a>Pasos siguientes:
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Centro de confianza de Microsoft: página web de DoD </a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html"> La guía de requisitos de seguridad de informática en la nube de DoD, Version 1, Release 2 </a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/"> Canales de reventa de Azure Government

<a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure Government. </a>




<!--HONumber=Jan17_HO3-->


