# Creación de proyecto n-capas

## Crea una solución

```bash
dotnet new sln -n ToDoApp
```

## Crea los proyectos

```bash
dotnet new classlib -n ToDoApp.Domain
dotnet new classlib -n ToDoApp.Business
dotnet new classlib -n ToDoApp.Persistence
dotnet new webapi -n ToDoApp.Presentation
```

## Agrega los proyectos a la solución

```bash
dotnet sln add ToDoApp.Domain
dotnet sln add ToDoApp.Business
dotnet sln add ToDoApp.Persistence
dotnet sln add ToDoApp.Presentation
```

## Añade las referencias entre proyectos

La referencia conecta el proyecto **`ToDoApp.Business`** al proyecto **`ToDoApp.Domain`**, permitiendo que el primero consuma el código del segundo. Esto significa que `ToDoApp.Domain` alojará las clases y entidades (como modelos o interfaces) mientras que `ToDoApp.Business` podrá usarlas directamente. Sin esta referencia, `ToDoApp.Business` no tendría acceso al código definido en `ToDoApp.Domain`. Por otro lado, `ToDoApp.Domain` no conoce ni depende de `ToDoApp.Business`, manteniendo una separación clara de responsabilidades.

```bash
dotnet add ToDoApp.Business/ToDoApp.Business.csproj reference ToDoApp.Domain/ToDoApp.Domain.csproj
dotnet add ToDoApp.Persistence/ToDoApp.Persistence.csproj reference ToDoApp.Domain/ToDoApp.Domain.csproj
dotnet add ToDoApp.Business/ToDoApp.Business.csproj reference ToDoApp.Persistence/ToDoApp.Persistence.csproj
dotnet add ToDoApp.Presentation/ToDoApp.Presentation.csproj reference ToDoApp.Business/ToDoApp.Business.csproj
```

# Entity Framework

## Configura el contexto en `ToDoApp.Persistence`

- Crea un archivo `ToDoDbContext.cs` con el DbContext para la entidad `ToDo`.

### Crea la migración inicial

```bash
dotnet ef migrations add InitialMigration --project ToDoApp.Persistence
dotnet ef database update --project ToDoApp.Persistence
```

# Levantar el Proyecto

## Navega al proyecto de presentación (`Presentation`)

```bash
cd ToDoApp.Presentation
```

## Ejecuta el servidor web

```bash
dotnet run
```

# Crear Proyecto de Pruebas Unitarias

## Crea un nuevo proyecto de pruebas

```bash
dotnet new xunit -n ToDoApp.Business.Tests
```

## Agrega el proyecto de pruebas a la solución

```bash
dotnet sln add ToDoApp.Business.Tests
```

## Agrega referencia al proyecto `ToDoApp.Business`

```bash
dotnet add ToDoApp.Business.Tests/ToDoApp.Business.Tests.csproj reference ToDoApp.Business/ToDoApp.Business.csproj
```

## Instala paquetes necesarios para las pruebas (opcional)

### Moq (para crear mocks)

```bash
dotnet add ToDoApp.Business.Tests package Moq
```

### FluentAssertions (para afirmaciones más legibles)

```bash
dotnet add ToDoApp.Business.Tests package FluentAssertions
```

## Estructura de ejemplo

Archivo: `ToDoServiceTests.cs` en `ToDoApp.Business.Tests`.

```csharp
using Xunit;
using Moq;
using ToDoApp.Business;
using ToDoApp.Domain;

public class ToDoServiceTests
{
    [Fact]
    public void AddToDo_ShouldAddToDoSuccessfully()
    {
        // Arrange
        var mockRepository = new Mock<IRepository<ToDo>>();
        var service = new ToDoService(mockRepository.Object);
        var newToDo = new ToDo { Title = "Test ToDo", Description = "Description" };

        // Act
        service.AddToDo(newToDo);

        // Assert
        mockRepository.Verify(repo => repo.Add(newToDo), Times.Once);
    }

    [Fact]
    public void GetAllToDos_ShouldReturnAllToDos()
    {
        // Arrange
        var mockRepository = new Mock<IRepository<ToDo>>();
        var todos = new List<ToDo>
        {
            new ToDo { Title = "ToDo1", Description = "Desc1" },
            new ToDo { Title = "ToDo2", Description = "Desc2" }
        };

        mockRepository.Setup(repo => repo.GetAll()).Returns(todos);
        var service = new ToDoService(mockRepository.Object);

        // Act
        var result = service.GetAllToDos();

        // Assert
        result.Should().HaveCount(2).And.Contain(todos);
    }
}
```

## Ejecutar Pruebas

```bash
dotnet test
```

## Opcional: Ver detalles de las pruebas

```bash
dotnet test --logger "console;verbosity=detailed"
```

# Clases en C#

## Estructura básica de una clase

```csharp
namespace MiProyecto.Modelos {
    public class MiClase {
        // Propiedad
        public int Id { get; set; }

        // Constructor
        public MiClase(int id) {
            Id = id;
        }

        // Método
        public void MostrarId() {
            Console.WriteLine($"ID: {Id}");
        }
    }
}
```

## Namespace

Si tu clase pertenece a una carpeta `Modelos`, el namespace debe reflejarlo: `MiProyecto.Modelos`.

- Dentro de su namespace, la clase puede ser accedida directamente.
- Fuera de su namespace, debes importar el namespace con `using`.

## Interfaces en C#

Es un contrato que define métodos, propiedades o eventos que una clase debe implementar.

## Estructura básica de una interfaz

```csharp
namespace MiProyecto.Servicios {
    public interface IMiServicio {
        void Procesar(); // Método
        int Calcular(int a, int b); // Método con parámetros
    }
}
```

## Implementación de una interfaz en una clase

```csharp
namespace MiProyecto.Servicios {
    public class MiServicio : IMiServicio {
        public void Procesar() {
            Console.WriteLine("Procesando...");
        }

        public int Calcular(int a, int b) {
            return a + b;
        }
    }
}
```

## Enumeraciones (Enums) en C#

Un `enum` es un conjunto de constantes con nombres. Se usa para representar opciones predefinidas.

```csharp
namespace MiProyecto.Utilidades {
    public enum Estado {
        Pendiente,
        Completado,
        Cancelado
    }
}
```

### Uso de un enum

```csharp
Estado estadoActual = Estado.Pendiente;

if (estadoActual == Estado.Pendiente) {
    Console.WriteLine("El estado está pendiente.");
}
```

## Excepciones en C#

Una excepción representa un error o una condición inesperada en tiempo de ejecución.

- Extiende la clase `Exception`.
- Agrega un mensaje personalizado y (opcionalmente) una excepción interna.

```csharp
using System;

namespace MiProyecto.Excepciones {
    public class MiExcepcionPersonalizada : Exception {
        // Constructor con mensaje
        public MiExcepcionPersonalizada(string mensaje) : base(mensaje) { }

        // Constructor con mensaje y excepción interna
        public MiExcepcionPersonalizada(string mensaje, Exception innerException)
            : base(mensaje, innerException) { }
    }
}
```

```csharp
if (valor < 0) {
    throw new MiExcepcionPersonalizada("El valor no puede ser negativo.");
}
```

### Cómo capturar excepciones

```csharp
try {
    int resultado = 10 / 0; // Esto lanzará una excepción
} catch (DivideByZeroException ex) {
    Console.WriteLine($"Error: {ex.Message}");
} catch (Exception ex) {
    Console.WriteLine($"Excepción no esperada: {ex.Message}");
} finally {
    Console.WriteLine("Finalizando el bloque.");
}
```

### Estructura general del código

```csharp
namespace MiProyecto.Modelos {
    public class MiClase {
        public int Propiedad { get; set; }
    }
}

namespace MiProyecto.Servicios {
    public interface IMiServicio {
        void Metodo();
    }

    public class MiServicio : IMiServicio {
        public void Metodo() {
            Console.WriteLine("Implementación.");
        }
    }
}

namespace MiProyecto.Excepciones {
    public class MiExcepcion : Exception {
        public MiExcepcion(string mensaje) : base(mensaje) { }
    }
}
```

# Getters, Setters, Constructores

## Propiedades Requeridas y Opcionales

```csharp
public class Persona {
    public string? Nombre { get; set; } // Puede ser nulo
    public int Edad { get; set; } = 18; // Valor predeterminado
}
```

## Propiedades Requeridas (C# 11+)

```csharp
public class Persona {
    public required string Nombre { get; set; } // Obligatorio
    public int Edad { get; set; } // Opcional
}
```

```csharp
var persona = new Persona { Nombre = "Juan", Edad = 25 }; // Correcto
var personaInvalida = new Persona { Edad = 25 }; // Error: Nombre es requerido
```

## Constructores en C#

```csharp
public class Persona {
    public string Nombre { get; set; }
    public int Edad { get; set; }

    // Constructor
    public Persona(string nombre, int edad) {
        Nombre = nombre;
        Edad = edad;
    }
}
```

```csharp
var persona = new Persona("Juan", 25);
```

## **Constructores Sobrecargados**

```csharp
public class Persona {
    public string Nombre { get; set; }
    public int Edad { get; set; }

    public Persona() {
        Nombre = "Desconocido";
        Edad = 0;
    }

    public Persona(string nombre, int edad) {
        Nombre = nombre;
        Edad = edad;
    }
}
```

## Simplificación con `record types` (C# 9+)

Los **record types** son una forma concisa de definir clases inmutables o mutables, incluyendo constructores, propiedades y métodos `ToString`, `Equals`, etc.

```csharp
public record Persona(string Nombre, int Edad);
```

```csharp
var persona = new Persona("Juan", 25);
```

## Herramientas y Anotaciones para Simplificar Código

## [DataMember] (Para serialización)

Permite controlar qué propiedades se incluyen en serializaciones.

```csharp
[DataContract]
public class Persona {
    [DataMember]
    public string Nombre { get; set; }
}
```

## [Required] (Validación de datos con DataAnnotations)

Usa `System.ComponentModel.DataAnnotations` para marcar propiedades como requeridas en validaciones.

```csharp
using System.ComponentModel.DataAnnotations;

public class Persona {
    [Required]
    public string Nombre { get; set; }
}
```
