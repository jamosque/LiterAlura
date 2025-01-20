Este proyecto es una API REST desarrollada en Java con Spring Boot que te permite gestionar un catálogo de libros.

**Funcionalidades:**

- **Buscar libro por título:** Busca un libro en la API de Gutendex y lo guarda en tu base de datos.
- **Listar todos los libros:** Muestra la lista de libros almacenados en tu base de datos.

**Tecnologías utilizadas:**

- Java
- Spring Boot
- Spring Data JPA
- PostgreSQL
- Gutendex API

**Cómo ejecutar el proyecto:**

1. Clona este repositorio: `git clone https://github.com/tu-usuario/literalura.git`
2. Configura la base de datos:
   - Crea una base de datos PostgreSQL llamada "literalura".
   - Actualiza las credenciales de la base de datos en el archivo `src/main/resources/application.properties`.
3. Ejecuta la aplicación:
   - Usando Maven: `mvn spring-boot:run`
   - Usando tu IDE: Ejecuta la clase principal `LiteraluraApplication`.

**Endpoints de la API:**

- **POST /libros/buscar-por-titulo?titulo={titulo}**: Busca un libro por su título.
- **GET /libros**: Lista todos los libros.

**Documentación de la API:**

La documentación de la API está disponible en la siguiente URL una vez que la aplicación esté en ejecución:

```
http://localhost:8080/swagger-ui/index.html
```

http://localhost:8080/swagger-ui/index.html
