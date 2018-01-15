# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Pagar por adelantado máquinas virtuales con instancias reservadas de máquina virtual

Pague por adelantado máquinas virtuales y ahorre dinero con instancias reservadas de máquina virtual. Para más información, vea las [ofertas de instancias reservadas de máquina virtual](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Puede comprar instancias reservadas de máquina virtual en [Azure Portal](https://portal.azure.com). Para comprar una instancia reservada de máquina virtual:
-   Debe tener un rol de propietario al menos en suscripción Enterprise o de Pago por uso.
-   Para las suscripciones Enterprise, las compras de reserva deben habilitarse en el [portal de EA](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Comprar una instancia reservada de máquina virtual
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Más servicios** > **Reservas**.
3. Haga clic en **Agregar** para comprar una nueva reserva.
4. Rellene todos los campos obligatorios. Las instancias de máquina virtual en ejecución que coinciden con los atributos seleccionados cumplen los requisitos para obtener el descuento de la reserva. El número real de las instancias de máquina virtual que obtienen el descuento depende el ámbito y la cantidad seleccionada.

    | Campo      | DESCRIPCIÓN|
    |:------------|:--------------|
    |NOMBRE        |Nombre de esta reserva.| 
    |La suscripción|Suscripción que se usa para pagar la reserva. Los costos anticipados de la reserva se cobran en el método de pago de la suscripción. El tipo de suscripción debe ser un contrato Enterprise (número de la oferta: MS-AZR-0017P) o de Pago por uso (número de la oferta: MS-AZR-0003P). Para una suscripción Enterprise, los cargos se deducen del saldo de compromiso monetario de la inscripción o se cobran como uso por encima del límite. Para la suscripción Pago por uso, los cargos se cobran en el método de pago de tarjeta de crédito o factura de la suscripción.|    
    |Scope       |El ámbito de la reserva puede cubrir una o varias suscripciones (ámbito compartido). Si selecciona: <ul><li>Suscripción única: el descuento de la reserva se aplica a las máquinas virtuales de esta suscripción. </li><li>Compartido: el descuento de la reserva se aplica a las máquinas virtuales en ejecución en cualquiera de las suscripciones en el contexto de facturación. Para los clientes de Enterprise, el ámbito compartido es la inscripción e incluye todas las suscripciones (excepto las suscripciones de desarrollo y pruebas) dentro de la inscripción. Para los clientes de Pago por uso, el ámbito compartido incluye todas las suscripciones de Pago por uso creadas por el administrador de la cuenta.</li></ul>|
    |La ubicación    |Región de Azure que está cubierta por la reserva.|    
    |Tamaño de VM     |Tamaño de las instancias de máquina virtual.|
    |Término        |Un año o tres años.|
    |Cantidad    |Número de instancias que se compran dentro de la reserva. La cantidad es el número de instancias de máquina virtual en ejecución a las que se aplica el descuento de facturación. Por ejemplo, si tiene 10 máquinas virtuales Standard_D2 en el Este de EE. UU., especificaría 10 como cantidad para maximizar el beneficio para todas las máquinas en ejecución. |
5. Puede ver el costo de la reserva al hacer clic en **Calcular costo**.

    ![Captura de pantalla antes de enviar la compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Seleccione **Comprar**.
7. Haga clic en **Ver esta reserva** para conocer el estado de la compra.

    ![Captura de pantalla antes de enviar la compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps-after-buying-a-reservation"></a>Pasos siguientes tras comprar una reserva
El descuento de la reserva se aplica automáticamente el número de máquinas virtuales en ejecución que coincidan con el ámbito y los atributos de la reserva. Puede actualizar el ámbito de la reserva a través de [Azure Portal](https://portal.azure.com), PowerShell, CLI o a través de la API. 

Para obtener información sobre cómo administrar una reserva, vea [Instancias reservadas de VM de Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

