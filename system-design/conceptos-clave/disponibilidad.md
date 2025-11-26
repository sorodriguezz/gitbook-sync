# Disponibilidad

## ¿Qué es la disponibilidad? <a href="#what-is-availability" id="what-is-availability"></a>

> La disponibilidad se refiere a la proporción de tiempo que un sistema está operativo y accesible cuando es necesario.

Generalmente se expresa como un porcentaje, que indica el tiempo de actividad del sistema durante un período específico.

La definición formal de disponibilidad es:

```
Availability = Uptime / (Uptime + Downtime)
```

**Tiempo de actividad** : el período durante el cual un sistema es funcional y accesible.

**Tiempo de inactividad** : período durante el cual un sistema no está disponible debido a fallas, mantenimiento u otros problemas.

## Niveles de disponibilidad <a href="#availability-tiers" id="availability-tiers"></a>

La disponibilidad suele expresarse en  `"nines"`. Cuanto mayor sea la disponibilidad, menor será el tiempo de inactividad.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.12.54.png" alt="" width="375"><figcaption></figcaption></figure>

Cada "nueve" adicional representa una mejora de un orden de magnitud en la disponibilidad.

> **Ejemplo:**  una disponibilidad del 99,99% representa una mejora **de 10 veces en el tiempo de actividad en comparación con el 99,9 %.**

## Estrategias para mejorar la disponibilidad <a href="#strategies-for-improving-availability" id="strategies-for-improving-availability"></a>

### 1. Redundancia <a href="#id-1-redundancy" id="id-1-redundancy"></a>

La redundancia implica tener componentes de respaldo que puedan tomar el control cuando los componentes principales fallan.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.14.24.png" alt="" width="375"><figcaption></figcaption></figure>

**Técnicas:**

* **Redundancia de servidor** : implementar múltiples servidores para manejar solicitudes, garantizando que si un servidor falla, los demás puedan continuar brindando servicio.
* **Redundancia de base de datos:** creación de una base de datos de réplica que pueda tomar el control si la base de datos principal falla.
* **Redundancia geográfica** : distribución de recursos en múltiples ubicaciones geográficas para mitigar el impacto de fallas regionales.

### 2. Equilibrio de carga <a href="#id-2-load-balancing" id="id-2-load-balancing"></a>

El equilibrio de carga distribuye el tráfico de red entrante entre múltiples servidores para garantizar que ningún servidor se convierta en un cuello de botella, mejorando así tanto el rendimiento como la disponibilidad.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.15.20.png" alt="" width="375"><figcaption></figcaption></figure>

**Técnicas:**

* **Balanceadores de carga de hardware** : dispositivos físicos que distribuyen el tráfico según reglas preconfiguradas.
* **Balanceadores de carga de software** : soluciones de software que gestionan la distribución del tráfico, como HAProxy, Nginx o soluciones basadas en la nube como AWS Elastic Load Balancer.

### 3. Mecanismos de conmutación por error <a href="#id-3-failover-mechanisms" id="id-3-failover-mechanisms"></a>

Los mecanismos de conmutación por error cambian automáticamente a un sistema redundante cuando se detecta una falla.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.16.09.png" alt="" width="375"><figcaption></figcaption></figure>

**Técnicas:**

* **Conmutación por error activo-pasivo** : un componente activo principal está respaldado por un componente pasivo en espera que asume el control en caso de falla.
* **Conmutación por error activo-activo** : Todos los componentes están activos y comparten la carga. Si uno falla, los demás componentes continúan gestionando la carga sin problemas.

### 4. Replicación de datos <a href="#id-4-data-replication" id="id-4-data-replication"></a>

La replicación de datos implica copiar datos de una ubicación a otra para garantizar que estén disponibles incluso si una ubicación falla.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.16.59.png" alt="" width="375"><figcaption></figcaption></figure>

**Técnicas:**

* **Replicación sincrónica** : los datos se replican en tiempo real para garantizar la coherencia en todas las ubicaciones.
* **Replicación asincrónica** : los datos se replican con un retraso, lo que puede ser más eficiente pero puede generar ligeras inconsistencias en los datos.

### 5. Monitoreo y alertas <a href="#id-5-monitoring-and-alerts" id="id-5-monitoring-and-alerts"></a>

La monitorización continua de la salud implica verificar el estado de los componentes del sistema para detectar fallas de manera temprana y activar alertas para tomar medidas inmediatas.

<figure><img src="../../.gitbook/assets/Captura de pantalla 2025-11-25 a la(s) 20.17.43.png" alt="" width="375"><figcaption></figcaption></figure>

**Técnicas:**

* **Señales de latido** : señales regulares enviadas entre componentes para verificar su estado.
* **Comprobaciones de estado** : scripts o herramientas automatizados que realizan comprobaciones periódicas del estado de los componentes.
* **Sistemas de alerta** : herramientas como PagerDuty u OpsGenie que notifican a los administradores sobre problemas detectados.

## Mejores prácticas para alta disponibilidad <a href="#best-practices-for-high-availability" id="best-practices-for-high-availability"></a>

1. **Diseño para fallas** : suponga que cualquier componente de su sistema puede fallar en cualquier momento y diseñe su sistema en consecuencia.
2. **Implementar controles de salud** : los controles de salud regulares le permiten detectar y responder a los problemas antes de que se conviertan en fallas críticas.
3. **Utilice múltiples zonas de disponibilidad** : distribuya su sistema en diferentes centros de datos para evitar fallas localizadas.
4. **Practique la ingeniería del caos** : introduzca fallas intencionalmente para probar la resiliencia del sistema.
5. **Implementar disyuntores** : evite fallas en cascada cortando rápidamente los servicios problemáticos.
6. **Utilice el almacenamiento en caché de forma inteligente** : el almacenamiento en caché puede mejorar la disponibilidad al reducir la carga en los sistemas back-end.
7. **Planifique la capacidad** : asegúrese de que su sistema pueda manejar aumentos de carga esperados e inesperados.

La disponibilidad es un aspecto crítico del diseño del sistema que garantiza que los usuarios puedan acceder a los servicios de manera confiable y continua.

Al implementar estrategias como redundancia, equilibrio de carga, mecanismos de conmutación por error y replicación de datos, puede diseñar sistemas de alta disponibilidad.
