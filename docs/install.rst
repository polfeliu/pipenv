.. _virtualenvironments-ref:

Pipenv & Entorno Virtuales
=============================

.. image:: https://farm3.staticflickr.com/2943/33485660921_dfc0494739_k_d.jpg

Este tutorial te guiará por la instalación y el uso de paquetes de Python.

Te mostrará como instalar y usar las herramientas necesarias y hacer fuertes 
recomendaciones en las buenas prácticas. Ten en cuenta que Python es usado 
para un gran cantidad de propósitos diferentes, y precisamente como manejes tus
dependecias puede cambiar basado en como decidas publicar tu sofware. La guía
presentada aquí es aplicable directamente al desarrollo y despliegue de servicios
en red(incluyendo aplicaciones web), pero también es adecuado para el manejo de
entornos de desarrollo y pruebas para cualquier tipo de proyecto.

.. Note:: Esta guía es escrita para Python 3, sin embargo, estas instrucciones
          deberían funcionar bien en Python 2.7 - Si lo sigues usando, por alguna razón.

☤  Asegurate de tener Python & pip
------------------------------------

Antes de continuar, asegurate de que tienes Python y que esta disponible en tu línea 
de comandos. Puedes verificar esto ejecutando:

.. code-block:: bash

    $ python --version

Deberias tener un output como ``3.6.2``. Si no tienes Python, por favor instala
la última versión 3.x desde `python.org`_ o mira la sección
`Installing Python`_ de *The Hitchhiker's Guide to Python*.

.. Note:: Si eres nuevo y obtienes un error como este:

    .. code-block:: python

        >>> python
        Traceback (most recent call last):
          File "<stdin>", line 1, in <module>
        NameError: name 'python' is not defined

    Es porque este comando tiene la intención de correr en un *shell* (tambien llamado
    *termial* o *consola*). Mira Python for Beginners `tutorial para empezar`_ para
    una introducción de el shell de tu sistema operativo e interactuar con Python.

Adicionalmente, necesitas asegurarte que tienes :ref:`pip` disponible. Puedes verificar
esto ejecutando:

.. code-block:: bash

    $ pip --version
    pip 9.0.1

Si tienes instalado Python desde su fuente, con un instalador de `python.org`_, o via `Homebrew`_ deberias ya tener pip.
Si estas en Linux e instalaste a traves de tu manejador de paquetes, 
tal vez necesites `instalar pip <https://pip.pypa.io/en/stable/installing/>`_ por separado.

Si tu plan es instalar pipenv usando Hombrew puedes saltarte este paso. El 
instalador de Homebrew se encarga de pip por ti.

.. _getting started tutorial: https://opentechschool.github.io/python-beginners/en/getting_started.html#what-is-python-exactly
.. _python.org: https://python.org
.. _Homebrew: https://brew.sh
.. _Installing Python: http://docs.python-guide.org/en/latest/starting/installation/


☤ Instalando Pipenv
-------------------

:ref:`Pipenv` es un manejador de dependencias para los proyectos de Python. Si estas familiarizado
con Node.js' `npm`_ o Ruby `bundler`_, es similar en espíritu a estas herramientas.
Mientras :ref:`pip` puede instalar paquetes de Python, Pipenv es recomendado como herramienta
de nivel superior que simplifica el manejo de dependencias para casos comunes.

Use ``pip`` to install Pipenv:

.. code-block:: python

    $ pip install --user pipenv


.. Note:: This does a `user installation`_ to prevent breaking any system-wide
    packages. If ``pipenv`` isn't available in your shell after installation,
    you'll need to add the `user base`_'s binary directory to your ``PATH``.

    On Linux and macOS you can find the user base binary directory by running
    ``python -m site --user-base`` and adding ``bin`` to the end. For example,
    this will typically print ``~/.local`` (with ``~`` expanded to the
    absolute path to your home directory) so you'll need to add
    ``~/.local/bin`` to your ``PATH``. You can set your ``PATH`` permanently by
    `modifying ~/.profile`_.

    On Windows you can find the user base binary directory by running
    ``py -m site --user-site`` and replacing ``site-packages`` with
    ``Scripts``. For example, this could return
    ``C:\Users\Username\AppData\Roaming\Python36\site-packages`` so you would
    need to set your ``PATH`` to include
    ``C:\Users\Username\AppData\Roaming\Python36\Scripts``. You can set your
    user ``PATH`` permanently in the `Control Panel`_. You may need to log
    out for the ``PATH`` changes to take effect.

.. _npm: https://www.npmjs.com/
.. _bundler: http://bundler.io/
.. _user base: https://docs.python.org/3/library/site.html#site.USER_BASE
.. _user installation: https://pip.pypa.io/en/stable/user_guide/#user-installs
.. _modifying ~/.profile: https://stackoverflow.com/a/14638025
.. _Control Panel: https://msdn.microsoft.com/en-us/library/windows/desktop/bb776899(v=vs.85).aspx

☤ Installing packages for your project
--------------------------------------

Pipenv manages dependencies on a per-project basis. To install packages,
change into your project's directory (or just an empty directory for this
tutorial) and run:

.. code-block:: bash

    $ cd myproject
    $ pipenv install requests

Pipenv will install the excellent `Requests`_ library and create a ``Pipfile``
for you in your project's directory. The :ref:`Pipfile` is used to track which
dependencies your project needs in case you need to re-install them, such as
when you share your project with others. You should get output similar to this
(although the exact paths shown will vary):

.. code-block:: text

    Creating a Pipfile for this project...
    Creating a virtualenv for this project...
    Using base prefix '/usr/local/Cellar/python3/3.6.2/Frameworks/Python.framework/Versions/3.6'
    New python executable in ~/.local/share/virtualenvs/tmp-agwWamBd/bin/python3.6
    Also creating executable in ~/.local/share/virtualenvs/tmp-agwWamBd/bin/python
    Installing setuptools, pip, wheel...done.

    Virtualenv location: ~/.local/share/virtualenvs/tmp-agwWamBd
    Installing requests...
    Collecting requests
      Using cached requests-2.18.4-py2.py3-none-any.whl
    Collecting idna<2.7,>=2.5 (from requests)
      Using cached idna-2.6-py2.py3-none-any.whl
    Collecting urllib3<1.23,>=1.21.1 (from requests)
      Using cached urllib3-1.22-py2.py3-none-any.whl
    Collecting chardet<3.1.0,>=3.0.2 (from requests)
      Using cached chardet-3.0.4-py2.py3-none-any.whl
    Collecting certifi>=2017.4.17 (from requests)
      Using cached certifi-2017.7.27.1-py2.py3-none-any.whl
    Installing collected packages: idna, urllib3, chardet, certifi, requests
    Successfully installed certifi-2017.7.27.1 chardet-3.0.4 idna-2.6 requests-2.18.4 urllib3-1.22

    Adding requests to Pipfile's [packages]...
    P.S. You have excellent taste! ✨ 🍰 ✨

.. _Requests: https://python-requests.org


☤ Using installed packages
--------------------------

Now that Requests is installed you can create a simple ``main.py`` file to
use it:

.. code-block:: python

    import requests

    response = requests.get('https://httpbin.org/ip')

    print('Your IP is {0}'.format(response.json()['origin']))

Then you can run this script using ``pipenv run``:

.. code-block:: bash

    $ pipenv run python main.py

You should get output similar to this:

.. code-block:: text

    Your IP is 8.8.8.8

Using ``$ pipenv run`` ensures that your installed packages are available to
your script. It's also possible to spawn a new shell that ensures all commands
have access to your installed packages with ``$ pipenv shell``.


☤ Next steps
------------

Congratulations, you now know how to install and use Python packages! ✨ 🍰 ✨

.. _proper_installation:


☤ Homebrew Installation of Pipenv
=================================

Homebrew is a popular open-source package management system for macOS.

Installing pipenv via Homebrew will keep pipenv and all of its dependencies in
an isolated virtual environment so it doesn't interfere with the rest of your
Python installation.

Once you have installed `Homebrew <https://brew.sh/>`_ simply run::

    $ brew install pipenv

To upgrade pipenv at any time::

    $ brew upgrade pipenv

.. _pragmatic_installation:

☤ Pragmatic Installation of Pipenv
==================================

If you have a working installation of pip, and maintain certain "toolchain" type Python modules as global utilities in your user environment, pip `user installs <https://pip.pypa.io/en/stable/user_guide/#user-installs>`_ allow for installation into your home directory. Note that due to interaction between dependencies, you should limit tools installed in this way to basic building blocks for a Python workflow like virtualenv, pipenv, tox, and similar software.

To install::

    $ pip install --user pipenv

For more information see the `user installs documentation <https://pip.pypa.io/en/stable/user_guide/#user-installs>`_, but to add the installed cli tools from a pip user install to your path, add the output of::

    $ python -c "import site; import os; print(os.path.join(site.USER_BASE, 'bin'))"

To upgrade pipenv at any time::

    $ pip install --user --upgrade pipenv

.. _crude_installation:

☤ Crude Installation of Pipenv
==============================

If you don't even have pip installed, you can use this crude installation method, which will bootstrap your whole system::

    $ curl https://raw.githubusercontent.com/kennethreitz/pipenv/master/get-pipenv.py | python

Congratulations, you now have pip and Pipenv installed!

