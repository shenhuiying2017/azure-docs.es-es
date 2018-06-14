---
title: Aprovisionamiento de una máquina virtual para inteligencia artificial y análisis geoespacial en Azure - Azure | Microsoft Docs
description: Aprovisionamiento de una máquina virtual para inteligencia artificial y análisis geoespacial en Azure.
keywords: aprendizaje profundo, IA, herramientas de ciencia de datos, máquina virtual de ciencia de datos, análisis geoespacial
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 93dfe6594aeaf45a6905fe8cb55c98dd37cc9599
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408615"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Aprovisionamiento de una máquina virtual para inteligencia artificial y análisis geoespacial en Azure 

La máquina virtual Geo Artificial Intelligence Data Science Virtual Machine (Geo-DSVM) es una extensión de la conocida [Azure Data Science Virtual Machine](http://aka.ms/dsvm) que se ha configurado especialmente para combinar la inteligencia artificial con el análisis geoespacial. Los análisis geoespaciales en la máquina virtual se realizan con tecnología de [ArcGIS Pro](https://www.arcgis.com/features/index.html). La máquina virtual de ciencia de datos permite la formación rápida de modelos de aprendizaje automático e incluso de modelos de aprendizaje profundo con datos enriquecidos con información geográfica. Solo es compatible en DSVM con Windows 2016. 

La máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial contiene varias herramientas para inteligencia artificial, entre las que se incluyen las siguientes:

- ediciones de GPU de plataformas de aprendizaje profundo populares, como Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; 
- herramientas para adquirir y procesar previamente imágenes y datos de texto; 
- herramientas de actividades de desarrollo como Microsoft R Server Developer Edition, Anaconda Python, Jupyter Notebook para Python y R, IDE para Python y R y bases de datos SQL;
- software de escritorio ArcGIS Pro de ESRI junto con interfaces de Python y R que pueden trabajar con datos geoespaciales de sus aplicaciones de inteligencia artificial. 


## <a name="create-your-geo-ai-data-science-vm"></a>Creación de una máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial

A continuación, se describe el procedimiento para crear una instancia de la máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial: 


1. Navegue a la lista de máquinas virtuales en [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Seleccione el botón **Crear**, en la parte inferior, para acceder a un asistente.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. El asistente usado para crear la instancia de Geo-DSVM necesita **datos de entrada** para cada uno de los **cuatro pasos** que se enumeran en la parte derecha de esta ilustración. Estas son las entradas necesarias para configurar cada uno de estos pasos:



   - **Aspectos básicos**

      1. **Nombre**: nombre del servidor de ciencia de datos que está creando.

      2. **Nombre de usuario**: identificador de inicio de sesión de la cuenta del administrador.

      3. **Contraseña**: contraseña de la cuenta del administrador.

      4. **Suscripción**: si tiene más de una suscripción, seleccione aquella en la que se creará y facturará la máquina.

      5. **Grupo de recursos**: puede crear uno nuevo o usar un grupo de recursos **vacío** existente de Azure en su suscripción.

      6. **Ubicación**: seleccione el centro de datos más adecuado. Normalmente es el centro de datos que tenga la mayoría de los datos o que esté más cercano a su ubicación física para un acceso más rápido a la red. Si necesita realizar aprendizaje profundo en GPU, debe seleccionar una de las ubicaciones de Azure que tiene las instancias de máquina virtual de GPU de la serie NC. Actualmente las ubicaciones que tienen máquinas virtuales de GPU son: **Este de EE. UU., Centro y norte de EE. UU., Centro y Sur de EE. UU., Oeste de EE.UU. 2, Europa del Norte, Europa Occidental**. Para obtener la lista más reciente, compruebe la página de [productos de Azure por región](https://azure.microsoft.com/regions/services/) y busque **Serie NC** en **Proceso**. 


   - **Configuración**: seleccione un tamaño de máquina virtual de GPU de la serie NC si tiene previsto ejecutar aprendizaje profundo en GPU en su instancia de Geo-DSVM. En caso contrario, puede elegir una de las instancias basadas en CPU.  Cree una cuenta de almacenamiento para su máquina virtual. 
   
   - **Resumen**: Compruebe que toda la información que ha especificado es correcta.

   - **Compra**: haga clic en **Comprar** para iniciar el aprovisionamiento. Se proporciona un vínculo a los términos del servicio. La máquina virtual no tiene ningún cargo adicional más allá del proceso para el tamaño del servidor que eligió en el paso **Tamaño** . 

>[!NOTE]
> El aprovisionamiento tardará entre 20 y 30 minutos. El estado del aprovisionamiento se muestra en el Portal de Azure.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Acceso a la máquina virtual Geo Artificial Intelligence Data Science Virtual Machine

Una vez creada la máquina virtual, podrá empezar a usar las herramientas que tiene instaladas y preconfiguradas. Hay iconos del menú de inicio e iconos del escritorio para muchas de las herramientas. Puede usar el escritorio remoto con las credenciales de la cuenta del administrador que configuró en la sección **Aspectos básicos** anterior. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>Uso de ArcGIS Pro instalado en la máquina virtual

La instancia de Geo-DSVM ya tiene el software de escritorio ArcGIS Pro preinstalado y el entorno preconfigurado para funcionar con todas las herramientas de DSVM. Al iniciar ArcGIS, se solicitan los datos de inicio de sesión a su cuenta de ArcGIS. Si ya tiene una cuenta de ArcGIS y tiene licencias para el software, puede usar sus credenciales existentes.  

![Inicio de sesión-Arc-GIS](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

En caso contrario, puede registrar una nueva cuenta y licencia de ArcGIS u obtener una [evaluación gratuita](https://www.arcgis.com/features/free-trial.html). 

![Evaluación-gratuita-ArcGIS](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Una vez registrada su cuenta de evaluación gratuita o de pago de ArcGIS, puede autorizar a ArcGIS Pro para su cuenta mediante las instrucciones de [Getting Started with ArcGIS Pro documentation](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf) (Introducción a la documentación de ArcGIS Pro). 

Después de iniciar sesión en el software de escritorio ArcGIS Pro con su cuenta de ArcGIS, podrá empezar a usar las herramientas de ciencia de datos instaladas y configuradas en la máquina virtual para sus proyectos de análisis geoespacial y de aprendizaje automático.

## <a name="next-steps"></a>Pasos siguientes

Empiece a usar la máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial con la guía de los temas siguientes:

* [Use the Geo AI Data Science VM](use-geo-ai-dsvm.md) (Uso de la máquina virtual de ciencia de datos para inteligencia artificial y análisis geoespacial)