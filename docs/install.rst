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

Si tienes instalado Python desde su fuente, con un instalador de `python.org`_, o 
via `Homebrew`_ deberías ya tener pip. Si estas en Linux e instalaste a través de 
tu manejador de paquetes, tal vez necesites `instalar pip <https://pip.pypa.io/en/stable/installing/>`_ por separado.

Si tu plan es instalar pipenv usando Hombrew puedes saltarte este paso. El 
instalador de Homebrew se encarga de pip por ti.

.. _tutorial para empezar: https://opentechschool.github.io/python-beginners/en/getting_started.html#what-is-python-exactly
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


.. Note:: Esto hace una `instalación de usuario`_ para prevenir romper cualquier paquete
    de sistema. Si ``pipenv`` no esta disponible en tu shell después de la instalación, 
    vas a necesitar agregar la carpeta raíz de binarios del `usuario`_ a tu ``PATH``.

    En Linux y macOS puedes buscar la carpeta raíz de binarios del usuario base ejecutando
    ``python -m site --user-base`` y agregando ``bin`` al final. Por ejemplo, esto
    normalmente imprimirá ``~/.local`` (expandiendo con ``~`` con 
    la ruta absoluta a tu carpeta home) entonces necesitarás agregar 
    ``~/.local/bin`` a tu ``PATH``. Puedes setear tu ``PATH`` de manera permanente 
    `modificando ~/.profile`_.

    En Windows puedes encontrar la carpeta raíz de binarios ejecutando
    ``py -m site --user-site`` y reemplazando ``site-packages`` con 
    ``Scripts``. Por ejemplo, esto retornará
    ``C:\Users\Username\AppData\Roaming\Python36\site-packages`` entonces
    vas a necesitar setear tu ``PATH`` para incluir
    ``C:\Users\Username\AppData\Roaming\Python36\Scripts``. Puedes setear tu ``PATH``
    de manera permanente en el `Panel de Control`_. Puedes necesitar cerrar sesión 
    para que los cambios en ``PATH`` surtan efecto.

.. _npm: https://www.npmjs.com/
.. _bundler: http://bundler.io/
.. _usuario: https://docs.python.org/3/library/site.html#site.USER_BASE
.. _instalación de usuario: https://pip.pypa.io/en/stable/user_guide/#user-installs
.. _modificando ~/.profile: https://stackoverflow.com/a/14638025
.. _Panel de Control: https://msdn.microsoft.com/en-us/library/windows/desktop/bb776899(v=vs.85).aspx

☤ Instalando paquetes para tu proyecto
--------------------------------------

Pipenv maneja dependencias por proyecto. Para instalar paquetes,
cambiate a tu carpeta de proyecto (o solo una carpeta vacía para este 
tutorial) y ejecuta:

.. code-block:: bash

    $ cd myproject
    $ pipenv install requests

Pipenv instalará la excelente librería `Requests`_ y creará un ``Pipfile`` para tu
carpeta de proyecto. El :ref:`Pipfile` es usado para seguir cual dependencia de tu
proyecto necesitas en caso de que quieras reinstalarlas, como cuando comparte el 
proyecto con otros. Deberías obtener un output parecido a este (aunque la ruta
exacta variará):

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


☤ Usando paquetes instalados
--------------------------

Ahora que Requests esta instalado puedes crear un archivo ``main.py`` para
usarlo:

.. code-block:: python

    import requests

    response = requests.get('https://httpbin.org/ip')

    print('Your IP is {0}'.format(response.json()['origin']))

Entonces puedes ejecutar este script usando ``pipenv run``:

.. code-block:: bash

    $ pipenv run python main.py

Deberías tener una salida parecida a esta:

.. code-block:: text

    Your IP is 8.8.8.8

Usando ``$ pipenv run`` se asegura de que tu paquete instalado esta disponible 
para tu script. También es posible generar un nuevo shell que se asegura de que 
todos los comandos tienen acceso a tus paquetes instalados con ``$ pipenv shell``.

☤ Próximos pasos
------------

¡Felicitaciones, ahora sabes cómo instalar y usar paquetes de Python! ✨ 🍰 ✨

.. _proper_installation:


☤ Instalación de Pipenv con Homebrew
=================================

Homebrew es un manejador de paquetes de sistema popular y open-source para macOS

Instalando Pipenv via Homebrew va a mantener Pipenv y todas sus dependencias en un
entorno virtual aislado para que no interfiera con el resto de tus instalaciones de Python.

Una vez tengas instalado `Homebrew <https://brew.sh/>`_ solo ejecuta::

    $ brew install pipenv

Para actualizar pipenv en cualquier momento::

    $ brew upgrade pipenv

.. _pragmatic_installation:

☤ Instalación pragmática de Pipenv
==================================

Si tienes una instalación funcional de pip, mantiene cierto "toolchain" escribe los módulos de Python como utilidades globales en tu entorno de usuario, pip `instalación de usuario <https://pip.pypa.io/en/stable/user_guide/#user-installs>`_ permite instalaciones en tu carpeta home. Nota que debido a interacciones entre dependencias, deberías limitar las herramientas instaladas de esta manera para un flujo de trabajo con Python como virtualenv, pipenv, tox y software similares.

Para instalar::

    $ pip install --user pipenv

Para más información mira la documentación de `instalaciones de usuario <https://pip.pypa.io/en/stable/user_guide/#user-installs>`_, pero para agregar herramientas cli desde una instalación de usuario con pip a tu path, agrega el output de::

    $ python -c "import site; import os; print(os.path.join(site.USER_BASE, 'bin'))"

Para actualizar pipenv en cualquier momento::

    $ pip install --user --upgrade pipenv

.. _crude_installation:

☤ Crude Installation of Pipenv
==============================

Si ni siquiera tienes instalado pip, puedes usar un método de instalación en bruto, el cual arrancará en todo tu sistema::

    $ curl https://raw.githubusercontent.com/kennethreitz/pipenv/master/get-pipenv.py | python

¡Felicidades, ahora tienes pip y Pipenv instalados!

