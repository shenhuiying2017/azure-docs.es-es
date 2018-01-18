---
title: "Procedimientos recomendados de seguridad de las máquinas virtuales de Azure"
description: "En este artículo se proporcionan varios procedimientos recomendados de seguridad para usarlos en máquinas virtuales ubicadas en Azure."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 6541d09d7f1a7e85333f54797dba7db79328e9de
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="best-practices-for-azure-vm-security"></a>Procedimientos recomendados de seguridad para las máquinas virtuales de Azure

En la mayoría de los escenarios de IaaS (infraestructura como servicio), las [máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/) son la principal carga de trabajo de las organizaciones que usan la informática en la nube. Esto es especialmente evidente en los [escenarios híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx), en los que las organizaciones quieran migrar lentamente las cargas de trabajo a la nube. En estos escenarios, siga las [consideraciones generales de seguridad de IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) y aplique los procedimientos recomendados de seguridad a todas las máquinas virtuales.

En este artículo se describen varios procedimientos recomendados de seguridad para las máquinas virtuales, todos derivados de nuestras experiencias directas y las de nuestros clientes con las máquinas virtuales.

Los procedimientos recomendados se basan en un consenso de opinión y son válidos para las funcionalidades y conjuntos de características actuales de la plataforma Azure. Puesto que las opiniones y las tecnologías pueden cambiar con el tiempo, tenemos previsto actualizar este artículo con regularidad para reflejar dichos cambios.

Para cada procedimiento recomendado, en este artículo se explica:

* El procedimiento recomendado.
* Por qué es conveniente habilitarlo.
* Cómo puedo aprender a habilitarlo.
* Qué puede ocurrir si no se habilita.
* Posibles alternativas al procedimiento recomendado.

En el artículo se examinan los siguientes de seguridad para las máquinas virtuales:

* Autenticación de la máquina virtual y control de acceso
* Acceso de red y disponibilidad de máquinas virtuales
* Protección de los datos en reposo almacenados en máquinas virtuales mediante su cifrado
* Administrar las actualizaciones de la máquina virtual
* Administrar la posición de seguridad de la máquina virtual
* Supervisar el rendimiento de la máquina virtual

## <a name="vm-authentication-and-access-control"></a>Autenticación de la máquina virtual y control de acceso

El primer paso para proteger la máquina virtual es garantizar que solo los usuarios autorizados puedan aprovisionar nuevas máquinas virtuales. Puede usar las [directivas de Azure](../azure-policy/azure-policy-introduction.md) con el fin de establecer convenciones para los recursos de su organización, crear directivas personalizadas y aplicarlas a recursos, como [grupos de recursos](../azure-resource-manager/resource-group-overview.md).

Las máquinas virtuales que pertenecen a un grupo de recursos heredan de forma natural sus directivas. Aunque se recomienda este enfoque para administrar las máquinas virtuales, también puede controlar el acceso a directivas de máquinas virtuales individuales mediante el [control de acceso basado en rol (RBAC)](../active-directory/role-based-access-control-configure.md).

Al habilitar las directivas de Resource Manager y RBAC para controlar el acceso a la máquina virtual, se ayuda a mejorar la seguridad general de la máquina virtual. Se recomienda consolidar las máquinas virtuales con el mismo ciclo de vida en el mismo grupo de recursos. Mediante los grupos de recursos, puede implementar, supervisar y acumular los costos para sus recursos. Para permitir a los usuarios acceder y configurar máquinas virtuales, use un [enfoque de privilegios mínimos](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). Y al asignar privilegios a los usuarios, planee utilizar los siguientes roles integrados de Azure:

- [Colaborador de la máquina virtual](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): puede administrar máquinas virtuales, pero no la red virtual ni la cuenta de almacenamiento a la que están conectadas.
- [Colaborador de la máquina virtual clásica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): puede administrar máquinas virtuales creadas con el modelo de implementación clásico, pero no la cuenta de almacenamiento ni la red virtual a la que están conectadas.
- [Administrador de seguridad](../active-directory/role-based-access-built-in-roles.md#security-manager): puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales.
- [Usuario de DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): puede ver todo el contenido, así como conectar, iniciar, reiniciar y apagar las máquinas virtuales.

No comparta cuentas ni contraseñas entre los administradores ni reutilice contraseñas en diferentes cuentas de usuario o servicios, especialmente las de las redes sociales u otras actividades no administrativas. Lo ideal es usar las plantillas de [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para configurar las máquinas virtuales de forma segura. Con este enfoque puede reforzar las opciones de implementación y aplicar la configuración de seguridad a lo largo de la implementación.

Es posible que las organizaciones que no apliquen el control de acceso a los datos mediante funcionalidades como RBAC estén concediendo más privilegios de los necesarios a sus usuarios. El acceso de usuarios no adecuado a determinados datos puede poner en peligro directamente esos datos.

## <a name="vm-availability-and-network-access"></a>Acceso de red y disponibilidad de máquinas virtuales

Si la máquina virtual ejecuta aplicaciones esenciales que necesitan tener una alta disponibilidad, recomendamos encarecidamente usar varias máquinas virtuales. Para mejorar la disponibilidad, cree al menos dos máquinas virtuales en el [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md).

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) también requiere que las máquinas virtuales de carga equilibrada pertenezcan al mismo conjunto de disponibilidad. Si se debe acceder a estas máquinas virtuales desde Internet, debe configurar un [equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-internet-overview.md).

Cuando las máquinas virtuales están expuestas a Internet, es importante [controlar el flujo de tráfico de red con los grupos de seguridad de red (NSG)](../virtual-network/virtual-networks-nsg.md). Puesto que los grupos de seguridad de red se pueden aplicar a subredes, puede minimizar el número de ellos si agrupa los recursos por subred y aplica estos grupos a las subredes. La intención es crear una capa de aislamiento de red, que puede hacerse configurando de la forma adecuada las funcionalidades de [seguridad de red](../best-practices-network-security.md) en Azure.

También puede usar la característica de acceso a las máquinas virtuales justo a tiempo desde Azure Security Center para controlar quién tiene acceso remoto a una máquina virtual específica y durante cuánto tiempo.

Las organizaciones que no aplican las restricciones de acceso de red a las máquinas virtuales accesibles desde Internet están expuestas a riesgos de seguridad; por ejemplo, a ataques por fuerza bruta mediante un protocolo de escritorio remoto (RDP).

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Protección de los datos en reposo almacenados en máquinas virtuales mediante su cifrado

El [cifrado de los datos en reposo](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) es un paso obligatorio en lo que respecta a la privacidad de los datos, el cumplimiento y la soberanía de los datos. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) permite que los administradores de TI cifren discos de máquinas virtuales IaaS Windows y Linux. Disk Encryption combina la característica BitLocker de Windows estándar del sector y la característica dm-crypt de Linux para ofrecer el cifrado de volumen para el sistema operativo y los discos de datos.

Puede aplicar Disk Encryption para proteger sus datos y así satisfacer los requisitos de cumplimiento y seguridad de la organización. La organización debería pensar en usar cifrado como ayuda para mitigar los riesgos relacionados con el acceso no autorizado a los datos. También se recomienda cifrar las unidades de disco antes de escribir los datos confidenciales en ellas.

Asegúrese de cifrar los volúmenes de datos de la máquina virtual para protegerlos en reposo en la cuenta de almacenamiento de Azure. Proteja las claves de cifrado y los secretos mediante [Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/).

Las organizaciones que no apliquen el cifrado de datos están más expuestas a problemas de integridad de los datos. Por ejemplo, los usuarios no autorizados pueden robar datos de las cuentas en peligro u obtener acceso no autorizado a los datos codificados en ClearFormat. Además de estos riesgos, para cumplir las normativas del sector, las compañías tienen que demostrar que son diligentes y que usan los controles adecuados para mejorar la seguridad de los datos.

Para más información acerca de Disk Encryption, consulte [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Administrar las actualizaciones de la máquina virtual

Puesto que las máquinas virtuales de Azure, al igual que todas las máquinas virtuales locales, están pensadas para ser administradas por el usuario, Azure no inserta las actualizaciones de Windows en ellas. No obstante, se recomienda dejar habilitada la configuración automática de Windows Update. Otra opción consiste en implementar [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) u otro producto de administración de actualizaciones adecuado en otra máquina virtual o local. WSUS y Windows Update mantienen actualizadas las máquinas virtuales. También se recomienda usar un producto de análisis para comprobar que todas las máquinas virtuales de IaaS estén actualizadas.

Las imágenes de archivo que proporciona Azure se actualizan periódicamente para incluir el ciclo de actualizaciones de Windows más reciente. Sin embargo, no hay ninguna garantía de que las imágenes estén actualizadas durante la implementación. Es posible que haya un retraso pequeño (de no más de unas cuantas semanas) después de que las versiones sean públicas. Buscar e instalar las actualizaciones de Windows debe ser el primer paso que debe realizar en cada implementación. Es especialmente importante aplicar esta medida al implementar imágenes que proceden de usted o de su propia biblioteca. Las imágenes que se proporcionan como parte de Azure Marketplace se actualizan automáticamente de forma predeterminada.

Las organizaciones que no aplican directivas de actualización de software están más expuestas a amenazas que aprovechan las vulnerabilidades conocidas, previamente fijas. Además de estos riesgos, para cumplir con las normativas del sector, las compañías tienen que demostrar que son diligentes y que usan los controles adecuados para mejorar la seguridad de sus cargas de trabajo ubicadas en la nube.

Es importante destacar que los procedimientos recomendados de actualización de software para los centros de datos tradicionales y IaaS de Azure tienen muchas similitudes. Por lo tanto, recomendamos evaluar las directivas de actualización de software actuales que se incluirán en las máquinas virtuales.

## <a name="manage-your-vm-security-posture"></a>Administrar la posición de seguridad de la máquina virtual

Las amenazas cibernéticas evolucionan, y para proteger las máquinas virtuales hace falta una funcionalidad de supervisión avanzada que pueda detectar rápidamente las amenazas, evitar el acceso no autorizado a los recursos, desencadenar alertas y reducir los falsos positivos. La posición de seguridad para esta carga de trabajo incluye todos los aspectos de seguridad de la máquina virtual, desde la administración de las actualizaciones para proteger el acceso de red.

Para supervisar la posición de seguridad de sus máquinas virtuales [Windows](../security-center/security-center-virtual-machine.md) y [Linux VMs](../security-center/security-center-linux-virtual-machine.md), utilice [Azure Security Center](../security-center/security-center-intro.md). En Azure Security Center, proteja las máquinas virtuales aprovechando las ventajas de las funcionalidades siguientes:

* Aplicar la configuración de seguridad del sistema operativo con las reglas de configuración recomendadas
* Identificar y descargar las actualizaciones críticas y de seguridad del sistema que puedan faltar
* Implementar recomendaciones de protección antimalware del punto de conexión
* Validar el cifrado del disco
* Evaluar y corregir las vulnerabilidades
* Detectar amenazas

Security Center puede supervisar activamente si hay posibles amenazas, que se mostrarán en **Alertas de seguridad**. Las amenazas correlacionadas se agregan en una única vista denominada **Incidente de seguridad**.

También puede ver el siguiente vídeo para comprender cómo Security Center puede ayudarle a identificar posibles amenazas en las máquinas virtuales que se encuentran en Azure:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player]

Las organizaciones que no aplican una posición de seguridad segura para sus máquinas virtuales no se enteran de posibles intentos de eludir los controles de seguridad establecidos llevados a cabo por usuarios no autorizados.

## <a name="monitor-vm-performance"></a>Supervisar el rendimiento de la máquina virtual

El abuso de los recursos puede ser un problema cuando los procesos de las máquinas virtuales consumen más recursos de los que deberían. Los problemas de rendimiento con una máquina virtual pueden provocar la interrupción del servicio, lo que infringe el principio de seguridad de disponibilidad. Por esta razón, resulta imprescindible supervisar el acceso a las máquinas virtuales no solo de forma reactiva (aunque haya un problema), sino también de forma preventiva, usando como base de referencia un rendimiento medido durante el funcionamiento normal.

Al analizar los [archivos de registro de diagnóstico de Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), puede supervisar los recursos de la máquina virtual e identificar posibles problemas que podrían poner en peligro la disponibilidad y rendimiento. La extensión Diagnósticos de Azure proporciona funcionalidades de supervisión y diagnóstico en máquinas virtuales basadas en Windows. Para habilitar estas funcionalidades, incluya la extensión como parte de la [plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

También puede usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) para obtener una mayor visibilidad del estado de los recursos.

Las organizaciones que no supervisan el rendimiento de la máquina virtual no pueden determinar si ciertos cambios en los patrones de rendimiento son normales o anómalos. Si la máquina virtual está consumiendo más recursos de lo habitual, una anomalía de este tipo podría indicar un posible ataque procedente de un recurso externo o un proceso en peligro que se está ejecutando en la máquina virtual.
