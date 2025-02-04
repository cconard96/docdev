Coding standards
================

As of GLPI 10, we rely on `PSR-12 <https://www.php-fig.org/psr/psr-12/>`_ for coding standards.


Call static methods
-------------------

================= ===========
Function location How to call
================= ===========
class itself      ``self::theMethod()``
parent class      ``parent::theMethod()``
another class     ``ClassName::theMethod()``
================= ===========

Static or Non static?
---------------------

Some methods in the source code as `declared as static <http://php.net/manual/fr/language.oop5.static.php>`_; some are not.

For sure, you cannot make static calls on a non static method. In order to call such a method, you will have to get an object instance, and then call the method on it:

.. code-block:: php

   <?php

   $object = new MyObject();
   $object->nonStaticMethod();

It may be different calling static classes. In that case; you can either:

* call statically the method from the object; like ``MyObject::staticMethod()``,
* call statically the method from an object instance; like ``$object::staticMethod()``,
* call non statically the method from an object instance; like ``$object->staticMethod()``.
* use `late static building <http://php.net/manual/en/language.oop5.late-static-bindings.php>`_; like ``static::staticMethod()``.

When you do not have any object instance yet; the first solution is probably the best one. No need to instantiate an object to just call a static method from it.

On the other hand; if you already have an object instance; you should better use any of the solution but the late static binding. That way; you will save performances since this way to go do have a cost.


Comments
--------

To be more visible, don't put inline block comments into ``/* */`` but comment each line with ``//``. Put docblocks comments into ``/** */``.

Each function or method must be documented, as well as all its parameters (see `Variables types`_ below), and its return.

For each method or function documentation, you'll need at least to have a description, the version it was introduced, the parameters list, the return type; each blocks separated with a blank line. As an example, for a void function:

.. code-block:: php

   <?php
   /**
    * Describe what the method does. Be concise :)
    *
    * You may want to add some more words about what the function
    * does, if needed. This is optional, but you can be more
    * descriptive here:
    * - it does something
    * - and also something else
    * - but it doesn't make coffee, unfortunately.
    *
    * @since 9.2
    *
    * @param string  $param       A parameter, for something
    * @param boolean $other_param Another parameter
    *
    * @return void
    */
   function myMethod($param, $other_param) {
      //[...]
   }

Some other information way be added; if the function requires it.

Refer to the `PHPDocumentor website <https://phpdoc.org/docs/latest>`_ to get more information on documentation.

Please follow the order defined below:

 #. Description,
 #. Long description, if any,
 #. `@deprecated`.
 #. `@since`,
 #. `@var`,
 #. `@param`,
 #. `@return`,
 #. `@see`,
 #. `@throw`,
 #. `@todo`,

Parameters documentation
^^^^^^^^^^^^^^^^^^^^^^^^

Each parameter must be documented in its own line, beginning with the ``@param`` tag, followed by the `Variables types`_, followed by the param name (``$param``), and finally with the description itself.
If your parameter can be of different types, you can list them separated with a ``|`` or you can use the ``mixed`` type; it's up to you!

All parameters names and description must be aligned vertically on the longest (plu one character); see the above example.

Override method: @inheritDoc? @see? docblock? no docblock?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There are many question regarding the way to document a child method in a child class.

Many editors use the ``{@inheritDoc}`` tag without anything else. **This is wrong**. This *inline* tag is confusing for many users; for more details, see the `PHPDocumentor documentation about it <https://www.phpdoc.org/docs/latest/guides/inheritance.html#the-inheritdoc-tag>`__.
This tag usage is not forbidden, but make sure to use it properly, or just avoid it. An usage example:

.. code-block:: php

   <?php

   abstract class MyClass {
      /**
       * This is the documentation block for the current method.
       * It does something.
       *
       * @param string $sthing Something to send to the method
       *
       * @return string
       */
      abstract public function myMethod($sthing);
   }

   class MyChildClass extends MyClass {
      /**
       * {@inheritDoc} Something is done differently for a reason.
       *
       * @param string $sthing Something to send to the method
       *
       * @return string
       */
      public function myMethod($sthing) {
         [...]
      }

Something we can see quite often is just the usage of the ``@see`` tag to make reference to the parent method. **This is wrong**. The ``@see`` tag is designed to reference another method that would help to understand this one; not to make a reference to its parent (you can also take a look at `PHPDocumentor documentation about it <https://www.phpdoc.org/docs/latest/references/phpdoc/tags/see.html>`__). While generating, parent class and methods are automatically discovered; a link to the parent will be automatically added.
An usage example:

.. code-block:: php

   <?php
   /**
    * Adds something
    *
    * @param string $type  Type of thing
    * @param string $value The value
    *
    * @return boolean
    */
   public function add($type, $value) {
      // [...]
   }

   /**
    * Adds myType entry
    *
    * @param string $value The value
    *
    * @return boolean
    * @see add()
    */
   public function addMyType($value) {
      return $this->addType('myType', $value);
   }

Finally, should I add a docblock, or nothing?

PHPDocumentor and various tools will just use parent docblock verbatim if nothing is specified on child methods. So, if the child method acts just as its parent (extending an abstract class, or some super class like ``CommonGLPI`` or ``CommonDBTM``); you may just omit the docblock entirely. The alternative is to copy paste parent docblock entirely; but that way, it would be required to change all children docblocks when parent if changed.

Variables types
---------------

Variables types for use in DocBlocks for Doxygen:

=============== ===========
 Type           Description
=============== ===========
mixed           A variable with undefined (or multiple) type
integer         Integer type variable (whole number)
float           Float type (point number)
boolean         Logical type (true or false)
string          String type (any value in ``""`` or ``' '``)
array           Array type
object          Object type
resource        Resource type (as returned from ``mysql_connect`` function)
=============== ===========

In addition to the above, you may use any valid types from `PHPStan <https://phpstan.org/writing-php-code/phpdoc-types>`_.

You may also use a specific class for the type as a replacement for `object` when you know the exact type of data being used. This is recommended if you use typehints.
Since PHP 7.1, you can have nullable typehints for method parameters and return types. You should prepend a `?` to the above types if they are nullable.

Inserting comment in source code for doxygen.
Result : full doc for variables, functions, classes...


Quotes / double quotes
----------------------

* You must use single quotes for indexes, constants declaration, translations, ...
* Use double quote in translated strings
* When you have to use tabulation character (``\t``), carriage return (``\n``) and so on, you should use double quotes.
* For performances reasons since PHP7, you may avoid strings concatenation.

Examples:

.. code-block:: php

   <?php
   //for that one, you should use double, but this is at your option...
   $a = "foo";
   
   //use double quotes here, for $foo to be interpreted
   //   => with double quotes, $a will be "Hello bar" if $foo = 'bar'
   //   => with single quotes, $a will be "Hello $foo"
   $a = "Hello $foo";
   
   //use single quotes for array keys
   $tab = [
      'lastname'  => 'john',
      'firstname' => 'doe'
   ];
   
   //Do not use concatenation to optimize PHP7
   //note that you cannot use functions call in {}
   $a = "Hello {$tab['firstname']}";
   
   //single quote translations
   $str = __('My string to translate');
   
   //Double quote for special characters
   $html = "<p>One paragraph</p>\n<p>Another one</p>";
   
   //single quote cases
   switch ($a) {
      case 'foo' : //use single quote here
         ...
      case 'bar' :
         ...
   }


Checking standards
------------------

Linting (checking and fixing coding standards) is a good way to ensure code quality and consistency of the code base.
This is done using [PHP CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer), [PHPStan](https://phpstan.org/), [ESLint](https://eslint.org/), [Stylelint](https://stylelint.io/) and [TwigCS](https://github.com/friendsoftwig/twigcs).

This can run the tasks using Docker, on your local host use `./tests/run_tests.sh lint` to proceed to all linting tasks, or use a scoped task, `./tests/run_tests.sh lint_php` to proceed to PHP linting only for example.
All possible lintings are listed in the `./tests/run_tests.sh` script.
For faster action you can run the scripts on your local machine or Docker development container using `node_modules/.bin/eslint . && echo "ESLint found no errors"` for example. You can find all the commands in [`.github/actions` directory](https://github.com/glpi-project/glpi/tree/main/.github/actions).
