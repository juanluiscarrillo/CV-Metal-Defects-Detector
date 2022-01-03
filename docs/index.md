# Visión Artificial: Metal imperfections detector 

Aplicación de visión artificial para la detección de imperfecciones en superficies de metales.

El trabajo combina varias tecnologías:
- Clasificación de los distintos tipos de imperfecciones presentes en las imágenes, realizado con una red neuronal convolucional, implementada en Python con Tensorflow.
- Detección de imperfecciones (lugares en la imagen donde apararecen las imperfecciones), implementada en Python con OpenCv, mediante técnicas clásicas de Visión Artificial.
- Servidor HTTP implementado en Python para recibir peticiones de detección sobre imágenes.
- Librería Java para integrar la solución en una aplicación de cliente implementada en este lenguaje.
- Creación de un Docker para el despliegue de la aplicación.

A continuación, se desarrolla la explicación del trabajo

## Introducción

Esta aplicación se desarrolla para la asignatura de *Aplicaciones* del Máster de Visión Artificial de la Universidad Rey Juan Carlos de Madrid. 

Un supuesto cliente frabricante de láminas metálica desea contratar los servicios de una empresa de Visión Artificial para realizar el control de calidad de las láminas. Las láminas que fabrica pueden presentar 6 tipos de defectos o imperfecciones:



El cliente proporciona un conjunto de imágenes, junto con sus anotaciones, de ejemplo, accesibles en el siguiente [enlace](https://www.kaggle.com/kaustubhdikshit/neu-surface-defect-database).







## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/juanluiscarrillo/CV-Metal-Imperfections-Detector/edit/main/docs/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/juanluiscarrillo/CV-Metal-Imperfections-Detector/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
