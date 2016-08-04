<properties
    pageTitle="Uso de Outlook en Azure RemoteApp | Microsoft Azure" 
    description="Aprenda a configurar y usar Outlook en Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/20/2016"
    ms.author="elizapo" />

# Uso de Microsoft Outlook en Azure RemoteApp

Azure RemoteApp admite Microsoft Outlook O365. Sepa más sobre la manera en que [Office funciona en Azure RemoteApp](remoteapp-officesubscription.md). Hay algunas opciones de configuración recomendadas para Outlook cuando se usa en Azure RemoteApp.

## Modo de almacenamiento en caché
El modo de almacenamiento en caché es una configuración recomendada al usar Outlook en Azure RemoteApp. Al configurar una cuenta de Outlook 2013 para usar el modo caché de Exchange, Outlook 2013 funciona desde una copia local del buzón de Microsoft Exchange del usuario que se almacena en un archivo de datos sin conexión (archivo OST) en el equipo del usuario, junto con la libreta de direcciones sin conexión (OAB). El buzón en la caché y OAB se actualizan periódicamente desde el servicio de Office 365. Conozca mejor [las diferencias entre el modo en caché y en línea](https://technet.microsoft.com/library/jj683103.aspx).

El usuario puede seleccionar **Modo caché de Exchange** o el **Modo en línea** durante la configuración de la cuenta o cambiando la configuración de la cuenta. También puede implementar un modo u otro mediante la herramienta de personalización de Office (OCT) o la directiva de grupo.

Lea las [instrucciones detalladas sobre cómo habilitar el modo en caché](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## Search
En Azure RemoteApp, el uso de la búsqueda dentro de Outlook tiene limitaciones. Azure RemoteApp usa máquinas virtuales agrupadas para adaptarse a las sesiones de usuario. La indexación de la búsqueda depende del id. de máquina, que es diferente para las distintas máquinas virtuales. Es posible que cada vez que un usuario inicie sesión en Azure RemoteApp, se dirija a una nueva máquina virtual. Eso significaría que, si se habilitase la búsqueda local, el indizador se ejecutaría cada vez que cambiase el id. de máquina (cuando el usuario estuviese en una máquina virtual diferente). En función del tamaño del archivo .OST, el indexador podría tardar mucho tiempo en completarse y usar los recursos necesarios para otras aplicaciones. La búsqueda no solo sería lenta, sino que quizás tampoco daría resultados. Mediante un perfil de cuenta del modo en línea podría solucionar este problema, pero el rendimiento general sufriría debido a la falta de una caché local (consulte el vínculo de arriba para más información acerca de la diferencia entre el modo en caché y en línea). Lamentablemente, no se puede deshabilitar la búsqueda local o indexada y no se puede habilitar la búsqueda en línea de forma predeterminada en Outlook 2013.

Outlook 2016 tiene una solución para abordar esta necesidad en el modo en caché, ofreciendo una nueva experiencia de búsqueda de servicios en línea para buzones hospedados en Exchange 2016 (o en Office 365). Esta usa los resultados de la búsqueda de servicios en la memoria caché local (OST). Outlook podría recurrir al uso del indexador de búsqueda local en algunos escenarios, pero la mayoría de las búsquedas usaría esta nueva característica de búsqueda de servicios. La recomendación de Azure RemoteApp sería usar Outlook 2016 si la búsqueda de correo es un escenario muy importante.

<!---HONumber=AcomDC_0727_2016-->