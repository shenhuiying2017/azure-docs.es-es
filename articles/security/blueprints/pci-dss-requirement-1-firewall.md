---
title: "Plano técnico de procesamiento de pagos de Azure: requisitos de Firewall"
description: Requisito 1 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Requisitos de firewall para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-1"></a>Requisito 1 de PCI DSS

**Instalación y mantenimiento de una configuración de firewall para proteger los datos de los titulares de tarjetas**

> [!NOTE]
> El [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) (Consejo de estándares de seguridad para el sector de tarjetas de pago) define estos requisitos como parte del [Estándar de Seguridad de Datos (DDS) para la PCI, versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de comprobación e instrucciones para cada requisito.

Los firewalls son dispositivos que controlan el tráfico permitido entre las redes de una entidad (internas) y las que no son de confianza (externas), así como el tráfico entrante y saliente de zonas de mayor confidencialidad de las redes de confianza internas de una entidad. El entorno de datos de los titulares de tarjetas es un ejemplo de un área de mayor confidencial dentro de la red de confianza de una entidad.
Un firewall examina todo el tráfico de red y bloquea las transmisiones que no cumplan los criterios de seguridad especificados.
Todos los sistemas deben protegerse contra el acceso no autorizado desde redes que no sean de confianza, con independencia de que se tenga acceso al sistema a través de Internet como comercio electrónico, acceso a Internet de los empleados a través de exploradores de escritorio, acceso al correo electrónico del empleado, conexiones dedicadas como conexiones de negocio a negocio, a través de redes inalámbricas o de otros orígenes. A menudo, las rutas de acceso entrantes y salientes aparentemente insignificantes de redes que no son de confianza pueden proporcionar rutas no protegidas a sistemas clave. Los firewalls son un mecanismo de protección clave para cualquier red de equipos.
Otros componentes del sistema pueden proporcionar funcionalidad de firewall, siempre y cuando cumplan los requisitos mínimos para los firewalls que se definen en el Requisito 1. Con independencia de que se usen otros componentes del sistema dentro del entorno de datos de los titulares de tarjetas para proporcionar funcionalidad de firewall, estos dispositivos deben estar incluidos dentro del ámbito y la evaluación del Requisito 1.

## <a name="pci-dss-requirement-11"></a>Requisito 1.1 de PCI DSS

**1.1** Establezca e implemente estándares de configuración de firewall y enrutador que incluyan lo siguiente (vea 1.1.1 a 1.1.7).


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso proporciona firewalls para el CDE mediante aislamiento de PaaS, y una implementación de App Service Environment garantiza la protección de la entrada y salida de datos del CDE.<br /><br />[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) es un plan de servicio Premium que se usa por motivos normativos. Para más información sobre los controles y configuración de ASE, vea [Guía de PCI: App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>Requisito 1.1.1 de PCI DSS

**1.1.1** Establezca un proceso formal para aprobar y probar todas las conexiones de red y los cambios en las configuraciones de firewall y enrutador.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Una instancia de la tienda web de Contoso establece un modelo DevOps de CI/CD para garantizar que todos los cambios se administren correctamente. [Operations Management Suite (OMS)](/azure/operations-management-suite/) proporciona un registro completo de los cambios. Se puede revisar y comprobar la exactitud de los cambios. Para obtener instrucciones más específicas, vea [Guía de PCI: Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />[Azure Security Center](https://azure.microsoft.com/services/security-center/) proporciona una visión centralizada del estado de la seguridad de todos los recursos de Azure. De un vistazo, puede comprobar que los controles de seguridad adecuados se hayan implementado y configurado correctamente, así como identificar rápidamente los recursos que requieran atención.|



### <a name="pci-dss-requirement-112"></a>Requisito 1.1.2 de PCI DSS

**1.1.2** Cree un diagrama de red actual que identifique todas las conexiones entre el entorno de datos de los titulares de tarjetas y otras redes, incluidas las inalámbricas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Consulte la documentación de diseño y arquitectura de referencia de la tienda web de Contoso que se proporciona como parte del patrón de instalación de la solución.|



### <a name="pci-dss-requirement-113"></a>Requisito 1.1.3 de PCI DSS

**1.1.3** Cree un diagrama actual que muestre todos los flujos de datos de los titulares de tarjetas entre sistemas y redes.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Consulte el DFD de la tienda web de Contoso y el Modelo de riesgos.|



### <a name="pci-dss-requirement-114"></a>Requisito 1.1.4 de PCI DSS

**1.1.4** Establezca requisitos de firewall en cada conexión a Internet y entre cualquier red perimetral (DMZ) y la zona de red interna.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure emplea dispositivos de protección de límites, como puertas de enlace, listas de control de acceso de red y firewalls de aplicación para controlar las comunicaciones en los límites externos e internos en el nivel de plataforma. Después, el cliente los configura de acuerdo a sus requisitos y especificaciones. Microsoft Azure filtra la comunicación cuando llega a la plataforma. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso proporciona una red perimetral mediante aislamiento de PaaS, y una implementación de App Service Environment garantiza la protección de la entrada y salida de datos de CDE.<br /><br />[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) es un plan de servicio Premium que se usa por motivos normativos. Para más información sobre los controles y configuración de ASE, vea [Guía de PCI: App Service Environment](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>Requisito 1.1.5 de PCI DSS

**1.1.5** Establezca una descripción de grupos, roles y responsabilidades para la administración de componentes de red.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso usa [Control de acceso basado en roles de Azure (RBAC)](/azure/active-directory/role-based-access-control-configure) para aislar los roles de usuario. RBAC permite la administración de acceso con precisión para Azure. Existen configuraciones específicas para el acceso a la suscripción y a Azure Key Vault.|



### <a name="pci-dss-requirement-116"></a>Requisito 1.1.6 de PCI DSS

**1.1.6** Cree un documentación de justificación y aprobación comercial del uso de todos los servicios, protocolos y puertos permitidos, también sobre las características de seguridad implementadas para los protocolos que se consideren no seguros.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la tienda web de Contoso solo se abren los puertos y protocolos requeridos en el diseño de RA. Los detalles sobre el flujo de datos se pueden ver en el DFD y el modelo de riesgos.|



### <a name="pci-dss-requirement-117"></a>Requisito 1.1.7 de PCI DSS

**1.1.7** Establezca un requisito para revisar los conjuntos de reglas de firewall y enrutador al menos cada seis meses.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la tienda web de Contoso, los conjuntos de reglas de firewall se revisan para garantizar que no se incluya ninguna regla innecesaria o que no se vaya a usar. De forma predeterminada, la demostración se implementa con una huella de privilegios mínimos y ruta de acceso de menor tamaño.|



## <a name="pci-dss-requirement-12"></a>Requisito 1.2 de PCI DSS

**1.2** Cree configuraciones de firewall y enrutador que restrinjan las conexiones entre las redes que no sean de confianza y cualquier componente del sistema en el entorno de datos de los titulares de tarjetas. 

> [!NOTE]
> Una "red que no es de confianza" es cualquier red externa a las redes que pertenecen a la entidad en proceso de revisión o que está fuera de la capacidad de control o administración de la entidad.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | El CDE de la tienda web de Contoso se define en la documentación de implementación y RA. Las redes que no son de confianza se deniegan de forma predeterminada.|



### <a name="pci-dss-requirement-121"></a>Requisito 1.2.1 de PCI DSS

**1.2.1** Restrinja el tráfico de entrada y de salida como corresponda para el entorno de datos de los titulares de tarjetas y deniegue todo el tráfico restante de forma específica.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | El CDE de la tienda web de Contoso se define en la documentación de implementación y RA. Las redes que no son de confianza se deniegan de forma predeterminada. En la demostración de la tienda web de Contoso el firewall de aplicaciones de Microsoft Azure se configura para permitir que solo los intervalos IP especificados tengan acceso a los servicios de Microsoft Azure. La tienda web de Contoso proporciona un firewall de denegación en todos los límites del CDE. Todas las configuraciones se realizan durante la instalación inicial de la implementación.

> [!NOTE]
> En esta solución se usa App Service Environment (ASE) para aislar el CDE, pero es fundamental que el Asesor de seguridad certificado (QSA) evalúe esta solución, dado que ASE implementa un aislamiento de red perimetral que le permite establecer conexiones de salida. PCI DSS requiere que se bloqueen todas las conexiones de entrada y de salida que no sean necesarias. Para que ASE funcione correctamente, ASE establecerá las conexiones de salida necesarias según se define en [Consideraciones de red para una instancia de App Service Environment](/azure/app-service/app-service-environment/network-info). Los clientes deben evaluar las conexiones de salida con su QSA antes de implementar la solución en un entorno de producción para asegurarse de que cumplirá los requisitos. |



### <a name="pci-dss-requirement-122"></a>Requisito 1.2.2 de PCI DSS

**1.2.2** Proteja y sincronice los archivos de configuración del enrutador.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso proporciona configuraciones sincronizadas para controles de red nativos de Microsoft Azure.|



### <a name="pci-dss-requirement-123"></a>Requisito 1.2.3 de PCI DSS

**1.2.3** Instale firewalls perimetrales entre todas las redes inalámbricas y el entorno de datos de los titulares de tarjetas, y configure estos firewalls para denegar o, si el tráfico es necesario con fines comerciales, permitir solo el tráfico autorizado entre el entorno inalámbrico y el de los datos de los titulares de tarjetas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso no tiene ninguna funcionalidad ni solución inalámbrica habilitada.|



## <a name="pci-dss-requirement-13"></a>Requisito 1.3 de PCI DSS

**1.3** Prohíba el acceso público directo entre Internet y cualquier componente del sistema en el entorno de datos de los titulares de tarjetas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure emplea dispositivos de protección de límite basados en red y en host, como firewalls, equilibradores de carga y listas de control de acceso. Estos dispositivos usan mecanismos como el aislamiento de VLAN, NAT y filtrado de paquetes para separar el tráfico de cliente del tráfico de Internet y administración. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En el momento de la implementación, la tienda web de Contoso proporciona las configuraciones del firewall de aplicaciones de Azure para permitir que solo los intervalos IP especificados tengan acceso al sitio, incluidas las máquinas virtuales de Azure del bastión de su CDE.|



### <a name="pci-dss-requirement-131"></a>Requisito 1.3.1 de PCI DSS

**1.3.1** Implemente una red perimetral para limitar el tráfico de entrada solo a los componentes del sistema que proporcionen servicios protocolos y puertos de acceso público autorizados.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La implementación de la tienda web de Contoso de su red perimetral garantiza que solo los servicios autorizados se puedan conectar con el CDE.|



### <a name="pci-dss-requirement-132"></a>Requisito 1.3.2 de PCI DSS

**1.3.2** Limite el tráfico de entrada de Internet a las direcciones IP dentro de la red perimetral.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La implementación de la tienda web de Contoso de su red perimetral garantiza que solo los servicios autorizados se puedan conectar con el CDE.|



### <a name="pci-dss-requirement-133"></a>Requisito 1.3.3 de PCI DSS

**1.3.3** Implemente medidas contra la suplantación de identidad para detectar direcciones IP de origen falsificadas y bloquear su entrada a la red. Por ejemplo, puede bloquear el tráfico procedente de Internet con una dirección de origen interna.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementa filtrado de red para impedir el tráfico falsificado y restringir el tráfico de entrada y salida a los componentes de plataformas seguras. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-134"></a>Requisito 1.3.4 de PCI DSS

**1.3.4** No permita el tráfico de salida no autorizado desde el entorno de datos de los titulares de tarjetas a Internet.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La arquitectura de la tienda web de Contoso evita el tráfico de salida no autorizado desde el entorno dentro del ámbito a Internet. Esto se consigue mediante la configuración de listas de control de acceso de tráfico de salida para los protocolos y puertos aprobados en Microsoft Azure. Estos controles incluyen el acceso al CDE en la base de datos de SQL Server. <br /><br />Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>Requisito 1.3.5 de PCI DSS

**1.3.5** Permita solo conexiones a la red "establecidas".


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementa filtrado de red para impedir el tráfico falsificado y restringir el tráfico de entrada y salida a los componentes de plataformas seguras. La red de Microsoft Azure está segregada para separar el tráfico de clientes del tráfico de administración. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-136"></a>Requisito 1.3.6 de PCI DSS

**1.3.6** Coloque los componentes del sistema en los que se almacenan datos de los titulares de tarjetas (por ejemplo, una base de datos) en una zona de la red interna, segregada de la red perimetral y otras redes que no sean de confianza.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa la segregación de redes y NAT para separar el tráfico de clientes del tráfico de administración. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La arquitectura de la tienda web de Contoso evita el tráfico de salida no autorizado desde el entorno dentro del ámbito a Internet. Esto se consigue mediante la configuración de listas de control de acceso de tráfico de salida para los protocolos y puertos aprobados en Microsoft Azure. Estos controles incluyen el acceso al CDE en la base de datos de SQL Server. <br /><br />Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>Requisito 1.3.7 de PCI DSS

**1.3.7** No divulgue direcciones IP privadas ni la información de enrutamiento a partes no autorizadas. 

> [!NOTE]
> Los métodos para ocultar las direcciones IP pueden incluir lo siguiente, aunque no se trata de una lista exhaustiva:
> - Traducción de direcciones de red (NAT).
> - Colocación de servidores que contienen datos de los titulares de tarjetas detrás de servidores proxy o firewalls.
> - Eliminación o filtrado de anuncios de rutas para las redes privadas que emplean direccionamiento registrado.
> - Uso interno del espacio de direcciones de RFC1918 en lugar de direcciones registradas.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa Traducción de direcciones de red (NAT) y segregación de redes para separar el tráfico de clientes del tráfico de administración. Los dispositivos de Azure se identifican de manera única mediante sus UUID y se autentican mediante Kerberos. Los dispositivos de red administrados de Azure se identifican por la dirección IP 1918 de RFC. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso coloca todos los datos de los titulares de tarjetas detrás de firewalls o servidores proxy, y usa el espacio de direcciones de RFC1918 de forma interna.|



## <a name="pci-dss-requirement-14"></a>Requisito 1.4 de PCI DSS

**1.4** Instale software de firewall o una funcionalidad equivalente en todos los dispositivos informáticos portátiles (incluidos los que pertenecen a la organización o los empleados) que se conectan a Internet cuando están fuera de la red (por ejemplo, los equipos portátiles que usan los empleados), y que también se usan para tener acceso al CDE. Las configuraciones de firewall (o equivalentes) incluyen la definición de opciones de configuración específicas.
- Se ejecuta un firewall personal (o una funcionalidad equivalente) de forma activa.
- Los usuarios de los dispositivos informáticos portátiles no pueden modificar el firewall personal (o una funcionalidad equivalente).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso no proporciona la protección de los dispositivos de usuario final. Se puede usar [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) para administrar los dispositivos móviles que los trabajadores usan para tener acceso a los datos de la empresa.|



## <a name="pci-dss-requirement-15"></a>Requisito 1.5 de PCI DSS

**1.5** Asegúrese de que las directivas de seguridad y los procedimientos operativos para administrar los servidores de seguridad estén documentados, en uso y sean conocidos por todas las partes afectadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En el momento de la implementación, la tienda web de Contoso proporciona las configuraciones del firewall de aplicaciones de Azure para permitir que solo los intervalos IP especificados tengan acceso al sitio, incluidas las máquinas virtuales de Azure del bastión de su CDE.|




