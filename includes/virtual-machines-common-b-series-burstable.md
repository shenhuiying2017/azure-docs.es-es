
La familia de máquinas virtuales de la serie B le permite elegir qué tamaño de máquina virtual proporciona el rendimiento base necesario para su carga de trabajo, con la posibilidad de ampliar el rendimiento de la CPU hasta el 100% de una vCPU con procesador Intel® Broadwell E5-2673 v4 2.3GHz o Intel® Haswell 2.4 GHz E5-2673 v3 .

Las máquinas virtuales de la serie B son idóneas para cargas de trabajo que no necesitan un rendimiento completo de la CPU de forma continua, como los servidores web, pequeñas bases de datos y entornos de desarrollo y de prueba. Estas cargas de trabajo suelen necesitar unos requisitos de rendimiento ampliables. La serie B le brinda la posibilidad de adquirir un tamaño de máquina virtual con un rendimiento base al tiempo que la instancia de máquina virtual acumula créditos si utiliza un rendimiento por debajo de este nivel de base. Cuando la máquina virtual ha acumulado crédito se puede ampliar por encima de la base de referencia de esta con un uso de hasta un 100 % de la vCPU si la aplicación necesita el mayor rendimiento de CPU posible.

La serie B incluye los siguientes seis tamaños de máquina virtual:

| Tamaño          | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Rendimiento base de CPU de la máquina virtual | Rendimiento máximo de CPU de la máquina virtual | Créditos ingresados / hora | Créditos máximos ingresados |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Preguntas y respuestas 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: ¿Cómo obtener un 135% del rendimiento base de una máquina virtual?
**R**: ese 135% se debe compartir entre las 8 vCPU que componen el tamaño de la máquina virtual. Por ejemplo, si la aplicación usa 4 de los 8 núcleos que están trabajando en el procesamiento por lotes y cada una de esas 4 vCPU se ejecuta al 30% de uso, la cantidad total de rendimiento de la CPU de la máquina virtual equivaldría al 120%.  Lo que significa que la máquina virtual podría acumular créditos basándose en este 15% de diferencia con el rendimiento base.  Pero también significa que cuando tenga créditos disponibles, esa misma máquina virtual puede utilizar el 100% de las 8 vCPU lo cual le proporcionaría a esa máquina virtual un rendimiento de CPU máximo del 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: ¿Cómo puedo supervisar mi saldo de crédito y uso?
**R**: Presentaremos 2 nuevas métricas en las próximas semanas, la métrica **Credit** le permitirá ver cuántos créditos ha acumulado su máquina virtual y la métrica **ConsumedCredit** le mostrará cuántos créditos de la CPU ha consumido la máquina virtual desde el banco.    Podrá ver estas métricas desde el panel de métricas del portal o mediante programación a través de las API de Azure Monitor.

Para más información acerca de cómo acceder a los datos de las métricas de Azure, consulte [Información general sobre las métricas en Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>P: ¿Cómo se acumulan los créditos?
**R**: Las tasas de acumulación y consumo de la máquina virtual se establecen de tal forma que una máquina virtual que se esté ejecutando exactamente a su nivel de rendimiento base, no tendrá una acumulación ni un consumo neto de créditos de ampliación.  Una máquina virtual tendrá un aumento neto en el número de créditos siempre que se esté ejecutando por debajo de su nivel de rendimiento base y tendrá una disminución neta cada vez que utilice la CPU por encima de este nivel.

**Ejemplo**: Quiero implementar una máquina virtual con el tamaño B1ms para mi aplicación de base de datos a la que se dedica poco tiempo y atención. Este tamaño permite que mi aplicación use hasta el 20 % de una vCPU como base de referencia, lo cual significa 0,2 créditos por minuto que puedo usar o acumular. 

Mi aplicación está ocupada al principio y al final de la jornada laboral de mis empleados, es decir, de 7:00 a 9:00 A.M. y de 4:00 a 6:00 PM. Durante las otras 20 horas del día, la aplicación suele estar inactiva y solo usa el 10% de la vCPU. Durante las horas de poca actividad, gano 0,2 créditos por minuto y solo consumo 0,1 créditos por minuto, por lo que la máquina virtual acumulará 0,1 x 60 = 6 créditos por hora.  Es decir, durante las 20 horas de poca actividad acumularé 120 créditos.  

Durante las horas punta, la aplicación realiza un uso promedio del 60% de la vCPU, sigo ganando 0,2 créditos por minuto pero consumo 0,6 créditos por minuto, lo cual supone un costo neto de 0,4 créditos por minuto o 0,4 x 60 = 24 créditos por hora. Si tengo 4 horas al día de uso máximo, eso significa 4 x 24 = 96 créditos de consumo al día.

Si tomo los 120 créditos que acumulé durante las horas de poca actividad y le resto los 96 créditos que utilicé durante las horas punta, obtendré 24 créditos adicionales que puedo acumular al día. Estos créditos los podré utilizar en otras ampliaciones de actividades.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: ¿Admite la serie B discos de datos de Premium Storage?
**R**: Sí, toda la serie B admite discos de datos de Premium Storage.   
    


    

    
