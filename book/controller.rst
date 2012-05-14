.. index::
   single: Kontroler

Kontroler
=========

Kontroler to funkcja PHP, którą tworzysz, aby pobierała informacje z żądania
HTTP, a następnie konstruowała i zwracała odpowiedź HTTP (jako obiekt
``Response`` Symfony2). Odpowiedź może być stroną HTML, dokumentem XML,
serializowaną tablicą JSON, obrazkiem, przekierowaniem, stroną błędu 404
lub czymkolwiek chcesz. Kontroler może zawierać dowolną logikę, jaką *Twoja aplikacja*
potrzebuje, aby wyrenderować zawartość strony.

Aby zobaczyć, jak proste to jest, spójrzmy na kontroler Symfony2 w akcji.
Poniższy kontroler wyrenderuje stronę, która po prostu wyświetli ``Hello world!``::

    use Symfony\Component\HttpFoundation\Response;

    public function helloAction()
    {
        return new Response('Hello world!');
    }

Zadanie kontrolera jest zawsze takie samo: stworzyć i zwrócić obiekt ``Response``.
Po drodze może odczytywać informacje z żądania, wczytywać dane z bazy danych,
wysłać email, czy zapisać informacje w sesji użytkownika. Jednakże w każdym przypadku
kontroler ostatecznie zwróci obiekt ``Response``, który będzie dostarczony z powrotem
do użytkownika.

Nie ma tutaj żadnej magii czy innych wymagań, o które trzeba się martwić! Oto kilka
najczęstszych przypadków:

* *Kontroler A* przygotowuje obiekt ``Response`` reprezentujący zawartość strony głównej
  witryny.

* *Kontroler B* odczytuje parametr ``slug`` z żądania, aby pobrać wpis bloga
  z bazy danych i utworzyć obiekt ``Response``, który wyświetli tego bloga. Jeśli
  ``slug`` nie zostanie znaleziony w bazie danych, kontroler utworzy obiekt ``Response``
  zawierający kod błędu 404.

* *Kontroler C* obsługuje formularz kontaktowy. Odczytuje dane formularza z żądania HTTP,
  zapisuje dane kontaktowe do bazy danych i wysyła email do administratora. W końcu tworzy
  obiekt ``Response``, który przekieruje przeglądarkę klienta do strony z podziękowaniami.

.. index::
   single: Kontroler; Cykl żądanie-kontroler-odpowiedź

Cykl żądanie, kontroler, odpowiedź
----------------------------------

Każde żądanie obsługiwane przez projekt Symfony2 przechodzi ten sam cykl. Framework dba o
powtarzające się czynności i w końcu wykonuje kontroler, który przechowuje Twój kod aplikacji:

#. Każde żądanie jest obsługiwane przez pojedynczy plik front kontrolera (np. ``app.php`` czy
   ``app_dev.php``), który startuje aplikację;

#. ``Router`` odczytuje informacje z żądania (np. URI), znajduje ścieżkę, która pasuje do
   podanych informacji, a następnie odczytuje parametr ``_controller`` ścieżki;

#. Wykonywany jest kontroler przypisany do znalezionej ścieżki, a kod w nim zawarty
   tworzy i zwraca obiekt ``Response``;

#. Nagłówki HTTP i zawartość obiektu ``Response`` są wysyłane z powrotem do klienta.

Tworzenie strony jest równie proste jak utworzenie kontrolera (#3) i stworzenie ścieżki,
która mapuje URL dla tego kontrolera (#2).

.. note::

    Pomimo podobnej nazwy, "front kontroler" zupełnie różni się od "kontrolerów", o których
    mówimy w tym rozdziale. Front kontroler to krótki plik PHP, który znajduje się w katalogu
    web Twojej aplikacji, a przez niego przechodzą wszystkie żądania. Typowa aplikacja posiada
    produkcyjny front kontroler (np. ``app.php``) i deweloperski front kontroler (np. ``app_dev.php``).
    Prawdopodobnie nigdy nie będziesz musiał edytować, podglądać, czy martwić się o front kontrolery
    w Twojej aplikacji.

.. index::
   single: Kontroler; Prosty przykład

Prosty kontroler
----------------

Podczas gdy kontroler może być jakąkolwiek funkcją PHP, metodą obiektu, czy ``Strukturą``,
w Symfony2 kontroler jest zazwyczaj pojedynczą metodą obiektu kontrolera. kontrolery
są również często nazywane *akcjami*.

.. code-block:: php
    :linenos:

    // src/Acme/HelloBundle/Controller/HelloController.php

    namespace Acme\HelloBundle\Controller;
    use Symfony\Component\HttpFoundation\Response;

    class HelloController
    {
        public function indexAction($name)
        {
          return new Response('<html><body>Hello '.$name.'!</body></html>');
        }
    }

.. tip::

    Zauważ, że *kontrolerem* jest metoda ``indexAction``, która zawarta jest
    w *klasie kontrolera* (``HelloController``). Niech nie zmyli Cię nazwenictwo:
    *klasa kontrolera* to po prostu wygodny sposób na grupowanie kilku kontrolerów/akcji
    razem. Zazwyczaj klasa kontrolera przechowuje kilka kontrolerów/akcji (np. ``updateAction``,
    ``deleteAction`` itd.)

Kontroler jest bardzo prosty, ale przeanalizujmy go:

* *linia 3*: Symfony2 korzysta z funkcjonalności przestrzeni nazw PHP 5.3, aby
  nazwać całą klasę kontrolera. Słowo kluczowe ``use`` importuje klasę ``Response``,
  którą nasz kontroler musi zwrócić.

* *linia 6*: Nazwa klasy to połączenie nazwy kontrolera (np. ``Hello``) i słowa ``Controller``.
  Jest to konwencja ujednolicająca kontrolery i pozwalająca na odnoszenie się do nich
  wyłącznie przez pierwszą część ich nazw (np. ``Hello``) w konfiguracji routingu.

* *linia 8*: Każda akcja w klasie kontrolera posiada przyrostek ``Action``, a w konfiguracji
  routingu odnosimy się do niej poprzez nazwę akcji (``index``). W następnym dziale
  utworzysz ścieżkę, która będzie mapować URL do tej akcji. Nauczysz się jak placeholdery
  ścieżki (``{name}``) mogą zostać argumentami metody akcji (``$name``).

* *linia 10*: Kontroler tworzy i zwraca obiekt ``Response``.

.. index::
   single: Kontroler; Ścieżki i kontrolery

Mapowanie URL do kontrolera
---------------------------

Nowy kontroler zwraca prostą stronę HTML. Aby móc zobaczyć tą stronę w Twojej przeglądarce,
musisz utworzyć ścieżkę (ang. route), która zmapuje określony wzór URL do kontrolera:

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        hello:
            pattern:      /hello/{name}
            defaults:     { _controller: AcmeHelloBundle:Hello:index }

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <route id="hello" pattern="/hello/{name}">
            <default key="_controller">AcmeHelloBundle:Hello:index</default>
        </route>

    .. code-block:: php

        // app/config/routing.php
        $collection->add('hello', new Route('/hello/{name}', array(
            '_controller' => 'AcmeHelloBundle:Hello:index',
        )));

Wejście pod ``/hello/ryan`` wykona kontroler ``HelloController::indexAction()``
i przekaże wartość ``ryan`` do zmiennej ``$name``. Tworzenie "strony" to tworzenie
metody kontrolera i powiązanej ścieżki.

Zwróć uwagę na składnię użytą w odwołaniu się do kontrolera ``AcmeHelloBundle:Hello:index``.
Symfony2 używa elastycznej notacji w celu odwoływania się do różnych kontrolerów.
Jest to najczęściej używana składnia, która mówi Symfony2, aby szukać klasy kontrolera
o nazwie ``HelloController`` wewnątrz bundla ``AcmeHelloBundle``. Następnie wykonywana
jest metoda ``indexAction()``.

Aby uzyskać więcej informacji odnośnie formatów odsyłaczy do różnych kontrolerów, zobacz
:ref:`controller-string-syntax`.

.. note::

    W tym przykładzie konfiguracja routingu znajduje się bezpośrednio w katalogu ``app/config/``.
    Lepszym sposobem na organizację ścieżek jest umieszczać każdą trasę w bundlu, do którego
    ona należy. Po więcej informacji sięgnij tutaj: :ref:`routing-include-external-resources`.

.. tip::

    O wiele więcej o routingu możesz dowiedzieć się w rozdziale :doc:`Routing</book/routing>`.

.. index::
   single: Kontroler; Argumenty kontrolera

.. _route-parameters-controller-arguments:

Parametry ścieżki jako argumenty kontrolera
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Wiesz już, że wartość ``AcmeHelloBundle:Hello:index`` parametru ``_controller`` odnosi
się do metody ``HelloController::indexAction()``, która znajduje się w bundlu ``AcmeHelloBundle``.
Zobacz w jaki sposób argumenty są przekazywane do tej metody:

.. code-block:: php

    <?php
    // src/Acme/HelloBundle/Controller/HelloController.php

    namespace Acme\HelloBundle\Controller;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class HelloController extends Controller
    {
        public function indexAction($name)
        {
          // ...
        }
    }

Kontroler ma jeden argument ``$name``, który odpowiada parametrowi ``{name}``
z przypisanej ścieżki (w naszym przypadku ma on wartość ``ryan``). W rzeczywistości,
kiedy uruchamiasz swój kontroler, Symfony2 dopasowuje każdy argument kontrolera
z parametrem ścieżki. Spójrz na poniższy przykład:

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        hello:
            pattern:      /hello/{first_name}/{last_name}
            defaults:     { _controller: AcmeHelloBundle:Hello:index, color: green }

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <route id="hello" pattern="/hello/{first_name}/{last_name}">
            <default key="_controller">AcmeHelloBundle:Hello:index</default>
            <default key="color">green</default>
        </route>

    .. code-block:: php

        // app/config/routing.php
        $collection->add('hello', new Route('/hello/{first_name}/{last_name}', array(
            '_controller' => 'AcmeHelloBundle:Hello:index',
            'color'       => 'green',
        )));

W tym przypadku kontroler może przyjąć kilka argumentów::

    public function indexAction($first_name, $last_name, $color)
    {
        // ...
    }

Zauważ, że obie zmienne (``{first_name}`` i ``{last_name}``) oraz domyślny parametr
``color`` są dostępne w kontrolerze jako jego argumenty. Kiedy trasa jest przypisana,
parametry ścieżki oraz ``wartości domyślne`` są łączone w jedną tablicę, która
jest dostępna dla kontrolera.

Mapowanie parametrów ścieżki do argumentów kontrolera jest łatwe i elastyczne. Pamiętaj
o następujących krokach:

* **Kolejność argumentów kontrolera nie ma znaczenia**

    Symfony potrafi dopasować nazwy parametrów ścieżki do nazw zmiennych w metodzie
    kontrolera. Innymi słowy, Symfony rozumie, że parametr ``{last_name}`` pasuje do
    argumentu ``$last_name``. Argumenty kontrolera mogą być kompletnie pomieszane, a
    i tak będą działać doskonale::

        public function indexAction($last_name, $color, $first_name)
        {
            // ..
        }

* **Każdy wymagany argument kontrolera musi pasować do parametru routingu**

    Poniższy przykład wyrzuci ``RuntimeException``, ponieważ w routingu nie został
    zdefiniowany parametr ``foo``::

        public function indexAction($first_name, $last_name, $color, $foo)
        {
            // ..
        }

    Rozwiązaniem problemu może być przypisanie wartości domyślnej do argumentu. Poniższy
    przykład nie wyrzuci wyjątku::

        public function indexAction($first_name, $last_name, $color, $foo = 'bar')
        {
            // ..
        }

* **Nie wszystkie parametry routingu muszą być argumentami kontrolera**

    Jeśli, na przykład, ``last_name`` nie jest istotny dla Twojego kontrolera,
    możesz go całkowicie pominąć::

        public function indexAction($first_name, $color)
        {
            // ..
        }

.. tip::

    Każda ścieżka posiada również specjalny parametr ``_route``, który jest równoważny
    z nazwą ścieżki, która została dopasowana (np. ``hello``). Rzadko jest to użyteczne,
    ale jest to również dostępne jako argument kontrolera.

.. _book-controller-request-argument:

Żądanie jako argument kontrolera
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Dla Twojej wygody, możesz powiedzieć Symfony, żeby przekazywał obiekt ``Request``
jako argument kontrolera. Jest to przydatne przede wszystkim wtedy, kiedy pracujesz
z formularzami, na przykład::

    use Symfony\Component\HttpFoundation\Request;

    public function updateAction(Request $request)
    {
        $form = $this->createForm(...);

        $form->bindRequest($request);
        // ...
    }

.. index::
   single: Kontroler; Podstawowa klasa kontrolera

Podstawowa klasa kontrolera
---------------------------
Dla ułatwienia, Symfony2 udostępnia podstawową klasę ``Controller``, która pomaga
w najczęstszych zadaniach kontrolerów i daje Twojej klasie kontrolera dostęp
do jakiegokolwiek zasobu, który może potrzebować. Dzięki dziedziczeniu z klasy ``Controller``
możesz uzyskać kilka pomocnych funkcji.

Dodaj instrukcję ``use`` na początku pliku kontrolera, a później zmodyfikuj ``HelloController`` tak,
aby dziedziczył klasę ``Controller``:

.. code-block:: php

    // src/Acme/HelloBundle/Controller/HelloController.php

    namespace Acme\HelloBundle\Controller;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;
    use Symfony\Component\HttpFoundation\Response;

    class HelloController extends Controller
    {
        public function indexAction($name)
        {
          return new Response('<html><body>Hello '.$name.'!</body></html>');
        }
    }

W rzeczywistości niczego to nie zmienia w sposobie działania Twojego kontrolera.
W następnym dziale dowiesz się o metodach helperów, które otrzymujesz z klasą
podstawowego kontrolera. Te metody to po prostu skróty do rdzennych funkcji Symfony2,
które są dla Ciebie dostępne niezależnie od tego, czy używasz podstawowej klasy ``Controller``,
czy nie. Świetnym sposobem, aby zobaczyć rdzenne funkcje w akcji to spojrzeć samemu
do pliku klasy :class:`Symfony\\Bundle\\FrameworkBundle\\Controller\\Controller`.

.. tip::

    Dziedziczenie podstawowej klasy w Symfony jest *opcjonalne*; zawiera ona przydatne skróty,
    lecz nic niezbędnego. Możesz również dziedziczyć ``Symfony\Component\DependencyInjection\ContainerAware``.
    Kontener usługi będzie wtedy dostępny przez właściwość ``container``.

.. note::

    Możesz również definiować własne :doc:`Kontrolery jako usługi</cookbook/controller/service>`.

.. index::
   single: Kontroler; Powszechne zadania

Powszechne zadania kontrolera
-----------------------------

Choć kontroler może robić dosłownie wszystko, większość z nich będzie wykonywać
te same podstawowe zadania w kółko. Te zadania, takie jak przekierowania, forwardowanie,
renderowanie szablonów, czy uzyskiwanie dostępu do rdzennych usług, w Symfony2 są bardzo
łatwe w użyciu.

.. index::
   single: Kontroler; Przekierowania

Przekierowania
~~~~~~~~~~~~~~

Jeśli hccesz przekierować użytkownika na inną stronę, użyj metody ``redirect()``::

    public function indexAction()
    {
        return $this->redirect($this->generateUrl('homepage'));
    }

Metoda ``generateUrl`` jest po prostu helperem, który generuje URL dla podanej ścieżki.
Po więcej informacji sięgnij do rozdziału :doc:`Routing </book/routing>`.

Domyślnie, metoda ``redirect()`` dokonuje przekierowania 302 (tymczasowe, ang. temporary).
Aby wykonać przekierowanie 301 (trwałe, ang. permanent), zmodyfikuj drugi argument::

    public function indexAction()
    {
        return $this->redirect($this->generateUrl('homepage'), 301);
    }

.. tip::

    Metoda ``redirect()`` to po prostu skrót, który tworzy obiekt ``Response``,
    którego zadaniem jest przekierowanie użytkownika. Jest to równoznaczne z:

    .. code-block:: php

        use Symfony\Component\HttpFoundation\RedirectResponse;

        return new RedirectResponse($this->generateUrl('homepage'));

.. index::
   single: Kontroler; Forwardowanie

Forwardowanie
~~~~~~~~~~~~~

Możesz również w łatwy sposów forwardować do innego kontrolera wewnętrznie za pomocą
metody ``forward()``. Zamiast przekierowywania przeglądarki użytkownika, tworzy ona
wewnętrzne pod-żądanie (ang. sub-request), które uruchamia określony kontroler. Metoda
``forward()`` zwraca obiekt ``Response``, który jest zwracany przez ten kontroler::

    public function indexAction($name)
    {
        $response = $this->forward('AcmeHelloBundle:Hello:fancy', array(
            'name'  => $name,
            'color' => 'green'
        ));

        // dalej zmodyfikuj odpowiedź, lub ją zwróć bezpośrednio

        return $response;
    }

Zauważ, że metoda `forward()` używa takiej samej reprezentacji jak kontroler
użyty w konfiguracji routingu. W tym wypadku, docelową klasą kontrolera będzie
``HelloController`` wewnątrz ``AcmeHelloBundle``. Tablica przekazana do metody
zostanie argumentami wynikowego kontrolera. Taki sam interfejs jest używany podczas
zagnieżdżania kontrolerów w szablonach (zobacz :ref:`templating-embedding-controller`).
Metoda docelowego kontrolera powinna wyglądać mniej więcej tak, jak poniżej::

    public function fancyAction($name, $color)
    {
        // ... create and return a Response object
    }

And just like when creating a controller for a route, the order of the arguments
to ``fancyAction`` doesn't matter. Symfony2 matches the index key names
(e.g. ``name``) with the method argument names (e.g. ``$name``). If you
change the order of the arguments, Symfony2 will still pass the correct
value to each variable.

.. tip::

    Like other base ``Controller`` methods, the ``forward`` method is just
    a shortcut for core Symfony2 functionality. A forward can be accomplished
    directly via the ``http_kernel`` service. A forward returns a ``Response``
    object::

        $httpKernel = $this->container->get('http_kernel');
        $response = $httpKernel->forward('AcmeHelloBundle:Hello:fancy', array(
            'name'  => $name,
            'color' => 'green',
        ));

.. index::
   single: Controller; Rendering templates

.. _controller-rendering-templates:

Rendering Templates
~~~~~~~~~~~~~~~~~~~

Though not a requirement, most controllers will ultimately render a template
that's responsible for generating the HTML (or other format) for the controller.
The ``renderView()`` method renders a template and returns its content. The
content from the template can be used to create a ``Response`` object::

    $content = $this->renderView('AcmeHelloBundle:Hello:index.html.twig', array('name' => $name));

    return new Response($content);

This can even be done in just one step with the ``render()`` method, which
returns a ``Response`` object containing the content from the template::

    return $this->render('AcmeHelloBundle:Hello:index.html.twig', array('name' => $name));

In both cases, the ``Resources/views/Hello/index.html.twig`` template inside
the ``AcmeHelloBundle`` will be rendered.

The Symfony templating engine is explained in great detail in the
:doc:`Templating </book/templating>` chapter.

.. tip::

    The ``renderView`` method is a shortcut to direct use of the ``templating``
    service. The ``templating`` service can also be used directly::

        $templating = $this->get('templating');
        $content = $templating->render('AcmeHelloBundle:Hello:index.html.twig', array('name' => $name));

.. index::
   single: Controller; Accessing services

Accessing other Services
~~~~~~~~~~~~~~~~~~~~~~~~

When extending the base controller class, you can access any Symfony2 service
via the ``get()`` method. Here are several common services you might need::

    $request = $this->getRequest();

    $templating = $this->get('templating');

    $router = $this->get('router');

    $mailer = $this->get('mailer');

There are countless other services available and you are encouraged to define
your own. To list all available services, use the ``container:debug`` console
command:

.. code-block:: bash

    php app/console container:debug

For more information, see the :doc:`/book/service_container` chapter.

.. index::
   single: Controller; Managing errors
   single: Controller; 404 pages

Managing Errors and 404 Pages
-----------------------------

When things are not found, you should play well with the HTTP protocol and
return a 404 response. To do this, you'll throw a special type of exception.
If you're extending the base controller class, do the following::

    public function indexAction()
    {
        $product = // retrieve the object from database
        if (!$product) {
            throw $this->createNotFoundException('The product does not exist');
        }

        return $this->render(...);
    }

The ``createNotFoundException()`` method creates a special ``NotFoundHttpException``
object, which ultimately triggers a 404 HTTP response inside Symfony.

Of course, you're free to throw any ``Exception`` class in your controller -
Symfony2 will automatically return a 500 HTTP response code.

.. code-block:: php

    throw new \Exception('Something went wrong!');

In every case, a styled error page is shown to the end user and a full debug
error page is shown to the developer (when viewing the page in debug mode).
Both of these error pages can be customized. For details, read the
":doc:`/cookbook/controller/error_pages`" cookbook recipe.

.. index::
   single: Controller; The session
   single: Session

Managing the Session
--------------------

Symfony2 provides a nice session object that you can use to store information
about the user (be it a real person using a browser, a bot, or a web service)
between requests. By default, Symfony2 stores the attributes in a cookie
by using the native PHP sessions.

Storing and retrieving information from the session can be easily achieved
from any controller::

    $session = $this->getRequest()->getSession();

    // store an attribute for reuse during a later user request
    $session->set('foo', 'bar');

    // in another controller for another request
    $foo = $session->get('foo');

    // use a default value of the key doesn't exist
    $filters = $session->set('filters', array());

These attributes will remain on the user for the remainder of that user's
session.

.. index::
   single Session; Flash messages

Flash Messages
~~~~~~~~~~~~~~

You can also store small messages that will be stored on the user's session
for exactly one additional request. This is useful when processing a form:
you want to redirect and have a special message shown on the *next* request.
These types of messages are called "flash" messages.

For example, imagine you're processing a form submit::

    public function updateAction()
    {
        $form = $this->createForm(...);

        $form->bindRequest($this->getRequest());
        if ($form->isValid()) {
            // do some sort of processing

            $this->get('session')->getFlashBag()->add('notice', 'Your changes were saved!');

            return $this->redirect($this->generateUrl(...));
        }

        return $this->render(...);
    }

After processing the request, the controller sets a ``notice`` flash message
and then redirects. The name (``notice``) isn't significant - it's just what
you're using to identify the type of the message.

In the template of the next action, the following code could be used to render
the ``notice`` message:

.. configuration-block::

    .. code-block:: html+jinja

        {% for flashMessage in app.session.flashbag.get('notice') %}
            <div class="flash-notice">
                {{ flashMessage }}
            </div>
        {% endfor %}

    .. code-block:: php

        <?php foreach ($view['session']->getFlashBag()->get('notice') as $message): ?>
            <div class="flash-notice">
                <?php echo "<div class='flash-error'>$message</div>" ?>
            </div>
        <?php endforeach; ?>

By design, flash messages are meant to live for exactly one request (they're
"gone in a flash"). They're designed to be used across redirects exactly as
you've done in this example.

.. index::
   single: Controller; Response object

The Response Object
-------------------

The only requirement for a controller is to return a ``Response`` object. The
:class:`Symfony\\Component\\HttpFoundation\\Response` class is a PHP
abstraction around the HTTP response - the text-based message filled with HTTP
headers and content that's sent back to the client::

    // create a simple Response with a 200 status code (the default)
    $response = new Response('Hello '.$name, 200);

    // create a JSON-response with a 200 status code
    $response = new Response(json_encode(array('name' => $name)));
    $response->headers->set('Content-Type', 'application/json');

.. tip::

    The ``headers`` property is a
    :class:`Symfony\\Component\\HttpFoundation\\HeaderBag` object with several
    useful methods for reading and mutating the ``Response`` headers. The
    header names are normalized so that using ``Content-Type`` is equivalent
    to ``content-type`` or even ``content_type``.

.. index::
   single: Controller; Request object

The Request Object
------------------

Besides the values of the routing placeholders, the controller also has access
to the ``Request`` object when extending the base ``Controller`` class::

    $request = $this->getRequest();

    $request->isXmlHttpRequest(); // is it an Ajax request?

    $request->getPreferredLanguage(array('en', 'fr'));

    $request->query->get('page'); // get a $_GET parameter

    $request->request->get('page'); // get a $_POST parameter

Like the ``Response`` object, the request headers are stored in a ``HeaderBag``
object and are easily accessible.

Final Thoughts
--------------

Whenever you create a page, you'll ultimately need to write some code that
contains the logic for that page. In Symfony, this is called a controller,
and it's a PHP function that can do anything it needs in order to return
the final ``Response`` object that will be returned to the user.

To make life easier, you can choose to extend a base ``Controller`` class,
which contains shortcut methods for many common controller tasks. For example,
since you don't want to put HTML code in your controller, you can use
the ``render()`` method to render and return the content from a template.

In other chapters, you'll see how the controller can be used to persist and
fetch objects from a database, process form submissions, handle caching and
more.

Learn more from the Cookbook
----------------------------

* :doc:`/cookbook/controller/error_pages`
* :doc:`/cookbook/controller/service`