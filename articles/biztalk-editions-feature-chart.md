<properties 
	pageTitle="Obtenga información acerca de las características de las ediciones de servicios de BizTalk | Azure" 
	description="Compare las capacidades de las ediciones de Servicios de BizTalk: Free, Developer, Basic, Standard y Premium. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>




# Servicios de BizTalk: Gráfico de ediciones

Servicios de BizTalk de Azure ofrece las siguientes ediciones: Free, Developer, Basic, Standard y Premium:

**Free (Vista previa)**: Incluye la capacidad de crear y administrar conexiones híbridas. Una conexión híbrida es una manera sencilla de conectar un sitio web de Azure a un recurso local, como SQL Server. 

**Developer**: Incluye conexiones híbridas y procesamiento de mensajes de EAI y EDI con un portal de administración fácil de utilizar para socios comerciales, además de compatibilidad con esquemas EDI y procesamiento enriquecido EDI sobre X12 y AS2. Puede crear escenarios comunes de EAI que conectan servicios en la nube con cualquier protocolo HTTP/S, REST, FTP, WCF y SFTP para leer y escribir mensajes.  Utilice la conectividad en sistemas locales de LOB con adaptadores SAP, Oracle eBusiness, Oracle DB, Siebel y SQL Server listos para utilizar. Todo en un entorno centrado en el desarrollador con herramientas de Visual Studio para desarrollo e implementación simples. Limitado a fines de desarrollo y prueba solo sin Contrato de nivel de servicio (SLA).

**Basic**: Incluye la mayoría de las capacidades de la edición Developer con aumentos en conexiones híbridas, puentes EAI, contratos EDI y conexiones del BizTalk Adapter Pack. También ofrece alta disponibilidad y la opción para escalar con un Contrato de nivel de servicio (SLA).

**Standard**: Incluye la mayoría de las capacidades de la edición Basic con aumentos en conexiones híbridas, puentes EAI, contratos EDI y conexiones del BizTalk Adapter Pack. También ofrece alta disponibilidad y la opción para escalar con un Contrato de nivel de servicio (SLA).


**Premium**: Incluye la mayoría de las capacidades de la edición Standard con aumentos en conexiones híbridas, puentes EAI, contratos EDI y conexiones del BizTalk Adapter Pack. También incluye el archivado, la alta disponibilidad y la opción para escalar con un Contrato de nivel de servicio (SLA).


La siguiente tabla muestra las diferencias:

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (Vista previa)</th>
        <th>Developer</th>
        <th>Básica</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>
<tr>
<td><strong>Precio de salida</strong></td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a>.</td>
<td>Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a>.</td>
</tr>
<tr>
<td><strong>Configuración mínima predeterminada</strong></td>
<td>1 unidad gratis</td>
<td>1 unidad Developer</td>
<td>1 unidad Basic</td>
<td>1 unidad Standard</td>
<td>1 unidad Premium</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Sí, en incrementos de 1 unidad Basic</td>
<td>Sí, en incrementos de una unidad Standard</td>
<td>Sí, en incrementos de una unidad Premium</td>
</tr>
<tr>
<td><strong>Escalamiento horizontal máximo permitido</strong></td>
<td>Sin escala</td>
<td>Sin escala</td>
<td>Hasta ocho unidades</td>
<td>Hasta ocho unidades</td>
<td>Hasta ocho unidades</td>
</tr>
<tr>
<td><strong>Puentes EAI por unidad</strong></td>
<td>No se incluye</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Incluye contratos TPM</td>
<td>No se incluye</td>
<td>Se incluye. 10 contratos por unidad.</td>
<td>Se incluye. 50 contratos por unidad.</td>
<td>Se incluye. 250 contratos por unidad.</td>
<td>Se incluye. 1000 contratos por unidad.</td>
</tr>
<tr>
<td><strong>Conexiones híbridas por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferencia de datos de conexiones híbridas (GB) por unidad</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Conexiones del servicio de adaptador BizTalk a sistemas locales de LOB</strong></td>
<td>No se incluye</td>
<td>1 conexión</td>
<td>2 conexiones</td>
<td>5 conexiones</td>
<td>25 conexiones</td>
</tr>
<tr>
<td align="left"><strong>Sistemas/protocolos compatibles:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus de servicio</li>
<li>Blob de Azure</li>
<li>API de REST</li>
</ul>
</td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Alta disponibilidad</strong>
<br/><br/>
Para el Contrato de nivel de servicio (SLA), consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Detalles de precios de Servicios de BizTalk de Azure</a>.
</td>
<td>No se incluye</td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Copia de seguridad y restauración</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Seguimiento</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Archivado</strong><br/><br/>
Incluye la recepción sin rechazo (NRR) y la descarga de mensajes controlados</td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>No se incluye</td>
<td>No se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Uso de código personalizado</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
<tr>
<td><strong>Uso de transformaciones, incluido XSLT personalizado</strong></td>
<td>No se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
<td>Se incluye</td>
</tr>
</table>

**Nota:**
<br/>Para la resistencia frente a los errores de hardware, la Alta disponibilidad implica disponer de varias máquinas virtuales en una sola unidad BizTalk.

## Preguntas más frecuentes
#### ¿Qué es una unidad BizTalk?
Una "unidad" es el nivel atómico de una implementación de Servicios de BizTalk de Azure. Cada edición incluye una unidad con distinta memoria y capacidad de proceso. Por ejemplo, una unidad Basic tiene más proceso que Developer, Standard tiene más proceso que Basic, etc. Cuando escala un servicio de BizTalk, se escala en términos de unidades.

#### ¿Cuál es la diferencia entre Servicios de BizTalk y una máquina virtual de BizTalk de Azure?
Servicios de BizTalk proporciona una verdadera arquitectura de plataforma como servicio (PaaS) para la creación de soluciones de integración en la nube. Con el modelo de PaaS, se puede centrar por completo en la lógica de la aplicación y dejar toda la administración de la infraestructura a Microsoft, incluyendo lo siguiente:

- No hay necesidad de administrar o revisar máquinas virtuales
- Microsoft garantiza la disponibilidad
- Puede controlar la escala a petición simplemente solicitando más o menos capacidad a través del portal de administración de Azure

BizTalk Server en Máquinas virtuales de Azure proporciona una arquitectura de infraestructura como servicio (IaaS). Puede crear máquinas virtuales y configurarlas exactamente como el entorno local, con lo que se facilita la ejecución de aplicaciones existentes en la nube sin cambiar el código. Con IaaS, sigue siendo su responsabilidad la configuración de las máquinas virtuales, la administración de las máquinas virtuales (por ejemplo, la instalación de software y revisiones de sistema operativo) y   el diseño de la aplicación para una alta disponibilidad. 

Si busca crear nuevas soluciones de integración que minimicen su esfuerzo de administración de la infraestructura, utilice Servicios de BizTalk. Si busca migrar rápidamente sus soluciones existentes de BizTalk o desea un entorno a petición para desarrollar y probar las aplicaciones de BizTalk Server, utilice BizTalk Server en la Máquina virtual de Azure.

#### ¿Cuál es la diferencia entre el Servicio de adaptador de BizTalk y las conexiones híbridas?
El Servicio de adaptador de BizTalk se usa por un Servicio de BizTalk de Azure. El Servicio de adaptador de BizTalk usa el Pack de adaptador de BizTalk para establecer una conexión con un sistema de línea de negocio (LOB) local. Una conexión híbrida ofrece una manera sencilla y adecuada de conectar aplicaciones de Azure, como sitios web y Servicios móviles, a un recurso local. 

#### Cuando creo un contrato en Servicios de BizTalk, ¿por qué el número de puentes sube en incrementos de dos, en lugar de solo uno? 

Cada contrato consta de dos puentes distintos, un puente de comunicación de envío y un puente de comunicación de recepción.

####  ¿Qué ocurre cuando se alcanza el límite en la cuota del número de puentes o contratos? 

No podrá implementar ningún otro puente ni crear otro contrato. Si desea implementar más, deberá escalar y aumentar las unidades del servicio de BizTalk o actualizar a una edición superior.

#### ¿Cómo realizo la migración de un nivel de Servicios de BizTalk a otro? 

Utilice el flujo de copia de seguridad y restauración para migrar de un nivel a otro. Solo se admiten algunas rutas de acceso de migración. Consulte [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873) para obtener más detalles sobre las rutas de acceso de migración compatibles.

#### ¿El servicio de adaptador BizTalk está incluido en el servicio? ¿Cómo puedo recibir el software?

Sí, el servicio de adaptador BizTalk con BizTalk Adapter Pack está incluido en la [descarga](http://www.microsoft.com/download/details.aspx?id=39087) del SDK de Servicios de BizTalk de Azure.

## Pasos siguientes

Para aprovisionar los Servicios de BizTalk de Azure en el Portal de administración de Azure, vaya a [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Otras referencias
- [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: Gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

<!--HONumber=46--> 
