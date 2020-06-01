## Microsoft Q#
##### ([voltar ao início](http://quantico.prins.ninja))<br>
--- 
_Ricardo Prins - Microsoft Student Partner - Ensign College <ricardo@iamprins.com>_<br>
_Rohit Ashiwal - Microsoft Student Partner - IIT Roorkee <rohit.ashiwal265@gmail.com>_

#### Introdução
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

Uma _operação_ em Q# é uma subrotina quântica. Em outras palavras, é uma rotina que se pode invocar e que contém operações quânticas, enquanto uma _função_ em Q# é uma subrotina clássica utilizada dentro de um algoritmo quântico. Especificamente falando, as funções não são capazes de alocar ou _pedir qubits emprestado_, e nem podem invocar operações. É possível, no entanto, passar a elas operações ou qubits para processamento. Funções, portanto, são inteiramente determinísticas no sentido em que ao invocá-las com os mesmos argumentos, sempre se terá o mesmo resultado - o que não ocorre com as operações.

### Tipos definidos pelo usuário

Podemos definir novos tipos usando a palavra-chave `newtype`. Vamos criar um tipo para números complexos:

```cs
newtype Complex = (Double, Double);
```

O comando acima cria um novo tipo com dois itens _anônimos_ do tipo `Double`. Também é possível definir tipos de usuários com itens nomeados (no exemplo abaixo, os nomes _Re_ e _Im_ para os itens)

```cs
newtype Complex = (Re: Double, Im: Double);
```

Desse modo, é possível utilizar o novo tipo criado como se fosse um tipo normal. Ele pode ser usado como argumento em funções, ter instâncias criadas, e daí por diante.

```cs
function ComplexAddition(a: Complex, b: Complex) : Complex {
    return Complex(a::Re + b::Re, a::Im + b::Im);
}
```

## Outras ferramentas úteis

### Comentários

Comentários começam com duas barras, `//`, e continuam até o final da linha. Comentários com três barras `///` são referentes a documentação.

### Namespaces

Um _namespace_ é uma região declarativa que fornece um escopo a identificadores (nomes de tipos, funções, variáveis, etc.) em seu interior.

Cada operação e cada tipo definido por usuário em Q# são definidos dentro de um _namespace_. A linguagem Q#, nesse sentido, segue às mesmas regras de nomenclatura que todas as outras linguagens .NET. No entanto, há uma sensível diferença: Q# não suporta referências relativas a _namespaces_. Em outras palavras, `c.d` não é resolvido em `a.b.c.d` caso `a.b` já tenha sido aberto.

A diretiva `open` é usada para incluir um _namespace_. Também podemos formar _aliases_ usando a palavra-chave `as`.

Exemplo:

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
