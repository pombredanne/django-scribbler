Getting Started
====================================

Below are the basic steps need to get django-scribbler integrated into your
Django project.


Configure Settings
------------------------------------

You need to include ``scribbler`` to your installed apps. django-scribbler requires
``django.contrib.auth`` which in turn requires ``django.contrib.sessions``
which are enabled in Django by default. You will also need to include a context processor
to include the current request in the template context.

.. code-block:: python

    INSTALLED_APPS = (
        # Required contrib apps
        'django.contrib.auth',
        'django.contrib.sessions',
        # Other installed apps would go here
        'scribbler',
    )

    TEMPLATE_CONTEXT_PROCESSORS = (
        # Other context processors would go here
        'django.core.context_processors.request',
    )

Note that ``TEMPLATE_CONTEXT_PROCESSORS`` is not included in the default settings
created by ``startproject``. You should take care to ensure that the default
context processors are included in this list. For a list of default
``TEMPLATE_CONTEXT_PROCESSORS`` please see 
`the official Django docs <https://docs.djangoproject.com/en/1.4/ref/settings/#template-context-processors>`_.

For the context processor to have any effect you need to make sure that the template
is rendered using a RequestContext. This is done for you with the
`render <https://docs.djangoproject.com/en/1.4/topics/http/shortcuts/#render>`_ shortcut.


Configure Urls
------------------------------------

You should include the scribbler urls in your root url patterns.

.. code-block:: python

    urlpatterns = patterns('',
        # Other url patterns would go here
        url(r'^scribbler/', include('scribbler.urls')),
    )


Create Database Tables
------------------------------------

You'll need to create the necessary database tables for storing scribble content.
This is done with the ``syncdb`` management command built into Django::

    python manage.py syncdb

django-all-access uses `South <http://south.aeracode.org/>`_ to handle database migrations. 
If you are also using South then you should run ``migrate`` instead::

    python manage.py migrate scribbler


User Permissions
------------------------------------

To edit scribbles on the front-end users must have the ``scribbler.add_scribble``
and ``scribbler.change_scribble`` permissions. You can configure uses to have
these permissions through the users section of the Django admin. Superusers have
all of these permissions by default.


Include Static Resources
------------------------------------

django-scribbler includes both CSS and JS resources which need to be included in your
templates to handle the front-end content management. Since you may want to include
scribbles on any page on your site these should be included in your base template.

.. code-block:: html

    <link rel="stylesheet" href="{{ STATIC_URL }}scribbler/css/codemirror.css">
    <link rel="stylesheet" href="{{ STATIC_URL }}scribbler/css/scribbler.css">
    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
    <script src="{{ STATIC_URL }}scribbler/js/codemirror.js"></script>
    <script src="{{ STATIC_URL }}scribbler/js/xml.js"></script>
    <script src="{{ STATIC_URL }}scribbler/js/javascript.js"></script>
    <script src="{{ STATIC_URL }}scribbler/js/css.js"></script>
    <script src="{{ STATIC_URL }}scribbler/js/scribbler.js"></script>

Please note that the scribble javascript requires jQuery which is not included with
the project. The above example uses the Google hosted CDN which is recommended.


Place Scribbles in Your Template
------------------------------------

You are now ready to place the scribble content blocks throughout your templates.
This is done with the ``scribble`` block tag. It takes one argument which is the
slug name for the scribble. Slugs must be unique per url/slug pair. That means you
cannot use the same slug more than once in the template but you can use the same
slug in different templates as long as they are rendered on different urls.

.. code-block:: html

    {% load scribbler_tags %}
    {% scribble 'header' %}
        <p>Blip {% now 'Y' %} {{ STATIC_URL|upper }}</p>
    {% endscribble %}

The content inside the block is the default content that will be rendered if a
matching scribble in the database is not found.

.. note::

    Scribble content can be any valid Django template. However the content does
    not include all of the context of the template. Only the context provided
    by the set of ``TEMPLATE_CONTEXT_PROCESSORS``.

That should be enough to get you up and running with django-scribbler.