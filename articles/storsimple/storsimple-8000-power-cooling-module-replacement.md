---
title: Reemplazar un PCM en el dispositivo de la serie StorSimple 8000 | Microsoft Docs
description: "Explica cómo quitar y reemplazar el Módulo de alimentación y refrigeración (PCM) en el dispositivo StorSimple"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 7d181e6e434c998573dbea4b541cfacf7a28ee66
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Reemplazar un Módulo de alimentación y de refrigeración en el dispositivo StorSimple
## <a name="overview"></a>Información general
El Módulo de alimentación y refrigeración (PCM) en el dispositivo StorSimple de Microsoft Azure consta de una fuente de alimentación y ventiladores que se controlan a través de los gabinetes EBOD y principal. Hay un único modelo de PCM que está certificado para cada gabinete. El gabinete principal está certificado para un PCM de 764 W y el gabinete EBOD está certificado para un PCM de 580 W. Aunque los PCM para el gabinete principal y el gabinete EBOD son diferentes, el procedimiento de reemplazo es idéntico.

Este tutorial explica cómo realizar lo siguiente:

* Quitar un PCM
* Instalar un PCM de reemplazo

> [!IMPORTANT]
> Antes de quitar y reemplazar un PCM, revise la información de seguridad en [Reemplazo de componentes de hardware de StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Antes de reemplazar un PCM
Tenga en cuenta los siguientes aspectos importantes antes de reemplazar el PCM:

* Si se produce un error en la fuente de alimentación del PCM, deje instalado el módulo defectuoso, pero quite el cable de alimentación. El ventilador continuará recibiendo  alimentación del gabinete y continuará proporcionando la refrigeración adecuada. Si se produce un error en el ventilador, el PCM debe reemplazarse inmediatamente.
* Antes de quitar el PCM, desconecte la alimentación de PCM desactivando el conmutador principal (si existe) o quitando físicamente el cable de alimentación. Esto proporciona una advertencia al sistema sobre un inminente corte de energía.
* Asegúrese de que el otro PCM funciona para la operación continua del sistema antes de reemplazar el PCM defectuoso. Tan pronto como sea posible, un PCM defectuoso debe sustituirse por un PCM totalmente operativo.
* El reemplazo del módulo del PCM tarda solo algunos minutos en completarse, pero se debe completar en los 10 minutos posteriores a haber quitado el PCM defectuoso para evitar el sobrecalentamiento.
* Tenga en cuenta que los módulos de reemplazo PCM de 764 W que se envían desde fábrica no contienen el módulo de batería de reserva. Tendrá que extraer la batería del PCM defectuoso e insertarla en el módulo de reemplazo antes de realizar el reemplazo. Para obtener más información, consulte cómo [extraer e insertar un módulo de batería de reserva](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Quitar un PCM
Siga estas instrucciones cuando esté preparado para quitar un Módulo de alimentación y refrigeración (PCM) de su dispositivo StorSimple de Microsoft Azure.

> [!NOTE]
> Antes de quitar el PCM, compruebe que tiene un reemplazo correcto (764 W para el gabinete principal) o 580 W para el gabinete EBOD.

#### <a name="to-remove-a-pcm"></a>Para quitar un PCM
1. En el Portal de Azure clásico, haga clic en **Configuración > Supervisar > Estado del hardware**. Compruebe el estado de los componentes de PCM en **Componentes compartidos** para identificar qué PCM ha generado el error:
   
   * Si una fuente de alimentación en PCM 0 está defectuosa, el estado de **Fuente de alimentación en PCM 0** será rojo.
   * Si una fuente de alimentación en PCM 1 está defectuosa, el estado de **Fuente de alimentación en PCM 1** será rojo.
   * Si ha fallado el ventilador de PCM 1, el estado de **Refrigeración 0 para PCM 0** o **Refrigeración 1 para PCM 0** será rojo.
2. Busque el PCM defectuoso en la parte posterior del gabinete principal. Si está ejecutando un modelo 8600, identifique el gabinete principal examinando el número de identificación de unidad de sistema que se muestra en el pantalla LED del panel frontal. El identificador de unidad predeterminado que se muestra en el gabinete principal es **00**, mientras que el de la unidad predeterminada en el receptáculo EBOD es **01**. El diagrama y la tabla siguiente explican el panel frontal de la pantalla LED.
   
    ![Identificador del sistema en el panel frontal OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Figura 1** Panel frontal del dispositivo  
   
   | Etiqueta | Descripción |
   |:--- |:--- |
   | 1 |Botón Silencio |
   | 2 |Alimentación del sistema |
   | 3 |Error de módulo |
   | 4 |Error lógico |
   | 5 |Pantalla de identificación de la unidad |
3. Los LED indicadores de supervisión en la parte posterior del gabinete principal también pueden utilizarse para identificar el PCM defectuoso. Vea la siguiente tabla y diagrama para aprender a usar los LED para localizar el PCM defectuoso. Por ejemplo, si el LED correspondiente a **Error del ventilador** está encendido, el ventilador ha producido un error. Del mismo modo, si el LED correspondiente a **Error de CA** está encendido, el sistema de alimentación ha producido un error. 
   
    ![Backplane de LED indicadores de supervisión del PCM del dispositivo](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Figura 2** Parte posterior del PCM con LED indicadores
   
   | Etiqueta | Descripción |
   |:--- |:--- |
   | 1 |Error de corriente alterna |
   | 2 |Error del ventilador |
   | 3 |Error de la batería |
   | 4 |PCM correcto |
   | 5 |Error de alimentación de CD |
   | 6 |Batería en funcionamiento |
4. Consulte el siguiente diagrama de la parte posterior del dispositivo StorSimple para encontrar el módulo PCM defectuoso. PCM 0 está a la izquierda y PCM 1 está a la derecha. La tabla siguiente explica los módulos.
   
     ![Backplane de módulos del gabinete principal del dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Figura 3** Parte posterior de dispositivo con módulos de complementos 
   
   | Etiqueta | Descripción |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
5. Desactive el PCM defectuoso y desconecte el cable de la fuente de alimentación. Ahora puede quitar el PCM.
6. Sujete el pestillo y el costado del asa del PCM entre el pulgar y el índice, y apriételos para abrir el asa.
   
    ![Abrir el asa del PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Figura 4** Abrir el asa del PCM
7. Sujete el asa y quite el PCM.
   
    ![Quitar PCM del dispositivo](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figura 5** Quitar el PCM

## <a name="install-a-replacement-pcm"></a>Instalar un PCM de reemplazo
Siga estas instrucciones para instalar un PCM en el dispositivo StorSimple. Asegúrese de que insertó el módulo de batería de reserva antes de instalar el PCM de reemplazo (se aplica a los PCM de 764 W) Para obtener más información, consulte cómo [extraer e insertar un módulo de batería de reserva](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar un PCM
1. Compruebe que tiene el PCM de reemplazo correcto para este gabinete. El gabinete principal necesita un PCM de 764 W, y el gabinete EBOD necesita un PCM de 580 W. No debe intentar usar el PCM de 580 W en el gabinete principal o el PCM de 764 W en el gabinete EBOD. La siguiente imagen muestra dónde identificar esta información en la etiqueta que se anexa al PCM.
   
    ![Etiqueta del PCM del dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Figura 6** Etiqueta del PCM
2. Compruebe si hay daños en el gabinete, preste especial atención a los conectores. 
   
   > [!NOTE]
   > **No instale el módulo si las clavijas del conector están dobladas.**
   > 
   > 
3. Con el asa del PCM en posición abierta, deslice el módulo hacia el gabinete.
   
    ![Instalación del PCM del dispositivo](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Figura 7** Instalar el PCM
4. Cierre el asa del PCM manualmente. Oirá un clic cuando el pestillo del asa encaje.
   
   > [!NOTE]
   > Para asegurarse de que han encajado las clavijas del conector, puede tirar suavemente del asa sin liberar el pestillo. Si el PCM se desplaza y sale de su lugar, implica que el pestillo se cerró antes de que encajen los conectores.
   
5. Conecte los cables de alimentación a la fuente de alimentación y el PCM.
6. Proteja las balas de alivio de presión.
7. Encienda el PCM.
8. Compruebe que el reemplazo se realizó correctamente: en Azure Portal del servicio StorSimple Device Manager, vaya al dispositivo y, a continuación, a **Configuración > Supervisar > Estado del hardware**. En **Componentes compartidos**, el estado del PCM debe ser verde.
   
   > [!NOTE]
   > El PCM puede tardar unos minutos para la sustitución para inicializarse completamente.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre el [Reemplazo de los componentes de hardware de StorSimple](storsimple-8000-hardware-component-replacement.md).

