---
title: "Mi Reporte Pract. 4"
date: 2026-05-28
draft: false
---



# REPORTE DE PRÁCTICAS DE LABORATORIO
## PARADIGMA DE PROGRAMACIÓN LÓGICA CON PROLOG

**Asignatura:** Paradigmas de Programación / Programación Lógica y Funcional  
**Facultad:** Ciencias de la Computación e Ingeniería  

---

### DATOS DEL ALUMNO
* **Nombre Completo:** Humberto Flores Espero  
* **Matrícula:** 376504  
* **Fecha de Entrega:** 29 de mayo de 2026  

---

## ÍNDICE
1. Resumen / Introducción al Paradigma Lógico
2. Primera Sesión: Instalación del Entorno de Desarrollo e Introducción a Prolog
   - 2.1 Configuración de SWI-Prolog
   - 2.2 Conceptos Fundamentales de la Sintaxis
   - 2.3 Ejemplo Base: Base de Conocimiento Familiar (Árbol Genealógico)
3. Segunda Sesión: Continuación de Programación con Prolog
   - 3.1 El Concepto y Manejo de la Recursividad
   - 3.2 Estructura y Manipulación de Listas
   - 3.3 Operaciones Aritméticas y Comparaciones (`is`)
4. Tercera Sesión: Aplicaciones Prácticas con Prolog
   - 4.1 Resolución del Problema de "Las Torres de Hanoi"
   - 4.2 Resolución del Problema de "La Banana y el Mono"
5. Conclusiones Generales
6. Referencias Bibliográficas

---

## 1. Resumen / Introducción al Paradigma Lógico

El paradigma de programación lógica representa un giro fundamental en comparación con el modelo imperativo convencional (C, C++, Java). Mientras que la programación tradicional exige que el desarrollador defina una secuencia algorítmica explícita paso a paso (*cómo* resolver el problema mediante la mutación de estados), el paradigma lógico se fundamenta en la lógica de predicados de primer orden para describir relaciones, restricciones y entidades del dominio del problema (*qué* es el problema).

El motor de ejecución del lenguaje lógico (habitualmente basado en una estrategia de resolución de cláusulas de Horn) actúa de forma autónoma empleando un mecanismo deductivo para responder a preguntas o consultas formuladas por el usuario. **Prolog** (PROgramming in LOGic) es la máxima expresión de este enfoque. Sus cimientos se apoyan sobre tres pilares conceptuales indispensables:
* **Hechos:** Afirmaciones atómicas incondicionales del dominio que se asumen como verdaderas (ej. `tiene_computadora(humberto).`).
* **Reglas:** Estructuras condicionales que expresan implicaciones lógicas. Tienen la forma `Cabeza :- Cuerpo.`, lo cual denota que la *Cabeza* es verdadera si y solo si todas las premisas declaradas en el *Cuerpo* se satisfacen simultáneamente.
* **Consultas:** El mecanismo de interfaz con el usuario, donde se interroga al sistema para comprobar la veracidad de una proposición o para instanciar variables que satisfagan un conjunto de condiciones.

Para lograr este propósito, Prolog recurre a la **Unificación** (un algoritmo que empareja términos y asigna valores de manera bidireccional a variables libres) y al **Backtracking** (vuelta atrás), un proceso automático de búsqueda en profundidad que retrocede en el árbol de derivación al encontrarse con un callejón sin salida lógico, permitiendo evaluar rutas alternativas de manera sistemática.

---

## 2. Primera Sesión: Instalación del Entorno de Desarrollo e Introducción a Prolog

### 2.1 Configuración de SWI-Prolog
Para ejecutar los programas diseñados en esta práctica, se utilizó **SWI-Prolog**, un compilador e intérprete gratuito, de código abierto y ampliamente adoptado en entornos de investigación y académicos debido a su estabilidad y herramientas de depuración gráfica integradas.

#### Procedimiento de Instalación en Sistemas Operativos comunes:
1. **Microsoft Windows:**
   - Se descargó el paquete ejecutable de 64 bits (`.exe`) desde la sección *Stable Versions* de la página oficial de SWI-Prolog.
   - Durante la instalación, se marcó la casilla obligatoria: *"Add swipl to the system PATH for all users"*. Esto permite invocar al compilador desde cualquier terminal del sistema operativo sin necesidad de rutas absolutas.
2. **Linux (Ubuntu/Debian):**
   - El entorno se instaló agregando el repositorio oficial PPA para garantizar la versión más actualizada mediante los comandos:
     ```bash
     sudo apt-add-repository ppa:swi-prolog/stable
     sudo apt update
     sudo apt install swi-prolog
     ```

Para validar que el proceso se realizó correctamente, se abrió una terminal de comandos y se digitó `swipl`. Al hacerlo, se visualizó la cabecera del intérprete y el prompt interactivo de entrada clásico del lenguaje:
```text
Welcome to SWI-Prolog (version 9.x.x)
?-