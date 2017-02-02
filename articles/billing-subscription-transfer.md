---
title: "Transferencia de la propiedad de una suscripción de Azure| Microsoft Docs"
description: "Transferencia de una suscripción de Azure a otro usuario y algunas preguntas frecuentes (P+F) sobre el proceso"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: f7b097883ca0340a3af83fc9531acfe571295994
ms.openlocfilehash: c0323a63676a784660146f84a7813cf2cd5de7b0


---
# <a name="transferring-ownership-of-an-azure-subscription"></a>Transferencia de la propiedad de una suscripción de Azure

Puede transferir fácilmente la suscripción a otro usuario para suscripciones de pago por uso, MSDN, Action Pack o BizSpark. Puede cambiar el administrador de cuenta en cualquier suscripción de pago por uso, MSDN, Action Pack o BizSpark que usted posea, sin importar el país en el que trabaja. También es posible transferir compras de Azure Marketplace para estos tipos de suscripción. 

Es posible que quiera transferir la propiedad de una suscripción de Azure si:

* Necesita transferir la titularidad de la facturación de su suscripción de Azure a otra persona.
* Desea cambiar la cuenta usada para suscribirse a Azure. Quizás usó su cuenta de Microsoft pero pretendía usar su cuenta profesional o educativa.
* Desea mover la suscripción de Azure de un directorio a otro.
* Tiene Azure y Office 365 en distintos inquilinos y desea consolidarlos.

Para cambiar su suscripción por una oferta diferente, consulte [Cambio de la suscripción de Azure a otra oferta](billing-how-to-switch-azure-offer.md). 

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>Transferencia de la propiedad de una suscripción de Azure
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Inicie sesión en <https://account.windowsazure.com/Subscriptions>. Debe ser el administrador de cuenta para realizar una transferencia de propiedad. Para más información sobre cómo averiguar quién es el administrador de cuenta de la suscripción, consulte las [Preguntas más frecuentes](#faq).
2. Seleccione la suscripción que va a transferir.
3. Haga clic en la opción **Transferir suscripción** .

   ![Pestaña Suscripciones de cuenta de Azure](./media/billing-subscription-transfer/image1.png)
4. Siga las indicaciones para especificar al destinatario.

   ![Cuadro de diálogo Transferir suscripción](./media/billing-subscription-transfer/image2.PNG)
5. El destinatario obtiene automáticamente un correo electrónico con un vínculo de aceptación.

   ![Correo electrónico de transferencia de suscripción a destinatario](./media/billing-subscription-transfer/image3.png)
6. El destinatario hace clic en el vínculo y sigue las instrucciones, incluyendo la especificación de su información de pago.

   ![Página web de primera transferencia de suscripción](./media/billing-subscription-transfer/image4.png)

   ![Página web de segunda transferencia de suscripción](./media/billing-subscription-transfer/image5.png)
7. ¡Éxito! La suscripción ya está transferida.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes
* <a name="whoisaa"></a> **¿Quién es el administrador de cuenta de la suscripción?**

  El administrador de cuenta es la persona que se inscribió o que adquirió la suscripción de Azure. Están autorizados para tener acceso al [Centro de cuentas](https://account.windowsazure.com/Home/Index) y realizar diversas tareas de administración como crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción o cambiar el administrador de servicios. Si no está seguro de quién es el administrador de cuenta de la suscripción, use los pasos siguientes para averiguarlo.

  1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
  2. En el menú de concentrador, seleccione **Suscripción**.
  3. Seleccione la suscripción que desee comprobar y, luego, consulte **Configuración**.
  4. Seleccione **Propiedades**. El administrador de cuentas de la suscripción se muestra en el cuadro **Administrador de cuentas** .  
* **¿Provocan las transferencias de suscripciones un tiempo de inactividad en el servicio?**

  No afectan al servicio. Si se transfiere la suscripción, se cancela la suscripción en el administrador de cuenta actual y se crea una suscripción en la cuenta del destinatario. La nueva suscripción está asociada a los servicios de Azure subyacentes. El identificador de la suscripción no cambia.
* **¿Cómo puedo usar este proceso para cambiar el directorio de suscripción?**   
  Se crea una suscripción de Azure en el directorio al que pertenece el administrador de cuenta. Para cambiar el directorio, transfiera la suscripción a una cuenta de usuario en el directorio de destino. Cuando el usuario completa los pasos para aceptar la transferencia, la suscripción se mueve automáticamente al directorio de destino.
* **¿Si asumo la propiedad de la facturación de una suscripción de otra organización, estos seguirán teniendo acceso a mis recursos?**

  Si la suscripción se transfiere a otro inquilino, los usuarios asociados al inquilino anterior perderán el acceso a la suscripción. Aunque un usuario deje de ser administrador o coadministrador de servicios, puede seguir teniendo acceso a la suscripción a través de otros mecanismos de seguridad. Entre ellos se incluyen los siguientes:

  * Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para obtener más información, consulte [Crear y cargar un certificado de administración para Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
  * Claves de acceso para servicios como Almacenamiento. Para más información, consulte [Acerca de las cuentas de almacenamiento de Azure](storage/storage-create-storage-account.md).
  * Credenciales de acceso remoto para servicios como Máquinas virtuales de Azure

  Esta lista no está completa. El destinatario debe considerar la actualización de todos los secretos asociados al servicio si es necesario restringir el acceso a sus recursos. La mayoría de los recursos se pueden actualizar como se indican a continuación:

  1. Vaya al [Portal de Azure](https://portal.azure.com).
  2. En el menú Concentrador, seleccione **Todos los recursos**.
  3. Seleccione el recurso. 
  4. En la hoja de recursos, haga clic en **Configuración**. Aquí puede ver y actualizar los secretos existentes.
* **Si transfiero la suscripción en medio del ciclo de facturación, ¿paga el destinatario todo el ciclo de facturación?**

  El remitente es el responsable del pago de cualquier uso que se notificó hasta el momento en que se completó la transferencia. El destinatario es el responsable del uso notificado desde el momento de la transferencia en adelante. Puede haber una parte del uso que se realizara antes de la transferencia, pero que se notificara con posterioridad. El uso se incluye en la factura del destinatario.
* **¿Tiene el destinatario acceso al historial de facturación y uso?**

  La única información disponible para el destinatario es la cantidad de la última factura o si la suscripción se transfirió antes de que se generara la primera factura, el balance actual. El resto del historial de facturación y uso no se transfiere con la suscripción.
* **¿Se puede cambiar la oferta durante una transferencia?**

  La oferta debe permanecer igual. Para cambiar la oferta, es preciso [ponerse en contacto con el servicio de soporte técnico](http://go.microsoft.com/fwlink/?LinkID=619338).
* **¿Puedo transferir una suscripción a una cuenta de usuario de otro país?**

  No, si se transfiere una suscripción a una cuenta de usuario en otro país, no es compatible. La cuenta de usuario del destinatario debe estar en el mismo país.
* **¿Puede usar el destinatario un método de pago diferente?**

  Sí. Sin embargo, el historial de facturación de la suscripción se divide en dos cuentas.  

* **¿Se verá afectado el método de pago después de transferir una suscripción de Azure?**

Para aceptar una transferencia de suscripción, se debe proporcionar una tarjeta de crédito o un método de pago similar para pagar la suscripción. Por ejemplo, si Roberto transfiere una suscripción a Julia y esta acepta la transferencia, Julia debe proporcionar también el método de pago que usará para pagar la suscripción. Una vez completada la transferencia, ya no se le cobrará más a Roberto la suscripción que pasó a Julia.

* **¿Cómo se migran los datos y servicios de una suscripción de Azure a otra?**

  Consulte [Move resources to new resource group or subscription](./azure-resource-manager/resource-group-move-resources.md) (Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción).

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Pasos siguientes después de aceptar la propiedad de una suscripción
1. Ahora es el administrador de cuenta. Revise y actualice la sección Administrador y coadministradores del servicio. Use la opción de Configuración del [Portal de Azure clásico](https://manage.windowsazure.com) para controlar los administradores. [Más información](http://go.microsoft.com/fwlink/?LinkID=533293).
2. También puede usar el control de acceso basado en roles (RBAC) para su suscripción y sus servicios. Visite [Azure Portal](https://portal.azure.com). [Más información sobre RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Actualice las credenciales asociadas a los servicios de esta suscripción. Entre ellos se incluyen los siguientes:
   * Certificados de administración que conceden al usuario derechos administrativos a los recursos de la suscripción. Para obtener más información, consulte [Create and upload a management certificate for Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
   * Claves de acceso para servicios como Almacenamiento. Para más información, consulte [Acerca de las cuentas de almacenamiento de Azure](storage/storage-create-storage-account.md).
   * Credenciales de acceso remoto para servicios como Máquinas virtuales de Azure
4. Actualice las alertas de facturación para esta suscripción en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions). [Más información](http://go.microsoft.com/fwlink/?LinkID=533292)
5. Si trabaja con un asociado, considere la posibilidad de actualizar el identificador del asociado en esta suscripción. Puede actualizar el identificador de asociado en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions).


## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente. 





<!--HONumber=Dec16_HO2-->


