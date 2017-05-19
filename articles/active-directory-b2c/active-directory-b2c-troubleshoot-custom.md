---
title: 'Azure Active Directory B2C: uso de Application Insights para resolver problemas de las directivas personalizadas | Microsoft Docs'
description: "cómo configurar Application Insights para realizar el seguimiento de la ejecución de las directivas personalizadas"
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
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 07eddeb35c2b88b2de08270d9ff5de317cc09ec7
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

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
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Si aún no existe, agregue un nodo secundario `<UserJourneyBehaviors>` al nodo `<RelyingParty>`. Tiene que estar situado inmediatamente después de `<DefaultUserJourney ReferenceId="YourPolicyName" />`
2. Agregue el siguiente nodo como nodo secundario del elemento `<UserJourneyBehaviors>`. Asegúrese de reemplazar `{Your Application Insights Key}` por la **Clave de instrumentación** que obtuvo de Application Insights en la sección anterior.

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
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Cargue la directiva.

### <a name="see-the-logs-in-application-insights"></a>Visualización de registros en Application Insights

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

Las entradas pueden ser largas.  Exporte a un archivo CSV para realizar un examen más detallado.

Puede obtener más información sobre estas herramientas de Analytics [aquí](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

^[!NOTE]
^ La Comunidad ha desarrollado un visor de userjourney para ayudar a los desarrolladores de identidad.  No está admitido por Microsoft y está disponible estrictamente tal cual.  Lee de la instancia de Application Insights y proporciona una vista bien estructurada de los eventos de userjourney.  Proporciona el código fuente para implementarlo en su propia solución.

[Github Repository for Unsupported Custom Policy Samples and Related tools](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies) (Repositorio de Github con ejemplos de directivas personalizadas no admitidas y herramientas relacionadas)





