<properties
    pageTitle="Compartir un inquilino de Azure AD entre suscripciones de Office 365 y Azure | Microsoft Azure"
    description="Obtenga información acerca de cómo compartir su inquilino de Azure AD de Office 365 y sus usuarios con su suscripción de Azure o viceversa."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="cjiang"/>


# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Uso de una cuenta de Office 365 con la suscripción de Azure y viceversa
Escenario: Ya dispone de una suscripción de Office 365 y desea obtener una suscripción de Azure, y quiere utilizar sus cuentas de usuario actuales de Office 365 para la suscripción de Azure. También es posible que ya sea suscriptor de Azure y desee obtener una suscripción de Office 365 para los usuarios de su cuenta actual de Azure Active Directory. En este artículo se explica cómo puede hacer ambas cosas con facilidad.

> [AZURE.NOTE] Este artículo no es aplicable para clientes del contrato Enterprise (EA). Si necesita más ayuda sobre cualquier aspecto de este artículo, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver rápidamente el problema.


## <a name="quick-guidance"></a>Guía rápida

- Si ya tiene una suscripción de Office 365 y desea suscribirse a Azure, use la opción **Inicie sesión con su cuenta de organización**. A continuación, continúe el proceso de suscripción a Azure con su cuenta de Office 365. Consulte los [pasos detallados más adelante en este artículo](#s1).

- Si ya tiene una suscripción de Azure y desea obtener una suscripción de Office 365, inicie sesión en Office 365 con su cuenta de Azure. A continuación, continúe con los pasos del registro. Una vez completado el registro, la suscripción de Office 365 se agrega a la misma instancia de Azure Active Directory a la que pertenece su suscripción de Azure. Para más información, consulte la sección de [pasos detallados más adelante en este artículo](#s2).

>[AZURE.NOTE] Para obtener una suscripción de Office 365, la cuenta que use para registrarse debe ser miembro del rol de directorio de administrador global o de administrador de facturación en el inquilino de Azure Active Directory. [Aprenda a determinar el rol en Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

Para comprender qué ocurre cuando agrega una suscripción a una cuenta, consulte la [información de contexto más adelante en este artículo](#background-information).

## <a name="detailed-steps"></a>Pasos detallados
<a id="s1"></a>
### <a name="scenario-1:-office-365-users-who-plan-to-buy-azure"></a>Escenario 1: Usuarios de Office 365 que desean comprar Azure
En este escenario, asumimos que Kelley Wall es una usuaria que tiene una suscripción de Office 365 y desea suscribirse a Azure. Además, hay otras dos usuarias activas: Jane y Tricia. La cuenta de Kelley es admin@contoso.onmicrosoft.com.

![Centro de administración de usuarios de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Para suscribirse a Azure, siga estos pasos:

1. Suscríbase a Azure en [Azure.com](https://azure.microsoft.com/). Haga clic en **Pruébelo gratis**. En la siguiente página, haga clic en **Empezar ahora**.

    ![Pruebe Azure gratis.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Haga clic en **Inicie sesión con su cuenta de organización**.

    ![Inicie sesión en Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Inicie sesión con su cuenta de Office 365. En este caso, será la cuenta de Office 365 de Kelley.

    ![Inicie sesión con su cuenta de Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Rellene la información y complete el proceso de registro.

    ![Rellene la información y complete el registro.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Haga clic en Empezar a administrar mi servicio.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Ahora ya está listo. En Azure Portal verá que aparecen los mismos usuarios. Para ello, siga estos pasos:

1. Haga clic en la opción **Empezar a administrar mi servicio** mostrada en la pantalla anterior.
2. Haga clic en **Examinar** y, luego, haga clic en **Active Directory**.

    ![Haga clic en Examinar y, luego, haga clic en Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Haga clic en **USUARIOS**.

    ![La pestaña Usuarios](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Todos los usuarios, incluida Kelley, aparecen como se esperaba.

    ![Lista de usuarios](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### <a name="scenario-2:-azure-users-who-plan-to-buy-office-365"></a>Escenario 2: Usuarios de Azure que desean comprar Office 365

En este escenario, Kelley Wall es una usuaria que tiene una suscripción de Azure con la cuenta admin@contoso.onmicrosoft.com. Kelley desea suscribirse a Office 365 y usar el mismo directorio que ya tiene con Azure.

>[AZURE.NOTE] Para obtener una suscripción de Office 365, la cuenta que use para registrarse debe ser miembro del rol de directorio de administrador global o de administrador de facturación en el inquilino de Azure Active Directory. [Aprenda a identificar el rol en Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

![Configuración de la suscripción de Azure Portal](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Usuarios de Active Directory de Azure Portal](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Para suscribirse a Office 365, siga estos pasos:

1. Vaya a la [página de productos de Office 365](https://products.office.com/business)y luego seleccione un plan que se ajuste a sus necesidades.
2. Después de seleccionar el plan, se muestra la página siguiente. No rellene el formulario. Haga clic en **Iniciar sesión** en la esquina superior derecha de la página.

    ![Página de prueba de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Inicie sesión con las credenciales de su cuenta. En este caso, será la cuenta de Kelley.

    ![Inicio de sesión en Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Haga clic en **Probar ahora**.

    ![Confirme su pedido para Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. En la página de confirmación del pedido, haga clic en **Continuar**.

    ![Recibo del pedido de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Ahora ya está listo. En el centro de administración de Office 365, verá los mismos usuarios del directorio de Contoso como usuarios activos. Para ello, siga estos pasos:

1. Abra el centro de administración de Office 365.
2. Expanda **USUARIOS** y luego haga clic en **Usuarios activos**.

    ![Usuarios del centro de administración de Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Identificación del rol en Azure Active Directory

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar** y, luego, haga clic en **Active Directory**.

    ![Active Directory en Azure Portal](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Haga clic en **USUARIOS**.

    ![Usuarios predeterminados de Active Directory de Azure Portal](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Haga clic en el usuario. En este ejemplo, el usuario es Kelley Wall.

    Observe el campo **ROL ORGANIZATIVO**.

    ![Identidad de usuario de Azure Portal](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Información general acerca de las suscripciones de Azure y Office 365
Office 365 y Azure usan el servicio Azure Active Directory para administrar usuarios y suscripciones. Piense en un directorio de Azure como en un contenedor en el que puede agrupar a usuarios y suscripciones. Para poder usar la misma cuenta de usuario con sus suscripciones de Azure y Office 365, asegúrese de que las suscripciones se hayan creado en el mismo directorio. Tenga en cuenta los siguientes puntos:

- Una suscripción se crea en un directorio, no al revés.
- Los usuarios pertenecen a los directorios, no al revés.
- Una suscripción se incluye en el directorio del usuario que la crea. En consecuencia, la suscripción de Office 365 se asocia a la misma cuenta de la suscripción de Azure si se usa esa cuenta para crear la suscripción de Office 365.

![Información de contexto](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para más información, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

>[AZURE.NOTE] Las suscripciones de Azure son propiedad de usuarios individuales del directorio.

>[AZURE.NOTE] Las suscripciones de Office 365 pertenecen al propio directorio. Los usuarios del directorio pueden trabajar con estas suscripciones si disponen de los permisos necesarios.

## <a name="next-steps"></a>Pasos siguientes
Si adquirió las suscripciones de Azure y Office 365 por separado en el pasado y desea poder acceder al inquilino de Office 365 desde la suscripción de Azure, consulte [Asociación del inquilino de Office 365 con una suscripción de Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Si tiene más preguntas, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.



<!--HONumber=Oct16_HO2-->


