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

Si te gusta sobreescribir las urls por defecto de PyPI con la url de un PyPI mirror, puedes hacer lo siguiente::

    $ pipenv install --pypi-mirror <mirror_url>

    $ pipenv update --pypi-mirror <mirror_url>

    $ pipenv sync --pypi-mirror <mirror_url>

    $ pipenv lock --pypi-mirror <mirror_url>

    $ pipenv uninstall --pypi-mirror <mirror_url>

Alternativamente, puedes setear la variable de entorno ``PIPENV_PYPI_MIRROR``.

☤ Inyectando credenciales en Pipfiles con variables de entorno
-----------------------------------------------------------------

Pipenv leerá las variables de entorno(si estan definidas) en tu Pipfile. Muy útil
si necesitas autenticarte a un PyPI privado::

    [[source]]
    url = "https://$USERNAME:${PASSWORD}@mypypi.example.com/simple"
    verify_ssl = true
    name = "pypi"

Por suerte - pipenv hasheará tu Pipfile *antes* de leer tus variables de entorno
(y, amablemente, sustituirá las variables de entorno de nuevo cuando instales desde un archivo lock
- asi no hay necesidad de hacer nada secreto! Woo!)

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

Para usar Pipenv con distribuciones Python de terceros (e.j. Anaconde), puedes proporcionar la ruta al binario de Python::

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

Pipenv incluye el paquete `safety <https://github.com/pyupio/safety>`_, y lo usará para escanear tu arbol de dependencias
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
    CVE-2017-7233: Open redirect and possible XSS attack via user-supplied numeric redirect URLs
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

Ahi un rango de plugins y extensiones mantenidos por la comunidad disponibles para un numero de editores e IDEs, asi como
diferentes productos los cuales integraron Pipenv en sus proyectos:

- `Heroku <https://heroku.com/python>`_ (Cloud Hosting)
- `Platform.sh <https://platform.sh/hosting/python>`_ (Cloud Hosting)
- `PyUp <https://pyup.io>`_ (Security Notification)
- `Emacs <https://github.com/pwalsh/pipenv.el>`_ (Editor Integration)
- `Fish Shell <https://github.com/fisherman/pipenv>`_ (Automatic ``$ pipenv shell``!)
- `VS Code <https://code.visualstudio.com/docs/python/environments>`_ (Editor Integration)

Trabajjo en progreso:

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

Si tienes `pyenv <https://github.com/pyenv/pyenv#simple-python-version-management-pyenv>`_ instalado y configurado, Pipenv automaticamente te preguntará si quieres instalar la versión requerida de Python si no la tienes disponible.

Esto es una caracteristca muy elegante, y estamos orgullos de ella::

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

Pipenv viene con muchas opciones que pueden ser habilitadas via variables de entorno 
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

    - ``PIPENV_PIPFILE`` — Cuando ejecutes pipenv desde un $PWD diferente a donde se encuentra el Pipfile, indicale a Pipenv donde encontrar el Pipfile de manera especifica con esta variable de entorno.

    - ``PIPENV_CACHE_DIR`` — Localización para Pipenv guardar el cache de los paquetes.

    - ``PIPENV_HIDE_EMOJIS`` — Desactiva los emojis en output.

    - ``PIPENV_DOTENV_LOCATION`` — Localización para Pipenv para cargar tus .env del proyecto.

    - ``PIPENV_DONT_LOAD_ENV`` — Le dice a Pipenv no cargar los archivos .env automáticamente.

Si te gusta setear estas variables de entorno por cada proyecto. Recomiendo usar el fantástico proyecto `direnv <https://direnv.net>`_.

También nota que `el mismo pip soporta variables de entorno <https://pip.pypa.io/en/stable/user_guide/#environment-variables>`_, si necesitas personalización adicional.

Por ejemplo::

    $ PIP_INSTALL_OPTION="-- -DCMAKE_BUILD_TYPE=Release" pipenv install -e .


☤ Custom Virtual Environment Location
-------------------------------------

Pipenv's underlying ``pew`` dependency will automatically honor the ``WORKON_HOME`` environment
variable, if you have it set — so you can tell pipenv to store your virtual environments wherever you want, e.g.::

    export WORKON_HOME=~/.venvs

In addition, you can also have Pipenv stick the virtualenv in ``project/.venv`` by setting the ``PIPENV_VENV_IN_PROJECT`` environment variable.


☤ Testing Projects
------------------

Pipenv is being used in projects like `Requests`_ for declaring development dependencies and running the test suite.

We've currently tested deployments with both `Travis-CI`_ and `tox`_ with success.

Travis CI
/////////

An example Travis CI setup can be found in `Requests`_. The project uses a Makefile to
define common functions such as its ``init`` and ``tests`` commands. Here is
a stripped down example ``.travis.yml``::

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

and the corresponding Makefile::

    init:
        pip install pipenv
        pipenv install --dev

    test:
        pipenv run py.test tests


Tox Automation Project
//////////////////////

Alternatively, you can configure a ``tox.ini`` like the one below for both local
and external testing::

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

Pipenv will automatically use the virtualenv provided by ``tox``. If ``pipenv install --dev`` installs e.g. ``pytest``, then installed command ``py.test`` will be present in given virtualenv and can be called directly by ``py.test tests`` instead of ``pipenv run py.test tests``.

You might also want to add ``--ignore-pipfile`` to ``pipenv install``, as to
not accidentally modify the lock-file on each test run. This causes Pipenv
to ignore changes to the ``Pipfile`` and (more importantly) prevents it from
adding the current environment to ``Pipfile.lock``. This might be important as
the current environment (i.e. the virtualenv provisioned by tox) will usually
contain the current project (which may or may not be desired) and additional
dependencies from ``tox``'s ``deps`` directive. The initial provisioning may
alternatively be disabled by adding ``skip_install = True`` to tox.ini.

This method requires you to be explicit about updating the lock-file, which is
probably a good idea in any case.

A 3rd party plugin, `tox-pipenv`_ is also available to use Pipenv natively with tox.

.. _Requests: https://github.com/kennethreitz/requests
.. _tox: https://tox.readthedocs.io/en/latest/
.. _tox-pipenv: https://tox-pipenv.readthedocs.io/en/latest/
.. _Travis-CI: https://travis-ci.org/

☤ Shell Completion
------------------

To enable completion in fish, add this to your config::

    eval (pipenv --completion)

Alternatively, with bash or zsh, add this to your config::

    eval "$(pipenv --completion)"

Magic shell completions are now enabled!

✨🍰✨

☤ Working with Platform-Provided Python Components
--------------------------------------------------

It's reasonably common for platform specific Python bindings for
operating system interfaces to only be available through the system
package manager, and hence unavailable for installation into virtual
environments with `pip`. In these cases, the virtual environment can
be created with access to the system `site-packages` directory::

    $ pipenv --three --site-packages

To ensure that all `pip`-installable components actually are installed
into the virtual environment and system packages are only used for
interfaces that don't participate in Python-level dependency resolution
at all, use the `PIP_IGNORE_INSTALLED` setting::

    $ PIP_IGNORE_INSTALLED=1 pipenv install --dev


.. _pipfile-vs-setuppy:

☤ Pipfile vs setup.py
---------------------

There is a subtle but very important distinction to be made between **applications** and **libraries**. This is a very common source of confusion in the Python community.

Libraries provide reusable functionality to other libraries and applications (let's use the umbrella term **projects** here). They are required to work alongside other libraries, all with their own set of subdependencies. They define **abstract dependencies**. To avoid version conflicts in subdependencies of different libraries within a project, libraries should never ever pin dependency versions. Although they may specify lower or (less frequently) upper bounds, if they rely on some specific feature/fix/bug. Library dependencies are specified via ``install_requires`` in ``setup.py``.

Libraries are ultimately meant to be used in some **application**. Applications are different in that they usually are not depended on by other projects. They are meant to be deployed into some specific environment and only then should the exact versions of all their dependencies and subdependencies be made concrete. To make this process easier is currently the main goal of Pipenv.

To summarize:

- For libraries, define **abstract dependencies** via ``install_requires`` in ``setup.py``. The decision of which version exactly to be installed and where to obtain that dependency is not yours to make!
- For applications, define **dependencies and where to get them** in the `Pipfile` and use this file to update the set of **concrete dependencies** in ``Pipfile.lock``. This file defines a specific idempotent environment that is known to work for your project. The ``Pipfile.lock`` is your source of truth. The ``Pipfile`` is a convenience for you to create that lock-file, in that it allows you to still remain somewhat vague about the exact version of a dependency to be used. Pipenv is there to help you define a working conflict-free set of specific dependency-versions, which would otherwise be a very tedious task.
- Of course, ``Pipfile`` and Pipenv are still useful for library developers, as they can be used to define a development or test environment.
- And, of course, there are projects for which the distinction between library and application isn't that clear. In that case, use ``install_requires`` alongside Pipenv and ``Pipfile``.

You can also do this::

    $ pipenv install -e .

This will tell Pipenv to lock all your ``setup.py``–declared dependencies.

☤ Changing Pipenv's Cache Location
----------------------------------

You can force Pipenv to use a different cache location by setting the environment variable ``PIPENV_CACHE_DIR`` to the location you wish. This is useful in the same situations that you would change ``PIP_CACHE_DIR`` to a different directory.

☤ Changing Where Pipenv Stores Virtualenvs
------------------------------------------

By default, Pipenv stores all of your virtualenvs in a single place.  Usually this isn't a problem, but if you'd like to change it for developer ergonomics, or if it's causing issues on build servers you can set ``PIPENV_VENV_IN_PROJECT`` to create the virtualenv inside the root of your project.

☤ Changing Default Python Versions
----------------------------------

By default, Pipenv will initialize a project using whatever version of python the python3 is. Besides starting a project with the ``--three`` or ``--two`` flags, you can also use ``PIPENV_DEFAULT_PYTHON_VERSION`` to specify what version to use when starting a project when ``--three`` or ``--two`` aren't used.
