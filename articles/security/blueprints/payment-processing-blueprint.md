---
title: Proyecto de procesamiento de pagos para entornos compatibles con PCI DSS
description: Requisito de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 7f85c8b0377e57f08044bac41dbddbbedb7a4f55
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="azure-blueprint-automation-payment-processing-for-pci-dss-compliant-environments"></a>Azure Blueprint Automation: procesamiento de pagos para entornos compatibles con PCI DSS

## <a name="overview"></a>Información general

El procesamiento de pagos para los entornos compatibles con PCI DSS proporciona una guía para implementar un entorno de plataforma como servicio (PaaS) compatible con PCI DSS que sea adecuado para controlar los datos confidenciales de tarjetas de pago. Muestra una arquitectura de referencia común y está diseñado para simplificar la adopción de Microsoft Azure. Este plano técnico muestra una solución completa para satisfacer las necesidades de organizaciones que buscan un enfoque basado en la nube para reducir la carga y los costos de implementación.

Este plano técnico está diseñado para cumplir con los requisitos de las estrictas Normas de seguridad de datos del sector de las tarjetas de pago (PCI DSS 3.2) para recopilar, almacenar y recuperar los datos de las tarjetas de pago. Muestra cómo tratar adecuadamente los datos de una tarjeta de crédito (incluyendo el número de tarjeta, la fecha de caducidad y los datos de comprobación) en un entorno seguro de múltiples niveles implementado como una solución PaaS de un extremo a otro basada en Azure. Para obtener más información sobre los requisitos de PCI DSS 3.2 y esta solución, consulte [PCI DSS Requirements - High-Level Overview](pci-dss-requirements-overview.md) (Requisitos de PCI DSS: información general de alto nivel).

Este plano técnico está diseñado para servir como base para que los clientes puedan conocer mejor sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Implementar una aplicación sin modificar en este entorno no es suficiente para satisfacer por completo los requisitos de una solución compatible con PCI DSS para una solución personalizada. Tenga en cuenta lo siguiente:
- Este plano técnico proporciona una línea de base para que los clientes puedan usar Microsoft Azure con PCI DSS.
- Para cumplir con los requisitos de PCI DSS, debe conseguir que un asesor de seguridad cualificado (QSA) y acreditado certifique una solución de cliente de producción.
- Los clientes tienen la responsabilidad de realizar las revisiones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura fundamental, ya que los requisitos pueden variar en función de las características de implementación y la ubicación geográfica de cada cliente.  

Para obtener información general de cómo funciona esta solución, vea este [vídeo](https://aka.ms/pciblueprintvideo) en el que se explica y demuestra cómo realizar la implementación.

## <a name="solution-components"></a>Componentes de soluciones

La arquitectura fundamental consta de los siguientes componentes:

- **Diagrama de arquitectura**. El diagrama muestra la arquitectura de referencia que se usa en la solución de Contoso Webstore.
- **Plantillas de implementación**. En esta implementación, las [plantillas de Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) se usan para implementar automáticamente los componentes de la arquitectura en Microsoft Azure; para ello, se especifican los parámetros de configuración durante la instalación.
- **Scripts de implementación automatizados**. Estos scripts le permiten implementar la solución de extremo a extremo. Asimismo, estos scripts constan de los siguientes elementos:
    - Una instalación de módulo y un script de configuración del [administrador global](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) se usan para instalar y comprobar que los roles de administrador global y los módulos de PowerShell necesarios están configurados correctamente.
    - Un script de instalación de PowerShell se usa para implementar la solución de un extremo a otro, que se proporciona a través de un archivo .zip y un archivo .bacpac que contiene una aplicación web de demostración creada previamente con contenido de [ejemplo de la base de datos SQL](https://github.com/Microsoft/azure-sql-security-sample) . El código fuente de esta solución está disponible para su revisión en el [repositorio de código del plano técnico de procesamiento de pagos][code-repo]. 

## <a name="architectural-diagram"></a>Diagrama de arquitectura

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Escenario de usuario

En el plano técnico se aborda el caso de uso siguiente.

> En este escenario se muestra cómo una tienda electrónica ficticia lleva la función de procesamiento de tarjetas de pago a una solución PaaS basada en Azure. La solución controla la recopilación de información básica del usuario junto con los datos de pago. Tenga en cuenta que la solución no procesa los pagos con los datos del titular de la tarjeta; una vez se recopilan los datos, los clientes son los responsables de iniciar y completar las transacciones mediante un procesador de pago. Para más información, vea la [revisión y orientación sobre la implementación](https://aka.ms/pciblueprintprocessingoverview).

### <a name="use-case"></a>Caso de uso
Una pequeña tienda electrónica llamada *Contoso Webstore* está lista para llevar su sistema de pago a la nube. Ha seleccionado Microsoft Azure para hospedar el proceso de compra y para permitir que un empleado recopile los pagos de las tarjetas de crédito de sus clientes.

El administrador está buscando una solución que se pueda implementar rápidamente y así poder lograr sus objetivos en una solución diseñada en la nube. Para ello, usará esta prueba de concepto (POC) para hablar con las partes interesadas acerca de cómo se puede usar Azure para recopilar, almacenar y recuperar los datos de las tarjetas de pago a la vez que se cumple con los estrictos requisitos de las Normas de seguridad de datos del sector de las tarjetas de pago (PCI DSS).

> Tendrá la responsabilidad de realizar las revisiones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante la arquitectura que usó esta POC, ya que los requisitos pueden variar en función de las características de implementación y la ubicación geográfica. Según la normativa PCI DSS, debe trabajar directamente con un asesor de seguridad cualificado (QSA) y acreditado para certificar la solución preparada para la producción.

### <a name="elements-of-the-foundational-architecture"></a>Elementos de la arquitectura fundamental

La arquitectura fundamental está diseñada con los siguientes elementos ficticios:

Sitio de dominio: `contosowebstore.com`

Los roles de usuario se emplean para ilustrar el caso de uso y proporcionan una visión general de la interfaz de usuario.

#### <a name="role-site-and-subscription-admin"></a>Rol: Administrador del sitio y la suscripción

|Elemento      |Ejemplo|
|----------|------|
|Nombre de usuario: |`adminXX@contosowebstore.com`|
| Nombre: |`Global Admin Azure PCI Samples`|
|Tipo de usuario:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- La cuenta de administrador no puede leer la información de la tarjeta de crédito sin enmascarar. Todas las acciones se registran.
- La cuenta de administrador no puede administrar ni iniciar sesión en SQL Database.
- La cuenta de administrador puede administrar Active Directory y la suscripción.

#### <a name="role-sql-administrator"></a>Rol: Administrador de SQL

|Elemento      |Ejemplo|
|----------|------|
|Nombre de usuario: |`sqlAdmin@contosowebstore.com`|
| Nombre: |`SQLADAdministrator PCI Samples`|
| Nombre: |`SQL AD Administrator`|
|Apellido: |`PCI Samples`|
|Tipo de usuario:| `Administrator`|

- La cuenta sqladmin no podrá ver la información de la tarjeta de crédito sin filtrar. Todas las acciones se registran.
- La cuenta sqladmin puede administrar SQL Database.

#### <a name="role-clerk"></a>Rol: distribuidor

|Elemento      |Ejemplo|
|----------|------|
|Nombre de usuario:| `receptionist_EdnaB@contosowebstore.com`|
| Nombre: |`Edna Benson`|
| Nombre:| `Edna`|
|Apellido:| `Benson`|
| Tipo de usuario: |`Member`|

Edna Benson es la directora del departamento de recepción y negocios. Es la responsable de garantizar que la información del cliente sea precisa y que la facturación se complete como es debido. Asimismo, ella es quien inició sesión para poder llevar a cabo todas las interacciones en el sitio web de demostración Contoso Webstore. Edna tiene los siguientes derechos: 

- Puede crear y leer la información de cliente.
- Puede modificar la información del cliente.
- Puede sobrescribir o reemplazar el número de tarjeta de crédito, la fecha de expiración y el número CVV.

> El usuario de Contoso Webstore se muestra automáticamente como **Edna**, para así poder probar las funcionalidades del entorno implementado.

### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore: precios estimados

Esta arquitectura fundamental y la aplicación web de ejemplo tienen una estructura de tarifas mensual y un costo de uso por hora que se debe tener en cuenta al asignar el tamaño de la solución. Estos costos pueden evaluarse con la [Calculadora de costos de Azure](https://azure.microsoft.com/pricing/calculator/). A partir de septiembre de 2017, el costo mensual estimado para esta solución es de 2500 USD, que incluye un cargo por uso de 1000 USD al mes para ASE v2. Estos costos varían según cuánto se use la solución y están sujetos a cambios. Le corresponde al cliente calcular los costos mensuales estimados en el momento de la implementación, para poder obtener una estimación más precisa. 

Esta solución utiliza los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

>- Application Gateway
>- Azure Active Directory
>- App Service Environment v2
>- Log Analytics de OMS
>- Azure Key Vault
>- Grupos de seguridad de red
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure Security Center
>- Aplicación web de Azure
>- Azure Automation
>- Runbooks de Azure Automation
>- DNS de Azure
>- Azure Virtual Network
>- Máquina virtual de Azure
>- Grupo de recursos y directivas de Azure
>- Azure Blob Storage
>- Control de acceso basado en roles de Azure Active Directory (RBAC)

## <a name="deployment-architecture"></a>Arquitectura de implementación

En la siguiente sección se detallan los elementos de desarrollo e implementación.

### <a name="network-segmentation-and-security"></a>Segmentación de red y seguridad

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

La arquitectura fundamental reduce el riesgo de sufrir vulnerabilidades de seguridad, ya que usa Application Gateway con el firewall de aplicaciones web (WAF) y tiene el conjunto de reglas OWASP habilitado. Entre estas funcionalidades, cabe destacar:

- [SSL de un extremo a otro](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Opción para habilitar la [descarga SSL](/azure/application-gateway/application-gateway-ssl-portal)
- Opción para deshabilitar [TLS v1.0 y v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicaciones web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (modo WAF)
- [Modo de prevención](/azure/application-gateway/application-gateway-web-application-firewall-portal) con el conjunto de reglas OWASP 3.0
- Opción para habilitar el [registro de diagnósticos](/azure/application-gateway/application-gateway-diagnostics)
- [Sondeos de estado personalizados](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) y [Azure Advisor](/azure/advisor/advisor-security-recommendations) que proporcionan protección adicional y notificaciones. Asimismo, Azure Security Center también proporciona un sistema de reputación.

#### <a name="virtual-network"></a>Red virtual

La arquitectura fundamental define una red virtual privada con un espacio de direcciones de 10.0.0.0/16.

#### <a name="network-security-groups"></a>Grupos de seguridad de red

Cada uno de los niveles de red tiene un grupo de seguridad de red dedicado (NSG):
- Un grupo de seguridad de red DMZ para el firewall y el modo WAFS de Application Gateway
- Un NSG para un jumpbox de administración (host de tipo bastión)
- Un NSG para el entorno del servicio de la aplicación

Cada uno de los NSG tiene determinados puertos y protocolos abiertos para así permitir que la solución funcione con seguridad y como es debido. Para obtener más información, consulte [Guía de PCI: grupos de seguridad de red](#network-security-groups).

Cada uno de los NSG tiene determinados puertos y protocolos abiertos para así permitir que la solución funcione con seguridad y como es debido. Además, las siguientes opciones de configuración están habilitadas para cada NSG:
- Los [eventos y registros de diagnóstico](/azure/virtual-network/virtual-network-nsg-manage-log) habilitados se almacenan en la cuenta de almacenamiento. 
- Log Analytics de OMS conectado a los [diagnósticos del NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

#### <a name="subnets"></a>Subredes
 Asegúrese de que cada subred esté asociada a su NSG correspondiente.

#### <a name="custom-domain-ssl-certificates"></a>Certificados SSL de dominio personalizado
 El tráfico HTTPS se habilita mediante un certificado SSL de dominio personalizado.

### <a name="data-at-rest"></a>Datos en reposo

La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

#### <a name="azure-storage"></a>Azure Storage

Para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa el [cifrado del servicio Storage](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Azure SQL Database

La instancia de Azure SQL Database usa las siguientes medidas de seguridad de base de datos:

- [Autenticación y autorización de AD](/azure/sql-database/sql-database-aad-authentication).
- [Auditoría de SQL Database](/azure/sql-database/sql-database-auditing-get-started).
- [Cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Reglas de firewall](/azure/sql-database/sql-database-firewall-configure), lo que permite crear grupos de trabajo ASE y administrar la IP de cliente.
- [Detección de amenazas de SQL](/azure/sql-database/sql-database-threat-detection-get-started).
- [Columnas de Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Enmascaramiento de datos dinámicos de SQL Database](/azure/sql-database/sql-database-dynamic-data-masking-get-started) mediante el script de PowerShell posterior a la implementación.

### <a name="logging-and-auditing"></a>Registro y auditoría

[Operations Management Suite (OMS)](/azure/operations-management-suite/) puede proporcionar a Contoso Webstore un registro completo de toda la actividad del usuario y del sistema, además de un registro de datos de los titulares de las tarjetas. Además puede revisar y comprobar la exactitud de los cambios. 

- **Registros de actividad:** [los registros de actividad](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones que se realizaron en los recursos de la suscripción.
- **Registros de diagnóstico:** [los registros de diagnóstico](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) son todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, Azure Blob Storage, tablas y registros de cola.
- **Registros de firewall:** Application Gateway proporciona registros de diagnóstico y acceso completos. Los registros de firewall están disponibles para los recursos de Application Gateway que tienen WAF habilitado.
- **Archivado de registros:** todos los registros de diagnóstico se configuran para escribir en una cuenta de almacenamiento de Azure centralizada y cifrada y que así queden archivados durante un período de retención definido (2 días). A continuación, esos registros se conectan a Azure Log Analytics para procesarlos, almacenarlos y agregarlos a los paneles. [Log Analytics](https://azure.microsoft.com/services/log-analytics) es un servicio de OMS que le ayudará a recopilar y analizar los datos que generaron los recursos en entornos locales o en la nube.

### <a name="encryption-and-secrets-management"></a>Administración de cifrado y secretos

Contoso Webstore cifra todos los datos de las tarjetas de crédito y usa Azure Key Vault para administrar las claves, evitando así la recuperación de CHD.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) le permite proteger las claves criptográficas y los secretos que usan los servicios y aplicaciones en la nube. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) se usa para cifrar todos los datos de los clientes titulares de tarjetas, la fecha de expiración y el CVV.
- Los datos se almacenan en el disco mediante [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) y BitLocker.

### <a name="identity-management"></a>Administración de identidades

Las siguientes tecnologías proporcionan funcionalidades de administración de identidad en el entorno de Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) es el directorio de varios inquilinos basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios de la solución se crearon en Azure Active Directory, incluidos los usuarios que acceden a SQL Database.
- La autenticación para acceder a la aplicación se realiza con Azure AD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos también usa Azure AD para autenticar la aplicación Azure SQL Database. Para obtener más información, consulte [Always Encrypted: protección de datos confidenciales en SQL Database](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) detecta posibles vulnerabilidades que afectan a las identidades de la organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.
- El [control de acceso basado en roles de Azure (RBAC)](/azure/active-directory/role-based-access-control-configure) permite administrar al detalle el control de acceso de Azure. El acceso a la suscripción está limitado al administrador de dicha suscripción y el acceso a Azure Key Vault está restringido para todos los usuarios.

Para obtener más información sobre el uso de las características de seguridad de Azure SQL Database, consulte el ejemplo de la [aplicación de demostración de Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).
   
### <a name="web-and-compute-resources"></a>Recursos web y de procesos

#### <a name="app-service-environment"></a>Entorno de App Service

[Azure App Service](/azure/app-service/) es un servicio administrado para la implementación de aplicaciones web. La aplicación Contoso Webstore se implementa como una [aplicación web del servicio de aplicaciones](/azure/app-service-web/app-service-web-overview).

[Azure App Service Environment (ASE v2)](/azure/app-service/app-service-environment/intro) es una característica de App Service que proporciona un entorno completamente aislado y dedicado para ejecutar de forma segura las aplicaciones de App Service a gran escala. es un plan de servicio Premium que usa esta arquitectura fundamental para habilitar el cumplimiento de PCI DSS.

Las instancias de ASE están aisladas para ejecutar únicamente las aplicaciones de un solo cliente y siempre se implementan en una red virtual. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de las aplicaciones, y las aplicaciones pueden establecer conexiones seguras de alta velocidad a los recursos corporativos locales a través de redes virtuales.

Puede usar las instancias de ASE de esta arquitectura en los siguientes controles o configuraciones:

- En un host que esté en Virtual Network y en las reglas de seguridad de red.
- En una instancia de ASE configurada con certificados ILB autofirmados para la comunicación HTTPS.
- En un [modo de equilibrio de carga interno](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3).
- Al deshabilitar [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings), un protocolo TLS que está en desuso desde la perspectiva de PCI DSS.
- Al cambiar el [cifrado TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Al controlar [puertos N/W de tráfico entrante](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic). 
- En [WAF: al restringir los datos](/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Al permitir el [tráfico de SQL Database](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).


#### <a name="jumpbox-bastion-host"></a>En un jumpbox (host de tipo bastión).

Como el entorno de App Service está protegido y bloqueado, debe haber un mecanismo para permitir cualquier versión o cambio de DevOps que sea necesario como, por ejemplo, la capacidad de supervisar la aplicación web mediante Kudu. La máquina virtual está protegida en un equilibrador de carga de NAT que le permite conectar VM en un puerto distinto a TCP 3389. 

Se ha creado una máquina virtual como un jumpbox (host de tipo bastión) con las siguientes configuraciones:

-   [Extensión antimalware](/azure/security/azure-security-antimalware)
-   [Extensión de OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensión de Diagnósticos de Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) mediante Azure Key Vault (cumple con Azure Government, PCI DSS, HIPAA y otros requisitos).
-   Una [directiva de apagado automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reducir el consumo de recursos de la máquina virtual cuando no esté en uso.

### <a name="security-and-malware-protection"></a>Seguridad y protección contra malware

[Azure Security Center](https://azure.microsoft.com/services/security-center/) proporciona una visión centralizada del estado de seguridad de todos los recursos de Azure. Podrá comprobar de un solo vistazo si los controles de seguridad adecuados se han implementado y configurado correctamente, y podrá identificar rápidamente cualquier recurso que requiera su atención.  

[Azure Advisor](/azure/advisor/advisor-overview) es un consultor en la nube personalizado que le ayudará a seguir los procedimientos recomendados para optimizar las implementaciones de Azure. Analiza la configuración de recursos y la telemetría de uso, y recomienda soluciones que pueden ayudar a mejoran la rentabilidad, el rendimiento, la alta disponibilidad y la seguridad de los recursos de Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) para Azure Cloud Services y Virtual Machines es una funcionalidad de protección en tiempo real que le permitirá identificar y eliminar virus, spyware y otro software malintencionado mediante alertas que se pueden configurar para que le avisen si hay software malintencionado o no deseado que se intenta instalar o ejecutar en los sistemas de Azure.

### <a name="operations-management"></a>Administración de operaciones

#### <a name="application-insights"></a>Application Insights

Use [Application Insights](https://azure.microsoft.com/services/application-insights/) para obtener un conocimiento práctico y detallado mediante la administración del rendimiento de las aplicaciones y análisis instantáneos.

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) es un servicio de Operations Management Suite (OMS) que le permite recopilar y analizar los datos que generaron los recursos en entornos locales o en la nube.

#### <a name="oms-solutions"></a>Soluciones de OMS

Estas soluciones adicionales de OMS deberían considerarse y configurarse:
- [Análisis de registros de actividad](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure Networking Analytics](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Seguimiento de cambios](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Análisis de Key Vault](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Mapa de servicio](/azure/operations-management-suite/operations-management-suite-service-map)
- [Seguridad y auditoría](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Antimalware](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Administración de actualizaciones](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Integración en Security Center

La implementación predeterminada está diseñada para proporcionar una línea de base de las recomendaciones de Security Center, y así poder indicar si el estado de la configuración es correcto y seguro. Puede habilitar la recopilación de datos en Azure Security Center. Para obtener más información, consulte [Guía de inicio rápido de Azure Security Center](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Implementación de la solución

Los componentes para implementar esta solución están disponibles en el [repositorio de código del proyecto de PCI][code-repo]. Para implementar la arquitectura fundamental, debe ejecutar una serie de pasos mediante Microsoft PowerShell v5. Para conectarse al sitio web, debe proporcionar un nombre de dominio personalizado (por ejemplo, contoso.com). Esto se especifica mediante el conmutador `-customHostName` del paso 2. Para obtener más información, consulte [Comprar un nombre de dominio personalizado para Azure Web Apps](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). No es necesario tener un nombre de dominio personalizado para implementar y ejecutar la solución correctamente, pero no se podrá conectar al sitio web si quiere realizar alguna demostración.

Los scripts agregan usuarios de dominio al inquilino de Azure AD que especifique. Le recomendamos crear un nuevo inquilino de Azure AD para usarlo como prueba.

Si tiene algún problema durante la implementación, consulte [FAQ and troubleshooting](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md) (Preguntas más frecuentes y solución de problemas).

Le recomendamos que use una instalación nueva de PowerShell para implementar la solución. Como alternativa, compruebe que esté usando los módulos más recientes, ya que son necesarios para ejecutar correctamente los scripts de la instalación. En este ejemplo, iniciaremos sesión en el jumpbox (host de tipo bastión) y ejecutaremos los siguientes comandos. Tenga en cuenta que esto habilitará el comando de dominio personalizado.


1. Instale los módulos necesarios y configure correctamente los roles de administrador.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Si quiere obtener instrucciones de uso detalladas, consulte las [instrucciones de script: configuración de la cuenta administrativa y el permiso](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Instale la administración de actualizaciones de la solución. 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Si quiere obtener instrucciones de uso detalladas, consulte las [instrucciones de script: implementar y configurar recursos de Azure](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Registre y supervise OMS. Una vez que se implemente la solución, podrá abrir un área de trabajo de [Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) y podrá usar las plantillas de ejemplo incluidas en el repositorio de la solución para mostrar cómo se puede configurar un panel de supervisión. Para buscar las plantillas de ejemplo de OMS, consulte la [carpeta omsDashboards](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Tenga en cuenta que se deben recopilar datos de OMS para que las plantillas se implementen correctamente. Esta operación puede durar una hora o más en función de la actividad del sitio.
 
    Al configurar el registro de OMS, considere la posibilidad de incluir estos recursos:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Modelo de amenazas

Un diagrama de flujo de datos (DFD) y un modelo de amenazas de ejemplo para Contoso Webstore, en [modelo de amenazas en el plano técnico de procesamiento de pagos](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matriz de responsabilidades del cliente

Los clientes tienen la responsabilidad de guardar una copia de la [matriz del resumen de responsabilidades](https://aka.ms/pciblueprintcrm32), en la cual se describen los requisitos de PCI DSS que son responsabilidad de esos clientes, quienes, a su vez, son responsabilidad de Microsoft Azure.

## <a name="pci-compliance-review"></a>Revisión del cumplimiento de PCI 

Coalfire systems, Inc. se encargó de revisar la solución (asesores de seguridad cualificados de PCI-DSS). El documento de [Revisión del cumplimiento de PCI e instrucciones para la implementación](https://aka.ms/pciblueprintprocessingoverview) proporciona la opinión del auditor de la solución y sus reflexiones para transformar el proyecto en una implementación que esté lista para la producción.

## <a name="disclaimer-and-acknowledgements"></a>Declinación de responsabilidades y reconocimientos

*Septiembre de 2017*

- Este documento es meramente informativo. MICROSOFT Y AVYAN NO GARANTIZAN DE NINGUNA MANERA (YA SEA DE FORMA EXPRESA, IMPLÍCITA O ESTATUTARIA) LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.  
- Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto de Microsoft o Ayvan.  
- Los clientes pueden copiar y usar este documento con fines internos y de referencia.  

  > [!NOTE]
  > Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.  

- La solución de este documento está pensada como una arquitectura fundamental y no debe usarse tal cual para fines de producción. Para lograr el cumplimiento del estándar PCI, los clientes deben ponerse en contacto con su asesor de seguridad cualificado.  
- Todos los nombres de los clientes, los registros de transacciones y cualquier dato relacionado en esta página son ficticios, ya que se han creado específicamente para esta arquitectura fundamental y solo se proporcionan con fines ilustrativos. No se pretende crear ninguna asociación o conexión real y ninguna debe inferirse.  
- Esta solución fue desarrollada conjuntamente por Microsoft y Avyan Consulting y está disponible en la [licencia MIT](https://opensource.org/licenses/MIT).
- Coalfire, el auditor de PCI DSS de Microsoft, se encargó de revisar esta solución. La [revisión del cumplimiento de PCI](https://aka.ms/pciblueprintcrm32) proporciona una revisión independiente de terceros de la solución y los componentes que han de tenerse en cuenta. 

### <a name="document-authors"></a>Autores de los documentos

- *Frank Simorjay (Microsoft)*  
- *Gururaj Pandurangi (Avyan Consulting)*


[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repositorio de código"
