=============================
django-model-admin-reorder
=============================


.. image:: http://img.shields.io/travis/mishbahr/django-model-admin-reorder.svg?style=flat-square
    :target: https://travis-ci.org/fsecada/django-model-admin-reorder/

.. image:: http://img.shields.io/pypi/v/django-model-admin-reorder.svg?style=flat-square
    :target: https://pypi.python.org/pypi/django-model-admin-reorder/
    :alt: Latest Version

.. image:: http://img.shields.io/pypi/dm/django-model-admin-reorder.svg?style=flat-square
    :target: https://pypi.python.org/pypi/django-model-admin-reorder/
    :alt: Downloads

.. image:: http://img.shields.io/pypi/l/django-model-admin-reorder.svg?style=flat-square
    :target: https://pypi.python.org/pypi/django-model-admin-reorder/
    :alt: License


Custom ordering for the apps and models in the admin app. You can also rename, cross link or exclude models from the app list.

-- To Be Updated


Disclaimer
---------
This is a fork that's intended to provide support for Django v.3.2 and beyond, as well as new versions of python. The main repo has not been updated since 2018 and the developer is MIA. There are numerous forks but no dedicated Pypi distros. For production-level work in enterprise settings, this can be a significant blocker towards keeping code up to date.

Special thanks goes out to `Jrago <https://github.com/Jragon>`_ for his fork and work. Without that, I would have had to spend numerous minutes updating and linting code.

Features
--------

* Reorder apps in admin index - this will allow you to position most used apps in top of the page, instead of listing apps alphabetically. e.g. ``sites`` app before the ``auth`` app

* Rename app labels easily for third party apps without having to modify the source code. e.g. rename ``auth`` app to ``Authorisation`` for the django admin app.

* Split large apps into smaller groups of models.

* Reorder models within an app. e.g. ``auth.User`` model before the ``auth.Group`` model.

* Exclude any of the models from the app list. e.g. Exclude ``auth.Group`` from the app list. Please note this only excludes the model from the app list and it doesn't protect it from access via url.

* Cross link models from multiple apps. e.g. Add ``sites.Site`` model to the ``auth`` app.

* Rename individual models in the app list. e.g. rename ``auth.User`` from ``User`` to ``Staff``


Documentation
-------------

The full documentation is at https://django-model-admin-reorder.readthedocs.org.


Install
----------

Install django-model-admin-reorder:

.. code-block:: bash

    pip install django-model-admin-reorder


Configuration
-------------

1. Add `admin_reorder` to `INSTALLED_APPS`:

   .. code-block:: python

    INSTALLED_APPS = (
        ...
        'admin_reorder',
        ...
    )


2. Add the `ModelAdminReorder` to `MIDDLEWARE_CLASSES`:

   .. code-block:: python

    MIDDLEWARE_CLASSES = (
        ...
        'admin_reorder.middleware.ModelAdminReorder',
        ...
    )


3. Add the setting `ADMIN_REORDER` to your settings.py:

   .. code-block:: python

    ADMIN_REORDER = (
        # Keep original label and models
        'sites',

        # Rename app
        {'app': 'auth', 'label': 'Authorisation'},

        # Reorder app models
        {'app': 'auth', 'models': ('auth.User', 'auth.Group')},

        # Exclude models
        {'app': 'auth', 'models': ('auth.User', )},

        # Cross-linked models
        {'app': 'auth', 'models': ('auth.User', 'sites.Site')},

        # models with custom name
        {'app': 'auth', 'models': (
            'auth.Group',
            {'model': 'auth.User', 'label': 'Staff'},
        )},
    )


If our project has multiple admin sites:
========================================

1. Add `admin_reorder` to `INSTALLED_APPS`:

   .. code-block:: python

    INSTALLED_APPS = (
        ...
        'admin_reorder',
        ...
    )

2. Create your own middleware inherited from `ModelAdminReorderMiddlewareMixin`, define `settings_variable_name` and methods `get_admin_site` and `get_admin_site_url_names`

  .. code-block:: python

    from admin_reorder.middleware import ModelAdminReorderMiddlewareMixin
    from PROJECT_NAME.admin import short_admin_site

    class ReorderShortAdminSiteMiddleware(ModelAdminReorderMiddlewareMixin):
        settings_variable_name = 'SHORT_ADMIN_REORDER'

        def get_admin_site(self):
            return short_admin_site

        def get_admin_site_url_names(self):
            names = super().get_admin_site_url_names()
            names.append('short_admin_index')
            return names


3. Add the `ReorderShortAdminSiteMiddleware` to `MIDDLEWARE_CLASSES`:

   .. code-block:: python

    MIDDLEWARE_CLASSES = (
        ...
        'PROJECT_NAME.middleware.ReorderShortAdminSiteMiddleware',
        ...
    )
