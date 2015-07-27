<properties pageTitle="Inicios de sesión desde varias ubicaciones geográficas" description="Un informe que indica a los usuarios dónde parece que dos inicios de sesión se originaron desde distintas regiones y que, según el tiempo pasado entre los inicios de sesión, parece imposible que el usuario haya viajado entre dichas regiones."" services="active-directory" documentationCenter="" authors="kenhoff" manager="ilanas" editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="kenhoff"/>

# Inicios de sesión desde varias ubicaciones geográficas

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Este informe incluye actividades de inicio de sesión correcto de un usuario en las que parece que dos inicios de sesión se originaron desde distintas regiones y que, según el tiempo pasado entre los inicios de sesión, parece imposible que el usuario haya viajado entre dichas regiones. Las causas posibles son:</p><ul><li>El usuario comparte su contraseña</li><li>El usuario está usando un escritorio remoto para iniciar un explorador web para el inicio de sesión</li><li>Un hacker ha iniciado sesión en la cuenta de un usuario desde otro país.</li></ul><p>Los resultados de este informe mostrarán los eventos de inicio de sesión correcto, así como el tiempo entre los inicios de sesión, las regiones desde donde parece que se originaron los inicios de sesión y el tiempo estimado de viaje entre las regiones.</p><p>El tiempo de viaje mostrado solo es una estimación y puede ser distinto del tiempo de viaje real entre las ubicaciones. Además, no se genera ningún evento para inicios de sesión entre regiones vecinas.</p> | Directorio > pestaña Informes |

![Inicios de sesión desde varias ubicaciones geográficas](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=July15_HO3-->