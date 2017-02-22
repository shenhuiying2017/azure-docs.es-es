---
title: "Introducción a Microsoft Azure StorSimple Data Manager | Microsoft Docs"
description: "Proporciona información general sobre el servicio StorSimple Data Manager (versión preliminar privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 32fcca540c1db317c5f11e94b2ee3359cadcc806
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73

---

# <a name="storsimple-data-manager-overview-private-preview"></a>Información general sobre el servicio StorSimple Data Manager (versión preliminar privada)

## <a name="overview"></a>Información general

Microsoft Azure StorSimple es una solución de almacenamiento en la nube híbrida que aborda las dificultades de los datos no estructurados asociados normalmente a recursos compartidos de archivos. StorSimple usa el almacenamiento en la nube como una extensión de la solución local y organiza automáticamente los datos en niveles entre el almacenamiento local y el almacenamiento en la nube. La protección de datos integrada, con instantáneas locales y de nube, elimina la necesidad de una extensa infraestructura de almacenamiento. Tanto el archivo como la recuperación ante desastres también se realizan sin problemas, ya que la nube que actúa como una ubicación fuera del sitio.

El servicio de transformación de datos que se presenta en este documento permite acceder sin problemas a los datos de StorSimple en la nube. Este servicio proporciona varias API que extraen datos de StorSimple y los presentan a otros servicios de Azure en formatos listos para consumirse. Los formatos que se admiten en esta versión preliminar son blobs de Azure y recursos de Azure Media Services. Esta transformación permite conectar fácilmente servicios como Azure Media Services, Azure HDInsight, Azure Machine Learning y Azure Search para utilizar datos en un dispositivo local de la serie StorSimple 8000.

A continuación se muestra un diagrama de bloques de alto nivel que lo ilustra.

![Diagrama de alto nivel](./media//storsimple-data-manager-overview/high-level-diagram.png)

En este documento se explica cómo puede suscribirse a una versión preliminar privada de este servicio. También se explica cómo puede usar este servicio para escribir aplicaciones que usen datos de StorSimple y otros servicios de Azure en la nube.

## <a name="sign-up-for-data-manager-preview"></a>Suscripción para una versión preliminar de Data Manager
Antes de registrarse para el servicio Data Manager, consulte los siguientes requisitos previos.

### <a name="prerequisites"></a>Requisitos previos

En este ejercicio se supone que dispone de:
* una suscripción a Azure activa
* acceso a un dispositivo registrado de la serie StorSimple 8000
* todas las claves asociadas al dispositivo de la serie StorSimple 8000

### <a name="sign-up"></a>Suscripción

StorSimple Data Manager está en versión preliminar privada. Realice los siguientes pasos para registrarse para obtener una versión preliminar privada de este servicio:

1.  Inicie sesión en Azure Portal con la extensión de StorSimple Data Manager en: [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager). Use sus credenciales de Azure para iniciar sesión.

2.  Haga clic en el icono **+** para crear un servicio. Haga clic en **Almacenamiento** y, después, en **See All** (Ver todos) en la hoja que se abre.

    ![Buscar icono de StorSimple Data Manager](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. Verá el icono de StorSimple Data Manager.

    ![Seleccionar icono de StorSimple Data Manager](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. Haga clic en el icono de StorSimple Data Manager y, después, en **Create** (Crear). Seleccione la suscripción en la que desea habilitar la versión preliminar y haga clic en **Sign me up!** (Suscribirme)

    ![Suscribirme](./media/storsimple-data-manager-overview/sign-me-up.png)

5. Así se envía una solicitud de incorporación. Dicha incorporación se realizará lo antes posible. Una vez habilitada la suscripción, ya se puede crear un servicio StorSimple Data Manager.

6. Para acceder fácilmente al servicio StorSimple Data Manager, haga clic en el icono de la estrella para anclarlo a sus favoritos.

    ![Acceder a StorSimple Data Manager](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>Pasos siguientes

[Manage using the StorSimple Data Manager service UI](storsimple-data-manager-ui.md) (Administración del uso de la interfaz de usuario del servicio StorSimple Data Manager).


<!--HONumber=Nov16_HO4-->


