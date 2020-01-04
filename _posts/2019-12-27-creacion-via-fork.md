---
title: Creación vía fork | Paso a paso
date: 2019-12-27 10:40:00 -0300
tags: jekyll liquid blogging
---

La bifurcación –*fork*– significa copiar o clonar un repositorio, lo que permite experimentar libremente con los cambios sin afectar el proyecto original.
Por lo general, la gente bifurca repos para proponer cambios al proyecto original o *usar ese proyecto como punto de partida*. Este último caso es el que nos ocupará de aquí en adelante...

## Paso 1

+ Suponiendo que ya contamos con una cuenta en GitHub, lo primero es iniciar sesión en dicha cuenta y hacer un *fork* de todo el repositorio del tema elegido –[Emerald](https://github.com/KingFelix/emerald)–
+ El nombre predeterminado de nuestro nuevo repositorio conviene cambiarlo (yo le puse [notas-mas-docs](https://github.com/dsigno/notas-mas-docs)), y habrá en este punto no confundirse y crear –como en muchos tutoriales se muestra– un nombre asociado a un *User Website*: de estos se permiten **solo uno** por cuenta GitHub.
+ Puede ser un buen momento para cambiar la descripción y URL de website (informativo) que está incluído en la *template* original

Si visitamos la dirección de la subcarpeta donde se verá nuestro sitio, notaremos  que *no se vé* tal como lo esperábamos... las templates, como dijimos, vienen pensadas para trabajar pensando en un *User Website*.

Traduzco lo siguiente:

>**Problema común #3**: El uso de un *project website* para su sitio web de Jekyll [presenta cierta complejidad](http://jekyllrb.com/docs/github-pages/#project_page_url_structure) porque su sitio web se publicará en un subdirectorio. La URL se verá como `http://yourname.github.io/repository-name`, lo que causará problemas en las plantillas de Jekyll, como **romper referencias** a imágenes y no permitirle obtener una vista previa del sitio web localmente.

## Paso 2

+ Aunque desde hace un tiempo GitHub cambió el criterio de publicar el sitio desde una nueva rama –*branch*– a crear llamada la **rama *gh-pages***, y se puede hacer directamente desde la rama *master* (ver [aquí](https://blog.webjeda.com/create-jekyll-blog/#step-3-check-whether-you-are-on-the-right-branchnot-required) ), **voy a crearla igualmente** para evitarme confusiones con anteriores lecturas...
+ Luego de creada, hice a *gh-pages* la **rama por defecto**.
+ Aprovecho y borro algunos de los posts de prueba (de relleno) con que contaba la plantilla original.

En este paso tomé decisiones más que nada personales... y posiblemente discutibles.

## Paso 3

En este paso vamos a trabajar sobre el archivo más determinante en el comportamiento de nuestro sitio Jekyll: **_config.yml**.

+ Hay cuestiones básicas de configuración sobre el sitio y su autor (o autores, vía *collections*) . Aquí se detallan el nombre del sitio, descripción, etc.

+ Para solucionar los problemas de visualización que aparecieron por usar la opción *project website* , la opción**baseurl** [Base URL : Serve the website from the given base URL] debe cambiarse a una que incluya el subdirectorio, y aquí apareció EL problema...

### EL problema en sus inicios

Del README.md de Emerald se sugiere lo siguiente:

> #### Baseurl
> Emerald was thought to be used mainly with Github, in particular into [project site](https://pages.github.com/). For this reason several tags have been included `{{ site.baseurl }}` to refer to the "/emerald/" directory. You can change the "baseurl" value into the `config.yml` file, to match your directory (for example "/blog/") or the root of your project. In that case you must set the "baseurl" value to "/".

Esto implica en mi sitio `baseurl: /notas-mas-docs/`  (notar el *trailing slash*)

¿Problema?: En los posts se veían duplicados los *slash* después del subdirectorio.

Ejemplo: `https://dsigno.github.io/notas-mas-docs//code-syntax`

### Intento de solución

Buscando y leyendo encontré esto en este [*issue*](https://github.com/jekyll/jekyll/issues/332#issuecomment-18952908)

> I finally figured out the trick, if you're looking for a solution with the standard URL for GitHub Pages (`username.github.io/project-name/`). Here's what to do:
>
> In `_config.yml`, set the `baseurl` option to `/project-name` -- note the leading slash and the **absence** of a trailing slash.
> Now you'll need to change the way you do links in your templates and posts, in the following two ways:
> When referencing JS or CSS files, do it like this: `{{ site.baseurl }}/path/to/css.css` -- note the slash immediately following the variable (just before "path").
>
> When doing permalinks or internal links, do it like this: `{{ site.baseurl }}{{ post.url }}` -- note that there is **no** slash between the two variables.
> [...]
> This way you can preview your site locally from the site root on localhost, but when GitHub generates your pages from the gh-pages branch all the URLs will start with `/project-name` and resolve properly.

Esto implica en mi sitio `baseurl: /notas-mas-docs`  (notar que eliminé el *trailing slash*)

¿Problema?: se perdieron las referencias a los estilos, logo, favicon...

### Solución (por ahora)

La manera que encontré de resolver esta cuestión es dejar el último *setting* para baseurl , pero tuve que rastrear dónde hará falta agregar un *slash* para recuperar las referencias correctas, por ej. en \_includes/head.html cambiar

`<link rel="stylesheet" href="{{ "css/main.css" | prepend: site.baseurl }}">`

a

`<link rel="stylesheet" href="{{ "/css/main.css" | prepend: site.baseurl }}">`

y lo mismo para solucionar el link al favicon. Por ahora funciona (veremos en local)

NOTA: falta chequear que pasa con el **atom.xml**

## Paso 4

Ya que me metí con \_includes/head.html, aproveché y cambié la fuente a Montserrat

`<link href="https://fonts.googleapis.com/css?family=Montserrat:400,400i,500,500i,700,700i&display=swap" rel="stylesheet">`

También tuve que hacer cambios en \ _sass/typography.scss

## Paso 5

 Publicar el primer post, *like a Hello World!*

Sólofalta crear un nuevo archivo markdown dentro de la carpeta **_posts**, con el formato de nombre de archivo `year-month-day-title.md`.

NOTA: Dejaremos para más adelante detalles sobre el **Front Matter** indispensable al comienzo del archivo markdown, sólo colocaremos el título del primer post.



***
FUENTES:

+ [Build a blog with Jekyll and Github Pages](http://andrewbtran.github.io/JRN-418/class13/jekyll/)
+ [Smashing Magazine - Build A Blog With Jekyll And GitHub Pages](https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/)
+ [Jekyll Issues aren't as Bad as You Think](https://blog.webjeda.com/jekyll-issues/)
