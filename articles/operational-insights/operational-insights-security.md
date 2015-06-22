<properties 
	pageTitle="Seguridad de Visión operativa" 
	description="Visión operativa es un servicio de análisis que permite a los administradores de TI obtener un visión profunda en entornos locales y en la nube. Le permite interactuar con datos de máquina en tiempo real e históricos para desarrollar con rapidez una visión personalizada, y proporciona patrones desarrollados por Microsoft y la comunidad para analizar datos." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>





<h1 id="">Seguridad de Visión operativa</h1>


Microsoft se compromete a proteger su privacidad y sus datos, al tiempo que ofrece software y servicios que le ayuden a administrar la infraestructura de TI de su organización. Reconocemos que cuando confía sus datos a otros usuarios, esa confianza requiere una seguridad rigurosa. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.

La seguridad y la protección de los datos son prioritarias en Microsoft. Póngase en contacto con nosotros si tiene preguntas, sugerencias o problemas acerca de la siguiente información, incluidas nuestras directivas de seguridad: <a href="mailto:scdata@microsoft.com" target="_blank">scdata@microsoft.com</a>.

En este artículo se explica cómo se recopilan los datos, se procesan y se protegen en Visión operativa de Microsoft Azure. Puede usar cualquiera de los agentes para conectarse directamente con el servicio web o puede usar System Center Operations Manager para recopilar datos operativos para el servicio de Visión operativa. Los datos recopilados se envían a través de Internet para el servicio de Visión operativa, que se hospeda en Microsoft Azure.

El servicio de Visión operativa administra sus datos de forma segura mediante el uso de los siguientes métodos:

**Segregación de datos:** los datos de cliente se mantienen separados de forma lógica en cada componente en el servicio de Visión operativa. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio. 

Cada cliente tiene un blob de Azure dedicado que aloja los datos a largo plazo. El blob se cifra con claves por cliente únicas, que se cambian cada 90 días.

**Retención de datos:** las métricas agregadas para cada uno de los módulos de inteligencia se almacenan en una base de datos SQL hospedada por Microsoft Azure. Estos datos se almacenan durante 390 días. Los datos de búsqueda indizados se almacenan durante 10 días de media antes de que se limpien los datos. Si se alcanza antes el límite superior de 20 millones de registros para cada tipo de dato, Visión operativa limpia los datos antes de 10 días. Si no se alcanza el límite de datos antes de los 10 días, Visión operativa espera hasta que se alcanza el límite para limpiarlos.

**Seguridad física:** el servicio de Visión operativa es atendido por el personal de Microsoft y todas las actividades se registran y se pueden auditar. El servicio de Visión operativa se ejecuta completamente en Azure y cumple los criterios de ingeniería comunes de Azure. Puede ver detalles acerca de la seguridad física de los recursos de Azure en la página 18 de la  <a href="http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf" target="_blank">Información general de la información de Microsoft Azure</a>.

**Cumplimiento y certificaciones:** el equipo de servicio y desarrollo de software de Visión operativa trabaja de manera activa con los equipos y cumplimiento y servicio legal de Microsoft y otros socios del sector para adquirir una variedad de certificaciones, incluida ISO, antes de que el servicio de Visión operativa esté generalmente disponible.

Actualmente cumplimos los siguientes estándares de seguridad:

- Criterios de ingeniería comunes de Windows
- Certificación de Microsoft Trustworthy Computing



<!--HONumber=45--> 
 