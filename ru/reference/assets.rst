Assets Management
=================
Phalcon\Assets is a component that allows the developer to manage static resources
such as css stylesheets or javascript libraries in a web application.

:doc:`Phalcon\\Assets\\Manager <../api/Phalcon_Assets_Manager>` is available in the services
container, so you can add resources from any part of the application where the container
is available.

Adding Resources
----------------
Assets supports two built-in resources: css and javascripts. You can create other
resources if you need. The assets manager internally stores two default collections
of resources one for javascript and another for css.

You can easily add resources to these collections like follows:

.. code-block:: php

    <?php

    class IndexController extends Phalcon\Mvc\Controller
    {
        public function index()
        {

            //Add some local CSS resources
            $this->assets
                ->addCss('css/style.css')
                ->addCss('css/index.css');

            //and some local javascript resources
            $this->assets
                ->addJs('js/jquery.js')
                ->addJs('js/bootstrap.min.js');

        }
    }

Then in the views added resources can be printed:

.. code-block:: html+php

    <html>
        <head>
            <title>Some amazing website</title>
            <?php $this->assets->outputCss() ?>
        </head>
        <body>

            <!-- ... -->

            <?php $this->assets->outputJs() ?>
        </body>
    <html>

Local/Remote resources
----------------------
Local resources are those who're provided by the same application and they're located in the document root
of the application. URLs in local resources are generated by the 'url' service, usually
:doc:`Phalcon\\Mvc\\Url <../api/Phalcon_Mvc_Url>`.

Remote resources are those such as common library like jquery, bootstrap, etc. that are provided by a CDN.

.. code-block:: php

    <?php

    //Add some local CSS resources
    $this->assets
        ->addCss('//netdna.bootstrapcdn.com/twitter-bootstrap/2.3.1/css/bootstrap-combined.min.css', false)
        ->addCss('css/style.css', true);

Collections
-----------
Collections groups resources of the same type, the assets manager implicitly creates two collections: css and js.
You can create additional collections to group specific resources for ease of placing those resources in the views:

.. code-block:: php

    <?php

    //Javascripts in the header
    $this->assets
        ->collection('header')
        ->addJs('js/jquery.js')
        ->addJs('js/bootstrap.min.js');

    //Javascripts in the footer
    $this->assets
        ->collection('footer')
        ->addJs('js/jquery.js')
        ->addJs('js/bootstrap.min.js');

Then in the views:

.. code-block:: html+php

    <html>
        <head>
            <title>Some amazing website</title>
            <?php $this->assets->outputJs('header') ?>
        </head>
        <body>

            <!-- ... -->

            <?php $this->assets->outputJs('footer') ?>
        </body>
    <html>

Prefixes
--------
Collections can be URL-prefixed, this allows to easily change from a server to other at any moment:

.. code-block:: php

    <?php

    $scripts = $this->assets->collection('footer');

    if ($config->enviroment == 'development') {
        $scripts->setPrefix('/');
    } else {
        $scripts->setPrefix('http:://cdn.example.com/');
    }

    $scripts->addJs('js/jquery.js')
            ->addJs('js/bootstrap.min.js');

A chaineable syntax is available too:

.. code-block:: php

    <?php

    $scripts = $assets
        ->collection('header')
        ->setPrefix('http:://cdn.example.com/')
        ->setLocal(false)
        ->addJs('js/jquery.js')
        ->addJs('js/bootstrap.min.js');

Custom Output
-------------
Methods outputJs and outputCss are available to generate the necessary HTML code according to each type of resources.
You can override this method or print the resources manually in the following way:

.. code-block:: php

    <?php

    foreach ($this->assets->collection('js') as $resource) {
        echo Phalcon_Tag::javascriptInclude($resource->getPath());
    }