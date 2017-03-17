---
title: "Procedimientos recomendados de seguridad de las máquinas virtuales de Azure | Microsoft Docs"
description: "En este artículo se proporciona una colección de procedimientos recomendadas de seguridad para emplearse en máquinas virtuales ubicadas en Azure."
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
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Procedimientos recomendados de seguridad de las máquinas virtuales de Azure

En la mayoría de los escenarios de IaaS (infraestructura como servicio), las [máquinas virtuales](https://docs.microsoft.com/en-us/azure/virtual-machines/) son la principal carga de trabajo de las organizaciones que usan la informática en la nube. Además, constituyen los elementos predominantes de los [escenarios híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx), en los que las organizaciones quieran migrar lentamente las cargas de trabajo a la nube. Debe seguir las [consideraciones generales de seguridad del escenario de IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) y asegurarse de que ha aplicado procedimientos recomendados de seguridad a todas las máquinas virtuales que se encuentren en Azure.

En este artículo veremos una colección de procedimientos recomendados de seguridad de las máquinas virtuales de Azure. Estos procedimientos recomendados proceden de nuestra experiencia con las máquinas virtuales Azure y de la de clientes como usted. 

Para cada procedimiento recomendado, explicaremos:

- Qué es el procedimiento recomendado
- Por qué le conviene habilitar este procedimiento recomendado
- Cuál podría ser el resultado si no habilita el procedimiento recomendado
- Alternativas posibles al procedimiento recomendado
- Cómo aprender a habilitar el procedimiento recomendado

Este artículo de procedimientos recomendados de seguridad de máquinas virtuales de Azure se basa en las funcionalidades y los conjuntos de características de la plataforma Azure existentes cuando se redactó. Las opiniones y las tecnologías cambian con el tiempo, por lo que se actualizará de forma periódica para reflejar esos cambios.

Estos son algunos de los procedimientos recomendados de seguridad de las máquinas virtuales de Azure descritos en este artículo:

- Control de acceso y autenticación de máquinas virtuales
- Acceso de red y disponibilidad de máquinas virtuales
- Protección de los datos en reposo almacenados en máquinas virtuales de Azure mediante su cifrado
- Administración de actualizaciones de máquinas virtuales
- Administración de la posición de seguridad de máquinas virtuales
- Supervisión del rendimiento de máquinas virtuales

## <a name="virtual-machine-authentication-and-access-control"></a>Control de acceso y autenticación de máquinas virtuales

El primer paso para proteger la máquina virtual es garantizar que solo los usuarios autorizados puedan aprovisionar nuevas máquinas virtuales. Puede usar [las directivas de Resource Manager](../azure-resource-manager/resource-manager-policy.md) con el fin de establecer convenciones para los recursos de su organización, crear directivas personalizadas y aplicarlas a recursos, como un [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Las máquinas virtuales que pertenecen a ese grupo de recursos heredarán estas directivas. Aunque se trata del método recomendado para administrar recursos (incluidas las máquinas virtuales) que tienen necesidades diferentes y se encuentran en distintos grupos de recursos, también puede controlar el acceso individual a las máquinas virtuales utilizando el [control de acceso basado en roles (RBAC)](../active-directory/role-based-access-control-configure.md).

Al habilitar las directivas de Azure Resource Manager y el RBAC para controlar el acceso a las máquinas virtuales, mejorará la seguridad global de la máquina virtual. Se recomienda colocar en el mismo grupo de recursos aquellas máquinas virtuales estrechamente acopladas que comparen el mismo ciclo de vida. Los grupos de recursos le permiten implementar y supervisar los recursos como un grupo, y acumular los costos de facturación por grupo de recursos. Adopte el enfoque de [privilegios mínimos](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) para permitir que los usuarios aprovisionen máquinas virtuales y planee usar los siguientes roles integrados de Azure al asignar privilegios a los usuarios:

- [Colaborador de la máquina virtual](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): puede administrar máquinas virtuales, pero no la red virtual ni la cuenta de almacenamiento a la que están conectadas.
- [Colaborador de la máquina virtual clásica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas.
- [Administrador de seguridad](../active-directory/role-based-access-built-in-roles.md#security-manager): puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales.
- [Usuario de DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): puede ver todo el contenido, así como conectar, iniciar, reiniciar y apagar las máquinas virtuales.

No comparta cuentas ni contraseñas entre los administradores o reutilice contraseñas en diferentes cuentas de usuario o servicios, especialmente las de medios sociales o de otras actividades no administrativas. Lo ideal es usar las plantillas de [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para proteger el aprovisionamiento de sus máquinas virtuales. Con este enfoque puede proteger las opciones de implementación y aplicar la configuración de seguridad a lo largo de la implementación.

Es posible que las organizaciones que no apliquen el control de acceso a los datos mediante funcionalidades como RBAC estén concediendo más privilegios de los necesarios a sus usuarios. Esto puede poner en peligro los datos si algunos usuarios obtienen acceso a datos que no les conciernen.
 

## <a name="virtual-machine-availability-and-network-access"></a>Acceso de red y disponibilidad de máquinas virtuales

Si la máquina virtual ejecuta aplicaciones esenciales que necesitan tener una alta disponibilidad, se recomienda encarecidamente que use varias máquinas virtuales.  Para mejorar la disponibilidad, cree al menos dos máquinas virtuales en el [conjunto de disponibilidad](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md). El [equilibrador de carga](../load-balancer/load-balancer-overview.md) de Azure también requiere que las VM de carga equilibrada pertenezcan al mismo conjunto de disponibilidad. Si se puede acceder a estas máquinas virtuales desde Internet, debe configurar un [equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-internet-overview.md).

Cuando las máquinas virtuales están expuestas a Internet, es importante asegurarse de [controlar el flujo de tráfico de red con los grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).  Puesto que los grupos de seguridad de red se pueden aplicar a subredes, puede minimizar el número de ellos si agrupa los recursos por subred y aplica estos grupos a subredes. La intención es crear una capa de aislamiento de red, que puede realizarse configurando de la forma adecuada las funcionalidades de [seguridad de red](../best-practices-network-security.md) en Azure.  

También puede usar la característica de acceso a las máquinas virtuales justo a tiempo desde Azure Security Center para controlar quién puede acceder de forma remota a una máquina virtual específica y durante cuánto tiempo. Vea el siguiente vídeo para obtener más información sobre cómo usar esta funcionalidad:


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Las organizaciones que no aplican la restricción de acceso de red a las máquinas virtuales accesibles desde Internet están expuestas a riesgos de seguridad, por ejemplo, a ataque por fuerza bruta mediante RDP. 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>Protección de los datos en reposo almacenados en máquinas virtuales de Azure mediante su cifrado

Hoy en día, el [cifrado de los datos en reposo](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) es un paso obligatorio en lo que respecta a la privacidad de los datos, el cumplimiento y la soberanía de los datos. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) permite que los administradores de TI cifren discos de máquina virtual IaaS con Windows y Linux. Cifrado de discos de Azure aprovecha la característica BitLocker de Windows, estándar en el sector, y la característica DM-Crypt de Linux para ofrecer cifrado de volumen para los discos de datos y del sistema operativo.

Puede aprovechar Cifrado de discos de Azure para proteger sus datos y así satisfacer los requisitos de cumplimiento y seguridad de la organización. Además, las organizaciones deberían pensar en usar cifrado como ayuda para mitigar los riesgos relacionados con el acceso no autorizado a los datos. También se recomienda cifrar las unidades antes de escribir datos confidenciales en ellas. 

Asegúrese de cifrar los volúmenes de datos de la máquina virtual para proteger los volúmenes de datos en reposo en la cuenta de almacenamiento de Azure. Proteja las claves de cifrado y los secretos mediante [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/). 

Las organizaciones que no aplican el cifrado de datos están más expuestas a sufrir problemas de integridad de datos, por ejemplo, usuarios no autorizados o malintencionados que roban datos y cuentas en peligro que acceden sin autorización a datos sin cifrar. Además de estos riesgos, las compañías que deben cumplir normativas del sector tienen que demostrar que son diligentes y que usan los controles adecuados para mejorar la seguridad de los datos.

Puede obtener más información sobre Azure Disk Encryption en el artículo [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](azure-security-disk-encryption.md).


## <a name="manage-virtual-machine-updates"></a>Administración de actualizaciones de máquinas virtuales

Azure no inserta las actualizaciones de Windows en Microsoft Azure Virtual Machines, ya que estos equipos están diseñados para que sean los usuarios quienes los administren. Es como cualquier máquina local. No obstante, se recomienda a los clientes que dejen habilitada la configuración automática de Windows Update. Otra opción consiste en implementar un servidor de [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) u otro producto de administración de actualizaciones adecuado en otra máquina virtual de Azure o local. WSUS y Windows Update mantienen actualizadas las máquinas virtuales. También es conveniente usar un producto de análisis para comprobar que todas las máquinas virtuales de IaaS estén actualizadas.

Las imágenes en existencia que proporciona Azure se actualizan periódicamente para incluir el ciclo de actualizaciones de Windows más reciente. Sin embargo, no hay ninguna garantía de que las imágenes sean actuales durante la implementación. Es posible que haya un retraso ligero (no más de unas semanas) en las versiones públicas. Buscar e instalar las actualizaciones de Windows debe ser el primer paso que debe realizar en cada implementación. Es muy importante tener esto en cuenta al implementar imágenes propias o de su propia biblioteca. Las imágenes proporcionadas como parte de la Galería de Windows Azure siempre tienen habilitadas las actualizaciones automáticas de Windows de forma predeterminada.

Las organizaciones que no aplican directivas de actualización de software están más expuestas a amenazas que aprovechan vulnerabilidades conocidas que ya se corrigieron. Además de estos riesgos, las compañías que deben cumplir normativas del sector tienen que demostrar que son diligentes y que usan los controles adecuados para mejorar la seguridad de sus cargas de trabajo ubicadas en la nube.
Es importante destacar que existen muchas similitudes entre las prácticas recomendadas de actualización de software en un centro de datos tradicional y en IaaS de Azure, por lo tanto, se recomienda evaluar las directivas de actualización de software actuales para incluir máquinas virtuales de Azure.

## <a name="manage-virtual-machine-secure-posture"></a>Administración de la posición de seguridad de máquinas virtuales

Las amenazas cibernéticas evolucionan, y para proteger las máquinas virtuales hace falta una funcionalidad de supervisión más avanzada que pueda detectar rápidamente las amenazas, desencadenar alertas y reducir los falsos positivos. La posición de segura para este tipo de carga de trabajo abarca todos los aspectos de seguridad de la máquina virtual, desde la administración de actualizaciones hasta el acceso de red seguro, aunque la supervisión activa de amenazas trate de obtener acceso no autorizado a sus recursos.

Puede usar [Azure Security Center](../security-center/security-center-intro.md) para supervisar la posición de seguridad de sus máquinas virtuales [Windows](../security-center/security-center-virtual-machine.md) y [Linux](../security-center/security-center-linux-virtual-machine.md). Puede aprovechar las siguientes funcionalidades de Azure Security Center para supervisar las máquinas virtuales:

- Configuración de seguridad del sistema operativo (SO) con las reglas de configuración recomendadas
- Seguridad del sistema y actualizaciones críticas que faltan
- Recomendaciones de protección de puntos de conexión (antimalware)
- Validación de cifrado de disco
- Evaluación y corrección de vulnerabilidades
- Detección de amenazas

Security Center puede supervisar activamente si hay amenazas, que se mostrarán en Alertas de seguridad. Las amenazas correlacionadas se agregarán a una única vista denominada "*Incidente de seguridad*". También puede ver el siguiente vídeo para comprender cómo Security Center puede ayudarlo a identificar posibles amenazas en las máquinas virtuales que se encuentran en Azure.

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Las organizaciones que no adoptan una posición de seguridad en sus máquinas virtuales no son conscientes de todos los posibles intentos que se realizan de sortear los controles de seguridad.

## <a name="monitoring-virtual-machine-performance"></a>Supervisión del rendimiento de máquinas virtuales

El abuso de los recursos también puede ser un problema si tiene procesos en una máquina virtual que consumen más recursos de lo que debería. Una máquina virtual con un problema de rendimiento puede causar la interrupción del servicio, lo que va en contra de uno de los principios de seguridad: la disponibilidad. Por esta razón, resulta imprescindible supervisar el acceso a las máquinas virtuales no solo de forma reactiva (aunque haya un problema), sino también contar con una línea base durante el horario de funcionamiento normal.

[Registros de diagnóstico de Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) puede ayudarlo a supervisar los recursos de su máquina virtual y a identificar posibles problemas que pongan en peligro su rendimiento y disponibilidad. La extensión Azure Diagnostics proporciona funcionalidades de supervisión y diagnóstico en una máquina virtual de Azure basada en Windows. Para habilitar estas funcionalidades en la máquina virtual, incluya la extensión como parte de la [plantilla ](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)de Azure Resource Manager. También puede usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) para obtener una mayor visibilidad del estado de los recursos.

Las organizaciones que no supervisen el rendimiento de la máquina virtual no podrán determinar si ciertos cambios en los patrones de rendimiento forman parte de su uso normal o si hay una operación anómala que está consumiendo más recursos de lo normal. Las anomalías podrían indicar un posible ataque procedente de un recurso externo o un proceso atacado que se está ejecutando en esta máquina virtual. 
