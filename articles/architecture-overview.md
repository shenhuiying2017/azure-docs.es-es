<properties 
	pageTitle="Arquitectura de aplicaciones en Microsoft Azure" 
	description="Información general de arquitectura que cubre patrones de diseño habituales." 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Arquitectura de aplicaciones en Microsoft Azure
Recursos para la creación de aplicaciones que usan Microsoft Azure.

##Patrones de diseño de arquitectura de Azure
Microsoft publica la serie de patrones de diseño de arquitectura para ayudarle a crear sus propios diseños personalizados. Los patrones están diseñados como guías de arquitectura concisas que pueden combinarse para proporcionar orientación sobre cómo aprovechar mejor la plataforma Microsoft Azure para resolver las necesidades de su organización.


[Información general](../azure-architectures-cpif-overview/)-[Redes híbridas](../azure-architectures-cpif-infrastructure-hybrid-networking/)-[Procesamiento por lotes fuera del sitio](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/)-[Capa de datos multisitio](../azure-architectures-cpif-foundation-multi-site-data-tier/)-[Capa web con equilibrio de carga global](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/)-[Capa de búsqueda de Azure](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Cada patrón contiene
 
- Una descripción del servicio
- Una lista de los servicios de Azure necesarios para aprovechar el patrón
- Diagramas de arquitectura
- Dependencias de arquitectura
- Limitaciones o consideraciones del diseño que pueden influir en el patrón
- Interfaces y extremos
- Antipatrones
- Consideraciones sobre la arquitectura de alto nivel de clave, como disponibilidad y resistencia, SLA compuestos para servicios usados, escala y rendimiento y consideraciones operativas y sobre costos.

##Proyectos de arquitectura de Microsoft

Microsoft publica un conjunto de proyectos de arquitectura de alto nivel que muestran cómo crear tipos específicos de sistemas que incluyen productos de Microsoft y servicios de Microsoft Azure. Cada proyecto incluye un archivo 2D basado en Visio que puede descargar y modificar, un archivo PDF 3D con más color para presentar el plano y un vídeo que explica la versión 3D. Consulte [Proyectos de arquitectura de Microsoft](http://msdn.microsoft.com/dn630664).

Los diagramas de proyectos 2D usan el conjunto de símbolos empresariales y de nube mencionados a continuación.

Los PDF de proyectos 3D se crean en una herramienta de terceros, pero se está desarrollando una plantilla de Visio. Consulte aquí un [vídeo de aprendizaje de la versión BETA de la plantilla.](http://aka.ms/3dBlueprintTemplate). Para obtener la versión beta de la plantilla de productos 3D de Visio, envíe un correo electrónico a [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

![Diagrama 3D de Proyectos de arquitectura de Microsoft](./media/architecture-overview/BluePrintThumb.jpg)

##Conjunto de iconos y símbolos empresariales y de nube

[Descargue el conjunto de iconos y símbolos empresariales y de nube](http://aka.ms/CnESymbols) para crear materiales técnicos que describen Azure, Windows Server, SQL Server y otros productos de Microsoft. Puede utilizarlos en diagramas de arquitectura, materiales de formación, presentaciones, hojas de datos, infografía, notas del producto e incluso en libros de terceros si el libro sirve para enseñar a las personas los productos de Microsoft. Los símbolos están en formatos Visio y PNG. Se incluyen instrucciones sobre cómo usar los archivos PNG en PowerPoint.

El conjunto de símbolos se envía cada trimestre y se actualiza a medida que se publican nuevos servicios. Si desea una vista previa de la publicación más reciente, que puede incluir servicios adicionales de Azure, envíe un correo electrónico a [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

Deseamos saber lo que piensa; para ello, la descarga incluye instrucciones para que entregue sus comentarios. Si ha usado los símbolos, rellene la breve [encuesta](http://aka.ms/azuresymbolssurveyv2) de 5 preguntas o envíenos un correo electrónico a la dirección anterior para hacernos saber si son útiles y cómo los usa.

Están disponibles símbolos adicionales en la [Galería de símbolos de Microsoft Office Visio](http://www.microsoft.com/es-es/download/details.aspx?id=35772), aunque no están optimizados para diagramas de arquitectura como el conjunto de CnE.

![Conjunto de iconos y símbolos empresariales y de nube](./media/architecture-overview/CnESymbols.png)

##Patrones de diseño
Microsoft Patterns and Practices ha publicado el libro [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx) que está disponible en MSDN y como descarga PDF. También hay disponible un póster de gran formato en el que se enumeran todos los patrones.

![Póster de patrones de nube de Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Infografías de arquitectura
Microsoft publica varios pósteres e infografías relacionados con la arquitectura. Entre ellos, se encuentran [Creación de aplicación en la nube para el mundo real](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) y [Escalación de aplicaciones con Servicios en la nube](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografías de arquitectura de Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=July15_HO4-->