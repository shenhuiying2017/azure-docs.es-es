<properties 
	pageTitle="Solución de problemas: Creación de un área de trabajo de Aprendizaje automático y conexión a la misma | Microsoft Azure" 
	description="Soluciones para problemas comunes al crear y conectarse a un área de trabajo de aprendizaje automático de Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="garye"/>


#Guía de solución de problemas: Creación de un área de trabajo de Aprendizaje automático y conexión a la misma

Esta guía proporciona soluciones para algunos de los desafíos más comunes al configurar áreas de trabajo de Aprendizaje automático de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Propietario del espacio de trabajo

Cuando se crea un área de trabajo de Aprendizaje automático nueva, el identificador que escribe en el campo PROPIETARIO DEL ÁREA DE TRABAJO debe ser una cuenta de Microsoft válida (anteriormente Windows Live ID), por ejemplo, john-contoso@live.com o john-contoso@hotmail.com. No puede ser una cuenta que no pertenezca a Microsoft, como su cuenta de correo electrónico corporativa. Para crear una cuenta gratuita de Microsoft, vaya a [www.live.com](http://www.live.com).

Observe que la cuenta que usó para iniciar sesión en el portal de Azure y crear el área de trabajo no tiene automáticamente permiso para abrir esa área de trabajo, a menos que especifique esa cuenta como propietaria. Para abrir un área de trabajo en Estudio de aprendizaje automático, debe iniciar sesión en la cuenta de Microsoft que se definió como propietaria del área de trabajo, o bien, puede ser necesario que reciba una invitación del propietario para unirse al área de trabajo. Sin embargo, en el portal de Azure puede *administrar* el área de trabajo, lo que incluye la capacidad de cambiar el propietario y configurar el acceso.

Para obtener más información sobre cómo administrar un área de trabajo, consulte [Administración de un área de trabajo de Aprendizaje automático de Azure].

[Administración de un área de trabajo de Aprendizaje automático de Azure]: machine-learning-manage-workspace.md

##Regiones permitidas

Aprendizaje automático actualmente se encuentra disponible en la región centro y sur de EE. UU. Si su suscripción no incluye la región Centro y sur de EE. UU., es posible que vea el mensaje de error "No dispone de una suscripción en las regiones permitidas. Regiones permitidas: centro y sur de EE. UU".

Para solucionar esto, tal como aparece en la siguiente captura de pantalla, seleccione **Consultar al soporte técnico de Microsoft** en el Portal de administración de Azure y elija **Facturación** como el **TIPO DE SOPORTE TÉCNICO** para solicitar que esta región se agregue a su suscripción.

![Consultar al soporte técnico de Microsoft][screen1]

##Cuenta de almacenamiento
 
El servicio de Aprendizaje automático necesita una cuenta de almacenamiento para almacenar los datos. Puede utilizar una cuenta de almacenamiento existente en la región del centro y sur de EE. UU. o puede crear una nueva cuenta de almacenamiento al crear la nueva área de trabajo de Aprendizaje automático (si dispone de cuota para crear una nueva cuenta de almacenamiento). Para saber si puede crear una nueva cuenta de almacenamiento, en el Portal de administración vaya a **Configuración** y, a continuación, haga clic en **Uso**.

![Creación del espacio de trabajo][screen2]

Tras crear la nueva área de trabajo de Aprendizaje automático, puede iniciar sesión en Estudio de aprendizaje automático con la cuenta de Microsoft que especificó como propietaria del área de trabajo. Si encuentra el mensaje de error "No se encontró el área de trabajo" (similar a la captura de pantalla siguiente), use estos pasos para eliminar las cookies del explorador.

![Espacio de trabajo no encontrado][screen3]

**Para eliminar las cookies del explorador**

	If you use Internet Explorer, click the **Tools** button in the upper-right corner and select **Internet options**.  

	![Internet options][screen4]

	Under the **General** tab, click **Delete…**

	![General tab][screen5]

	In the **Delete Browsing History** dialog box, make sure **Cookies and website data** is selected, and click **Delete**.

	![Delete cookies][screen6]

Una vez eliminadas las cookies, reinicie el explorador y vaya a la página [Aprendizaje automático de Microsoft Azure](https://studio.azureml.net). Cuando se le solicite un nombre de usuario y contraseña, especifique la misma cuenta de Microsoft que ha escrito como propietario del espacio de trabajo.

Nuestro objeto es hacer que la experiencia de Aprendizaje automático sea lo más directa posible. Publique cualquier comentario o problema en el [foro de Aprendizaje automático de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para ayudarnos a ofrecerle un mejor servicio.

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
 

<!---HONumber=July15_HO1-->