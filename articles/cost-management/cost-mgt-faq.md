---
title: "Preguntas más frecuentes sobre Azure Cost Management | Microsoft Docs"
description: "En este artículo se ofrecen respuestas a algunas de las preguntas frecuentes sobre Azure Cost Management."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/14/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: f62e5a224c2fb33714a80bc47b98238208b787e5
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Preguntas más frecuentes sobre Azure Cost Management

En este artículo se tratan algunas preguntas comunes sobre Azure Cost Management (también conocido como Cloudyn). Si tiene preguntas acerca de Cost Management, puede formularlas en [FAQs for Azure Cost Management by Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn) (Preguntas más frecuentes sobre Azure Cost Management en Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>¿Cómo se resuelven los problemas de configuración empresariales indirectos comunes?

La primera vez que use el portal de Cloudyn, podría ver los siguientes mensajes si es usuario de Contrato Enterprise o Proveedor de soluciones en la nube (CSP):

- "The specified API key is not a top level enrollment key" (La clave de API especificada no es una clave de registro de nivel superior) aparece en el Asistente para la **configuración de Azure Cost Management**.
- "Direct Enrollment – No" (Inscripción directa: no) aparece en el portal de Contrato Enterprise.
- "No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account" (No se encontraron datos de uso de los últimos 30 días. Póngase en contacto con su distribuidor para asegurarse de que se habilitó el margen de beneficio para su cuenta de Azure) aparece en el portal de Cloudyn.

Los mensajes anteriores indican que adquirió un Contrato Enterprise de Azure a través de un revendedor o CSP. El CSP o el revendedor deben habilitar el _margen de beneficio_ para su cuenta de Azure a fin de que pueda ver sus datos en Cloudyn.

Aquí se muestra cómo corregir los problemas:

1. El revendedor debe habilitar el _margen de beneficio_ para su cuenta. Para ver las instrucciones, consulte la [guía de incorporación para clientes indirectos ](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide) (en inglés).

2. Debe generar la clave de Contrato Enterprise de Azure para su uso con Cloudyn. Para ver las instrucciones consulte [Adding Your Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) (Agregar clave de Azure EA) o [How to Find Your EA Enrollment ID and API Key](https://youtu.be/u_phLs_udig) (Cómo buscar el identificador de inscripción de EA y la clave de API).

Solo un administrador de servicios de Azure puede habilitar Cost Management. Los permisos de coadministrador son insuficientes.

Para poder generar la clave de API de Contrato Enterprise de Azure para configurar Cloudyn, debe habilitar la API de facturación de Azure siguiendo las instrucciones que aparecen en:

- [Introducción a las API de informes para clientes de Enterprise](../billing/billing-enterprise-api.md)
- [API de informes de Microsoft Azure Enterprise Portal](https://ea.azure.com/helpdocs/reportingAPI) en **Habilitación del acceso de datos a la API**


Es posible que también deba conceder permisos a administradores de departamento, propietarios de cuentas y administradores de empresa para _ver cargos_ con la API de facturación.

## <a name="why-dont-i-see-optimizer-recommendations"></a>¿Por qué no veo las recomendaciones del optimizador?

La información de las recomendaciones solo está disponible para las cuentas que están activadas. No verá información de las recomendaciones en las categorías de informes del **optimizador** de las cuentas que *no están activadas*, entre las que se incluyen:

- Administrador de optimizaciones
- Optimización de tamaño
- Ineficacias

Si no puede ver los datos de las recomendaciones del optimizador, es muy probable que tenga cuentas sin activar. Para activar una cuenta, debe registrarla con sus credenciales de Azure.

Para activar una cuenta:

1.  En el portal de Cloudyn, haga clic en **Settings** (Configuración) en la esquina superior derecha y seleccione **Cloud Accounts** (Cuentas en la nube).
2.  En la ficha Microsoft Azure Accounts (Cuentas de Microsoft Azure), busque cuentas que tengan alguna suscripción **sin activar**.
3.  A la derecha de la cuenta sin activar, haga clic en el símbolo de **editar**, que se parece a un lápiz.
4.  Tanto el identificador de inquilino como el identificador de velocidad se detectan automáticamente. Haga clic en **Next**.
5.  Se le redirigirá a Azure Portal. Inicie sesión en el portal y autorice a Cloudyn Collector a acceder a sus datos de Azure.
6.  A continuación, se le redirige a la página de administración de cuentas de Cloudyn, la suscripción se actualiza y la cuenta pasa a estar estado **activa**. Muestra una marca de verificación verde.
7.  Si no ve una marca de verificación verde en una o varias suscripciones, significa que no tiene permisos para crear ninguna aplicación de lectura (CloudynCollector) para la suscripción. Los usuarios con permisos superiores en la suscripción deben repetir los pasos 3 y 4.  

Tras completar los pasos anteriores podrá ver las recomendaciones del optimizador en uno o dos días. Sin embargo, pueden pasar hasta cinco días estén disponibles todos los datos de las optimizaciones.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>¿Cómo se habilitan los usuarios suspendidos o bloqueados?

Si recibe una alerta con una solicitud para permitir el acceso para un usuario, debe activar la cuenta de usuario.

Para activar la cuenta de usuario:

1. Inicie sesión en Cloudyn con la cuenta de usuario administrativo de Azure que usó para configurar Cloudyn. O bien, inicie sesión con una cuenta de usuario a la que se concediera acceso de administrador.

2. Seleccione el símbolo de engranaje en la esquina superior derecha y seleccione **User Management** (Administración de usuarios).

3. Busque el usuario, seleccione el símbolo de lápiz y, a continuación, edite el usuario.

4. En **User status** (Estado del usuario), cambie el estado de **Suspended** (Suspendido) a **Active** (Activo).

Las cuentas de usuario de Cloudyn se conectan mediante el inicio de sesión único de Azure. Si un usuario escribe mal su contraseña, podría bloquearse en Cloudyn, aunque podría seguir teniendo acceso a Azure.

Si cambia la dirección de correo electrónico en Cloudyn de la dirección predeterminada de Azure, su cuenta puede bloquearse. Puede que aparezca "status initiallySuspended". Si su cuenta de usuario se bloquea, póngase en contacto con el administrador alternativo para restablecer su cuenta.

Se recomienda crear al menos dos de cuentas de administrador de Cloudyn por si una de ellas se bloquea.

Si no puede iniciar sesión en el portal de Cloudyn, asegúrese de que está utilizando la dirección URL de Azure Cost Management correcta para iniciar sesión en Cloudyn. Use [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Evite usar la dirección URL directa de Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>¿Cómo activo cuentas no activadas con las credenciales de Azure?

Tan pronto como Cloudyn detecte las cuentas de Azure, los datos de costos de proporcionarán inmediatamente en informes basados en costos. Sin embargo, para que Cloudyn proporcione datos de uso y rendimiento, debe registrar sus credenciales de Azure para las cuentas. Para obtener instrucciones, consulte [Agregar Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Para agregar las credenciales de Azure de una cuenta, en el portal de Cloudyn, seleccione el símbolo de edición a la derecha del nombre de cuenta, no de la suscripción.

Hasta que sus credenciales de Azure se agreguen a Cloudyn, la cuenta aparecerá como _inactiva_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>¿Cómo agrego varias cuentas y entidades a una suscripción existente?

Las entidades adicionales se usan para agregar contratos Enterprise adicionales a una suscripción de Cloudyn. En los vínculos siguientes se describe cómo agregar entidades adicionales:

- Artículo [Adding an Entity](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) (Agregar una entidad)
- Vídeo [Defining your hierarchy with Cost Entities](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) (Definir la jerarquía con entidades de costos)

Para los CSP:

Para agregar cuentas de CSP adicionales a una entidad, seleccione **MSP Access** (Acceso de MSP) en lugar de **Enterprise** al crear la nueva entidad. Si su cuenta está registrada como un contrato Enterprise y desea agregar credenciales de CSP, es posible que el personal de soporte técnico de Cloudyn tenga que modificar la configuración de su cuenta. Si es un suscriptor de pago de Azure, puede crear una nueva solicitud de soporte técnico en Azure Portal. Seleccione **Ayuda y soporte técnico** y, a continuación, **Nueva solicitud de soporte técnico**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Símbolos de divisa en los informes de Cloudyn

Puede tener varias cuentas de Azure con distintas divisas. Sin embargo, los informes de costos de Cloudyn no muestran más que un tipo de moneda por informe.

Si tiene varias suscripciones que utilizan distintas monedas, la entidad primaria y sus monedas de entidad secundarias se muestran con el símbolo **$**. Nuestra sugerencia de procedimiento recomendado consiste en evitar utilizar distintas monedas en la misma jerarquía de entidad. En otras palabras, todas las suscripciones que se organizan en una estructura de entidad deben utilizar la misma moneda.

Cloudyn detecta automáticamente la moneda de la suscripción del Contrato Enterprise y la presenta correctamente en los informes.  No obstante, Cloudyn solo muestra el símbolo **$** para las cuentas de Azure directas de la web y CSP.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>¿Qué son las escalas de tiempo de actualización de datos de Cloudyn?

Cloudyn tiene las escalas de tiempo de actualización de datos siguientes:

- **Inicial**: después de la configuración, se pueden tardar hasta 24 horas en ver los datos de costos en Cloudyn. Cloudyn puede tardar hasta 10 días en recopilar datos suficientes para mostrar recomendaciones de ajuste de tamaño.
- **Diaria**: desde el décimo día hasta el final de cada mes, Cloudyn deben mostrar sus datos actualizados del día anterior después de UTC+3 aproximadamente al día siguiente.
- **Mensual**: desde el primer día hasta el décimo de cada mes, es posible que Cloudyn solo muestre los datos hasta el final del mes anterior.

Cloudyn procesa los datos del día anterior cuando todos los datos del día anterior están disponibles. Los datos del día anterior están disponibles normalmente en Cloudyn hacia la hora UTC+3 cada día. Algunos datos, como las etiquetas, pueden tardar 24 horas más en procesarse.

Los datos del mes actual no están disponibles para su recopilación al principio de cada mes. Durante el período, los proveedores de servicios finalizan su facturación para el mes anterior. Los datos del mes anterior aparecen en Cloudyn entre cinco y diez días después del inicio de cada mes. Durante este tiempo, podría ver solo los costos amortizados del mes anterior. Es posible que no vea los datos de facturación o uso diarios. Cuando los datos están disponibles, Cloudyn los procesa con carácter retroactivo. Después del procesamiento, se muestran todos los datos mensuales entre el quinto y el décimo día de cada mes.

Si se produce un retraso al enviar datos de Azure a Cloudyn, los datos se siguen registrando en Azure. Los datos se transfieren a Cloudyn cuando se restaura la conexión.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>¿Cómo debe configurar un CSP directo el acceso a Cloudyn para asociados o clientes de CSP indirectos?

Para obtener instrucciones, consulte [Configuración del acceso indirecto a CSP en Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>¿Qué hace que aparezca el elemento de menú Optimizer (Optimizador)?

Después de agregar el acceso a Azure Resource Manager y de recopilar los datos, debería ver la opción **Optimizador** (Optimizador). Para activar el acceso a Azure Resource Manager, consulte [¿Cómo activo cuentas no activadas con las credenciales de Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>¿Se basa en el agente de Cost Management/Cloudyn?

Nº No se utilizan agentes. Los datos de métricas de la máquina virtual de Azure para las máquinas virtuales se recopilan des de la API de Microsoft Insights. Si quiere recopilar datos de métricas de las máquinas virtuales de Azure, estos deben tener habilitada la configuración de diagnóstico.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>¿Los informes de Cloudyn muestran más de un inquilino de AD por informe?

Sí. Puede [crear una entidad de la cuenta en la nube correspondiente](tutorial-user-access.md#create-entities) para cada inquilino de AD que tenga. A continuación, puede ver todos los datos del inquilino de Azure AD y otros proveedores de plataformas en la nube, como Amazon Web Services y Google Cloud Platform.
