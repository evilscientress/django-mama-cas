.. _settings:

Settings
========

.. currentmodule:: django.conf.settings

None of these settings are required and have sane defaults, but may be used to
customize behavior and improve security.

.. attribute:: MAMA_CAS_ALLOW_AUTH_WARN

   :default: ``False``

   If set, allows the user to control transparency of the single sign-on
   process. When enabled, an additional checkbox will be displayed on the
   login form.

.. attribute:: MAMA_CAS_ATTRIBUTE_CALLBACKS

   :default: ``()``

   A tuple of dotted paths to callables that each provide a dictionary of
   name and attribute values. These values are merged together and included
   with a service or proxy validation success. Each callable is provided the
   authenticated ``User`` and the service URL as arguments. For example::

      # In settings.py
      MAMA_CAS_ATTRIBUTE_CALLBACKS = ('path.to.custom_attributes',)

      # In a convenient location
      def custom_attributes(user, service):
          return {'givenName': user.first_name, 'email': user.email}

   Two callbacks are provided to cover basic use cases and serve as
   examples for custom callbacks:

   ``mama_cas.callbacks.user_name_attributes``
      Returns name-related fields using get_username(), get_full_name() and
      get_short_name().

   ``mama_cas.callbacks.user_model_attributes``
      Returns all fields on the user object, except for ``id`` and
      ``password``.

   .. warning::

      This setting has been deprecated in favor of per-service configuration
      with MAMA_CAS_SERVICES.

.. attribute:: MAMA_CAS_ENABLE_SINGLE_SIGN_OUT

   :default: ``False``

   If set, causes single logout requests to be sent to all accessed services
   when a user logs out. It is up to each service to handle these requests
   and terminate the session appropriately.

   .. note::

      By default, the single logout requests are sent synchronously. If
      `requests-futures`_ is installed, they are sent asynchronously.

   .. warning::

      This setting has been deprecated in favor of per-service configuration
      with MAMA_CAS_SERVICES.

.. attribute:: MAMA_CAS_SINGLE_SIGN_OUT_SESSION_LIFETIME

   :default: ``None``

   Session lifetime in hours of the services using the sso server.

   If set, causes single logout requests to be sent to all accessed services
   within the last x hours when a user logs out. If the setting is not set,
   the old behaviour is used and single sign out requests are sent for all
   services that have been accessed since the last login of the user.

   .. warning::

      If the setting is not set the old behaviour is used which might
      leave out services when the user is logged in multiple times.
      See `issue #83`_ for details.

.. attribute:: MAMA_CAS_FOLLOW_LOGOUT_URL

   :default: ``True``

   Controls the client redirection behavior at logout when the ``service``
   parameter is provided. When this setting is ``True`` and the parameter
   is present, the client will be redirected to the specified URL. When
   this setting is ``False`` or the parameter is not provided, the client
   is redirected to the login page.

.. attribute:: MAMA_CAS_SERVICE_BACKENDS

   :default: ``['mama_cas.services.backends.SettingsBackend']``

   A list of paths to service backends.

.. attribute:: MAMA_CAS_SERVICES

   :default: ``[]``

   A list containing all allowed services for the server. Each list item is
   a dictionary containing the configuration for each service. For example::

      MAMA_CAS_SERVICES = [
          {
              'SERVICE': '^https://[^\.]+\.example\.com',
              'CALLBACKS': [
                  'mama_cas.callbacks.user_name_attributes',
              ],
              'LOGOUT_ALLOW': True,
              'LOGOUT_URL': 'https://www.example.com/logout',
              'PROXY_ALLOW': True,
              'PROXY_PATTERN': '^https://proxy\.example\.com',
          }
      ]

   The following configuration options are available for each service:

   **SERVICE**

   A Python regular expression that is tested against to match a given
   service identifier. This option is required.

   **CALLBACKS**

   A list of dotted paths to callables that each provide a dictionary of
   name and attribute values. These values are merged together and included
   with a service or proxy validation success. Each callable is provided the
   authenticated ``User`` and the service URL as arguments. Defaults to ``[]``.

   Two callbacks are provided to cover basic use cases and serve as
   examples for custom callbacks:

   ``mama_cas.callbacks.user_name_attributes``
      Returns name-related fields using get_username(), get_full_name() and
      get_short_name().

   ``mama_cas.callbacks.user_model_attributes``
      Returns all fields on the user object, except for ``id`` and
      ``password``.

   **LOGOUT_ALLOW**

   A boolean setting to determine whether single log-out requests are sent
   for this service. Defaults to ``False``.

   **LOGOUT_URL**

   A URL that will be used for a single log-out request for the service. If
   not specified, the service URL will be used instead. Defaults to ``''``.

   **PROXY_ALLOW**

   A boolean setting to determine whether proxy requests are allowed for this
   service. Defaults to ``True``.

   **PROXY_PATTERN**

   A Python regular expression that is tested against to determine if the
   provided pgtUrl is allowed to make proxy requests. Defaults to ``''``.

.. attribute:: MAMA_CAS_TICKET_EXPIRE

   :default: ``90``

   Controls the length of time, in seconds, between when a service or proxy
   ticket is generated and when it expires. If the ticket is not validated
   before this time has elapsed, it becomes invalid. This does **not**
   affect proxy-granting ticket expiration or the duration of a user's single
   sign-on session.

.. attribute:: MAMA_CAS_TICKET_RAND_LEN

   :default: ``32``

   Sets the number of random characters created as part of the ticket string.
   It should be long enough that the ticket string cannot be brute forced
   within a reasonable amount of time. Longer values are more secure, but
   could cause compatibility problems with some clients.

.. attribute:: MAMA_CAS_VALID_SERVICES

   :default: ``()``

   A list of valid Python regular expressions that a service URL is tested
   against when a ticket is validated or the client is redirected. If none of
   the regular expressions match the provided URL, the action fails. If no
   valid services are configured, any service URL is allowed. For example::

      MAMA_CAS_VALID_SERVICES = (
          '^https?://www\.example\.edu/secure',
          '^https://[^\.]+\.example\.com',
      )

   .. warning::

      This setting has been deprecated in favor of MAMA_CAS_SERVICES.

.. attribute:: MAMA_CAS_LOGIN_TEMPLATE

   :default: ``'mama_cas/login.html'``

   A path to the login template to use. Make sure Django can find this template
   using normal Django template discovery rules.

.. attribute:: MAMA_CAS_WARN_TEMPLATE

   :default: ``'mama_cas/warn.html'``

   A path to the warning template to use. Make sure Django can find this
   template using normal Django template discovery rules.

.. _requests-futures: https://github.com/ross/requests-futures
.. _issue #83: https://github.com/jbittel/django-mama-cas/issues/83
