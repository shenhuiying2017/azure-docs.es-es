---
title: "Plano técnico de procesamiento de pagos de Azure: requisitos de contraseña"
description: Requisito 2 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 4ae9fc7d5b53d33f9feb98c450970e0560afa2af
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Requisitos de contraseña para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-2"></a>Requisito 2 de PCI DSS

**No usar valores predeterminados proporcionados por el proveedor para contraseñas de sistemas y otros parámetros de seguridad**

> [!NOTE]
> Estos requisitos los define el [Consejo de Estándares de Seguridad para la Industria de Tarjeta de Pago (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte del [Estándar de Seguridad de Datos PCI (DSS) versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de pruebas e instrucciones para cada requisito.

Los usuarios malintencionados (internos y externos a una entidad) utilizan a menudo las contraseñas predeterminadas del proveedor y otras configuraciones predeterminadas de este para poner en peligro los sistemas. Las comunidades de hackers conocen a la perfección estas contraseñas y opciones de configuración, que se determinan fácilmente a través de información pública.

## <a name="pci-dss-requirement-21"></a>Requisito 2.1 de PCI DSS
 
**2.1** Cambiar siempre los valores predeterminados proporcionados por el proveedor y eliminar o deshabilitar las cuentas predeterminadas innecesarias **antes** de instalar un sistema en la red.
Esto se aplica a TODAS las contraseñas predeterminadas, incluidas, entre otras, aquellas usadas por los sistemas operativos, el software que proporciona los servicios de seguridad, las cuentas de la aplicación y del sistema, los terminales de punto de venta (PDV), las cadenas de la comunidad del protocolo simple de administración de redes (SNMP), etc.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los requisitos de la directiva de contraseñas de Microsoft Azure Active Directory se exigen para las nuevas contraseñas proporcionadas por los clientes en el portal de AADUX. Los cambios de contraseña de autoservicio iniciados por el cliente requieren la validación de la contraseña anterior. Las contraseñas de restablecimiento del administrador deben cambiarse al realizar el siguiente inicio de sesión. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore requiere que los usuarios utilicen contraseñas seguras para todos los usuarios. Ninguna cuenta de invitado o de ejemplo está habilitada en la demostración.<br /><br />Las redes inalámbricas y SNMP no se implementan en la solución.|



### <a name="pci-dss-requirement-211"></a>Requisito 2.1.1 de PCI DSS

**2.1.1** En el caso de los entornos inalámbricos conectados al entorno de datos de titulares de tarjetas o que transmiten este tipo de datos, cambie TODOS los valores predeterminados de proveedores inalámbricos durante la instalación, incluidas, entre otras, las contraseñas, las cadenas de comunidad SNMP y las claves de cifrado inalámbrico predeterminadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Las redes inalámbricas y SNMP no se implementan en la solución.|



## <a name="pci-dss-requirement-22"></a>Requisito 2.2 de PCI DSS

**2.2** Desarrollar estándares de configuración para todos los componentes del sistema. Asegúrese de que estos estándares abordan todas las vulnerabilidades de seguridad conocidas y son coherentes con los estándares de protección de sistemas aceptados en el sector.
Entre los orígenes de los estándares de protección de sistemas aceptados en el sector se pueden incluir:
- Centro de seguridad de Internet (CIS)
- Organización internacional de normalización (ISO)
- Instituto de seguridad, redes, auditorías y administradores del sistema (SANS)
- Tecnología del Instituto nacional de estándares (NIST)

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | En el caso de Microsoft Azure, el equipo de servicios de seguridad técnica OSSC desarrolla estándares de configuración de seguridad para sistemas del entorno de Microsoft Azure que sean coherentes con los estándares de protección aceptados en el sector. Estas opciones de configuración se documentan en las líneas de base del sistema, y los cambios pertinentes realizados en la configuración se comunican a los equipos afectados (p. ej., el equipo IPAK). Los procedimientos se implementan para supervisar el cumplimiento de los estándares de configuración de seguridad. Los estándares de configuración de seguridad para sistemas del entorno de Microsoft Azure son coherentes con los estándares de protección aceptados en el sector y se revisan una vez al año por lo menos. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona protección para todos los servicios en el ámbito del entorno de datos de titulares de tarjetas (CDE). <br /><br />Contoso Webstore también implementa [Azure Security Center](https://azure.microsoft.com/services/security-center/), que proporciona una visión centralizada del estado de la seguridad de todos los recursos de Azure. De un vistazo, puede comprobar que los controles de seguridad adecuados se han implementado y configurado correctamente, y puede identificar rápidamente cualquier recurso que requiera atención.<br /><br />Contoso Webstore usa Operations Management Suite para registrar todos los cambios en el sistema. [Operations Management Suite (OMS)](/azure/operations-management-suite/) proporciona un registro completo de los cambios. Se puede revisar y comprobar la exactitud de los cambios. Para obtener instrucciones más específicas, consulte [PCI Guidance - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) (Guía de PCI: Operations Management Suite).|



### <a name="pci-dss-requirement-221"></a>Requisito 2.2.1 de PCI DSS

**2.2.1** Implementar solo una función principal por servidor para evitar que las funciones que requieren diferentes niveles de seguridad coexistan en el mismo servidor. (Por ejemplo, los servidores web, los servidores de base de datos y DNS deben implementarse en servidores diferentes). 

> [!NOTE]
> En los casos en los que estén en uso las tecnologías de virtualización, implemente solo una función principal por cada componente del sistema virtual.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Los servicios de Contoso Webstore se implementan como servicios PaaS. Todos los servicios están aislados y se segmentan mediante la segmentación de redes.<br /><br />Contoso Webstore también usa la opción [App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para exigir procedimientos clave. Para obtener más información, consulte [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Guía de PCI: App Service Environment).|



### <a name="pci-dss-requirement-222"></a>Requisito 2.2.2 de PCI DSS

**2.2.2** Habilitar solo los servicios, protocolos, demonios, etc. necesarios según se requiera para la función del sistema.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Las configuraciones de hardware y software de Microsoft Azure se revisan al menos cada trimestre para identificar y eliminar las funciones, los puertos, los protocolos y los servicios innecesarios. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Los servicios de Contoso Webstore se implementan como servicios PaaS. Todos los servicios están aislados y se segmentan mediante la segmentación de redes.<br /><br />Contoso Webstore también usa la opción [App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para exigir procedimientos clave. Para obtener más información, consulte [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Guía de PCI: App Service Environment).|



### <a name="pci-dss-requirement-223"></a>Requisito 2.2.3 de PCI DSS

**2.2.3** Implementar funciones de seguridad adicionales para los servicios, protocolos o demonios necesarios que se consideren inseguros. 

> [!NOTE]
> Cuando se utiliza SSL o TLS anticipado, se deben cumplir los requisitos del apéndice A2.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Los servicios de Contoso Webstore se implementan como servicios PaaS. Todos los servicios están aislados y se segmentan mediante la segmentación de redes. La implementación también proporciona protección para todos los servicios del ámbito del CDE. <br /><br />Contoso Webstore también implementa [Azure Security Center](https://azure.microsoft.com/services/security-center/), que proporciona una visión centralizada del estado de la seguridad de todos los recursos de Azure. De un vistazo, puede comprobar que los controles de seguridad adecuados se han implementado y configurado correctamente, y puede identificar rápidamente cualquier recurso que requiera atención.<br /><br />Contoso Webstore también usa la opción [App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para exigir procedimientos clave. Para obtener más información, consulte [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Guía de PCI: App Service Environment).|



### <a name="pci-dss-requirement-224"></a>Requisito 2.2.4 de PCI DSS

**2.2.4** Configurar parámetros de seguridad del sistema para evitar un uso incorrecto.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Azure garantiza que solo el personal autorizado puede configurar los controles de seguridad de la plataforma Azure mediante controles de acceso multifactor y una necesidad empresarial documentada. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore utiliza AAD y RBAC de AD para administrar la implementación correcta de los parámetros de seguridad. Para obtener más información, consulte [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Guía de PCI: Administración de identidades).|



### <a name="pci-dss-requirement-225"></a>Requisito 2.2.5 de PCI DSS

**2.2.5** Quitar todas las funcionalidades innecesarias, como scripts, controladores, funciones, subsistemas, sistemas de archivos y servidores web innecesarios.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona documentación sobre cómo se establecen los límites. El diagrama de flujo de datos y el modelo de amenazas de Contoso muestran todos los servicios usados y controles habilitados.|



## <a name="pci-dss-requirement-23"></a>Requisito 2.3 de PCI DSS

**2.3** Cifrar todo el acceso administrativo que no sea de la consola con criptografía segura. 

> [!NOTE]
> Cuando se utiliza SSL o TLS anticipado, se deben cumplir los requisitos del apéndice A2.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure garantiza que se exija el uso de criptografía segura al acceder a la infraestructura del hipervisor. Microsoft Azure también garantiza que los clientes que usan el Portal de administración de Microsoft Azure puedan tener acceso a sus consolas de servicio o IaaS con criptografía segura. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore muestra cómo se pueden implementar las contraseñas seguras en una solución. Además, se pueden realizar todas las pruebas para comprobar que el cifrado se ha implementado en toda la solución.<br /><br />Contoso Webstore también usa la opción [App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para exigir procedimientos clave. Para obtener más información, consulte [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Guía de PCI: App Service Environment).|



## <a name="pci-dss-requirement-24"></a>Requisito 2.4 de PCI DSS

**2.4** Realizar un inventario de los componentes del sistema que pertenecen al ámbito de PCI DSS.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | El inventario de soluciones de PaaS de demostración de Contoso Webstore puede revisarse en la documentación proporcionada. Para obtener más información, consulte [PCI Guidance - Pre-Installed OMS Solutions](payment-processing-blueprint.md#oms-solutions) (Instrucciones de PCI: soluciones de OMS preinstaladas).|



## <a name="pci-dss-requirement-25"></a>Requisito 2.5 de PCI DSS

**2.5** Asegurarse de que las directivas de seguridad y los procedimientos operativos para administrar los valores predeterminados del proveedor y otros parámetros de seguridad están documentados, en uso y todas las partes afectadas los conocen.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore proporciona documentación que ofrece una visión general de los parámetros de seguridad, así como de los elementos de documentos del servicio. |



## <a name="pci-dss-requirement-26"></a>Requisito 2.6 de PCI DSS

**2.6** Los proveedores de hospedaje compartido deben proteger los datos del titular de la tarjeta y del entorno hospedado de cada entidad. Estos proveedores deben cumplir los requisitos específicos que se detallan en *Appendix A: Additional PCI DSS Requirements for Shared Hosting Providers* (Apéndice A: requisitos adicionales de PCI DSS para los proveedores de hospedaje compartido).

**Responsabilidades:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. Microsoft Azure no es un proveedor de hospedaje compartido. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | No aplicable. Microsoft Azure no es un proveedor de hospedaje compartido.|




