.. _api:

API Reference
=============

.. contents:: Resource Types
   :local:
   :depth: 2

.. note:: All ``datetime`` parameters must be in ISO 8601 format in UTC time
   (using time zone designator "Z") and expressed to millisecond precision as
   recommended by the `W3C Date and Time Formats Note`_ eg. ``2017-06-19T11:16:19.744Z``

.. _`W3C Date and Time Formats Note`: https://www.w3.org/TR/NOTE-datetime

.. _alerts:

Alerts
------

.. _post_alert:

Create an alert
~~~~~~~~~~~~~~~

Creates a new alert, or updates an existing alert if the ``environment``-
``resource``-``event`` combination already exists.

::

    POST /alert

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``resource``    | string   | **Required** resource under alarm            |
+-----------------+----------+----------------------------------------------+
| ``event``       | string   | **Required** event name                      |
+-----------------+----------+----------------------------------------------+
| ``environment`` | string   | environment, used to namespace the resource  |
+-----------------+----------+----------------------------------------------+
| ``severity``    | string   | see :ref:`severity_table` table              |
+-----------------+----------+----------------------------------------------+
| ``correlate``   | list     | list of related event names                  |
+-----------------+----------+----------------------------------------------+
| ``status``      | string   | see :ref:`status_table` table                |
+-----------------+----------+----------------------------------------------+
| ``service``     | list     | list of effected services                    |
+-----------------+----------+----------------------------------------------+
| ``group``       | string   | used to group events of similar type         |
+-----------------+----------+----------------------------------------------+
| ``value``       | string   | event value                                  |
+-----------------+----------+----------------------------------------------+
| ``text``        | string   | freeform text description                    |
+-----------------+----------+----------------------------------------------+
| ``tags``        | set      | set of tags                                  |
+-----------------+----------+----------------------------------------------+
| ``attributes``  | dict     | dictionary of key-value pairs                |
+-----------------+----------+----------------------------------------------+
| ``origin``      | string   | monitoring component that generated the alert|
+-----------------+----------+----------------------------------------------+
| ``type``        | string   | event type                                   |
+-----------------+----------+----------------------------------------------+
| ``createTime``  | datetime | time alert was generated at the origin       |
+-----------------+----------+----------------------------------------------+
| ``timeout``     | integer  | seconds before alert is considered stale     |
+-----------------+----------+----------------------------------------------+
| ``rawData``     | string   | unprocessed raw data                         |
+-----------------+----------+----------------------------------------------+

.. note:: Only ``resource`` and ``event`` are mandatory. The ``status`` can be
          dynamically assigned by the Alerta API based on the ``severity``.

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPOST http://localhost:8080/alert \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "attributes": {
            "region": "EU"
          },
          "correlate": [
            "HttpServerError",
            "HttpServerOK"
          ],
          "environment": "Production",
          "event": "HttpServerError",
          "group": "Web",
          "origin": "curl",
          "resource": "web01",
          "service": [
            "example.com"
          ],
          "severity": "major",
          "tags": [
            "dc1"
          ],
          "text": "Site is down.",
          "type": "exceptionAlert",
          "value": "Bad Gateway (501)"
        }'

Example Response
++++++++++++++++

::

    201 CREATED

.. code-block:: json

  {
    "alert": {
      "attributes": {
        "flapping": false,
        "ip": "127.0.0.1",
        "notify": true,
        "region": "EU"
      },
      "correlate": [
        "HttpServerError",
        "HttpServerOK"
      ],
      "createTime": "2018-01-27T21:00:12.999Z",
      "customer": null,
      "duplicateCount": 0,
      "environment": "Production",
      "event": "HttpServerError",
      "group": "Web",
      "history": [
        {
          "event": "HttpServerError",
          "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "severity": "major",
          "status": null,
          "text": "Site is down.",
          "type": "severity",
          "updateTime": "2018-01-27T21:00:12.999Z",
          "value": "Bad Gateway (501)"
        }
      ],
      "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
      "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
      "lastReceiveId": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
      "lastReceiveTime": "2018-01-27T21:00:13.070Z",
      "origin": "curl",
      "previousSeverity": "indeterminate",
      "rawData": null,
      "receiveTime": "2018-01-27T21:00:13.070Z",
      "repeat": false,
      "resource": "web01",
      "service": [
        "example.com"
      ],
      "severity": "major",
      "status": "open",
      "tags": [
        "dc1"
      ],
      "text": "Site is down.",
      "timeout": 86400,
      "trendIndication": "moreSevere",
      "type": "exceptionAlert",
      "value": "Bad Gateway (501)"
    },
    "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
    "status": "ok"
  }

Example Response (during blackout period)
+++++++++++++++++++++++++++++++++++++++++

::

    202 ACCEPTED

.. code-block:: json

    {
      "message": "Suppressed alert during blackout period",
      "id": "1711c57e-5c6a-4c39-881b-9d8d174feafe",
      "status": "ok"
    }


.. _get_alert_id:

Retrieve an alert
~~~~~~~~~~~~~~~~~

Retrieves an alert with the given alert ID.

::

    GET /alert/:id

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258 \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "alert": {
        "attributes": {
          "flapping": false,
          "ip": "127.0.0.1",
          "notify": true,
          "region": "EU"
        },
        "correlate": [
          "HttpServerError",
          "HttpServerOK"
        ],
        "createTime": "2018-01-27T21:00:12.999Z",
        "customer": null,
        "duplicateCount": 0,
        "environment": "Production",
        "event": "HttpServerError",
        "group": "Web",
        "history": [
          {
            "event": "HttpServerError",
            "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
            "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
            "severity": "major",
            "status": null,
            "text": "Site is down.",
            "type": "severity",
            "updateTime": "2018-01-27T21:00:12.999Z",
            "value": "Bad Gateway (501)"
          }
        ],
        "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
        "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
        "lastReceiveId": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
        "lastReceiveTime": "2018-01-27T21:00:13.070Z",
        "origin": "curl",
        "previousSeverity": "indeterminate",
        "rawData": null,
        "receiveTime": "2018-01-27T21:00:13.070Z",
        "repeat": false,
        "resource": "web01",
        "service": [
          "example.com"
        ],
        "severity": "major",
        "status": "open",
        "tags": [
          "dc1"
        ],
        "text": "Site is down.",
        "timeout": 86400,
        "trendIndication": "moreSevere",
        "type": "exceptionAlert",
        "value": "Bad Gateway (501)"
      },
      "status": "ok",
      "total": 1
    }

Set alert status
~~~~~~~~~~~~~~~~

Sets the status of an alert, and logs the status change to the alert history.

::

    PUT /alert/:id/status

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``status``      | string   | **Required** New status from ``open``,       |
|                 |          | ``assign``, ``ack``, ``closed``, ``expired`` |
+-----------------+----------+----------------------------------------------+
| ``text``        | string   | reason for status change                     |
+-----------------+----------+----------------------------------------------+
| ``timeout``     | integer  | Seconds.                                     |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258/status \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "status": "ack",
          "text": "disk needs replacing.",
          "timeout": 7200
        }'

Action alert
~~~~~~~~~~~~

Takes an action against an alert which can change the status or severity
of the alert and logs the action to the alert history.

::

    PUT /alert/:id/action

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``action``      | string   | **Required** Action from ``ack``, ``unack``` |
|                 |          | ``shelve``, ``unshelve``, ``close``          |
+-----------------+----------+----------------------------------------------+
| ``text``        | string   | reason for action                            |
+-----------------+----------+----------------------------------------------+
| ``timeout``     | integer  | Seconds.                                     |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258/action \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "action": "shelve",
          "text": "swap out servers.",
          "timeout": 7200
        }'

Tag and untag alerts
~~~~~~~~~~~~~~~~~~~~

Adds or removes tag values from the set of tags for an alert.

::

    PUT /alert/:id/tag
    PUT /alert/:id/untag

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``tags``        | set      | tags to add or remove                        |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258/tag \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "tags": [
            "linux",
            "linux2.6",
            "dell"
          ]
        }'

Update alert attributes
~~~~~~~~~~~~~~~~~~~~~~~

Adds, deletes or modifies alert attributes. To delete an attribute assign
"null" to the attribute.

::

    PUT /alert/:id/attributes

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``attributes``  | dict     | dictionary of key-value attributes           |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258/attributes \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "attributes": {
            "incidentKey": "1234abcd",
            "ip": "10.1.1.1",
            "region": null
          }
        }'


Delete an alert
~~~~~~~~~~~~~~~

Permanently deletes an alert. It cannot be undone.

::

    DELETE /alert/:id

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XDELETE http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258 \
    -H 'Authorization: Key demo-key'

.. _get_alerts:

Search alerts
~~~~~~~~~~~~~

Find alerts using various alert attributes or a mongo-type query parameter to
filter results.

::

    GET /alerts

Parameters
++++++++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``<attr>``      | string   | any alert attribute. eg. ``status=open``     |
+-----------------+----------+----------------------------------------------+
| ``q`` (*)       | json     | mongo query (see `Mongo Query Operators`_)   |
+-----------------+----------+----------------------------------------------+
| ``fields`` (*)  | list     | show or hide alert attributes                |
+-----------------+----------+----------------------------------------------+
| ``from-date``   | datetime | ``lastReceiveTime`` > ``from-date``          |
+-----------------+----------+----------------------------------------------+
| ``to-date``     | datetime | ``lastReceiveTime`` <= ``to-date`` (now)     |
+-----------------+----------+----------------------------------------------+
| ``sort-by``     | string   | attr to sort by (default:``lastReceiveTime``)|
+-----------------+----------+----------------------------------------------+
| ``reverse``     | boolean  | change direction of default sort order       |
+-----------------+----------+----------------------------------------------+
| ``page``        | integer  | number between 1 and total pages (default: 1)|
+-----------------+----------+----------------------------------------------+
| ``page-size``   | integer  | default: 1000 (set ``DEFAULT_PAGE_SIZE`` )   |
+-----------------+----------+----------------------------------------------+

.. _Mongo Query Operators: http://docs.mongodb.org/manual/reference/operator/query/

The ``<attr>`` search parameter works as follows:

  * Any combination of valid alert attributes can be used to narrow down results.

  * Search syntax is ``=`` (equals), ``!=`` (not equals), ``=~`` (regex match)
    and ``!=~`` (regex exclude).

  * When searching for alert ``id`` the query will attempt to match against ``id``
    and ``lastReceiveId``. The "short id" (ie. first 8-characters) can
    be used. eg. ``id=ba358336`` instead of ``id=ba358336-802d-40ee-8ace-bf5fa8529280``.

  * Use `"dot notation"`_ to query custom attributes. eg. ``attributes.city=Berlin``

  * Alert ``history`` is limited to the 100 most recent status or severity changes.
    (set using ``HISTORY_LIMIT``)

  * If "customer views" is enabled then the appropriate ``customer`` filter for
    that user will be automatically applied.

.. _"dot notation": https://docs.mongodb.com/v3.2/core/document/#document-dot-notation

The ``q`` search parameter works as follows:

  * Any valid JSON-compliant Mongo query using `Mongo Query Operators`_. Useful
    when there is a need to "or" several attributes to get the required
    search filter  eg. ``q={"$or":[{"service":"Web"},{"resource":{"$regex":"web"}}]}``

.. warning:: In the next major release of Alerta (5.0) the ``fields`` parameter
     will be removed. Also the ``q`` search term may change and no longer be
     mongo-specific.

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/alerts?group=Web \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "alerts": [
        {
          "attributes": {
            "flapping": false,
            "incidentKey": "1234abcd",
            "ip": "10.1.1.1",
            "notify": true
          },
          "correlate": [
            "HttpServerError",
            "HttpServerOK"
          ],
          "createTime": "2018-01-27T21:00:12.999Z",
          "customer": null,
          "duplicateCount": 0,
          "environment": "Production",
          "event": "HttpServerError",
          "group": "Web",
          "history": [
            {
              "event": "HttpServerError",
              "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
              "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
              "severity": "major",
              "status": null,
              "text": "Site is down.",
              "type": "severity",
              "updateTime": "2018-01-27T21:00:12.999Z",
              "value": "Bad Gateway (501)"
            },
            {
              "event": "HttpServerError",
              "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
              "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
              "severity": null,
              "status": "ack",
              "text": "disk needs replacing.",
              "type": "status",
              "updateTime": "2018-01-27T21:04:42.412Z",
              "value": null
            }
          ],
          "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "lastReceiveId": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "lastReceiveTime": "2018-01-27T21:00:13.070Z",
          "origin": "curl",
          "previousSeverity": "indeterminate",
          "rawData": null,
          "receiveTime": "2018-01-27T21:00:13.070Z",
          "repeat": false,
          "resource": "web01",
          "service": [
            "example.com"
          ],
          "severity": "major",
          "status": "ack",
          "tags": [
            "dc1",
            "linux",
            "linux2.6",
            "dell"
          ],
          "text": "Site is down.",
          "timeout": 86400,
          "trendIndication": "moreSevere",
          "type": "exceptionAlert",
          "value": "Bad Gateway (501)"
        }
      ],
      "autoRefresh": true,
      "lastTime": "2018-01-27T21:00:13.070Z",
      "more": false,
      "page": 1,
      "pageSize": 1000,
      "pages": 1,
      "severityCounts": {
        "major": 1
      },
      "status": "ok",
      "statusCounts": {
        "ack": 1
      },
      "total": 1
    }

.. _get_alerts_history:

List all alert history
~~~~~~~~~~~~~~~~~~~~~~

Returns a list of alert severity and status changes.

::

    GET /alerts/history

Parameters
++++++++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``<attr>``      | string   |                                              |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/alerts/history?service=example.com \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "history": [
        {
          "attributes": {
            "flapping": false,
            "incidentKey": "1234abcd",
            "ip": "10.1.1.1",
            "notify": true
          },
          "customer": null,
          "environment": "Production",
          "event": "HttpServerError",
          "group": "Web",
          "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "origin": "curl",
          "resource": "web01",
          "service": [
            "example.com"
          ],
          "severity": "major",
          "tags": [
            "dc1",
            "linux",
            "linux2.6",
            "dell"
          ],
          "text": "Site is down.",
          "type": "severity",
          "updateTime": "2018-01-27T21:00:12.999Z",
          "value": "Bad Gateway (501)"
        },
        {
          "attributes": {
            "flapping": false,
            "incidentKey": "1234abcd",
            "ip": "10.1.1.1",
            "notify": true
          },
          "customer": null,
          "environment": "Production",
          "event": "HttpServerError",
          "group": "Web",
          "href": "http://localhost:8080/alert/17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "id": "17d8e7ea-b3ba-4bb1-9c5a-29e60865f258",
          "origin": "curl",
          "resource": "web01",
          "service": [
            "example.com"
          ],
          "status": "ack",
          "tags": [
            "dc1",
            "linux",
            "linux2.6",
            "dell"
          ],
          "text": "disk needs replacing.",
          "type": "status",
          "updateTime": "2018-01-27T21:04:42.412Z"
        }
      ],
      "status": "ok",
      "total": 2
    }

Get severity and status counts for alerts
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Returns a count of alerts grouped by severity and status.

::

    GET /alerts/count

Parameters
++++++++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``<attr>``      | string   |                                              |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/alerts/count?environment=Production \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "severityCounts": {
        "critical": 1,
        "major": 1
      },
      "status": "ok",
      "statusCounts": {
        "ack": 1,
        "open": 1
      },
      "total": 2
    }

Top 10 alerts by resource
~~~~~~~~~~~~~~~~~~~~~~~~~

Returns a list of the top 10 resources grouped by an alert attribute. By
default it is grouped by ``event`` but this can be any valid attribute.

::

    GET /alerts/top10/count
    GET /alerts/top10/flapping

Parameters
++++++++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``<attr>``      | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``q``           | dict     | mongo query see `Mongo Query Operators`_     |
+-----------------+----------+----------------------------------------------+
| ``group-by``    | string   | any valid alert attribute. Default:``event`` |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/alerts/top10?group-by=group \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "status": "ok",
      "top10": [
        {
          "count": 2,
          "duplicateCount": 0,
          "environments": [
            "Production"
          ],
          "group": "Web",
          "resources": [
            {
              "href": "http://localhost:8080/alert/0099bae5-9683-48a1-a49d-f566fe143770",
              "id": "0099bae5-9683-48a1-a49d-f566fe143770",
              "resource": "web02"
            },
            {
              "href": "http://localhost:8080/alert/e9fb05a0-b65c-4faa-8868-6f06a74a2b5b",
              "id": "e9fb05a0-b65c-4faa-8868-6f06a74a2b5b",
              "resource": "web01"
            }
          ],
          "services": [
            "example.com"
          ]
        }
      ],
      "total": 1
    }

.. _environments:

Environments
------------

An environment cannot be created -- it is a dynamically derived resource based
on existing alerts.

List all environments
~~~~~~~~~~~~~~~~~~~~~

Returns a list of environments and an alert count for each.

::

    GET /environments

Parameters
++++++++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``<attr>``      | string   |                                              |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/environments \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "environments": [
        {
          "count": 2,
          "environment": "Production"
        }
      ],
      "status": "ok",
      "total": 1
    }

.. _services:

Services
--------

A service cannot be created -- it is a dynamically derived resource based on existing alerts.

List all services
~~~~~~~~~~~~~~~~~

Returns a list of services grouped by environment and an alert count for each.

::

    GET /services

Parameters
++++++++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``<attr>``      | string   |                                              |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/services?environment=Production \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "services": [
        {
          "count": 2,
          "environment": "Production",
          "service": "example.com"
        }
      ],
      "status": "ok",
      "total": 1
    }

.. _tags:

Tags
----

A tag cannot be created -- it is a dynamically derived resource based on existing alerts.

List all tags
~~~~~~~~~~~~~

Returns a list of tags grouped by environment and an alert count for each.

::

    GET /tags

Parameters
++++++++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``<attr>``      | string   |                                              |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/tags?environment=Production \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

  {
      "status": "ok",
      "tags": [
          {
              "count": 2,
              "environment": "Production",
              "tag": "linux"
          },
          {
              "count": 1,
              "environment": "Production",
              "tag": "dc2"
          },
          {
              "count": 1,
              "environment": "Production",
              "tag": "hp"
          },
          {
              "count": 2,
              "environment": "Production",
              "tag": "dell"
          },
          {
              "count": 2,
              "environment": "Production",
              "tag": "dc1"
          },
          {
              "count": 2,
              "environment": "Production",
              "tag": "linux2.6"
          }
      ],
      "total": 6
  }

.. _blackouts:

Blackout Periods
----------------

Create a blackout
~~~~~~~~~~~~~~~~~

Create a new blackout period for alert suppression.

::

    POST /blackout

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``environment`` | string   | **Required**                                 |
+-----------------+----------+----------------------------------------------+
| ``resource``    | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``service``     | list     |                                              |
+-----------------+----------+----------------------------------------------+
| ``event``       | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``group``       | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``tags``        | list     |                                              |
+-----------------+----------+----------------------------------------------+
| ``startTime``   | datetime | start time of blackout. Default: now         |
+-----------------+----------+----------------------------------------------+
| ``endTime``     | datetime | end time. Default: now +                     |
|                 |          | ``BLACKOUT_DURATION``                        |
+-----------------+----------+----------------------------------------------+
| ``duration``    | integer  | seconds. Default: ``BLACKOUT_DURATION``      |
|                 |          | Only used if ``endTime`` not defined         |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPOST http://localhost:8080/blackout \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "environment": "Production",
          "service": ["example.com"],
          "group": "Web"
        }'

Example Response
++++++++++++++++

::

    201 CREATED

.. code-block:: json

    {
      "blackout": {
        "customer": null,
        "duration": 3600,
        "endTime": "2018-01-27T22:10:31.705Z",
        "environment": "Production",
        "event": null,
        "group": "Web",
        "href": "http://localhost:8080/blackout/79d12b79-45b9-4419-80e4-1f6c17478eb6",
        "id": "79d12b79-45b9-4419-80e4-1f6c17478eb6",
        "priority": 3,
        "remaining": 3599,
        "resource": null,
        "service": [
          "example.com"
        ],
        "startTime": "2018-01-27T21:10:31.705Z",
        "status": "active",
        "tags": []
      },
      "id": "79d12b79-45b9-4419-80e4-1f6c17478eb6",
      "status": "ok"
    }

List all blackouts
~~~~~~~~~~~~~~~~~~

Returns a list of blackout periods, including expired blackouts.

::

    GET /blackouts

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/blackouts \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "blackouts": [
        {
          "customer": null,
          "duration": 3600,
          "endTime": "2018-01-27T22:10:31.705Z",
          "environment": "Production",
          "event": null,
          "group": "Web",
          "href": "http://localhost:8080/blackout/79d12b79-45b9-4419-80e4-1f6c17478eb6",
          "id": "79d12b79-45b9-4419-80e4-1f6c17478eb6",
          "priority": 3,
          "remaining": 3345,
          "resource": null,
          "service": [
            "example.com"
          ],
          "startTime": "2018-01-27T21:10:31.705Z",
          "status": "active",
          "tags": []
        },
        {
          "customer": null,
          "duration": 3600,
          "endTime": "2018-01-27T22:14:32.377Z",
          "environment": "Development",
          "event": null,
          "group": "Performance",
          "href": "http://localhost:8080/blackout/c17832d4-c477-4eb1-b2d5-662e7a3600be",
          "id": "c17832d4-c477-4eb1-b2d5-662e7a3600be",
          "priority": 5,
          "remaining": 3586,
          "resource": null,
          "service": [],
          "startTime": "2018-01-27T21:14:32.377Z",
          "status": "active",
          "tags": []
        }
      ],
      "status": "ok",
      "total": 2
    }

Delete a blackout
~~~~~~~~~~~~~~~~~

Permanently deletes a blackout period. It cannot be undone.

::

    DELETE /blackout/:id

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XDELETE http://localhost:8080/blackout/c17832d4-c477-4eb1-b2d5-662e7a3600be \
    -H 'Authorization: Key demo-key'

.. _heartbeats:

Heartbeats
----------

Send a heartbeat
~~~~~~~~~~~~~~~~

Creates a new heartbeat, or updates an existing heartbeat if a heartbeat
from the ``origin`` already exists.

::

    POST /heartbeat

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``origin``      | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``tags``        | list     |                                              |
+-----------------+----------+----------------------------------------------+
| ``timeout``     | integer  | Seconds.                                     |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPOST http://localhost:8080/heartbeat \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "origin": "cluster05",
          "timeout": 120,
          "tags": ["db05", "dc2"]
        }'

Example Response
++++++++++++++++

::

    201 CREATED

.. code-block:: json

    {
      "heartbeat": {
        "createTime": "2018-01-27T21:15:38.675Z",
        "customer": null,
        "href": "http://localhost:8080/heartbeat/1a3f2e0a-3c65-4199-84ae-a21fb892ccc0",
        "id": "1a3f2e0a-3c65-4199-84ae-a21fb892ccc0",
        "latency": 0,
        "origin": "cluster05",
        "receiveTime": "2018-01-27T21:15:38.675Z",
        "since": 0,
        "status": "ok",
        "tags": [
          "db05",
          "dc2"
        ],
        "timeout": 120,
        "type": "Heartbeat"
      },
      "id": "1a3f2e0a-3c65-4199-84ae-a21fb892ccc0",
      "status": "ok"
    }

Get a heartbeat
~~~~~~~~~~~~~~~

Retrieves a heartbeat based on the heartbeat ID.

::

    GET /heartbeat/:id

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/heartbeat/1a3f2e0a-3c65-4199-84ae-a21fb892ccc0 \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "heartbeat": {
        "createTime": "2018-01-27T21:15:38.675Z",
        "customer": null,
        "href": "http://localhost:8080/heartbeat/1a3f2e0a-3c65-4199-84ae-a21fb892ccc0",
        "id": "1a3f2e0a-3c65-4199-84ae-a21fb892ccc0",
        "latency": 0,
        "origin": "cluster05",
        "receiveTime": "2018-01-27T21:15:38.675Z",
        "since": 34,
        "status": "ok",
        "tags": [
          "db05",
          "dc2"
        ],
        "timeout": 120,
        "type": "Heartbeat"
      },
      "status": "ok",
      "total": 1
    }

List all heartbeats
~~~~~~~~~~~~~~~~~~~

Returns a list of all heartbeats.

::

  GET /heartbeats

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/heartbeats \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "heartbeats": [
        {
          "createTime": "2018-01-27T21:17:13.922Z",
          "customer": null,
          "href": "http://localhost:8080/heartbeat/f5eb11ef-e02b-42f2-9013-6efca6eca22a",
          "id": "f5eb11ef-e02b-42f2-9013-6efca6eca22a",
          "latency": 0,
          "origin": "web02",
          "receiveTime": "2018-01-27T21:17:13.922Z",
          "since": 45,
          "status": "ok",
          "tags": [
            "linux",
            "dc1"
          ],
          "timeout": 120,
          "type": "Heartbeat"
        },
        {
          "createTime": "2018-01-27T21:17:55.936Z",
          "customer": null,
          "href": "http://localhost:8080/heartbeat/e0582765-ee64-4944-8a94-1869a079d81f",
          "id": "e0582765-ee64-4944-8a94-1869a079d81f",
          "latency": 0,
          "origin": "cluster05",
          "receiveTime": "2018-01-27T21:17:55.936Z",
          "since": 3,
          "status": "ok",
          "tags": [
            "db05",
            "dc2"
          ],
          "timeout": 120,
          "type": "Heartbeat"
        }
      ],
      "status": "ok",
      "total": 2
    }

Delete a heartbeat
~~~~~~~~~~~~~~~~~~

Permanently deletes a heartbeat. It cannot be undone.

::

    DELETE /heartbeat/:id

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XDELETE http://localhost:8080/heartbeat/e0582765-ee64-4944-8a94-1869a079d81f \
    -H 'Authorization: Key demo-key'

.. _api_keys:

API Keys
--------

Create an API key
~~~~~~~~~~~~~~~~~

Creates a new API key.

::

    POST /key

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``user``        | string   | username                                     |
+-----------------+----------+----------------------------------------------+
| ``scopes``      | string   | ``admin``, ``write``, or ``read``            |
+-----------------+----------+----------------------------------------------+
| ``text``        | string   | freeform description text                    |
+-----------------+----------+----------------------------------------------+
| ``expireTime``  | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``customer``    | string   | **Admin use only**                           |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPOST http://localhost:8080/key \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "user": "admin@alerta.io",
          "scopes": ["write"],
          "text": "API key for external system"
        }'

Example Response
++++++++++++++++

::

    201 CREATED

.. code-block:: json

    {
      "data": {
        "count": 0,
        "customer": null,
        "expireTime": "2019-01-27T22:18:42.245Z",
        "href": "http://localhost:8080/key/_Jwm-qaGa0kBM9R1CyyQn-0qxLtBtij4ToQf6beL",
        "id": "ca931aec-4e56-496f-a8d6-be11d93ddaed",
        "key": "_Jwm-qaGa0kBM9R1CyyQn-0qxLtBtij4ToQf6beL",
        "lastUsedTime": null,
        "scopes": [
          "write"
        ],
        "text": "API key for external system",
        "type": "read-write",
        "user": "admin@alerta.io"
      },
      "key": "_Jwm-qaGa0kBM9R1CyyQn-0qxLtBtij4ToQf6beL",
      "status": "ok"
    }

List all API keys
~~~~~~~~~~~~~~~~~

Returns a list of API keys.

::

    GET /keys

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/keys \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "keys": [
        {
          "count": 0,
          "customer": null,
          "expireTime": "2019-01-27T22:18:42.245Z",
          "href": "http://localhost:8080/key/_Jwm-qaGa0kBM9R1CyyQn-0qxLtBtij4ToQf6beL",
          "id": "ca931aec-4e56-496f-a8d6-be11d93ddaed",
          "key": "_Jwm-qaGa0kBM9R1CyyQn-0qxLtBtij4ToQf6beL",
          "lastUsedTime": null,
          "scopes": [
            "write"
          ],
          "text": "API key for external system",
          "type": "read-write",
          "user": "admin@alerta.io"
        },
        {
          "count": 21,
          "customer": null,
          "expireTime": "2019-01-27T19:22:27.120Z",
          "href": "http://localhost:8080/key/demo-key",
          "id": "532c9b59-9e90-40d4-8a3b-887362a79e9c",
          "key": "demo-key",
          "lastUsedTime": "2018-01-27T22:19:04.113Z",
          "scopes": [
            "admin",
            "write",
            "read"
          ],
          "text": "Admin key created by alertad script",
          "type": "read-write",
          "user": "foo@foobar.com"
        }
      ],
      "status": "ok",
      "total": 2
    }


Delete an API key
~~~~~~~~~~~~~~~~~

Permanently deletes an API key. It cannot be undone.

::

    DELETE /key/:key

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XDELETE http://localhost:8080/key/532c9b59-9e90-40d4-8a3b-887362a79e9cO8rhJSKrdfQWXqRhvSwJQJRZg9yU0s2Z4VLP4855 \
    -H 'Authorization: Key demo-key'

.. _users:

Users
-----

Create a user
~~~~~~~~~~~~~

Creates a new Basic Auth user.

::

    POST /auth/signup

Input
+++++

+--------------------+----------+-------------------------------------------+
| Name               | Type     | Description                               |
+====================+==========+===========================================+
| ``name``           | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``email``          | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``password``       | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``text``           | string   |                                           |
+--------------------+----------+-------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPOST http://localhost:8080/auth/signup \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "name": "Joe Bloggs",
          "email": "joe.bloggs@example.com",
          "password": "secret",
          "text": "demo user"
        }'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI4Y2IwYjYyNC0zY2Q3LTQ1YjktOThhNS01ZGZhYzVmMDE2NmMiLCJyb2xlcyI6WyJ1c2VyIl0sImlzcyI6Imh0dHA6Ly9sb2NhbGhvc3Q6ODA4MC8iLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJqb2UuYmxvZ2dzQGV4YW1wbGUuY29tIiwibmFtZSI6IkpvZSBCbG9nZ3MiLCJlbWFpbCI6ImpvZS5ibG9nZ3NAZXhhbXBsZS5jb20iLCJzY29wZSI6InJlYWQgd3JpdGUiLCJqdGkiOiI2ODlhMmY3Yy0zNTJlLTQ5M2ItYWZjYi1iOWUwOTE3ODAyMDgiLCJleHAiOjE1MTMxODIxNDcsInByb3ZpZGVyIjoiYmFzaWMiLCJpYXQiOjE1MTE5NzI1NDcsIm5iZiI6MTUxMTk3MjU0NywiYXVkIjoiaHR0cDovL2xvY2FsaG9zdDo4MDgwLyJ9.c5jpr8YksoJmoZ6KUwsYP5fgwZr-jdA4W3JUCbv1vXU"
    }

Update a user
~~~~~~~~~~~~~

Updates the specified user by setting the values of the parameters passed.
Any parameters not provided will be left unchanged.

::

    PUT /user/:user

Input
+++++

+--------------------+----------+-------------------------------------------+
| Name               | Type     | Description                               |
+====================+==========+===========================================+
| ``name``           | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``email``          | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``password``       | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``status``         | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``roles``          | set      | set of roles                              |
+--------------------+----------+-------------------------------------------+
| ``attributes``     | dict     | dictionary of key-value pairs             |
+--------------------+----------+-------------------------------------------+
| ``text``           | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``email_verified`` | boolean  |                                           |
+--------------------+----------+-------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/user/0a35bfd8-1175-4cd2-96f6-eda9861fd15d \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "password": "p8ssw0rd",
          "text": "test user",
          "email_verified": false
        }'

Update me
~~~~~~~~~

Updates the logged in user by setting the values of the parameters passed.
Any parameters not provided will be left unchanged.

It is not allowed to update ``roles``, ``email_verified`` status or change
the ``email`` to one that is already associated with another user.

::

    PUT /user/me

Input
+++++

+--------------------+----------+-------------------------------------------+
| Name               | Type     | Description                               |
+====================+==========+===========================================+
| ``name``           | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``email``          | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``password``       | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``status``         | string   |                                           |
+--------------------+----------+-------------------------------------------+
| ``attributes``     | dict     | dictionary of key-value pairs             |
+--------------------+----------+-------------------------------------------+
| ``text``           | string   |                                           |
+--------------------+----------+-------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/user/me \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "password": "p8ssw0rd",
          "text": "test user me"
        }'

Update user attributes
~~~~~~~~~~~~~~~~~~~~~~

Updates the specified user attributes.

::

    PUT /user/:id/attributes

Input
+++++

+--------------------+----------+-------------------------------------------+
| Name               | Type     | Description                               |
+====================+==========+===========================================+
| ``attributes``     | dict     | dictionary of key-value pairs             |
+--------------------+----------+-------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/user/0a35bfd8-1175-4cd2-96f6-eda9861fd15d/attributes \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "attributes": {
            "team": "developers"
          }
      }'

Update user me attributes
~~~~~~~~~~~~~~~~~~~~~~~~~

Updates the logged in user attributes.

::

    PUT /user/me/attributes

Input
+++++

+--------------------+----------+-------------------------------------------+
| Name               | Type     | Description                               |
+====================+==========+===========================================+
| ``attributes``     | dict     | dictionary of key-value pairs             |
+--------------------+----------+-------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPUT http://localhost:8080/user/me/attributes \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "attributes": {
            "teams": ["developers", "operations"]
          }
      }'

List all users
~~~~~~~~~~~~~~

Returns a list of users.

::

    GET /users

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/users \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "domains": [
        "*"
      ],
      "groups": [
        "*"
      ],
      "orgs": [
        "*"
      ],
      "status": "ok",
      "time": "2017-01-02T00:24:00.393Z",
      "total": 2,
      "users": [
        {
          "createTime": "2017-01-01T23:49:38.486Z",
          "email_verified": false,
          "id": "b91811e7-52dd-4a8f-adae-b4d5c628d6f8",
          "login": "jane.doe@example.org",
          "name": "Jane Doe",
          "provider": "basic",
          "role": "user",
          "text": "demo user"
        },
        {
          "createTime": "2017-01-02T00:23:24.487Z",
          "email_verified": true,
          "id": "166b41d6-849f-440d-ba30-1a5345d86fb6",
          "login": "joe.bloggs@example.com",
          "name": "Joe Bloggs",
          "provider": "basic",
          "role": "user",
          "text": "demo user"
        }
      ]
    }

Delete a user
~~~~~~~~~~~~~

Permanently deletes a user. It cannot be undone.

::

    DELETE /user/:user

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XDELETE http://localhost:8080/user/166b41d6-849f-440d-ba30-1a5345d86fb6 \
    -H 'Authorization: Key demo-key'

.. _perms:

Permissions
-----------

Create permission
~~~~~~~~~~~~~~~~~

Creates a new permission lookup. Used to match user groups/roles to scopes.

::

    POST /perm

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``scopes``      | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``match``       | regex    |                                              |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPOST http://localhost:8080/perm \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "scopes": ["read", "write", "admin:alerts"],
          "match": "alerta_ops"
        }'

Example Response
++++++++++++++++

::

    201 CREATED

.. code-block:: json

    {
      "id": "40c2daee-1d77-44d5-b62d-e3e446396cef",
      "permission": {
        "id": "40c2daee-1d77-44d5-b62d-e3e446396cef",
        "match": "alerta_ops",
        "scopes": [
          "read",
          "write",
          "admin:keys"
        ]
      },
      "status": "ok"
    }

List all permissions
~~~~~~~~~~~~~~~~~~~~

Returns a list of permissions.

::

    GET /perms

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/perms \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "permissions": [
        {
          "id": "5b726183-019f-4add-b6dc-caba87e873f7",
          "match": "alerta_ro",
          "scopes": [
            "read"
          ]
        },
        {
          "id": "f4c91af3-5222-4201-9da0-02c40122f4c4",
          "match": "alerta_rw",
          "scopes": [
            "read",
            "write"
          ]
        },
        {
          "id": "1f84f919-c07a-4bd1-93b0-26e28871257f",
          "match": "alerta_ops",
          "scopes": [
            "read",
            "write",
            "admin:keys"
          ]
        }
      ],
      "status": "ok",
      "time": "2017-07-29T21:42:30.500Z",
      "total": 3
    }

Delete a permission
~~~~~~~~~~~~~~~~~~~

Permanently delete a permission. It cannot be undone.

::

    DELETE /perm/:perm

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XDELETE http://localhost:8080/perm/1f84f919-c07a-4bd1-93b0-26e28871257f \
    -H 'Authorization: Key demo-key'

.. _customers:

Customers
---------

Create a customer
~~~~~~~~~~~~~~~~~

Creates a new customer lookup. Used to match user logins to customers.

::

    POST /customer

Input
+++++

+-----------------+----------+----------------------------------------------+
| Name            | Type     | Description                                  |
+=================+==========+==============================================+
| ``customer``    | string   |                                              |
+-----------------+----------+----------------------------------------------+
| ``match``       | regex    |                                              |
+-----------------+----------+----------------------------------------------+

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XPOST http://localhost:8080/customer \
    -H 'Authorization: Key demo-key' \
    -H 'Content-type: application/json' \
    -d '{
          "customer": "Example Corp.",
          "match": "example.com"
        }'

Example Response
++++++++++++++++

::

    201 CREATED

.. code-block:: json

    {
      "customer": {
        "customer": "Example Corp.",
        "id": "289ca657-ea2c-4775-9e07-cc96844cc717",
        "match": "example.com"
      },
      "id": "289ca657-ea2c-4775-9e07-cc96844cc717",
      "status": "ok"
    }

List all customers
~~~~~~~~~~~~~~~~~~

Returns a list of customers.

::

    GET /customers

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl http://localhost:8080/customers \
    -H 'Authorization: Key demo-key'

Example Response
++++++++++++++++

::

    200 OK

.. code-block:: json

    {
      "customers": [
        {
          "customer": "Example Corp.",
          "id": "289ca657-ea2c-4775-9e07-cc96844cc717",
          "match": "example.com"
        },
        {
          "customer": "Example Org.",
          "id": "90f4e211-c815-4112-9e1a-6e53de5a59c6",
          "match": "example.org"
        }
      ],
      "status": "ok",
      "time": "2017-01-02T01:21:38.958Z",
      "total": 2
    }

Delete a customer
~~~~~~~~~~~~~~~~~

Permanently delete a customer. It cannot be undone.

::

    DELETE /customer/:customer

Example Request
+++++++++++++++

.. code-block:: bash

    $ curl -XDELETE http://localhost:8080/customer/90f4e211-c815-4112-9e1a-6e53de5a59c6 \
    -H 'Authorization: Key demo-key'
