<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace | Azure" description="Solutions for common issues in creating and connecting to an Azure Machine Learning workspace " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

# Guía de solución de problemas: creación y conexión a un espacio de trabajo de aprendizaje automático de Azure

Esta guía proporciona soluciones para algunos de los problemas más comunes al configurar los espacios de trabajo de aprendizaje automático de Azure.

## Propietario del espacio de trabajo

Cuando crea un nuevo espacio de trabajo de aprendizaje automático de Azure, el identificador que especifica en el campo PROPIETARIO DEL ESPACIO DE TRABAJO debe ser una cuenta de Microsoft válida (anteriormente Windows Live ID), como <john-contoso@live.com> o <john-contoso@hotmail.com>. No puede ser una cuenta que no pertenezca a Microsoft, como su cuenta de correo electrónico corporativa. Para crear una cuenta gratuita de Microsoft, vaya a [www.live.com][www.live.com].

## Regiones permitidas

El aprendizaje automático de Azure actualmente se encuentra en vista previa pública en la región Centro-Sur de EE. UU. Si su suscripción no incluye la región Centro-Sur de EE. UU., es posible que vea el error "No dispone de una suscripción en las regiones permitidas. Regiones permitidas: Centro-Sur de EE. UU."

Para resolver esto, seleccione "Consultar al soporte técnico de Microsoft" (aparece a continuación) desde su Portal de Azure y seleccione **Facturación** como el **TIPO DE SOPORTE** para que se agregue esta región a su suscripción. El aprendizaje automático de Azure agregará varias regiones con el paso del tiempo.

![Consultar al soporte técnico de Microsoft][Consultar al soporte técnico de Microsoft]

## Cuenta de almacenamiento

El servicio de aprendizaje automático de Azure necesita una cuenta de almacenamiento para almacenar datos. Puede utilizar una cuenta de almacenamiento existente en el Centro-Sur de EE. UU. o puede crear una nueva cuenta de almacenamiento al crear el nuevo espacio de trabajo de aprendizaje automático (si dispone de cuota para crear una nueva cuenta de almacenamiento). Para ver si puede crear una nueva cuenta de almacenamiento, en el Portal de Azure vaya a **Configuración** después a **Uso**.

![Creación del espacio de trabajo][Creación del espacio de trabajo]

Tras crear el nuevo Espacio de trabajo de aprendizaje automático, puede iniciar sesión en el Estudio de aprendizaje automático con la cuenta de Microsoft que ha especificado como el propietario del espacio de trabajo. Si encuentra el error, "Espacio de trabajo no encontrado" parecido a la siguiente captura de pantalla, realice los siguientes pasos para corregir el problema.

![Espacio de trabajo no encontrado][Espacio de trabajo no encontrado]

1.  Vacíe las cookies del explorador.

    Si utiliza Internet Explorer, haga clic en el botón **Herramientas** en la esquina superior derecha y seleccione **Opciones de Internet**.

    ![Opciones de Internet][Opciones de Internet]

    En la pestaña **General**, haga clic en **Eliminar…**

    ![Pestaña General][Pestaña General]

    En el cuadro de diálogo **Eliminar historial del explorador**, asegúrese de que **Cookies y datos de sitios web** está seleccionado y haga clic en **Eliminar**.

    ![Eliminación de cookies][Eliminación de cookies]

2.  Una vez se hayan vaciado las cookies, reinicie el explorador y, a continuación, vaya a [][]<https://studio.azureml.net></a>. Cuando se le solicite un nombre de usuario y contraseña, especifique la misma cuenta de Microsoft que ha escrito como el propietario del espacio de trabajo.

Nuestro objetivo es hacer de la experiencia del aprendizaje automático de Azure lo más sencilla y directa posible. Publique cualquier comentario o problema más abajo o en el [Foro de aprendizaje automático de Azure][Foro de aprendizaje automático de Azure] para ayudarnos a ofrecerle un mejor servicio.

  [www.live.com]: http://www.live.com
  [Consultar al soporte técnico de Microsoft]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
  [Creación del espacio de trabajo]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
  [Espacio de trabajo no encontrado]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
  [Opciones de Internet]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
  [Pestaña General]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
  [Eliminación de cookies]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
  []: https://studio.azureml.net
  [Foro de aprendizaje automático de Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/es-es/home?forum=MachineLearning
