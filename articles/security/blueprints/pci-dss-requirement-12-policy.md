---
title: 'Proyecto del procesamiento de pagos de Azure: requisitos de directiva'
description: Requisito 12 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 05e9ed7c886d37a024db1eedbc541705b7d8a9a9
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Requisitos de directiva para entornos compatibles con PCI DSS  
## <a name="pci-dss-requirement-12"></a>Requisito 12 de PCI DSS

**Mantener una directiva que trate la seguridad de la información de todos los usuarios**

> [!NOTE]
> [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) (Consejo de estándares de seguridad para la industria de tarjeta de pago) define estos requisitos como parte del [Estándar de Seguridad de Datos PCI (PCI DSS), versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de comprobación e instrucciones para cada requisito.

Una directiva de seguridad fiable establece el tono de seguridad de toda la entidad e informa al personal técnico de lo que se espera de él. Toda la plantilla debe ser consciente de la confidencialidad de los datos y la responsabilidad que tienen para protegerlos. A fin de cumplir el Requisito 12, el término "personal" hace referencia a los empleados a jornada completa y a tiempo parcial, a empleados temporales, contratistas y consultores que se encuentran "físicamente presentes" en las instalaciones de la entidad o que tienen acceso al entorno de datos de los titulares de las tarjetas.

## <a name="pci-dss-requirement-121"></a>Requisito 12.1 de PCI DSS

**12.1** Establecer, publicar, mantener y distribuir una directiva de seguridad.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes se deben encargar de establecer y mantener una directiva de seguridad de información.|



### <a name="pci-dss-requirement-1211"></a>Requisito 12.1.1 de PCI DSS

**12.1.1** Revisar la directiva de seguridad al menos cada año y actualizarla cuando cambie el entorno.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben encargarse de actualizar su directiva de seguridad de información al menos cada año o cuando haya cambios en el entorno de datos de los titulares de las tarjetas (CDE).|



## <a name="pci-dss-requirement-122"></a>Requisito 12.2 de PCI DSS

**12.2** Implementar un proceso de evaluación de riesgos que:
- Se lleve a cabo al menos cada año y cuando haya cambios significativos en el entorno (por ejemplo, adquisición, fusión, reubicación, etcétera).
- Identifique las vulnerabilidades, amenazas y activos críticos.
- Proporcione un análisis de riesgos formal y documentado.
- > En los ejemplos de las metodologías de evaluación de riesgos se incluyen, entre otros, el estándar OCTAVA, ISO 27005 y NIST SP 800-30.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de implementar un proceso de evaluación de riesgos que aborde todas las amenazas que aparecen en el requisito 12.2.|



## <a name="pci-dss-requirement-123"></a>Requisito 12.3 de PCI DSS

**12.3** Desarrollar directivas de uso de tecnologías críticas y definir el uso correcto de estas tecnologías.

> [!NOTE]
> En los ejemplos de tecnologías críticas se incluyen, entre otras cosas, tecnologías de acceso remoto e inalámbricas, equipos portátiles, tabletas, medios extraíbles electrónicos, formas de usar el correo electrónico y formas de usar Internet.
Asegúrese de que estas directivas de uso solicitan lo siguiente.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1231"></a>Requisito 12.3.1 de PCI DSS

**12.3.1** Visto bueno explícito de las entidades autorizadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1232"></a>Requisito 12.3.2 de PCI DSS

**12.3.2** Autenticación para usar la tecnología.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1233"></a>Requisito 12.3.3 de PCI DSS

**12.3.3** Una lista de todos estos dispositivos y el personal con acceso.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1234"></a>Requisito 12.3.4 de PCI DSS

**12.3.4** Un método para determinar de forma precisa y fácil quién es el propietario, la información de contacto y el propósito (por ejemplo, etiquetar, codificar y realizar un inventario de los dispositivos).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1235"></a>Requisito 12.3.5 de PCI DSS

**12.3.5** Usos aceptables de la tecnología.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1236"></a>Requisito 12.3.6 de PCI DSS

**12.3.6** Ubicaciones de red aceptables de las tecnologías.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de determinar las ubicaciones de red aceptables para las VM basadas en la nube, y los servicios de almacenamiento y soporte técnico.|



### <a name="pci-dss-requirement-1237"></a>Requisito 12.3.7 de PCI DSS

**12.3.7** Lista de productos que aprobó la empresa.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de determinar las ubicaciones de red aceptables para las VM basadas en la nube, y los servicios de almacenamiento y soporte técnico.|



### <a name="pci-dss-requirement-1238"></a>Requisito 12.3.8 de PCI DSS

**12.3.8** Desconexión automática de las sesiones de las tecnologías de acceso remoto después de un período de inactividad específico.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa la funcionalidad de bloqueo de sesión de AD corporativa de Microsoft, que bloquea la sesión tras un período de inactividad. Las conexiones de red se terminan después de 30 minutos de inactividad. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1239"></a>Requisito 12.3.9 de PCI DSS

**12.3.9** Activación de tecnologías de acceso remoto para proveedores y asociados de negocios solo cuando estos lo necesiten, con función de desactivación inmediata tras su uso.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-12310"></a>Requisito 12.3.10 de PCI DSS

**12.3.10** Si el personal tiene acceso a los datos de los titulares de tarjetas a través de tecnologías de acceso remoto, se usa para prohibir que se copien, se muevan o se almacenen estos datos en discos duros locales y en medios electrónicos extraíbles, a menos que se tenga una autorización explícita para solventar una necesidad empresarial determinada.
Si existe una necesidad empresarial autorizada, las directivas de uso deben solicitar que los datos estén protegidos conforme a todos los requisitos de DSS PCI aplicables.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben encargarse de garantizar que el personal que tiene acceso a los datos de los titulares de tarjetas mediante tecnologías de acceso remoto no pueda copiar, mover o guardar estos datos en discos duros locales o en medios electrónicos extraíbles, a no ser que tenga una autorización explícita para solventar una necesidad empresarial determinada.|



## <a name="pci-dss-requirement-124"></a>Requisito 12.4 de PCI DSS

**12.4** Asegúrese de que la directiva y los procedimientos de seguridad definen claramente para el personal las responsabilidades para la seguridad de la información.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1241"></a>Requisito 12.4.1 de PCI DSS

**12.4.1** Requisito adicional solo para proveedores de servicios: los ejecutivos de administración deben establecer las responsabilidades para proteger los datos de los titulares de tarjetas y un programa de cumplimiento de PCI DSS que incluya lo siguiente:
- Responsabilidad general para mantener el cumplimiento de PCI DSS.
- Definir el acta de un programa de cumplimiento de PCI DSS y comunicación con los ejecutivos de administración. 

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018; después de esta fecha se convertirá en un requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes que son proveedores de servicios tienen la obligación de documentar su programa de cumplimiento de PCI.|



## <a name="pci-dss-requirement-125"></a>Requisito 12.5 de PCI DSS

**12,5** Asignar a un usuario o grupo las siguientes responsabilidades de administración de seguridad de la información del equipo.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben definir y asignar las responsabilidades de seguridad de la información a sus empleados.|



### <a name="pci-dss-requirement-1251"></a>Requisito 12.5.1 de PCI DSS

**12.5.1** Establecer, documentar y distribuir directivas de seguridad y procedimientos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben definir y asignar las responsabilidades de seguridad de la información para sus empleados.|



### <a name="pci-dss-requirement-1252"></a>Requisito 12.5.2 de PCI DSS

**12.5.2** Supervisar y analizar las alertas de seguridad e información y distribuirlas al personal adecuado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben definir y asignar las responsabilidades de seguridad de la información para sus empleados.|



### <a name="pci-dss-requirement-1253"></a>Requisito 12.5.3 de PCI DSS

**12.5.3** Establecer, documentar y distribuir la respuesta ante incidentes y los procedimientos de escalabilidad para garantizar que todas las situaciones se manejan de forma oportuna y eficaz.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1254"></a>Requisito 12.5.4 de PCI DSS

**12.5.4** Administrar cuentas de usuario, incluyendo los elementos que se hayan agregado, eliminado y modificado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



### <a name="pci-dss-requirement-1255"></a>Requisito 12.5.5 de PCI DSS

**12.5.5** Supervisar y controlar todos los accesos a los datos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de crear y mantener aquellas directivas que dicten el uso correcto, la implementación y la autenticación de tecnologías críticas en su CDE.|



## <a name="pci-dss-requirement-126"></a>Requisito 12.6 de PCI DSS

**12.6** Implementar un programa de reconocimiento de seguridad formal para hacer que todo el personal sea consciente de la importancia de la directiva de seguridad de los datos de los titulares de tarjetas y de los procedimientos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben encargarse de crear y mantener directivas acerca del reconocimiento de la seguridad que estén dirigidas al personal con acceso a CDE.|



### <a name="pci-dss-requirement-1261"></a>Requisito 12.6.1 de PCI DSS

**12.6.1** Ofrecer cursos al personal tras la contratación y al menos cada año. 

> [!NOTE]
> Estos métodos pueden variar según el rol de cada trabajador y su nivel de acceso a los datos de los titulares de tarjetas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben encargarse de garantizar que el personal recibe al menos cada año cursos específicos acerca de la seguridad de la información y los estándares de PCI-DSS.|



### <a name="pci-dss-requirement-1262"></a>Requisito 12.6.2 de PCI DSS

**12.6.2** Requerir al personal que confirme de forma anual que ha leído y entendido la directiva de seguridad y los procedimientos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben encargarse de garantizar que el personal recibe al menos cada año cursos específicos acerca de la seguridad de la información y los estándares de PCI-DSS.|



## <a name="pci-dss-requirement-127"></a>Requisito 12.7 de PCI DSS

**12.7** Filtrar los posibles candidatos a personal para minimizar el riesgo de ataques de carácter interno. (Por ejemplo, se pueden comprobar el historial previo de empleo, los antecedentes penales, el historial de crédito y otras referencias). 

> [!NOTE]
> Este requisito puede tomarse como una recomendación si se trata de personal que se vaya a contratar para ciertos puestos como, por ejemplo, el de cajero, que solo tiene acceso a un número de tarjeta cada vez que realiza una transacción.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben asegurarse de que se haya comprobado como es debido el historial de todo el personal con acceso al CDE.|



## <a name="pci-dss-requirement-128"></a>Requisito 12.8 de PCI DSS

**12.8** Mantener e implementar directivas y procedimientos para administrar los proveedores de servicios con los cuales se comparten los datos de los titulares de tarjetas o que podrían afectar a la seguridad de estos datos, tal como se indica a continuación.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben asegurarse de que los proveedores del servicio cumplen el estándar PCI, ya que se comparten con ellos los datos de los titulares de las tarjetas y esto podría afectar a la seguridad del CDE. Los clientes deben tener una lista de todos los proveedores de servicios que se usan en el CDE.|



### <a name="pci-dss-requirement-1281"></a>Requisito 12.8.1 de PCI DSS

**12.8.1** Mantener una lista de proveedores de servicios, incluyendo una descripción del servicio proporcionado.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes deben asegurarse de que los proveedores del servicio cumplen el estándar PCI, ya que se comparten con ellos los datos de los titulares de las tarjetas y esto podría afectar a la seguridad del CDE. Los clientes deben tener una lista de todos los proveedores de servicios que se usan en el CDE.|



### <a name="pci-dss-requirement-1282"></a>Requisito 12.8.2 de PCI DSS

**12.8.2** Mantener un contrato escrito que incluya una confirmación de que los proveedores de servicios se hacen responsables de la seguridad de aquellos datos de los titulares de tarjetas a los que tienen acceso, que guardan, que procesan o que transmiten en nombre del cliente, o en la medida en que puedan afectar a la seguridad del entorno de datos de los clientes que sean titulares de tarjetas. 

> [!NOTE]
> El texto exacto de una confirmación dependerá del contrato entre las dos partes, los detalles del servicio que se proporciona y las responsabilidades asignadas a cada parte. La confirmación no tiene que incluir el texto exacto que se proporciona en este requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes se hacen responsables de mantener acuerdos por escrito con aquellos proveedores de servicios que confirmen la responsabilidad de garantizar la seguridad de los datos de los titulares de tarjetas.|



### <a name="pci-dss-requirement-1283"></a>Requisito 12.8.3 de PCI DSS

**12.8.3** Asegúrese de que hay un proceso establecido para contratar proveedores de servicios, incluyendo la realización de las diligencias adecuadas antes de realizar el contrato.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los que deben asegurarse de que hay un proceso establecido para contratar proveedores de servicios, incluyendo la realización de las diligencias adecuadas antes de realizar el contrato.|



### <a name="pci-dss-requirement-1284"></a>Requisito 12.8.4 de PCI DSS

**12.8.4** Mantener un programa anual para supervisar el estado de cumplimiento de PCI DSS de los proveedores de servicios.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | El cliente es el responsable de mantener un programa anual para supervisar el estado de cumplimiento de PCI DSS de los proveedores de servicios.|



### <a name="pci-dss-requirement-1285"></a>Requisito 12.8.5 de PCI DSS

**12.8.5** Mantener información acerca de qué requisitos de PCI DSS administra cada proveedor de servicios y cuáles administra la entidad.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes tienen la responsabilidad de guardar una copia de la [matriz del resumen de responsabilidades](https://aka.ms/pciblueprintcrm32), en la cual se describen los requisitos de PCI DSS que son responsabilidad de esos clientes, quienes, a su vez, son responsabilidad de Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>Requisito 12.9 de PCI DSS

**12.9** Requisito adicional solo para proveedores de servicios: los proveedores de servicios deben confirmar por escrito a los clientes que se hacen responsables de la seguridad de aquellos datos de los titulares de tarjetas a los que tienen acceso, que guardan, que procesan o que transmiten en nombre de un cliente, o en la medida en que puedan afectar a la seguridad del entorno de datos de los clientes que sean titulares de tarjetas. 

> [!NOTE]
> El texto exacto de una confirmación dependerá del contrato entre las dos partes, los detalles del servicio que se proporciona y las responsabilidades asignadas a cada parte. La confirmación no tiene que incluir el texto exacto que se proporciona en este requisito.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes que sean proveedores de servicios deben confirmar sus responsabilidades para cumplir con el estándar PCI. |



## <a name="pci-dss-requirement-1210"></a>Requisito 12.10 de PCI DSS

**12.10** Implementar un plan de respuesta ante incidentes. Debe estar preparado para responder inmediatamente a una infracción de seguridad del sistema.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de desarrollar planes y pruebas de IR que tengan en cuenta los controles de los clientes en relación con los puntos de contacto compartidos y las aplicaciones de los clientes que aprovechen la infraestructura de Azure. Es responsabilidad del cliente proporcionar información de contacto precisa a Azure en caso de que se deba notificar un incidente que pueda afectar a la aplicación o a los datos.|



### <a name="pci-dss-requirement-12101"></a>Requisito 12.10.1 de PCI DSS

**12.10.1** Crear el plan de respuesta ante incidentes que se implementará en caso de que se produzca una infracción de seguridad en el sistema. Asegúrese de que el plan aborda los siguientes puntos, como mínimo:
- Roles, responsabilidades y estrategias de comunicación y contacto en caso de que haya algún problema que incluya la notificación de las marcas de pago, como mínimo.
- Procedimientos de respuesta ante incidentes específicos.
- Procedimientos de recuperación y continuidad del negocio.
- Procesos de copia de seguridad de datos.
- Análisis de los requisitos legales para informar de los compromisos.
- Cobertura y respuestas de todos los componentes críticos del sistema.
- Referencia o inclusión de los procedimientos de respuesta a incidentes desde las marcas de pago.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de desarrollar planes y pruebas de IR que tengan en cuenta los controles de los clientes en relación con los puntos de contacto compartidos y las aplicaciones de los clientes que aprovechen la infraestructura de Azure. Es responsabilidad del cliente proporcionar información de contacto precisa a Azure en caso de que se deba notificar un incidente que pueda afectar a la aplicación o a los datos.|



### <a name="pci-dss-requirement-12102"></a>Requisito 12.10.2 de PCI DSS

**12.10.2** Revisar y probar el plan al menos cada año, incluyendo todos los elementos que se enumeran en el requisito 12.10.1.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de desarrollar planes y pruebas de IR que tengan en cuenta los controles de los clientes en relación con los puntos de contacto compartidos y las aplicaciones de los clientes que aprovechen la infraestructura de Azure. Es responsabilidad del cliente proporcionar información de contacto precisa a Azure en caso de que se deba notificar un incidente que pueda afectar a la aplicación o a los datos.|



### <a name="pci-dss-requirement-12103"></a>Requisito 12.10.3 de PCI DSS

**12.10.3** Designar personal específico que esté disponible las 24 horas del día los 7 días de la semana para responder a las alertas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de desarrollar planes y pruebas de IR que tengan en cuenta los controles de los clientes en relación con los puntos de contacto compartidos y las aplicaciones de los clientes que aprovechen la infraestructura de Azure. Es responsabilidad del cliente proporcionar información de contacto precisa a Azure en caso de que se deba notificar un incidente que pueda afectar a la aplicación o a los datos.|



### <a name="pci-dss-requirement-12104"></a>Requisito 12.10.4 de PCI DSS

**12.10.4** Proporcionar cursos adecuados al personal dedicado a responder en caso de que se produzca alguna infracción de seguridad.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de desarrollar planes y pruebas de IR que tengan en cuenta los controles de los clientes en relación con los puntos de contacto compartidos y las aplicaciones de los clientes que aprovechen la infraestructura de Azure. Es responsabilidad del cliente proporcionar información de contacto precisa a Azure en caso de que se deba notificar un incidente que pueda afectar a la aplicación o a los datos.|



### <a name="pci-dss-requirement-12105"></a>Requisito 12.10.5 de PCI DSS

**12.10.5** Incluye alertas de los sistemas de supervisión de seguridad entre los que se incluyen la detección de intrusos, la prevención de intrusiones, los firewalls y los sistemas para la supervisión de la integridad de los archivos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de desarrollar planes y pruebas de IR que tengan en cuenta los controles de los clientes en relación con los puntos de contacto compartidos y las aplicaciones de los clientes que aprovechen la infraestructura de Azure. Es responsabilidad del cliente proporcionar información de contacto precisa a Azure en caso de que se deba notificar un incidente que pueda afectar a la aplicación o a los datos.|



### <a name="pci-dss-requirement-12106"></a>Requisito 12.10.6 de PCI DSS

**12.10.6** Desarrollar un proceso para modificar y desarrollar el plan de respuesta ante incidentes según las lecciones aprendidas e incorporar los progresos conseguidos en el sector.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes son los encargados de desarrollar planes y pruebas de IR que tengan en cuenta los controles de los clientes en relación con los puntos de contacto compartidos y las aplicaciones de los clientes que aprovechen la infraestructura de Azure. Es responsabilidad del cliente proporcionar información de contacto precisa a Azure en caso de que se deba notificar un incidente que pueda afectar a la aplicación o a los datos.|



## <a name="pci-dss-requirement-1211"></a>Requisito 12.11 de PCI DSS

**12.11** **Requisito adicional solo para proveedores de servicios:** realizar revisiones trimestrales para confirmar que el personal sigue la directiva de seguridad y los procedimientos operativos.
Las revisiones deben incluir los siguientes procesos:
- Revisiones de registros diarias
- Revisiones del conjunto de reglas de firewall
- Aplicar los estándares de configuración a los nuevos sistemas
- Responder a alertas de seguridad
- Cambiar los procesos de administración 

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018; después de esta fecha se convertirá en un requisito.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes que son proveedores de servicios deben encargarse de documentar sus revisiones de procesos para confirmar el rendimiento de control del cumplimiento de PCI.|



### <a name="pci-dss-requirement-12111"></a>Requisito 12.11.1 de PCI DSS

**12.11.1** Requisito adicional solo para proveedores de servicios: mantener la documentación del proceso de revisión trimestral para incluir:
- La documentación de los resultados de las revisiones.
- La revisión y cierre de sesión de los resultados del personal responsable del programa de cumplimiento PCI DSS. 

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018; después de esta fecha se convertirá en un requisito.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | Los clientes que son proveedores de servicios deben encargarse de documentar sus revisiones de procesos para confirmar el rendimiento de control del cumplimiento de PCI.|




