---
description: Documento que explica la diferencias entre DTO y DAO
---

# Patrón DTO vs Patrón DAO

## Explicación teorica

### Data Transfer Object (DTO)

Se utiliza principalmente para transferir datos entre software componentes o capas.

<mark style="color:purple;">Simplicidad</mark>: Sirven únicamente para el _transporte de datos_, sin contener lógica de negocio o comportamiento complejo.

<mark style="color:purple;">Planos</mark>: Poseen propiedades básicas y accesibles a través de métodos simples como getters y setters.

<mark style="color:purple;">Serializables</mark>: Pueden ser transformados a formatos como JSON o XML para su transmisión entre procesos o servicios.

### Data Access Object (DAO)

Componente de software que proporciona una interfaz abstracta para algún tipo de base de datos o mecanismo de _persistencia_.

<mark style="color:purple;">Abstracción</mark>: Separan las operaciones de la base de datos de la lógica de negocio.

<mark style="color:purple;">Encapsulamiento</mark>: Centralizan el acceso a datos, ocultado los detalles de la base de datos.

<mark style="color:purple;">Reusabilidad y Mantenibilidad</mark>: Promueven la eficiencia al gestionar los datos, facilitando actualizaciones y mantenimiento.















