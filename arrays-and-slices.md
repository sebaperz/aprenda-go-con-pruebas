# Arrays y slices

**[Puedes encontrar todo el código de este capítulo aquí](https://github.com/quii/learn-go-with-tests/tree/main/arrays)**

Los arrays te permiten almacenar múltiples elementos del mismo tipo en una variable en
un orden en particular.

Cuando tienes arrays, es muy común tener que iterar sobre ellos. Así que
usar [nuestro nuevo conocimiento de `for`](iteration.md) para hacer una función `Suma`. La función `Suma` tomará
tomará un array de números y devolverá el total.

Usemos nuestras habilidades en TDD

## Escribe primero el test

Crea una nueva carpeta para trabajar. Crea un nuevo archivo llamado `sum_test.go` e introduce lo siguiente:

```go
package main

import "testing"

func TestSuma(t *testing.T) {

	numeros := [5]int{1, 2, 3, 4, 5}

	obtenido := Suma(numeros)
	deseado := 15

	if obtenido != deseado {
		t.Errorf("obtenido %d deseado %d dado, %v", obtenido, deseado, numeros)
	}
}
```

Las matrices tienen una _capacidad fija_ que se define al declarar la variable.
Podemos inicializar un array de dos maneras:

\[N\]type{valor1, valor2, ..., valorN} por ejemplo `numeros := [5]int{1, 2, 3, 4, 5}`.
* \[...\]tipo{valor1, valor2, ..., valorN} por ejemplo `numeros := [...]int{1, 2, 3, 4, 5}`

A veces es útil imprimir también las entradas de la función en el mensaje de error.
Aquí, estamos utilizando el marcador de posición `%v` para imprimir el formato "por defecto",  que funciona bien para los arrays.

[Lea más sobre las cadenas de formato](https://golang.org/pkg/fmt/)

## Intenta ejecutar la prueba

Si has inicializado go mod con `go mod init main` te aparecerá un error
`_testmain.go:13:2: cannot import "main"`. Esto se debe a que según la práctica común
el paquete main sólo contendrá integración de otros paquetes y no código testeable por unidades y
por lo tanto Go no le permitirá importar un paquete con el nombre `main`.

Para solucionar esto, puedes renombrar el módulo main en `go.mod` a cualquier otro nombre.

Una vez solucionado el error anterior, si ejecutas `go test` el compilador fallará con el conocido error
`./sum_test.go:10:15: undefined: Sum`. Ahora podemos proceder a escribir el método
a probar.

## Escribir la cantidad mínima de código para que la prueba se ejecute y comprobar la salida de la prueba que fallida

En `sum.go`

```go
package main

func Suma(numeros[5]int) int {
	return 0
}
```


Tu prueba debería fallar ahora con _un claro mensaje de error_.

`sum_test.go:13: obtenido 0 deseado 15 dado, [1 2 3 4 5]`

## Escribe suficiente código para que pase

```go
func Suma(numeros [5]int) int {
	suma := 0
	for i := 0; i < 5; i++ {
		suma += numeros [i]
	}
	return suma
}
```

Para obtener el valor de un array en un índice concreto, basta con utilizar la sintaxis `array[índice]`.
En este caso, estamos utilizando `for` para iterar 5 veces a través del
array y añadir cada elemento a `suma`.

## Refactorizar

Introduzcamos [`range`](https://gobyexample.com/range) para ayudar a limpiar nuestro código

```go
func Suma(numeros [5]int) int {
	suma := 0
	for _, numero := range numeros {
		suma += numero
	}
	return suma
}
```

`range` te permite iterar sobre un array. En cada iteración, ``range`` devuelve dos valores - el índice y el valor.
Estamos optando por ignorar el valor del índice mediante el uso de `_` [identificador en blanco](https://golang.org/doc/effective_go.html#blank).

### Arrays y su tipo

Una propiedad interesante de los arrays es que el tamaño está codificado en su tipo. Si intentas
pasar un `[4]int` a una función que espera `[5]int`, no compilará.
Son tipos diferentes, así que es lo mismo que intentar pasar una `string` a
una función que quiere un `int`.

Puede que pienses que es bastante engorroso que los arrays tengan una longitud fija, ¡y la mayoría de las veces probablemente no los usaras!.

Go tiene _slices_ que no codifican el tamaño de la colección y en su lugar pueden
tener cualquier tamaño.

El siguiente requisito será sumar colecciones de distintos tamaños.

## Escribe primero la prueba

Ahora usaremos el [tipo slice][slice] que nos permite tener colecciones de
cualquier tamaño. La sintaxis es muy parecida a la de los arrays, sólo hay que omitir el tamaño
al declararlos

`mySlice := []int{1,2,3}` en lugar de `myArray := [3]int{1,2,3}`.

```go
func TestSuma(t *testing.T) {

	t.Run("colección de 5 números", func(t *testing.T) {
		numeros := [5]int{1, 2, 3, 4, 5}

		obtenido := Suma(numeros)
		deseado := 15

		if obtenido != deseado {
			t.Errorf("obtenido %d deseado %d dado, %v", obtenido, deseado, numeros)
		}
	})

	t.Run("colección de cualquier tamaño", func(t *testing.T) {
		numeros := []int{1, 2, 3}

		obtenido := Suma(numeros)
		deseado := 6

		if obtenido != deseado {
			t.Errorf("obtenido %d deseado %d dado, %v", obtenido, deseado, numeros)
		}
	})

}
```
## Intenta y ejecuta la prueba

Ésta no compila

`./sum_test.go:22:13: cannot use numbers (type []int) as type [5]int in argument to Suma`

## Escribe la cantidad mínima de código para que el test se ejecute y verifica la salida de la prueba que falla

El problema es que podemos

* Romper la API existente cambiando el argumento de `Suma` para que sea un slice en lugar de un array.
  
Cuando hagamos esto, potencialmente arruinaremos
  el día a alguien porque nuestro _otro_ test ya no compilará.
* Crear una nueva función

En nuestro caso, nadie más está usando nuestra función, así que en lugar de tener dos funciones que mantener, vamos a tener sólo una.

```go
func Suma( numeros []int) int {
	suma := 0
	for _, numero := range numeros {
		suma += numero
	}
	return suma
}
```

Si intentas ejecutar las pruebas seguirán sin compilar, tendrás que cambiar la primera prueba para pasar un slice en lugar de un array.

## Escribir suficiente código para que pase

Resulta que arreglar los problemas del compilador era todo lo que necesitábamos hacer aquí y ¡las pruebas pasan!

## Refactorizar

Ya hemos refactorizado `Suma` - todo lo que hicimos fue reemplazar arrays por slices, así que no se requieren cambios extra.
Recuerda que no debemos descuidar nuestro código de pruebas en la etapa de refactorización - podemos mejorar aún más nuestras pruebas de `Suma`.

```go
func TestSuma(t *testing.T) {

	t.Run("colección de 5 números", func(t *testing.T) {
		numeros := []int{1, 2, 3, 4, 5}

		obtenido := Suma(numeros)
		deseado := 15

		if obtenido != deseado {
			t.Errorf("obtenido %d deseado %d dado, %v", obtenido, deseado, numeros)
		}
	})

	t.Run("colección de cualquier tamaño", func(t *testing.T) {
		numeros := []int{1, 2, 3}

		obtenido := Suma(numeros)
		deseado := 6

		if obtenido != deseado {
			t.Errorf("obtenido %d deseado %d dado, %v", obtenido, deseado, numeros)
		}
	})

}
```

Es importante cuestionarse el valor de las pruebas. El objetivo no debería ser
tener tantas pruebas como sea posible, sino tener tanta _confianza_ como
como sea posible en su código base. Tener demasiadas pruebas puede convertirse en un verdadero problema
y sólo añade más sobrecarga en el mantenimiento. **Cada prueba tiene un coste**.

En nuestro caso, puedes ver que tener dos pruebas para esta función es redundante.
Si funciona para una rebanada de un tamaño es muy probable que funcione para una rebanada de
cualquier tamaño (dentro de lo razonable).

El conjunto de herramientas de pruebas de Go incluye una [herramienta de cobertura](https://blog.golang.org/cover).
Aunque el objetivo final no debe ser una cobertura del 100%, la herramienta de cobertura puede ayudar a
a identificar áreas de tu código no cubiertas por las pruebas. Si has sido estricto con TDD
es bastante probable que tengas una cobertura cercana al 100% de todos modos.

Pruebe a ejecutar

`go test -cover`

Debería ver

```bash
PASS
coverage: 100.0% of statements
```
Ahora elimina una de las pruebas y comprueba la cobertura de nuevo.

Ahora que estamos satisfechos de tener una función bien probada, deberías hacer un commit de tu excelente trabajo antes de enfrentarte al siguiente reto.

Necesitamos una nueva función llamada `SumaTodo` que tomará un número variable de
slices, devolviendo un nuevo slice que contenga los totales de cada slice pasado.

Por ejemplo

`SumaTodo([]int{1,2}, []int{0,9})` devolvería `[]int{3, 9}`.

o

`SumaTodo([]int{1,1,1})` devolvería `[]int{3}`.

## Escribe primero la prueba

```go
func TestSumaTodo(t *testing.T) {

	obtenido := SumaTodo([]int{1, 2}, []int{0, 9})
	deseado := []int{3, 9}

	if obtenido != deseado {
		t.Errorf("obtenido %v deseado %v", obtenido, deseado)
	}
}
```

## Intenta ejecutar la prueba

`./sum_test.go:23:9: undefined: SumaTodo`

## Escribe la cantidad mínima de código para que la prueba se ejecute y verifica la salida de la prueba que falla.

Necesitamos definir `SumaTodo` de acuerdo con lo que quiere nuestro test.

Go te permite escribir [_variadic functions_](https://gobyexample.com/variadic-functions) que pueden tomar un número variable de argumentos.

```go
func SumaTodo(numerosASumar ...[]int) []int {
	return nil
}
```
Esto es válido, ¡pero nuestras pruebas siguen sin compilar!

`./sum_test.go:26:9: invalid operation: obtenido != deseado (slice can only be compared to nil)`

Go no permite usar operadores de igualdad con slices. _Podrías_ escribir
una función para iterar sobre cada elemento `obtenido` y `deseado` y comprobar sus valores
pero por comodidad, podemos usar [`reflect.DeepEqual`][deepEqual] que es
útil para ver si _cualquiera_ dos variables son iguales.

```go
func TestSumaTodo(t *testing.T) {

	obtenido := SumaTodo([]int{1, 2}, []int{0, 9})
	deseado := []int{3, 9}

	if !reflect.DeepEqual(obtenido, deseado) {
		t.Errorf("obtenido %v deseado %v", obtenido, deseado)
	}
}

```
\(asegúrate de `import reflect` en la parte superior de tu archivo para tener acceso a `DeepEqual`)

Es importante tener en cuenta que `reflect.DeepEqual` no es "type safe" - el código
compilará aunque hayas hecho una tontería. Para ver esto en acción
cambia temporalmente la prueba a:

```go
func TestSumaTodo(t *testing.T) {

	obtenido := SumaTodo([]int{1, 2}, []int{0, 9})
	deseado := "bob"

	if !reflect.DeepEqual(obtenido, deseado) {
		t.Errorf("obtenido %v deseado %v", obtenido, deseado)
	}
}
```

Lo que hemos hecho aquí es tratar de comparar un `slice` con una `string`. Esto no tiene
no tiene sentido, ¡pero la prueba compila! Así que mientras que el uso de `reflect.DeepEqual`
es una forma cómoda de comparar slices \( entre otras cosas\) hay que tener cuidado
al usarlo.

(Desde Go 1.21, está disponible el paquete estándar [slices](https://pkg.go.dev/slices#pkg-overview), que tiene la función [slices.Equal](https://pkg.go.dev/slices#Equal) para hacer una simple comparación superficial en slices, donde no necesitas preocuparte por los tipos como en el caso anterior. Tenga en cuenta que esta función espera que los elementos sean [comparable](https://pkg.go.dev/builtin#comparable). Por tanto, no puede aplicarse a slices con elementos no comparables, como slices 2D).  

Vuelve a cambiar la prueba y ejecútala. Deberías tener una salida de prueba como la siguiente

`sum_test.go:30: obtenido [] deseado [3 9]`

## Escribe suficiente código para que pase

Lo que tenemos que hacer es iterar sobre los varargs, calcular la suma usando nuestra función
 `Suma` existente, y luego añadirla al slice que devolveremos

```go
func SumaTodo(numerosASumar ...[]int) []int {
	longitudDeNumeros := len(numerosASumar)
	sumas := make([]int, longitudDeNumeros)

	for i, numeros := range numerosASumar {
		sumas[i] = Suma(numeros)
	}

	return sumas
}
```

Lots of new things to learn!

There's a new way to create a slice. `make` allows you to create a slice with
a starting capacity of the `len` of the `numbersToSum` we need to work through. The length of a slice is the number of elements it holds `len(mySlice)`, while the capacity is the number of elements it can hold in the underlying array `cap(mySlice)`, e.g., `make([]int, 0, 5)` creates a slice with length 0 and capacity 5.

You can index slices like arrays with `mySlice[N]` to get the value out or
assign it a new value with `=`

The tests should now pass.

## Refactor

As mentioned, slices have a capacity. If you have a slice with a capacity of
2 and try to do `mySlice[10] = 1` you will get a _runtime_ error.

However, you can use the `append` function which takes a slice and a new value,
then returns a new slice with all the items in it.

```go
func SumAll(numbersToSum ...[]int) []int {
	var sums []int
	for _, numbers := range numbersToSum {
		sums = append(sums, Sum(numbers))
	}

	return sums
}
```

In this implementation, we are worrying less about capacity. We start with an
empty slice `sums` and append to it the result of `Sum` as we work through the varargs.

Our next requirement is to change `SumAll` to `SumAllTails`, where it will
calculate the totals of the "tails" of each slice. The tail of a collection is
all items in the collection except the first one \(the "head"\).

## Write the test first

```go
func TestSumAllTails(t *testing.T) {
	got := SumAllTails([]int{1, 2}, []int{0, 9})
	want := []int{2, 9}

	if !reflect.DeepEqual(got, want) {
		t.Errorf("got %v want %v", got, want)
	}
}
```

## Try and run the test

`./sum_test.go:26:9: undefined: SumAllTails`

## Write the minimal amount of code for the test to run and check the failing test output

Rename the function to `SumAllTails` and re-run the test

`sum_test.go:30: got [3 9] want [2 9]`

## Write enough code to make it pass

```go
func SumAllTails(numbersToSum ...[]int) []int {
	var sums []int
	for _, numbers := range numbersToSum {
		tail := numbers[1:]
		sums = append(sums, Sum(tail))
	}

	return sums
}
```

Slices can be sliced! The syntax is `slice[low:high]`. If you omit the value on
one of the sides of the `:` it captures everything to that side of it. In our
case, we are saying "take from 1 to the end" with `numbers[1:]`. You may wish to
spend some time writing other tests around slices and experiment with the
slice operator to get more familiar with it.

## Refactor

Not a lot to refactor this time.

What do you think would happen if you passed in an empty slice into our
function? What is the "tail" of an empty slice? What happens when you tell Go to
capture all elements from `myEmptySlice[1:]`?

## Write the test first

```go
func TestSumAllTails(t *testing.T) {

	t.Run("make the sums of some slices", func(t *testing.T) {
		got := SumAllTails([]int{1, 2}, []int{0, 9})
		want := []int{2, 9}

		if !reflect.DeepEqual(got, want) {
			t.Errorf("got %v want %v", got, want)
		}
	})

	t.Run("safely sum empty slices", func(t *testing.T) {
		got := SumAllTails([]int{}, []int{3, 4, 5})
		want := []int{0, 9}

		if !reflect.DeepEqual(got, want) {
			t.Errorf("got %v want %v", got, want)
		}
	})

}
```

## Try and run the test

```text
panic: runtime error: slice bounds out of range [recovered]
    panic: runtime error: slice bounds out of range
```

Oh no! It's important to note that while the test _has compiled_, it _has a runtime error_.  

Compile time errors are our friend because they help us write software that works,  
runtime errors are our enemies because they affect our users.

## Write enough code to make it pass

```go
func SumAllTails(numbersToSum ...[]int) []int {
	var sums []int
	for _, numbers := range numbersToSum {
		if len(numbers) == 0 {
			sums = append(sums, 0)
		} else {
			tail := numbers[1:]
			sums = append(sums, Sum(tail))
		}
	}

	return sums
}
```

## Refactor

Our tests have some repeated code around the assertions again, so let's extract those into a function.

```go
func TestSumAllTails(t *testing.T) {

	checkSums := func(t testing.TB, got, want []int) {
		t.Helper()
		if !reflect.DeepEqual(got, want) {
			t.Errorf("got %v want %v", got, want)
		}
	}

	t.Run("make the sums of tails of", func(t *testing.T) {
		got := SumAllTails([]int{1, 2}, []int{0, 9})
		want := []int{2, 9}
		checkSums(t, got, want)
	})

	t.Run("safely sum empty slices", func(t *testing.T) {
		got := SumAllTails([]int{}, []int{3, 4, 5})
		want := []int{0, 9}
		checkSums(t, got, want)
	})

}
```

We could've created a new function `checkSums` like we normally do, but in this case, we're showing a new technique, assigning a function to a variable. It might look strange but, it's no different to assigning a variable to a `string`, or an `int`, functions in effect are values too. 

It's not shown here, but this technique can be useful when you want to bind a function to other local variables in "scope" (e.g between some `{}`). It also allows you to reduce the surface area of your API. 

By defining this function inside the test, it cannot be used by other functions in this package. Hiding variables and functions that don't need to be exported is an important design consideration.

A handy side-effect of this is this adds a little type-safety to our code. If
a developer mistakenly adds a new test with `checkSums(t, got, "dave")` the compiler
will stop them in their tracks.

```bash
$ go test
./sum_test.go:52:21: cannot use "dave" (type string) as type []int in argument to checkSums
```

## Wrapping up

We have covered

* Arrays
* Slices
  * The various ways to make them
  * How they have a _fixed_ capacity but you can create new slices from old ones
    using `append`
  * How to slice, slices!
* `len` to get the length of an array or slice
* Test coverage tool
* `reflect.DeepEqual` and why it's useful but can reduce the type-safety of your code

We've used slices and arrays with integers but they work with any other type
too, including arrays/slices themselves. So you can declare a variable of
`[][]string` if you need to.

[Check out the Go blog post on slices][blog-slice] for an in-depth look into
slices. Try writing more tests to solidify what you learn from reading it.

Another handy way to experiment with Go other than writing tests is the Go
playground. You can try most things out and you can easily share your code if
you need to ask questions. [I have made a go playground with a slice in it for you to experiment with.](https://play.golang.org/p/ICCWcRGIO68)

[Here is an example](https://play.golang.org/p/bTrRmYfNYCp) of slicing an array
and how changing the slice affects the original array; but a "copy" of the slice
will not affect the original array.
[Another example](https://play.golang.org/p/Poth8JS28sc) of why it's a good idea
to make a copy of a slice after slicing a very large slice.

[for]: ../iteration.md#
[blog-slice]: https://blog.golang.org/go-slices-usage-and-internals
[deepEqual]: https://golang.org/pkg/reflect/#DeepEqual
[slice]: https://golang.org/doc/effective_go.html#slices
