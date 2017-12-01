---
title: 'Plano de procesamiento de pagos de Azure: requisitos de sistemas seguros'
description: Requisito 6 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 24c8d90d3fec27258165472e99ba3d36ffcba733
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Requisitos de sistemas seguros para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-6"></a>Requisito 6 de PCI DSS

**Desarrollar y mantener aplicaciones y sistemas seguros**

> [!NOTE]
> Estos requisitos los define el [Consejo de Estándares de Seguridad para la Industria de Tarjeta de Pago (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte del [Estándar de Seguridad de Datos PCI (DSS) versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de pruebas e instrucciones para cada requisito.

Hay usuarios sin escrúpulos que usan las vulnerabilidades de seguridad para obtener acceso con privilegios a los sistemas. Muchas de estas vulnerabilidades las fijan las revisiones de seguridad proporcionadas por el proveedor, que deben instalarlas las entidades que administran los sistemas. Todos los sistemas deben tener todas las revisiones de software apropiadas para protegerse contra las acciones que pongan en peligro los datos de titulares de tarjetas y los ataques que realizan usuarios y software malintencionados.

> [!NOTE]
> Las revisiones de software apropiadas son las que se han evaluado y probado lo suficiente como para determinar que no entran en conflicto con las configuraciones de seguridad existentes. En el caso de las aplicaciones desarrolladas internamente, muchas vulnerabilidades pueden evitarse mediante el uso de procesos de desarrollo estándar del sistema y técnicas de codificación segura.

## <a name="pci-dss-requirement-61"></a>Requisito 6.1 de PCI DSS

**6.1** Establecer un proceso para identificar vulnerabilidades de seguridad mediante el uso de orígenes externos de buena reputación para la información sobre las vulnerabilidades de seguridad y asignar una clasificación de riesgo de seguridad (por ejemplo, "alto", "medio" o "bajo") a las vulnerabilidades de seguridad detectadas recientemente.

> [!NOTE]
> Las clasificaciones de riesgo se deben basar en los procedimientos recomendados por el sector, así como teniendo en cuenta un posible impacto. Por ejemplo, los criterios para clasificar las vulnerabilidades pueden incluir la consideración de la puntuación base de CVSS, la clasificación por parte del proveedor o el tipo de sistemas afectados. 
> 
> Los métodos de evaluación de vulnerabilidades y asignación de clasificaciones de riesgo variarán en función de la estrategia de evaluación de riesgos y el entorno de la organización. Las clasificaciones de riesgo deben, como mínimo, identificar todas las vulnerabilidades que se consideren un "alto riesgo" para el entorno. Además de la clasificación de riesgo, las vulnerabilidades pueden considerarse "críticas" si suponen una amenaza inminente para el entorno, si afectan a sistemas fundamentales o si podrían dar lugar a un riesgo potencial si no se resuelven. Algunos ejemplos de sistemas fundamentales pueden incluir los sistemas de seguridad, los sistemas y dispositivos orientados al público, bases de datos y otros sistemas que almacenan, procesan o transmiten datos de titulares de tarjetas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los procedimientos se han establecido e implementado para examinar vulnerabilidades en los hosts de hipervisor en el límite del ámbito. El examen de vulnerabilidades se ejecuta en sistemas operativos de servidor, bases de datos y dispositivos de red con las herramientas de examen de vulnerabilidades adecuadas. Los exámenes de vulnerabilidades se realizan de forma trimestral como mínimo. Microsoft Azure contrata asesores independientes para realizar pruebas de penetración del límite de Microsoft Azure. También se realizan ejercicios de simulacro de ataques regularmente y los resultados se usan para realizar mejoras de seguridad. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore reduce el riesgo de sufrir vulnerabilidades de seguridad ya que usa Application Gateway con WAF y tiene el conjunto de reglas OWASP habilitado. Para obtener más información, consulte [PCI Guidance - Mitigation of the Risk of Security Vulnerabilities](payment-processing-blueprint.md#application-gateway) (Guía de PCI: mitigación del riesgo de las vulnerabilidades de seguridad).|



## <a name="pci-dss-requirement-62"></a>Requisito 6.2 de PCI DSS

**6.2** Asegúrese de que todos los componentes del sistema y el software están protegidos contra vulnerabilidades conocidas mediante la instalación de revisiones de seguridad proporcionadas por el proveedor aplicable. Instale revisiones de seguridad críticas en el primer mes de lanzamiento.

> [!NOTE]
> Las revisiones de seguridad críticas deben identificarse según la clasificación de proceso de riesgo definida en el requisito 6.1.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure es responsable de garantizar que todos los dispositivos de red y software de sistema operativo del hipervisor estén protegidos contra vulnerabilidades conocidas mediante la instalación de revisiones de seguridad proporcionadas por el proveedor aplicable. A menos que un cliente solicite no usar el servicio, existe un proceso de administración de revisiones para garantizar que las vulnerabilidades en el nivel del sistema operativo se impidieron y corrigieron de manera oportuna. Los servidores de producción se examinan para validar el cumplimiento de las revisiones mensualmente. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore es una solución de servicio de PaaS. Azure proporciona mantenimiento para todas las revisiones del servicio.|



## <a name="pci-dss-requirement-63"></a>Requisito 6.3 de PCI DSS

**6.3** Desarrollar aplicaciones de software internas y externas (incluido el acceso administrativo a las aplicaciones basado en web) de forma segura, como sigue:
- De acuerdo con PCI DSS (por ejemplo, registro y autenticación seguros)
- En función de estándares o procedimientos recomendados del sector
- Mediante la incorporación de seguridad de la información durante el ciclo de vida de desarrollo del software 

> [!NOTE]
> Esto se aplica a todo el software desarrollado internamente, así como al software personalizado y diseñado a medida desarrollado por un tercero.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los puntos de conexión y las aplicaciones de Microsoft Azure se desarrollan según la metodología de Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft que está en consonancia con los requisitos de DSS. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore se diseñó para seguir los procedimientos recomendados del sector para proteger CHD. La guía de implementación proporciona los detalles de las medidas de seguridad y el registro está habilitado.|



### <a name="pci-dss-requirement-631"></a>Requisito 6.3.1 de PCI DSS

**6.3.1** Eliminar las cuentas, los id. de usuario y las contraseñas de aplicaciones en desarrollo, de prueba o personalizadas antes de activar dichas aplicaciones o de que se liberen para los clientes.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Un asesor de seguridad designado fuera del equipo de desarrollo de Azure realiza una revisión de seguridad final (FSR) de las versiones principales antes de la implementación de producción para garantizar que solo se liberan las aplicaciones listas para la producción. Como parte de esta revisión final, se garantiza que se han eliminado todas las cuentas y datos de prueba. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona un servicio de almacenamiento provisional que se ha registrado y aislado. Cada uno de los niveles de red tiene un grupo de seguridad de red dedicado [NSG]. Para obtener más información, consulte [Guía de PCI: grupos de seguridad de red](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>Requisito 6.3.2 de PCI DSS

**6.3.2** Revisar el código personalizado antes de liberarlo para su producción o para los clientes con el fin de identificar cualquier posible vulnerabilidad de codificación (mediante procesos manuales o automatizados) para incluir al menos lo siguiente:
- Los cambios en el código los revisan personas diferentes al autor del código de origen y conocedoras de las técnicas de revisión de código y de los procedimientos de codificación segura.
- Las revisiones del código aseguran que este se desarrolla según las instrucciones de codificación segura.
- Las correcciones correspondientes se implementan antes de la versión.
- La administración revisa y aprueba los resultados de la revisión de código antes de la versión. 

> [!NOTE]
> Este requisito de las revisiones de código se aplica a todo el código personalizado (tanto interno como de acceso público) como parte del ciclo de vida de desarrollo del sistema. 
>
> Las revisiones de código las pueden llevar a cabo terceros o personal interno conocedor. Las aplicaciones web de acceso público también están sujetas a controles adicionales, a fin de abordar vulnerabilidades y amenazas continuas tras la implementación, tal como se define en el requisito 6.6 de PCI DSS.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los puntos de conexión y las aplicaciones de Microsoft Azure se desarrollan según la metodología de Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona un servicio de almacenamiento provisional que se ha registrado y aislado. Cada uno de los niveles de red tiene un grupo de seguridad de red dedicado [NSG]. Para obtener más información, consulte [Guía de PCI: grupos de seguridad de red](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>Requisito 6.4 de PCI DSS

**6.4** Seguir los procedimientos y procesos de control de cambios para todos los cambios en los componentes del sistema. Los procesos deben incluir los siguientes aspectos (consulte los requisitos 6.4.1 a 6.4.6).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft sigue las instrucciones del NIST en relación con las consideraciones de seguridad acerca del desarrollo de software y esa información de seguridad se debe integrar en el SDLC desde el inicio del sistema. La integración continua de recomendaciones de seguridad en Microsoft SDL permite:<ul><li>La identificación temprana y la mitigación de vulnerabilidades de seguridad y configuraciones erróneas</li><li>El reconocimiento de posibles problemas de codificación de software causados por los controles de seguridad necesarios</li><li>La identificación de los servicios de seguridad compartida y la reutilización de herramientas de procedimientos recomendados de seguridad, lo que mejora la posición de seguridad a través de métodos y técnicas de eficacia probada</li><li>El cumplimiento del programa de administración de riesgos ya completo de Microsoft</li></ul>Microsoft Azure ha establecido los procesos de administración de versiones y cambios para controlar la implementación de los cambios más importantes, incluidas:<ul><li>La identificación y la documentación del cambio previsto</li><li>La identificación de los objetivos empresariales, las prioridades y los escenarios durante la planificación del producto</li><li>La especificación del diseño de la función o del componente</li><li>La revisión de la preparación operativa en función de unos criterios predefinidos o de una lista de comprobación para evaluar el impacto o el riesgo general</li><li>La administración de cambios, pruebas y autorizaciones según criterios de entrada o salida para entornos de DEV (desarrollo), INT (prueba de integración), STAGE (preproducción) y PROD (producción), según corresponda. Los clientes son responsables de sus propias aplicaciones hospedadas en Microsoft Azure.</li></ul> |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona un servicio de almacenamiento provisional que se ha registrado y aislado. <br /><br />Cada uno de los niveles de red tiene un grupo de seguridad de red dedicado [NSG]. Para obtener más información, consulte [Guía de PCI: grupos de seguridad de red](payment-processing-blueprint.md#network-security-groups).<br /><br />Los cambios se registran con Operations Management Suite y los runbooks se usan para recopilar registros. [Operations Management Suite (OMS)](/azure/operations-management-suite/) proporciona un registro completo de los cambios. Se puede revisar y comprobar la exactitud de los cambios. Para obtener instrucciones más específicas, consulte [PCI Guidance - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) (Guía de PCI: Operations Management Suite).|



### <a name="pci-dss-requirement-641"></a>Requisito 6.4.1 de PCI DSS

**6.4.1** Separar los entornos de desarrollo o de prueba de los entornos de producción y aplicar la separación con controles de acceso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona un servicio de almacenamiento provisional que se ha registrado y aislado. Cada uno de los niveles de red tiene un grupo de seguridad de red dedicado [NSG]. Para obtener más información, consulte [Guía de PCI: grupos de seguridad de red](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>Requisito 6.4.2 de PCI DSS

**6.4.2** Separación de obligaciones entre entornos de desarrollo o de prueba y de producción

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona un servicio de almacenamiento provisional que se ha registrado y aislado. Cada uno de los niveles de red tiene un grupo de seguridad de red dedicado [NSG]. Para obtener más información, consulte [Guía de PCI: grupos de seguridad de red](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>Requisito 6.4.3 de PCI DSS

**6.4.3** La producción de datos (PAN en vivo) no se usa para pruebas ni desarrollo.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore no tiene datos en directo del número de cuenta principal (PAN).|



### <a name="pci-dss-requirement-644"></a>Requisito 6.4.4 de PCI DSS

**6.4.4** La eliminación de los datos de prueba y de las cuentas de los componentes del sistema antes de que el sistema pase a estar activo o entre en producción.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore no tiene ninguna cuenta de prueba.|



### <a name="pci-dss-requirement-645"></a>Requisito 6.4.5 de PCI DSS

**6.4.5** Los procedimientos de control de cambios para la implementación de revisiones de seguridad y las modificaciones de software deben incluir lo siguiente:
- **6.5.4.1** Documentación de impacto.
- **6.5.4.2** Aprobación del cambio documentado por parte de las entidades autorizadas.
- **6.5.4.3** Prueba de funcionalidades para comprobar que el cambio no afecta negativamente a la seguridad del sistema.
- **6.5.4.4** Procedimientos de reserva.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore es una solución de servicio de PaaS. Azure proporciona mantenimiento para todas las revisiones del servicio.|



### <a name="pci-dss-requirement-646"></a>Requisito 6.4.6 de PCI DSS

**6.4.6** Tras la finalización de un cambio importante, se deben implementar todos los requisitos pertinentes de PCI DSS en todas las redes y sistemas nuevos o modificados, así como en la documentación actualizada según corresponda.

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018. Después, será un requisito.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore es una solución de servicio de PaaS. Azure proporciona mantenimiento para todas las revisiones del servicio.|



## <a name="pci-dss-requirement-65"></a>Requisito 6.5 de PCI DSS

**6.5** Resolución de las vulnerabilidades de codificación comunes en los procesos de desarrollo de software como se muestra a continuación:
- Formar a los desarrolladores al menos anualmente en técnicas de codificación segura actualizadas, incluida la forma de evitar vulnerabilidades de codificación comunes.
- Desarrollar aplicaciones basadas en instrucciones de codificación segura.

> [!NOTE]
> Las vulnerabilidades que se enumeran desde la sección 6.5.1 hasta la sección 6.5.10 eran las que se incluían en los procedimientos recomendados del sector cuando se publicó esta versión de PCI DSS. Sin embargo, a medida que se actualizan los procedimientos recomendados del sector acerca de la administración de vulnerabilidades (por ejemplo, la guía OWASP, SANS CWE Top 25, la codificación segura de CERT, etc.), deben usarse los procedimientos recomendados actuales para estos requisitos. 
> 
> [!NOTE]
> Los requisitos de 6.5.1 a 6.5.6 que se enumeran a continuación se aplican a todas las aplicaciones (internas o externas). Los requisitos de 6.5.7 a 6.5.10 que se enumeran a continuación se aplican a todas las aplicaciones web o interfaces de aplicaciones (internas o externas). 

- **6.5.1** Errores de inyección, especialmente la inyección de SQL. También se consideran los errores de inyección de la inyección de comandos del sistema operativo, de LDAP y de XPath, así como otros errores de este tipo.
- **6.5.2** Desbordamientos del búfer
- **6.5.3** Almacenamiento criptográfico no seguro
- **6.5.4** Comunicaciones no seguras
- **6.5.5** Control incorrecto de errores
- **6.5.6** Todas las vulnerabilidades de "alto riesgo" identificadas en el proceso de identificación de vulnerabilidades (tal y como se define en el requisito 6.1 de PCI DSS)
- **6.5.7** Creación de scripts de sitios (XSS)
- **6.5.8** Control de acceso incorrecto (por ejemplo, referencias a objetos directos no seguros, errores al restringir el acceso URL, recorrido de directorio y errores al restringir el acceso de usuario a las funciones)
- **6.5.9** Falsificación de solicitud entre sitios (CSRF)
- **6.5.10** Administración de sesiones y autenticación interrumpidas

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Consulte la sección "Microsoft Azure" del [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | La demostración de Contoso Webstore proporciona instrucciones para proteger el desarrollo, un DFD y un modelo de amenazas para ilustrar las recomendaciones de desarrollo seguras.|



## <a name="pci-dss-requirement-66"></a>Requisito 6.6 de PCI DSS

**6.6** En el caso de las aplicaciones web de acceso público, aborde las vulnerabilidades y amenazas nuevas de forma continuada y asegúrese de que estas aplicaciones están protegidas frente a ataques conocidos por cualquiera de los métodos siguientes:

- Revisar las aplicaciones web de acceso público a través de herramientas o métodos manuales o automatizados de evaluación de seguridad de vulnerabilidades de aplicaciones, por lo menos anualmente y tras cada cambio 

   > [!NOTE]
   > Esta evaluación no es igual a los análisis de vulnerabilidades llevados a cabo para el requisito 11.2. 

- Instalación de una solución técnica automatizada que detecta y evita los ataques basados en web (por ejemplo, un firewall de aplicación web) frente a aplicaciones web de acceso público, para comprobar continuamente todo el tráfico.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure comprueba las aplicaciones web de acceso público como parte de su proceso de SDL antes de que las aplicaciones se implementan en el entorno de producción. Además, Microsoft examina todas las aplicaciones web de acceso público en producción de forma regular para detectar las posibles vulnerabilidades. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | La solución a la que se hace referencia reduce el riesgo de sufrir vulnerabilidades de seguridad, ya que usa Application Gateway con WAF y tiene el conjunto de reglas OWASP habilitado. Para obtener más información, consulte [PCI Guidance - Mitigation of the Risk of Security Vulnerabilities](payment-processing-blueprint.md#application-gateway) (Guía de PCI: mitigación del riesgo de las vulnerabilidades de seguridad).|



## <a name="pci-dss-requirement-67"></a>Requisito 6.7 de PCI DSS

**6.7** Asegúrese de que las directivas de seguridad y los procedimientos operativos para desarrollar y mantener las aplicaciones y los sistemas seguros queden documentados, en uso y que todas las partes afectadas los conozcan.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | La solución a la que se hace referencia reduce el riesgo de sufrir vulnerabilidades de seguridad, ya que usa Application Gateway con WAF y tiene el conjunto de reglas OWASP habilitado. Para obtener más información, consulte [PCI Guidance - Mitigation of the Risk of Security Vulnerabilities](payment-processing-blueprint.md#application-gateway) (Guía de PCI: mitigación del riesgo de las vulnerabilidades de seguridad).|




