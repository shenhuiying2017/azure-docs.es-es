<properties 
	pageTitle="Descripción de Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure | Microsoft Azure" 
	description="Obtenga información acerca de la base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure. Revise los factores de motivación comunes del negocio para determinar la tecnología de SQL que funciona mejor en el caso de la aplicación." 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="selcint"/>

# Descripción de Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure

Microsoft Azure proporciona dos opciones para el hospedaje de SQL Server: [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) y [SQL Server en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). En este artículo, empezamos por observar cómo se ajusta cada opción a la idea general de la plataforma de datos de Microsoft y luego pasamos a tratar información más detallada sobre los requisitos empresariales que motivan su elección. Si asigna mayor prioridad al ahorro o bien antepone la mínima administración a todo lo demás, este artículo puede ayudarle a decidir el enfoque correcto, en función del comportamiento de cada una de las opciones ante los requisitos empresariales que más le preocupan.

- [Plataforma de datos de Microsoft](#platform)
- [Base de datos SQL de Azure y SQL Server en Máquina virtual de Azure en detalle](#close)	
- [Motivaciones empresariales al elegir Base de datos SQL de Azure o SQL Server en Máquina virtual de Azure](#business)	
	- [Coste](#cost)
		- [Conceptos básicos sobre facturación y licencias](#billing)	
		- [Cálculo del coste total de la aplicación](#appcost)	
	- [Administración](#admin)	
	- [Contrato de nivel de servicio (SLA)](#sla)	
	- [Plazo de comercialización](#market)	
- [Resumen](#summary)	
- [Agradecimientos](#ack)	
- [Recursos adicionales](#resources)	


##<a name="platform"></a>Plataforma de datos de Microsoft

Una de las primeras cosas que hay que comprender al comparar Azure con bases de datos SQL Server locales es que puede usarlas todas. La plataforma de datos de Microsoft aprovecha la tecnología de SQL Server y la pone a disposición de los usuarios en máquinas físicas locales, entornos en nubes privadas, entornos en nubes privadas hospedados por terceros y la nube pública. Esto permite satisfacer necesidades únicas y diversas del negocio mediante una combinación de implementaciones locales y hospedadas en nube al tiempo que se usa el mismo conjunto de productos de servidor, herramientas de desarrollo y conocimientos en todos estos entornos.

   ![][1]

Como se ve en el diagrama, cada oferta puede caracterizarse por el nivel de administración que se tiene sobre la infraestructura (en el eje X) y el grado de relación coste-eficacia obtenido mediante la automatización y la consolidación en el nivel de base de datos (en el eje Y).

Al diseñar una aplicación, existen cuatro opciones básicas para el hospedaje de la parte de SQL Server de la aplicación:

- SQL Server en máquinas físicas no virtualizadas 
- SQL Server en máquinas locales virtualizadas (nube privada)
- SQL Server en máquinas virtuales de Azure (nube pública)
- Base de datos SQL de Azure (nube pública)

En las secciones siguientes, se obtendrá información sobre las dos últimas: Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure. Además, exploraremos factores de motivación comunes del negocio para determinar la opción que mejor funciona en el caso de la aplicación.

##<a name="close"></a>Base de datos SQL de Azure y SQL Server en Máquina virtual de Azure en detalle

**Base de datos SQL de Microsoft Azure (Base de datos SQL de Azure)** es una base de datos relacional como servicio, que se engloba en la categoría del sector denominada *Plataforma como servicio (PaaS)*. Base de datos SQL de Azure se compila en hardware y software estandarizado que Microsoft posee, hospeda y mantiene. Con Base de datos SQL, podrá desarrollar directamente en el servicio mediante funcionalidad y características integradas. Al utilizar Base de datos SQL, se emplea el método de pago por uso de opciones para escalar vertical u horizontalmente a fin de aumentar la potencia.

**SQL Server en Máquina virtual (VM) de Azure** que se engloba en la categoría del sector denominada *Infraestructura como servicio (IaaS)* y permite ejecutar SQL Server en una máquina virtual en la nube. De manera similar a Base de datos SQL de Azure, se compila en hardware estandarizado que Microsoft posee, hospeda y mantiene. Si usa SQL Server en una máquina virtual, puede incorporar su propia licencia de SQL Server a Azure o utilizar una de la imágenes preconfiguradas de SQL Server en el portal de Azure.

Por lo general, estas dos opciones SQL se optimizan con diferentes fines:

- **Base de datos SQL de Azure** se optimiza para reducir al mínimo los costes generales en cuanto al aprovisionamiento y la administración de varias bases de datos. Minimiza los costes corrientes de administración porque no es necesario administrar máquinas virtuales, el sistema operativo ni el software de base de datos, incluidas las actualizaciones, la alta disponibilidad y las copias de seguridad. Habitualmente, Base de datos SQL puede aumentar considerablemente el número de bases de datos administradas por un solo recurso de TI o de desarrollo.
- **Ejecución de SQL Server en Máquina virtual de Azure** se optimiza para ampliar aplicaciones existentes de SQL Server locales a Azure en un escenario híbrido o para implementar una aplicación existente en Azure en un escenario de migración o un escenario de desarrollo/pruebas. Un ejemplo del escenario híbrido es el mantenimiento de réplicas de la base de datos secundaria en Azure a través de [Red virtual de Azure](../virtual-network/virtual-networks-overview.md). Con SQL Server en máquinas virtuales de Azure, tiene todos los derechos administrativos sobre una instancia dedicada de SQL Server y una máquina virtual basada en la nube. Es la elección perfecta cuando una organización ya dispone de recursos de TI para mantener las máquinas virtuales. Con SQL Server en una máquina virtual, puede compilar un sistema muy personalizado para abordar los requisitos de rendimiento y disponibilidad específicos de la aplicación.

En la siguiente tabla se resumen las principales características de Base de datos SQL de Azure y SQL Server en máquinas virtuales de Azure:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Base de datos SQL de Azure</th>
   <th align="left" valign="middle">SQL Server en máquinas virtuales de Azure</th>
   
</tr>
<tr>
   <td valign="middle"><p><b>Más adecuado para</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Nuevas aplicaciones diseñadas para la nube que tienen restricciones de tiempo en desarrollo y marketing. 
          <li type=round>Aplicaciones que requieren alta disponibilidad automática, solución de recuperación ante desastres y mecanismos de actualización integrados.
          <li type=round>Cuando se tienen cientos o miles de bases de datos pero no se desea administrar el sistema operativo, el hardware y las opciones de configuración subyacentes. 
         <li type=round>Aplicaciones que usan patrones de escalado horizontal.
         <li type=round>Bases de datos de 500&#160;GB de tamaño como máximo.
         <li type=round>Compilación de aplicaciones de software como servicio.
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Aplicaciones existentes que requieren una rápida migración a la nube con el mínimo de cambios.
      <li type=round>Aplicaciones de SQL Server que requieren acceso a recursos locales (como, por ejemplo, Active Directory) desde Azure a través de un túnel seguro. 
      <li type=round>Cuando se requiere un entorno de TI personalizado con derechos administrativos completos.
      <li type=round>Escenarios de desarrollo rápido y pruebas cuando no se desea comprar hardware de SQL Server de no producción local.
      <li type=round>Recuperación ante desastres para aplicaciones de SQL Server locales con <a href="http://msdn.microsoft.com/library/jj919148.aspx">copia de seguridad en Almacenamiento de Azure</a> o con <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">réplicas AlwaysOn en máquinas virtuales de Azure</a>.
      <li type=round>Bases de datos grandes con más de 1&#160;TB de tamaño.
      </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Recursos</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>No se desea emplear recursos de TI para el soporte y mantenimiento de la infraestructura subyacente.
       <li type=round>Se desea centrarse en el nivel de aplicación.
       </ul></td>
   <td valign="middle"><ul><li type=round>Se cuenta con recursos de TI para soporte y mantenimiento.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Coste total de propiedad</b></p></td>
   <td valign="middle"><ul><li type=round>Elimina costes de hardware. Reduce los costes administrativos.</ul></td>
   <td valign="middle"><ul><li type=round>Elimina costes de hardware. </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Continuidad del negocio</b></p></td>
   <td valign="middle"><ul><li type=round>Además de capacidades integradas de infraestructura de tolerancia a fallos, Base de datos SQL de Azure proporciona características como, por ejemplo, Restauración a un momento dado, Restauración geográfica y Replicación geográfica para aumentar la continuidad del negocio. Para obtener más información, consulte <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Continuidad del negocio en Base de datos SQL de Azure</a>.</ul></td>
   <td valign="middle"><ul><li type=round>SQL Server en máquinas virtuales de Azure permite configurar una solución de recuperación ante desastres y alta disponibilidad para las necesidades específicas de la base de datos. Por consiguiente, podrá tener un sistema altamente optimizado para la aplicación. Podrá probar y ejecutar conmutaciones por error cuando sea necesario. Para obtener más información, vea <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure</a>.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Nube híbrida</b></p></td>
   <td valign="middle"><ul><li type=round>La aplicación local puede obtener acceso a datos de Base de datos de SQL de Azure.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Con SQL Server en máquinas virtuales de Azure, se pueden tener aplicaciones que se ejecuten parcialmente en la nube y parcialmente en la instalación local. Por ejemplo, se puede ampliar la red local y el dominio de Active Directory a la nube a través de <a href="https://azure.microsoft.com/documentation/articles/virtual-networks-overview/">Servicios de red de Azure</a>. Además, se pueden almacenar archivos de datos locales en Almacenamiento de Azure con la <a href="http://msdn.microsoft.com/library/dn385720.aspx">característica Archivos de datos de SQL Server en Azure</a>. Para obtener más información, consulte <a href="http://msdn.microsoft.com/library/dn606154.aspx">Introducción a la nube híbrida de SQL Server 2014</a>.
      <li type=round>Compatible con la recuperación ante desastres para aplicaciones de SQL Server locales con <a href="http://msdn.microsoft.com/library/jj919148.aspx">copia de seguridad en Almacenamiento de Azure</a> o con <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">réplicas AlwaysOn en máquinas virtuales de Azure</a>.
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>Motivaciones empresariales al elegir Base de datos SQL de Azure o SQL Server en Máquina virtual de Azure

###<a name="cost"></a>Coste

Si se encuentra en una empresa incipiente con falta de medios o en un equipo de una compañía bien establecida que opera con restricciones presupuestarias, los fondos limitados suelen ser el factor principal a la hora de decidir cómo hospedar la base de datos. En esta sección, obtendremos en primer lugar información relativa a los conceptos básicos sobre facturación y licencias en Azure con respecto a estas dos opciones de base de datos relacional: Base de datos SQL de Azure y SQL Server en Máquina virtual de Azure. Seguidamente, veremos cómo debemos calcular el coste total de la aplicación.

####<a name="billing"></a>Conceptos básicos sobre facturación y licencias

**Base de datos SQL de Azure** se vende a los clientes como servicio, no con licencia, mientras que SQL Server en Máquina virtual de Azure requiere licencias tradicionales de SQL Server.

Actualmente, **Base de datos SQL de Azure** se encuentra disponible en diversos niveles de servicio. Para los niveles de servicio Basic, Standard y Premium, se factura por hora a una tarifa fija en función del nivel de servicio y el nivel de rendimiento que se elija. Los niveles de servicio Basic, Standard y Premium se han concebido para proporcionar un rendimiento predecible con varios niveles de rendimiento para igualar los requisitos máximos de la aplicación. Se puede cambiar de un nivel de servicio a otro y de un nivel de rendimiento a otro para igualar las necesidades variables de capacidad de proceso de la aplicación. Para obtener la información más reciente sobre los niveles de servicio que se admiten actualmente, vea [Niveles de servicio de Base de datos SQL de Azure (ediciones)](sql-database-service-tiers.md).

Con **Base de datos SQL de Azure**, Microsoft Azure configura, revisa y actualiza automáticamente el software de base de datos en centros de datos de todo el mundo. Por lo tanto, se reducen los costes de administración. Además, sus capacidades de [copia de seguridad integrada](http://msdn.microsoft.com/library/azure/jj650016.aspx) ayudan a obtener un ahorro significativo, sobre todo, cuando se tiene gran cantidad de base de datos. Cuando se usa Base de datos SQL de Azure, no se factura por consultas individuales que se ejecuten en Base de datos SQL de Azure ni por el tráfico entrante de Internet. Se le facturará el [tráfico saliente de Internet](http://azure.microsoft.com/pricing/details/data-transfers/). Si la base de datos tiene un alto volumen de transacciones y debe admitir muchos usuarios simultáneos, recomendamos usar el nivel de servicio Premium en lugar de Basic o Standard.

Con SQL Server en **Máquina virtual de Azure**, se emplean licencias tradicionales de SQL Server. También puede usar la imagen de SQL Server que proporciona la plataforma o incorporar su licencia de SQL Server a Azure. Cuando use las imágenes suministradas por la plataforma de SQL Server, el coste depende del tamaño de la máquina virtual, así como de la versión de SQL Server que elija. Básicamente, se paga el coste de licencias por minuto de SQL Server, las licencias por minuto de Windows Server y el coste de almacenamiento de Azure. La opción de facturación por minuto permite utilizar SQL Server durante el tiempo que sea necesario sin comprar la licencia completa de SQL Server. Si incorpora su propia licencia de SQL Server a Azure, solo se cobran los costes de proceso y almacenamiento de Azure. Para obtener más información, consulte [Movilidad de Licencias a través de Software Assurance en Azure](http://azure.microsoft.com/pricing/license-mobility/).

####<a name="appcost"></a>Cálculo del costo total de la aplicación

Cuando se comienza a usar una plataforma en la nube, el coste de ejecución de la aplicación incluye principalmente los costes de administración y desarrollo; además de los costes de servicio que la plataforma en la nube pública requiere.

A continuación, se ofrece el cálculo del coste pormenorizado para la aplicación en ejecución en Base de datos SQL de Azure y en SQL Server en máquinas de Azure:

**Cuándo se usa Base de datos SQL de Azure:**

*Coste total de la aplicación = costes de administración muy minimizados + costes de desarrollo de software + costes de servicio de Base de datos SQL de Azure*

**Cuándo se usa SQL Server en Máquina virtual de Azure:**

*Coste total de la aplicación = costes minimizados de desarrollo/modificación de software + costes de administración + costes de licencias de SQL Server y Windows Server + costes de almacenamiento de Azure*

> [AZURE.IMPORTANT]actualmente, Base de datos SQL de Azure no admite todas las características de SQL Server. Para obtener información de comparación detallada, consulte [Instrucciones y limitaciones de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ff394102.aspx). Tenga esto presente si desea mover una base de datos existente a Base de datos SQL de Azure ya que podría necesitar presupuesto adicional sobre el rediseño de la base de datos. Base de datos SQL de Azure es una oferta de plataforma como servicio de Microsoft. Cuando migre una aplicación existente de SQL Server local a Base de datos SQL de Azure, recomendamos actualizar la aplicación para aprovechar todas las ventajas de la oferta de plataforma como servicio. Por ejemplo, comience por usar [Sitios web de Azure](http://azure.microsoft.com/documentation/services/websites/) o [Servicios en la nube de Azure](http://azure.microsoft.com/services/cloud-services/) en el nivel de aplicación para aumentar la relación costo-beneficios. Además, valide la aplicación en distintos niveles de servicio de Base de datos SQL de Azure y compruebe cual se ajusta más a las necesidades de la aplicación. Este proceso ayuda a obtener mejores resultados de rendimiento y costes minimizados. Para obtener más información, vea [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](sql-database-service-tiers.md).

Para obtener una estimación de costos pormenorizada, use la [Calculadora de precios de Azure](http://azure.microsoft.com/pricing/calculator/).

Para obtener más información sobre precios, consulte los siguientes recursos:

- [Detalles de precios de Base de datos SQL de Azure](http://azure.microsoft.com/pricing/details/sql-database/) 
- [Detalles de precios de Máquina virtual](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [SQL Server en máquinas virtuales de Azure: Detalles de precios](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Windows Server en máquinas virtuales de Azure: Detalles de precios](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>Administración

Si ya está ocupado en muchas tareas, quizá asumir la administración de bases de datos y servidores no es lo que desea. En muchas empresas, la decisión de pasar a un servicio en la nube está muy relacionada con la posibilidad de reducir la carga de complejidad de administración. Con **Base de datos SQL de Azure**, Microsoft administra el hardware físico como, por ejemplo, discos duros, servidores y almacenamiento; replica automáticamente todos los datos para proporcionar alta disponibilidad; configura y actualiza el software de base de datos; administra el equilibrio de carga; y realiza una conmutación por error transparente en caso de error del servidor. Puede continuar para administrar las instancias de Base de datos SQL de Azure pero sin controlar los recursos físicos de la instancia subyacente de SQL Server y de la plataforma de Azure. Por ejemplo, puede administrar bases de datos e iniciar sesión, realizar optimización de índices y optimizar consultas, pero no administrar tablas del sistema ni llevar a cabo la administración de grupos de archivos. Para obtener más información, consulte [Instrucciones y limitaciones de Base de datos SQL de Azure](http://msdn.microsoft.com/library/ff394102.aspx).

Por otra parte, puede que cuente con conocimientos internos y desee mantener el control desde la ubicación de la base de datos hasta la máquina propiamente dicha. Con **Ejecución de SQL Server en Máquina virtual de Azure**, tiene control completo sobre la configuración del sistema operativo y la instancia de SQL Server. Con una máquina virtual, usted decide cuándo actualizar el software del sistema operativo y de la base de datos, y cuándo instalar cualquier otro software adicional, como, por ejemplo, herramientas antivirus y de copia de seguridad. Además, se puede controlar el tamaño de la máquina virtual, el número de discos y sus configuraciones de almacenamiento. Por ejemplo, Azure permite cambiar el tamaño de una máquina virtual cuando sea necesario. Para obtener más información, vea [Tamaños de máquinas virtuales y servicios en la nube de Azure](../virtual-machines/virtual-machines-size-specs.md).

###<a name="sla"></a>Contrato de nivel de servicio (SLA)

Para algunos de nosotros, cumplir las obligaciones de tiempo de actividad de un contrato de nivel de servicio (SLA) es la máxima prioridad. En esta sección, analizaremos lo que significa SLA para cada opción de hospedaje de base de datos.

Para **Base de datos SQL de Azure**, dados los niveles de servicio Basic, Standard y Premium, Microsoft proporciona un contrato de nivel de servicio de disponibilidad del 99,99 %. Observe que el contrato de nivel de servicio de disponibilidad aborda la posibilidad de conectarse a la base de datos. Dicho de otro modo, es un SLA de nivel de base de datos. Para obtener la información más reciente sobre SLA, consulte [Contrato de nivel de servicio](http://azure.microsoft.com/support/legal/sla/). Para obtener la información más reciente sobre Niveles de servicio de Base de datos SQL de Azure (ediciones) y los planes de Continuidad del negocio admitidos, vea [Niveles de servicio de Base de datos SQL de Azure](sql-database-service-tiers.md).

Para **Máquinas virtuales hospedadas en Azure**, Microsoft proporciona un contrato de nivel de servicio de disponibilidad del 99,95 % y esta disponibilidad corresponde a la máquina virtual, no a los procesos en ejecución en el interior de la máquina virtual (como, por ejemplo, SQL Server). El [Contrato de nivel de servicio de Máquina virtual](http://www.microsoft.com/download/details.aspx?id=38427) requiere que se hospeden como mínimo dos máquinas virtuales en un conjunto de disponibilidad. Con esta configuración, Azure garantiza que al menos una de las máquinas virtuales esté disponible el 99,95% del tiempo. Para alta disponibilidad (HA) de base de datos en las máquinas virtuales, se debe configurar una de las opciones de alta disponibilidad admitidas en SQL Server, como Grupos de disponibilidad AlwaysOn. Tenga en cuenta que el establecimiento de AlwaysOn en Azure requiere de cierta administración y configuración manual, y se paga un extra por cada máquina secundaria con la que opere.


###<a name="market"></a>Plazo de comercialización

**Base de datos SQL de Azure** es la solución correcta para aplicaciones diseñadas en la nube cuando la productividad del desarrollador y un plazo de comercialización rápido son factores críticos. Con una funcionalidad de tipo DBA mediante programación, resulta perfecto para arquitectos y desarrolladores en la nube puesto que reduce la necesidad de administrar el sistema operativo y la base de datos subyacentes. Ayuda a los desarrolladores a comprender y configurar tareas relacionadas con la base de datos. Por ejemplo, se puede usar la [API de REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) y [cmdlets de PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) para automatizar y administrar operaciones administrativas para cientos de bases de datos. Con [escalado elástico](sql-database-elastic-pool.md) de la nube, se puede centrar fácilmente en el nivel de aplicación y comercializar la aplicación más rápidamente.

La **ejecución de SQL Server en Máquina virtual de Azure** es perfecta si las aplicaciones nuevas y existentes requieren tener acceso y control de todas las características de una instancia de SQL Server, así como cuándo se migran aplicaciones y bases de datos locales existentes a la nube tal cual están. Dado que no es necesario cambiar los niveles de presentación, aplicación y datos, se ahorra tiempo y presupuesto en renovar la arquitectura de la solución existente. En su lugar, puede centrarse en migrar todos los paquetes de su solución a las máquinas virtuales y realizar algunas que requiere la plataforma de Azure. Para obtener más información, vea [Prácticas recomendadas de rendimiento para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md).

##<a name="summary"></a>Resumen

En este artículo, trataremos: Base de datos SQL de Azure y SQL Server en Máquina virtual de Azure en detalle. Además, hemos tratado los factores de motivación comunes del negocio que podrían afectar a la toma de decisiones sobre lo que se elige.

A continuación, se ofrece un resumen de sugerencias que debe tener en cuenta al usar una u otra opción:

Elija **Base de datos SQL de Azure**, si:

- Compila aplicaciones basadas en la nube completamente nuevas; o desea migrar la base de datos de SQL Server a Azure y la base de datos no utiliza una de las funcionalidades no admitidas de Base de datos SQL de Azure. Para obtener más información, consulte [Referencia de Transact-SQL de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). Este enfoque ofrece las ventajas de un servicio en la nube completamente administrado y garantiza un plazo de comercialización rápido.

- Desea que Microsoft realice operaciones de administración comunes en las bases de datos y requiere contratos de nivel de servicio de disponibilidad más seguros para las bases de datos. Este enfoque puede minimizar los costes de administración y, al mismo tiempo, proporciona una disponibilidad garantizada de la base de datos.

    [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md)


Elija **SQL Server en Máquina virtual de Azure**, si:

- Tiene aplicaciones locales existentes y desea dejar de realizar el mantenimiento de su propio hardware o contempla la posibilidad de usar soluciones híbridas. Este enfoque permite tener acceso más rápidamente a una gran capacidad de bases de datos y además conecta las aplicaciones locales a la nube a través de un túnel seguro.

- Tiene recursos de TI existentes, requiere derechos administrativos completos sobre SQL Server y precisa de plena compatibilidad con SQL Server local (por ejemplo, algunas características no existen en Base de datos SQL de Azure). Este enfoque permite minimizar costes de desarrollo o modificaciones de aplicaciones existentes con la flexibilidad de ejecutar la mayoría de las aplicaciones. Además, ofrece control total sobre la configuración de la máquina virtual, el sistema operativo y la base de datos.

    [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md)

> [AZURE.NOTE]¿Desea probar SQL Server 2016 CTP2? Suscríbase a Microsoft Azure y, a continuación, acceda [aquí](http://aka.ms/sql2016vm "aquí") para crear una máquina virtual con SQL Server 2016 CTP2 ya instalado.


##<a name="ack"></a>Agradecimientos

Este artículo del grupo Servicios de contenido empresarial y en la nube de Microsoft se produjo con la ayuda de muchas personas de la comunidad Microsoft.

**Autor:** Selcin Turkarslan

**Colaboradores técnicos:** Conor Cunningham

**Revisores técnicos:** Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**Revisores editoriales:** Heidi Steen, Maggie Sparkman.

¡Gracias a todos por hacer posible este artículo!

##<a name="resources"></a>Recursos adicionales 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Descripción</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN: Base de datos SQL de Azure</a></p>
<p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/">Información general de SQL Server en máquinas virtuales de Azure</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com: Base de datos SQL de Azure</a></p></td>
   <td valign="middle">Vínculos a la documentación de la biblioteca.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-application-patterns-and-development-strategies/">Estrategias de desarrollo y patrones de aplicación de SQL Server en máquinas virtuales de Azure</p></td>
   <td valign="middle">En este artículo se tratan los patrones de aplicación más frecuentes que se aplican a SQL Server en máquinas virtuales de Azure y también en escenarios híbridos que incluyan Base de datos SQL de Azure. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Bloque de aplicación de control de errores transitorios de biblioteca de empresa de Microsoft</p></td>
   <td valign="middle">Esta biblioteca permite que los desarrolladores aumenten la resistencia de las aplicaciones en ejecución en Base de datos SQL de Azure agregando una lógica de control de errores transitorios más robusta. Los errores transitorios son aquellos que se producen debido a algún estado transitorio como, por ejemplo, problemas de conectividad de la red o falta de disponibilidad del servicio. Puesto que Base de datos SQL de Azure es un servicio multiempresa, es importante controlar dichos errores para minimizar cualquier tiempo de inactividad de la aplicación. </td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png

<!---HONumber=Oct15_HO4-->