---
title: Creación de una revisión de acceso para los miembros de un grupo o los usuarios con acceso a una aplicación con Azure AD | Microsoft Docs
description: Aprenda a crear una revisión de acceso para los miembros de un grupo o los usuarios con acceso a una aplicación.
services: active-directory
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: billmath
ms.openlocfilehash: 784a461421420af403a43f944d6f63aef3ccc152
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195614"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Creación de una revisión de acceso de los miembros de un grupo o el acceso a la aplicación con Azure AD

Las asignaciones de roles se convierten en "obsoletas" cuando los usuarios tienen acceso con privilegios que ya no necesitan. Para reducir el riesgo derivado de las asignaciones de acceso obsoletas, los administradores pueden usar Azure Active Directory (Azure AD) para crear una revisión de acceso para los miembros de grupo o usuarios asignados a una aplicación. La creación de revisiones de acceso periódicas puede ahorrar tiempo. Si necesita revisar periódicamente los usuarios que tienen acceso a una aplicación o son miembros de un grupo, puede definir la frecuencia de las revisiones. Para más información sobre estos escenarios, vea [Administración del acceso de los usuarios con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) y [Administración del acceso de los invitados con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Creación de una revisión de acceso

1. Como administrador global, vaya a la [página de revisiones de acceso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) y seleccione **Programas**.

2. Seleccione el programa que contiene el control de revisión de acceso que desee crear. El **Programa predeterminado** siempre está presente, o puede crear otro. Por ejemplo, puede elegir tener un programa cada para iniciativa de cumplimiento de normas u objetivo empresarial.

3. En el programa, seleccione **Controles** y, a continuación, seleccione **Agregar** para agregar un control.

4. Ponga un nombre a la revisión de acceso. Opcionalmente, asigne a la revisión una descripción. El nombre y la descripción se muestran a los revisores.

5. Establezca la fecha de inicio. De forma predeterminada, una revisión de acceso ocurre una vez, se inicia a la misma hora en que se crea y finaliza en un mes. Puede cambiar las fechas de inicio y de finalización para hacer que una revisión de acceso se inicie en el futuro, transcurridos tantos días como desee.

6. Para que la revisión de acceso sea periódica, cambie la frecuencia de Una vez a Semanal, Mensual, Trimestral o Anual, y utilice el cuadro de texto o el control deslizante para definir el número de días que cada revisión de la serie periódica estará abierta para las aportaciones de los revisores. Por ejemplo, la duración máxima que puede establecer para una revisión mensual es 27 días, para evitar la superposición de revisiones. 

7.  Puede terminar la serie de revisiones de acceso periódicas de tres formas: se ejecuta continuamente para iniciar revisiones indefinidamente, o hasta una fecha concreta, o hasta que se haya completado un número definido de veces. Usted u otro administrador global puede detener la serie después de su creación cambiando la fecha en la configuración, de manera que termine en esa fecha.

8. Las revisiones de acceso pueden ser de los miembros de un grupo o de los usuarios que se hayan asignado a una aplicación. Puede limitar aún más el ámbito de la revisión de acceso para revisar solo los usuarios invitados que sean miembros (o se hayan asignado a la aplicación), en lugar de revisar todos los usuarios miembros o que tengan acceso a la aplicación.

9. Seleccione una o más personas para revisar todos los usuarios en el ámbito. También puede seleccionar que los miembros revisen su propio acceso. Si el recurso es un grupo, puede pedir a sus propietarios que hagan la revisión. También puede requerir que los revisores proporcionen un motivo cuando aprueben el acceso.

10. Si desea aplicar manualmente los resultados cuando se complete la revisión, haga clic en **Iniciar**.  En caso contrario, en la sección siguiente se explica cómo configurar la revisión para que se aplique automáticamente.

### <a name="configuring-an-access-review-with-auto-apply"></a>Configuración de una revisión de acceso con aplicación automática

1.  Expanda el menú de Configuración de finalización y active Aplicar automáticamente los resultados al recurso. 

2.  En los casos donde los usuarios no se revisaron por el revisor dentro del período de revisión, puede hacer que la revisión de acceso adopte la recomendación del sistema (si esta opción está habilitada) sobre la denegación o aprobación del acceso continuado del usuario, dejar su acceso sin cambios o revocar el acceso. Esto no afectará a los usuarios que hayan sido revisados por los revisores manualmente: si la decisión final del revisor es Denegar, el acceso del usuario se revocará.

3.  Para habilitar la opción de adoptar las recomendaciones en caso de que los revisores no respondan, debe estar habilitado Mostrar recomendaciones en la configuración avanzada.
 
4.  Finalmente, haga clic en **Iniciar**.

Según las selecciones de la Configuración de finalización, la aplicación automática se ejecutará después de la fecha de finalización de la revisión o cuando se detenga manualmente la revisión. El estado de la revisión cambiará de Completado a estados intermedios como Aplicando y, por último, Aplicado. Debería esperar ver a los usuarios denegados, si es que los hay, eliminados de la pertenencia al grupo o la asignación de aplicaciones en unos minutos.


## <a name="manage-the-access-review"></a>Administración de la revisión de acceso

De forma predeterminada, Azure AD envía un correo electrónico a los revisores poco después de iniciar la revisión. Si decide no hacer que Azure AD envíe el correo electrónico, asegúrese de informar a los revisores de que hay una revisión de acceso esperando para que la lleven a cabo. Puede mostrarles las instrucciones sobre cómo [revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md). Si la revisión es para que los invitados revisen su acceso, muéstreles las instrucciones sobre cómo [revisar su propio acceso](active-directory-azure-ad-controls-perform-access-review.md).

Si alguno de los revisores es un invitado, solo se notificará a los invitados por correo electrónico si ya han aceptado su invitación.

Para administrar una serie de revisiones de acceso, vaya a la revisión de acceso de **Controles** y verá los próximos eventos en Revisión programada; ahí podrá editar la fecha de finalización o agregar o quitar revisores según corresponda. 

Puede seguir el progreso de las revisiones a medida que los revisores las realizan en el panel de Azure AD, en la sección **Revisiones de acceso**. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Pasos siguientes

Cuando se inicie una revisión de acceso, Azure AD enviará automáticamente a los revisores un correo electrónico solicitándoles que revisen el acceso. Si un usuario no recibió un correo electrónico, puede enviarle las instrucciones que se indican en el articulo sobre [cómo revisar el acceso](active-directory-azure-ad-controls-perform-access-review.md). 

Si se trata de una revisión puntual, una vez finalizado el período de revisión de acceso o cuando el administrador detenga la revisión de acceso, siga los pasos sobre [cómo realizar una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md) para ver los resultados y aplicarlos.  

Si se trata de una serie de revisiones, vaya al **historial de revisiones** en la página de la serie de revisiones de acceso para seleccionar una revisión de acceso completa.  Las próximas revisiones aparecerán en la lista **Revisión programada**, donde puede modificar la duración y agregar o quitar revisores para las revisiones individuales.


