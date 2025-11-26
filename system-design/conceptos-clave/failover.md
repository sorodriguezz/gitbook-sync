---
description: Definición de conmutación por error
---

# Failover

La conmutación por error es la capacidad de cambiar automáticamente y sin problemas a un sistema de respaldo confiable. Cuando un componente o sistema principal falla, un modo operativo de reserva o redundancia debería lograr la conmutación por error y reducir o eliminar el impacto negativo en los usuarios.

Para lograr redundancia ante un fallo o la finalización anormal de una versión anteriormente activa, una base de datos, sistema, servidor u otro componente de hardware o red en espera debe estar siempre listo para entrar en funcionamiento automáticamente. En otras palabras, todas las técnicas de respaldo, incluidos los sistemas de servidores informáticos en espera, deben ser inmunes a fallos, ya que la conmutación por error es fundamental para la recuperación ante desastres (DR).

### ¿Qué es una conmutación por error? (Failover)

La automatización de la conmutación por error en servidores incluye condiciones de pulso o latido. Es decir, los cables de latido conectan dos o más servidores en una red, con el servidor principal siempre activo. Mientras el latido continúa o percibe el pulso, el servidor secundario simplemente descansa.

Sin embargo, si el servidor secundario detecta algún cambio en el pulso del servidor principal de conmutación por error, iniciará sus instancias y asumirá las operaciones del servidor principal. También enviará un mensaje al técnico o al centro de datos solicitando que restablezcan el servidor principal en línea. Algunos sistemas, denominados automatizados con configuración de aprobación manual, simplemente alertan al técnico o al centro de datos, solicitando que el cambio en el servidor se realice manualmente.

La virtualización simula un entorno informático mediante una máquina virtual o pseudomáquina que ejecuta software host. De esta forma, el proceso de conmutación por error puede ser independiente de los componentes físicos de hardware de los servidores informáticos.

### ¿Cómo funciona la conmutación por error ?

Las configuraciones más comunes para alta disponibilidad (HA) son activo-activo, activo-pasivo o activo-en espera. Cada técnica de implementación logra la conmutación por error de forma diferente, aunque ambas mejoran la confiabilidad.

Normalmente, al menos dos nodos que ejecutan activa y simultáneamente el mismo tipo de servicio conforman un clúster de alta disponibilidad activo-activo. Este clúster distribuye las cargas de trabajo entre todos los nodos de forma más uniforme, evitando la sobrecarga de un solo nodo y logrando el equilibrio de carga. Además, al mantener más nodos disponibles, el rendimiento y los tiempos de respuesta mejoran. Para garantizar que el clúster de alta disponibilidad funcione sin problemas y logre redundancia, las configuraciones y ajustes individuales de los nodos deben ser idénticos.

Por el contrario, en un clúster activo-pasivo, aunque debe haber al menos dos nodos, no todos están activos. En un sistema de dos nodos con el primero activo, el segundo permanece pasivo o en espera como servidor de conmutación por error. En este modo operativo en espera, puede permanecer listo si el servidor principal activo deja de funcionar para servir como respaldo. Sin embargo, a menos que se produzca un fallo, los clientes solo se conectan al servidor activo.

Al igual que en el clúster activo-activo, ambos servidores del clúster activo-en espera deben estar configurados con la misma configuración. De esta forma, los clientes no percibirán ningún cambio en el servicio, incluso si el enrutador o servidor de conmutación por error debe tomar el control.

Claramente, en un clúster activo-en espera, aunque el nodo en espera siempre está en ejecución, la utilización real se acerca a cero.

En un clúster activo-activo, la utilización de ambos nodos se acerca al 50%, aunque cada uno puede gestionar toda la carga por sí solo. Sin embargo, esto también significa que un fallo en un nodo puede reducir el rendimiento si un nodo con configuración activo-activo gestiona más de la mitad de la carga de forma constante.

El tiempo de interrupción durante un fallo es prácticamente nulo con una configuración de alta disponibilidad (HA) activa-activa, ya que ambas rutas están activas. Con una configuración activa-pasiva, el tiempo de interrupción puede ser mayor, ya que el sistema debe cambiar de un nodo a otro, lo cual requiere tiempo.

### ¿Qué es un clúster de conmutación por error?

Un clúster de conmutación por error es un conjunto de servidores informáticos que proporcionan tolerancia a fallos (FT), disponibilidad continua (CA) o alta disponibilidad (HA) en conjunto. Las configuraciones de red de clústeres de conmutación por error pueden usar máquinas virtuales (VM), solo hardware físico o ambos.

Si uno de los servidores de un clúster de conmutación por error falla, se activa el proceso de conmutación por error. Esto envía instantáneamente la carga de trabajo del componente fallido a otro nodo del clúster, lo que evita el tiempo de inactividad.

Proporcionar alta disponibilidad (HA) o capacidad de conmutación por error (CA) para aplicaciones y servicios es el objetivo principal de un clúster de conmutación por error. También conocidos como clústeres tolerantes a fallos (FT), los clústeres de CA eliminan el tiempo de inactividad cuando fallan los sistemas principales, lo que permite a los usuarios finales seguir usando aplicaciones y servicios sin interrupciones ni tiempos de espera.

Por el contrario, a pesar de una posible breve interrupción del servicio, los clústeres de alta disponibilidad ofrecen un tiempo de inactividad mínimo, recuperación automática y ausencia de pérdida de datos. El proceso de recuperación en clústeres de alta disponibilidad se puede configurar mediante las herramientas de administración de clústeres de conmutación por error, incluidas en la mayoría de las soluciones de clústeres de conmutación por error.

En un sentido más amplio, un clúster consta de dos o más nodos o servidores, generalmente conectados físicamente mediante cables y software. Algunas implementaciones de conmutación por error incluyen tecnologías de agrupación en clúster adicionales, como procesamiento paralelo o concurrente, balanceo de carga y soluciones de almacenamiento en la nube.

La conmutación por error de Internet consiste esencialmente en una conexión a Internet redundante o secundaria que se utiliza como enlace de conmutación por error en caso de fallo. Esto puede considerarse como otra función de conmutación por error en los servidores.

### ¿Qué es una conmutación por error del servidor de aplicaciones?

Los servidores de aplicaciones son simplemente servidores que ejecutan aplicaciones. Esto significa que la conmutación por error de servidores de aplicaciones es una estrategia de conmutación por error para proteger este tipo de servidores.

Como mínimo, estos servidores de aplicaciones deben tener nombres de dominio únicos y, idealmente, deberían ejecutarse en servidores diferentes. Las prácticas recomendadas para clústeres de conmutación por error suelen incluir el balanceo de carga del servidor de aplicaciones.

### ¿Qué son las pruebas de conmutación por error?

Las pruebas de conmutación por error validan la capacidad de un sistema durante una falla del servidor para asignar suficientes recursos a la recuperación. En otras palabras, las pruebas de conmutación por error evalúan la capacidad de conmutación por error de los servidores.

La prueba determinará si el sistema tiene la capacidad, en caso de cualquier tipo de terminación anormal o fallo, para gestionar los recursos adicionales necesarios y trasladar las operaciones a sistemas de respaldo. Por ejemplo, las pruebas de conmutación por error y recuperación determinan la capacidad del sistema para gestionar y alimentar una CPU adicional o varios servidores una vez que alcanza un umbral de rendimiento, que a menudo se supera durante fallos críticos. Esto pone de relieve la importante relación entre las pruebas de conmutación por error, la resiliencia y la seguridad.

### ¿Qué es la conmutación por error y la conmutación por recuperación?

En informática y tecnologías relacionadas, como las redes, la conmutación por error es el proceso de transferir operaciones a una instalación de recuperación de respaldo. El sitio de respaldo en la conmutación por error suele ser una red informática, un componente de hardware, un sistema o un servidor en espera o redundante, a menudo en una ubicación secundaria de recuperación ante desastres (DR). Normalmente, la conmutación por error implica el uso de una herramienta o servicio de conmutación por error para detener y reiniciar temporalmente las operaciones desde una ubicación remota.

Una operación de recuperación implica el retorno de la producción a su ubicación original tras un período de mantenimiento programado o un desastre. Es el retorno del estado de espera a la plena funcionalidad.

Normalmente, los diseñadores de sistemas ofrecen capacidad de conmutación por error en sistemas, servidores o redes que exigen CA, HA o un alto nivel de confiabilidad. Las prácticas de conmutación por error también se han vuelto menos dependientes del hardware físico, con poca o ninguna interrupción del servicio gracias al uso de software de virtualización.

### ¿Druva ofrece una estrategia de conmutación por error en la nube?

Con la recuperación al sitio principal con un solo clic y la mitigación posterior al evento, Druva ofrece algo único en la industria. Una configuración simple e idéntica de sus máquinas virtuales principal y de conmutación por error es el primer paso. La transferencia de datos comienza una vez conectados los discos de las máquinas virtuales y, una vez completada, se redirigen las conexiones DNS y se reinician las máquinas virtuales principales.

Ahora más que nunca, las amenazas de los datos de los trabajadores remotos y los ciberatacantes están en aumento. Los datos son el combustible que su empresa necesita; protéjalos con una sólida estrategia de recuperación ante desastres. Potencie su enfoque aprovechando el alcance global y la escalabilidad de Druva, basado en AWS.

