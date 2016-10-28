<properties
   pageTitle="Protección del servicio SQL de Azure en Azure Security Center | Microsoft Azure"
   description="En este documento se explica cómo las recomendaciones de Azure Security Center ayudan a proteger el servicio SQL de Azure y a cumplir las directivas de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# Protección del servicio SQL de Azure en Azure Security Center

El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, crea recomendaciones que lo guiarán por el proceso de configuración de los controles necesarios. Las recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales, redes, SQL y aplicaciones.

Este artículo aborda las recomendaciones sobre el servicio SQL de Azure. Las recomendaciones del servicio SQL de Azure se centran en la habilitación de la auditoría de servidores y bases de datos SQL de Azure y del cifrado de bases de datos SQL. Use la tabla siguiente como referencia para ayudarlo a entender las recomendaciones disponibles sobre el servicio SQL y lo que harán cada una de ellas si se aplican.

## Recomendaciones sobre el servicio SQL disponibles

|Recomendación|Description|
|-----|-----|
|[Habilitar Auditoría SQL de servidor](security-center-enable-auditing-on-sql-servers.md)|Recomienda activar la auditoría de servidores SQL Server de Azure (solo el servicio SQL de Azure; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales).|
|[Habilitar Auditoría SQL de base de datos](security-center-enable-auditing-on-sql-databases.md)|Recomienda activar la auditoría de bases de datos SQL de Azure (solo el servicio SQL de Azure; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales).|
|[Habilitar Cifrado de datos transparente en bases de datos SQL](security-center-enable-transparent-data-encryption.md)|Recomienda habilitar el cifrado de bases de datos SQL (solo el servicio SQL de Azure).|

## Otras referencias

Para obtener más información sobre las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte los siguientes artículos:

- [Protección de las máquinas virtuales en Azure Security Center](security-center-virtual-machine-recommendations.md)
- [Protección de las aplicaciones en Azure Security Center](security-center-application-recommendations.md)
- [Protección de las redes en Azure Security Center](security-center-network-recommendations.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.

<!---HONumber=AcomDC_0810_2016-->