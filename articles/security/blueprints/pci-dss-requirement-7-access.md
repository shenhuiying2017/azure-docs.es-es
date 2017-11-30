---
title: 'Plano de procesamiento de pagos de Azure: requisitos de acceso'
description: Requisito 7 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Requisitos de acceso para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-7"></a>Requisito 7 de PCI DSS

**Restricción del acceso a los datos de los titulares de tarjetas según la necesidad de conocimiento de la información de las empresas**

> [!NOTE]
> El [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) (Consejo de estándares de seguridad para el sector de tarjetas de pago) define estos requisitos como parte del [Estándar de Seguridad de Datos (DDS) para la PCI, versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de comprobación e instrucciones para cada requisito.

Para asegurarse de que solo el personal autorizado puede acceder a los datos más importantes, los sistemas y los procesos deben estar configurados para limitar el acceso según la necesidad de conocimiento de la información y según las responsabilidades del trabajo.

Con "necesidad de conocimiento de la información" hacemos referencia a cuando se conceden derechos de acceso solo al menor número posible de datos y privilegios necesarios para realizar un trabajo.

## <a name="pci-dss-requirement-71"></a>Requisito 7.1 de PCI DSS

**7.1** Limite el acceso a componentes del sistema y datos de titulares de tarjetas solo a aquellas personas cuyo trabajo exige ese acceso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Azure exige las directivas de ISMS existentes relacionadas con el acceso de usuarios de Azure a los componentes del sistema de Azure, la verificación de la efectividad del control de acceso, la proporción de acceso administrativo Just-In-Time, la revocación de acceso cuando ya no es necesario y la seguridad de que el personal que accede al entorno de la plataforma de Azure lo hace porque es necesario. El acceso de Azure a entornos de clientes está restringido y solo se permite bajo aprobación del cliente.<br /><br />Se han establecido procedimientos para restringir el acceso físico al centro de datos a empleados, proveedores, autónomos y visitantes autorizados. La verificación de seguridad y de entrada son pasos necesarios para el personal que solicita acceso temporal al interior de las instalaciones del centro de datos. Los equipos de Azure revisan los registros de acceso físico cada trimestre. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de la limitación de acceso a los componentes del sistema y datos de titulares de tarjetas solo a aquellas personas cuyo trabajo exige ese acceso. Esto incluye la limitación y la restricción de acceso al Portal de administración de Azure, así como la especificación de cuentas o roles con permisos para crear, modificar o eliminar servicios PaaS.|



### <a name="pci-dss-requirement-711"></a>Requisito 7.1.1 de PCI DSS

**7.1.1** Defina las necesidades de acceso para todos los roles, entre las que se incluyen:
- Componentes del sistema y recursos de datos a los que necesita acceder cada rol para realizar su trabajo
- Nivel de privilegio necesario (por ejemplo, usuario, administrador, etc.) para acceder a los recursos

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de la definición y documentación del proceso de aprobación del id. de usuario, de la definición de privilegios mínimos, de la restricción de acceso a datos de titulares de tarjetas, del uso de id. únicos, de la proporción de división de tareas y de la revocación de acceso de usuario cuando ya no es necesario.|



### <a name="pci-dss-requirement-712"></a>Requisito 7.1.2 de PCI DSS

**7.1.2** Restrinja el acceso a los id. de usuario con privilegios a los privilegios mínimos para realizar su trabajo.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha adoptado directivas aplicables de seguridad corporativa y organizativa, incluida una directiva de seguridad de la información. Las directivas han sido aprobadas, publicadas y comunicadas a Microsoft Azure. La directiva de seguridad de la información de Microsoft Azure exige que el acceso a los recursos de Microsoft Azure se conceda basándose en una justificación empresarial, que tenga la autorización del propietario del recurso y que se vea limitado en función de los principios de "necesidad de conocimiento de la información" y "privilegios mínimos". La directiva también aborda los requisitos de ciclo de vida de administración de acceso, incluido el aprovisionamiento de acceso, la autorización de acceso, la autenticación, la eliminación de derechos de acceso y las revisiones de acceso periódicas. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso crea tres cuentas durante la implementación: admin, sqladmin y edna (el usuario predefinido con la sesión iniciada en la aplicación web durante la ejecución de la demostración). Los roles de usuario están limitados a tareas según el escenario documentado en la demostración.|



### <a name="pci-dss-requirement-713"></a>Requisito 7.1.3 de PCI DSS

**7.1.3** Asigne accesos según la clasificación de trabajo y las funciones de cada persona.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso crea tres cuentas durante la implementación: admin, sqladmin y edna (el usuario predefinido con la sesión iniciada en la aplicación web durante la ejecución de la demostración). Los roles de usuario están limitados a tareas según el escenario documentado en la demostración.|



### <a name="pci-dss-requirement-714"></a>Requisito 7.1.4 de PCI DSS

**7.1.4** Requiera la aprobación de documentos por parte de personal autorizado que especifique los privilegios necesarios.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de la limitación de acceso a los componentes del sistema y datos de titulares de tarjetas solo a aquellas personas cuyo trabajo exige ese acceso. Esto incluye la limitación y la restricción de acceso al Portal de administración de Azure, así como la especificación de cuentas o roles con permisos para crear, modificar o eliminar servicios PaaS.|



## <a name="pci-dss-requirement-72"></a>Requisito 7.2 de PCI DSS

**7.2** Establezca un sistema de control de acceso para los componentes del sistema que restrinja el acceso según la necesidad de conocimiento de la información de un usuario y que esté configurado en "denegar todo", a menos que se permita de manera específica.
Este sistema de control de acceso debe incluir lo siguiente:
- 7.2.1 Cobertura de todos los componentes del sistema
- 7.2.2 Asignación de privilegios a usuarios según su función y clasificación del trabajo
- 7.2.3 Configuración "denegar todo" predeterminada

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la tienda web de Contoso se usa Azure Active Directory para restringir el acceso solo a los usuarios designados. Para obtener más información, consulte [Guía de PCI: Administración de identidades](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>Requisito 7.3 de PCI DSS

**7.3** Asegúrese de que las directivas de seguridad y los procedimientos operativos para restringir el acceso físico a los datos de titulares de tarjetas están documentados, en uso y que los conocen todas las partes afectadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la documentación de la tienda web de Contoso se proporciona un caso de uso y una descripción relacionadas con quien usa la CHD y cómo se usa.|




