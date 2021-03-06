I tag della dependency injection
================================

Tag:

* ``data_collector``
* ``form.type``
* ``form.type_extension``
* ``form.type_guesser``
* ``kernel.cache_warmer``
* ``kernel.event_listener``
* ``kernel.event_subscriber``
* ``monolog.logger``
* ``monolog.processor``
* ``templating.helper``
* ``routing.loader``
* ``translation.loader``
* ``twig.extension``
* ``validator.initializer``

Abilitare helper di template personalizzati in PHP 
--------------------------------------------------

Per abilitare un helper di template personalizzato, aggiungerlo come normale servizio in
una delle proprie configurazioni, taggarlo con ``templating.helper`` e definire un
attributo ``alias`` (l'helper sarà accessibile nei template tramite questo
alias):

.. configuration-block::

    .. code-block:: yaml

        services:
            templating.helper.your_helper_name:
                class: Fully\Qualified\Helper\Class\Name
                tags:
                    - { name: templating.helper, alias: alias_name }

    .. code-block:: xml

        <service id="templating.helper.your_helper_name" class="Fully\Qualified\Helper\Class\Name">
            <tag name="templating.helper" alias="alias_name" />
        </service>

    .. code-block:: php

        $container
            ->register('templating.helper.your_helper_name', 'Fully\Qualified\Helper\Class\Name')
            ->addTag('templating.helper', array('alias' => 'alias_name'))
        ;

.. _reference-dic-tags-twig-extension:

Abilitare estensioni personalizzate in Twig
-------------------------------------------

Per abilitare un'estensione di Twig, aggiungerla come normale servizio in una delle
proprie configurazioni e taggarla con ``twig.extension``:

.. configuration-block::

    .. code-block:: yaml

        services:
            twig.extension.your_extension_name:
                class: Fully\Qualified\Extension\Class\Name
                tags:
                    - { name: twig.extension }

    .. code-block:: xml

        <service id="twig.extension.your_extension_name" class="Fully\Qualified\Extension\Class\Name">
            <tag name="twig.extension" />
        </service>

    .. code-block:: php

        $container
            ->register('twig.extension.your_extension_name', 'Fully\Qualified\Extension\Class\Name')
            ->addTag('twig.extension')
        ;

Per informazioni su come creare la classe estensione di Twig, vedere la
`documentazione di Twig`_ sull'argomento.

Prima di scrivere la propria estensione, dare un'occhiata al
`repository ufficiale di estensioni Twig`_ , che include tante estensioni
utili. Per esempio, ``Intl`` e il suo filtro ``localizeddate``, che
formatta una data a seconda del locale dell'utente. Anche queste estensioni ufficiali
devono essere aggiunte come servizi:

.. configuration-block::

    .. code-block:: yaml

        services:
            twig.extension.intl:
                class: Twig_Extensions_Extension_Intl
                tags:
                    - { name: twig.extension }

    .. code-block:: xml

        <service id="twig.extension.intl" class="Twig_Extensions_Extension_Intl">
            <tag name="twig.extension" />
        </service>

    .. code-block:: php

        $container
            ->register('twig.extension.intl', 'Twig_Extensions_Extension_Intl')
            ->addTag('twig.extension')
        ;

.. _dic-tags-kernel-event-listener:

Abilitare ascoltatori personalizzati
------------------------------------

Per abilitare un ascoltatore personalizzato, aggiungerlo come normale servizio in una
della proprie configurazioni e taggarlo con ``kernel.event_listener``. Bisogna fornire
il nome dell'evento che il proprio ascolta, come anche il metodo che sarà
richiamato:

.. configuration-block::

    .. code-block:: yaml

        services:
            kernel.listener.your_listener_name:
                class: Fully\Qualified\Listener\Class\Name
                tags:
                    - { name: kernel.event_listener, event: xxx, method: onXxx }

    .. code-block:: xml

        <service id="kernel.listener.your_listener_name" class="Fully\Qualified\Listener\Class\Name">
            <tag name="kernel.event_listener" event="xxx" method="onXxx" />
        </service>

    .. code-block:: php

        $container
            ->register('kernel.listener.your_listener_name', 'Fully\Qualified\Listener\Class\Name')
            ->addTag('kernel.event_listener', array('event' => 'xxx', 'method' => 'onXxx'))
        ;

.. note::

    Si può anche specificare una priorità, come attributo del tag ``kernel.event_listener``
    (probabilmente il metodo o gli attributi dell'evento), con un intero positivo oppure
    negativo. Questo consente di assicurarsi che i propri ascoltatori siano sempre
    richiamati prima o dopo altri ascoltatori che ascoltano lo stesso evento.



.. _dic-tags-kernel-event-subscriber:

Abilitare sottoscrittori personalizzati
---------------------------------------

.. versionadded:: 2.1

   La possibilità di aggiungere sottoscrittori di eventi al kernel è nuova nella 2.1.

Per abilitare un sottoscrittore personalizzato, aggiungerlo come normale servizio in una
delle proprie configurazioni, con il tag ``kernel.event_subscriber``:

.. configuration-block::

    .. code-block:: yaml

        services:
            kernel.subscriber.your_subscriber_name:
                class: Fully\Qualified\Subscriber\Class\Name
                tags:
                    - { name: kernel.event_subscriber }

    .. code-block:: xml

        <service id="kernel.subscriber.your_subscriber_name" class="Fully\Qualified\Subscriber\Class\Name">
            <tag name="kernel.event_subscriber" />
        </service>

    .. code-block:: php

        $container
            ->register('kernel.subscriber.your_subscriber_name', 'Fully\Qualified\Subscriber\Class\Name')
            ->addTag('kernel.event_subscriber')
        ;

.. note::

    Il servizio deve implementare l'interfaccia
    :class:`Symfony\Component\EventDispatcher\EventSubscriberInterface`.

.. note::

    Se il servizio è creato da un factory, si **DEVE** impostare correttamente il
    parametro ``class`` per questo tag, per far sì che funzioni correttamente.

Abilitare motori di template personalizzati
-------------------------------------------

Per abilitare un motore di template personalizzato, aggiungerlo come normale servizio in
una delle proprie configurazioni, con il tag ``templating.engine``:

.. configuration-block::

    .. code-block:: yaml

        services:
            templating.engine.your_engine_name:
                class: Fully\Qualified\Engine\Class\Name
                tags:
                    - { name: templating.engine }

    .. code-block:: xml

        <service id="templating.engine.your_engine_name" class="Fully\Qualified\Engine\Class\Name">
            <tag name="templating.engine" />
        </service>

    .. code-block:: php

        $container
            ->register('templating.engine.your_engine_name', 'Fully\Qualified\Engine\Class\Name')
            ->addTag('templating.engine')
        ;

Abilitare caricatori di rotte personalizzati
--------------------------------------------

Per abilitare un caricatore personalizzato di rotte, aggiungerlo come normale servizio
in una delle proprie configurazioni, con il tag ``routing.loader``:

.. configuration-block::

    .. code-block:: yaml

        services:
            routing.loader.your_loader_name:
                class: Fully\Qualified\Loader\Class\Name
                tags:
                    - { name: routing.loader }

    .. code-block:: xml

        <service id="routing.loader.your_loader_name" class="Fully\Qualified\Loader\Class\Name">
            <tag name="routing.loader" />
        </service>

    .. code-block:: php

        $container
            ->register('routing.loader.your_loader_name', 'Fully\Qualified\Loader\Class\Name')
            ->addTag('routing.loader')
        ;

.. _dic_tags-monolog:

Usare un canale di log personalizzato con Monolog
-------------------------------------------------

Monolog consente di condividere i suoi gestori tra diversi canali di log.
Il servizio di log usa il canale ``app``, ma si può cambiare canale quando si
inietta il logger in un servizio.

.. configuration-block::

    .. code-block:: yaml

        services:
            mio_servizio:
                class: Fully\Qualified\Loader\Class\Name
                arguments: [@logger]
                tags:
                    - { name: monolog.logger, channel: acme }

    .. code-block:: xml

        <service id="mio_servizio" class="Fully\Qualified\Loader\Class\Name">
            <argument type="service" id="logger" />
            <tag name="monolog.logger" channel="acme" />
        </service>

    .. code-block:: php

        $definition = new Definition('Fully\Qualified\Loader\Class\Name', array(new Reference('logger'));
        $definition->addTag('monolog.logger', array('channel' => 'acme'));
        $container->register('mio_servizio', $definition);;

.. note::

    Funziona solo quando il servizio di log è il parametro di un costruttore, non
    quando è iniettato tramite setter.

.. _dic_tags-monolog-processor:

Aggiungere un processore per Monolog
------------------------------------

Monolog consente di aggiungere processori nel logger o nei gestori, per aggiungere
dati extra nelle registrazioni. Un processore riceve la registrazione come parametro e
deve restituirlo dopo aver aggiunto dei dati extra, nell'attributo ``extra`` della
registrazione.

Vediamo come poter usare ``IntrospectionProcessor`` per aggiungere file, riga, classe
e metodo quando il logger viene attivato.

Si può aggiungere un processore globalmente:

.. configuration-block::

    .. code-block:: yaml

        services:
            mio_servizio:
                class: Monolog\Processor\IntrospectionProcessor
                tags:
                    - { name: monolog.processor }

    .. code-block:: xml

        <service id="mio_servizio" class="Monolog\Processor\IntrospectionProcessor">
            <tag name="monolog.processor" />
        </service>

    .. code-block:: php

        $definition = new Definition('Monolog\Processor\IntrospectionProcessor');
        $definition->addTag('monolog.processor');
        $container->register('mio_servizio', $definition);

.. tip::

    Se il proprio servizio non è un metodo (che usa ``__invoke``), si può aggiungere
    l'attributo ``method`` nel tag, per usare un metodo specifico.

Si può anche aggiungere un processore per un gestore specifico, usando l'attributo
``handler``:

.. configuration-block::

    .. code-block:: yaml

        services:
            mio_servizio:
                class: Monolog\Processor\IntrospectionProcessor
                tags:
                    - { name: monolog.processor, handler: firephp }

    .. code-block:: xml

        <service id="mio_servizio" class="Monolog\Processor\IntrospectionProcessor">
            <tag name="monolog.processor" handler="firephp" />
        </service>

    .. code-block:: php

        $definition = new Definition('Monolog\Processor\IntrospectionProcessor');
        $definition->addTag('monolog.processor', array('handler' => 'firephp');
        $container->register('mio_servizio', $definition);

Si può anche aggiungere un processore per uno specifico canale di log, usando
l'attributo ``channel``. Registrerà il processore solo per il canale di log
``security``, usato nel componente Security:

.. configuration-block::

    .. code-block:: yaml

        services:
            mio_servizio:
                class: Monolog\Processor\IntrospectionProcessor
                tags:
                    - { name: monolog.processor, channel: security }

    .. code-block:: xml

        <service id="mio_servizio" class="Monolog\Processor\IntrospectionProcessor">
            <tag name="monolog.processor" channel="security" />
        </service>

    .. code-block:: php

        $definition = new Definition('Monolog\Processor\IntrospectionProcessor');
        $definition->addTag('monolog.processor', array('channel' => 'security');
        $container->register('mio_servizio', $definition);

.. note::

    Non si possono usare entrambi gli attributi ``handler`` e ``channel`` per lo stesso
    tag, perché i gestori sono condivisi tra tutti i canali.

..  _`documentazione di Twig`: http://twig.sensiolabs.org/doc/extensions.html
..  _`repository ufficiale di estensioni Twig`: http://github.com/fabpot/Twig-extensions
