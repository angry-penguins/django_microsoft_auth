=====
Usage
=====

Quickstart
----------

1. Install `Django <https://docs.djangoproject.com/en/1.11/topics/install/>`_
2. Install and configure the `Sites framework <https://docs.djangoproject.com/en/2.1/ref/contrib/sites/#enabling-the-sites-framework>`_

    .. important::

        **Make sure you update the domain of the active SITE_ID**

        This needs to match the host (hostname + post) that you are using to
        access the Django site with. The easiest way to do this to go to
        `/admin/sites/site/1/change/` if you have the admin site enabled.

3. Create a `Microsoft OAuth Application <https://apps.dev.microsoft.com/>`_

    .. important::

        You will need Client ID and an Application Secret for step 5. Make sure
        you generate these and store them somewhere.

        You will also need a `Web Platform` with `Allow Implicit Flow` and a
        valid Redirect URL. This **must** match the absolute URL of your
        `microsoft_auth:auth-callback` view. By default this would be
        `https://<your-domain>/microsoft/auth-callback/`.

        This URL **must be HTTPS** unless your hostname is `localhost`.
        `localhost` can **only** be used if `DEBUG` is set to `True`.
        Microsoft only allows HTTP authentication if the hostname is
        `localhost`.

        Add `User.Read` under `Delegated Permissions`

4. Install package from PyPi

.. code-block:: console

    $ pip install django_microsoft_auth

5. Add the following to your `settings.py`

.. code-block:: python3

    INSTALLED_APPS = [
        # other apps...
        'django.contrib.sites',
        'microsoft_auth',
    ]

    TEMPLATES = [
        {
            # other template settings...
            'OPTIONS': {
                'context_processors': [
                    # other context_processors...
                    'microsoft_auth.context_processors.microsoft',
                ],
            },
        },
    ]

    AUTHENTICATION_BACKENDS = [
        'microsoft_auth.backends.MicrosoftAuthenticationBackend',
        'django.contrib.auth.backends.ModelBackend' # if you also want to use Django's authentication
        # I recommend keeping this with at least one database superuser in case of unable to use others
    ]

    # values you got from step 2 from your Mirosoft app
    MICROSOFT_AUTH_CLIENT_ID = 'your-client-id-from-apps.dev.microsoft.com'
    MICROSOFT_AUTH_CLIENT_SECRET = 'your-client-secret-from-apps.dev.microsoft.com'

    # pick one MICROSOFT_AUTH_LOGIN_TYPE value
    # Microsoft authentication
    # include Microsoft Accounts, Office 365 Enterpirse and Azure AD accounts
    MICROSOFT_AUTH_LOGIN_TYPE = 'ma'

    # Xbox Live authentication
    MICROSOFT_AUTH_LOGIN_TYPE = 'xbl'  # Xbox Live authentication



6. Add the following to your `urls.py`

.. code-block:: python3

    urlpatterns = [
        # other urlpatterns...
        path('microsoft/', include('microsoft_auth.urls', namespace='microsoft')),
    ]

7. Run migrations

.. code-block:: console

    $ python manage.py migrate

8. Start site and goto `/admin` to and logout if you are logged in.
9. Login as `Microsoft/Office 365/Xbox Live` user. It will fail. This will
   automatically create your new user.
10. Login as a `Password` user with access to change user accounts.
11. Go to `Admin -> Users` and edit your Microsoft user to have any permissions
    you want as you normally.
12. See `microsoft_auth/templates/microsoft/admin_login.html` for details
    examples on making a Login form.
