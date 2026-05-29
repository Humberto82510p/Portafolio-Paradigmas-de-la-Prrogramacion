---
title: "Mi Reporte Pract. 0"
date: 2026-05-28
draft: false
---
## Materia: 
Paradigmas de la Programación

---

## Práctica 0:  Uso de repositorios

---

### Alumno: 

Flores Espero Humberto
**Matrícula:** 376504  

---

### Docente:

Jose Carlos Gallegos Mariscal

---

### Grupo: 
941

---

Ensenada, B.C. a 20 de febrero de 2026

---

# PRIMERA SESIÓN: MARKDOWN

---

## ¿Qué es Markdown?

Markdown fue desarrollado en 2004 por John Gruber, y se refiere tanto a una manera de dar formato a archivos de texto plano, como a una utilidad escrita en el lenguaje de programación Perl para convertir archivos Markdown en HTML.

En esta práctica nos centraremos en la primera acepción: aprender a escribir archivos utilizando la sintaxis de Markdown para estructurar información de manera clara y semántica.

---

## ¿Cómo se utiliza?

Markdown funciona bajo un principio muy simple:

_Usa símbolos especiales para representar la estructura semántica del contenido._

Es decir, ciertos caracteres indican qué tipo de contenido es el texto.

Por ejemplo:

- `#` indica un encabezado.
- `*` o `_` indican énfasis.
- `-` indica una lista.
- `` ` `` indica código.

Estos símbolos no son decoración, sino marcas semánticas que describen la función del texto dentro del documento.

---

## ¿Cuál es su sintaxis?

La sintaxis de Markdown se caracteriza por ser:

*  **Lineal** → Se interpreta línea por línea.
*  **Basada en prefijos y delimitadores** → Usa símbolos antes o alrededor del texto.
* **Sensible al contexto** → Lo que está antes y después influye en la interpretación.
* **No estrictamente tipada** → No requiere declarar tipos de datos.
* **No compilada** → Se interpreta o transforma (por ejemplo, a HTML).

---

### Conclusión

Markdown permite estructurar documentos de manera clara, sencilla y portable, facilitando la conversión a otros formatos como HTML o PDF.

---
# SEGUNDA SESIÓN: GIT Y GITHUB

## ¿Qué es Git y GitHub?

### Git

Git es un sistema de control de versiones distribuido, lo que significa que cada copia local de un proyecto contiene un historial completo de cambios.

Cada clon del este proyecto es un repositorio completamente funcional. Esto permite trabajar:

- Sin conexión a internet.
- De forma remota.
- Con historial completo del proyecto.

Git administra la evolución del código a través del tiempo.

---

### GitHub

GitHub es una plataforma en línea que utiliza Git.

En términos simples, es un servicio de alojamiento (hosting) para repositorios Git. Permite almacenar proyectos en la nube y facilitar la colaboración entre múltiples desarrolladores.

---

## ¿Cómo se utilizan?

### Git

Git se utiliza como un sistema de registro histórico estructurado. Su funcionamiento se basa en tres ideas fundamentales:

---

### 1. Registro de cambios

Cada modificación en un proyecto puede convertirse en una versión formal llamada commit.

Esto permite:

- Conservar el historial.
- Recuperar estados anteriores.
- Identificar errores.
- Comparar versiones.

---

### 2. Trabajo en líneas paralelas (ramas)

Git permite dividir el desarrollo en múltiples líneas independientes llamadas ramas (branches).

Una rama es una línea alternativa de evolución del proyecto.

Esto permite:

- Probar nuevas funciones sin afectar la versión principal.
- Trabajar en paralelo.
- Desarrollar características experimentales sin comprometer estabilidad.

---

### 3. Integración de cambios

Después de trabajar en paralelo, los cambios pueden integrarse nuevamente en una línea principal mediante procesos de fusión (merge).

Esto mantiene coherencia en el desarrollo del proyecto.

---

## GitHub:

### Uso como repositorio remoto:

GitHub actúa como un punto central donde:

- Se almacenan proyectos.
- Se sincronizan cambios.
- Se respaldan versiones.
- Se comparte el código.

Permite que múltiples personas trabajen sobre el mismo proyecto.

---

### Uso colaborativo:

GitHub introduce dinámicas sociales y organizativas como:

- **Pull Requests** → Solicitudes para integrar cambios.
- **Revisión de código**.
- **Gestión de incidencias (Issues)**.
- **Documentación compartida**.

---

### Coordinación de equipos

GitHub funciona como un entorno de coordinación distribuida para el desarrollo colaborativo.

Mientras Git gestiona la historia técnica del código, GitHub gestiona la interacción entre personas.

---

## ¿Cuáles son sus comandos esenciales?

### Git

- `git init` → Inicia un repositorio nuevo.
- `git clone` → Copia un repositorio existente.
- `git config` → Configura datos del usuario.
- `git add` → Prepara archivos para guardar.
- `git commit` → Guarda una versión del proyecto.
- `git status` → Muestra el estado actual.
- `git log` → Muestra el historial de versiones.
- `git branch` → Crea o lista ramas.
- `git checkout` → Cambia de rama o versión.
- `git merge` → Une ramas.
- `git remote` → Administra repositorios remotos.
- `git push` → Sube cambios al repositorio remoto.
- `git pull` → Descarga y fusiona cambios.
- `git fetch` → Descarga cambios sin fusionar.
- `git reset` → Revierte cambios o commits.
- `git rm` → Elimina archivos del repositorio.
- `git mv` → Mueve o renombra archivos.

---

### GitHub (Funciones principales)

- **Pull Request** → Solicitud para integrar cambios.
- **Fork** → Copia un repositorio a tu cuenta.
- **Issues** → Gestión de tareas o errores.
- **Actions** → Automatización de procesos.

---

## ¿Cómo se crea un repositorio?

### En Git (local):

1. Crear o elegir una carpeta del proyecto.
2. Inicializar Git en esa carpeta.
3. Configurar identidad (si es necesario).
4. Agregar archivos al control de versiones.
5. Crear el primer commit.

---

### En GitHub (remoto):

1. Iniciar sesión en GitHub.
2. Crear un nuevo repositorio desde el panel principal.
3. Definir:
   - Nombre del repositorio.
   - Descripción (opcional).
   - Público o privado.
4. Confirmar la creación.
5. (Opcional) Conectar el repositorio local con el remoto para sincronizar cambios.

---

### Conclusión:

Git permite controlar la evolución técnica del código a lo largo del tiempo, mientras que GitHub facilita la colaboración, sincronización y organización del desarrollo en equipo.

---
# TERCERA SESIÓN: HUGO Y GITHUB ACTIONS

## ¿Qué es Hugo?

Hugo es un generador de sitios estáticos (Static Site Generator) escrito en el lenguaje Go.

Su función es transformar archivos de contenido (generalmente en Markdown) en un sitio web estático compuesto por archivos HTML, CSS y JavaScript listos para ser publicados.

Características principales:

- Es muy rápido.
- No requiere base de datos.
- Genera sitios seguros (al ser estáticos).
- Es ideal para blogs, documentación y portafolios.

Hugo separa:

- **Contenido** (archivos Markdown).
- **Diseño** (temas y plantillas).
- **Configuración** (archivo config).

---

## ¿Qué es GitHub Actions?

GitHub Actions es una herramienta de automatización integrada en GitHub.

Permite ejecutar procesos automáticos cada vez que ocurre un evento en el repositorio, como:

- Hacer un push.
- Crear un pull request.
- Publicar una nueva versión.

En esta práctica se utiliza para:

- Construir automáticamente el sitio generado con Hugo.
- Publicarlo en GitHub Pages sin intervención manual.

---

## ¿Qué es GitHub Pages?

GitHub Pages es un servicio que permite publicar sitios web estáticos directamente desde un repositorio de GitHub.

Funciona como alojamiento gratuito para:

- Proyectos personales.
- Portafolios.
- Documentación.
- Blogs.

El sitio queda disponible en una dirección como:
https://usuario.github.io/repositorio


---

## ¿Cómo crear un sitio estático con Hugo?

Proceso general:

1. Instalar Hugo en el sistema.
2. Crear un nuevo sitio: hugo new site nombre-del-sitio
3. Elegir e instalar un tema.
4. Crear contenido en formato Markdown:hugo new posts/mi-entrada.md
5. Ejecutar servidor local para pruebas:hugo server
6. Generar los archivos estáticos finales:
   

Hugo generará una carpeta llamada `public/` que contiene el sitio listo para ser publicado.

---

## ¿Cómo subir el sitio a GitHub?

1. Inicializar el repositorio Git (si no existe).
2. Agregar los archivos del proyecto.
3. Hacer commit.
4. Conectar el repositorio con GitHub.
5. Subir los cambios con push.

El repositorio contendrá:

- Archivos de configuración.
- Contenido en Markdown.
- Tema.
- Configuración de GitHub Actions.

---

## ¿Cómo configurar GitHub Actions para publicar el sitio?

1. Crear una carpeta dentro del repositorio llamada: .github/workflows/

2. Crear un archivo YAML (por ejemplo: `hugo.yml`).

3. Definir el flujo de trabajo que:

- Instale Hugo.
- Compile el sitio.
- Publique la carpeta generada en GitHub Pages.

El proceso es automático:

- Cada vez que se haga un push a la rama principal.
- GitHub Actions construirá el sitio.
- Lo desplegará en GitHub Pages.

---

## Integración de conocimientos

En esta sesión se combinan los conocimientos previos:

- **Markdown** → Para escribir el contenido.
- **Git** → Para versionar el proyecto.
- **GitHub** → Para alojar el repositorio.
- **Hugo** → Para generar el sitio web.
- **GitHub Actions** → Para automatizar la publicación.

---

## Conclusión

La integración de Hugo con GitHub Actions permite crear un flujo de trabajo profesional donde:

- El contenido se escribe en Markdown.
- Se versiona con Git.
- Se almacena en GitHub.
- Se publica automáticamente en GitHub Pages.

Este proceso representa una práctica real utilizada en entornos profesionales para el desarrollo y despliegue de sitios web estáticos.
