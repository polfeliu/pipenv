.. pipenv documentation master file, created by
   sphinx-quickstart on Mon Jan 30 13:28:36 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Pipenv: Python Dev Workflow for Humans
======================================

.. image:: https://img.shields.io/pypi/v/pipenv.svg
    :target: https://pypi.python.org/pypi/pipenv

.. image:: https://img.shields.io/pypi/l/pipenv.svg
    :target: https://pypi.python.org/pypi/pipenv

.. image:: https://img.shields.io/pypi/pyversions/pipenv.svg
    :target: https://pypi.python.org/pypi/pipenv

.. image:: https://img.shields.io/badge/Say%20Thanks!-🦉-1EAEDB.svg
    :target: https://saythanks.io/to/kennethreitz

---------------

**Pipenv** es una herramienta que apunta a traer todo lo mejor del mundo de empaquetado (bundler, composer, npm, cargo, yarn, etc.) al mundo de Python. *Windows es un ciudadano primera-clase en nuestro mundo*

Automáticamente crea y maneja un entorno virtual para tus proyectos, también como agregar/remover paquetes desde tu ``Pipfile`` como instalar/desisntalar paquetes. También genera el más importante ``Pipfile.lock``, que es usado para producir determinado build.

Pipenv está destinado principalmente a proporcionar a usuarios y desarrolladores de aplicaciones con un metodo sencillo para configurar un entorno de trabajo. Para la distinción entre librerias y aplicaciones y el uso de ``setup.py`` vs ``Pipfile`` para definir dependencias, mira :ref:`pipfile-vs-setuppy`.

.. raw:: html

    <iframe src="https://player.vimeo.com/video/233134524" width="700" height="460" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

Los problemas que Pipenv busca resolver son multifacéticos

- No necesitas usar más ``pip`` y ``virtualenv`` separados. Trabajan juntos.
- Manejar un archivo ``requirements.txt`` `puede ser problemático <https://www.kennethreitz.org/essays/a-better-pip-workflow>`_, por eso Pipenv usa en su lugar ``Pipfile`` y ``Pipfile.lock``, que son superiores para usos básicos
- Los Hashes se usan en todas partes, siempre. Seguridad. Automáticamente expone vulnerabilidades de seguridad.
- Te da una vista de tu árbol de dependecias (e.g. ``$ pipenv graph``).
- Coordina el  flujo de desarrollo cargando archivos ``.env``.

¡Instala hoy Pipenv!
---------------------

Si estas en MacOS, puedes instalar Pipenv fácilmente con Homebrew::

    $ brew install pipenv

O, si estás usando Ubuntu 17.10::

    $ sudo apt install software-properties-common python-software-properties
    $ sudo add-apt-repository ppa:pypa/ppa
    $ sudo apt update
    $ sudo apt install pipenv

De lo contrario, solo usa pip::

    $ pip install pipenv

✨🍰✨

.. toctree::
   :maxdepth: 2

   install

Testimonios de Usuarios
-----------------

**Jannis Leidel**, former pip maintainer—
    *Pipenv is the porcelain I always wanted to build for pip. It fits my brain and mostly replaces virtualenvwrapper and manual pip calls for me. Use it.*

**David Gang**—
    *This package manager is really awesome. For the first time I know exactly what my dependencies are which I installed and what the transitive dependencies are. Combined with the fact that installs are deterministic, makes this package manager first class, like cargo*.

**Justin Myles Holmes**—
    *Pipenv is finally an abstraction meant to engage the mind instead of merely the filesystem.*

☤ Características de Pipenv
-----------------

- Habilita verdaderos *builds deterministas*, mientras fácilmente especificas *solo lo que quieres*.
- Genera y verifica hashes en los archivos para bloquear dependencias.
- Automáticamente instala la versión de Python, si ``pyenv`` esta disponible
- Automáticamente busca tu proyecto home, recursivamente, buscando por un ``Pipfile``
- Automáticamente genera un ``Pipfile``, si no existe
- Automáticamente crea un entorno virtual en una locación estándar
- Automáticamente agrega/remueve paquetes a un ``Pipfile`` cuando se instala/desinstala
- Automáticamente carga archivos ``.env``, si estos existen.

Los comandos principales son ``install``, ``uninstall`` and ``lock``, el cual genera un ``Pipfile.lock``. Estos tienen la intención de reemplazar el uso de ``$ pip install``, así como manejar manualmente un entorno virtual (para activar uno, corre ``$ pipenv shell``).

Conceptos Básicos
//////////////

- Un entorno virtual se creará automáticamente, cuando no exista.
- Cuando no se pasen parámetros a ``install``, todos los paquetes ``[packages]`` especificados se instalarán.
- Para iniciar un entorno virtual con Python 3, corre ``$ pipenv --three``. 
- Para iniciar un entorno virtual con Python 2, corre ``$ pipenv --two``. 
- De lo contrario, cualquier entorno virtual será por defecto.

Otros Comandos
//////////////

- ``graph`` va a imprimir un bonito árbol de todas tus dependencias instaladas.
- ``shell`` generará un shell con el entorno virtual activado.
- ``run`` va a correr el comando dado desde el entorno virtual, con algún argumento adelante (e.g. ``$ pipenv run python`` o ``$ pipenv run pip freeze``)
- ``check`` asegura que los requerimientos en PEP 508 se están cumpliendo en el entorno actual.

Más guías de documentación
----------------------------

.. toctree::
   :maxdepth: 2

   básico
   avanzado
   diagnostico

☤ Uso de Pipenv
--------------

.. click:: pipenv:cli
   :prog: pipenv
   :show-nested:

Indices y tablas
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
