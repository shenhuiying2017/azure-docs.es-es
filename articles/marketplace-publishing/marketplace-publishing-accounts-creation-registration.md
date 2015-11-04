<properties
   pageTitle="Creación y registro del proceso de cuenta del anunciante | Microsoft Azure"
   description="Instrucciones para crear una cuenta vendedor de Microsoft, para que, tras la aprobación, se puedan vender varios tipos de ofertas en Azure Marketplace."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Creación de una cuenta de vendedor de Microsoft
Este artículo le guía a través de la creación y el registro de las cuentas necesarias para convertirse en un vendedor de Microsoft aprobado para Azure Marketplace.

## 1. Creación de una cuenta de Microsoft (MSA)
> [AZURE.WARNING]Para finalizar el proceso de publicación, debe crear una cuenta Microsoft. Esta cuenta se usará para registrarse e iniciar sesión tanto en el Portal de publicación como en el panel de vendedores. Solo debe tener una cuenta Microsoft para sus ofertas de Azure Marketplace. No deben ser específicas a servicios u ofertas.

La dirección que constituye el nombre de usuario debe estar en su dominio y controlada por su equipo de TI (por ejemplo, publishing@yourcompany.com). Los pagos, la información fiscal y los informes se tramitarán con esta cuenta.

  >[AZURE.WARNING]No se admiten palabras como "Azure", "Microsoft" en el registro de la cuenta MSA. Evite el uso de estas palabras para completar el proceso de creación y registro de cuentas.

1. Cree una lista de distribución (DL) o un grupo de seguridad (SG) dentro del dominio de la compañía.
  - Agregue el equipo de incorporación a la lista de distribución.
  - La lista de distribución debe estar activa para recibir correo electrónico de confirmación.
  - Se debe completar en los sistemas internos. Por ej. marketplace@yourcompany.com
2. Abra una nueva sesión exploración de incógnito en Chrome o de InPrivate en Internet para asegurarse de que no está conectado a una cuenta existente.
3. Regístrese para obtener una cuenta Microsoft (MSA) con el correo electrónico de la lista de distribución.
 - Puede registrarse para obtener una cuenta Microsoft en [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Use marketplace@yourcompany.com como la dirección de correo electrónico.
 - El identificador de MSA es ahora marketplace@yourcompany.com.

    ![dibujo][img-msalive]

4. Use un número de teléfono válido para el registro. El sistema le enviará un código de verificación en un mensaje de texto o una llamada automática si es necesario verificar la identidad.
5. Compruebe la dirección de correo electrónico enviada a la lista de distribución.

    ![dibujo][img-email]

6. Ahora está listo para usar la nueva MSA en el panel de vendedores.

> [AZURE.IMPORTANT]El uso de la lista de distribución permite que varias personas reciban notificaciones por correo electrónico que son informes importantes de la información de pago y también garantiza que la propiedad de la MSA se pueden transferir y que no está vinculada a un único usuario.

## 2. Creación de su cuenta en el Panel de vendedores
El Panel de vendedores de Microsoft se usa para registrar la información de la empresa una vez. El usuario inscrito debe ser un representante válido de la compañía y debe proporcionar su información personal, como una forma de validar su identidad. La persona que se inscribe debe usar una cuenta de Microsoft (MSA) que se comparte para la compañía y debe usarse la misma cuenta en el Portal de publicación de Azure. Debe asegurarse de que su compañía no tenga ya una cuenta en el panel de vendedores antes de intentar crear una nueva. Durante el proceso, recopilaremos los datos de su cuenta bancaria, su información fiscal y la dirección de la compañía. Normalmente, estos datos se pueden obtener de contactos financieros o comerciales.

> [AZURE.IMPORTANT]Deben completarse los componentes del perfil de vendedor siguientes para avanzar por las distintas fases de la creación e implementación de la oferta.


| Perfil de vendedor | Para iniciar el borrador | Ensayo | Publicación gratis y plantilla de solución | Publicación de ofertas comerciales |
|----|----|----|----|----|
|Registro de la compañía | Obligatorio | Obligatorio | Obligatorio | Obligatorio |
|Identificación de perfil fiscal | Opcional | Opcional | Opcional | Obligatorio |
|Cuenta bancaria | Opcional | Opcional | Opcional | Obligatorio |


> [AZURE.NOTE]BYOL solo se admite para las máquinas virtuales y se considera una oferta **GRATIS**.


### Registro de su cuenta de compañía
1. Abra una nueva sesión de exploración de InPrivate en Internet o de incógnito en Chrome para asegurarse de que no está conectado a una cuenta existente.

2. Vaya a [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com).

    ![dibujo][img-sd-url]

3. Inicie sesión con su cuenta de Microsoft (MSA) de registro de la empresa (por ejemplo, AzureStore@yourcompany.com)

    ![dibujo][img-signin]

4. Complete el asistente “Ayúdenos a proteger su cuenta”, que comprobará su identidad por teléfono o por correo electrónico.

    ![dibujo][img-verify]

5. Vaya a **Detalles de la cuenta**. En esta pantalla, debe proporcionar su información PERSONAL, que solo se usa para verificar su identidad. Se trata de su nombre, dirección de correo electrónico, domicilio y número de teléfono privados. Esta información no se comparte con nadie fuera de Microsoft.

    ![dibujo][img-sd-top]

    ![dibujo][img-sd-info]

6. Regístrese en nombre de su empresa mediante la designación de su tipo de cuenta como Compañía NO como cuenta individual. Haga clic en **Siguiente**.

    ![dibujo][img-sd-type]

7. Rellene los datos de la compañía. Debe ser una información precisa: sede central, referencias, todo ello lo comprobará un equipo de Microsoft.

    ![dibujo][img-sd-mktg1]

8. El nombre de la compañía en los datos siguientes se usa en el portal del anunciante, por tanto, debe ser preciso.

    ![dibujo][img-sd-mktg2]

9. Use la dirección de la sede central de la compañía.

    ![dibujo][img-sd-addr]

10. Use referencias que sean accesibles y reconocibles, como un representante de la compañía.

    ![dibujo][img-sd-legal]

11. Haga clic en **Enviar para aprobación** y ya finalizó.

    ![dibujo][img-sd-submit]


Si solo piensa publicar ofertas gratis, vaya a la sección 3, **Registro de su cuenta en el Portal de publicación**.

Si piensa publicar ofertas comerciales, debe completar la información fiscal y bancaria de su cuenta de Panel de vendedores. Consulte la sección 2.2.

> [AZURE.IMPORTANT]No podrá probar correctamente las ofertas en el entorno de ensayo ni podrá llevar sus ofertas a producción sin completar la información fiscal y de la cuenta bancaria.

Si prefiere actualizar la información bancaria y fiscal más adelante, vaya al siguiente paso 3. **Registre su cuenta en el Portal de publicación** y vuelva después mediante el uso de los vínculos en el Portal de publicación.

### Adición de información bancaria y fiscal
 Si desea publicar ofertas comerciales de compra, también necesitará agregar información del pago y fiscal y enviarla para su validación en el Panel de vendedores. Si solo va a publicar ofertas gratis (u ofertas BYOL), no necesita agregar esta información. Puede agregarla más adelante, pero se tarda algún tiempo en validar la información fiscal. Si sabe que ofrecerá ofertas comerciales de compra, se recomienda que las agregue tan pronto como sea posible.

1. Inicie sesión en el [Panel de vendedores](http://sellerdashboard.microsoft.com) con su cuenta de Microsoft.

2. Haga clic en la pestaña **CUENTA** y, después, haga clic en Pago e impuestos.

3. Haga clic en **AGREGAR INFORMACIÓN DE PAGO E IMPUESTOS**.

4. En la página **Elija un método de pago**, bajo Nuevo método de pago, haga clic en **Cuenta bancaria** o **PayPal**.

> [AZURE.IMPORTANT]Si tiene ofertas comerciales que compran los clientes en Marketplace, esta es la cuenta donde recibirá el pago para esas compras.

5. Especifique los datos de la cuenta bancaria o de una cuenta PayPal.

6. Haga clic en **SIGUIENTE**.

7. En la página **Información sobre impuestos**, seleccione el país o región donde tenga la residencia permanente, después, seleccione el país o región donde mantiene la nacionalidad principal y, finalmente, haga clic en SIGUIENTE.

8. Escriba la información fiscal y, después, haga clic en **SIGUIENTE**.

9. Haga clic en **Enviar**. Si todavía no está preparado para enviar la información fiscal para su validación, haga clic en **Guardar** o en **Guardar y salir**. Se tarda algún tiempo en validar la información fiscal, por lo que recomendamos que la envíe para su validación lo antes posible.

> [AZURE.WARNING]No podrá llevar a producción las ofertas comerciales sin completar la información fiscal y de cuenta bancaria en su cuenta de Panel de vendedores.

## 3. Registre su cuenta en el Portal de publicación
El Portal de publicación de Azure se usa para publicar y administrar sus ofertas. Encontrará información útil en el Portal de publicación, que le guiará por todo el proceso de creación de ofertas.

> [AZURE.WARNING]DEBE usar aquí la misma cuenta de Microsoft de compañía que se usó para el registro del Panel de vendedores. Se pueden agregar usuarios adicionales para ayudar una vez creada la cuenta del anunciante principal.

1.	Abra una nueva sesión exploración de incógnito en Chrome o de InPrivate en Internet para asegurarse de que no está conectado a una cuenta personal.
2.	Vaya a [http://publish.windowsazure.com](http://publish.windowsazure.com)
3.	 Inicie sesión con su cuenta de Microsoft (MSA) de registro de la compañía (es decir, AZStore@yourcompany.com) y, si es necesario, agregue coadministradores.
4.	Lea y acepte los términos del contrato de anunciante (la primera vez que inicia sesión en el Portal de publicación) y listo.

  >[AZURE.TIP]Las directivas de participación se mencionan [aquí](http://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Si tiene problemas para completar el registro como vendedor, abra una incidencia de soporte técnico, tal como se indica a continuación:
  1. Póngase en contacto con [Soporte técnico](http://go.microsoft.com/fwlink?LinkId=272975).
  2. Elija **Registro del Panel de vendedores y su cuenta**.
  3. Elija **Registro para una cuenta de desarrollador**
  4. Elija un **método** de contacto.

### Países "Sell-from"

> [AZURE.WARNING]
Para vender sus servicios en Microsoft Azure Marketplace, es necesario que la entidad registrada proceda de uno de los países "sell from" aprobados. Esta restricción se debe a motivos de pago e impuestos. Trabajamos activamente para ampliar esta lista de países en un futuro próximo, así pues, manténgase atento. Para obtener la lista completa, consulte la sección 1b del documento en el vínculo [http://go.microsoft.com/fwlink/?LinkID=526833](http://go.microsoft.com/fwlink/?LinkID=526833).




## Pasos siguientes
Ahora que la cuenta se creó y se registró, haga clic en el tipo de artefacto (máquina virtual, servicio de programador, servicio de datos, plantilla de soluciones) que desea publicar en Azure Marketplace. Visite uno de los artículos siguientes para obtener más información sobre cómo publicar su oferta respectiva:

|| Imagen de máquina virtual | Servicio para desarrolladores | Servicio de datos | Plantilla de solución |
|----|-----|-----|-----|-----|
|**Paso 2: Creación de la oferta** | [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md)| [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md) | [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md) | [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md) |
|| [Requisitos previos técnicos de imagen de la máquina virtual][link-single-vm-prereq] | Requisitos previos técnicos de servicio de desarrolladores | Requisitos previos técnicos de servicio de datos | [Requisitos previos técnicos de plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [Guía de publicación de imágenes de máquina virtual][link-single-vm] | Guía de publicación de servicios de desarrolladores | Guía de publicación de servicios de datos | [Guía de publicación de plantillas de solución](marketplace-publishing-solution-template-creation.md)
|| [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] |

## Otras referencias
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/es-ES/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!----HONumber=Oct15_HO3-->