---
title: "Sincronización de Azure AD Connect: interfaz de usuario de Synchronization Service Manager | Microsoft Docs"
description: "Conozca la pestaña Operaciones de Synchronization Service Manager para Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 610dab0af17f927d86b677f647acd0dfe2569583
ms.openlocfilehash: 53b98aaf67b874b0af7d0e94e29bcbe23fc6fc5b


---
# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronización de Azure AD Connect: Synchronization Service Manager

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

En la pestaña Operaciones se muestran los resultados de las operaciones más recientes. Esta pestaña es clave para entender y solucionar los problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Información visible en la pestaña Operaciones
La mitad superior muestra todas las ejecuciones en orden cronológico. De forma predeterminada, el registro de operaciones conservará información sobre los últimos 7 días, pero puede cambiar este parámetro con el [programador](active-directory-aadconnectsync-feature-scheduler.md). Debe buscar las ejecuciones que no muestran un estado de operación correcta. Para cambiar la ordenación, haga clic en los encabezados.

En la columna **Estado** se encuentra la información más importante, puesto que muestra el problema más grave de una ejecución. A continuación tiene un resumen rápido de los estados más comunes que debe analizar por orden de prioridad (donde * indica varias cadenas de error posibles).

| Estado | Comentario |
| --- | --- |
| stopped-* |No se ha podido completar la ejecución. Por ejemplo, si el sistema remoto está inactivo y no se puede conectar a él. |
| stopped-error-limit |Se han generado más de 5000 errores. La ejecución se ha detenido automáticamente debido al elevado número de errores. |
| completed-\*-errors |Se completa la ejecución, pero hay errores (menos de 5000) que deben investigarse. |
| completed-\*-warnings |La ejecución se ha completado, pero algunos datos no tienen el estado esperado. Si se producen errores, es posible que se trate únicamente de un síntoma. Le recomendamos que primero resuelva los errores y que luego investigue las advertencias. |
| Correcto |No hay ningún problema. |

Cuando seleccione una fila, la parte inferior se actualizará para mostrar los detalles de la ejecución. En el extremo izquierdo de la parte inferior, es posible que aparezca una lista con la información **Paso #**. Solo aparecerá si tiene varios dominios en el bosque; cada dominio estará representado por un paso. El nombre de dominio puede encontrarse bajo el encabezado **Partición**. En **Synchronization Statistics**(Estadísticas de sincronización) puede encontrar más información sobre el número de cambios que se han procesado. Puede hacer clic en los vínculos para obtener una lista de los objetos modificados. Si hay objetos con errores, estos se mostrarán en **Errores de sincronización**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Solución de problemas en la pestaña Operaciones
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Cuando se producen errores, tanto el objeto del error como el propio error son vínculos que proporcionarán información adicional.

Empiece haciendo clic en la cadena de error (en la imagen anterior,**sync-rule-error-function-triggered** ). En primer lugar, aparecerá información general del objeto. Para ver el error real, haga clic en el botón **Seguimiento de la pila**. De esta forma, podrá consultar información de depuración del error.

**SUGERENCIA:** Puede hacer clic con el botón derecho en la casilla **call stack information** (información de la pila de llamadas), hacer clic en **seleccionar todo** y, después, en **copiar**. A continuación, puede copiar la pila y buscar el error en el editor que prefiera, como el Bloc de notas.

* Si el error procede de **SyncRulesEngine**, la información de la pila de llamadas tendrá en primer lugar una lista de todos los atributos del objeto. Desplácese hacia abajo hasta que vea el encabezado **InnerException = >**.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
  La línea posterior muestra el error. En la imagen anterior, el error procede de una regla de sincronización personalizada creada por Fabrikam.

Si el error no proporciona suficiente información, puede fijarse en los datos. Puede hacer clic en el vínculo con el identificador de objeto y realizar un [seguimiento de un objeto y sus datos a través del sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).



<!--HONumber=Jan17_HO2-->


