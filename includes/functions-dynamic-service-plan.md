## <a name="dynamic-service-plan"></a>Plan de servicio dinámico
En el plan de servicio dinámico, las aplicaciones de función se asignarán a una instancia de aplicación de función. Si necesita más instancias, se agregarán dinámicamente.
Esas instancias pueden abarcar varios recursos informáticos, de modo que se podrá sacar el máximo partido de la infraestructura de Azure disponible. Además, las funciones se ejecutarán en paralelo, con lo que se reduce al máximo el tiempo total necesario para procesar las solicitudes. Se suma, en segundos, el tiempo de ejecución de cada función y se agrega mediante la aplicación de función contenedora. Los costos se determinan según el número de instancias, el tamaño de la memoria y el tiempo de ejecución total medido en gigabytes por segundos. Se trata de una opción excelente si los requisitos de proceso son intermitentes o las horas de trabajo suelen ser muy reducidas, ya que permite pagar solo por los recursos de proceso cuando se estén usando realmente.   

### <a name="memory-tier"></a>Nivel de memoria
Según las funciones que requiera, puede seleccionar la cantidad de memoria necesaria para ejecutarlas en la aplicación de función (contenedor de funciones).
Las opciones de tamaño de memoria varían de **128 a 1536 MB**. El tamaño de memoria seleccionada corresponde al espacio de trabajo que necesitan todas las funciones que forman parte de la aplicación de función. Si el código requiere más memoria que el tamaño seleccionado, la instancia de aplicación de función se cerrará debido a la falta de memoria disponible.

### <a name="scaling"></a>Escalado
La plataforma de funciones de Azure evaluará los requisitos de tráfico según los desencadenadores configurados para decidir si escalar o reducir verticalmente. La granularidad de escalado es la aplicación de función. En este caso, escalar verticalmente significa agregar más instancias de una aplicación de función. Y al contrario, a medida que el tráfico disminuye, las instancias de aplicación se deshabilitan; se terminan reduciendo verticalmente a cero cuando no hay ninguna en ejecución.  

### <a name="resource-consumption-and-billing"></a>Consumo de recursos y facturación
En la asignación de recursos de modo dinámico, el proceso se realiza de forma diferente al plan del Servicio de aplicaciones estándar; por lo tanto, el modelo de consumo también es distinto, lo que permite utilizar un modelo de "pago por uso". El consumo se notificará por función de aplicación solo cuando se esté ejecutando el código.  
Se calcula multiplicando el tamaño de memoria (en GB) por la cantidad total de tiempo de ejecución (en segundos) de todas las funciones que se ejecutan en esa aplicación de función. La unidad de consumo será **GB/s (gigabytes por segundos)**.



<!--HONumber=Jan17_HO3-->


