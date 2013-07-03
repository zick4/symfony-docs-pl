.. index::
   single: formularze; pola; country

Typ pola: country
=================

Typ ``country`` jest podzbiorem ``ChoiceType`` który wyświetla nazwy i kody
krajów świata. Dodatkową korzyścią jest to, że nazwy krajów wyświetlane są w
języku użytkownika.

Każdej nazwie kraju przypisana jest "wartość" będąca dwuliterowym kodem kraju.

.. note::

   Ustawienie regionalne użytkownika jest ustalane przy użyciu
   :phpmethod:`Locale::getDefault`


W odróżnieniu od typu ``choice``, nie potrzeba tu ustalać opcji ``choices`` lub
``choice_list``, ponieważ ten typ pola automatycznie wykorzystuje predefiniowaną
listę wszystkich krajów świata. Można ręcznie określić kążdą z tych opcji, ale
następnie trzeba użyć typ ``choice`` a nie ``country``.

+------------------+-----------------------------------------------------------------------+
| Renderowane jako | znaczniki mogą różne (zobacz :ref:`forms-reference-choice-tags`)      |
+------------------+-----------------------------------------------------------------------+
| Odziedziczone    | - `multiple`_                                                         |
| opcje            | - `expanded`_                                                         |
|                  | - `preferred_choices`_                                                |
|                  | - `empty_value`_                                                      |
|                  | - `error_bubbling`_                                                   |
|                  | - `required`_                                                         |
|                  | - `label`_                                                            |
|                  | - `read_only`_                                                        |
+------------------+-----------------------------------------------------------------------+
| Rodzic           | :doc:`choice</reference/forms/types/choice>`                          |
+------------------+-----------------------------------------------------------------------+
| Klasa            | :class:`Symfony\\Component\\Form\\Extension\\Core\\Type\\CountryType` |
+------------------+-----------------------------------------------------------------------+

Odziedziczone opcje
-------------------

Opcje te są odziedziczone z typu :doc:`choice</reference/forms/types/choice>`:

.. include:: /reference/forms/types/options/multiple.rst.inc

.. include:: /reference/forms/types/options/expanded.rst.inc

.. include:: /reference/forms/types/options/preferred_choices.rst.inc

.. include:: /reference/forms/types/options/empty_value.rst.inc

.. include:: /reference/forms/types/options/error_bubbling.rst.inc

Opcje te są odziedziczone z typu :doc:`field</reference/forms/types/field>`:

.. include:: /reference/forms/types/options/required.rst.inc

.. include:: /reference/forms/types/options/label.rst.inc

.. include:: /reference/forms/types/options/read_only.rst.inc

.. _`Locale::getDefault()`: http://php.net/manual/en/locale.getdefault.php
