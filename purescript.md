
<h1 style="text-align: center;" markdown="1">Introducción al Lenguaje PureScript</h1>
<img src="http://blog.brakmic.com/wp-content/uploads/2016/01/ps-logo.png"
     width="300" height="200" style="margin-left:35%;margin-top:7%;">

<h3 style="text-align: center;" markdown="1">Javier Casas  |  Omar García</h3>

---

# Purescript

 - Lenguaje pequeño y fuertemente tipado que compila JavaScript entendible y eficiente

 - Lenguaje funcional puro, inspirado en Haskell

 - Sistema de tipos sólido y expresivo diseñado para producir código JavaScript desde un inicio

 - Provee una rica FFI facilitando interacción fácil y directa con otras librerías de JavaScript


---

# Origen

Pequeño lenguaje con el objetivo de crear DSL usando un  sistema de tipos con funcionalidades como tipos de datos algebraicos y records extensibles no disponible en otros lenguajes AltJS

<img src="https://pbs.twimg.com/profile_images/527150942357966848/RbdnJC9g_400x400.jpeg"
     style="margin-left:30%">


---

# Antecedentes

 - Emscripten: LLVM bytecode (from GHC)  →  JavaScript

 - Fay: subconjunto de Haskell  →  JavaScript

 - GHCJS: GHC Haskell  →  JavaScript

 - Haste: Haskell  →  JavaScript

 - UHC: Haskell  →  JavaScript backend

 - Elm: Inspirado en Haskell. Enfoque Reactivo funcional →  JavaScript


---

# Ventajas con respecto a Haskell

 - No tiene runtime

 - Es estricto y no perezoso

 - FFI fácil de usar

 - Fácil integración con librerías de JavaScript

 - Integración con herramientas como Node.js y Webpack


---

# Funcionalidades

|                              |                               |
| :---                         | :---                          |
| Tipos de datos algebraicos   | Records extendibles           |
|                              |                               |
| Pattern Matching             | Efectos extendibles           |
|                              |                               |
| Inferencia de tipos          | Módulos                       |
|                              |                               |
| Type classes                 | Simple FFI                    |
|                              |                               |
| Higher kinded types          | No runtime                    |
|                              |                               |
| Rank-N types                 | Código generado entendible    |


---

# Hello World

```haskell
module Main (main) where  

import Prelude
import qualified Control.Monad.Eff as Eff
import qualified Control.Monad.Eff.Console as Console

main :: Eff.Eff (console :: Console.CONSOLE) Unit
main = do
 Console.log "Hello from PureScript!"

```

```javascript
"use strict";

var Control_Monad_Eff_Console = require("Control.Monad.Eff.Console");
var main = Control_Monad_Eff_Console.log("Hello from PureScript!");
module.exports = {
   main: main
};

```

---

# Literales

```haskell
a = 0.0 :: Number


b = "a" :: String


c = true :: Boolean


d = [0.0] :: Array Number


e = {foo: 0.0} :: { foo :: Number }


f = (\a -> a) :: forall a. a -> a
```

---

# Funciones

```haskell
sum :: Int -> Int -> Int
sum a b = a + b

successor :: Int -> Int
successor = sum 1

sumSuccessors :: Int -> Int -> Int
sumSuccessors a b = sum (successor a) (successor b)

seven :: Int
seven = sumSuccessors 2 3

identity :: forall t. t -> t
identity a = a

const :: forall a b. a -> b -> a
const x y = x

($) :: forall a b. (a -> b) -> a -> b
($) f x = f x
infixr 0 $

```

---

# Records

```haskell
john :: { name :: String, birthYear :: Int }
john = {name: "Hank", birthYear: 1985}
```

```haskell
vehicle :: { name :: String, mfgDate :: Int }
vehicle = { name: "Tesla", mfgDate: 2014}
```

```haskell
-- forall: tipo cuantifiado universalmente
greet :: forall r. { name :: String | r } -> String
greet namedThing = "Hello, " <> namedThing.name
```

```haskell
johnJr :: { name :: String, birthYear :: Int }
johnJr = john { birthYear = 2010 }

```

---

# Entensible Records

```haskell
fullName  :: { first :: String, last :: String } -> String
fullName { first: f, last: l } = f <> " " <> l

```

```haskell

fullName' ::           { first :: String, last :: String     } -> String
...

fillName { first: "John", last: "Doe", age: 26 }

-- Error: Type of expression contains additional label age
```

```haskell

fullName  :: forall r. { first :: String, last :: String | r } -> String
...

fillName { first: "John", last: "Doe", age: 26 }

-- OK

```

---

# Records

Se considera un buena práctica para incrementar el tipado seguro
la utilización de type alias


```haskell
type Person r = { first :: String, last :: String | r }

fullName :: forall r. Person r -> String
...
```


```haskell
type Person' = { first :: String
               , last :: String
               }

fullName' :: Person' -> String
...
```

---

# Definiendo tipos de datos


Tipos de datos algebraicos
```haskell
data Ordering = Less | Equal | Greater

data List t = EmptyList | Cons t (List t)
```

Alias

```haskell
type Option = Maybe

type NumberArray = Array Number

type NumberList = List Number

type Point = { x :: Number, y :: Number }

type Pair a b = { left :: a, right :: b }

type Predicate a = a -> Boolean
```

---

# Pattern Matching

Simple
```haskell
toString :: Boolean -> String
toString true  = "true"
toString false = "false"
```

Arrays
```haskell
takeFive :: Array Int -> Int
takeFive [0, 1, a, b, _] = a * b
takeFive _ = 0
```

Records y named patterns
```haskell
doubleZero :: Point -> Point
doubleZero p@{ x: 0, y: y } = p { y = y * 2.0 }
doubleZero p = p

```

---

#Pattern Matching

Patrones anidados:
```haskell
type Address = { street :: String, city :: String }
type Person = { name :: String, address :: Address }

livesInLA :: Person -> Boolean
livesInLA { address: { city: "Los Angeles" } } = true
livesInLA _ = false
```

Named patterns y guards
```haskell
sortPair :: Array Int -> Array Int
sortPair arr@[x, y]
  | x <= y = arr
  | otherwise = [y, x]
sortPair arr = arr
```

---

# Estructura típica de un módulo

```haskell

module ModuleName (value0, main, Type0(..)) where

import OtherModule (value1, Type1(..))
import qualified Prelude as P

data Type0 t = Constructor0 t | Constructor1 t t

value0 :: Type0 Int
value0 = Constructor1 0 1

main :: Eff () Unit
main = P.return P.unit

```
---

# Efectos

```haskell
main :: Eff.Eff (console :: Console.CONSOLE) Unit

```

### Tipos de Efectos

| Nativos                      | Navegador | No nativos                            |
| :---                         | :---:     | --:                                   |
| Console IO                   | Maybe     |  Manipulación del DOM                 | |                              |           |                                       |
| Generación de números random | Either    | XMLHttpRequest / AJAX requests        |
|                              |           |                                       |
| Excepciones                  |           | Interacción con websockets            |
|                              |           |                                       |
| Lectura/escritura estados mutables |     | Interacción con almacenamiento local  |


---

# Efectos extensibles

```haskell

module EffExample (main) where

import Control.Monad.Eff (Eff(..))
import Control.Monad.Eff.Random (random, RANDOM())
-- random :: Eff (random :: RANDOM) Number

import Control.Monad.Eff.Console (log, print, CONSOLE())
-- log :: String -> Eff (console :: CONSOLE) Unit
-- print :: ... -> Eff (console :: CONSOLE) Unit

main :: Eff (console :: CONSOLE, random :: RANDOM) Unit
main = random >>= print
```

---

# Componiendo Efectos

```haskell
main :: Eff (console :: CONSOLE, random :: Random) Unit
main = do
  a <- random
  b <- random
  log "First random number:"
  print a
  log "Second random number:"
  print b

```

---
# Purescript tiene type classes

```haskell
class Show a where
  show :: a -> String
```

```haskell
instance showBoolean :: Show Boolean where
  show true = "true"
  show false = "false"
```

```haskell
instance showArray :: (Show a) => Show (Array a) where
  show array = "[" <> map show array <> "]"
```

---

# FFI: PureScript --> JavaScript


```haskell
module A where

import Prelude

add x y = x + y
```

```javascript
var A = require('A');
A.add(3)(4);
// 7
```

---

# FFI: JavaScript --> PureScript

Simple

```javascript
// URI.js
exports.encodeURIComponent = encodeURIComponent;
```

```haskell
-- URI.purs
module URI where

foreign import encodeURIComponent :: String -> String


encodeURIComponent "hello world"
-- "hello%20world"
```

No tan simple

```haskell
foreign import data TimeoutID :: *
foreign import data TIMER :: !

foreign import setTimeout :: forall e. Eff e Unit -> Number -> Eff (timer :: TIMER) TimeoutID
foreign import clearTimeout :: TimeoutID -> Eff (timer :: TIMER) Unit
```