<properties
   pageTitle="Información general de seguridad de Máquinas virtuales de Azure | Microsoft Azure"
   description="Máquinas virtuales de Azure le ofrece la flexibilidad de la virtualización sin necesidad de adquirir y mantener el hardware físico que ejecuta la máquina virtual. Este artículo ofrece una visión general de las principales características de seguridad de Azure que se pueden usar con Máquinas virtuales de Azure."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/20/2016"
   ms.author="terrylan"/>

# Información general de seguridad de Máquinas virtuales de Azure

Máquinas virtuales de Azure permite implementar una amplia gama de soluciones informáticas con agilidad. Gracias a la compatibilidad con Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP y Servicios de BizTalk de Azure, puede implementar cualquier carga de trabajo y cualquier idioma en casi cualquier sistema operativo.

Una máquina virtual de Azure le ofrece la flexibilidad de la virtualización sin necesidad de adquirir y mantener el hardware físico que ejecuta la máquina virtual. Puede compilar e implementar aplicaciones con la tranquilidad de saber que sus datos están protegidos en nuestros centros de datos de alta seguridad.

Con Azure, puede crear soluciones compatibles con seguridad mejorada que:

- Protegen sus máquinas virtuales de virus y malware.
- Protegen con cifrado su información confidencial.
- Protegen el tráfico de red.
- Identifican y detectan amenazas.
- Satisfacen los requisitos de cumplimiento.

El objetivo de este artículo es proporcionar una visión general de las principales características de seguridad de Azure que se pueden usar con máquinas virtuales. Además, se incluyen vínculos a artículos que ofrecen detalles de cada una de estas características para que pueda tener más información al respecto.

En este artículo se tratan las principales funcionalidades de seguridad de máquinas virtuales de Azure:

- Antimalware
- Módulo de seguridad de hardware
- Cifrado de discos de máquinas virtuales
- Copia de seguridad de máquina virtual
- Azure Site Recovery
- Redes virtuales
- Informes y administración de directivas de seguridad
- Cumplimiento normativo

## Antimalware

Con Azure, puede utilizar software antimalware de los principales proveedores de seguridad, como Microsoft, Symantec, Trend Micro, McAfee y Kaspersky, para proteger las máquinas virtuales de archivos malintencionados, adware y otras amenazas. Consulte más abajo la sección Más información para ver artículos sobre soluciones de asociados.

Microsoft Antimalware para servicios en la nube y máquinas virtuales de Azure es una funcionalidad de protección en tiempo real que permite identificar y eliminar virus, spyware y otro software malintencionado. Microsoft Antimalware activa alertas configurables cuando software no deseado o malintencionado intenta instalarse o ejecutarse en los sistemas de Azure.

Microsoft Antimalware es una solución de un único agente dirigida a entornos de aplicaciones e inquilinos, concebida para ejecutarse en segundo plano sin intervención humana. Puede implementar la protección en función de las necesidades de sus cargas de trabajo de aplicaciones, con configuración de protección básica o personalizada avanzada que incluye supervisión antimalware.

Al implementar y habilitar Microsoft Antimalware, están disponibles las siguientes características principales:

- Protección en tiempo real: supervisa la actividad en Servicios en la nube y Máquinas virtuales para detectar y bloquear la ejecución de malware.
- Análisis programado: realiza periódicamente exámenes dirigidos para detectar malware, lo que incluye programas que se ejecutan activamente.
- Corrección de malware: actúa automáticamente sobre el malware detectado y elimina o pone en cuarentena los archivos malintencionados y limpia las entradas del Registro malintencionadas.
- Actualizaciones de firmas: instala automáticamente las últimas firmas de protección (definiciones de virus) para garantizar que la protección está actualizada con una frecuencia determinada previamente.
- Actualizaciones del motor antimalware: actualiza automáticamente el motor de Microsoft Antimalware.
- Actualizaciones de la plataforma antimalware: actualiza automáticamente la plataforma de Microsoft Antimalware.
- Protección activa: envía a Azure informes de metadatos de telemetría sobre las amenazas detectadas y los recursos sospechosos para garantizar una respuesta rápida a las amenazas en constante evolución, y para permitir la entrega sincrónica de firmas en tiempo real a través de Microsoft Active Protection System (MAPS).
- Informes de ejemplos: proporciona informes de ejemplos al servicio Microsoft Antimalware que ayudan a mejorar el servicio y permiten la solución de problemas.
- Exclusiones: permite a los administradores de aplicaciones y servicios configurar determinados archivos, procesos y unidades para que se excluyan de la protección y el examen por motivos de rendimiento o de otro tipo.
- Recopilación de eventos antimalware: registra el estado del servicio antimalware, las actividades sospechosas y las acciones de corrección adoptadas en el registro de eventos del sistema operativo y los recopila en la cuenta de Almacenamiento de Azure del cliente.

Más información: para más información sobre software antimalware para proteger sus máquinas virtuales, Microsoft Antimalware, así como software antimalware de otros proveedores de seguridad principales, como Symantec y Trend Micro, consulte:

- [Microsoft Antimalware para Servicios en la nube y Máquinas virtuales de Azure](../azure-security-antimalware.md)
- [Implementación de soluciones antimalware en máquinas virtuales de Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Instalación y configuración de Endpoint Protection en una máquina virtual de Azure](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [New Antimalware Options for Protecting Azure Virtual Machines – McAfee Endpoint Protection (Nuevas opciones antimalware para proteger las máquinas virtuales de Azure: McAfee Endpoint Protection)](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Soluciones de seguridad en Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## Módulo de seguridad de hardware

La autenticación y el cifrado no mejoran la seguridad a menos que las propias claves estén bien protegidas. Puede simplificar la administración y la seguridad de claves y secretos críticos guardándolos en el Almacén de claves de Azure. El Almacén de claves permite guardar claves en módulos de seguridad de hardware (HSM) que tienen la certificación FIPS 140-2 nivel 2. Sus claves de cifrado de SQL Server para copias de seguridad o [cifrado de datos transparente](https://msdn.microsoft.com/library/bb934049.aspx) se pueden almacenar en el Almacén de claves con otras claves y secretos de sus aplicaciones. Los permisos y el acceso a estos elementos protegidos se administran con [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Más información:

- [¿Qué es el Almacén de claves de Azure?](../key-vault/key-vault-whatis.md)
- [Introducción al Almacén de claves de Azure](../key-vault/key-vault-get-started.md)
- [Blog del Almacén de claves de Azure](https://blogs.technet.microsoft.com/kv/)

## Cifrado de discos de máquinas virtuales

Cifrado de discos de Azure es una nueva funcionalidad que permite cifrar los discos de máquinas virtuales de Azure de Windows y Linux. Cifrado de discos de Azure aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar del sector de Windows y la característica [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para ofrecer cifrado de volumen para el sistema operativo y los discos de datos.

La solución se integra con el Almacén de claves de Azure para ayudarle a controlar y administrar las claves y secretos de cifrado de disco en su suscripción del almacén de claves, al mismo tiempo que garantiza que todos los datos de los discos de las máquinas virtuales se cifran en reposo en el almacenamiento de Azure.

Más información:

- [Cifrado de disco de Azure para máquinas virtuales IaaS Linux y Windows](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0/file/150942/1/Azure%25)
- [Azure Disk Encryption for Linux and Windows Virtual Machines](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/) (Cifrado de discos de Azure para máquinas virtuales de Linux y Windows)
- [Cifrado de una máquina virtual de Azure](../security-center/security-center-disk-encryption.md)

## Copia de seguridad de máquina virtual

Copia de seguridad de Azure es una solución escalable que protege los datos de su aplicación sin necesidad de realizar ninguna inversión y afrontando unos costos operativos mínimos. Los errores de una aplicación pueden dañar los datos, y los errores humanos pueden crear errores en las aplicaciones. Con Copia de seguridad de Azure, las máquinas virtuales que ejecutan Windows y Linux están protegidas.

Más información:

- [¿Qué es la Copia de seguridad de Azure?](../backup/backup-introduction-to-azure-backup.md)
- [Azure Backup](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [P+F de servicio de Copia de seguridad de Azure](../backup/backup-azure-backup-faq.md)

## Azure Site Recovery

Una parte importante de la estrategia de BCDR de su organización es averiguar cómo mantener las aplicaciones y cargas de trabajo corporativas listas para su funcionamiento cuando se producen interrupciones planificadas e imprevistas. Azure Site Recovery le ayuda mediante la coordinación de la replicación, conmutación por error y recuperación de aplicaciones y cargas de trabajo para que estén disponibles desde una ubicación secundaria si la ubicación principal deja de funcionar.

Site Recovery:

- **Simplifica su estrategia de BCDR**: Site Recovery facilita el control de la replicación, la conmutación por error y la recuperación de varias cargas de trabajo y aplicaciones de negocios desde una única ubicación. Site recovery organiza la replicación y la conmutación por error pero no intercepta los datos de su aplicación ni obtiene información alguna sobre ella.
- **Proporciona replicación flexible**: con Site Recovery puede replicar las cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V, máquinas virtuales de VMware y servidores físicos de Windows o Linux.
- **Admite recuperación y conmutación por error**: Site Recovery proporciona conmutaciones por error de prueba que admiten maniobras de recuperación ante desastres sin que los entornos de producción se vean afectados. También puede ejecutar conmutaciones por error planeadas sin pérdidas de datos para interrupciones previstas o conmutaciones por error con una pérdida de datos mínima (según la frecuencia de replicación) ante desastres inesperados. Después de la conmutación por error puede ejecutar una conmutación por recuperación a los sitios principales. Site Recovery proporciona planes de recuperación que pueden incluir scripts y libros de Automatización de Azure para que pueda personalizar la conmutación por error y la recuperación de aplicaciones de varios niveles.
- **Elimina centros de datos secundarios**: puede replicar en un sitio local secundario o en Azure. Si usa Azure como destino para la recuperación ante desastres, eliminará el costo y la complejidad de mantener un sitio secundario, y los datos replicados se almacenarán en Almacenamiento de Azure, con todas la resistencia que esto proporciona.
- **Se integra con tecnologías de BCDR existentes**: Site Recovery se asocia con otras características de BCDR de la aplicación. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, incluida la compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad.

Más información:

- [¿Qué es Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
- [¿Cómo funciona Azure Site Recovery?](../site-recovery/site-recovery-components.md)
- [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## Redes virtuales

Las máquinas virtuales necesita conectividad de red. Para satisfacer este requisito, es necesario que las máquinas virtuales de Azure estén conectadas a una red virtual de Azure. Una red virtual de Azure es una construcción lógica creada encima del tejido de red físico de Azure. Cada red virtual lógica de Azure está aislada de todas las demás redes virtuales de Azure. Esto contribuye a garantizar que otros clientes de Microsoft Azure no puedan acceder al tráfico de red de sus implementaciones.

Más información:

- [Azure Network Security Overview (Información general sobre Azure Network Security)](security-network-overview.md)
- [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md)
- [Networking features and partnerships for Enterprise scenarios](https://azure.microsoft.com/blog/networking-enterprise/) (Características de red y asociaciones para escenarios empresariales)

## Informes y administración de directivas de seguridad

Azure Security Center ayuda a evitar, detectar y responder a amenazas, al tiempo que proporciona más visibilidad y control de la seguridad de los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

Azure Security Center le ayuda a optimizar y controlar la seguridad de máquinas virtuales al:

- Proporcionar [recomendaciones de seguridad](../security-center/security-center-recommendations.md) para máquina virtual, como aplicar actualizaciones del sistema, configurar puntos de conexión de listas de control de acceso, habilitar antimalware, habilitar grupos de seguridad de red y aplicar cifrado de discos.
- Supervisar el estado de las máquinas virtuales.

Más información:

- [Introducción al Centro de seguridad de Azure](../security-center/security-center-intro.md)
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](../security-center/security-center-faq.md)
- [Guía de planeamiento y operaciones de Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

## Cumplimiento normativo

Máquinas virtuales de Azure tiene las certificaciones de FISMA, FedRAMP, HIPAA, PCI DSS nivel 1 y otros programas de cumplimiento fundamentales. Esto facilita en gran medida que sus propias aplicaciones de Azure cumplan los requisitos de cumplimiento y que su empresa satisfaga una amplia gama de requisitos normativos locales e internacionales.

Más información:

- [Centro de confianza de Microsoft: conformidad](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf) (La nube de confianza: Seguridad, privacidad y cumplimiento de Microsoft Azure)

<!---HONumber=AcomDC_0525_2016-->