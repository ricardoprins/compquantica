## Microsoft Q#

#### Introdução
---
_Colaborações adicionais: Rohit Ashiwal - Microsoft Student Partner - rohit.ashiwal265@gmail.com_

Um modelo natural à computação quântica trata o computador quântico como um co-processador, similar aos utilizados em GPUs, FPGAs e outros processadores adjuntos. O controle lógico primário executa código clássico em um computador clássico "host". Quando necessário e apropriado, o <i>host</i> invoca uma subrotina que é executada no processador adjunto. Quando a subrotina termina, o programa _host_ então acessa os resultados correspondentes.

A linguagem Q# é uma linguagem de programação de domínio específico, utilizada para expressar algoritmos quânticos. Ela é usada para escrever subrotinas que são executadas em um processador quântico adjunto, sob o controle de um computador e programas clássicos (o host mencionado acima). Até que processadores quânticos sejam disponíveis em larga escala, as subrotinas em Q# podem ser executadas em um simulador.

## Tipos de variáveis em Q#

### Tipos primitivos

A linguagem fornece vários _tipos primitivos_, a partir de quais outros tipos podem ser construídos:

- `Int` represenando inteiros assinalados
- `BigInt` inteiros assinalados de tamanho arbitrário
- `Double` representando números de ponto flutuante com precisão dupla
- `Bool` para tipos booleanos
- `Qubit` para representar um bit quântico, ou qubit
- `Pauli` para representar um dos quatro operadores Pauli de um qubit
- `Result` para conter resultados de medida
- `Range` representa uma sequência de inteiros
- `String` representa uma sequência de caracteres Unicode
- `Unit` um tipo que permite somente um valor `()`

Exemplos:

```cs
let one = 1;
let pi = 3.14;
```

Por definição, as variáveis em Q# são imutáveis, mas podemos declará-las como mutáveis por meio da palavra-chave `mutable`.

Exemplo:

```cs
mutable x = 1;
set x = 2;
```

### Tipos vetoriais

Um vetor é uma estrutura de dados que pode armazenar uma coleção de elementos de um tamanho fixo, que sejam do mesmo tipo. Em Q#, podemos criar um vetor por meio do emprego de colchetes ao redor dos elementos desse vetor.

Exemplo:

```cs
let arr = [1, 2, 3, 4];
let even = arr[1..2..4];
```

> ⚠️ Importante \
\
Os elementos de um vetor não podem ser alterados após a criação do mesmo. Para realizar operações dessa natureza, é necessário recorrer a outros artifícios que não serão tratados neste momento.

Uma outra maneira de se declarar um vetor é por meio da palavra-chave `new`.

Exemplo:

```cs
let zero = new Int[13];
let qubits = new Qubit[10];
```

### Tuplas

Variáveis tuplas armazenam diferentes tipos dentro de si. No entanto, esse aninhamento é finito, uma vez que tuplas não podem conter a si mesmas.

O tipo _unit_ é uma tupla que contém um valor _vazio_.

As instâncias de uma tupla são `imutáveis`. O Q# ainda não fornece um mecanismo para alteração dos valores em uma tupla após ela ter sido criada.

Exemplo:

```cs
let tup = (3, false);
```

### Operações e Funções

A Q# operation is a quantum subroutine. That is, it is a callable routine that contains quantum operations whereas a Q# function is a classical subroutine used within a quantum algorithm. Specifically, functions may not allocate or borrow qubits, not may they call operations. It is possible, however, to pass them operations or qubits for processing. Functions are thus entirely deterministic in the sense that calling them with the same arguments will always produce same result.

Together, operations and functions are called _callables_.

### Tipos definidos pelo usuário

We can define new types using the `newtype` statement. Let's create a complex datatype.

```cs
newtype Complex = (Double, Double);
```

This statement creates a new type with two _anonymous_ items of type `Double`. Aside from anonymous items, user define types also support named items as of Q# version 0.7 or higher.

```cs
newtype Complex = (Re: Double, Im: Double);
```

And now, we can use this newly created type just like any other type. We can pass it to a function, create different instances of it, etc.

```cs
function ComplexAddition(a: Complex, b: Complex) : Complex {
    return Complex(a::Re + b::Re, a::Im + b::Im);
}
```

## Statement and Other Constructs

### Comments

Comments begin with two forward slashes, `//`, and continue until the end of line. A comment may appear anywhere in a Q# source file.

### Namespaces

A namespace is a declarative region that provides a scope to the identifiers (the names of types, functions, variables, etc) inside it.

Every Q# operation, and user-defined type is defined within a namespace. Q# follows the sames rules for naming as other .NET languages. However, Q# does not support relative references to namespaces. Implies, `c.d` will not resolve to `a.b.c.d` if `a.b` is already opened.

`open` directive is used to include a namespace. We can also form aliases using `as` keyword

Example:

```cs
namespace NS {
    open Microsoft.Quantum.Intrinsic;
    open Microsoft.Quantum.Math as Math;
}
```

### Control Flow

#### For Loop

The `for` statement supports iteration over an integer range or over an array. The binding of the declared symbol(s) bound to each value in the range or array. In particular, it is possible to destruct e.g. array items for an iteration over an array upon assignment to the loop variable(s).

Example:

```cs
for (qubit in qubits) {  // qubits: Qubit[]
    H(qubit);
}

for (idx in 0 .. Length(qubits) - 1) {
    set results w/= idx <- (idx, M(qubits[idx]));  // update-and-reassign statement
}
```

The loop variable is bound at each enterance to the loop body, and unbound at the end of the body. In particular, the loop variable is not bound after the for loop is completed.

#### Repeat-Until-Success Loop

The `repeat` statment supports the quantum "repeat until success" pattern. The block is repeated `until` a certained condition _is_ met.

Example:

```cs
using (qubit = Qubit()) {
    repeat {
        H(qubit);
        let result = M(qubit);
        Message($"{result}");
    } until (result == Zero);
}
```

#### While Loop

Repeat-until-success patterns have a very quantum-specific connotation. They are widely used in particular classes of quantum algorithms -- hence the dedicated language construct in Q#. However, loops that break based on a condition and whose execution length is thus unknown at compile time need to be handled with particular care in a quantum runtime. Their use within functions on the other hand is unproblematic, since these only contain code that will be executed on conventional (non-quantum) hardware.

> ℹ️ Note\
\
Q# supports to use of while loops within functions only.

```cs
mutable (item, idx) = (-1, 0);

while (idx < Length(arr) and item < 0) {
    set item = arr[idx];
    set item += 1;
}
```
    

### Conditional Statement

Q# supports `if` statements for conditional execution. Also, nested conditionals are allowed.

```cs
if (result == One) {
    X(target);
}

if (i == 1) {
    X(target);
} elif (i == 2) {
    Y(target);
} else {
    Z(target);
}
```

### Qubit Management

> ℹ️ Note\
\
None of these statements are allowed within the body of a function. They are only valid within operations.

#### Clean Qubits

The `using` statement is used to acquire new qubits for use during a statement block. The qubits are guaranteed to be initialized to the computational `Zero` state. The qubits should be in the computational `Zero` state at the end of the statement block; simulators are encouraged to enforce this.

> ⚠️ Warning\
\
Target machines expect that qubits are in the |0Γƒ⌐ state immediately before delocation, so that they can be reused and offered to other `using` blocks for allocation. Whenever possible, use unitary operations to return any allocated qubits to |0Γƒ⌐. If need be, `Reset` operation can be used to ensure that the measured qubit is returned to |0Γƒ⌐. Such a measurement will destroy any entanglement with the remaining qubits and can thus impact the computation.

Example:

```cs
using (register = Qubit[8]) {
    // Do stuff ...
    ResetAll(register);
}
```
