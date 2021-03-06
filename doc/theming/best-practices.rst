======================================
Best practices for writing CKAN themes
======================================

.. _don't use c:

---------------
Don't use ``c``
---------------

As much as possible, avoid accessing the Pylons template context :py:data:`c`
(or :py:data:`tmpl_context`). Use
:doc:`template helper functions <template-helper-functions>` instead.
You can use the :py:class:`~ckan.plugins.interfaces.ITemplateHelpers` plugin
interface to add custom helper functions, see
:ref:`custom template helper functions`.


-----------------
Use ``url_for()``
-----------------

Always use :py:func:`~ckan.lib.helpers.url_for` (available to templates as
``h.url_for()``) when linking to other CKAN pages, instead of hardcoding URLs
like ``<a href="/dataset">``. Links created with
:py:func:`~ckan.lib.helpers.url_for` will update themselves if the URL routing
changes in a new version of CKAN, or if a plugin changes the URL routing.


---------------------------------------------------------------------
Use ``{% trans %}``, ``{% pluralize %}``, ``_()`` and ``ungettext()``
---------------------------------------------------------------------

All user-visible strings should be internationalized, see
:doc:`/contributing/string-i18n`.


-----------------------------------------------------------------
Helper function names should begin with the name of the extension
-----------------------------------------------------------------

Namespacing helper functions in this way avoids accidentally overriding, or
being overriden by, a core helper function, or a helper function from another
extension. For example:

.. literalinclude:: /../ckanext/example_theme/v08_custom_helper_function/plugin.py
   :pyobject: ExampleThemePlugin.get_helpers


.. _snippet filenames best practice:

-------------------------------------------------------------
Snippet filenames should begin with the name of the extension
-------------------------------------------------------------

Namespacing snippets in this way avoids accidentally overriding, or being
overridden by, a core snippet, or a snippet from another extension.
For example::

 snippets/example_theme_most_popular_groups.html


.. _javascript module names best practice:

----------------------------------------------------------------------
|javascript| modules names should begin with the name of the extension
----------------------------------------------------------------------

Namespacing |javascript| modules in this way avoids accidentally overriding, or
being overridden by, a core module, or a module from another extension.  For
example: ``fanstatic/example_theme_popover.js``:

.. literalinclude:: /../ckanext/example_theme/v16_initialize_a_javascript_module/fanstatic/example_theme_popover.js

.. _javascript module docstrings best practice:

-------------------------------------------
|javascript| modules should have docstrings
-------------------------------------------

A |javascript| module should have a docstring at the top of the file, briefly
documentating what the module does and what options it takes. For example:

.. literalinclude:: /../ckanext/example_theme/v17_popover/fanstatic/example_theme_popover.js
   :language: javascript


.. _pubsub unsubscribe best practice:

-------------------------------------------------------------------
JavaScript modules should unsubscribe from events in ``teardown()``
-------------------------------------------------------------------

Any JavaScript module that calls :js:func:`this.sandbox.client.subscribe`
should have a ``teardown()`` function that calls
:js:func:`~this.sandbox.client.unsubscribe`, to prevent memory leaks.
CKAN calls the ``teardown()`` functions of modules when those modules are
removed from the page.

.. _pubsub overuse best practice:

--------------------
Don't overuse pubsub
--------------------

There shouldn't be very many cases where a JavaScript module really needs to
use :ref:`Pubsub <pubsub>`, try to only use it when you really need to.

JavaScript modules in CKAN are designed to be small and loosely-coupled,
for example modules don't share any global variables and don't call
each other's functions. But pubsub offers a way to tightly couple JavaScript
modules together, by making modules depend on multiple events published by
other modules. This can make the code buggy and difficult to understand.


--------------------------------------------
Use ``{% snippet %}``, not ``{% include %}``
--------------------------------------------

Always use CKAN's custom ``{% snippet %}`` tag instead of Jinja's default
``{% include %}`` tag. Snippets can only access certain global variables, and
any variables explicitly passed to them by the calling template. They don't
have access to the full context of the calling template, as included files do.
This makes snippets more reusable, and much easier to debug.


.. _snippet docstrings best practice:

-------------------------------
Snippets should have docstrings
-------------------------------

A snippet should have a docstring comment at the top of the file that briefly
documents what the snippet does and what parameters it requires. For example:

.. literalinclude:: /../ckanext/example_theme/v10_custom_snippet/templates/snippets/example_theme_most_popular_groups.html
   :language: django
