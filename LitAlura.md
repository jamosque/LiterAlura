## Estructura del proyecto y archivos

Para este proyecto, vamos a crear una API REST con Spring Boot que interactúe con la API de Gutendex y almacene la información de los libros en una base de datos PostgreSQL.

**Estructura de carpetas:**

```
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── alura
│   │   │           └── literalura
│   │   │               ├── modelo
│   │   │               │   ├── Autor.java
│   │   │               │   └── Libro.java
│   │   │               ├── repositorio
│   │   │               │   ├── AutorRepositorio.java
│   │   │               │   └── LibroRepositorio.java
│   │   │               ├── controlador
│   │   │               │   └── LibroControlador.java
│   │   │               └── servicio
│   │   │                   └── LibroServicio.java
│   │   └── resources
│   │       └── application.properties
│   └── test
│       └── java
│           └── com
│               └── alura
│                   └── literalura
│                       └── LiteraluraApplicationTests.java
└── pom.xml

```

**Descripción de los archivos:**

**pom.xml:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.4</version>
        <relativePath/> </parent>
    <groupId>com.alura</groupId>
    <artifactId>literalura</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>literalura</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
            <version>2.0.2</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

**src/main/java/com/alura/literalura/modelo/Libro.java:**

```java
package com.alura.literalura.modelo;

import jakarta.persistence.*;

import java.util.List;

@Entity
public class Libro {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String titulo;

    @ManyToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})
    @JoinTable(name = "libro_autor",
            joinColumns = @JoinColumn(name = "libro_id"),
            inverseJoinColumns = @JoinColumn(name = "autor_id"))
    private List<Autor> autores;

    private String idioma;

    private Integer descargas;

    public Libro() {
    }

    public Libro(String titulo, List<Autor> autores, String idioma, Integer descargas) {
        this.titulo = titulo;
        this.autores = autores;
        this.idioma = idioma;
        this.descargas = descargas;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitulo() {
        return titulo;
    }

    public void setTitulo(String titulo) {
        this.titulo = titulo;
    }

    public List<Autor> getAutores() {
        return autores;
    }

    public void setAutores(List<Autor> autores) {
        this.autores = autores;
    }

    public String getIdioma() {
        return idioma;
    }

    public void setIdioma(String idioma) {
        this.idioma = idioma;
    }

    public Integer getDescargas() {
        return descargas;
    }

    public void setDescargas(Integer descargas) {
        this.descargas = descargas;
    }
}
```

**src/main/java/com/alura/literalura/modelo/Autor.java:**

```java
package com.alura.literalura.modelo;

import jakarta.persistence.*;

import java.time.LocalDate;
import java.util.List;

@Entity
public class Autor {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nombre;

    private LocalDate fechaNacimiento;

    @ManyToMany(mappedBy = "autores")
    private List<Libro> libros;

    public Autor() {
    }

    public Autor(String nombre, LocalDate fechaNacimiento) {
        this.nombre = nombre;
        this.fechaNacimiento = fechaNacimiento;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public LocalDate getFechaNacimiento() {
        return fechaNacimiento;
    }

    public void setFechaNacimiento(LocalDate fechaNacimiento) {
        this.fechaNacimiento = fechaNacimiento;
    }

    public List<Libro> getLibros() {
        return libros;
    }

    public void setLibros(List<Libro> libros) {
        this.libros = libros;
    }
}
```

**src/main/java/com/alura/literalura/repositorio/LibroRepositorio.java:**

```java
package com.alura.literalura.repositorio;

import com.alura.literalura.modelo.Libro;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface LibroRepositorio extends JpaRepository<Libro, Long> {
    Optional<Libro> findByTitulo(String titulo);
}
```

**src/main/java/com/alura/literalura/repositorio/AutorRepositorio.java:**

```java
package com.alura.literalura.repositorio;

import com.alura.literalura.modelo.Autor;
import org.springframework.data.jpa.repository.JpaRepository;

public interface AutorRepositorio extends JpaRepository<Autor, Long> {
}
```

**src/main/java/com/alura/literalura/controlador/LibroControlador.java:**

```java
package com.alura.literalura.controlador;

import com.alura.literalura.modelo.Libro;
import com.alura.literalura.servicio.LibroServicio;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import io.swagger.v3.oas.annotations.tags.Tag;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/libros")
@Tag(name = "Libros", description = "Controlador para la gestión de libros")
public class LibroControlador {

    @Autowired
    private LibroServicio libroServicio;

    @Operation(summary = "Obtiene un libro por su título", description = "Busca un libro en la API de Gutendex y lo guarda en la base de datos.")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "201", description = "Libro encontrado y guardado en la base de datos.",
                    content = {@Content(mediaType = "application/json",
                            schema = @Schema(implementation = Libro.class))}),
            @ApiResponse(responseCode = "404", description = "Libro no encontrado en la API de Gutendex.",
                    content = @Content),
            @ApiResponse(responseCode = "409", description = "El libro ya existe en la base de datos.",
                    content = @Content)})
    @PostMapping("/buscar-por-titulo")
    public ResponseEntity<Libro> buscarLibroPorTitulo(@RequestParam String titulo) {
        return new ResponseEntity<>(libroServicio.buscarLibroPorTitulo(titulo), HttpStatus.CREATED);
    }

    @Operation(summary = "Lista todos los libros", description = "Devuelve una lista con todos los libros guardados en la base de datos.")
    @ApiResponse(responseCode = "200", description = "Lista de libros obtenida correctamente.",
            content = {@Content(mediaType = "application/json",
                    schema = @Schema(implementation = Libro.class))})
    @GetMapping
    public List<Libro> listarLibros() {
        return libroServicio.listarLibros();
    }

}
```

**src/main/java/com/alura/literalura/servicio/LibroServicio.java:**

```java
package com.alura.literalura.servicio;

import com.alura.literalura.modelo.Autor;
import com.alura.literalura.modelo.Libro;
import com.alura.literalura.repositorio.AutorRepositorio;
import com.alura.literalura.repositorio.LibroRepositorio;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Objects;

@Service
public class LibroServicio {

    @Autowired
    private LibroRepositorio libroRepositorio;

    @Autowired
    private AutorRepositorio autorRepositorio;

    public Libro buscarLibroPorTitulo(String titulo){
        if (libroRepositorio.findByTitulo(titulo).isPresent()) {
            throw new IllegalArgumentException("El libro ya existe en la base de datos.");
        }
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<LibroGutendex[]> response = restTemplate.getForEntity(
                "https://gutendex.com/books?search=" + titulo,
                LibroGutendex[].class);
        if (response.getBody() == null || response.getBody().length == 0) {
            throw new IllegalArgumentException("Libro no encontrado.");
        }
        LibroGutendex libroGutendex = response.getBody()[0];
        List<Autor> autores = new ArrayList<>();
        for (AutorGutendex autorGutendex : libroGutendex.getAutores()) {
            Autor autor = new Autor(autorGutendex.getNombre(), autorGutendex.getFechaNacimiento());
            autores.add(autorRepositorio.save(autor));
        }
        Libro libro = new Libro(libroGutendex.getTitulo(), autores, libroGutendex.getIdioma(), libroGutendex.getDescargas());
        return libroRepositorio.save(libro);
    }

    public List<Libro> listarLibros() {
        return libroRepositorio.findAll();
    }

}
```

**src/main/resources/application.properties:**

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/literalura
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

**Clases adicionales (LibroGutendex y AutorGutendex):**

Estas clases se utilizan para mapear la respuesta de la API de Gutendex.

```java
// LibroGutendex.java
public class LibroGutendex {
    private String titulo;
    private List<AutorGutendex> autores;
    private String idioma;
    private Integer descargas;

    // Constructor, getters y setters
}

// AutorGutendex.java
public class AutorGutendex {
    private String nombre;
    private LocalDate fechaNacimiento;

    // Constructor, getters y setters
}
```

**Documentación:**

* **LibroControlador:** Este controlador define las rutas para interactuar con la API.
    * `/buscar-por-titulo`: Busca un libro por título en la API de Gutendex, lo guarda en la base de datos y lo devuelve.
    * `/`: Lista todos los libros guardados en la base de datos.
* **LibroServicio:** Esta clase contiene la lógica de negocio para la gestión de libros.
    * `buscarLibroPorTitulo`: Implementa la lógica para buscar un libro en la API de Gutendex y guardarlo en la base de datos.
    * `listarLibros`: Obtiene la lista de todos los libros de la base de datos.
* **LibroRepositorio:** Interfaz que extiende JpaRepository para realizar operaciones CRUD en la entidad Libro.
* **AutorRepositorio:** Interfaz que extiende JpaRepository para realizar operaciones CRUD en la entidad Autor.
* **Libro:** Entidad que representa un libro en la base de datos.
* **Autor:** Entidad que representa un autor en la base de datos.

**Consideraciones:**

* Asegúrate de tener PostgreSQL instalado y configurado en tu máquina.
* Reemplaza las credenciales de la base de datos en el archivo `application.properties` con tus propias credenciales.
* Puedes usar la dependencia `springdoc-openapi-starter-webmvc-ui` para generar la documentación de la API automáticamente.
* Este es un ejemplo básico, puedes añadir más funcionalidades y endpoints según los requisitos del desafío.
