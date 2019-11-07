=============================
Django Mail Admin
=============================

The one and only django app to receive & send mail with templates and multiple configurations.


Screenshots
-----------

.. image:: https://github.com/Bearle/django_mail_admin/blob/master/screenshots/1.jpg?raw=true
.. image:: https://github.com/Bearle/django_mail_admin/blob/master/screenshots/2.jpg?raw=true

Features
--------

* Everything django-mailbox has
* Everything django-post-office has
* Database configurations - activate an outbox to send from, activate a mailbox to receive from
* Templates
* Translatable
* Mailings - using send_many() or 'cc' and 'bcc' or even recipients - all of those accept comma-separated lists of emails

Dependencies
============

* `django >= 1.9 <http://djangoproject.com/>`_
* `django-jsonfield <https://github.com/bradjasper/django-jsonfield>`_

Documentation
-------------

The full documentation is at https://django_mail_admin.readthedocs.io.

Quickstart
----------

**Q**: What versions of Django/Python are supported?

Install django mail admin::

    pip install django_mail_admin

Add it to your `INSTALLED_APPS`:

.. code-block:: python

    INSTALLED_APPS = (
        ...
        'django_mail_admin',
        ...
    )

* Run ``migrate``::

    python manage.py migrate django_mail_admin

* Set ``django_mail_admin.backends.CustomEmailBackend`` as your ``EMAIL_BACKEND`` in django's ``settings.py``::

    EMAIL_BACKEND = 'django_mail_admin.backends.CustomEmailBackend'


* Set cron/Celery/RQ job to send/receive email, e.g. ::

    * * * * * (cd $PROJECT; python manage.py send_queued_mail --processes=1 >> $PROJECT/cron_mail.log 2>&1)
    * * * * * (cd $PROJECT; python manage.py get_new_mail >> $PROJECT/cron_mail_receive.log 2>&1)
    0 1 * * * (cd $PROJECT; python manage.py cleanup_mail --days=30 >> $PROJECT/cron_mail_cleanup.log 2>&1)

.. note::

   Once you have entered a mailbox to receive emails, you can easily verify that you
   have properly configured your mailbox by either:

   * From the Django Admin, using the 'Get New Mail' action from the action
     dropdown on the Mailbox changelist
   * *Or* from a shell opened to your project's directory, using the
     ``get_new_mail`` management command by running::

       python manage.py get_new_mail

   If you have also configured the Outbox, you can verify that it is working, e.g. ::

        from django_mail_admin import mail, models

        mail.send(
            'from@example.com',
            'recipient@example.com', # List of email addresses also accepted
            subject='My email',
            message='Hi there!',
            priority=models.PRIORITY.now,
            html_message='Hi <strong>there</strong>!',
        )

Custom Email Backends
---------------------

By default, ``django_mail_admin`` uses custom Email Backends that looks up for Outbox models in database. If you want to
use a different backend, you can do so by configuring ``BACKENDS``, though you will not be able to use Outboxes and will have to set EMAIL_HOST etc. in django's ``settings.py``.

For example if you want to use `django-ses <https://github.com/hmarr/django-ses>`_::

    DJANGO_MAIL_ADMIN = {
        'BACKENDS': {
            'default': 'django_mail_admin.backends.CustomEmailBackend',
            'smtp': 'django.core.mail.backends.smtp.EmailBackend',
            'ses': 'django_ses.SESBackend',
        }
    }

You can then choose what backend you want to use when sending mail:

.. code-block:: python

    # If you omit `backend_alias` argument, `default` will be used
    mail.send(
        'from@example.com',
        ['recipient@example.com'],
        subject='Hello',
    )

    # If you want to send using `ses` backend
    mail.send(
        'from@example.com',
        ['recipient@example.com'],
        subject='Hello',
        backend='ses',
    )




Optional requirements
---------------------

1. `django_admin_row_actions` for some useful actions in the admin interface
2. `requests` & social_django for Gmail

Running Tests
-------------

Does the code actually work?

::

    source <YOURVIRTUALENV>/bin/activate
    (myenv) $ pip install tox
    (myenv) $ tox

Credits
-------

Tools used in rendering this package:

*  Cookiecutter_
*  `cookiecutter-djangopackage`_

.. _Cookiecutter: https://github.com/audreyr/cookiecutter
.. _`cookiecutter-djangopackage`: https://github.com/pydanny/cookiecutter-djangopackage
