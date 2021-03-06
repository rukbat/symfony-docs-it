.. index::
   single: Validazione; Vincoli personalizzati

Come creare vincoli di validazione personalizzati
-------------------------------------------------

È possibile creare vincoli personalizzati estendendo la classe base
:class:`Symfony\\Component\\Validator\\Constraint`. Le opzioni dei propri vincoli
sono rappresentate come proprietà pubbliche della classe. Ad esempio,
i vincoli di :doc:`Url</reference/constraints/Url>` includono le proprietà
``message`` (messaggio) e ``protocols`` (protocolli):

.. code-block:: php

    namespace Symfony\Component\Validator\Constraints;
    
    use Symfony\Component\Validator\Constraint;

    /**
     * @Annotation
     */
    class Protocol extends Constraint
    {
        public $message = 'Questo valore non è un protocollo valido';
        public $protocols = array('http', 'https', 'ftp', 'ftps');
    }

.. note::

    In questo vincolo, l'annotazione ``@Annotation`` è necessaria per
    poterne rendere disponibile l'uso nelle altre classi.

Come si può vedere, un vincolo è estremamente minimalistico. La validazione
vera e propria è effettuata da un'altra classe di "validazione dei vincoli". La
classe per la validazione dei vincoli è definita dal metodo del vincolo ``validatedBy()``,
che usa una semplice logica predefinita:

.. code-block:: php

    // nella classe base Symfony\Component\Validator\Constraint
    public function validatedBy()
    {
        return get_class($this).'Validator';
    }

In altre parole, se si crea un ``Constraint``, ovvero un vincolo, personalizzato (come ``MioVincolo``),
Symfony2, automaticamente, cercherà anche un'altra la classe, ``MioVincoloValidator``
per effettuare la validazione vera e propria.

Anche la classe validatrice è semplice e richiede solo un metodo obbligatorio: ``isValid``.
Si prenda, ad esempio, la classe ``ProtocolValidator``:

.. code-block:: php

    namespace Symfony\Component\Validator\Constraints;
    
    use Symfony\Component\Validator\Constraint;
    use Symfony\Component\Validator\ConstraintValidator;

    class ProtocolValidator extends ConstraintValidator
    {
        public function isValid($value, Constraint $constraint)
        {
            if (in_array($value, $constraint->protocols)) {
                $this->setMessage($constraint->message, array('%protocols%' => $constraint->protocols));

                return true;
            }

            return false;
        }
    }

.. note::

    Non dimenticare di richiamare ``setMessage`` per costruire un messaggio di errore quando
    il valore non è valido.

Validatori di vincoli con dipendenze
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Se il proprio vincolo ha delle dipendenze, come una connessione alla base dati,
sarà necessario configurarlo come servizio nel contenitore delle dipendenze.
Questo servizio dovrà includere il tag ``validator.constraint_validator`` e
l'attributo ``alias``:

.. configuration-block::

    .. code-block:: yaml

        services:
            validator.unique.nome_proprio_validatore:
                class: Nome\Pienamente\Qualificato\Della\Classe\Validatore
                tags:
                    - { name: validator.constraint_validator, alias: nome_alias }

    .. code-block:: xml

        <service id="validator.unique.nome_proprio_validatore" class="Nome\Pienamente\Qualificato\Della\Classe\Validatore">
            <argument type="service" id="doctrine.orm.default_entity_manager" />
            <tag name="validator.constraint_validator" alias="nome_alias" />
        </service>

    .. code-block:: php

        $container
            ->register('validator.unique.nome_proprio_validatore', 'Nome\Pienamente\Qualificato\Della\Classe\Validatore')
            ->addTag('validator.constraint_validator', array('alias' => 'nome_alias'))
        ;

La classe del vincolo dovrà utilizzare l'alias appena definito per riferirsi al
validatore corretto::

    public function validatedBy()
    {
        return 'nome_alias';
    }

Come già detto, Symfony2 cercherà automaticamente una classe il cui nome
sia uguale a quello del vincolo ma con il suffisso ``Validator``. Se il proprio
validatore di vincoli è definito come servizio, è importante che si sovrascriva
il metodo ``validatedBy()``, in modo tale che restituisca l'alias utilizzato
nella definizione del servizio, altrimenti Symfony2 non utilizzerà il servizio di validazione
dei vincoli e istanzierà la classe senza che le dipendenze vengano iniettate.

Validatore con vincolo di classe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Oltre a validare la proprietà di una classe, un vincolo può avere visibilità su una classe,
fornendo un bersaglio::

    public function getTargets()
    {
        return self::CLASS_CONSTRAINT;
    }

In questo modo, il metodo ``isValid()`` del validatore accetta un oggetto come primo parametro::

    class ProtocolClassValidator extends ConstraintValidator
    {
        public function isValid($protocol, Constraint $constraint)
        {
            if ($protocol->getFoo() != $protocol->getBar()) {

                // lega il messaggio di errore alla proprietà foo
                $this->context->addViolationAtSubPath('foo', $constraint->getMessage(), array(), null);

                return true;
            }

            return false;
        }
    }

