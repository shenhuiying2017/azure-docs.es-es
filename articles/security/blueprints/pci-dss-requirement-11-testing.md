---
title: "Plano técnico de procesamiento de pagos de Azure: requisitos de pruebas"
description: Requisito 11 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 57429741afd2ffd16c09a0f1485cb1cfbdda5571
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Requisitos de pruebas para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-11"></a>Requisito 11 de PCI DSS

**Pruebas frecuentes de los procesos y sistemas de seguridad**

> [!NOTE]
> El [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) (Consejo de estándares de seguridad para el sector de tarjetas de pago) define estos requisitos como parte del [Estándar de Seguridad de Datos (DDS) para la PCI, versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de comprobación e instrucciones para cada requisito.

Usuarios malintencionados y investigadores continuamente descubren vulnerabilidades en los sistemas y consiguen introducirse mediante software nuevo. Los componentes del sistema, los procesos y el software personalizado se deben probar con frecuencia para garantizar que los controles de seguridad sigan reflejando un entorno cambiante.

## <a name="pci-dss-requirement-111"></a>Requisito 11.1 de PCI DSS

**11.1** Implemente procesos para comprobar la presencia de puntos de acceso inalámbrico (802.11) y detectar e identificar todos los puntos de acceso inalámbrico autorizados y no autorizados de forma trimestral.

> [!NOTE]
> Entre los métodos que se pueden usar en el proceso se incluyen, entre otros, análisis de redes inalámbricas, inspecciones físicas o lógicas de los componentes y la infraestructura del sistema, control de accesos a redes (NAC) o IDS/IPS inalámbricos.
Con independencia de los métodos que se usen, deben ser suficientes para detectar e identificar los dispositivos autorizados y no autorizados.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Azure no admite ni permite las conexiones inalámbricas en el entorno de red de Azure. Los equipos de seguridad interna analizan los sistemas en busca de señales inalámbricas no autorizadas de forma trimestral. Dichas señales se investigan y se eliminan. No se permite a los clientes implementar tecnología inalámbrica en el entorno de Azure. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la solución no se implementan redes inalámbricas ni SNMP.|



### <a name="pci-dss-requirement-1111"></a>Requisito 11.1.1 de PCI DSS

**11.1.1** Mantenga un inventario de los puntos de acceso inalámbrico no autorizados, incluida una justificación comercial documentada.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Vea la sección "Microsoft Azure" del [Requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la solución no se implementan redes inalámbricas ni SNMP.|



### <a name="pci-dss-requirement-1112"></a>Requisito 11.1.2 de PCI DSS

**11.1.2** Implemente procedimientos de respuesta a incidentes en caso de que se detecten puntos de acceso inalámbrico no autorizados.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Vea la sección "Microsoft Azure" del [Requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la solución no se implementan redes inalámbricas ni SNMP.|



## <a name="pci-dss-requirement-112"></a>Requisito 11.2 de PCI DSS

**11.2** Ejecute exámenes de vulnerabilidades de red internas y externas al menos de forma trimestral y después de cualquier cambio significativo en la red (por ejemplo, instalaciones de nuevos componentes del sistema, cambios en la topología de red, modificaciones de las reglas de firewall y actualizaciones de productos). 

> [!NOTE]
> Se pueden combinar varios informes de análisis para que el proceso de examen trimestral muestre que se han analizado todos los sistemas y solucionado todas las vulnerabilidades. Es posible que se requiera documentación adicional para comprobar que las vulnerabilidades no corregidas estén en proceso de solucionarse.
> Para el cumplimiento inicial de PCI DSS, no es necesario completar cuatro trimestres de análisis aprobados si el asesor comprueba que 1) el resultado del examen más reciente fue un examen aprobado, 2) la entidad tiene directivas y procedimientos documentados que requieren análisis trimestral y 3) tras realizar un nuevo examen, se confirma que se han corregido las vulnerabilidades observadas en los resultados. Para los años posteriores a la revisión inicial de PCI DSS, deben haberse producido cuatro trimestres de exámenes aprobados.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Azure lleva a cabo exámenes de vulnerabilidades internas y externas trimestrales. Los exámenes los realiza personal cualificado. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Se han realizado pruebas de penetración en la tienda web de Contoso y se han examinado las vulnerabilidades en un esfuerzo "tal cual". Los resultados de las pruebas de penetración se pueden duplicar mediante herramientas comunes, como nmap o pentest-tools.com. Los resultados de la prueba de penetración proporcionarán una superficie expuesta a ataques no concluyente, sin elementos infringibles. Además, [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporcionan información y corrección de vulnerabilidades.|



### <a name="pci-dss-requirement-1121"></a>Requisito 11.2.1 de PCI DSS

**11.2.1** Realice exámenes de vulnerabilidades internas de forma trimestral. Solucione vulnerabilidades y realice nuevos exámenes para comprobar que todas las vulnerabilidades de "alto riesgo" se resuelvan de acuerdo con la clasificación de vulnerabilidades de la entidad (Requisito 6.1). Los exámenes los debe realizar personal cualificado.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure realiza exámenes en busca de vulnerabilidades en la infraestructura subyacente en el ámbito. Microsoft Azure implementa el examen de vulnerabilidades en sistemas operativos de servidor, bases de datos y dispositivos de red con las herramientas de examen de vulnerabilidades adecuadas. Las aplicaciones web de Azure se examinan con las soluciones de análisis adecuadas del sector. Los exámenes de vulnerabilidades se realizan de forma trimestral.<br /><br />Los nuevos exámenes se realizan según sea necesario en todos los sistemas hasta que se resuelven todas las vulnerabilidades de "alto riesgo" (de acuerdo con lo identificado en el Requisito 6.1). |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Se han realizado pruebas de penetración en la tienda web de Contoso y se han examinado las vulnerabilidades en un esfuerzo "tal cual". Los resultados de las pruebas de penetración se pueden duplicar mediante herramientas comunes, como nmap o pentest-tools.com. Los resultados de la prueba de penetración proporcionarán una superficie expuesta a ataques no concluyente, sin elementos infringibles. Además, [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporcionan información y corrección de vulnerabilidades.|



### <a name="pci-dss-requirement-1122"></a>Requisito 11.2.2 de PCI DSS

**11.2.2** Realice exámenes trimestrales de vulnerabilidades externas a través de un Proveedor aprobado de análisis (ASV) que haya aprobado el Consejo de estándares de seguridad para la industria de tarjeta de pago (PCI SSC). Realice nuevos exámenes según sea necesario hasta lograr un resultado satisfactorio. 

> [!NOTE]
> Los exámenes trimestrales de vulnerabilidades externas los debe realizar un Proveedor aprobado de análisis (ASV) aprobado por el Consejo de estándares de seguridad para la industria de tarjeta de pago (PCI SSC).
> Consulte la Guía del programa de ASV publicada en el sitio web de PCI SSC para ver las responsabilidades del cliente de análisis, la preparación del análisis, etc.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure realiza exámenes externos en busca de vulnerabilidades en la infraestructura subyacente en el ámbito a la que se tiene acceso de forma externa. Los exámenes los realiza un Proveedor aprobado de análisis (ASV).<br /><br />Microsoft Azure se suscribe a notificaciones de revisión mensuales de MSRC/OSSC y examina los sistemas en busca de vulnerabilidades al menos de forma trimestral. Las vulnerabilidades identificadas se evalúan y corrigen por cada escala de tiempo establecida según el nivel de riesgo.<br /><br />Para identificar vulnerabilidades de seguridad, cada trimestre se realiza un examen de las vulnerabilidades de seguridad completo y dirigido de los componentes prioritarios del entorno de Microsoft Azure. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Al implementar la tienda web de Contoso, los clientes de la demostración son responsables de realizar exámenes y nuevos exámenes trimestrales de vulnerabilidades externas según sea necesario en todas las instancias de PaaS en su entorno de datos de los titulares de tarjetas (CDE), mediante un Proveedor aprobado de análisis (ASV) aprobado por el Consejo de estándares de seguridad para la industria de tarjeta de pago.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>Requisito 11.2.3 de PCI DSS

**11.2.3** Realice exámenes internos y externos, y nuevos exámenes según sea necesario, después de cualquier cambio significativo. Los exámenes los debe realizar personal cualificado.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los resultados se notifican a las partes interesadas, y el equipo de seguridad de Azure realiza el seguimiento de la corrección hasta el cierre. Los resultados de las pruebas de Azure se pueden compartir con los clientes conforme al acuerdo de confidencialidad. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de realizar exámenes trimestrales de las vulnerabilidades internas y externas, y nuevos exámenes según sea necesario en todas las instancias de PaaS en su CDE. Los exámenes deben realizarse después de aplicar cambios significativos en el entorno dentro del ámbito.<br /><br />Los exámenes los debe realizar un ASV o personal independiente de la organización.|



## <a name="pci-dss-requirement-113"></a>Requisito 11.3 de PCI DSS

**11.3** Implemente una metodología de pruebas de penetración que cumpla los siguientes criterios:
- Debe estar basado en enfoques de pruebas de penetración aceptados por el sector (por ejemplo, NIST SP800-115).
- Debe incluir cobertura para todo el perímetro del CDE y los sistemas críticos.
- Debe incluir pruebas del interior y exterior de la red.
- Debe incluir pruebas para validar cualquier control de segmentación y reducción del ámbito.
- Debe definir pruebas de penetración de nivel de aplicación para incluir, como mínimo, las vulnerabilidades descritas en el Requisito 6.5.
- Debe definir pruebas de penetración de nivel de red para incluir los componentes que admiten funciones de red, así como los sistemas operativos.
- Debe incluir la revisión y consideración de las amenazas y vulnerabilidades que se han producido en los últimos 12 meses
- Debe especificar la retención de los resultados de las pruebas de penetración y las actividades de corrección.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure valida los servicios mediante pruebas de penetración de terceros según las diez principales de OWASP (Proyectos de seguridad de aplicaciones web abiertas) con evaluadores con certificación CREST. El seguimiento de los resultados de las pruebas se realiza a través de un registro de riesgos que se audita y revisa de forma periódica para garantizar el cumplimiento con las prácticas de seguridad. <br /><br />Microsoft también usa simulacros de ataque contra la infraestructura, servicios y aplicaciones administrados por Microsoft. Durante el proceso de simulacros de ataque y pruebas de penetración de sitios activos no se usa deliberadamente ningún tipo de datos de los clientes finales. Las pruebas se realizan en la infraestructura y plataformas de Microsoft Azure, así como en las propias aplicaciones y datos de Microsoft. Nunca se usan inquilinos, aplicaciones ni datos del cliente hospedados en Azure.<br /><br />Microsoft Azure ha contratado a un evaluador independiente para desarrollar un plan de evaluación del sistema y realizar una evaluación de los controles. Las evaluaciones de los controles se realizan anualmente y los resultados se notifican a las partes relevantes. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Se han realizado pruebas de penetración en la tienda web de Contoso y se han examinado las vulnerabilidades en un esfuerzo "tal cual". Los resultados de las pruebas de penetración se pueden duplicar mediante herramientas comunes, como nmap o pentest-tools.com. Los resultados de la prueba de penetración proporcionarán una superficie expuesta a ataques no concluyente, sin elementos infringibles. Además, [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporcionan información y corrección de vulnerabilidades.|



### <a name="pci-dss-requirement-1131"></a>Requisito 11.3.1 de PCI DSS

**11.3.1** Realice pruebas de penetración *externas* al menos cada año y después de cualquier actualización o modificación significativa de la infraestructura o aplicación (por ejemplo, una actualización del sistema operativo o una subred o un servidor web que se agregan al entorno).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Vea la sección "Microsoft Azure" del [Requisito 11.3](#pci-dss-requirement-11-3). |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Se han realizado pruebas de penetración en la tienda web de Contoso y se han examinado las vulnerabilidades en un esfuerzo "tal cual". Los resultados de las pruebas de penetración se pueden duplicar mediante herramientas comunes, como nmap o pentest-tools.com. Los resultados de la prueba de penetración proporcionarán una superficie expuesta a ataques no concluyente, sin elementos infringibles. Además, [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporcionan información y corrección de vulnerabilidades.|



### <a name="pci-dss-requirement-1132"></a>Requisito 11.3.2 de PCI DSS

**11.3.2** Realice pruebas de penetración *internas* al menos cada año y después de cualquier actualización o modificación significativa de la infraestructura o aplicación (por ejemplo, una actualización del sistema operativo o una subred o un servidor web que se agregan al entorno).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure contrata asesores independientes para realizar pruebas de penetración del límite de Microsoft Azure. También se realizan ejercicios de simulacro de ataques regularmente y los resultados se usan para realizar mejoras de seguridad. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Se han realizado pruebas de penetración en la tienda web de Contoso y se han examinado las vulnerabilidades en un esfuerzo "tal cual". Los resultados de las pruebas de penetración se pueden duplicar mediante herramientas comunes, como nmap o pentest-tools.com. Los resultados de la prueba de penetración proporcionarán una superficie expuesta a ataques no concluyente, sin elementos infringibles. Además, [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporcionan información y corrección de vulnerabilidades.|



### <a name="pci-dss-requirement-1133"></a>Requisito 11.3.3 de PCI DSS

**11.3.3** Las vulnerabilidades infringibles encontradas durante las pruebas de penetración deben corregirse, y las pruebas deben repetirse para comprobarlo.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Se han establecido procedimientos para supervisar los componentes de la plataforma de Microsoft Azure en busca de vulnerabilidades de seguridad conocidas. <br /><br /><br /><br />Para identificar vulnerabilidades de seguridad, cada trimestre se realiza un examen de las vulnerabilidades de seguridad completo y dirigido de los componentes prioritarios del entorno de producción de Azure. Los resultados se notifican a las partes interesadas y el equipo realiza el seguimiento de la corrección hasta el cierre. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations), que proporcionan información y corrección de vulnerabilidades, se han usado para asegurarse de que se corrigieron todos los problemas para el CDE de demostración de la tienda web de Contoso.|



### <a name="pci-dss-requirement-1134"></a>Requisito 11.3.4 de PCI DSS

**11.3.4** Si se usó la segmentación para aislar el CDE de otras redes, conviene realizar pruebas de penetración al menos cada año y después de realizar cambios en los controles y métodos de segmentación para comprobar que los métodos de segmentación sean operativos y estén vigentes, así como para aislar todos los sistemas fuera del ámbito de los sistemas en el CDE.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Se han establecido procedimientos para supervisar los componentes de la plataforma de Microsoft Azure en busca de vulnerabilidades de seguridad conocidas. <br /><br /><br /><br />Para identificar vulnerabilidades de seguridad, cada trimestre se realiza un examen de las vulnerabilidades de seguridad completo y dirigido de los componentes prioritarios del entorno de producción de Azure. Los resultados se notifican a las partes interesadas y el equipo realiza el seguimiento de la corrección hasta el cierre. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations), que proporcionan información y corrección de vulnerabilidades, se han usado para asegurarse de que se corrigieron todos los problemas para el CDE de demostración de la tienda web de Contoso.|



### <a name="pci-dss-requirement-11341"></a>Requisito 11.3.4.1 de PCI DSS

**11.3.4.1** *Requisito adicional solo para proveedores de servicios:* Si se usó la segmentación, confirme el ámbito de PCI DSS mediante la realización de pruebas de penetración en los controles de segmentación al menos cada seis meses y después de realizar cambios en los controles y métodos de segmentación.

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018. A partir de esa fecha, será un requisito.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Vea la sección "Microsoft Azure" del [Requisito 11.3.4](#pci-dss-requirement-11-3-4). |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations), que proporcionan información y corrección de vulnerabilidades, se han usado para asegurarse de que se corrigieron todos los problemas para el CDE de demostración de la tienda web de Contoso.|



## <a name="pci-dss-requirement-114"></a>Requisito 11.4 de PCI DSS

**11.4** Use técnicas de detección y prevención de intrusiones para detectar y evitar intrusiones en la red. Supervise todo el tráfico en el perímetro del entorno de datos de los titulares de tarjetas, así como en los puntos críticos del entorno, y avise al personal de los posibles peligros.
Mantenga actualizados los motores, las líneas base y las firmas de detección y prevención de intrusiones.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure realiza análisis en tiempo real de los eventos dentro de su entorno operativo, y los sistemas IDS generan alertas casi en tiempo real sobre los eventos que podrían poner en peligro el sistema. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso es un servicio de PaaS y la prevención y detección de intrusiones de red es responsabilidad de Azure. [Azure Security Center](https://azure.microsoft.com/services/security-center/) y [Azure Advisor](/azure/advisor/advisor-security-recommendations) proporcionan alertas y corrección de intrusiones.|



## <a name="pci-dss-requirement-115"></a>Requisito 11.5 de PCI DSS

**11.5** Implemente un mecanismo de detección de cambios (por ejemplo, herramientas de supervisión de integridad de archivos) para alertar al personal de la modificación no autorizada (incluidos cambios, adiciones y eliminaciones) de archivos críticos del sistema, archivos de configuración o archivos de contenido. Asimismo, configure el software para realizar comparaciones de los archivos imprescindibles al menos con carácter semanal. 

> [!NOTE]
> Para fines de detección de cambios, los archivos imprescindibles normalmente son aquellos que no cambian con frecuencia, pero cuya modificación puede indicar que el sistema está en peligro o que existe riesgo de peligro. Los mecanismos de detección de cambios como los productos de supervisión de la integridad de los archivos normalmente vienen preconfigurados con archivos imprescindibles para el sistema operativo relacionado. Otros archivos imprescindibles, como los relativos a las aplicaciones personalizadas, se deben evaluar y definir por la entidad (es decir, el comerciante o proveedor de servicios).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure mantiene y notifica a los clientes los posibles cambios y eventos que pueden afectar a la seguridad o disponibilidad de los servicios a través de un panel de servicios en línea. Los cambios en los compromisos y obligaciones de seguridad de los clientes de Microsoft Azure se actualizan en el sitio web de Microsoft Azure de manera puntual.<br /><br />La instalación o los cambios de software en el entorno de producción de Microsoft Azure se restringen al personal de administración autorizado y se rigen por los procedimientos de administración de cambios. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La demostración de la tienda web de Contoso es un servicio de PaaS y la detección de cambios se ha implementado mediante OMS. Para más información, vea [Guía de PCI: Soluciones de OMS preinstaladas](payment-processing-blueprint.md#oms-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>Requisito 11.5.1 de PCI DSS

**11.5.1** Implemente un proceso para responder a las alertas generadas por la solución de detección de cambios.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Las reglas del evento de supervisión de Azure proporcionan un mayor nivel de supervisión para operaciones y recursos de alto riesgo. El cumplimiento normativo de los dispositivos de red administrados por Azure se supervisa con estándares de seguridad establecidos. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Las alertas de cambios de la tienda web de Contoso se proporcionan con la implementación de OMS. Para más información, vea [Guía de PCI: Soluciones de OMS preinstaladas](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>Requisito 11.6 de PCI DSS

**11.6** Asegúrese de que las directivas de seguridad y los procedimientos operativos para la supervisión y pruebas de seguridad estén documentados, en uso y sean conocidos por todas las partes afectadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Las alertas de cambios de la tienda web de Contoso se proporcionan con la implementación de OMS. Para más información, vea [Guía de PCI: Soluciones de OMS preinstaladas](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|




