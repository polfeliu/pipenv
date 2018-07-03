.. _basic:

Uso Básico de Pipenv
=====================

.. image:: https://farm4.staticflickr.com/3931/33173826122_b7ee8f1a26_k_d.jpg

Este documento cubre algunos de las características más básicas de Pipenv.

☤ Ejemplo de Pipfile & Pipfile.lock
--------------------------------

.. _example_files:

Este es un ejemplo sencillo de un ``Pipfile`` y el resultado de ``Pipfile.lock``.

Ejemplo de Pipfile
///////////////

::

    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true
    name = "pypi"

    [packages]
    requests = "*"


    [dev-packages]
    pytest = "*"


Ejemplo de Pipfile.lock
////////////////////

::

    {
        "_meta": {
            "hash": {
                "sha256": "8d14434df45e0ef884d6c3f6e8048ba72335637a8631cc44792f52fd20b6f97a"
            },
            "host-environment-markers": {
                "implementation_name": "cpython",
                "implementation_version": "3.6.1",
                "os_name": "posix",
                "platform_machine": "x86_64",
                "platform_python_implementation": "CPython",
                "platform_release": "16.7.0",
                "platform_system": "Darwin",
                "platform_version": "Darwin Kernel Version 16.7.0: Thu Jun 15 17:36:27 PDT 2017; root:xnu-3789.70.16~2/RELEASE_X86_64",
                "python_full_version": "3.6.1",
                "python_version": "3.6",
                "sys_platform": "darwin"
            },
            "pipfile-spec": 5,
            "requires": {},
            "sources": [
                {
                    "name": "pypi",
                    "url": "https://pypi.python.org/simple",
                    "verify_ssl": true
                }
            ]
        },
        "default": {
            "certifi": {
                "hashes": [
                    "sha256:54a07c09c586b0e4c619f02a5e94e36619da8e2b053e20f594348c0611803704",
                    "sha256:40523d2efb60523e113b44602298f0960e900388cf3bb6043f645cf57ea9e3f5"
                ],
                "version": "==2017.7.27.1"
            },
            "chardet": {
                "hashes": [
                    "sha256:fc323ffcaeaed0e0a02bf4d117757b98aed530d9ed4531e3e15460124c106691",
                    "sha256:84ab92ed1c4d4f16916e05906b6b75a6c0fb5db821cc65e70cbd64a3e2a5eaae"
                ],
                "version": "==3.0.4"
            },
            "idna": {
                "hashes": [
                    "sha256:8c7309c718f94b3a625cb648ace320157ad16ff131ae0af362c9f21b80ef6ec4",
                    "sha256:2c6a5de3089009e3da7c5dde64a141dbc8551d5b7f6cf4ed7c2568d0cc520a8f"
                ],
                "version": "==2.6"
            },
            "requests": {
                "hashes": [
                    "sha256:6a1b267aa90cac58ac3a765d067950e7dbbf75b1da07e895d1f594193a40a38b",
                    "sha256:9c443e7324ba5b85070c4a818ade28bfabedf16ea10206da1132edaa6dda237e"
                ],
                "version": "==2.18.4"
            },
            "urllib3": {
                "hashes": [
                    "sha256:06330f386d6e4b195fbfc736b297f58c5a892e4440e54d294d7004e3a9bbea1b",
                    "sha256:cc44da8e1145637334317feebd728bd869a35285b93cbb4cca2577da7e62db4f"
                ],
                "version": "==1.22"
            }
        },
        "develop": {
            "py": {
                "hashes": [
                    "sha256:2ccb79b01769d99115aa600d7eed99f524bf752bba8f041dc1c184853514655a",
                    "sha256:0f2d585d22050e90c7d293b6451c83db097df77871974d90efd5a30dc12fcde3"
                ],
                "version": "==1.4.34"
            },
            "pytest": {
                "hashes": [
                    "sha256:b84f554f8ddc23add65c411bf112b2d88e2489fd45f753b1cae5936358bdf314",
                    "sha256:f46e49e0340a532764991c498244a60e3a37d7424a532b3ff1a6a7653f1a403a"
                ],
                "version": "==3.2.2"
            }
        }
    }

☤ Recomendaciones generales & Control de versión
-------------------------------------------

- Generalmente, mantén a ambos ``Pipfile`` y ``Pipfile.lock`` en tu control de versión.
- No mantengas ``Pipfile.lock`` en tu control de version si estas usando multiples versiones de Python 
- Especifica tu versión de Python en la sección de tu `Pipfile`'s ``[requires]`` . En resumen, deberias tener solo una versión de Python, como herramienta de desarrollo.
- ``pipenv install`` es totalmente compatible con la sintaxis de ``pip install``, puedes encontrar toda su documentación `aqui <https://pip.pypa.io/en/stable/user_guide/#installing-packages>`_.


☤ Ejemplo del flujo de trabajo de Pipenv
-------------------------

Clona / crea el repositorio del proyecto::

    …
    $ cd myproject

Instala desde Pipfile, si hay uno::

    $ pipenv install

O, agrega un paquete a tu nuevo proyecto::

    $ pipenv install <package>

Esto creara un ``Pipfile`` si no existe. Si existe, automaticamente se editara con los nuevos paquetes que proporciones.

A continuacion, activa el shell de Pipenv::

    $ pipenv shell
    $ python --version
    …

.. _initialization:

☤ Ejemplo de uso del flujo de trabajo.
---------------------------------

- Averigua que cambio en upstream: ``$ pipenv update --outdated``.
- Actualizar paquetes, dos opciones:
    a. ¿Quieres actualizar todo? Solo haz ``$ pipenv update``.
    b. ¿Quieres actualizar paquete por paquete? ``$ pipev update <pkg>`` for cada paquete desactualizado.

☤ Importando desde requirements.txt
---------------------------------

Si solo tienes un archivo ``requirements.txt`` disponible cuando ejecutes ``pipenv install``,
pipenv automáticamente importara el contenido de este archivo y creara un ``Pipfile`` por ti.

También puedes especificar ``$ pipenv install -r path/to/requirements.txt`` para importar un archivo requirements.

Si tu archivo requirements tiene versiones fijas, vas a querer editar el nuevo ``Pipfile``
para removerlos, y dejar que ``pipenv`` siga las versiones fijas. Si quieres dejar las versiones fijas
en tu ``Pipfile.lock`` por ahora, ejecuta ``pipenv lock --keep-outdated``. Asegurate de `actualizar <#initialization>`_ pronto!

.. _specifying_versions:

☤ Especifica la versión de un parquete
----------------------------------

Para instalar con pipenv una versión especifica de una librería, el uso es simple::

    $ pipenv install requests==2.13.0

Esto actualizara tu ``Pipfile`` para reflejar este requisito, automáticamente

☤ Especifica la versión de Python
-------------------------------

Para crear un nuevo entorno virtual, usando una versión especifica de Python que tengas instalada (y en tu ``PATH``), usa la bandera ``--python VERSION``, asi:

Usar Python 3::

   $ pipenv --python 3

Usar Python3.6::

   $ pipenv --python 3.6

Usar Python 2.7.14::

    $ pipenv --python 2.7.14

Cuando des una versión de Python, de esta manera, Pipenv automáticamente escaneara tu sistema en busca de la versión de Python dada.

Si un ``Pipfile`` no ha sido creado todavía, uno se creara por ti, que se vera como esto::

    [[source]]
    url = "https://pypi.python.org/simple"
    verify_ssl = true

    [dev-packages]

    [packages]

    [requires]
    python_version = "3.6"

Nota la inclusión de ``[requires] python_version = "3.6"``. Esto especifica que tu aplicación requiere esta versión
de Python, y la usara automáticamente cuando ejecutes ``pipenv install`` con este ``Pipfile`` en el futuro
(e.j. en otras maquinas) Si esto no es verdad, siéntete libre de remover esta sección.

Si no especificas una versión de Python en la linea de comandos, tanto el ``[requires]`` ``python_full_version`` o ``python_version`` sera seleccionado
automáticamente, usando cualquier instalación de ``python`` por defecto, cuando se ejecute


☤ Dependencias editables (e.j. ``-e .``)
----------------------------------------

Le puedes decir a Pipenv para instalar una ruta como editable - a menudo es util para
el directorio actual cuando se trabaje en un paquete::

    $ pipenv install --dev -e .

    $ cat Pipfile
    ...
    [dev-packages]
    "e1839a8" = {path = ".", editable = true}
    ...

Nota que todas las subdependencias se agregaran al ``Pipfile.lock``

.. Nota:: Las Subdependencias **no** son agregadas al ``Pipfile.lock``
          si dejas la opcion ``-e`` por fuera.


.. _environment_management:

☤ Environment Management with Pipenv
------------------------------------

Los tres comandos principales que usaras en el manejo de tu pipenv entorno son 
``$ pipenv install``, ``$ pipenv uninstall``, and ``$ pipenv lock``.

.. _pipenv_install:

$ pipenv install
////////////////

``$ pipenv install`` es usado para la instalación de paquetes en tu entorno virtual con pipenv
y actualización de tu Pipfile

Junto con el comando de instalación básico, que toma la forma::

    $ pipenv install [package names]

El usuario puede proporcionar estos parámetros adicionales:

    - ``--two`` — Realiza la instalación en un entorno virtual usando la ruta ``python2`` del sistema.
    - ``--three`` — Realiza la instalación en un entorno virtual usando la ruta ``python3`` del sistema.
    - ``--python`` — Realiza la instalación en un entorno virtual usando la versión del interprete de python proporcionada.

    .. advertencia:: Ninguno de los comandos mencionados deberían usarse juntos. También son 
                 **destructivos** y borraran tu actual entorno virtual antes de reemplazarlo
                 con una versión apropiada.

    .. nota:: El entorno virtual creado por Pipenv puede ser diferente de lo que esperas.
              Caracteres peligrosos (e.j. ``$`!*@`` así como el espacio, siguiente linea, carriage return, 
              y tabulación) son convertidos a guion bajo(_). Adicionalmente, la ruta completa al directorio 
              actual es codificada en un "valor slug" y se agrega para asegurar que el nombre del entorno virtual
              es único.

    - ``--dev`` — Instala ambos ``develop`` y ``default`` paquetes desde ``Pipfile.lock``.
    - ``--system`` — Usa el comando pip del sistema ``pip`` y no el que esta en tu entorno virtual.
    - ``--ignore-pipfile`` — Ignora el  ``Pipfile`` e instala desde el ``Pipfile.lock``.
    - ``--skip-lock`` — Ignora el ``Pipfile.lock`` e instala desde el ``Pipfile``. Ademas, no escribe en el ``Pipfile.lock`` reflejando los cambios del ``Pipfile``.

.. _pipenv_uninstall:

$ pipenv uninstall
//////////////////

``$ pipenv uninstall`` soporta todos los parámetros de `pipenv install <#pipenv-install>`_,
así como dos opciones adicionales, ``--all`` y ``--all-dev``.

    - ``--all`` — Este parámetro limpia todos los archivos de tu entorno virtual,
      pero deja el Pipfile intacto

    - ``--all-dev`` — Este parámetro eliminara todos los paquetes de desarollo del
      entorno virtual, y los elimina del Pipfile.

.. _pipenv_lock:

$ pipenv lock
/////////////

``$ pipenv lock`` es usado para crear ``Pipfile.lock``, el cual declara **todas** las dependencias (y subdependencias) de tu proyecto, sus ultimas versiones, y el actual hash de los archivos descargados. Esto asegura repetibles, y mas importantes *deterministas* builds.

☤ Configuración sobre el shell
---------------------------

Los Shells son típicamente mal configurados para el uso del subshell, así que ``$ pipenv shell --fancy`` puede producir resultados inesperados. Si este es el caso, intenta ``$ pipenv shell``, el cual usa "modo de compatibilidad", e intentará generar una subshell a pesar de la mala configuración.

Una apropiada configuración de shell solo setea variables de entorno como ``PATH`` durante el inicio de sesión, no en cada subshell generada (como están típicamente configuradas para hacer). En fish, esto se ve así::

    if status --is-login
        set -gx PATH /usr/local/bin $PATH
    end

Deberías hacer esto tambien para tu shell, en tu ``~/.profile`` o ``~/.bashrc`` o donde sea apropiado. 

.. nota:: El shell se lanza en modo interactivo. Esto significa que si tu shell lee su configuración desde un archivo especifico para el modo interactivo (e.j. bash por defecto busca por un archivo ``~/.bashrc`` para la configuración del modo interactivo) entonces necesitaras modificar (o crear) este archivo.

☤ Una nota sobre dependencias en SCV
-------------------------------

Pipenv resolverá las subdependencias de las dependencias de SCV, pero solo si estas son instaladas en modo editable::

    $ pipenv install -e git+https://github.com/requests/requests.git#egg=requests

    $ cat Pipfile
    [packages]
    requests = {git = "https://github.com/requests/requests.git", editable=true}

Si editable no es true, las subdependencias no se resolverán.

Para mas información acerca de otras opciones disponibles cuando se especifica dependencias de SCV, por favor revisa los `aspectos del Pipfile <https://github.com/pypa/pipfile>`__.


☤ Pipfile.lock características de seguridad
--------------------------------

``Pipfile.lock`` toma ventaja de algunas buenas mejoras de seguridad en ``pip``.
Por defecto, el ``Pipfile.lock`` se generara con un hash sha256 para cada paquete descargado.
Esto permitará a ``pip`` garantizar que estas instalando lo que intentas cuando hay una red comprometida,
o descargando dependencias desde un endpoint PyPI poco fiable.

We highly recommend approaching deployments with promoting projects from a development
environment into production. You can use ``pipenv lock`` to compile your dependencies on
your development environment and deploy the compiled ``Pipfile.lock`` to all of your
production environments for reproducible builds.

.. note:

    If you'd like a ``requirements.txt`` output of the lockfile, run ``$ pipenv lock -r``.
    This will include all hashes, however (which is great!). To get a ``requirements.txt``
    without hashes, use ``$ pipenv run pip freeze``.

