<properties 
	pageTitle="Guía de solución de problemas: Creación y conexión a un espacio de trabajo de aprendizaje automático de Azure | Azure" 
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
	ms.date="08/25/2014" 
	ms.author="garye"/>


#Guía de solución de problemas: creación y conexión a un espacio de trabajo de aprendizaje automático de Azure

Esta guía proporciona soluciones para algunos de los problemas más comunes al configurar los espacios de trabajo de aprendizaje automático de Azure.

##Propietario del espacio de trabajo

Cuando se crea un nuevo espacio trabajo de aprendizaje automático de Azure, el identificador que especifique en el campo WORKSPACE OWNER debe ser una cuenta válida de Microsoft (anteriormente Windows Live ID), como john-contoso@live.com o john-contoso@hotmail.com. No puede ser una cuenta que no sea de Microsoft, como la cuenta de correo electrónico corporativa. Para crear una cuenta gratuita de Microsoft, vaya a [www.live.com](http://www.live.com). 

##Regiones permitidas

El Aprendizaje automático de Azure actualmente se encuentra en vista previa pública en la región Centro-Sur de EE. UU. Si su suscripción no incluye la región Centro-Sur de EE. UU., es posible que vea el error "No dispone de una suscripción en las regiones permitidas. Regiones permitidas: Centro-Sur de EE. UU." 

Para resolver esto, seleccione "Ponerse en contacto con el soporte técnico de Microsoft" (aparece a continuación) desde su Portal de Azure y seleccione **Facturación** como **TIPO DE SOPORTE** para que se agregue esta región a su suscripción. El Aprendizaje automático de Azure agregará varias regiones con el paso del tiempo.

![Contact Microsoft support][screen1]

##Cuenta de almacenamiento
 
El servicio de Aprendizaje automático de Azure necesita una cuenta de almacenamiento para almacenar datos. Puede utilizar una cuenta de almacenamiento existente en el Centro-Sur de EE. UU. o puede crear una nueva cuenta de almacenamiento al crear el nuevo espacio de trabajo de aprendizaje automático (si dispone de cuota para crear una nueva cuenta de almacenamiento). Para ver si puede crear una nueva cuenta de almacenamiento, en el Portal de Azure vaya a **Configuración** y después a **Uso**.

![Create workspace][screen2]

Tras crear el nuevo espacio de trabajo de Aprendizaje automático, puede iniciar sesión en Estudio de aprendizaje automático con la cuenta de Microsoft que ha especificado como el propietario del espacio de trabajo. Si encuentra un error "Espacio de trabajo no encontrado" parecido a la siguiente captura de pantalla, realice los siguientes pasos para corregir el problema.

![Workspace not found][screen3]

1. Vacíe las cookies del explorador.

	Si utiliza Internet Explorer, haga clic en el botón **Herramientas** en la esquina superior derecha y seleccione **Opciones de Internet**.  

	![Internet options][screen4]

	En la pestaña **General**, haga clic en **Eliminar...**

	![General tab][screen5]

	En el cuadro de diálogo **Eliminar historial del explorador**, asegúrese de que **Cookies y datos de sitios web** está seleccionado y haga clic en **Eliminar**.

	![Delete cookies][screen6]

2. Una vez se hayan vaciado las cookies, reinicie el explorador y, a continuación, vaya a [https://studio.azureml.net](https://studio.azureml.net). Cuando se le solicite un nombre de usuario y contraseña, especifique la misma cuenta de Microsoft que ha escrito como propietario del espacio de trabajo.

Nuestro objetivo es hacer de la experiencia del aprendizaje automático de Azure lo más sencilla y directa posible. Publique cualquier comentario o problema más abajo o en el [Foro de aprendizaje automático de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning) para ayudarnos a ofrecerle un mejor servicio. 

[screen1]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 