<a id="step-1-navigate-to-the-virtual-network-gateway" class="xliff"></a>

### Paso 1: Navegación a la puerta de enlace de red virtual

1. En [Azure Portal](https://portal.azure.com), vaya a **Todos los recursos**. 
2. Para abrir la hoja de la puerta de enlace de la red virtual, vaya a la puerta de enlace de la red virtual que desea eliminar y haga clic en ella.

<a id="step-2-delete-connections" class="xliff"></a>

### Paso 2: Eliminación de conexiones

1. En la hoja de la puerta de enlace de red virtual, haga clic en **Conexiones** para ver todas las conexiones a la puerta de enlace.
2. Haga clic en **"..."** en la fila del nombre de la conexión y seleccione **Eliminar** en la lista desplegable.
3. Haga clic en **Sí** para confirmar que quiere eliminar la conexión. Si tiene varias conexiones, elimine cada conexión.

<a id="step-3-delete-the-virtual-network-gateway" class="xliff"></a>

### Paso 3: Eliminación de la puerta de enlace de red virtual

Tenga en cuenta que si tiene una configuración P2S en esta red virtual además de la configuración S2S, al eliminar la puerta de enlace de la red virtual se desconectarán automáticamente todos los clientes P2S sin previo aviso.

1. En la hoja de la puerta de enlace de la red virtual, haga clic en **Introducción**.
2. En la hoja **Información general**, haga clic en **Eliminar** para eliminar la puerta de enlace.
