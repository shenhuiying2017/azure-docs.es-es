---
title: 'Azure Blueprint Automation: Financial Services for Regulated Workloads'
description: "Plano técnico de servicios financieros para cargas de trabajo reguladas"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 19e26c16866dada8dcff04a520ce4c208d67c365
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-blueprint-automation-financial-services-blueprint-for-regulated-workloads"></a>Azure Blueprint Automation: plano técnico de servicios financieros para cargas de trabajo reguladas

## <a name="overview"></a>Información general

Financial Services Blueprint for Regulated Workloads ayuda a implementar una aplicación web de plataforma como servicio (PaaS) segura y compatible diseñada para administrar datos confidenciales en la nube. El plano técnico consta de scripts automatizados e instrucciones que exponen una arquitectura de referencia sencilla y un diseño que ayuda a simplificar la adopción de soluciones de Microsoft Azure. En él se ilustra una solución completa que satisface las necesidades de las organizaciones que buscan reducir la carga y los costos de implementación en la nube.

Está diseñado para satisfacer los requisitos de los estándares de compatibilidad más estrictos establecidos por el Instituto Americano de Contables Públicos Certificados (American Institute of Certified Public Accountants), como SOC 1, SOC 2, el DSS 3.2 del consejo de Estándares de Seguridad de Datos para la Industria de Tarjeta de Pago (Payment Card Industry Data Security Standards) y el FFIEC para la recopilación, el almacenamiento y la recuperación de datos financieros de carácter confidencial. Demuestra el tratamiento adecuado de dichos datos mediante la implementación de una solución que administra los datos financieros en un entorno seguro, compatible y de niveles múltiples. La solución se implementa como una solución PaaS basada en Azure de extremo a extremo. 

El plano técnico pretende servir como base para que los clientes desarrollen y comprendan los requisitos de administrar datos protegidos en la nube. La solución no debe usarse en una implementación de producción tal y como está, sino únicamente para comprender, diseñar e implementar servicios de Azure; está diseñada como una base de referencia para ayudar a los clientes a usar Microsoft de manera segura y compatible.

Cualquier solución de cliente de producción basada en este plano técnico debe recibir la certificación de un auditor acreditado. Las soluciones pueden variar en función de las especificaciones de implementación y la zona geográfica de cada cliente.

Para obtener una visión general rápida de cómo funciona esta solución, vea este [vídeo](https://aka.ms/fsiblueprintvideo) donde se explica y se demuestra su implementación.

## <a name="solution-components"></a>Componentes de soluciones

La arquitectura está formada por los siguientes componentes y usa las funcionalidades de implementación de la solución de cumplimiento de PCI DSS de Azure.

- **Diagrama de arquitectura**. El diagrama muestra la arquitectura de referencia que se usa en la solución de Contoso Webstore.
- **Plantillas de implementación**. En esta implementación, las [plantillas de Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) se usan para implementar automáticamente los componentes de la arquitectura en Microsoft Azure; para ello, se especifican los parámetros de configuración durante la instalación.
- **Scripts de implementación automatizados**. Estos scripts le permiten implementar la solución de extremo a extremo. Asimismo, estos scripts constan de los siguientes elementos:
    - Una instalación de módulo y un script de configuración del [administrador global](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) se usan para instalar y comprobar que los roles de administrador global y los módulos de PowerShell necesarios están configurados correctamente. 
    - Un script de instalación de PowerShell se usa para implementar la solución de un extremo a otro, que se proporciona a través de un archivo .zip y un archivo .bacpac que contiene una aplicación web de demostración creada previamente con contenido de [ejemplo de la base de datos SQL](https://github.com/Microsoft/azure-sql-security-sample) . El código fuente de esta solución está disponible para su revisión en el [repositorio de código del plano técnico de procesamiento de pagos][code-repo]. 

## <a name="architectural-diagram"></a>Diagrama de arquitectura

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Escenario de usuario

En el plano técnico se incluye el caso de uso que se describe a continuación.

> En este escenario se ilustra cómo una tienda web ficticia mueve datos confidenciales a una solución PaaS en la nube basada en Azure. La solución de ejemplo ilustra el tratamiento y la recopilación de información básica del usuario y los datos confidenciales seleccionados. Este trabajo toma prestado Azure Blueprint Automation: Payment Processing for PCI DSS-compliant environments for payment card processing. Para más información sobre la expansión de este trabajo, en el documento ["Review and Guidance for Implementation"](https://aka.ms/pciblueprintprocessingoverview) (Revisión e instrucciones de implementación) se proporciona una revisión de los entornos compatibles con PCI DSS.

### <a name="use-case"></a>Caso de uso
Una pequeña tienda web llamada *Contoso Webstore* está lista para mover a la nube datos financieros, entre los que se incluye la información de pago de los clientes. 

Para conseguir sus objetivos, el administrador de Contoso Webstore está buscando una solución que se pueda implementar rápidamente. Para ello, usará esta prueba de concepto (POC) para hablar con las partes interesadas acerca de cómo se puede usar Azure para recopilar, almacenar y recuperar los datos financieros de acuerdo con los estrictos requisitos de cumplimiento.

> Tendrá la responsabilidad de realizar las revisiones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante la arquitectura que usó esta POC, ya que los requisitos pueden variar en función de las características de implementación y la ubicación geográfica. 

### <a name="elements-of-the-foundational-architecture"></a>Elementos de la arquitectura fundamental

La arquitectura fundamental está diseñada con los siguientes elementos ficticios:

Sitio de dominio: `contosowebstore.com`

Los roles de usuario se emplean para ilustrar el caso de uso y proporcionan una visión general de la interfaz de usuario.

#### <a name="role-site-and-subscription-admin"></a>Rol: Administrador del sitio y la suscripción

|item      |Ejemplo|
|----------|------|
|Nombre de usuario: |`adminXX@contosowebstore.com`|
| Nombre: |`Global Admin Azure PCI Samples`|
|Tipo de usuario:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- La cuenta de administrador no puede leer la información financiera sin enmascarar. Todas las acciones se registran.
- La cuenta de administrador no puede administrar ni iniciar sesión en SQL Database.
- La cuenta de administrador puede administrar Active Directory y las suscripciones.

#### <a name="role-sql-administrator"></a>Rol: Administrador de SQL

|item      |Ejemplo|
|----------|------|
|Nombre de usuario: |`sqlAdmin@contosowebstore.com`|
| Nombre: |`SQLADAdministrator PCI Samples`|
| Nombre: |`SQL AD Administrator`|
|Apellido: |`PCI Samples`|
|Tipo de usuario:| `Administrator`|

- La cuenta sqladmin no puede ver la información de la tarjeta de crédito sin filtrar. Todas las acciones se registran.
- La cuenta sqladmin puede administrar SQL Database.

#### <a name="role-clerk"></a>Rol: distribuidor

|item      |Ejemplo|
|----------|------|
|Nombre de usuario:| `receptionist_EdnaB@contosowebstore.com`|
| Nombre: |`Edna Benson`|
| Nombre:| `Edna`|
|Apellido:| `Benson`|
| Tipo de usuario: |`Member`|

Edna Benson es la directora del departamento de recepción y negocios. Es la responsable de garantizar que la información del cliente sea precisa y que la facturación se complete como es debido. Asimismo, ella es quien inició sesión para poder llevar a cabo todas las interacciones en el sitio web de demostración Contoso Webstore. Edna tiene los siguientes derechos: 

- Puede crear y leer la información de cliente.
- Puede modificar la información del cliente.
- Puede sobrescribir la información financiera.
- La cuenta de Edna no puede ver la información financiera sin filtrar.

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
>- Control de acceso basado en rol de Azure Active Directory (RBAC)

## <a name="deployment-architecture"></a>Arquitectura de implementación

En la siguiente sección se detallan los elementos de desarrollo e implementación.

### <a name="network-segmentation-and-security"></a>Segmentación de red y seguridad

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

La arquitectura fundamental reduce el riesgo de sufrir vulnerabilidades de seguridad al usar Application Gateway con un firewall de aplicaciones web (WAF) y tener habilitado el conjunto de reglas OWASP. Entre estas funcionalidades, cabe destacar:

- [SSL de un extremo a otro](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Descarga de SSL](/azure/application-gateway/application-gateway-ssl-portal) habilitado
- [TLS v1.0 y v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) deshabilitado
- [Firewall de aplicaciones web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (modo WAF)
- [Modo de prevención](/azure/application-gateway/application-gateway-web-application-firewall-portal) con el conjunto de reglas OWASP 3.0
- [Registro de diagnóstico](/azure/application-gateway/application-gateway-diagnostics) habilitado
- [Sondeos de estado personalizados](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) y [Azure Advisor](/azure/advisor/advisor-security-recommendations), que proporcionan protección adicional y notificaciones. Asimismo, Azure Security Center también proporciona un sistema de reputación.

#### <a name="virtual-network"></a>Red virtual

La arquitectura fundamental define una red virtual privada con un espacio de direcciones de 10.0.0.0/16.

#### <a name="network-security-groups"></a>Grupos de seguridad de red

Cada uno de los niveles de red tiene un grupo de seguridad de red dedicado (NSG):

- Un grupo de seguridad de red DMZ para el firewall y el modo WAFS de Application Gateway
- Un NSG para un jumpbox de administración (host de tipo bastión)
- Un NSG para el entorno del servicio de la aplicación

Cada uno de los NSG tiene determinados puertos y protocolos abiertos para así permitir que la solución funcione con seguridad y como es debido. Para obtener más información, consulte [Guía de PCI: grupos de seguridad de red](#network-security-groups).

Además, las siguientes opciones de configuración están habilitadas para cada NSG:

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

[Operations Management Suite (OMS)](/azure/operations-management-suite/) puede proporcionar a la tenida web Contoso Webstore un registro completo de toda la actividad del usuario y del sistema, además de un registro de los datos financieros. Se puede revisar y comprobar la exactitud de los cambios. 

- **Registros de actividad.**  Los [registros de actividad](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones que se realizaron en los recursos de la suscripción.
- **Registros de diagnóstico**  Los [registros de diagnóstico:](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) son todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, registros de Azure Blob Storage y registros de tablas y colas.
- **Registros de firewall.**  Application Gateway proporciona registros completos de diagnóstico y acceso. Los registros de firewall están disponibles para los recursos de Application Gateway que tienen WAF habilitado.
- **Archivado de registros.**  Todos los registros de diagnóstico se configuran para escribirse en una cuenta de Azure Storage centralizada y cifrada y que así queden archivados durante un período de retención definido (2 días). A continuación, esos registros se conectan a Azure Log Analytics para procesarlos, almacenarlos y agregarlos a los paneles. [Log Analytics](https://azure.microsoft.com/services/log-analytics) es un servicio de OMS que le ayudará a recopilar y analizar los datos que generaron los recursos en entornos locales o en la nube.

### <a name="encryption-and-secrets-management"></a>Administración de cifrado y secretos

La tienda web Contoso Webstore cifra todos los datos de las tarjetas de crédito y utiliza Azure Key Vault para administrar las claves, lo que impide que se puedan recuperar los datos CHD.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) se usa para cifrar todos los datos financieros de los clientes.
- Los datos se almacenan en el disco mediante [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) y BitLocker.

### <a name="identity-management"></a>Administración de identidades

Las siguientes tecnologías proporcionan funcionalidades de administración de identidad en el entorno de Azure.

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) es el servicio de directorio y de administración de identidades multiinquilino basado en la nube de Microsoft. Todos los usuarios de la solución se crearon en Azure Active Directory, incluidos los usuarios que acceden a SQL Database.
- La autenticación para acceder a la aplicación se realiza con Azure AD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos también usa Azure AD para autenticar la aplicación en Azure SQL Database. Para obtener más información, consulte [Always Encrypted: protección de datos confidenciales en SQL Database](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) detecta posibles vulnerabilidades que podrían afectan a las identidades de la organización, configura respuestas automatizadas a acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.
- El [control de acceso basado en rol de Azure (RBAC)](/azure/active-directory/role-based-access-control-configure) permite administrar al detalle el control de acceso de Azure. El acceso a la suscripción está limitado al administrador de dicha suscripción y el acceso a Azure Key Vault está restringido para todos los usuarios.

Para obtener más información sobre el uso de las características de seguridad de Azure SQL Database, consulte el ejemplo de la [aplicación de demostración de Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).
   
### <a name="web-and-compute-resources"></a>Recursos web y de procesos

#### <a name="app-service-environment"></a>Entorno de App Service

[Azure App Service](/azure/app-service/) es un servicio administrado para la implementación de aplicaciones web. La aplicación Contoso Webstore se implementa como una [aplicación web del servicio de aplicaciones](/azure/app-service-web/app-service-web-overview).

[Azure App Service Environment (ASE v2)](/azure/app-service/app-service-environment/intro) es una característica de App Service que proporciona un entorno completamente aislado y dedicado para ejecutar de forma segura las aplicaciones de App Service a gran escala. es un plan de servicio Premium que usa esta arquitectura fundamental para habilitar el cumplimiento de PCI DSS.

Las instancias de ASE están aisladas para ejecutar únicamente las aplicaciones de un solo cliente y siempre se implementan en una red virtual. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de las aplicaciones, y las aplicaciones pueden establecer conexiones seguras de alta velocidad a los recursos corporativos locales a través de redes virtuales.

Puede usar las instancias de ASE de esta arquitectura en los siguientes controles o configuraciones:

- Un host dentro de una red virtual segura y reglas de seguridad de red.
- Una instancia de ASE configurada con certificados ILB autofirmados para la comunicación HTTPS.
- En un [modo de equilibrio de carga interno](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3).
- [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) deshabilitado
- [Cifrado TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings) modificado
- Al controlar [puertos N/W de tráfico entrante](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic). 
- [WAF: al restringir datos](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Tráfico de SQL Database](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) permitido


#### <a name="jumpbox-bastion-host"></a>Jumpbox (host de tipo bastión)

Como el entorno de App Service está protegido y bloqueado, debe haber un mecanismo para permitir cualquier versión o cambio de DevOps que sea necesario como, por ejemplo, la capacidad de supervisar la aplicación web mediante Kudu. Una máquina virtual está protegida detrás del equilibrador de carga de NAT, lo que ofrece la posibilidad de conectarse a la máquina virtual en un puerto distinto de TCP 3389. 

Se ha creado una máquina virtual como un jumpbox (host de tipo bastión) con las siguientes configuraciones:

-   [Extensión antimalware](/azure/security/azure-security-antimalware)
-   [Extensión de OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensión de Diagnósticos de Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) con Azure Key Vault 
-   Una [directiva de apagado automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reducir el consumo de recursos de la máquina virtual cuando no esté en uso.

### <a name="security-and-malware-protection"></a>Seguridad y protección contra malware

[Azure Security Center](https://azure.microsoft.com/services/security-center/) proporciona una visión centralizada del estado de la seguridad de todos los recursos de Azure. De un vistazo, puede comprobar que los controles de seguridad adecuados se hayan implementado y configurado correctamente, así como identificar rápidamente los recursos que requieran atención.  

[Azure Advisor](/azure/advisor/advisor-overview) es un consultor en la nube personalizado que le ayudará a seguir los procedimientos recomendados para optimizar las implementaciones de Azure. Analiza la configuración de recursos y la telemetría de uso, y recomienda soluciones que pueden ayudar a mejorar la rentabilidad, el rendimiento, la alta disponibilidad y la seguridad de los recursos de Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) para Azure Cloud Services y Virtual Machines es una funcionalidad de protección en tiempo real que le ayuda a identificar y eliminar virus, spyware y otro software malintencionado mediante alertas que se pueden configurar para que le avisen si hay software malintencionado o no deseado que se intenta instalar o ejecutar en los sistemas de Azure.

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

La implementación predeterminada está diseñada para proporcionar una base de referencia de recomendaciones de Security Center que indican un estado de configuración correcto y seguro. Puede habilitar la recopilación de datos en Azure Security Center. Para obtener más información, consulte [Guía de inicio rápido de Azure Security Center](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Implementación de la solución

Los componentes para implementar esta solución están disponibles en el [repositorio de código del plano técnico de procesamiento de pago][code-repo]. Para implementar la arquitectura fundamental, debe ejecutar una serie de pasos mediante Microsoft PowerShell v5. Para conectarse al sitio web, debe proporcionar un nombre de dominio personalizado (por ejemplo, contoso.com). Esto se especifica mediante el conmutador `-customHostName` del paso 2. Para obtener más información, consulte [Comprar un nombre de dominio personalizado para Azure Web Apps](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). No es necesario tener un nombre de dominio personalizado para implementar y ejecutar la solución correctamente, pero no se podrá conectar al sitio web si quiere realizar alguna demostración.

Los scripts agregan usuarios de dominio al inquilino de Azure AD que especifique. Microsoft recomienda crear un nuevo inquilino de Azure AD para usarlo como prueba.

Si tiene algún problema durante la implementación, consulte [FAQ and troubleshooting](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md) (Preguntas más frecuentes y solución de problemas).

Microsoft recomienda firmemente el uso de una instalación nueva de PowerShell para implementar la solución. Como alternativa, compruebe que esté usando los módulos más recientes, ya que son necesarios para ejecutar correctamente los scripts de la instalación. En este ejemplo, se inicia sesión en el jumpbox (host de tipo bastión) y se ejecutan los siguientes comandos. Tenga en cuenta que esto habilitará el comando de dominio personalizado.


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

Los clientes tienen la responsabilidad de guardar una copia de la [matriz del resumen de responsabilidades](https://aka.ms/fsiblueprintcrm), en la cual se describen los requisitos de FFIEC que son responsabilidad de esos clientes, quienes, a su vez, son responsabilidad de Microsoft Azure.



## <a name="disclaimer-and-acknowledgments"></a>Declinación de responsabilidades y reconocimientos

*Septiembre de 2017*

- Este documento es meramente informativo. MICROSOFT Y AVYAN NO GARANTIZAN DE NINGUNA MANERA (YA SEA DE FORMA EXPRESA, IMPLÍCITA O ESTATUTARIA) LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.  
- Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto de Microsoft o Ayvan.  
- Los clientes pueden copiar y usar este documento con fines internos y de referencia.  

  > [!NOTE]
  > Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de proceso en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.  

- La solución de este documento está pensada como una arquitectura fundamental y no debe usarse tal cual para fines de producción. Lograr el cumplimiento requiere que los clientes consulten con su auditor para validar la solución definitiva del cliente.  
- Todos los nombres de los clientes, los registros de transacciones y cualquier dato relacionado en esta página son ficticios, ya que se han creado específicamente para esta arquitectura fundamental y solo se proporcionan con fines ilustrativos. No se pretende crear ninguna asociación o conexión real y ninguna debe inferirse.  
- Esta solución fue desarrollada conjuntamente por Microsoft y Avyan Consulting y está disponible en la [licencia MIT](https://opensource.org/licenses/MIT).

### <a name="document-authors"></a>Autores de los documentos

* *Frank Simorjay (Microsoft)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repositorio de código"
