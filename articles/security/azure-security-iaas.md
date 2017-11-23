---
title: Procedimientos de seguridad recomendados para cargas de trabajo de IaaS en Azure | Documentos de Microsoft
description: " La migración de cargas de trabajo a IaaS de Azure nos brinda la oportunidad de volver a evaluar nuestros diseños "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 376a3e47e5099aa4d74732e0b6ed14ed9af14091
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Procedimientos de seguridad recomendados para cargas de trabajo de IaaS de Azure

Cuando empezó a pensar en mover cargas de trabajo a la infraestructura como servicio (IaaS) de Azure, es probable que se diera cuenta de que algunas de las consideraciones le eran familiares. Puede que ya tenga experiencia en la protección de entornos virtuales. El movimiento a IaaS de Azure le permite aplicar sus conocimientos de protección de entornos virtuales, además de usar un nuevo conjunto de opciones que le ayudan a proteger sus recursos.

Es preciso destacar que no cabe esperar trasladar los recursos locales tal cual están a Azure. Los nuevos desafíos y las nuevas opciones permiten volver a evaluar los diseños, las herramientas y los procesos existentes.

Su responsabilidad de la seguridad se basa en el tipo de servicio en la nube. En el siguiente gráfico se resume cómo se reparte la responsabilidad entre Microsoft y usted.


![Áreas de responsabilidad](./media/azure-security-iaas/sec-cloudstack-new.png)


Se analizarán algunas de las opciones disponibles en Azure que pueden ayudarle a cumplir los requisitos de seguridad de su organización. Tenga en cuenta que los requisitos de seguridad pueden variar para diferentes tipos de cargas de trabajo. Ninguno de estos procedimientos recomendados puede por sí solo proteger sus sistemas. Al igual que en otros aspectos de la seguridad, es preciso elegir las opciones adecuadas y ver si las soluciones se pueden complementar entre sí y suplir las deficiencias.

## <a name="use-privileged-access-workstations"></a>Uso de estaciones de trabajo con privilegios de acceso

A menudo, las organizaciones son víctima de ciberataques porque los administradores realizan ciertas acciones en las que usan cuentas con derechos elevados. Normalmente, no lo hacen de forma malintencionada, sino porque la configuración y los procesos existentes lo permiten. La mayoría de estos usuarios entiende el riesgo de estas acciones desde un punto de vista conceptual, pero no por eso dejan de hacerlo.

Cosas como comprobar el correo electrónico y explorar Internet parecen muy inofensivas. Pero pueden exponer las cuentas con privilegios elevados y ponerse en peligro por parte de actores malintencionados. Se puede examinar las actividades, usar mensajes de correo electrónico diseñados especialmente o poner en práctica otras técnicas para obtener acceso a su empresa. Se recomienda encarecidamente el uso de estaciones de trabajo de administración seguras (SAW) para realizar todas las tareas de administración de Azure. Constituye una manera de reducir la exposición a riesgos accidentales.

Las estaciones de trabajo con privilegios de acceso (PAW) proporcionan un sistema operativo dedicado para tareas delicadas, que está protegido contra ataques de Internet y vectores de amenazas. La separación de estas tareas especiales y las cuentas de los dispositivos y las estaciones de trabajo de uso diario proporciona una protección segura. Esta separación limita el impacto de los ataques de suplantación de identidad, las vulnerabilidades del sistema operativo y de las aplicaciones, diversos ataques de suplantación y ataques de robo de credenciales. (registro de pulsaciones de teclas, Pass-the-Hash y Pass-the-Ticket)

El enfoque PAW es una extensión de la práctica recomendada y establecida de usar una cuenta administrativa asignada individualmente. La cuenta administrativa es independiente de una cuenta de usuario estándar. Un PAW proporciona una estación de trabajo de confianza para esas cuentas confidenciales.

Para más información y para obtener una guía de implementación, consulte [Estaciones de trabajo con privilegios de acceso](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Uso de Multi-factor Authentication

En el pasado, el perímetro de la red se usaba para controlar el acceso a los datos corporativos. En un mundo en el que la prioridad es los dispositivos móviles y la nube, la identidad es el plano de control: se usa para controlar el acceso a servicios IaaS desde cualquier dispositivo. También se usa para obtener visibilidad y comprender dónde y cómo se utilizan sus datos. La protección de la identidad digital de los usuarios de Azure es la piedra angular de la protección de las suscripciones contra el robo de identidad y otros tipos de delincuencia informática.

Uno de los pasos más útiles que se pueden dar para proteger una cuenta es habilitar la autenticación en dos fases. La autenticación en dos fases es una manera de autenticar mediante algo además de una contraseña. Esto ayuda a reducir el riesgo de acceso por parte de alguien que averigüe la contraseña de otra persona.

[Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de opciones sencillas de comprobación (llamadas telefónicas, mensajes de texto o notificaciones de aplicaciones móviles). Los usuarios eligen el método que prefieran.

La forma más fácil de usar Multi-Factor Authentication es la aplicación móvil de Microsoft Authenticator que se puede utilizar en dispositivos móviles que ejecutan Windows, iOS y Android. Con la versión más reciente de Windows 10 y la integración de la versión local de Active Directory con Azure Active Directory (Azure AD), se puede usar [Windows Hello para empresas](../active-directory/active-directory-azureadjoin-passport-deployment.md) para iniciar sesión directamente en los recursos de Azure. En este caso, el dispositivo Windows 10 se usa como segundo factor de autenticación.

En el caso de cuentas que administran la suscripción de Azure y cuentas que pueden iniciar sesión en máquinas virtuales, el uso de Multi-Factor Authentication le proporciona un nivel de seguridad mucho mayor que la utilización de solo una contraseña. Otras formas de autenticación en dos fases podrían funcionar perfectamente, pero su implementación puede ser complicada si no están ya en producción.

La captura de pantalla siguiente muestra algunas de las opciones disponibles para Azure Multi-Factor Authentication:

![Opciones de Multi-Factor Authentication](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Limitación y restricción del acceso administrativo

La protección de las cuentas que pueden administrar su suscripción de Azure es muy importante. Poner en riesgo cualquiera de esas cuentas niega el valor de los restantes pasos que puede dar para garantizar la confidencialidad y la integridad de los datos. Como ha demostrado recientemente [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), los ataques internos suponen una amenaza enorme para la seguridad global de cualquier organización.

Evalúe los derechos administrativos de los individuos siguiendo criterios similares a los siguientes:

- ¿Llevan a cabo tareas que requieran privilegios administrativos?
- ¿Con qué frecuencia se realizan las tareas?
- ¿Existe un motivo concreto por el que otro administrador no puede realizar las tareas en su nombre?

Documente los restantes enfoques alternativos conocidos a conceder el privilegio y la razón por la que no se puede aceptar cada uno de ellos.

El uso de solo la administración Just-In-Time evita la existencia innecesaria de cuentas con derechos elevados en períodos en que dichos derechos no se necesitan. Las cuentas tienen derechos elevados durante un tiempo limitado para que los administradores puedan hacer su trabajo. Luego, esos derechos se quitan al final de un turno o cuando se termina una tarea.

Puede usar [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) para administrar, supervisar y controlar el acceso en su organización. Esta aplicación le ayuda a estar al tanto de las acciones que realizan los usuarios de su organización. También ofrece administración Just-In-Time a Azure AD al introducir el concepto de administradores aptos. Dichos usuarios tienen cuentas con el potencial de que se les otorgue derechos de administrador. Estos tipos de usuarios pueden pasar por un proceso de activación y se les puede otorgar derechos de administrador durante un tiempo limitado.


## <a name="use-devtest-labs"></a>Uso de DevTest Labs

El uso de Azure en entornos de desarrollo y laboratorio permite a las organizaciones agilizar el desarrollo y las pruebas, ya que elimina los retrasos que introduce la adquisición de hardware. Por desgracia, existe el riesgo de que la falta de familiaridad con Azure o el deseo de ayudar a que se empiece a usar los antes posible puedan provocar que el administrador sea excesivamente permisivo con la asignación de derechos, Este riesgo puede exponer involuntariamente la organización a ataques internos. A algunos usuarios se les puede otorgar mucho más acceso del que deberían tener.

El servicio [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) usa el [control de acceso basado en rol de Azure](../active-directory/role-based-access-control-what-is.md) (RBAC). RBAC le permite segregar los deberes del equipo en roles que solo conceden el nivel de acceso necesario para que los usuarios realicen su trabajo. Incluye roles predefinidos (propietario, usuario de laboratorio y colaborador). Estos roles se pueden usar incluso para asignar derechos a asociados externos, lo que simplifica considerablemente la colaboración.

Como DevTest Labs usa RBAC, es posible crear [roles personalizados](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md) adicionales. DevTest Labs no solo simplifica la administración de permisos, simplifica el proceso de aprovisionar los entornos. También le ayuda resolver otros desafíos típicos de los equipos que trabajan en entornos de desarrollo y pruebas. Requiere cierta preparación, pero a largo plazo facilitará las cosas a su equipo.

Algunas de las características clave de Azure DevTest Labs incluyen:

- Control administrativo sobre las opciones disponibles para los usuarios. El administrador puede administrar de forma centralizada cosas como los tamaños de máquina virtual máximos permitidos, el número máximo de máquinas virtuales y cuándo se inician y apagan las máquinas virtuales.
- Automatización de la creación de entornos de laboratorio.
- Seguimiento de costos.
- Distribución simplificada de máquinas virtuales para realizar trabajo colaborativo temporal.
- Autoservicio, que permite a los usuarios aprovisionar sus laboratorios mediante plantillas.
- Administración y limitación del consumo.

![Uso de DevTest Labs para crear un laboratorio](./media/azure-security-iaas/devtestlabs.png)

No hay ningún costo adicional asociado con el uso de DevTest Labs. La creación de laboratorios, directivas, plantillas y artefactos es gratuita. Solo se paga por los recursos de Azure usados en los laboratorios, como máquinas virtuales, cuentas de almacenamiento y redes virtuales.



## <a name="control-and-limit-endpoint-access"></a>Control y límite de acceso a puntos de conexión

El hospedaje de laboratorios o sistemas de producción en Azure significa que debe ser posible acceder a los sistemas desde Internet. De forma predeterminada, se puede acceder al puerto RDP de las máquinas virtuales Windows desde Internet, mientras que las máquinas virtuales Linux tienen el puerto SSH abierto. Es necesario realizar algunos pasos para limitar los puntos de conexión expuestos para minimizar el riesgo de acceso no autorizado.

En Azure, existen tecnologías que pueden ayudarle a limitar el acceso a dichos puntos de conexión administrativos. Por ejemplo, puede usar grupos de seguridad de red ([NSG](../virtual-network/virtual-networks-nsg.md)). Si usa Azure Resource Manager para la implementación, los NSG limitan el acceso desde todas las redes a solo los puntos de conexión de administración (RDP o SSH). Cuando piense en los NSG, piense en las ACL del enrutador. Puede utilizarlas para controlar estrechamente la comunicación de red entre los distintos segmentos de las redes de Azure. Esto es similar a la creación de redes en redes perimetrales u otras redes aisladas. No inspeccionan el tráfico, sino que ayudan con la segmentación de la red.


En Azure, puede configurar una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) desde la red local. Una VPN de sitio a sitio extiende la red local a la nube. Esto le proporciona otra oportunidad de usar los NSG, ya que también puede modificarlos para no permitir el acceso desde ningún sitio que no sea la red local. Luego, puede exigir que la administración se realice conectando primero con la red de Azure a través de VPN.

La opción de la VPN de sitio a sitio puede ser muy atractiva en aquellos casos en los que se hospedan sistemas de producción que están estrechamente integrados con sus recursos locales de Azure.

Como alternativa, la opción de [punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) se puede usar en aquellos casos en los que se desea administrar sistemas que no necesitan acceso a los recursos locales. Esos sistemas pueden aislarse en su propia red virtual de Azure. Los administradores pueden conectarse a la VPN del entorno hospedado de Azure desde su estación de trabajo administrativa.

>[!NOTE]
>Puede usar cualquiera de las opciones de VPN para volver a configurar las ACL en los NSG de forma que no permitan el acceso a los puntos de conexión de administración desde Internet.

Otra opción que merece la pena considerar es la implementación de una [puerta de enlace de Escritorio remoto](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md). Puede usar esta implementación para conectarse de forma segura a los servidores de Escritorio remoto a través de HTTPS y aplicar al mismo tiempo controles más detallados a esas conexiones.

Algunas de las características a las que tendría acceso incluyen:

- Opciones de administrador para limitar las conexiones a las solicitudes de sistemas específicos.
- Autenticación mediante tarjeta inteligente o Azure Multi-Factor Authentication.
- Control sobre los sistemas a los que alguien puede conectarse a través de la puerta de enlace.
- Control sobre el redireccionamiento de dispositivos y discos.

## <a name="use-a-key-management-solution"></a>Uso de una solución de administración de claves

Una administración de claves segura es esencial para proteger los datos en la nube. Con [Azure Key Vault](../key-vault/key-vault-whatis.md) puede almacenar de forma segura claves de cifrado y pequeños secretos como contraseñas en módulos de seguridad de hardware (HSM). Para tener mayor seguridad, puede importar o generar las claves en HSM.

Microsoft procesa las claves en los HSM validados de FIPS 140-2 nivel 2 (hardware y firmware). Supervise y audite el uso de claves con registros de Azure: registros de canalizaciones en Azure o el sistema de administración de eventos e información de seguridad (SIEM) para análisis adicionales y la detección de amenazas.

Cualquiera con una suscripción a Azure puede crear y usar instancias de Key Vault. Aunque Key Vault beneficia a los desarrolladores y a los administradores de seguridad, un administrador que sea responsable de los servicios de Azure en una organización lo puede implementar y administrar.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Cifrado de discos virtuales y almacenamiento en disco

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) afronta la amenaza de robo de datos o la exposición de acceso no autorizado que se logra al mover un disco. El disco se puede conectar a otro sistema como forma de omitir otros controles de seguridad. Disk Encryption usa [BitLocker](https://technet.microsoft.com/library/hh831713) en Windows y DM-Crypt en Linux para cifrar tanto el sistema operativo como las unidades de datos. Azure Disk Encryption se integra con Key Vault para controlar y administrar las claves de cifrado. Está disponible para máquinas virtuales estándar y máquinas virtuales con Premium Storage.

Para más información, consulte [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](azure-security-disk-encryption.md).

El [cifrado del servicio Azure Storage](../storage/common/storage-service-encryption.md) ayuda a proteger los datos en reposo. Está habilitado en el nivel de la cuenta de almacenamiento. Cifra los datos tal y como se escriben en nuestros centros de datos y los descifra automáticamente cuando accede a ellos. Admite los siguientes escenarios:

- Cifrado de blobs en bloques, blobs en anexos y blobs en páginas
- Cifrado de plantillas y VHD archivados migrados desde instalaciones locales a Azure
- Cifrado de discos de datos y SO subyacente para las máquinas virtuales de IaaS creadas con los VHD

Antes de continuar con el cifrado de Azure Storage, debe saber que existen dos limitaciones:

- No está disponible en las cuentas de almacenamiento clásico.
- Solo cifra los datos escritos después de que el cifrado esté habilitado.

## <a name="use-a-centralized-security-management-system"></a>Usa un sistema de administración de seguridad centralizado.

Es preciso supervisar la aplicación de revisiones, la configuración, los eventos y las actividades de los servidores que se pueden considerar cuestiones de seguridad. Para abordar estas inquietudes, puede usar [Security Center](https://azure.microsoft.com/services/security-center/) y [Seguridad y cumplimiento de Operations Management Suite](https://azure.microsoft.com/services/security-center/). Estas dos opciones van más allá de la configuración del sistema operativo. También proporcionan la supervisión de la configuración de la infraestructura subyacente, como la configuración de red y el uso de la aplicación virtual.

## <a name="manage-operating-systems"></a>Administración de sistemas operativos

En una implementación de IaaS, usted es responsable de la administración de los sistemas que implementa, tal como ocurre con los restantes servidores o estaciones de trabajo del entorno. La aplicación de revisiones, el sistema de protección, las asignaciones de derechos y cualquier otra actividad relacionada con el mantenimiento del sistema es responsabilidad suya. En sistemas que se integran estrechamente con los recursos locales, se pueden usar las mismas herramientas y procedimientos que se utilizan en local para los antivirus, el antimalware, la aplicación de revisiones y la copia de seguridad.

### <a name="harden-systems"></a>Sistemas de protección
Todas las máquinas virtuales de IaaS de Azure deben protegerse, con el fin de que solo expongan los puntos de conexión de servicio que son necesarios para las aplicaciones que están instaladas. Para máquinas virtuales Windows, siga las recomendaciones que publica Microsoft como línea de base para la solución [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx).

Security Compliance Manager es una herramienta gratuita. Puede usarla para configurar y administrar sus equipos de escritorio, centros de datos tradicionales y la nube privada y pública mediante la directiva de grupo y System Center Configuration Manager rápidamente.

Security Compliance Manager proporciona directivas listas para implementar y paquetes de Administración de configuración deseada de demostrada eficacia. Estas líneas de base se basan en las recomendaciones de la [Guía de seguridad de Microsoft](https://technet.microsoft.com/en-us/library/cc184906.aspx) y en los procedimientos recomendados del sector. Le ayudan a administrar el desfase de la configuración, a solucionar los requisitos de cumplimiento y a reducir las amenazas de seguridad.

Puede usar Security Compliance Manager para importar la configuración actual de sus equipos mediante dos métodos diferentes. En primer lugar, puede importar directivas de grupo basadas en Active Directory. En segundo lugar, puede importar la configuración de una máquina de referencia "copia maestra" mediante la [herramienta LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) para hacer copia de seguridad de la directiva de grupo local. A continuación, puede importar la directiva de grupo local en Security Compliance Manager.

Compare sus estándares con los procedimientos recomendados del sector, personalícelos y cree nuevas directivas y paquetes de configuración de Administración de configuración deseada. Se han publicado las bases de referencia de todos los sistemas operativos compatibles, incluida Actualización de aniversario de Windows 10 y Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Instalación y administración de antimalware

En entornos hospedados por separado del entorno de producción, puede usar una extensión de antimalware para ayudar a proteger las máquinas virtuales y los servicios en la nube. Dicha extensión se integra con [Azure Security Center](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) incluye características como la protección en tiempo real, los análisis programados, la corrección de malware, las actualizaciones de firmas, las actualizaciones del motor, los ejemplos de informes, la colección de eventos de exclusión y la [compatibilidad con PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Azure Antimalware](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Instalación de las últimas actualizaciones de seguridad
Algunas de las primeras cargas de trabajo que los clientes mueven a Azure son laboratorios y sistemas orientados externamente. Si sus máquinas virtuales hospedadas en Azure hospedan aplicaciones o servicios que deben estar accesibles desde Internet, esté alerta a la aplicación de revisiones. Aplique revisiones no solo del sistema operativo. Las vulnerabilidades de aplicaciones de terceros a las que no se han aplicado revisiones también pueden provocar problemas que podrían haberse evitado si hubiera una buena administración de revisiones vigente.

### <a name="deploy-and-test-a-backup-solution"></a>Implementación y comprobación de una solución de copia de seguridad

Al igual que las actualizaciones de seguridad, una copia de seguridad se debe realizar de la misma manera que cualquier otra operación, al menos en los sistemas que formen parte del entorno de producción que se extiende a la nube. Los sistemas de prueba y desarrollo deben seguir estrategias de copia de seguridad que proporcionen funcionalidades de restauración que sean similares a las que los usuarios están acostumbrado en función de su experiencia en entornos locales.

Las cargas de trabajo de producción movidas a Azure deben integrarse con las soluciones de copia de seguridad existentes cuando sea posible. O bien, puede usar [Azure Backup](../backup/backup-azure-arm-vms.md) para ayudarle a enfrentar los requisitos de copia de seguridad.


## <a name="monitor"></a>Supervisión

[Security Center](../security-center/security-center-intro.md) proporciona una evaluación continua del estado de seguridad de los recursos de Azure para identificar posibles vulnerabilidades de la seguridad. Una lista de recomendaciones le guiará por el proceso de configuración de los controles necesarios.

Algunos ejemplos son:

- Aprovisionamiento de antimalware para ayudar a identificar y eliminar el software malintencionado.
- Configuración de reglas y grupos de seguridad de red para controlar el tráfico a las máquinas virtuales.
- Aprovisionamiento de firewalls de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web.
- Implementación de actualizaciones del sistema que faltan.
- Resolución de las configuraciones de sistema operativo que no coinciden con las líneas base recomendadas.

La imagen siguiente muestra algunas de las opciones que puede habilitar en Security Center.

![Directivas de Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) es una solución de administración de TI basada en la nube de Microsoft que ayuda a administrar y proteger infraestructuras locales y en la nube. Dado que Operations Management Suite se implementa como un servicio basado en la nube, se puede implementar rápidamente y con una inversión mínima en recursos de infraestructura.

Las características nuevas se ofrecen automáticamente, lo que supone un ahorro en costos de mantenimiento continuo y actualización. Operations Management Suite también se integra con System Center Operations Manager. OMS tiene distintos componentes que le ayudan a administrar mejor las cargas de trabajo de Azure, entre los que se incluye un módulo de [seguridad y cumplimiento](../operations-management-suite/oms-security-getting-started.md).

Puede usar las características de seguridad y cumplimiento de Operations Management Suite para ver información sobre los recursos. La información está organizada en cuatro categorías principales:

- **Dominios de seguridad**: explore detalladamente los registros de seguridad con el tiempo. Acceda a evaluaciones de malware, evaluación de actualizaciones, información de seguridad de red, información de identidad y acceso y equipos con eventos de seguridad. Aproveche las ventajas del acceso rápido al panel de Azure Security Center.
- **Problemas importantes**: identifique rápidamente la cantidad de problemas activos y su gravedad.
- **Detecciones (versión preliminar)**: identifique patrones de ataque mediante la visualización de alertas de seguridad a medida que se producen contra sus recursos.
- **Inteligencia frente a amenazas**: identifique patrones de ataque mediante la visualización del número total de servidores con tráfico IP malintencionado saliente, el tipo de amenaza malintencionada y un mapa que muestra de dónde proceden dichas direcciones IP.
- **Consultas comunes de seguridad**: vea una lista de las consultas de seguridad más comunes que puede usar para supervisar el entorno. Al hacer clic en cualquiera de ellas, se abrirá la hoja **Buscar** con los resultados de la consulta.

En la siguiente captura de pantalla se muestra un ejemplo de la información que se puede visualizar en Operations Management Suite.

![Líneas de base de seguridad de Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Pasos siguientes

* [Blog del equipo de seguridad de Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Patrones y procedimientos recomendados de seguridad en Azure](security-best-practices-and-patterns.md)
