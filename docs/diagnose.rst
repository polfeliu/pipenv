.. _diagnose:

Problemas frecuentes encontrados con Pipenv
======================================

Pipenv esta siendo mejorado constantemente por voluntarios, pero sigue siendo
un proyecto muy nuevo con recursos limitados, y tiene algunas peculiaridades que 
se necesitan tratar. Necesitamos la ayuda de todos(¡incluso la tuya!).

Aqui hay algunas preguntas comunes de gente usando Pipenv. Por favor échale un vistazo
y mira si resuelve tu problema.

.. Note:: **Asegúrate primero de estar ejecutando la versión más reciente de Pipenv**

☤ Tus dependencias no pudieron ser resueltas
-----------------------------------------

Asegúrate de que tus dependencias **realmente** resuelven. Si estas seguro de ello,
tal vez necesites limpiar cache. Ejecuta el siguiente comando::

    pipenv run pipenv-resolver --clear

e intenta de nuevo.

Si esto no funciona, intenta borrar toda la carpeta cache manualmente. Suele estar
ubicado en una de estas rutas:

* ``~/Library/Caches/pipenv`` (macOS)
* ``%LOCALAPPDATA%\pipenv\pipenv\Cache`` (Windows)
* ``~/.cache/pipenv`` (other operating systems)

Pipenv no instala prelanzamientos (e.j. una versión con el sufijo alpha/beta/etc.
como *10b1*) por defecto. Necesitarás pasar la bandera ``--pre`` en tu comando
o setear

::

    [pipenv]
    allow_prereleases = true

en tu Pipfile.

☤ No module named <module name>
---------------------------------

Esto generalmente es el resultado de usar Pipenv con otros paquetes de sistema. Nosotros
**fuertemente** recomendamos instalar Pipenv en un entorno aislado. Desinstalar 
todas las instalaciones existentes de Pipenv y mirar :ref:`proper_installation` 
para escoger una de las maneras recomendadas de instalar Pipenv


☤ Mi Python instalado con pyenv no es encontrado
----------------------------------------

Asegúrate de tener ``PYENV_ROOT`` seteado correctamente. Pipenv solo soporta
distribuciones CPython, con nombre de versiones como ``3.6.4`` o similares.

☤ Pipenv no respeta las versiones locales y globales de pyenv
------------------------------------------------------------------

Pipenv por defecto usa la versión de Python con la que fue instalada para 
crear el entorno virtual. Puedes setear la opción ``--python`` o
``$PYENV_ROOT/shims/python`` para dejar que pyenv consulte cuando escoger el
interprete. Mira :ref:`specifying_versions` para más información

Si quieres a Pipenv para automáticamente "haga lo correcto", puedes setear la
variable de entorno ``PIPENV_PYTHON`` a ``$PYENV_ROOT/shims/python``. Esto
hará que Pipenv use la versión activa de Python de pyenv para crear el 
entorno virtual por defecto.

.. _unknown-local-diagnose:

☤ ValueError: unknown locale: UTF-8
-----------------------------------

macOS tiene un bug en su detección de localización que nos impide detectar tu
codificación de shell correctamente. Esto también puede ser un problema en otros
sistemas si la variable local no especifica una codificación.

La solución es setear las siguientes dos variables de entorno en un formato estándar
de localización:

* ``LC_ALL``
* ``LANG``

Para Bash, por ejemplo, puedes agregar lo siguiente en tu ``~/.bash_profile``: 

.. code-block:: bash

    export LC_ALL='en_US.UTF-8'
    export LANG='en_US.UTF-8'

Para Zsh, el archivo a editar es ``~/.zshrc``.

.. Note:: Puedes cambiar ambos ``en_US`` y ``UTF-8`` al lenguaje/region 
          y codificación que uses.

☤ /bin/pip: No such file or directory
-------------------------------------

Esto puede estar relacionado a tu configuración local. Mira :ref:`unknown-local-diagnose`
para una posible solución.

☤ ``shell`` does not show the virtualenv’s name in prompt
---------------------------------------------------------

Esto es intencional. Puedes hacerlo por ti mismo con cualquier plugins de shell,
o una configuración inteligente ``PS1``. Si lo quieres de vuelta, usa

::

    pipenv shell -c

en su lugar (no disponible en Windows).

☤ Pipenv no respeta las dependencias en setup.py
--------------------------------------------------

No, no lo hace, intencionalmente. Pipfile y setup.py sirven propósitos diferentes,
y no debes considerar uno u otro por defecto. Mira :ref:`pipfile-vs-setuppy` 
para más información.

☤ Usando ``pipenv run`` en el programa Supervisor
---------------------------------------------

Cuando configuras un programa supervisor ``command`` con ``pipenv run ...``, 
necesitas setear una variable de entorno local adecuada para que funcione.

Agrega esta linea debajo de la sección ``[supervisord]`` en ``/etc/supervisor/supervisord.conf``::
    
    [supervisord]
    environment=LC_ALL='en_US.UTF-8',LANG='en_US.UTF-8'
