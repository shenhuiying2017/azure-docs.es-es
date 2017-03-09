---
title: Procedimientos de seguridad recomendados para cargas de trabajo de IaaS en Azure | Documentos de Microsoft
description: " La migración de cargas de trabajo a IaaS de Azure nos brinda la oportunidad de volver a evaluar nuestros diseños.s "
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
ms.date: 03/06/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 774feff39bee6f34b1fb292f130d8240ec070c81
ms.lasthandoff: 03/07/2017




---

# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Procedimientos de seguridad recomendados para cargas de trabajo de IaaS de Azure

Cuando empezó a pensar en mover cargas de trabajo a IaaS de Azure, es probable que se diera cuenta de que algunas de las consideraciones son conocidas. Es posible que ya tenga experiencia en la protección de entornos virtuales. El movimiento a IaaS de Azure le permite aplicar sus conocimientos de protección de entornos virtuales, además de proporcionarle un nuevo conjunto de opciones que le ayudan a proteger sus recursos.

En primer lugar, es preciso destacar que no cabe esperar trasladar los recursos locales tal cual están a Azure. Los nuevos desafíos y las nuevas opciones permiten volver a evaluar los diseños, las herramientas y los procesos existentes.




![Áreas de responsabilidad](./media/azure-security-iaas/sec-cloudstack-new.png)

>[!NOTE]
> Su responsabilidad de la seguridad se basa en el tipo de servicio en la nube. En el gráfico anterior se resume cómo se reparte la responsabilidad entre Microsoft y usted.

## <a name="best-practices"></a>Procedimientos recomendados
Aquí se van a analizar algunas de las opciones disponibles en Azure que pueden ayudarle a cumplir los requisitos de seguridad de su organización. En dicho análisis, no debemos perder de vista los distintos tipos de cargas de trabajo y cómo pueden variar sus requisitos de seguridad. Ninguno de estos procedimientos recomendados puede por sí solo proteger sus sistemas. Al igual que en otros aspectos de la seguridad, es preciso elegir las opciones adecuadas y ver si las soluciones se pueden complementar entre sí y llenar unas los huecos que dejan las otras.

### <a name="use-privileged-access-workstations-paw"></a>Uso de estaciones de trabajo con privilegios de acceso (PAW)

A menudo, las organizaciones son víctima de ciberataques porque los administradores realizan ciertas acciones en las que usan cuentas con derechos elevados. Normalmente, no lo hacen de forma malintencionada, sino porque la configuración y los procesos existentes lo permiten. La mayoría de estos usuarios conocen el riesgo desde un punto de vista conceptual, pero aun así siguen realizando acciones que saben que entrañan riesgos.

Cosas como comprobar el correo electrónico y explorar Internet parecen inofensivas, pero pueden exponer cuentas con privilegios elevado a actores malintencionados que pueden usar las actividades de exploración, en especial mensajes de correo electrónico especialmente diseñados, u otras técnicas para obtener acceso a su empresa. Se recomienda encarecidamente el uso de estaciones de trabajo de administración segura para realizar todas las tareas de administración de Azure como una manera de reducir la exposición a riesgos accidentales.

Las estaciones de trabajo con privilegios de acceso (PAW) proporcionan un sistema operativo dedicado para tareas delicadas protegido contra ataques de Internet y vectores de amenazas. La separación de estas tareas y cuentas delicadas de las estaciones de trabajo y dispositivos de uso diario proporciona una protección muy eficaz contra ataques de suplantación de identidad (phishing), las vulnerabilidades de las aplicaciones o del sistema operativo, diversos ataques de suplantación y ataques de robo de credenciales, como registro de pulsaciones de teclas, y ataques pass-the-hash y pass-the-ticket.

El enfoque de PAW es una extensión del procedimiento recomendado ya establecido de utilizar cuentas de usuario y de administración independiente para el personal administrativo. Esta práctica utiliza una cuenta administrativa asignada individualmente que es independiente de la cuenta estándar del usuario. PAW se basa en dicha práctica de separación de cuentas y proporciona una estación de trabajo de confianza para las cuentas confidenciales.

Para más información sobre las estaciones de trabajo con privilegios de acceso e instrucciones para la implementación de PAW, siga este vínculo:

- [Uso de estaciones de trabajo con privilegios de acceso](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)

### <a name="use-multifactor-authentication"></a>Uso de Multi-Factor Authentication

En el pasado, el perímetro de la red se usaba para controlar el acceso a los datos corporativos. En el mundo actual, en el que la nube y todo lo móvil tienen preponderancia, la identidad es el plano de control: se usa tanto para controlar el acceso a los servicios de IaaS desde cualquier dispositivo como para obtener visibilidad e información de dónde y cómo se usan los datos. La protección de la identidad digital de los usuarios de Azure es la piedra angular de la protección de las suscripciones contra el robo de identidad y otros tipos de delincuencia informática.

Uno de los pasos más útiles que se pueden dar para proteger una cuenta es habilitar la autenticación en dos fases. La autenticación en dos fases es una forma de autenticarse en la que se usa algo más que la mera contraseña. En la segunda fase se añade un elemento adicional de seguridad,  que ayuda a reducir el riesgo de acceso por parte de alguien que averigüe la contraseña de otra persona.

Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura gracias a una gran variedad de opciones sencillas de verificación (llamadas telefónicas, mensajes de texto o notificaciones de aplicaciones móviles), lo que permite a los usuarios elegir el mecanismo que prefieran.

La forma más fácil de usar [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) es la aplicación móvil de Microsoft Authenticator que se puede usar en dispositivos móviles con Windows, IOS y Android. Con la versión más reciente de Windows 10 y la versión local de Active Directory integrada con Azure AD, se puede usar [Windows Hello para empresas](../active-directory/active-directory-azureadjoin-passport-deployment.md) para lograr un inicio de sesión único de conexión directa a los recursos de Azure. En este caso, el dispositivo Windows 10 se utilizará como segundo factor de autenticación.


En el caso de Azure, la forma más sencilla de habilitar la autenticación en dos fases es usar Azure Multi-Factor Authentication (MFA). [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) tiene una aplicación que se puede usar en dispositivos móviles, puede funcionar a través de llamadas de teléfono, mensajes de texto o un código generado en la aplicación y puede integrarse con su directorio local.

En el caso de las cuentas que administran la suscripción de Azure se debe usar MFA, mientras que en aquellas que pueden iniciar sesión en Virtual Machines se debe usar MFA cuando sea posible. El uso de MFA en estas cuentas brinda un nivel de seguridad mucho mayor que si solo se usa una contraseña. El uso de otras formas de autenticación en dos fases puede funcionar igual de bien, pero su implementación puede ser más compleja si no están ya en producción.

La siguiente captura de pantalla muestra algunas de las opciones disponibles para la autenticación de Azure MFA.

![Opciones de MFA](./media/azure-security-iaas/mfa-options.png)

### <a name="limit-and-constrain-administrative-access"></a>Limitación y restricción del acceso administrativo

La protección de las cuentas que pueden administrar su suscripción de Azure es muy importante. El riesgo de cualquiera de esas cuentas niega eficazmente el valor de los restantes pasos que puede dar para garantizar la confidencialidad e integridad de los datos. Como ha demostrado recientemente la fuga de información confidencial de [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), los ataques internos suponen una amenaza enorme amenaza a la seguridad global de cualquier organización.

Los usuarios que tienen derechos administrativos se deberían haber evaluado siguiendo un criterio similar al siguiente:

- ¿Llevan a cabo tareas que requieran privilegios administrativos?
- ¿Con qué frecuencia se realizan las tareas?
- Motivo concreto por el que otro administrador no puede realizar las tareas en su nombre.
- Documente los restantes enfoques alternativos conocidos a conceder el privilegio y la razón por la que no se puede aceptar cada uno de ellos.

El uso de solo la administración Just-In-Time evita la existencia innecesaria de cuentas con derechos elevados en períodos en que dichos derechos no se necesitan. Las cuentas tienen derechos elevados durante un tiempo limitado, lo que permite a los administradores realizar su trabajo, pero al final del turno o cuando se completa una tarea los derechos se quitan.

[PIM](../active-directory/active-directory-privileged-identity-management-configure.md) permite administrar, supervisar y controlar el acceso en la organización. Le ayuda a conocer las acciones que realizan los usuarios de la organización y lleva la administración Just-in-Time a Azure AD mediante la introducción del concepto de administrador elegible. Dichos usuarios tienen cuentas con el potencial de que se les otorgue derechos de administrador. Estos tipos de usuarios pueden pasar por un proceso de activación y se les puede otorgar derechos de administrador durante un tiempo limitado.


### <a name="use-devtest-labs"></a>Uso de DevTest Labs

El uso de Azure en entornos de desarrollo y laboratorio permite a las organizaciones agilizar el desarrollo y las pruebas, ya que elimina los retrasos que introduce la adquisición de hardware. Por desgracia, existe el riesgo de que la falta de familiaridad con Azure o el deseo de ayudar a que se empiece a usar los antes posible puedan provocar que el administrador sea excesivamente permisivo con asignación de derechos, lo que puede exponer involuntariamente la organización a ataques internos. A algunos usuarios se les puede otorgar mucho más acceso del que deberían tener.

En Azure hemos incluido un servicio llamado [DevTest Labs](../devtest-lab/devtest-lab-overview.md). DevTest Labs usa el [
control de acceso basado en rol (RBAC) de Azure](../active-directory/role-based-access-control-what-is.md)(RBAC). RBAC le permite segregar los deberes del equipo en roles que solo conceden el nivel de acceso necesario para que los usuarios realicen su trabajo. Incluye roles predefinidos (Propietario, Usuario de laboratorio y Colaborador). Estos roles incluso se pueden usar para asignar derechos a asociados comerciales externos, lo que simplifica considerablemente la colaboración.

Dado que DevTest Labs usa RBAC, es posible crear más [roles personalizados](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs no solo simplifica la administración de permisos, también se ha diseñado para simplificar el proceso de obtención de entornos aprovisionados y para resolver otros problemas que pueden surgir en los equipos que trabajan en entornos de desarrollo y prueba. Requiere cierta preparación, pero a largo plazo facilitará cosas a su equipo.

Algunas de las características clave de Azure DevTest Labs incluyen:

- Control administrativo sobre las opciones disponibles para los usuarios. El administrador puede administrar de forma centralizada información tal como los tamaños de máquina virtual máximos permitidos, el número máximo de máquinas virtuales y cuándo se inician y apagan las máquinas virtuales
- Automatización de la creación de entornos de laboratorio
- Seguimiento de costos
- Distribución simplificada de máquinas virtuales para realizar trabajo colaborativo temporal
- Autoservicio, que permite a los usuarios aprovisionar sus laboratorios con plantillas
- Administración y limitación del consumo

![Creación de DevTestLab](./media/azure-security-iaas/devtestlabs.png)

No hay ningún costo adicional asociado con el uso de DevTest Labs. La creación de laboratorios, la configuración de directivas, las plantillas y los artefactos son totalmente gratuitos. Solo se paga por los recursos de Azure usados en los laboratorios, como las máquinas virtuales, cuentas de almacenamiento y redes virtuales.



### <a name="control-and-limit-endpoint-access"></a>Control y límite de acceso a puntos de conexión

El hospedaje de laboratorios o sistemas de producción en Azure significa que debe ser posible acceder a los sistemas desde Internet. De forma predeterminada, al puerto RDP de las máquinas virtuales Windows se puede acceder desde Internet, mientras que las máquinas virtuales Linux tienen el puerto SSH abierto. Esto significa que dar los pasos necesarios para 'limitar los puntos de conexión expuestos' es necesario para minimizar el riesgo de acceso no autorizado.

En Azure, hay tecnologías que pueden ayudarle a limitar el acceso a dichos puntos de conexión administrativos. En Azure puede usar grupos de seguridad de red ([NSG](../virtual-network/virtual-networks-nsg.md)). Si utiliza Resource Manager para la implementación, los NSG se usan para limitar el acceso desde todas las redes a solo los puntos de conexión de administración (RDP o SSH).  Cuando piense en los NSG, piense en las ACL del enrutador. Puede utilizarlas para controlar estrechamente la comunicación de red entre los distintos segmentos de las redes de Azure. Es similar a la creación de redes en redes perimetrales o en otras redes aisladas. No inspeccionan el tráfico, pero sirven de ayuda con la segmentación de la red.


En Azure puede configurar una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) desde su red local, lo que amplia eficazmente su red local hasta la nube.  Esto le proporcionaría otra oportunidad de usar los NSG, ya que también ha podido modificar los NSG para no permitir el acceso desde cualquier lugar que no sea la red local y, después, requerir que la administración se realice mediante la conexión a la red de Azure a través de VPN.

La opción de la VPN de sitio a sitio puede ser muy atractiva en aquellos casos en los que se hospedan sistemas de producción que están estrechamente integrados con sus recursos locales de Azure.

Como alternativa, la opción de [punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) se puede usar en aquellos casos en los que se desea administrar sistemas que no necesitan acceso a los recursos locales. Dichos sistemas pueden aislarse en su propia instancia de Azure Virtual Network y los administradores puede acceder a una VPN el entorno hospedado de Azure desde su estación de trabajo de administración.

>[!NOTE]
Todas las opciones de VPN le permitirían volver a configurar las ACL en los NSG para no permitir el acceso a los puntos de conexión de administración desde Internet.

Otra opción que merece la pena considerar es la implementación de una [puerta de enlace de Escritorio remoto](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md). La implementación de esta puerta de enlace de Escritorio remoto se puede usar para conectarse de forma segura a través de HTTPS a servidores de escritorio remoto al aplicar controles más granulares a dichas conexiones.

Algunas de las características a las que tendría acceso incluyen:

- Opciones de administrador para limitar las conexiones a las solicitudes de sistemas específicos.
- Autenticación de tarjeta inteligente o Azure MFA
- Control sobre los sistemas a los que alguien puede conectarse a través de la puerta de enlace.
- Control sobre el redireccionamiento de dispositivos y discos.

### <a name="use-a-key-management-solution"></a>Uso de una solución de administración de claves

Una administración de claves segura es esencial para proteger los datos en la nube. Con [Azure Key Vault](../key-vault/key-vault-whatis.md) puede almacenar de forma segura claves de cifrado y pequeños secretos como contraseñas en módulos de seguridad de hardware (HSM). Para tener mayor seguridad, puede importar o generar las claves en HSM.

Si decide hacerlo, Microsoft procesará las claves en los HSM validados de FIPS 140-2 nivel 2 (hardware y firmware). Supervise y audite el uso de claves con el registro de Azure: canalice los registros en Azure o en su SIEM para que se realicen análisis adicionales y se detecten amenazas.

Cualquiera con una suscripción a Azure puede crear y usar instancias de Key Vault. Aunque Key Vault beneficia tanto a los desarrolladores como a los administradores de seguridad, lo puede implementar y administrar el administrador de una organización responsable de los servicios de Azure.


### <a name="encrypt-virtual-disks-and-disk-storage"></a>Cifrado de discos virtuales y almacenamiento en disco

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) afronta la amenaza de robo de datos o la exposición de acceso no autorizado que se logra al mover un disco. El disco se puede conectar a otro sistema como forma de omitir otros controles de seguridad. Disk Encryption usa [BitLocker](https://technet.microsoft.com/library/hh831713 ) en Windows y DM-Crypt en Linux para cifrar tanto el sistema operativo como las unidades de datos. Azure Disk Encryption se integra con Key Vault para control y administrar las claves de cifrado y está disponible tanto para las máquinas virtuales Estándar como para las máquinas virtuales con Premium Storage.

Para más información, examine el artículo [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](azure-security-disk-encryption.md).

[Cifrado del servicio Azure Storage](../storage/storage-service-encryption.md) protege los datos en reposo. Se habilita en el nivel de la cuenta de almacenamiento y cifra los datos a medida que se escriben en nuestros centros de datos y se descifran automáticamente cuando se accede a ellos. Admite los siguientes escenarios:

- El cifrado de blobs en bloques, blobs en anexos y blobs en páginas.
- El cifrado de plantillas y VHD archivados migrados desde instalaciones locales a Azure.
- El cifrado de discos de datos y OS subyacentes para las VM de IaaS creadas con los VHD.

Antes de continuar con el cifrado de Azure Storage debe conocer dos limitaciones importantes:

- No está disponible en las cuentas de almacenamiento clásico.
- Solo cifra los datos escritos después de que el cifrado esté habilitado.

### <a name="use-a-centralized-security-management-system"></a>Utilice un sistema de administración de seguridad centralizada

Es preciso supervisar la aplicación de revisiones, la configuración, los eventos y las actividades de los servidores que se pueden considerar cuestiones de seguridad. Para abordar estas inquietudes puede usar [Security Center](https://azure.microsoft.com/services/security-center/) y [Seguridad y cumplimiento en Operations Management Suite](https://azure.microsoft.com/services/security-center/). Estas dos opciones van más allá de la configuración en el sistema operativo y también proporcionan supervisión de la configuración de la infraestructura subyacente como la configuración de la red y el uso dispositivos virtuales.

### <a name="operating-system-management-best-practices"></a>Procedimientos recomendados de administración de un sistema operativo

En una implementación de IaaS usted es responsable de la administración de los sistemas que implementa, tal como ocurre con los restantes servidores o estaciones de trabajo del entorno. Esto significa que la aplicación de revisiones, el sistema de protección, las asignaciones de derechos y cualquier otra actividad relacionada con el mantenimiento del sistema es responsabilidad suya.  Para los sistemas que se integran estrechamente con los recursos locales se pueden utilizar las mismas herramientas y procedimientos que se usan en local para los antivirus, el anti-malware, la aplicación de revisiones y la copia de seguridad.

**Sistema de protección** Todas las máquinas virtuales de IaaS de Azure debe protegerse, con el fin de que solo expongan los puntos de conexión de servicios que son necesarios para las aplicaciones que estén instaladas. Para las máquinas virtuales con Windows, siga las recomendaciones que publica Microsoft como base de referencia para la solución Security Compliance Manager.

[Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) (recientemente se ha lanzado la versión 4.0) Es una herramienta gratuita que permite configurar y administrar rápidamente los equipos de escritorio, el centro de datos tradicional, y la nube privada y pública mediante una directiva de grupo y System Center Configuration Manager.

SCM proporciona directivas listas para su implementación y paquetes de configuración de DCM que se prueban. Esta base de referencia se basa en las recomendaciones de la [guía de seguridad de Microsoft](https://technet.microsoft.com/en-us/library/cc184906.aspx) y en los procedimientos recomendados del sector, lo que le permite administrar desfases de configuración, afrontar los requisitos de cumplimiento y reducir las amenazas a la seguridad.

SCM se puede usar para importar la configuración actual de los equipos mediante dos métodos diferentes: en primer lugar, se pueden importar las directivas de grupo basadas en Active Directory y, en segundo lugar, se puede importar la configuración de una máquina de referencia de "copia maestra" mediante el uso de la [herramienta LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) para realizar una copia de seguridad de la directiva de grupo local que, posteriormente, se puede importar en SCM.

Compare sus estándares con los procedimientos recomendados del sector, personalícelos y cree nuevas directivas y paquetes de configuración de DCM.
Se han publicado las bases de referencia de todos los sistemas operativos compatibles, incluida Actualización de aniversario de Windows 10 y Windows Server 2016.


**Instalación y administración de antimalware**

Para los entornos hospedados de forma independiente del entorno de producción, hay una extensión de antimalware que se puede usar para proteger las máquinas virtuales y los servicios en la nube, y que se integra con [Azure Security Center](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) incluye características como la protección en tiempo real, los análisis programados, la corrección de malware, las actualizaciones de firmas, las actualizaciones del motor, los ejemplos de informes, la colección de eventos de exclusión y la [compatibilidad con PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Azure Antimalware](./media/azure-security-iaas/azantimalware.png)

**Instalación de las últimas actualizaciones de seguridad** Algunas de las primeras cargas de trabajo que vemos que los clientes mueven a Azure son los laboratorios y los sistemas con conexión con el exterior. Si hospeda máquinas virtuales en Azure que hospedan aplicaciones o servicios a los que debe de poder accederse desde Internet, es preciso que esté atento a la aplicación de revisiones. Recuerde que esto es algo más que la aplicación de revisiones al sistema operativo. Las vulnerabilidades de aplicaciones de otros fabricantes a las que no se han aplicado revisiones también pueden provocar problemas que podrían haberse evitado fácilmente si hubiera una buena administración de revisiones vigente.

**Implementación y comprobación de una solución de copia de seguridad**

Al igual que las actualizaciones de seguridad, la copia de seguridad se debe realizar de la misma manera que cualquier otra operación, al menos en los sistemas que formen parte del entorno de producción que se extiende a la nube. Los sistemas de prueba y desarrollo deben seguir estrategias de copia de seguridad que puedan proporcionar funcionalidades de restauración similares a los que los usuarios están acostumbrado en función de su experiencia en entornos locales.

Las cargas de trabajo de producción movidas a Azure deben integrarse con las soluciones de copia de seguridad existentes siempre que sea posible, o bien se puede usar [Azure Backup](../backup/backup-azure-arm-vms.md) como ayuda para cumplir los requisitos de copia de seguridad.


### <a name="monitor"></a>Supervisión

[Security Center](../security-center/security-center-intro.md) proporciona una evaluación continua del estado de seguridad de los recursos de Azure para identificar posibles vulnerabilidades de la seguridad. Una lista de recomendaciones le guiará por el proceso de configuración de los controles necesarios.

Algunos ejemplos son:

- Aprovisionamiento de antimalware para ayudar a identificar y eliminar el software malintencionado.
- Configuración de reglas y grupos de seguridad de red para controlar el tráfico a las máquinas virtuales.
- Aprovisionamiento de firewalls de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web.
- Implementación de actualizaciones del sistema que faltan.
- Resolución de las configuraciones de sistema operativo que no coinciden con las líneas base recomendadas.

La imagen siguiente muestra varias de las opciones que se pueden habilitar en Security Center.

![Directivas de Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

 [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) es una solución de administración de TI basada en la nube de Microsoft que ayuda a administrar y proteger infraestructuras locales y en la nube. Dado que OMS se implementa como un servicio basado en la nube, se puede implementar rápidamente y con una inversión mínima en recursos de infraestructura.

Las características nuevas se ofrecen automáticamente, lo que ahorra los costos de mantenimiento continuo y actualización. También se integra con System Center Operations Manager.  OMS tiene distintos componentes que le ayudan a administrar mejor las cargas de trabajo de Azure, entre los que se incluye un módulo de [seguridad y cumplimiento](../operations-management-suite/oms-security-getting-started.md).

Las características de seguridad y cumplimiento de OMS permiten visualizar información acerca de los recursos organizada en cuatro categorías principales:

- Dominios de seguridad: en esta área podrá explorar con mayor profundidad los registros de seguridad con el paso del tiempo, acceder a la evaluación del malware, actualizar la evaluación, seguridad de la red, la información de identidad y acceso, equipos con eventos de seguridad y acceder rápidamente al panel de Azure Security Center.
- Problemas importantes: esta opción permitirá identificar rápidamente la cantidad de problemas activos y su gravedad.
- Detecciones (vista previa): permite identificar patrones de ataque mediante la visualización de alertas de seguridad a medida que se producen contra sus recursos.
- Inteligencia de amenazas: permite identificar patrones de ataque mediante la visualización del número total de servidores con tráfico de IP malintencionadas salientes, el tipo de amenaza malintencionada y un mapa que muestra de dónde proceden dichas direcciones IP.
- Consultas comunes de seguridad: esta opción proporciona una lista de las consultas de seguridad más comunes que puede utilizar para supervisar el entorno. Al hacer clic en cualquiera de ellas, se abrirá la hoja Buscar con los resultados de la consulta

La captura de pantalla siguiente muestra un ejemplo del tipo de información que puede mostrar OMS.

![Líneas de base de seguridad de OMS](./media/azure-security-iaas/oms-security-baseline.png)



## <a name="next-steps"></a>Pasos siguientes


* [Blog del equipo de seguridad de Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Centro de respuesta de seguridad de Microsoft](https://technet.microsoft.com/library/dn440717.aspx)
* [Patrones y procedimientos recomendados de seguridad en Azure](security-best-practices-and-patterns.md)

