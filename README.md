# ECMAScript 6

## Introdução
ECMAScript 6, também conhecido como ECMAScript 2015, é a última versão do padrão ECMAScript. ES6 é 
uma atualização significativa para a linguagem e é a primeira atualização desde que ES5 se tornou um 
padrão em 2009. A implementação dessas funcionalidades nas principais engines de JavaScript 
[já está em andamento](http://kangax.github.io/es5-compat-table/es6/).

Veja o [padrão ES6](http://www.ecma-international.org/ecma-262/6.0/) para a especificação completa 
da linguagem ECMAScript 6.

ES6 inclui as seguintes novas funcionalidades:
- [Setas (Arrows)](#arrows)
- [Classes](#classes)
- [Objetos Literais Aprimorados (Enhanced Object Literals)](#enhanced-object-literals)
- [Template Strings](#template-strings)
- [Desestruturação (Destructuring)](#destructuring)
- [default + rest + spread](#default--rest--spread)
- [let + const](#let--const)
- [iterators + for..of](#iterators--forof)
- [generators](#generators)
- [unicode](#unicode)
- [modules](#modules)
- [module loaders](#module-loaders)
- [map + set + weakmap + weakset](#map--set--weakmap--weakset)
- [proxies](#proxies)
- [symbols](#symbols)
- [subclassable built-ins](#subclassable-built-ins)
- [promises](#promises)
- [math + number + string + array + object APIs](#math--number--string--array--object-apis)
- [binary and octal literals](#binary-and-octal-literals)
- [reflect api](#reflect-api)
- [tail calls](#tail-calls)

## Funcionalidades ECMAScript

### Setas (Arrows)
Setas são a maneira simplificada para funções usando a sintaxe `=>`. Elas são sintaticamente
similares ao recurso relacionado em C#, Java 8 e CofeeScript. Suportam tanto corpos de blocos 
statement, quanto os de expressões que retornam o valor da expressão. Diferentemente de funções, 
Setas compartilham o mesmo léxico `this`, tanto como o código que o envolve (surrounding code).

```JavaScript
// Expressão
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);
var pairs = evens.map(v => ({even: v, odd: v + 1}));

// Statement
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// Léxico "this"
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

### Classes
Classes ES6 são simplesmente um "docinho" para o padrão OO baseado em protótipos. Ter uma forma 
declarativa, conveniente e simples de padrões de classes faz delas mais fáceis de serem usadas e 
encoraja a interoperabilidade. Classes suportam herança baseada em protótipos, chamadas super, 
métodos instanciados e estáticos e construtores.

```JavaScript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    this.idMatrix = SkinnedMesh.defaultMatrix();
    this.bones = [];
    this.boneMatrices = [];
    //...
  }
  update(camera) {
    //...
    super.update();
  }
  get boneCount() {
    return this.bones.length;
  }
  set matrixType(matrixType) {
    this.idMatrix = SkinnedMesh[matrixType]();
  }
  static defaultMatrix() {
    return new THREE.Matrix4();
  }
}
```

### Objetos Literais Aprimorados (Enhanced Object Literals)
Object Literals são estendidos para dar suporte à especificação de prototype na construção, 
simplificações para atribuições `foo: foo`, definição de métodos, chamadas super e computar propriedades 
de nome com expressões. Juntos, estes fazem Objetos Literais e declarações de classes mais próximos e 
o benefício do design baseado em objetos com as mesmas conveniências.

```JavaScript
var obj = {
    // __proto__
    __proto__: theProtoObj,
    // Simplificação para ‘handler: handler’
    handler,
    // Métodos
    toString() {
     // Chamadas super
     return "d " + super.toString();
    },
    // Nomes de propriedades computados (dinamicos)
    [ 'prop_' + (() => 42)() ]: 42
};
```

### Template Strings
Template Strings proveem "açúcar sintático" para montar strings. São similares a interpolação de 
strings em Perl, Python e outros. Opcionalmente, uma tag pode ser adicionada para permitir a 
construção da string ser customizada, evitando ataques de injeção ou construindo estruturas de dados 
de alto nível a partir de conteúdos de strings.

```JavaScript
// Criação básica de string literal
`In JavaScript '\n' is a line-feed.`

// Strings multilinhas
`In JavaScript this is
 not legal.`

// Interpolation de string
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// Montar um prefixo de requisição HTTP pode ser usado para interpretar substutuições e construção
POST`http://foo.org/bar?a=${a}&b=${b}
     Content-Type: application/json
     X-Credentials: ${credentials}
     { "foo": ${foo},
       "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

### Desestruturação (Destructuring)
Desestruturação permite binding usando matching de padrões com suporte para matching de arrays e 
objetos. Desestruturação é "fail-soft", similar ao lookup padrão de objeto `foo["bar"]`, produzindo 
valores `undefined` quando não encontrado.

```JavaScript
// Matching de array
var [a, , b] = [1,2,3];

// Matching de objeto
var { op: a, lhs: { op: b }, rhs: c }
       = getASTNode()

// shorthand para matching de objeto faz
// bind para `op`, `lhs` e `rhs` no escopo
var {op, lhs, rhs} = getASTNode()

// Pode ser usado na posição do parametro
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// Desestruturação fail-soft
var [a] = [];
a === undefined;

// Desestruturação fail-soft com defaults
var [a = 1] = [];
a === 1;
```

### Default + Rest + Spread
Valores padrão de parâmetros avaliados pelo "chamador". Transforma um array em argumentos 
consecutivos em uma chamada de função. Vincula (bind) parâmetros para array. Rest troca 
a necessidade de argumentos e endereça casos de espaços comuns mais diretamente.

```JavaScript
function f(x, y=12) {
  // y é 12 se não for passado (ou passado como undefined)
  return x + y;
}
f(3) == 15
```
```JavaScript
function f(x, ...y) {
  // y é um Array
  return x * y.length;
}
f(3, "hello", true) == 6
```
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// Passa cada elementos do array como argumento
f(...[1,2,3]) == 6
```

### Let + Const
`let` é o novo `var`. `const` é de atribuição única (single-assignment). Restrições de Estático 
previnem o uso antes da atribuição.

```JavaScript
function f() {
  {
    let x;
    {
      // OK, escopo de bloco
      const x = "sneaky";
      // error, const
      x = "foo";
    }
    // error: já declarado no bloco
    let x = "inner";
  }
}
```

### Iterators + For..Of
Objetos iteradores permitem iteradores personalizações como CLR IEnumerable ou Java Iterable. 
Generalização de `for..in` para iteração customizada com `for..of`. Não é preciso array, o que 
permite padrões lazy de design como LINQ.

```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // trunca a sequência em 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

A iteração é baseada nessas interfaces (usando a sintaxe de tipo de 
[TypeScript](http://typescriptlang.org) somente para exemplificar):
```TypeScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

### Geradores (Generators)
Generators simplificam iterações usando `function*` and `yield`.  Uma função declarada como 
`function*` retorna uma instância de Generator.  Generators são subtipos de iteradores que incluem 
`next` e `throw`. Estes valores permitem voltar no gerador, então `yield` é uma forma de expressão 
que retorna um valor -- ou o lança (throws).

Nota: Também pode ser usado para habilitar programação assíncrona "‘await’-like", tal como a 
proposta de `await` em ES7.

```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

A iteração é baseada nessas interfaces (usando a sintaxe de tipo de 
[TypeScript](http://typescriptlang.org) somente para exemplificar):

```TypeScript
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```

### Unicode
Foram feitas adições para suporte total a Unicode, incluindo a nova forma literal de Unicode em 
string e o novo modo de expressões regulares `u` para lidar com code points, tal como novas APIs 
para processar strings no nível em código 21bit. Essas adições permitem o desenvolvimento de 
aplicativos globais em JavaScript.

```JavaScript
// mesmo que em ES5.1
"𠮷".length == 2

// novo ‘u’ de expressões regulares
"𠮷".match(/./u)[0].length == 2

// nova maneira
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// novo String ops
"𠮷".codePointAt(0) == 0x20BB7

// for-of itera pontos de código (code points)
for(var c of "𠮷") {
  console.log(c);
}
```

### Módulos (Modules)
Suporte a Módulos em nível de linguagem para definição de componentes. Codifica padrões de loaders 
de módulos populares em JavaScript (AMD, CommonJS). Comportamente em runtime definido por um loader 
que pode ser definido. Modelo assíncrono implícito -- nenhum código executa até os módulos 
requeridos ficarem disponíveis e processados.

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
import * as math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```

Algumas features adicionais incluem `export default` e `export *`:

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.log(x);
}
```
```JavaScript
// app.js
import ln, {pi, e} from "lib/mathplusplus";
alert("2π = " + ln(e)*pi*2);
```

### Carregadores de Módulos (Module Loaders)
Module loaders suporta:
- Carregamento Dinâmico (Dynamic loading)
- Isolamento de Estado (State isolation)
- Isolamento Global de Namespace (Global namespace isolation)
- Hooks de Compilação (Compilation hooks)
- Virtualização Aninhada (Nested virtualization)

O loader padrão de módulo pode ser configurado e novos loaders podem ser construídos para processar 
e carregar códigos em contextos isolados ou restringidos.

```JavaScript
// Carregamento Dinâmico -- ‘System’ é o loader padrão
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// Cria sandbox de execução
var loader = new Loader({
  global: fixup(window) // substitui ‘console.log’
});
loader.eval("console.log('hello world!');");

// Manipula módulo de cache diretamente
System.get('jquery');
System.set('jquery', Module({$: $})); // CUIDADO: ainda não finalizado
```

### Map + Set + WeakMap + WeakSet
Estruturas de dados eficientes para algoritmos comuns. WeakMaps proveem coleções de pares 
chave/valor em que as chaves são fracamente referenciadas (weakly referenced).

```JavaScript
// Sets
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Maps
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
// Porque o objeto adicionado não possui outras referências, não será mantido no conjunto 
```

### Proxies
Proxies permitem a criação de objetos com a gama completa de comportamentos de host objects de 
JavaScript. Podem ser usados para interceptação, virtualização de objetos, logging/profiling etc.

```JavaScript
// Proxying de um objeto normal
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```JavaScript
// Proxying de um objeto de função
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

Existem "armadilhas" para todas as metaoperações a nível de runtime:

```JavaScript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

### Símbolos (Symbols)
Symbols são um novo tipo de primitivo que permitem controle de acesso ao estado de objeto. Permitem 
que se use propriedades como chave tanto para `string` (como em ES5), quanto `symbol`. Opcionalmente, 
é possível usar o parâmetro `description` em debugging -- mas não é parte da identidade. Symbols são 
únicos (como gensym), mas não privados, já que podem ser expostos através de características de 
reflection, como `Object.getOwnPropertySymbols`.

```JavaScript
var MyClass = (function() {

  // módulo de símbolo em escopo
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

  return MyClass;
})();

var c = new MyClass("hello")
c["key"] === undefined
```

### Subclassable Built-ins
Em ES6, built-ins como `Array`, `Date` e `Element`s DOM podem ser "subclasseados".

Construção de Objeto para a função chamada `Ctor` agora usa 2 fases -- ambas virtualmente 
"despachadas" (dispatched):

- Chama `Ctor[@@create]` para alocar o objeto, instalando qualquer comportamento especial
- Invoca o construtor na nova instância para inicializar

O conhecido símbolo `@@create` é avaliado via `Symbol.create`. Built-ins agora expõem explicitamente 
seus `@@create`.

```JavaScript
// Pseudocódigo de Array
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // Instala [[DefineOwnProperty]]
        // para magicamente atualizar 'length'
    }
}

// Código de usuário do Array subclasseado
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// "new" em 2 fases:
// 1) Chama @@create para alocar o objeto
// 2) Invoca o construtor na nova instância
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```

### Math + Number + String + Array + APIs de Objetos
Muitas novas adições de bibliotecas, incluindo bibliotecas core d Math, helpers de conversão de 
Array, helpers de String e `Object.assign` para copiar.

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".includes("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // Returns a real Array
Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1) // [0,7,7]
[1, 2, 3].find(x => x == 3) // 3
[1, 2, 3].findIndex(x => x == 2) // 1
[1, 2, 3, 4, 5].copyWithin(3, 0) // [1, 2, 3, 1, 2]
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

### Binário e Octal literais
2 novas formas de literais numéricos foram adicionados para binário (`b`) e octal (`o`).

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```

### Promises
Promises são a biblioteca para programação assíncrona. Promises são a primeira representação de 
classe para um valor que pode estar disponível no futuro.

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

### Reflect API
API completa para reflection expõe metaoperações em nível de runtime em objetos. Isso é efetivamente 
o inverso da Proxy API e permite realizar chamadas correspondendo às mesmas metaoperações, como as 
proxy traps. Especialmente útil para implementar proxies.

```JavaScript
// Ainda sem exemplo
```

### Tail Calls
Calls in tail-position are guaranteed to not grow the stack unboundedly.  Makes recursive algorithms safe in the face of unbounded inputs.

Chamadas em tail-position (no final da instrução) garantem o não crescimento da stack de forma 
descontrolada. Fazer algoritmos recursivos de forma segura em face a inputs descontrolados.

```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// Stack overflow na maioria das implementações atuais,
// mas seguro em inputs arbitrários em ES6
factorial(100000)
``
