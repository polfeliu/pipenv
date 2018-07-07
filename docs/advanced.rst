.. _advanced:

Uso avanzado de Pipenv
========================

.. image:: https://farm4.staticflickr.com/3672/33231486560_bff4124c9a_k_d.jpg

Este documento cubre alguna de las características más avanzadas y magnificas de Pipenv.

☤ Advertencias
---------

- Una rueda de dependencias proporcionadas en un ``Pipfile`` no serán capturadas por ``$ pipenv lock``.
- Hay algunos issues conocidos por usar indices privados, relacionados a hashing. Estamos activamente trabajado en solucionar estos problemas. Sin embargo, puedes tener mucha suerte con estos.
- Las instalaciones tienen la intención de ser lo más deterministas posibles - usa la bandera ``--sequential`` para incrementar esto, si experimentas algún error

☤ Especificando indice de paquete
----------------------------

Si te gusta un paquete especifico para ser instalado por un determinado indice de paquete, puedes hacer lo siguiente::

    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true
    name = "pypi"

    [[source]]
    url = "http://pypi.home.kennethreitz.org/simple"
    verify_ssl = false
    name = "home"

    [dev-packages]

    [packages]
    requests = {version="*", index="home"}
    maya = {version="*", index="pypi"}
    records = "*"

Muy elegante.

☤ Usando un PyPI Mirror
----------------------------

Si te gusta sobrescribir las urls por defecto de PyPI con la url de un PyPI mirror, puedes hacer lo siguiente::

    $ pipenv install --pypi-mirror <mirror_url>

    $ pipenv update --pypi-mirror <mirror_url>

    $ pipenv sync --pypi-mirror <mirror_url>

    $ pipenv lock --pypi-mirror <mirror_url>

    $ pipenv uninstall --pypi-mirror <mirror_url>

Alternativamente, puedes setear la variable de entorno ``PIPENV_PYPI_MIRROR``.

☤ Inyectando credenciales en Pipfiles con variables de entorno
-----------------------------------------------------------------

Pipenv leerá las variables de entorno(si están definidas) en tu Pipfile. Muy útil
si necesitas autenticarte a un PyPI privado::

    [[source]]
    url = "https://$USERNAME:${PASSWORD}@mypypi.example.com/simple"
    verify_ssl = true
    name = "pypi"

Por suerte - pipenv hasheará tu Pipfile *antes* de leer tus variables de entorno
(y, amablemente, sustituirá las variables de entorno de nuevo cuando instales desde un archivo lock
- así no hay necesidad de hacer nada secreto! Woo!)

☤ Especificando básicamente cualquier cosa
-------------------------------

Si te gusta especificar que un paquete especifico solo sea instalado en ciertos sistemas, 
puedes usar `especificadores PEP 508 <https://www.python.org/dev/peps/pep-0508/>`_ para lograr esto.

Aquí tienes un ``Pipfile`` de ejemplo, el cual solo instalará ``pywinusb`` en sistemas Windows::

    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true
    name = "pypi"

    [packages]
    requests = "*"
    pywinusb = {version = "*", sys_platform = "== 'win32'"}

Voilà!

Aquí tienes un ejemplo más complejo::

    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true

    [packages]
    unittest2 = {version = ">=1.0,<3.0", markers="python_version < '2.7.9' or (python_version >= '3.0' and python_version < '3.4')"}

Magia. Magia pura sin adulterar.

☤ Desplegando Dependencias de Sistema
-------------------------------

Puedes decirle a Pipenv para instalar el contenido de un Pipfile en su sistema padre con la bandera ``--system``::

    $ pipenv install --system

Esto es útil para contenedores Docker, e infraestructuras de despliegue (e.j. Heroku hace esto).

También útil para despliegue es la bandera ``--deploy``::

    $ pipenv install --system --deploy

Esto fallará en construcción si el ``Pipfile.lock`` esta desactualizado, en su lugar generará uno nuevo.

☤ Pipenv y otras distribuciones
---------------------------------------

Para usar Pipenv con distribuciones Python de terceros (e.j. Anaconda), puedes proporcionar la ruta al binario de Python::

    $ pipenv install --python=/path/to/python

Anaconda usa Conda para manejar paquetes. Para reusar paquetes instalados con Conda, usa la bandera ``--site-packages``::

    $ pipenv --python=/path/to/python --site-packages

☤ Generando un ``requirements.txt``
-----------------------------------

Puedes convertir un ``Pipfile`` y ``Pipfile.lock`` en un archivo ``requirements.txt`` muy fácil, y tener todos los beneficios extras y otras buenas cosas que incluimos.  

Echemosle un vistazo a este ``Pipfile``::

    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true

    [packages]
    requests = {version="*"}

Y genera un ``requirements.txt`` de eso::

    $ pipenv lock -r
    chardet==3.0.4
    requests==2.18.4
    certifi==2017.7.27.1
    idna==2.6
    urllib3==1.22

Si deseas generar un ``requirements.txt`` con solo requerimientos de desarrollo puedes hacerlo también! Tomemos el siguiente ``Pipfile``::

    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true

    [dev-packages]
    pytest = {version="*"}

Y generara un ``requirements.txt`` de eso::

    $ pipenv lock -r --dev
    py==1.4.34
    pytest==3.2.3

Muy elegante.

☤ Detectando vulnerabilidades de seguridad
---------------------------------------

Pipenv incluye el paquete `safety <https://github.com/pyupio/safety>`_, y lo usará para escanear tu árbol de dependencias
para conocidas vulnerabilidades!

Ejemplo::

    $ cat Pipfile
    [packages]
    django = "==1.10.1"

    $ pipenv check
    Checking PEP 508 requirements…
    Passed!
    Checking installed package safety…

    33075: django >=1.10,<1.10.3 resolved (1.10.1 installed)!
    Django before 1.8.x before 1.8.16, 1.9.x before 1.9.11, and 1.10.x before 1.10.3, when settings.DEBUG is True, allow remote attackers to conduct DNS rebinding attacks by leveraging failure to validate the HTTP Host header against settings.ALLOWED_HOSTS.

    33076: django >=1.10,<1.10.3 resolved (1.10.1 installed)!
    Django 1.8.x before 1.8.16, 1.9.x before 1.9.11, and 1.10.x before 1.10.3 use a hardcoded password for a temporary database user created when running tests with an Oracle database, which makes it easier for remote attackers to obtain access to the database server by leveraging failure to manually specify a password in the database settings TEST dictionary.

    33300: django >=1.10,<1.10.7 resolved (1.10.1 installed)!
    CVE-2017-7233: Open redirect and possible XSS attack vía user-supplied numeric redirect URLs
    ============================================================================================

    Django relies on user input in some cases  (e.g.
    :func:`django.contrib.auth.views.login` and :doc:`i18n </topics/i18n/index>`)
    to redirect the user to an "on success" URL. The security check for these
    redirects (namely ``django.utils.http.is_safe_url()``) considered some numeric
    URLs (e.g. ``http:999999999``) "safe" when they shouldn't be.

    Also, if a developer relies on ``is_safe_url()`` to provide safe redirect
    targets and puts such a URL into a link, they could suffer from an XSS attack.

    CVE-2017-7234: Open redirect vulnerability in ``django.views.static.serve()``
    =============================================================================

    A maliciously crafted URL to a Django site using the
    :func:`~django.views.static.serve` view could redirect to any other domain. The
    view no longer does any redirects as they don't provide any known, useful
    functionality.

    Note, however, that this view has always carried a warning that it is not
    hardened for production use and should be used only as a development aid.

✨🍰✨

.. note::
   
   In order to enable this functionality while maintaining its permissive
   copyright license, `pipenv` embeds an API client key for the backend
   Safety API operated by pyup.io rather than including a full copy of the
   CC-BY-NC-SA licensed Safety-DB database. This embedded client key is
   shared across all `pipenv check` users, and hence will be subject to
   API access throttling based on overall usage rather than individual
   client usage.


☤ Integraciones de Comunidad
------------------------

Hay un rango de plugins y extensiones mantenidos por la comunidad disponibles para un numero de editores e IDEs, así como
diferentes productos los cuales integraron Pipenv en sus proyectos:

- `Heroku <https://heroku.com/python>`_ (Cloud Hosting)
- `Platform.sh <https://platform.sh/hosting/python>`_ (Cloud Hosting)
- `PyUp <https://pyup.io>`_ (Security Notification)
- `Emacs <https://github.com/pwalsh/pipenv.el>`_ (Editor Integration)
- `Fish Shell <https://github.com/fisherman/pipenv>`_ (Automatic ``$ pipenv shell``!)
- `VS Code <https://code.visualstudio.com/docs/python/environments>`_ (Editor Integration)

Trabajo en progreso:

- `Sublime Text <https://github.com/kennethreitz/pipenv-sublime>`_ (Editor Integration)
- `PyCharm <https://www.jetbrains.com/pycharm/download/>`_ (Editor Integration)
- Mysterious upcoming Google Cloud product (Cloud Hosting)



☤ Abriendo un módulo en tu editor
------------------------------

Pipenv te permite abrir cualquier módulo de Python que este instalado (incluyendo uno en tu código base), con el comando ``$ pipenv open``::

    $ pipenv install -e git+https://github.com/kennethreitz/background.git#egg=background
    Installing -e git+https://github.com/kennethreitz/background.git#egg=background…
    ...
    Updated Pipfile.lock!

    $ pipenv open background
    Opening '/Users/kennethreitz/.local/share/virtualenvs/hmm-mGOawwm_/src/background/background.py' in your EDITOR.

Esto te permite a ti leer el código que estas consumiendo, en lugar de buscarlo en GitHub.

.. note:: La variable de entorno estándar ``EDITOR`` es usada para esto. Si estas usando VS Code, por ejemplo, querrás hacer  ``export EDITOR=code`` (Si estas en macOS vas a querer `instalar el comando <https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line>`_ en tu ``PATH`` primero).

☤ Instalaciones automáticas de Python
-------------------------------

Si tienes `pyenv <https://github.com/pyenv/pyenv#simple-python-version-management-pyenv>`_ instalado y configurado, Pipenv automáticamente te preguntará si quieres instalar la versión requerida de Python si no la tienes disponible.

Esto es una característica muy elegante, y estamos orgullos de ella::

    $ cat Pipfile
    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true

    [dev-packages]

    [packages]
    requests = "*"

    [requires]
    python_version = "3.6"

    $ pipenv install
    Warning: Python 3.6 was not found on your system…
    Would you like us to install latest CPython 3.6 with pyenv? [Y/n]: y
    Installing CPython 3.6.2 with pyenv (this may take a few minutes)…
    ...
    Making Python installation global…
    Creating a virtualenv for this project…
    Using /Users/kennethreitz/.pyenv/shims/python3 to create virtualenv…
    ...
    No package provided, installing all dependencies.
    ...
    Installing dependencies from Pipfile.lock…
    🐍   ❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒❒ 5/5 — 00:00:03
    To activate this project's virtualenv, run the following:
     $ pipenv shell

Pipenv automáticamente honra tanto el ``python_full_version`` y `python_version`` `PEP 508 <https://www.python.org/dev/peps/pep-0508/>`_ especificadores.

💫✨🍰✨💫

☤ Carga automática de ``.env``
-------------------------------

Si un archivo ``.env`` esta presente en tu proyecto, ``$ pipenv shell`` y ``$ pipenv run`` automáticamente las cargará para ti

    $ cat .env
    HELLO=WORLD⏎

    $ pipenv run python
    Loading .env environment variables…
    Python 2.7.13 (default, Jul 18 2017, 09:17:00)
    [GCC 4.2.1 Compatible Apple LLVM 8.1.0 (clang-802.0.42)] on darwin
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import os
    >>> os.environ['HELLO']
    'WORLD'

Esto es muy útil para mantener las credenciales de producción fuera de tu código base.
¡No recomendamos publicar archivos ``.env`` en el control de versiones de tu código fuente!

Si tu archivo ``.env`` esta localizado en una ruta diferente o tiene un nombre diferente, puedes setear la variable de entorno ``PIPENV_DOTENV_LOCATION``::

    $ PIPENV_DOTENV_LOCATION=/path/to/.env pipenv shell

Para prevenir que pipenv cargue el archivo ``.env``, setea la variable de entorno ``PIPENV_DONT_LOAD_ENV``::

    $ PIPENV_DONT_LOAD_ENV=1 pipenv shell

☤ Atajos personalizados para Scripts
-------------------------

Pipenv soporta atajos personalizados en la sección ``scripts``. ``pipenv run`` automáticamente los cargará y encontrará el comando correcto para reemplazarlo. Dado el ``Pipfile``::

    [scripts]
    printfoo = "python -c \"print('foo')\""

Puedes escribir en la terminal para ejecutar::

    $ pipenv run printfoo
    foo

☤ Soporte para Variables de Entorno
-----------------------------------

Pipenv soporta el uso de valores en variables de entorno. Por ejemplo::

    [[source]]
    url = "https://${PYPI_USERNAME}:${PYPI_PASSWORD}@my_private_repo.example.com/simple"
    verify_ssl = true
    name = "pypi"

    [dev-packages]

    [packages]
    requests = {version="*", index="home"}
    maya = {version="*", index="pypi"}
    records = "*"

Las variables de entorno pueden ser especificadas como ``${MY_ENVAR}`` o ``$MY_ENVAR``.
En Windows, ``%MY_ENVAR%`` también es soportada junto con ``${MY_ENVAR}`` o ``$MY_ENVAR``.


☤ Configuración con Variables de Entorno
------------------------------------------

Pipenv viene con muchas opciones que pueden ser habilitadas vía variables de entorno 
en shell. Para activarlas, simplemente crea las variables en tu shell y pipenv las 
detectará.

    - ``PIPENV_DEFAULT_PYTHON_VERSION`` — Usa esta versión de Python cuando crea un entorno virtual, por defecto (e.j. ``3.6``).

    - ``PIPENV_SHELL_FANCY`` — Siempre usa modo elegante cuando invocas ``pipenv shell``.

    - ``PIPENV_VENV_IN_PROJECT`` — Si esta seteada, usa ``.venv`` en tu carpeta de proyecto
      en lugar del manejador global ``pew``.

    - ``PIPENV_COLORBLIND`` — Desactiva los colores en la terminal, por alguna razón.

    - ``PIPENV_NOSPIN`` — Desactiva terminal spinner, para logs más limpios. Automáticamente seteado en un entorno CI.

    - ``PIPENV_MAX_DEPTH`` — Setea un entero para el número máximo de búsqueda recursivas para un Pipfile.

    - ``PIPENV_TIMEOUT`` — Setea un entero para el número máximo de segundos que Pipenv esperará
     para que la creación de un entorno virtual se complete.  Por defecto es 120 segundos.

    - ``PIPENV_INSTALL_TIMEOUT`` — Setea un entero para el número máximo de segundos que Pipenv esperará
     para la instalación de un paquete antes que se acabe el tiempo. Por defecto es 900 segundos.

    - ``PIPENV_IGNORE_VIRTUALENVS`` — Seteala para desactivar automáticamente usando un entorno virtual activado sobre el entorno virtual actual del proyecto.

    - ``PIPENV_PIPFILE`` — Cuando ejecutes pipenv desde un $PWD diferente a donde se encuentra el Pipfile, indícale a Pipenv donde encontrar el Pipfile de manera especifica con esta variable de entorno.

    - ``PIPENV_CACHE_DIR`` — Localización para Pipenv guardar el cache de los paquetes.

    - ``PIPENV_HIDE_EMOJIS`` — Desactiva los emojis en output.

    - ``PIPENV_DOTENV_LOCATION`` — Localización para Pipenv para cargar tus .env del proyecto.

    - ``PIPENV_DONT_LOAD_ENV`` — Le dice a Pipenv no cargar los archivos .env automáticamente.

Si te gusta setear estas variables de entorno por cada proyecto. Recomiendo usar el fantástico proyecto `direnv <https://direnv.net>`_.

También nota que `el mismo pip soporta variables de entorno <https://pip.pypa.io/en/stable/user_guide/#environment-variables>`_, si necesitas personalización adicional.

Por ejemplo::

    $ PIP_INSTALL_OPTION="-- -DCMAKE_BUILD_TYPE=Release" pipenv install -e .


☤ Localización Personalizada de Entorno Virtual
-------------------------------------

La dependencia ``pew`` de Pipenv automáticamente honrará la variable de entorno ``WORKON_HOME``,
si la tienes seteada - le puedes decir a pipenv para guardar tus entoros virtuales en cualquier lugar que quieras, e.g.::

    export WORKON_HOME=~/.venvs

Además, puedes tener a Pipenv para que mantenga un solo entorno virtual en ``project/.venv`` configurando la variable de entorno ``PIPENV_VENV_IN_PROJECT``.


☤ Testeando Proyectos.
------------------

Pipenv esta siendo usando en proyectos como `Requests`_ para declarar dependencias de desarrollo y ejecutar el conjunto de tests 

Actualmente hemos testeado despliegues de manera exitosa tanto con `Travis-CI`_ y con `tox`_.  

Travis CI
/////////

Una configuración de ejemplo con Travis CI puede ser encontrada en `Requests`_. El proyecto usa
un Makefile para definir las funciones comunes como lo son los comandos ``init`` y ``tests``. Este
es un ejemplo simplificado de un ``.travis.yml``::

    language: python
    python:
        - "2.6"
        - "2.7"
        - "3.3"
        - "3.4"
        - "3.5"
        - "3.6"
        - "3.7-dev"

    # command to install dependencies
    install: "make"

    # command to run tests
    script:
        - make test

Y el Makefile correspondiente::

    init:
        pip install pipenv
        pipenv install --dev

    test:
        pipenv run py.test tests


Automatización de Proyecto con Tox
//////////////////////

Alternativamente, puedes configurar un ``tox.ini`` como el siguiente tanto para local
y testeo externo::

    [tox]
    envlist = flake8-py3, py26, py27, py33, py34, py35, py36, pypy

    [testenv]
    deps = pipenv
    commands=
        pipenv install --dev
        pipenv run py.test tests

    [testenv:flake8-py3]
    basepython = python3.4
    commands=
        pipenv install --dev
        pipenv run flake8 --version
        pipenv run flake8 setup.py docs project test

Pipenv automáticamente usara el entorno virtual proporcionado por ``tox``. Si ``pipenv install --dev`` instala ``pytest``, entonces el comando ``py.test`` estará disponible en tu entorno virtual y puedes llamado directamente por ``py.test tests`` en vez de ``pipenv run py.test tests``.

Tal vez quieras agregar ``--ignore-pipfile`` a ``pipenv install``, para no
modificar accidentalmente el lock-file cada que corre el test. Esto causa que Pipenv
igonore los cambios al ``Pipfile`` y (más importante) previene de agregar el
actual entorno al ``Pipfile.lock``. Esto puede ser importante como el entorno
actual (e.j. el entorno proporcionado por tox) usualmente contendrá el proyecto
actual (lo cual puede ser o no lo deseado) y dependencias adicionales desde la
directiva ``deps`` de ``tox``. Lo proporcionado inicialmente puede ser desactivado
agregando ``skip_install = True`` al tox.ini.

Este método requiere que seas explicito acerca de actualizar el lock-file, lo cual
puede ser una buena idea en cualquier caso.

Un plugin de 3ros, `tox-pipenv`_ es también disponible para usa Pipenv nativamente con tox.

.. _Requests: https://github.com/kennethreitz/requests
.. _tox: https://tox.readthedocs.io/en/latest/
.. _tox-pipenv: https://tox-pipenv.readthedocs.io/en/latest/
.. _Travis-CI: https://travis-ci.org/

☤ Completado en Shell
------------------

Para activar el completado en fish, agrega esto a tu configuración::

    eval (pipenv --completion)

También, con bash o zsh, agrega esto a tu configuración::

    eval "$(pipenv --completion)"

Completado mágico en Shell ahora esta activado!

✨🍰✨

☤ Trabajando con componentes de Python provistos por la plataforma
--------------------------------------------------

Es muy común para enlaces de Python específicos de la plataforma
para interfaces del sistema operativo solo están disponibles a través
del manejador de paquetes del sistema, y por lo tanto no están disponibles
en entornos virtuales con `pip`. En estos casos, el entorno virtual
puede ser creado con acceso a la carpeta de `site-packages` del sistema::

    $ pipenv --three --site-packages

Para asegurar que todos los componentes instalables de `pip` realmente
sean instalados en el entorno vritual y los paquetes del sistema sean
solo usados por las interfaces que no participan en la resolución de
dependencias a nivel Python en absoluto, usa la configuración::

    $ PIP_IGNORE_INSTALLED=1 pipenv install --dev


.. _pipfile-vs-setuppy:

☤ Pipfile vs setup.py
---------------------


Hay una sutil pero muy importante diferencia para hacer entre **aplicaciones** y **librerías**. Esto es una fuente común de confusión en la comunidad de Python.

Las librerías proporcionan una funcionalidad recusable para otras librerías o aplicaciones (Usemos el término **proyectos**). Estas son requeridas para trabajar con otras librerías, todo con su propio set de subdependencias. Definen **dependencias abstractas**. Para evitar conflictos con versiones en subdependencias de diferentes librerías dentro de un proyecto, las librerías nunca deberían fijar versiones de dependencias. Aunque puede especificar menores o (menos frecuentes) limites superiores, si dependen de alguna característica/arreglo/bug. Las dependencias de librerías son especificadas vía ``install_requires`` en ``setup.py``.

Las librerías últimamente están destinadas a ser usadas en alguna **aplicación**. Las aplicaciones son diferentes en que usualmente no dependen de otros proyectos. Están destinadas a ser desplegadas en un entorno especifico y solo entonces deben usar las versiones exactas de todas sus dependencias y subdependencias. Hacer este proceso más sencillo es el objetivo principal de Pipenv.

Para resumir:

- Para librerías, define **dependencias abstractas** vía ``install_requires`` en ``setup.py``. La decisión de qué versión exacta debe ser instalada y donde obtener esa dependencia no es tuya!

- Para aplicaciones, define **dependencias y donde obtenerlas** en el `Pipfile` y usa este archivo para actualizar un conjunto de **dependencias concretas** en ``Pipfile.lock``. Este archivo define un entorno idempotent especifico que se sabe funciona para tu proyecto. El ``Pipfile.lock`` es tu fuente de confianza. El ``Pipfile`` es un conveniencia para ti para crear ese lock-file, ya que te permite permanecer algo impreciso acerca de la versión exacta de una dependencia para ser usada. Pipenv esta ahí para ayudarte a definir un conjunto de dependencias especificas de trabajo libres de conflicto, que de otra manera sería una muy tediosa tarea. 
- Por supuesto, ``Pipfile`` y Pipenv siguen siendo útiles para librerías de desarrollo, al poder ser usadas para definir un entorno de desarrollo o prueba.
- Y por supuesto, hay proyectos para los cuales la diferencia entre librería y aplicación no es tan clara. En ese caso, usa ``install_requires`` junto con Pipenv y ``Pipfile``.

También puedes hacer esto::

    $ pipenv install -e .

Esto le dirá a Pipenv para hacer lock a todas tus dependencias declaradas en tu ``setup.py``.

☤ Cambiando la locación de Cache de Pipenv
----------------------------------

Puedes forzar a Pipenv para usar diferentes locaciones de cache configurando la variable de entorno ``PIPENV_CACHE_DIR`` para la locación que quieres. Esto es útil en las mismas situaciones donde cambiarías a ``PIP_CACHE_DIR`` una carpeta diferente.

☤ Cambiando donde Pipenv guarda Entorno Virtuales
------------------------------------------

Por defecto, Pipenv guarda todos tus entorno virtuales en un solo lugar. Usualmente esto no es un problema, pero si te gustaría cambiarlo para comodidad de desarrollo, o si esta causando issues en servidores de construcción puedes setear ``PIPENV_VENV_IN_PROJECT`` para crear un entorno virtual dentro de la raíz de tu proyecto.

☤ Cambiando la versión por defecto de Python
----------------------------------

Por defecto,  Pipenv inicializará un proyecto usando cualquier versión de python que tenga python3. Además de iniciar un proyecto con las banderas ``--three`` o ``--two``, también puedes usar ``PIPENV_DEFAULT_PYTHON_VERSION`` para especificar cual versión usa cuando se inicie un proyecto y ``--three`` o ``--two`` no son usados.
