Este proyecto se construyo con el framework net8 con una arquitectura de tipo Domain Centric (Hex, Clean, Onion).

Las responsabilidades de este proyecto son únicamente las de un CRUD de ordenes que son publicados a través de un API RESTful, la documentación de los endpoints puede ser consultada en:

https://documenter.getpostman.com/view/22749219/2sAXjM5XuM

## Pasos previos para iniciar el proyecto

El unico paso que requiere este microservicio es el de crear una BD en MySQL y configurar los datos en el archivo appsettings.Development.json, ya con esto es necesario ejecutar el comando Update-Database desde la ventana de Package Manager Console seleccionando el proyecto de Infrastructure, con esto se ejecutaran las migraciones y se crearan las tablas en la BD.

Los principales patrones y estilos de arquitectura que guían este proyecto son:

## Arquitectura de Puertos y Adaptadores

La idea de Puertos y Adaptadores es que la aplicación(Dominio) sea el centro del sistema. Todas las entradas y salidas alcanzan o dejan el dominio a traves de un puerto. Este puerto aisla el dominio de las tecnologias externas, herramientas y mecánicas de entrega.

El dominio mismo nunca deberia tener ningún conocimiento de quien envía o recibe la entrada y salida. Esto le permite al sistema estar asegurado contra la evolución de la tecnología y los requerimientos del negocio.

Más información [aquí](https://www.thinktocode.com/2018/07/19/ports-and-adapters-architecture/)

## CQRS (Commmand Query Responsability Segregation):

Patrón con el cual dividimos nuestro modelo de objetos en dos, un modelo para consulta(Query) y un modelo para comando(Command). Este patrón es recomendado cuando se va desarrollar lógica de negocio compleja porque nos ayuda a separar las responsabilidades y a mantener un modelo de negocio consistente.

- Consulta: modelo a través del cual se divide la responsabilidad para presentar datos en la interfaz de usuario, los objetos se modelan basado en lo que se va a presentar y no en la lógica de negocio.
- Comando: son todas las operaciones que cambian el estado del sistema, este modelo se construye todo el modelo de objetos basado en la lógica de negocio de la aplicación. Las operaciones de cambio de estado del sistema las hacemos a traves de EntityFrameworkCore.

Más información [aquí](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs)

## HealthCheck

Health checks son expuestos por una aplicación como endpoints http. Los endpoints pueden ser configurados para una variedad de escenarios de monitoreo en tiempo real:

Pruebas de Salud : pueden ser usados por orquestadores de contenedores y balanceadores de carga para verificar el estado de salud de una aplicacion.
Uso de memoria, disco, y otros recursos fisicos del servidor que pueden ser monitoreados por estado de saludable.
Health checks pueden testear dependencias de una aplicación, como bases de datos y servicios externos para confirmar la disponibilidad de los mismos.
Más información [aquí](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-3.1)

## Especificaciones técnicas:

- Plantilla de microservicios con Net8 (Top level statements, minimal apis, mapgroup - for endpoints, global usings, records, more...)
- Listo para contenerizar con Docker
- Entity Framework Core 7(MSSql: database + schema) Code First
- FluentValidation
- Repositorio Genérico (muy útil con el manejo de agregados) y extendido (usado para ocultar caracteristicas no necesarias del generico)
- Shadow Properties en entidades : Propiedades de que se añaden a las entidades de dominio sin "envenenar" la definicion propia de la entidad en esa capa
- Inyeccion automática de Domain Services usando anotacion "[DomainService]" y de repositorios "[Repository]"
- MediaTR : registra manejadores de commands y queries de forma automática (via reflexion hace scan del assembly)
- Manejador de Excepciones Global
- Pruebas Unitarias (Domain) con Xunit
- NSubstitute para Mocking
- Pruebas de Integración (Api) con XUnit
- Logs : disco y ElasticSearch
- Swagger
- HealthCheck (para base de datos, endpoint "/healthz")
- Ejemplo de Comand + Query + Handlers
- Exposición de metricas con prometheus

### Estructura del proyecto:

Solucion para VisualStudio(.sln) compuesta de los siguientes carpetas :

- Api : Api Rest, punto de entrada de la aplicación
- Api.Tests : Integration Tests para la Api Rest
- Application : Capa de orquestacion de servicios de dominio; Ports, Commands, Queries, Handlers
- Infrastructure : Adapters
- Domain : Entities, Value Objects, Ports, Domain Services, Aggregates