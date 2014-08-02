<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Premium SQL Database" pageTitle="Sign up for Azure Premium for SQL Database" metaKeywords="" description="Describes how to sign up for the Premium for SQL Database preview, request your Premium database quota, and then upgrade a database to Premium in Azure SQL Database." metaCanonical="" services="cloud-services" documentationCenter="" title="Sign up for the preview of Premium for Azure SQL Database" authors="karaman" solutions="" manager="" editor="tysonn" />

Suscripción para la vista previa de Premium para Base de datos SQL de Azure
===========================================================================

En este tutorial, aprenderá los pasos necesarios para participar en la vista previa de Premium para Base de datos SQL.

Base de datos SQL de Azure ha publicado una vista previa limitada de un nuevo servicio: Premium para Base de datos SQL. Las bases de datos Premium proporcionan recursos reservados para un rendimiento más predecible para las aplicaciones en la nube.

[La característica descrita en este tema está disponible solo en la vista previa. Este tema es la documentación preliminar y está sujeta a cambios en las futuras versiones].

Tabla de contenido
------------------

-   [Paso 1: Suscripción para la vista previa de Premium para Base de datos SQL](#SignUp)
-   [Paso 2: Solicitud de cuota para base de datos Premium](#Quota)
-   [Paso 3: Creación de una base de datos Premium](#Upgrade)

Paso 1: Suscripción para la vista previa de Premium para Base de datos SQL
--------------------------------------------------------------------------

El primer paso para aprovechar esta característica es registrar su suscripción para la vista previa de Premium para Base de datos SQL.

1.  Inicie sesión en la [página de características de Azure](http://account.windowsazure.com/PreviewFeatures) con su cuenta Microsoft.

    **Nota:** solo los administradores de las cuentas de Azure pueden tener acceso al portal de cuentas. Si no es el administrador de la cuenta de su suscripción, solicite a esa persona que complete el proceso de registro para su suscripción. Para obtener más información acerca de las cuentas de Azure y las suscripciones, consulte [Opciones de compra](http://account.windowsazure.com/PreviewFeatures).

    ![Imagen1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2.  Busque el elemento **Premium para Base de datos SQL** en la lista de características de vista previa y haga clic en el botón **try it now** asociado al elemento.

    ![Imagen2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)

3.  Elija la suscripción en la que desea registrarse para la vista previa.

    ![Imagen3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

    Solo las suscripciones Azure activas y pagadas son aptas para la vista previa. Puede registrar varias suscripciones, pero cada suscripción se puede registrar una sola vez.

    Registrar una suscripción para la vista previa no supondrá gastos adicionales, pero una vez activada y otorgada la cuota Premium, la creación o actualización a una base de datos Premium están sujetas a los precios descritos en la [página de precios de Base de datos SQL](http://www.windowsazure.com/en-us/pricing/details/sql-database/).

    El estado actual de la solicitud de registro se refleja en la lista de características de vista previa.

    ![Imagen4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4.  Las solicitudes se aprobarán según la demanda y la capacidad actuales. Es posible que deba esperar hasta 2 días por la activación de la suscripción, con tiempos de espera más prolongados si hay una mayor demanda o si se cumplió la capacidad de vista previa pública.

5.  Recibirá una notificación por correo electrónico una vez que la suscripción esté activada para la vista previa.

Paso 2: Solicitud de cuota para base de datos Premium
-----------------------------------------------------

Una vez que la suscripción se activa para la vista previa, necesita solicitar una cuota de base de datos Premium para cada servidor en el que tiene pensado crear una base de datos Premium. Como la capacidad es limitada, solo solicite cuotas para los servidores en los que planea crear una base de datos Premium y cancele toda solicitud pendiente que no sea necesaria.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) con la cuenta Microsoft.

    **Nota**: los administradores de cuentas, administradores de servicio y co-administradores de la suscripción pueden solicitar la cuota una vez que la suscripción se haya registrado para la vista previa.

2.  Desplácese hasta la lista **Servers** en la extensión **Bases de datos SQL**.
3.  Seleccione el servidor para el que planea solicitar cuotas Premium de base de datos.
4.  Desplácese hasta **Quick Start** para el servidor seleccionado haciendo clic en el icono de rayo en la barra de navegación superior.
5.  Haga clic en **Solicitar cuota de base de datos Premium** en la sección **Base de datos Premium**.

    ![Imagen6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)

    Después de que la solicitud se envía, puede esperar hasta 5 días antes de que se le otorgue la cuota. Los tiempos de espera más largos pueden indicar una alta demanda o que se cumplió la capacidad de vista previa.

    Algunas notas adicionales sobre las solicitudes de cuota de base de datos Premium:

    -   La cuota no está disponible para los clientes con suscripciones de prueba gratuita.
    -   La cuota es limitada inicialmente, y se entregarán solicitudes basadas en la demanda actual y la capacidad disponible.
    -   Microsoft puede reclamar la cuota no utilizada después de 15 días.
    -   Se puede enviar una sola solicitud de cuota por cada servidor lógico de la suscripción.
    -   Inicialmente, la cuota está limitada a una base de datos por servidor lógico.
    -   La solicitud de cuota de base de datos es gratuita, sin embargo, la creación de una base de datos de edición Premium o la actualización de una base de datos de edición Web o Business aumentará el coste de la base de datos.

6.  Puede ver el estado de la solicitud de cuota en la página **Quick Start** del servidor.

    ![Imagen7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)

7.  Recibirá una notificación por correo electrónico cuando se conceda la solicitud de cuota de base de datos Premium y esté disponible para su uso.
8.  Una vez otorgada, puede ver la cuota de base de datos Premium restante de un servidor en la pestaña **Quick Start** o **Panel** del servidor.

    ![Imagen8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

Paso 3: Creación de una base de datos Premium
---------------------------------------------

Después de otorgada la cuota, puede crear una nueva base de datos de edición Premium o actualizar una base de datos Web o Business existente a Premium para aprovechar la capacidad reservada y tener un rendimiento más predecible.

![Imagen9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)

![Imagen10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)

Para obtener más información, consulte [Modificación de niveles de servicio y de rendimiento de la base de datos](http://go.microsoft.com/fwlink/p/?LinkID=311927).

**Nota:** solo puede cambiar el estado Premium o el tamaño de la reserva de la base de datos una vez en un período de 24 horas.

Pasos siguientes
----------------

Para obtener información adicional acerca de las bases de datos Premium, consulte:

-   [Modificación de niveles de servicio y de rendimiento de la base de datos](http://go.microsoft.com/fwlink/p/?LinkID=311927)
-   [Orientación sobre Premium para Base de datos SQL](http://go.microsoft.com/fwlink/p/?LinkId=313650)

