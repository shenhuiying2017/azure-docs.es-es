## Descripción del mantenimiento planeado frente al no planeado
Existen dos tipos de eventos de la plataforma Microsoft Azure que pueden afectar a la disponibilidad de sus máquinas virtuales: el mantenimiento planeado y el mantenimiento no planeado.

- Los **eventos de mantenimiento planeado** son actualizaciones periódicas realizadas por Microsoft en la plataforma Azure subyacente para mejorar en general la fiabilidad, el rendimiento y la seguridad de la infraestructura de la plataforma sobre las que se funcionan sus máquinas virtuales. La mayoría de estas actualizaciones se realizan sin afectar a las máquinas virtuales ni a los servicios en la nube. Sin embargo, en ocasiones estas actualizaciones requieren reiniciar la máquina virtual para aplicar las actualizaciones necesarias a la infraestructura de las plataformas.

- Los **eventos de mantenimiento no planeado** se producen cuando el hardware o la infraestructura física subyacente a su máquina virtual presentan algún tipo de error. Entre estos podemos encontrar errores de la red local, errores de los discos locales u otras errores a nivel de bastidor. Cuando se detecta un error de este tipo, la plataforma Azure automáticamente migrará su máquina virtual desde la máquina física averiada que aloja la máquina virtual hasta una máquina física sin problemas. Estos eventos no son habituales, pero pueden hacer que su máquina virtual se reinicie.

## Siga estas mejores prácticas para diseñar su aplicación para la alta disponibilidad.
Para reducir el impacto del tiempo de parada debido a uno o más de estos eventos, recomendamos las siguientes mejores prácticas de alta disponibilidad para las máquinas virtuales:

* [Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia].
* [Configure cada nivel de aplicación en conjuntos separados de disponibilidad].
* [Combine el equilibrador de carga con los conjuntos de disponibilidad]


### Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia.
Para proporcionar redundancia a la aplicación, recomendamos que agrupe dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual estará disponible y cumplirá el 99,95% de los niveles de servicio contratados de Azure. Para obtener más información, consulte [Acuerdo de Nivel de Servicio para máquinas virtuales](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [AZURE.IMPORTANT] Evite dejar una máquina virtual de instancia única sola en un conjunto de disponibilidad. Las máquinas virtuales en esta configuración no tienen derecho a la garantía de los contratos de nivel de servicio y sufrirán un tiempo de inactividad durante los eventos de Azure mantenimiento planeado.

La plataforma Azure subyacente asigna a cada máquina virtual del conjunto de disponibilidad un **dominio de actualización** y un **dominio de error**. Para un conjunto de disponibilidad determinado, se asignan de forma predeterminada 5 dominios de actualización que el usuario no puede configurar (las implementaciones de Azure Resource Manager pueden aumentarse para proporcionar hasta 20 dominios de actualización), con el fin de indicar grupos de máquinas virtuales y hardware físico subyacente que se pueden reiniciar simultáneamente. Cuando se configuran más de 5 máquinas virtuales dentro de un único conjunto de disponibilidad, la sexta máquina virtual se colocará en el mismo dominio de actualización que la primera, la séptima en el mismo que la segunda y así sucesivamente. El orden en que los dominios de actualización se reiniciarán tal vez no siga una secuencia durante un mantenimiento planeado, pero solo se reiniciarán uno por uno.

Los dominios de error definen un grupo de máquinas virtuales que comparten un origen de alimentación y un interruptor de red comunes. De manera predeterminada, las máquinas virtuales configuradas dentro de su conjunto de disponibilidad se separan en hasta 3 dominios de error para implementaciones de Azure Resource Manager (2 dominios de error para implementaciones clásicas). Aunque colocar las máquinas virtuales en un conjunto de disponibilidad no protege su aplicación contra errores del sistema operativo ni específicos de aplicaciones, limita el impacto de posibles errores de hardware físico, interrupciones de red o cortes de alimentación.

<!--Image reference-->
   ![Dibujo conceptual de la configuración del dominio de actualización y de error](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


### Configure cada nivel de aplicación en conjuntos separados de disponibilidad.

Si las máquinas virtuales son casi idénticas y tienen la misma función en su aplicación, recomendamos que configure un conjunto de disponibilidad para cada nivel de la aplicación. Si coloca dos niveles diferentes en el mismo conjunto de disponibilidad, todas las máquinas virtuales en un mismo nivel de aplicación se podrían reiniciar simultáneamente. Al configurar al menos 2 máquinas virtuales de un conjunto de disponibilidad, se garantiza que al menos esté disponible una en cada nivel.

Por ejemplo, podría poner todas las máquinas virtuales en el front-end de la aplicación que usa IIS, Apache, Nginx, etc., en un solo conjunto de disponibilidad. Asegúrese de que solo las máquinas virtuales de front-end se colocan en el mismo conjunto de disponibilidad. De la misma manera, asegúrese de que solo las máquinas virtuales de niveles de datos se colocan en su propio conjunto de disponibilidad, por ejemplo, las máquinas virtuales replicadas de SQL Server o las de MySQL.

<!--Image reference-->
   ![Niveles de aplicación](./media/virtual-machines-common-manage-availability/application-tiers.png)


### Combinación de un equilibrador de carga con conjuntos de disponibilidad
Combine [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) con un conjunto de disponibilidad para aprovechar al máximo la resistencia de la aplicación. El equilibrador de carga de Azure distribuye el tráfico entre varias máquinas virtuales. El equilibrador de carga de Azure está incluido en nuestras máquinas virtuales de niveles estándar. Tenga en cuenta que no todos los niveles de las máquinas virtuales incluyen el equilibrador de carga de Azure. Para obtener más información sobre el equilibrio de carga en máquinas virtuales, consulte [Equilibrio de carga de máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Si el equilibrador de carga no está configurado para equilibrar el tráfico entre varias máquinas virtuales, entonces cualquier evento de mantenimiento planeado afectará a la única máquina virtual dedicada al tráfico y provocará una interrupción del nivel de la aplicación. Si se colocan varias máquinas virtuales del mismo nivel en el mismo equilibrador de carga y conjunto de disponibilidad, se permitirá tener un tráfico continuamente disponible asistido por, al menos, una instancia.

 

<!-- Link references -->
[Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configure cada nivel de aplicación en conjuntos separados de disponibilidad]: #configure-each-application-tier-into-separate-availability-sets
[Combine el equilibrador de carga con los conjuntos de disponibilidad]: #combine-the-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets

<!---HONumber=AcomDC_0601_2016-->