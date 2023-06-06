Unit Testing
-----------------

Goals
^^^^^

As a plugin's complexity increases so does the possibility of a feature or bug fix breaking some other part of the plugin. For this, it is recommended that plugins have some unit tests in place to detect when expected functionality breaks.

Bootstrap
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, you need to create a bootstrap file to prepare the testing environment. This file should be located at ``tests/bootstrap.php``.
In the bootstrap file, you need to import a few required files and set a few constants, as well as loading your plugin. Note that you must manually check prerequisites since this check is not called automatically.
For example:

.. code-block:: php

    <?php
    global $CFG_GLPI;

    define('GLPI_ROOT', dirname(dirname(dirname(__DIR__))));
    define("GLPI_CONFIG_DIR", GLPI_ROOT . "/tests");

    include GLPI_ROOT . "/inc/includes.php";
    include_once GLPI_ROOT . '/tests/GLPITestCase.php';
    include_once GLPI_ROOT . '/tests/DbTestCase.php';

    $plugin = new \Plugin();
    $plugin->checkStates(true);
    $plugin->getFromDBbyDir('myplugin');

    if (!plugin_myplugin_check_prerequisites()) {
        echo "\nPrerequisites are not met!";
        die(1);
    }

    if (!$plugin->isInstalled('myplugin')) {
        $plugin->install($plugin->getID());
    }
    if (!$plugin->isActivated('myplugin')) {
        $plugin->activate($plugin->getID());
    }

You must replace "myplugin" with the directory name of your plugin.

Unit test files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All unit tests must be placed inside the ``tests/units`` directory in your plugin.
Each test file has to correspond to an existing class name. If your plugin has a file ``inc/test.class.php`` with the class name ``PluginMypluginTest``, the test file must be named ``PluginMypluginTest.php``.

Running your tests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To run your tests, go to the root of your GLPI installation and run:

.. code-block:: bash

    vendor/bin/atoum -bf plugins/myplugin/tests/bootstrap.php -d plugins/myplugin/tests/

You must replace "myplugin" with the directory name of your plugin.

Real examples
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following plugins are a good example of how to implement Atoum tests:

- `JAMF Plugin for GLPI <https://github.com/cconard96/jamf>`_
- `Fields Plugin for GLPI <https://github.com/pluginsGLPI/fields>`_

Further reading
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The `Atoum documentation <https://atoum.readthedocs.io/>`_ is a good place to start if you are not familiar with unit testing or Atoum.
