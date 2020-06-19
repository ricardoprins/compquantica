# Microsoft Q#
##### ([página inicial](http://quantico.prins.ninja))<br>
--- 
_Ricardo Prins - Microsoft Student Partner - Ensign College <ricardo@iamprins.com>_<br>
_Rohit Ashiwal - Microsoft Student Partner - IIT Roorkee <rohit.ashiwal265@gmail.com>_

## Tópicos
 * [Introdução](#introdução)
 * [Tipos de variáveis em Q#](#tipos-de-variáveis-em-q)
    - [Tipos primitivos](#tipos-primitivos)
    - [Tipos vetoriais](#tipos-vetoriais)
    - [Tuplas](#tuplas)
    - [Tipos definidos pelo usuário](#tipos-definidos-pelo-usuário)
* [Operações e funções](#operações-e-funções)
* [Outras ferramentas úteis](#outras-ferramentas-úteis)
    - [Comentários](#comentários)
    - [Namespaces](#namespaces)
* [Estruturas de controle](#estruturas-de-controle)
    - [*for*](#for)
    - [*repeat-until-success*](#repeat-until-success)
    - [*while*](#while)
    - [*if-else*](#if-else)
* [Gestão de Qubits](#gestão-de-qubits)
    - [Limpar Qubits](#limpar-qubits)
    
## Introdução
##### ([voltar para o topo](#tópicos))<br>
Um modelo natural à computação quântica trata o computador quântico como um co-processador, similar aos utilizados em GPUs, FPGAs e outros processadores adjuntos. O controle lógico primário executa código clássico em um computador clássico "host". Quando necessário e apropriado, o <i>host</i> invoca uma subrotina que é executada no processador adjunto. Quando a subrotina termina, o programa _host_ então acessa os resultados correspondentes.

A linguagem Q# é uma linguagem de programação de domínio específico, utilizada para expressar algoritmos quânticos. Ela é usada para escrever subrotinas que são executadas em um processador quântico adjunto, sob o controle de um computador e programas clássicos (o host mencionado acima). Até que processadores quânticos sejam disponíveis em larga escala, as subrotinas em Q# podem ser executadas em um simulador.

## Tipos de variáveis em Q#

### Tipos primitivos
##### ([voltar para o topo](#tópicos))<br>
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
##### ([voltar para o topo](#tópicos))<br>
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
##### ([voltar para o topo](#tópicos))<br>
Variáveis tuplas armazenam diferentes tipos dentro de si. No entanto, esse aninhamento é finito, uma vez que tuplas não podem conter a si mesmas.

O tipo _unit_ é uma tupla que contém um valor _vazio_.

As instâncias de uma tupla são `imutáveis`. O Q# ainda não fornece um mecanismo para alteração dos valores em uma tupla após ela ter sido criada.

Exemplo:

```cs
let tup = (3, false);
```

### Tipos definidos pelo usuário
##### ([voltar para o topo](#tópicos))<br>
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

## Operações e funções
##### ([voltar para o topo](#tópicos))<br>
Uma _operação_ em Q# é uma subrotina quântica. Em outras palavras, é uma rotina que se pode invocar e que contém operações quânticas, enquanto uma _função_ em Q# é uma subrotina clássica utilizada dentro de um algoritmo quântico. Especificamente falando, as funções não são capazes de alocar ou _pedir qubits emprestado_, e nem podem invocar operações. É possível, no entanto, passar a elas operações ou qubits para processamento. Funções, portanto, são inteiramente determinísticas no sentido em que ao invocá-las com os mesmos argumentos, sempre se terá o mesmo resultado - o que não ocorre com as operações.

## Outras ferramentas úteis
##### ([voltar para o topo](#tópicos))<br>
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

## Estruturas de controle

### _for_
##### ([voltar para o topo](#tópicos))<br>
A estrutura `for` dá suporte a iterações em um intervalo de inteiros ou membros de um vetor. A estrutura básica é similar a de outras linguagens de programação, e não cabe para os propósitos deste tutorial explicar esse funcionamento básico. Exemplos seguem abaixo:

```cs
for (qubit in qubits) {  // qubits: Qubit[]
    H(qubit);
}

for (idx in 0 .. Length(qubits) - 1) {
    set results w/= idx <- (idx, M(qubits[idx]));  // update-and-reassign statement
}
```

### _repeat-until-success_
##### ([voltar para o topo](#tópicos))<br>
A estrutura `repeat` dá suporte ao padrão quântico "repita até o sucesso (_repeat until success_). O bloco de instruções é iterado `until` (até que) uma certa condição seja satisfeita.

Exemplo:

```cs
using (qubit = Qubit()) {
    repeat {
        H(qubit);
        let result = M(qubit);
        Message($"{result}");
    } until (result == Zero);
}
```

### _while_
##### ([voltar para o topo](#tópicos))<br>
As estruturas de controle no formato _repeat-until-success_ possuem uma conotação bastante relacionada a conceitos quânticos. Elas são amplamente usadas em alguns tipos de algoritmos quânticos -- por essa razão, a elas foi dada uma atenção especial durante o desenvolvimento da linguagem Q#. 

No entanto, estruturas de repetição que são interrompidas com base em uma condição, e cujo tempo de execução seja desconhecido durante a compilação (_compile time_) requerem uma abordagem bastante cuidadosa em um tempo de execução quântico. No entanto, ao serem usadas em funções não há problema, visto que elas terão código apenas executável em hardware convencional (não-quântico).

> ℹ️ Nota\
\
A linguagem Q# apenas permite o uso de _while_ em funções, e não em operações! (ver [Operações e funções](#operações-e-funções) para entender a diferença)

```cs
mutable (item, idx) = (-1, 0);

while (idx < Length(arr) and item < 0) {
    set item = arr[idx];
    set item += 1;
}
``` 

### _if-else_
##### ([voltar para o topo](#tópicos))<br>
A linguagem Q# permite o uso de estruturas `if-else` para execução condicional. Também é permitido aninhar essas estruturas.

Exemplos:

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

## Gestão de _Qubits_
##### ([voltar para o topo](#tópicos))<br>
> ℹ️ Nota\
\
O uso desses comandos abaixo só é permitido em operações, e não em funções! (ver [Operações e funções](#operações-e-funções) para entender a diferença)

### Limpar _Qubits_

A palavra-chave `using` é utilizada para adquirir novos _qubits_ para uso em um bloco de instruções. Os _qubits_ são inicializados garantidamente no estado computacional `Zero`. Os _qubits_ devem estar no estado computacional `Zero` no fim do bloco de instruções; os simuladores são encorajados a garantir que isso aconteça.

> ⚠️ Importante\
\
É esperado que os _qubits_ estejam no estado |0Γƒ⌐ imediatamente após sua desalocação, de modo que eles possam ser reutilizados (e realocados) a outros blocos de instrução através do uso da palavra-chave `using`. Assim, sempre que possível, use operações unitárias para retornar quaisquer _qubits_ alocados ao estado |0Γƒ⌐. Caso seja necessário, o comando `Reset` pode ser utilizado para garantir que o _qubit_ medido em questão seja retornado ao estado |0Γƒ⌐. Tal medição necessariamente destruirá qualquer enlace com os _qubits_ remanescentes, podendo assim impactar na computação (cálculos e medidas).

Exemplo:

```cs
using (register = Qubit[8]) {
    // Do stuff ...
    ResetAll(register);
}
```
