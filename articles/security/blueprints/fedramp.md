---
title: 'Azure Blueprint Automation: aplicaciones web para FedRAMP'
description: 'Azure Blueprint Automation: aplicaciones web para FedRAMP'
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d0521d68bab8bd0b7db53a512da6d37033abd85e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation---web-applications-for-fedramp"></a>Azure Blueprint Automation: aplicaciones web para FedRAMP

## <a name="overview"></a>Información general

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) es un programa a nivel del gobierno de los Estados Unidos que proporciona un enfoque estandarizado para la evaluación de la seguridad, la concesión de autorizaciones y la supervisión continua de servicios y productos en la nube. Azure Blueprint Automation: aplicaciones web para FedRAMP proporciona instrucciones para la implementación de un entorno de infraestructura como servicio (IaaS) compatible con FedRAMP adecuado para una aplicación web simple accesible desde Internet. Esta solución automatiza la implementación y la configuración de recursos de Azure para una arquitectura de referencia común, la cual muestra las formas en que los clientes pueden cumplir los requisitos específicos de seguridad y cumplimiento y sirve como base para que los clientes creen y configuren sus propias soluciones en Azure. La solución implementa un subconjunto de controles de la línea de base de FedRAMP High basada en NIST SP 800-53. Para obtener más información sobre los requisitos de FedRAMP High y esta solución, consulte [FedRAMP High Requirements - High-Level Overview](fedramp-controls-overview.md) (Requisitos de FedRAMP High: información general de alto nivel). ***Nota: Esta solución se implementa en Azure Government.***

Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Implementar una aplicación sin modificar en este entorno no es suficiente para satisfacer por completo los requisitos de la línea de base de FedRAMP High. Tenga en cuenta lo siguiente:
- Esta arquitectura fundamental proporciona una línea de base para que los clientes puedan usar Azure de forma que sea compatible con FedRAMP.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente. 

Para obtener información general de cómo funciona esta solución, vea este [vídeo](https://aka.ms/fedrampblueprintvideo) en el que se explica y demuestra cómo realizar la implementación.

Haga clic [aquí](https://aka.ms/fedrampblueprintrepo) para ver las instrucciones de implementación.

## <a name="solution-components"></a>Componentes de soluciones

Azure Blueprint Automation implementa automáticamente una arquitectura de referencia de aplicación web de IaaS con controles de seguridad configurados previamente para ayudar a los clientes a lograr el cumplimiento de los requisitos de FedRAMP. La solución consta de plantillas de Azure Resource Manager y scripts de PowerShell que guían la configuración y la implementación de recursos. Se proporciona [documentación de cumplimiento](#compliance-documentation) complementaria de Azure Blueprint, que indica que la herencia de control de seguridad de Azure y los recursos y configuraciones implementados se alinean con los controles de seguridad de NIST SP 800-53, con lo que se permite que las organizaciones realicen un seguimiento rápido de las obligaciones de cumplimiento.

## <a name="architecture-diagram"></a>Diagrama de la arquitectura

Esta solución implementa una arquitectura de referencia para una aplicación web de IaaS con un back-end de la base de datos. La arquitectura incluye un nivel de web, un nivel de datos, infraestructura de Active Directory, puerta de enlace de aplicaciones y un equilibrador de carga. Las máquinas virtuales implementadas en los niveles de web y de datos se configuran en un conjunto de disponibilidad y las instancias de SQL Server se configuran en un grupo de disponibilidad AlwaysOn de alta disponibilidad. Las máquinas virtuales están unidas a un dominio y las directivas de grupo de Active Directory se usan para aplicar configuraciones de seguridad y cumplimiento a nivel del sistema operativo. Un jumpbox de administración (host de tipo bastión) proporciona una conexión segura para que los administradores accedan a los recursos implementados.

![alt text](images/fedramp-architectural-diagram.png?raw=true "Automatización del plano de aplicaciones web de IaaS para entornos compatibles con FedRAMP")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

* **Azure Virtual Machines**
    - (1) Administración/bastión (Windows Server 2016 Datacenter)
    - (2) Controlador de dominio de Active Directory (Windows Server 2016 Datacenter)
    - (2) Nodo de clúster de SQL Server (SQL Server 2016 en Windows Server 2012 R2)
    - (1) Testigo de SQL Server (Windows Server 2016 Datacenter)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
* **Conjuntos de disponibilidad**
    - (1) Controladores de dominio de Active Directory
    - (1) Testigo y nodos del clúster de SQL
    - (1) Web/IIS
* **Azure Virtual Network**
    - (1) /16 redes virtuales
    - (5) /24 subredes
    - Las configuraciones de DNS están establecidas en ambos controladores de dominio
* **Equilibrador de carga de Azure**
    - (1) Equilibrador de carga de SQL
* **Introducción a Puerta de enlace de aplicaciones**
    - (1) Habilitado para Application Gateway de WAF
      - Modo de firewall: prevención
      - Conjunto de reglas: OWASP 3.0
      - Escucha: puerto 443
* **Azure Storage**
    - (7) Cuentas de almacenamiento con redundancia geográfica
* **Azure Backup**
    - (1) Almacén de Recovery Services
* **Azure Key Vault**
    - (1) Key Vault
* **Azure Active Directory**
* **Administrador de recursos de Azure**
* **Azure Log Analytics**
* **Azure Automation**
    - (1) Cuenta de Automation
* **Operations Management Suite**
    - (1) Área de trabajo de OMS

## <a name="deployment-architecture"></a>Arquitectura de implementación

En la siguiente sección se detallan los elementos de desarrollo e implementación.

### <a name="network-segmentation-and-security"></a>Segmentación de red y seguridad

#### <a name="application-gateway"></a>Application Gateway

La arquitectura reduce el riesgo de sufrir vulnerabilidades de seguridad, ya que usa Application Gateway con el firewall de aplicaciones web (WAF) y tiene el conjunto de reglas OWASP habilitado. Entre estas funcionalidades, cabe destacar:

- [SSL de un extremo a otro](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Opción para habilitar la [descarga SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Opción para deshabilitar [TLS v1.0 y v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicaciones web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevención](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con el conjunto de reglas OWASP 3.0

#### <a name="virtual-network"></a>Red virtual

La arquitectura define una red virtual privada con un espacio de direcciones de 10.200.0.0/16.

#### <a name="network-security-groups"></a>Grupos de seguridad de red

Esta solución implementa los recursos en una arquitectura con una subred web independiente, la subred de la base de datos, la subred de Active Directory y la subred de administración dentro de una red virtual. Las subredes se separan de forma lógica mediante las reglas del grupo de seguridad de red aplicadas a las subredes individuales para restringir el tráfico entre subredes unicamente según sea necesario para la funcionalidad de administración y del sistema.

Vea la configuración de los [grupos de seguridad de red](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implementada con esta solución. Las organizaciones pueden configurar grupos de seguridad de red mediante la edición del archivo anterior usando [esta documentación](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guía.

Cada una de las subredes tiene un grupo de seguridad de red dedicado (NSG):
- 1 NSG para Application Gateway (LBNSG)
- 1 NSG para Jumpbox (MGTNSG)
- 1 NSG para los controladores de dominio principal y de copia de seguridad (ADNSG)
- 1 NSG para servidores SQL Server y el testigo de recurso compartido de archivos (SQLNSG)
- 1 NSG para el nivel de web (WEBNSG)

#### <a name="subnets"></a>Subredes

Cada subred se asocia a su NSG correspondiente.

### <a name="data-at-rest"></a>Datos en reposo

La arquitectura protege los datos en reposo mediante varias medidas de cifrado.

#### <a name="azure-storage"></a>Azure Storage

Para cumplir con los requisitos de cifrado de los datos en reposo, todas las cuentas de almacenamiento usan el [cifrado del servicio Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>SQL Database

SQL Database está configurado para usar el [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), que realiza el cifrado y descifrado de archivos de datos y de registro en tiempo real para proteger la información en reposo. TDE garantiza que los datos almacenados no hayan estado sujetos a un acceso no autorizado. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption se utiliza para los discos cifrados de máquinas virtuales de IaaS de Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos y sistema operativo. La solución se integra con Azure Key Vault para ayudarle a controlar y administrar las claves de cifrado del disco.

### <a name="logging-and-auditing"></a>Registro y auditoría

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. 

- **Registros de actividad:** [los registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones que se realizaron en los recursos de la suscripción.
- **Registros de diagnóstico:** [los registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) son todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, registros de Azure Storage, registros de auditoría de Key Vault, y registros de firewall y acceso a Application Gateway.
- **Archivo de registro:** los registros de diagnóstico y de actividad de Azure pueden conectarse a Azure Log Analytics para procesarse, almacenarse y agregarse a los paneles. El usuario puede configurar la retención hasta el día 730 para cumplir los requisitos de retención específicos de una organización.

### <a name="secrets-management"></a>Administración de secretos

En esta solución se usa Azure Key Vault para administrar las claves y los secretos.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. 
- La solución se integra con Azure Key Vault para administrar los secretos y las claves de cifrado del disco de la máquina virtual de IaaS.

### <a name="identity-management"></a>Administración de identidades

Las siguientes tecnologías proporcionan funcionalidades de administración de identidad en el entorno de Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft.
- La autenticación para una aplicación web implementada por el cliente puede realizarse mediante Azure AD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- El [control de acceso basado en rol de Azure (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite administrar al detalle el control de acceso de Azure. El acceso a la suscripción se limita al administrador de la suscripción y el acceso a los recursos se puede limitar según el rol de usuario.
- Una instancia implementada de Active Directory de IaaS proporciona administración de identidades en el nivel de sistema operativo para máquinas virtuales implementadas de IaaS.
   
### <a name="compute-resources"></a>Recursos de proceso

#### <a name="web-tier"></a>Nivel de web

La solución implementa máquinas virtuales del nivel de web en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Los conjuntos de disponibilidad garantizan que las máquinas virtuales se distribuyan entre varios clústeres de hardware aislados para mejorar la disponibilidad.

#### <a name="database-tier"></a>Nivel de base de datos

La solución implementa máquinas virtuales de nivel de base de datos en un conjunto de disponibilidad como un [grupo de disponibilidad AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). La función de grupo de disponibilidad AlwaysOn proporciona funcionalidades de alta disponibilidad y recuperación ante desastres. 

#### <a name="active-directory"></a>Active Directory

Todas las máquinas virtuales implementadas por la solución están unidas a un dominio y las directivas de grupo de Active Directory se usan para aplicar configuraciones de seguridad y cumplimiento a nivel del sistema operativo. Las máquinas de virtuales de Active Directory se implementan en un conjunto de disponibilidad.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (host de tipo bastión)

Un jumpbox de administración (host de tipo bastión) proporciona una conexión segura para que los administradores accedan a los recursos implementados. El NSG asociado a la subred de administración donde se encuentra la máquina virtual de jumpbox solo permite conexiones en el puerto TCP 3389 para RDP. 

### <a name="malware-protection"></a>Protección contra malware

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Virtual Machines proporciona una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar en caso de que un software malintencionado o no deseado conocido se intente instalar o ejecutar en máquinas virtuales protegidas.

### <a name="patch-management"></a>Administración de revisiones

Las máquinas virtuales Windows implementadas por este programa Blueprint Automation se configuran de forma predeterminada para recibir actualizaciones automáticas del servicio Windows Update. Esta solución también implementa la solución Azure Automation de OMS a través de la cual se pueden crear implementaciones de actualizaciones para implementar revisiones en servidores Windows cuando sea necesario.

### <a name="operations-management"></a>Administración de operaciones

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) es un servicio de Operations Management Suite (OMS) que le permite recopilar y analizar los datos generados por los recursos en los entornos locales o en Azure.

#### <a name="oms-solutions"></a>Soluciones de OMS

Las siguientes soluciones de OMS se instalaron previamente como parte de esta solución:
- [Evaluación de AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Evaluación de antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Seguridad y auditoría](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [Evaluación de SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Administración de actualizaciones](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Seguimiento de cambios](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Documentación de cumplimiento

### <a name="customer-responsibility-matrix"></a>Matriz de responsabilidades del cliente

En la [matriz de las responsabilidades del cliente](https://aka.ms/blueprinthighcrm) (libro de Excel) se enumeran todos los controles de seguridad que requiere la línea de base de FedRAMP High. La matriz denota, para cada control (o subparte del control), si la implementación responsable del control es responsabilidad de Microsoft, del cliente o compartida entre los dos. 

### <a name="control-implementation-matrix"></a>Matriz de implementación de control

En la [matriz de implementación de control](https://aka.ms/blueprintwacim) (libro de Excel) se enumeran todos los controles de seguridad que requiere la línea de base de FedRAMP High. La matriz denota, para cada control (o subparte del control) que se designa como responsabilidad del cliente en la matriz de responsabilidades del cliente, 1) si Blueprint Automation implementa el control y 2) una descripción de cómo se adapta la implementación a los requisitos del control. Este contenido también está disponible [aquí](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Implementación de la solución

Esta solución de Azure Blueprint se compone de archivos de configuración de JSON y scripts de PowerShell que se controlan mediante el servicio de API de Azure Resource Manager para implementar recursos en Azure. Puede encontrar instrucciones detalladas acerca de la implementación [aquí](https://aka.ms/fedrampblueprintrepo). ***Nota: Esta solución se implementa en Azure Government.***

#### <a name="quickstart"></a>Guía de inicio rápido
1. Clone o descargue [este](https://aka.ms/fedrampblueprintrepo) repositorio de GitHub en su estación de trabajo local.

2. Ejecute el script de PowerShell anterior a la implementación: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Haga clic en el botón siguiente, inicie sesión en Azure Portal, escriba los parámetros de plantilla de ARM necesarios y haga clic en **Comprar**.

    [![Implementación en Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Renuncia de responsabilidades

- Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "como está". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.  
- Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.  
- Los clientes pueden copiar y usar este documento con fines internos y de referencia.  
- Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.  
- Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
- Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
