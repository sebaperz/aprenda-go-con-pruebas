# Instalar Go, configurar el entorno para la productividad

Las instrucciones oficiales de instalación de Go están disponibles [aquí](https://golang.org/doc/install).

## Entorno de Go

### Módulos en Go

Go 1.11 introdujo [Módulos](https://go.dev/wiki/Modules). Este enfoque es el modo de compilación por defecto desde Go 1.16, por lo que no se recomienda el uso de `GOPATH`.

El objetivo de los módulos es resolver problemas relacionados con la gestión de dependencias, selección de versiones y compilaciones replicables; también permiten a los usuarios ejecutar código Go fuera del `GOPATH`.

Usar Módulos es bastante sencillo. Seleccione cualquier directorio fuera de `GOPATH` como raíz de su proyecto, y cree un nuevo módulo con el comando `go mod init`.

Se generará un archivo `go.mod`, que contendrá la ruta del módulo, una versión de Go, y sus requisitos de dependencia, que son los otros módulos necesarios para una compilación exitosa.

Si no se especifica `<modulepath>`, `go mod init` intentará adivinar la ruta del módulo a partir de la estructura de directorios. También puede sobreescribirse proporcionando un argumento.

```sh
mkdir my-project
cd my-project
go mod init <modulepath>
```

El archivo `go.mod` podría verse asi:

```
module cmd

go 1.16

```

La documentación incorporada proporciona una visión general de todos los comandos `go mod` disponibles.

```sh
go help mod
go help mod init
```

## Go Linting

Se puede configurar una mejor opción sobre el linter por defecto usando [GolangCI-Lint](https://golangci-lint.run).

Se puede instalar de la siguiente manera:

```sh
brew install golangci-lint
```

## Refactorización y sus herramientas

Un gran énfasis de este libro es la importancia de la refactorización.

Tus herramientas pueden ayudarte a hacer refactorizaciones más grandes con confianza.

Deberías estar lo suficientemente familiarizado con tu editor como para realizar lo siguiente con una simple combinación de teclas:

- **Extraer/Integrar variable**. Tomar valores mágicos y darles un nombre te permite simplificar tu código rápidamente.
- **Extraer método/función**. Es vital poder extraer funciones/métodos de una sección de código.
- **Renombrar**. Deberías ser capaz de renombrar símbolos a través de archivos con confianza.
- **go fmt**. Go tiene un formateador llamado `go fmt`. Tu editor debería ejecutarlo al guardar en cada archivo.
- Ejecutar pruebas. Deberías ser capaz de hacer cualquiera de las cosas anteriores y luego volver a ejecutar rápidamente tus pruebas para asegurarte de que tu refactorización no ha roto nada.

Además, para ayudarte a trabajar con tu código, deberías ser capaz de:

- **Ver la firma de una función**. Nunca deberías tener dudas sobre cómo llamar a una función en Go. Tu IDE debería describir una función en términos de su documentación, sus parámetros y lo que devuelve.
- **Ver definición de función**. Si todavía no está claro lo que hace una función, deberías ser capaz de saltar al código fuente y tratar de averiguarlo por ti mismo.
- **Encontrar los usos de un símbolo**. Comprender el contexto de una función puede ayudarte a tomar decisiones a la hora de refactorizar.

Dominar tus herramientas te ayudará a concentrarte en el código y reducir el cambio de contexto.

## Resumiendo

En este punto, deberías tener Go instalado, un editor disponible y algunas herramientas básicas. Go tiene un gran ecosistema de productos de terceros. Aquí hemos identificado algunos componentes útiles. Para una lista más completa, consulte [https://awesome-go.com](https://awesome-go.com).
