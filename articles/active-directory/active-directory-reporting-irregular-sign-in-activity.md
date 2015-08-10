<properties pageTitle="Actividad de inicio de sesión irregular" description="Un informe que incluye inicios de sesión que se han identificado como anómalos por nuestros algoritmos de aprendizaje automático."" services="active-directory" documentationCenter="" authors="kenhoff" manager="ilanas" editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="kenhoff"/>

# Actividad de inicio de sesión irregular

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Este informe incluye inicios de sesión que nuestros algoritmos de aprendizaje automático han identificado como "erróneos". Los motivos para marcar como irregular un intento de inicio de sesión incluyen ubicaciones de inicio de sesión inesperadas, la hora del día y las ubicaciones o una combinación de ambas cosas. Esto puede indicar que un hacker ha intentado iniciar sesión con esta cuenta. El algoritmo de aprendizaje automático clasifica los eventos como "erróneos" o "sospechosos", donde "sospechoso" indica una mayor probabilidad de una infracción de seguridad.</p><p>Los resultados de este informe mostrarán estos inicios de sesión, junto con la clasificación, la ubicación y una marca de tiempo asociada a cada inicio de sesión.</p><p>Enviaremos una notificación por correo electrónico a los administradores globales si encontramos 10 o más eventos de inicio de sesión erróneo dentro de un intervalo de 30 días o menos. Asegúrese de incluir aad-alerts-noreply@mail.windowsazure.com en la lista de remitentes seguros.</p> | Directorio > pestaña Informes |

<!---HONumber=July15_HO5-->