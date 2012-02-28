.. index::
   single: Page creation

Tworzenie stron w Symfony2
==========================

Tworzenie nowej strony w Symfony2 to prosty, dwuetapowy proces:

* *Utw�rz route*: Route definiuje URL (np. /o-nas) do Twojej strony oraz okre�la kontroler (kt�ry jest funkcj� PHP) kt�ry Symfony2 powinno wykona�, kiedy URL przychodz�cego ��dania pasuje do wzoru route'a.

* *Utw�rz kontroler*: Kontroler jest funkcj� PHP, kt�ra pobiera przychodz�ce ��danie i transformuje je w obiekt *Odpowiedzi* Symfony2, kt�ry jest zwracany u�ytkownikowi.


To proste podej�cie jest pi�kne, gdy� pasuje do sposobu funkcjonowania Sieci. Ka�da interakcja w Sieci jest inicjowana przez ��danie HTTP. Zadaniem Twojej aplikacji jest po prostu zinterpretowa� ��danie i zwr�ci� w�a�ciw� odpowied� HTTP.

Symfony2 stosuje t� filozofi� i dostarcza Ci narz�dzia i konwencje, kt�re pomog� Ci utrzyma� swoj� aplikacj� zorganizowan� wraz ze wzrostem liczby u�ytkownik�w i z�o�ono�ci.

Brzmi wystarczaj�co prosto? Zag��bmy si� w tym!

Strona "Witaj Symfony!"
-----------------------

Zacznijmy od klasycznej aplikacji "Witaj �wiecie!". Kiedy sko�czysz, u�ytkownik b�dzie mia� mo�liwo�� ujrze� osobiste pozdrowienie (np. "Witaj Symfony") wchodz�c w poni�szy URL:

.. code-block:: text

    http://localhost/app_dev.php/hello/Symfony

W rzeczywisto�ci b�dziesz m�g� zast�pi� *Symfony* innym imieniem, kt�re ma by� pozdrowione. Aby utworzy� stron�, wykonaj prosty, dwuetapowy proces.