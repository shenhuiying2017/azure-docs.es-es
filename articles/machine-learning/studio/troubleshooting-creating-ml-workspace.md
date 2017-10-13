---
title: "Solución de problemas: Creación de un área de trabajo de Machine Learning y conexión a la misma | Microsoft Docs"
description: "Soluciones para problemas comunes al crear y conectarse a un área de trabajo de aprendizaje automático de Azure"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: cdccd4ce7b87f47d21578076653bde901748188b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guía de solución de problemas: Creación de un área de trabajo de Aprendizaje automático y conexión a la misma
Esta guía proporciona soluciones para algunos de los desafíos más comunes al configurar áreas de trabajo de Aprendizaje automático de Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Propietario del espacio de trabajo
Para abrir un área de trabajo en Machine Learning Studio, debe iniciar sesión en la cuenta Microsoft que utilizó para crear el área de trabajo, o bien, puede ser necesario que reciba una invitación del propietario para unirse al área de trabajo. Desde Azure Portal puede administrar el área de trabajo, lo que incluye la capacidad de configurar el acceso.

Para obtener más información sobre cómo administrar un área de trabajo, consulte [Administración de un área de trabajo de Aprendizaje automático de Azure].

[Administración de un área de trabajo de Aprendizaje automático de Azure]: manage-workspace.md

## <a name="allowed-regions"></a>Regiones permitidas
Aprendizaje automático se encuentra actualmente disponible en un número limitado de regiones. Si su suscripción no incluye una de estas regiones, es posible que vea el mensaje de error "No dispone de una suscripción en las regiones permitidas".

Para solicitar que una región se agregue a su suscripción, cree una nueva solicitud de soporte técnico de Microsoft desde Azure Portal, elija **Facturación** como el tipo de problema y siga las indicaciones para enviar su solicitud.

## <a name="storage-account"></a>Cuenta de almacenamiento
El servicio de Aprendizaje automático necesita una cuenta de almacenamiento para almacenar los datos. Puede utilizar una cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento al crear la nueva área de trabajo de Aprendizaje automático (si dispone de cuota para crear una nueva cuenta de almacenamiento).

Tras crear la nueva área de trabajo de Machine Learning, puede iniciar sesión en Machine Learning Studio con la cuenta de Microsoft que usó para crear el área de trabajo. Si encuentra el mensaje de error "No se encontró el área de trabajo" (similar a la captura de pantalla siguiente), use estos pasos para eliminar las cookies del explorador.

![Espacio de trabajo no encontrado][screen3]

**Para eliminar las cookies del explorador**

1. Si usa Internet Explorer, haga clic en el botón **Herramientas** en la esquina superior derecha y seleccione **Opciones de Internet**.  

![Opciones de Internet][screen4]

2. En la pestaña **General**, haga clic en **Eliminar…**

![Pestaña General][screen5]

3. En el cuadro de diálogo **Eliminar el historial de exploración**, asegúrese de que **Cookies y datos del sitio web** está seleccionado y haga clic en **Eliminar**.

![Eliminación de cookies][screen6]

Una vez eliminadas las cookies, reinicie el explorador y vaya a la página [Aprendizaje automático de Microsoft Azure](https://studio.azureml.net) . Cuando se le solicite un nombre de usuario y contraseña, especifique la misma cuenta Microsoft que usó para crear el área de trabajo.

## <a name="comments"></a>Comentarios

Nuestro objeto es hacer que la experiencia de Aprendizaje automático sea lo más directa posible. Publique cualquier comentario o problema en el [foro de Aprendizaje automático de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para ayudarnos a ofrecerle un mejor servicio.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
