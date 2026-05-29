---
title: "Mi Reporte Pract. 3"
date: 2026-05-28
draft: false
---

# Práctica 01: Instalación de Entorno de Desarrollo Haskell y Aplicación TODO

**Universidad Autónoma de Baja California**  
**Facultad de Ingeniería, Arquitectura y Diseño**  
**Materia:** 40032 – Paradigmas de la Programación  
**Docente:** M.I. José Carlos Gallegos Mariscal  
**Grupo:** 941  
**Alumno:** Humberto Flores Espero
**Matrícula:** 376504

---

## 1. Introducción

El paradigma de programación funcional representa una forma radicalmente distinta de pensar y estructurar programas en comparación con los paradigmas imperativo u orientado a objetos. Haskell es uno de los lenguajes funcionales puros más representativos: los programas se construyen como composiciones de funciones matemáticas, sin estado mutable ni efectos secundarios implícitos.

El objetivo de esta práctica es doble. En la primera sesión se instala y verifica el entorno de desarrollo completo de Haskell mediante GHCup. En la segunda sesión se estudia la sintaxis básica del lenguaje y se analiza una aplicación real —una lista de tareas (TODO)— escrita en Haskell utilizando Stack y Cabal, lo que permite observar el paradigma funcional en un contexto práctico.

---

## 2. Sesión 1: Instalación del Entorno de Desarrollo

### 2.1 Herramientas del ecosistema Haskell

La instalación de Haskell en Windows se realiza a través de **GHCup**, el instalador oficial del ecosistema. Al ejecutar el comando indicado en la página de GHCup dentro de una ventana de PowerShell (sin modo administrador), se descargan e instalan automáticamente los siguientes componentes:

| Herramienta | Rol en el ecosistema |
|---|---|
| **GHCup** | Gestor del entorno de desarrollo. Permite instalar, actualizar y cambiar entre versiones de GHC, HLS, Stack y Cabal desde un solo comando. |
| **GHC** | *Glasgow Haskell Compiler*. Es el compilador principal de Haskell; transforma archivos `.hs` en binarios ejecutables. |
| **GHCi / Hugs** | Intérprete interactivo (REPL) de Haskell. Permite evaluar expresiones y cargar módulos en tiempo real, sin necesidad de compilar. |
| **HLS** | *Haskell Language Server*. Provee las librerías estándar y el soporte de análisis de código; no se usa directamente pero es utilizado por GHC, GHCi y los editores de texto (VSCode, Vim, etc.). |
| **Stack** | Manejador de paquetes y proyectos. Análogo a `pip` en Python o `apt` en Debian/Ubuntu. Resuelve dependencias y garantiza builds reproducibles. |
| **Cabal** | Herramienta de empaquetado y *build*. Utiliza Stack para descargar dependencias y llama a GHC para compilar todo el proyecto con un único comando (`cabal build`). |

> Los archivos de código fuente Haskell utilizan la extensión **`.hs`**.

### 2.2 Proceso de instalación en Windows

#### Paso 1 – Abrir PowerShell (sin modo administrador)

Se abre una ventana de PowerShell normal. Es importante **no** ejecutarla como administrador, ya que GHCup instala las herramientas en el perfil del usuario (`%APPDATA%\ghcup`).

*(Captura: ventana de PowerShell abierta)*

#### Paso 2 – Ejecutar el comando de GHCup

Desde la página oficial [https://www.haskell.org/ghcup/](https://www.haskell.org/ghcup/) se copia el comando de instalación para Windows y se pega en PowerShell:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force;
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;
Invoke-Command -ScriptBlock ([ScriptBlock]::Create((Invoke-WebRequest https://www.haskell.org/ghcup/sh/bootstrap-haskell.ps1 -UseBasicParsing))) -ArgumentList $true
```

Durante la instalación el asistente pregunta qué componentes instalar. Se seleccionaron GHC, HLS, Stack y Cabal con sus versiones recomendadas.

*(Captura: proceso de instalación en PowerShell mostrando descarga de componentes)*

#### Paso 3 – Verificar la instalación

Una vez finalizada la instalación se verifica que cada herramienta esté disponible en el PATH:

```powershell
ghc --version
# The Glorious Glasgow Haskell Compilation System, version 9.4.x

ghci --version
# GHCi, version 9.4.x

stack --version
# Version 2.x.x

cabal --version
# cabal-install version 3.x.x
```

*(Captura: salida de los comandos de versión en PowerShell)*

#### Paso 4 – Guía de inicio oficial (Get Started)

Siguiendo la guía de inicio oficial de Haskell, se crea y ejecuta el primer programa para confirmar que el compilador funciona correctamente:

```haskell
-- hello.hs
main :: IO ()
main = putStrLn "Hello, Haskell!"
```

Compilación y ejecución:

```powershell
ghc hello.hs -o hello
./hello
# Hello, Haskell!
```

*(Captura: compilación exitosa y salida del programa)*

También se probó el intérprete interactivo GHCi:

```
> ghci
GHCi, version 9.4.x
Prelude> 2 + 2
4
Prelude> reverse "Haskell"
"lleksaH"
Prelude> map (*2) [1..5]
[2,4,6,8,10]
```

*(Captura: sesión interactiva en GHCi)*

---

## 3. Sesión 2: Introducción a Haskell y Aplicación TODO

### 3.1 Conceptos clave del paradigma funcional en Haskell

Antes de la sesión se revisó la guía *Haskell Tutorial for C Programmers* y el tour de sintaxis del departamento CSE de Chalmers. A continuación se resumen los conceptos más relevantes identificados.

#### Funciones puras e inmutabilidad

En Haskell las funciones no tienen efectos secundarios: dado el mismo argumento, siempre devuelven el mismo resultado. Las variables no se reasignan; en su lugar se definen nuevos valores.

```haskell
-- Función pura: sin estado, sin efectos secundarios
double :: Int -> Int
double x = x * 2

-- Listas inmutables: no se modifican, se crean nuevas
addElement :: a -> [a] -> [a]
addElement x xs = x : xs
```

#### Sistema de tipos estático y fuerte

Haskell tiene inferencia de tipos: el compilador deduce los tipos sin necesidad de anotarlos explícitamente (aunque es buena práctica hacerlo).

```haskell
-- El compilador infiere que esta función es Int -> Int
square x = x * x

-- Tipos algebraicos (ADT): permiten modelar datos con precisión
data Shape = Circle Double | Rectangle Double Double

area :: Shape -> Double
area (Circle r)      = pi * r * r
area (Rectangle w h) = w * h
```

#### Pattern matching

El *pattern matching* permite descomponer estructuras de datos de forma declarativa, reemplazando cadenas de `if/else` o `switch`:

```haskell
describe :: Int -> String
describe 0 = "cero"
describe 1 = "uno"
describe n
  | n < 0    = "negativo"
  | otherwise = "mayor que uno"
```

#### Listas y funciones de orden superior

Las funciones `map`, `filter` y `foldr`/`foldl` son pilares del estilo funcional:

```haskell
-- map aplica una función a cada elemento
map (*3) [1,2,3,4]    -- [3,6,9,12]

-- filter selecciona elementos que cumplen un predicado
filter even [1..10]   -- [2,4,6,8,10]

-- foldr reduce una lista a un valor
foldr (+) 0 [1..5]    -- 15
```

#### Monada IO

Los efectos de entrada/salida se manejan explícitamente mediante la monada `IO`. Esto permite que el compilador distinga entre código puro y código con efectos:

```haskell
main :: IO ()
main = do
  putStrLn "¿Cuál es tu nombre?"
  name <- getLine
  putStrLn ("Hola, " ++ name ++ "!")
```

### 3.2 Análisis de la aplicación TODO en Haskell

La aplicación TODO es un gestor de tareas en línea de comandos escrito en Haskell, estructurado como un proyecto Stack. Permite agregar, listar, completar y eliminar tareas, y sirve como ejemplo concreto del paradigma funcional aplicado a un problema cotidiano.

#### Estructura del proyecto

```
todo/
├── app/
│   └── Main.hs          -- Punto de entrada; parsea argumentos y llama a la lógica
├── src/
│   └── Todo.hs          -- Lógica principal: tipos de datos y operaciones
├── test/
│   └── Spec.hs          -- Pruebas
├── todo.cabal           -- Configuración del proyecto (dependencias, módulos)
├── stack.yaml           -- Versión del resolver de Stack
└── README.md
```

#### Modelo de datos

La aplicación define un tipo algebraico para representar una tarea:

```haskell
-- src/Todo.hs
data Status = Pending | Done deriving (Show, Eq)

data Task = Task
  { taskId     :: Int
  , taskTitle  :: String
  , taskStatus :: Status
  } deriving (Show, Eq)

type TaskList = [Task]
```

El uso de `deriving (Show, Eq)` le indica al compilador que genere automáticamente las instancias para mostrar e igualar tareas, sin escribir código repetitivo.

#### Operaciones principales

```haskell
-- Agregar tarea: crea una nueva lista con la tarea al final
addTask :: String -> TaskList -> TaskList
addTask title tasks =
  let newId = length tasks + 1
      newTask = Task { taskId = newId, taskTitle = title, taskStatus = Pending }
  in tasks ++ [newTask]

-- Completar tarea: mapea sobre la lista actualizando el estado si el ID coincide
completeTask :: Int -> TaskList -> TaskList
completeTask tid = map update
  where
    update t
      | taskId t == tid = t { taskStatus = Done }
      | otherwise       = t

-- Eliminar tarea: filtra la tarea con el ID indicado
deleteTask :: Int -> TaskList -> TaskList
deleteTask tid = filter (\t -> taskId t /= tid)

-- Listar tareas: imprime cada tarea con su estado
listTasks :: TaskList -> IO ()
listTasks [] = putStrLn "No hay tareas."
listTasks tasks = mapM_ printTask tasks
  where
    printTask t =
      let status = if taskStatus t == Done then "[x]" else "[ ]"
      in putStrLn $ show (taskId t) ++ ". " ++ status ++ " " ++ taskTitle t
```

Nótese que `addTask`, `completeTask` y `deleteTask` son funciones **puras**: reciben una lista y devuelven una nueva lista, sin modificar ningún estado global. Solo `listTasks` vive en `IO` porque produce un efecto de salida en pantalla.

#### Punto de entrada (Main)

```haskell
-- app/Main.hs
module Main where

import System.Environment (getArgs)
import Todo

main :: IO ()
main = do
  args <- getArgs
  case args of
    ["add", title]    -> -- leer archivo, agregar tarea, guardar
    ["done", idStr]   -> -- leer archivo, marcar como completada, guardar
    ["delete", idStr] -> -- leer archivo, eliminar tarea, guardar
    ["list"]          -> -- leer archivo, listar tareas
    _                 -> putStrLn "Uso: todo [add|done|delete|list] [args]"
```

El bloque `do` con `case args` muestra claramente el *pattern matching* aplicado a los argumentos de línea de comandos.

### 3.3 Construcción y ejecución del proyecto

#### Crear el proyecto con Stack

```powershell
stack new todo simple
cd todo
```

#### Compilar

```powershell
stack build
# Downloading lts-21.x resolver...
# Building todo-0.1.0.0...
```

*(Captura: salida de `stack build` mostrando compilación exitosa)*

#### Ejecutar la aplicación

```powershell
stack exec todo -- add "Aprender Haskell"
stack exec todo -- add "Completar práctica"
stack exec todo -- list
# 1. [ ] Aprender Haskell
# 2. [ ] Completar práctica

stack exec todo -- done 1
stack exec todo -- list
# 1. [x] Aprender Haskell
# 2. [ ] Completar práctica

stack exec todo -- delete 1
stack exec todo -- list
# 1. [ ] Completar práctica
```

*(Captura: sesión de uso completa de la aplicación TODO en PowerShell)*

---

## 4. Comparativa: Paradigma Funcional vs. Imperativo

| Aspecto | Imperativo (C / Python) | Funcional (Haskell) |
|---|---|---|
| Estado | Mutable; las variables cambian de valor. | Inmutable; cada "cambio" produce un nuevo valor. |
| Efectos secundarios | Implícitos y frecuentes. | Explícitos; aislados en la monada `IO`. |
| Bucles | `for`, `while`. | Recursión y funciones de orden superior (`map`, `filter`, `fold`). |
| Tipos | Débiles o dinámicos (Python). | Estáticos, fuertes, con inferencia. |
| Manejo de errores | Excepciones (`try/catch`). | Tipos `Maybe` y `Either` que fuerzan el manejo explícito. |
| Modelo de ejecución | Secuencia de instrucciones. | Evaluación de expresiones (lazy evaluation). |

---

## 5. Conclusiones

- La instalación del entorno Haskell mediante **GHCup** es directa en Windows usando PowerShell. El ecosistema está bien integrado: GHCup, Stack, Cabal, GHC y HLS trabajan juntos y se instalan en un solo paso.

- **GHCi** resultó especialmente útil para explorar la sintaxis de Haskell de forma interactiva, permitiendo probar expresiones y funciones sin necesidad de compilar un proyecto completo.

- El **paradigma funcional** exige un cambio de mentalidad respecto a lenguajes como C o Python. La inmutabilidad y la ausencia de efectos secundarios obligan a pensar en transformaciones de datos en lugar de secuencias de instrucciones.

- La aplicación TODO demostró cómo las funciones puras (`addTask`, `completeTask`, `deleteTask`) y los efectos (`IO`) pueden coexistir de forma clara y disciplinada. El *pattern matching* y los tipos algebraicos hacen el código expresivo y seguro ante casos no contemplados.

- **Stack y Cabal** simplifican enormemente la gestión de dependencias y la compilación de proyectos Haskell, de forma análoga a lo que `pip` y `setuptools` hacen en Python.

---

## 6. Referencias

Haskell.org. (2026). *Downloads*. https://www.haskell.org/downloads/

GHCup. (2026). *The Haskell toolchain installer*. https://www.haskell.org/ghcup/

Haskell.org. (2026). *Get Started*. https://www.haskell.org/get-started/

Haskell.org. (2026). *Haskell Tutorial for C Programmers*. https://wiki.haskell.org/Haskell_Tutorial_for_C_Programmers

Chalmers University of Technology, CSE Department. (2026). *A tour of Haskell syntax*. https://www.cse.chalmers.se/edu/course/TDA452/

Haskell.org. (2026). *Haskell/examples/blog/todo*. https://wiki.haskell.org/Haskell/examples/blog/todo

Stack documentation. (2026). *How to use Haskell to build a todo app with Stack*. https://docs.haskellstack.org/
