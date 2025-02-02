===============
django-guardian
===============

.. image:: https://github.com/django-guardian/django-guardian/workflows/Tests/badge.svg?branch=devel
  :target: https://github.com/django-guardian/django-guardian/actions/workflows/tests.yml

.. image:: https://img.shields.io/pypi/v/django-guardian.svg
    :target: https://pypi.python.org/pypi/django-guardian

.. image:: https://img.shields.io/pypi/pyversions/django-guardian.svg
    :target: https://pypi.python.org/pypi/django-guardian

``django-guardian`` is an implementation of per object permissions [1]_ on top
of Django's authorization backend

Documentation
-------------

Online documentation is available at https://django-guardian.readthedocs.io/.

Requirements
------------

* Python 3.8+
* A supported version of Django (currently 3.2+)

GitHub Actions run tests against Django versions 3.2, 4.1, 4.2, 5.0 and main.

Installation
------------

To install ``django-guardian`` simply run::

    pip install django-guardian

Configuration
-------------

We need to hook ``django-guardian`` into our project.

1. Put ``guardian`` into your ``INSTALLED_APPS`` at settings module:

.. code:: python

    INSTALLED_APPS = (
     ...
     'guardian',
    )

2. Add extra authorization backend to your ``settings.py``:

.. code:: python

    AUTHENTICATION_BACKENDS = (
        'django.contrib.auth.backends.ModelBackend', # default
        'guardian.backends.ObjectPermissionBackend',
    )

3. Create ``guardian`` database tables by running::

     python manage.py migrate

Usage
-----

After installation and project hooks we can finally use object permissions
with Django_.

Lets start really quickly:

.. code:: python

      >>> from django.contrib.auth.models import User, Group
      >>> jack = User.objects.create_user('jack', 'jack@example.com', 'topsecretagentjack')
      >>> admins = Group.objects.create(name='admins')
      >>> jack.has_perm('change_group', admins)
      False
      >>> from guardian.models import UserObjectPermission
      >>> UserObjectPermission.objects.assign_perm('change_group', jack, obj=admins)
      <UserObjectPermission: admins | jack | change_group>
      >>> jack.has_perm('change_group', admins)
      True

Of course our agent jack here would not be able to *change_group* globally:

.. code:: python

    >>> jack.has_perm('change_group')
    False

Admin integration
-----------------

Replace ``admin.ModelAdmin`` with ``GuardedModelAdmin`` for those models
which should have object permissions support within admin panel.

For example:

.. code:: python

    from django.contrib import admin
    from myapp.models import Author
    from guardian.admin import GuardedModelAdmin

    # Old way:
    #class AuthorAdmin(admin.ModelAdmin):
    #    pass

    # With object permissions support
    class AuthorAdmin(GuardedModelAdmin):
        pass

    admin.site.register(Author, AuthorAdmin)


.. [1] Great paper about this feature is available at `djangoadvent articles <https://github.com/djangoadvent/djangoadvent-articles/blob/master/1.2/06_object-permissions.rst>`_.

.. _Django: http://www.djangoproject.com/
