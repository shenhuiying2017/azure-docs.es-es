---
title: "Azure Active Directory B2C: Troubleshoot Custom Policies (Azure Active Directory B2C: directivas personalizadas de solución de problemas) | Microsoft Docs"
description: "Un tema sobre cómo solucionar problemas con las directivas personalizadas de Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 2fa67038f2a214c1569fc65fd9f1beba394cb790
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Collecting Logs (Azure Active Directory B2C: recopilación de registros)

En este artículo se proporcionan los pasos para recopilar registros de Azure AD B2C de forma que pueda diagnosticar problemas con sus directivas personalizadas.

## <a name="use-application-insights"></a>Uso de Application Insights

Azure AD B2C admite una característica para enviar datos a Application Insights.  Application Insights proporciona un modo de diagnosticar excepciones y visualizar problemas de rendimiento de la aplicación.

### <a name="setup-application-insights"></a>Configuración de Application Insights

1. Vaya al [Portal de Azure](https://portal.azure.com). Asegúrese de que se encuentra en el inquilino con su suscripción de Azure (no su inquilino de Azure AD B2C).
1. Haga clic en **+ Nuevo** en el menú de navegación izquierdo.
1. Busque y seleccione **Application Insights** y, a continuación, haga clic en **Crear**.
1. Complete el formulario y haga clic en **Crear**. Seleccione **General** para el **Tipo de aplicación**.
1. Una vez creado el recurso, abra el recurso de Application Insights.
1. Busque **Propiedades** en el menú izquierdo y haga clic.
1. Copie la **Clave de instrumentación** y guárdela para la sección siguiente.

### <a name="set-up-the-custom-policy"></a>Configuración de la directiva personalizada

1. Abra el archivo de usuario de confianza (p. ej., SignUpOrSignin.xml).
1. Agregue los siguientes atributos al elemento `<TrustFrameworkPolicy>`:

  ```XML
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Si aún no existe, agregue un nodo secundario `<UserJourneyBehaviors>` al nodo `<RelyingParty>`.
2. Agregue el siguiente nodo como nodo secundario del elemento `<UserJourneyBehaviors>`. Asegúrese de reemplazar `{Your Application Insights Key}` por la **Clave de instrumentación** que obtuvo en la sección anterior.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` indica a ApplicationInsights que acelere la telemetría a través de la canalización de procesamiento, buena para el desarrollo, pero limitada en volúmenes elevados.
  * `ClientEnabled="true"` envía el script del lado cliente ApplicationInsights para realizar un seguimiento de la vista de página y de los errores del lado cliente (no es necesario).
  * `ServerEnabled="true"` envía el JSON UserJourneyRecorder como evento personalizado a Application Insights.
  El XML tendrá un aspecto similar al siguiente:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Cargue la directiva.

### <a name="see-the-logs"></a>Visualización de los registros

>[!NOTE]
> Hay un breve retraso (inferior a cinco minutos) antes de que pueda ver nuevos registros en Application Insights.

1. Abra el recurso de Application Insights que ha creado en [Azure Portal](https://portal.azure.com).
1. En el menú **Información general**, haga clic en **Analytics**.
1. Abra una nueva pestaña en Application Insights.
1. A continuación se muestra una lista de consultas que puede usar para ver los registros

| Consultar | Descripción |
|---------------------|--------------------|
traces | Ver todos los registros generados por Azure AD B2C |
traces \| where timestamp > ago(1d) | Ver todos los registros generados por Azure AD B2C para el último día

Puede obtener más información sobre estas herramientas de Analytics [aquí](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).





