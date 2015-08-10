<properties 
   pageTitle="Conector de espera" 
   description="Conector de espera" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/29/2015"
   ms.author="rajram"/>

#Conector de espera
El conector de espera permite que una aplicación retrase su ejecución durante un período de tiempo especificado o hasta la aparición de un tiempo especificado. Cuando se emplea en un flujo, se puede usar para retrasar la ejecución.

##Uso del conector de espera
Para usar el conector espera, deberá crear primero una instancia de la aplicación de API del conector de espera. Esta tarea puede realizarse en línea mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del conector de espera en Azure Marketplace.

##Uso del conector de espera en la superficie del diseñador de aplicaciones lógicas
El conector de espera se puede usar como una acción. No tiene ningún desencadenador.

###Acción
- Haga clic en el conector de espera en el panel derecho.

	![Lista de acciones][1]
- El conector de espera admite dos acciones: 
	- Retraso
	- Retrasar hasta
	 
- Seleccione *Retraso*.

	![Entrada de retraso][2]
- Proporcione las entradas para la acción y configúrela.

	![Acción configurada][3]

<table>
	<tr>
		<th>Parámetro</th>
		<th>Tipo</th>
		<th>Descripción del parámetro</th>
	</tr>
	<tr>
		<td>Duración en minutos</td>
		<td>integer</td>
		<td>Duración del retraso en minutos</td>
	</tr>
</table>


## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=July15_HO5-->