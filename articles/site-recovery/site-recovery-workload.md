<properties
	pageTitle="Guía de cargas de trabajo de Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas localmente en Azure o en un sitio local secundario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/21/2015" 
	ms.author="pratshar"/>

# Guía de cargas de trabajo de Site Recovery

Azure Site Recovery permite a los clientes a implementar soluciones de disponibilidad de aplicaciones. Ya sea en aplicaciones basadas en Windows Server o en Linux, aplicaciones empresariales de origen de Microsoft u ofertas de otros proveedores, puede usar Azure Site Recovery para habilitar la recuperación ante desastres, entornos de desarrollo/pruebas a petición o migraciones a la nube.

Microsoft tiene amplios conocimientos y experiencia en el desarrollo de las mejores aplicaciones empresariales, como SharePoint, Exchange, Dynamics, SQL Server y Active Directory. Microsoft también trabaja en estrecha colaboración con proveedores líderes, como Oracle, SAP, IBM y Red Hat para asegurarse de que sus aplicaciones y servicios funcionan bien en plataformas de Microsoft, incluido Hyper-V y Azure. Hemos aprovechado estos puntos fuertes exclusivos para obtener un conocimiento profundo de los requisitos de disponibilidad de cada aplicación para permitirle implementar las mejores soluciones de disponibilidad y recuperación ante desastres de su clase que se pueden personalizar para cada aplicación.


##Principales características
Las características de Azure Site Recovery se han diseñado teniendo en cuenta la protección y recuperación de las aplicaciones:

- La replicación casi sincrónica con RPO de tan solo 30 segundos, que satisface las necesidades de las aplicaciones más críticas.
- Instantáneas coherentes con la aplicación para aplicaciones simples o de N niveles
- Se integra con la replicación a nivel de aplicación. Aprovecha las mejores ofertas de nivel de aplicación que incluyen la replicación de AD, AlwaysOn de SQL Server, grupos de disponibilidad de base de datos de Exchange y protección de datos de Oracle
- Los Planes de recuperación flexibles permiten la recuperación de toda la pila de aplicaciones con un solo clic, incluida la ejecución de scripts externos o incluso acciones manuales. 
- La administración avanzada de redes en ASR y Azure automatiza todas las configuraciones de red específicas de su aplicación: reserva direcciones IP, configura equilibradores de carga o usa el Administrador de tráfico de Microsoft para cambios de red con RTO bajo.
- Biblioteca de automatización sofisticada que proporciona scripts específicos de aplicaciones, listos para producción. Descárguela e intégrela en sus soluciones basadas n ASR.


##Resumen de guía de cargas de trabajo

Las tecnologías de replicación de ASR son compatibles con cualquier aplicación que se ejecuta en una máquina virtual. Hemos llevado a cabo pruebas adicionales en colaboración con los equipos de producto de las aplicaciones para mejorar aún más cada aplicación.

**Carga de trabajo** | <p>**Replicación de máquinas virtuales de Hyper-V**</p> <p>**(para sitio secundario)**</p> | <p>**Replicación de máquinas de Hyper-V virtual**</p> <p>**(para Azure)**</p> | <p>**Replicación de máquinas virtuales de VMware**</p> <p>**(en sitio secundario)**</p> | <p>**Replicación de máquinas virtuales de VMware**</p><p>**(para Azure)****</p> ---|---|---|---|--- Active Directory, DNS | Y | Y | Y | Y Aplicaciones web (IIS, SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y |Y <p>SAP</p><p>Replicación del sitio de SAP en Azure para sin clúster</p> | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Exchange (no DAG) | Y | Próximamente | Y | Y Escritorio remoto/VDI | Y | Y | Y | N/A <p>Linux</p> <p>(aplicaciones y sistema operativo)</p> | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | Próximamente | Y | Próximamente Oracle | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) Servidor de archivos de Windows | Y | Y | Y | Y

##Active Directory y DNS

Todas las aplicaciones empresariales, como SharePoint, Dynamics AX y SAP, dependen de la infraestructura de DNS y AD para funcionar correctamente. Al crear una solución de recuperación ante desastres para cualquier aplicación de este tipo, es importante proteger y recuperar AD antes de que aparezcan los demás componentes de la aplicación en caso de una interrupción.

Con Azure Site Recovery, puede crear un plan de recuperación ante desastres automatizada completa para su AD. Si tiene un AD para varias aplicaciones, como SharePoint y SAP, en el sitio primario y decide realizar una conmutación por error del sitio completo, puede realizar una conmutación por error de AD primero mediante el plan de recuperación de AD y, a continuación, de las otras aplicaciones mediante planes de recuperación específicos de la aplicación.

Consulte el documento vinculado para obtener una guía detallada acerca de la [implementación de Azure Site Recovery para AD](http://aka.ms/asr-ad)

##SQL Server
Microsoft SQL Server es la base para muchos fabricantes de origen de nivel empresarial, terceros y líneas personalizadas de aplicaciones empresariales que se ejecutan en el centro de datos de un cliente local. Las aplicaciones como SharePoint, Dynamics y SAP usan SQL Server para implementar servicios de datos. Las tecnologías de alta disponibilidad y recuperación ante desastres para SQL Server y Azure Site Recovery son complementarias y pueden usarse para proporcionar protección de extremo a extremo para las aplicaciones empresariales de varios niveles. Azure Site Recovery ofrece los siguientes beneficios para los entornos de SQL Server:

- Protege fácilmente los servidores SQL independientes o agrupados en Azure o en un sitio secundario. Amplía una solución de recuperación ante desastres simple y rentable para cualquier versión y edición de SQL Server.
- Se integra con grupos de disponibilidad de AlwaysOn de SQL Server, la mejor solución de su clase de recuperación ante desastres y administra la operación de conmutación por error y conmutación por recuperación con planes de recuperación de ASR.
- Crea planes de recuperación de extremo a extremo para toda la pila de aplicaciones, incluidas las bases de datos SQL.
- Use Azure Site Recovery para ampliar SQL Server durante los picos de carga separándolos en tamaños mayores de IaaS VM en Azure.
- Use Azure Site Recovery para crear copias de prueba de SQL Server en Azure o en un sitio secundario. Use esta copia para el análisis y las comprobaciones de cumplimiento de los datos sin afectar el entorno de producción.

Consulte el documento vinculado para obtener una guía detallada acerca de la [implementación de Azure Site Recovery para SQL](http://aka.ms/asr-sql)

##SharePoint
SharePoint es una aplicación popular que permite a las organizaciones colaborar al proporcionar portales de intranet, administración de documentos y archivos, así como capacidades de búsqueda empresarial redes sociales, sitios web. También es una plataforma de aplicaciones para implementar con facilidad aplicaciones personalizadas y flujos de trabajo. Mediante Azure Site Recovery para SharePoint puede:

- Eliminar la necesidad y el costo de la infraestructura asociada para una granja de servidores de espera para la recuperación ante desastres. Con ASR, puede habilitar la protección de toda la granja de servidores (web, aplicaciones y niveles de base de datos) en Azure o en un sitio secundario.
- Simplificar la administración e implementación de las aplicaciones. Las actualizaciones implementadas en el sitio principal se replican automáticamente y están disponibles cuando se recupera la granja de servidores en el sitio secundario. Esto elimina la complejidad de la administración para mantener actualizada una granja de servidores de reserva y reduce el TCO.
- Facilitar el desarrollo de aplicaciones de SharePoint y pruebas mediante la creación de una copia de producción a petición para probar y depurar.
- Simplifique su ruta de acceso a la nube mediante ASR para migrar las implementaciones de SharePoint a Azure.

Consulte el documento vinculado para obtener una guía detallada acerca de la [implementación de Azure Site Recovery para Sharepoint](http://aka.ms/asr-sharepoint)


##Dynamics AX
Microsoft Dynamics AX es una importante solución de planeación de recursos empresariales (ERP) que es fácil de aprender y usar para ofrecer valor más rápido, aprovechar las oportunidades de negocio y estimular la implicación del usuario y la innovación en toda la organización.

- Con ASR, puede habilitar la protección de todo Dynamics Ax (nivel web y AOS, niveles de base de datos, SharePoint) en Azure o en un sitio secundario.
- Simplifique su ruta de acceso a la nube mediante ASR para migrar las implementaciones de Dynamics Ax a Azure.
- Facilite el desarrollo de aplicaciones de Dynamics y pruebas mediante la creación de una copia de producción a petición para probar y depurar.

Consulte el documento vinculado para obtener una guía detallada acerca de la [implementación de Azure Site Recovery para Dynamics AX](http://aka.ms/asr-dynamics)

## RDS 
Servicios de Escritorio remoto (RDS) de Windows Server acelera y extiende las implementaciones de aplicaciones y escritorio a cualquier dispositivo, lo que mejora la eficiencia de los trabajadores remotos, al tiempo que ayuda a mantener la propiedad intelectual crítica segura y simplificar el cumplimiento de las normativas. Servicios de Escritorio remoto habilita la infraestructura de escritorio virtual (VDI), escritorios basados en sesión y aplicaciones, lo que permite a los usuarios trabajar desde cualquier lugar.

Con ASR, puede habilitar la protección de escritorios virtuales agrupados administrados o no administrados en un sitio secundario y aplicaciones remotas y sesiones en un sitio secundario o Azure.

Consulte el documento vinculado para obtener una guía detallada acerca de la [implementación de Azure Site Recovery para RDS/VDI](http://aka.ms/asr-rds)


## Exchange
Microsoft Exchange es el software preferido usado por las empresas para hospedar su mensajería y servicios de correo electrónico. Exchange garantiza el acceso a la comunicación a través de PC, teléfono o explorador, al tiempo que proporciona una confiabilidad, manejabilidad y protección de datos sin precedentes.

Microsoft Exchange admite de forma nativa soluciones de alta disponibilidad y recuperación ante desastres. Las tecnologías de Azure Site Recovery y grupos de disponibilidad de bases de datos de Exchange son complementarias.

- Exchange DAG es la opción de implementación recomendada para habilitar la mejor recuperación ante desastres para implementaciones de Exchange. Los planes de recuperación de ASR puede incluir DAG para orquestar las conmutaciones por error de DAG a través de los sitios.
- Para implementaciones pequeñas, como un único servidor o servidores no agrupados, Azure Site Recovery puede proteger y realizar una conmutación por error de servidores individuales en Azure o en un sitio secundario.

Consulte el documento vinculado para obtener una guía detallada acerca de la [implementación de Azure Site Recovery para Exchange](http://aka.ms/asr-exchange)

## SAP

SAP es un software de planeación de recursos empresariales (ERP) usado por muchas organizaciones en todo el mundo. Con frecuencia, SAP se considera una de las aplicaciones críticas en las empresas. La arquitectura y las operaciones de estas aplicaciones son muy complejas y asegurarse de cumplir los requisitos de BCDR es muy importante.

- Con ASR, puede habilitar la protección de toda la implementación de SAP con diferentes niveles en Azure o en un sitio secundario.
- Simplifique su ruta de acceso a la nube mediante ASR para migrar las implementaciones de SAP a Azure.
- Facilite el desarrollo de aplicaciones SAP y pruebas mediante la creación de una copia de producción a petición para probar y depurar.

Consulte el documento vinculado para obtener una guía detallada acerca de la [implementación de Azure Site Recovery para SAP NetWeaver](http://aka.ms/asr-sap)

<!---HONumber=Sept15_HO4-->